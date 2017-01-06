# Week 3 MVP - Tech Lead Notes
---

Broad weekly thoughts:

As the project become more complex and individualized (in terms of code), it becomes harder for a lead to write out code ahead of time for proposed functionality. So this week you will be seeing things much less in terms of concrete class outlines or sample code.

Instead, it is going to be on you to decide the best approach on how to tackle a problem. What this means is that you will need to look over your current implementation and proposed changes in order to determine how you're going to write and structure your code. 

I will be more of a resource to bounce implementation ideas off of and a life line in the event that you just cannot resolve a bug or can finish a feature implementation. To that effect, this week will be different than the prior ones in that we'll have scheduled group discussions about the code and its implementation. You will be expected to come with questions and a basic understanding of what needs to be accomplished; I will be there to fill in the gaps. 

**The group chats will also serve a dual purpose.**

There are aspects of this week's design that I'm not entirely certain myself how would be best implemented. I have some idea based on the current state of the project, but I'd rather us discuss implementation details together so we can nail down what is possible and not possible. 

I'd like to have at least 2-3 1 hour chats where we talk about different points of the features. We'll need to chat further on times that are best for poeple. 

---

### Navigation Re-design

So design is coming up with a big ask of us here in somewhat re-doing our navigation UI. Based on the new designs, I'm going to make a few suggestions on how to proceed: 

1. Removing the octo image and replacing it with the "+" icon will be trivial (as will re-positioning it), and we still want it to basically do the same thing: modally present the list of possible operations. 
2. The navigation controller's navbar will need to be hidden, however. Fortunately we should be able to call `setNavigationBarHidden(Bool, animated: Bool)` on the navcontroller to hide it in each view controller. 
3. If we're hiding the nav bar, we're going to have to hook up our `dismiss` functionality to the buttons that we're adding to replace the bar's nav items. 
4. In the preview view, the "back arrow" button should obviously pop the view controller back to the operation list. 
5. Clicking on the "checkmark" button is effectively our "Done" button and should dismiss the nav stack and show an updated `FoaasViewController`

---
### Style Manager

We'll need something to coordinate app-wide stylistic changes to both color and font. Ideally I see this being implemented in a singleton-like (if not, just like a singleton) way. 

#### Font

We need to install Roboto (I think the PM notes has the link) into our app. You've done this before, so just remember a few things like adding the "Fonts Provided By App" key to your plist once you've added the font bundle. Check [this link](https://grokswift.com/custom-fonts/) if you need any pointers. 

What I think we should do, is have font-presets that can be updated easily later if needed. By this I mean that we should be able to call a struct to get a specified font size/weight. It would look something like this in action:

```swift
let label = UILabel()
label.text = "Hello, World!"
label.font = Roboto.header // would correspond to whatever style "header" should be in terms of font weight, color, and size

let label2 = UILabel() 
label.text = "Rogue One"
label.font = Roboto.light.pt(34) // I'd like to be able to do this as well, though
```
We should be able to support the font weights and sizes that are requested from the PM.

#### Colors

The idea behind color management is similar: we want to be able to define and re-use a set number of color constants. These constants will correspond to the app's color palette, `primary, primaryDark, primaryLight` and `accent`. It's important we define this in a manager because we'll want the ability to update the app's palette on demand. In addition to the four main color palette items, we'll also need several other colors to work with the `FoaasOperationTableViewController`. Each primary color palette has 10 different hues of the color to use, numbered from 50 to 900 (see PM's notes). This palette will also need to be updated

#### Color Hex Conversion

The values provided to us from the PM will be in their hex values. There are a number of ways to convert hexadecimal to RGB colors, implement whichever way you see fit. However, make sure that we're able to convert a hex string into RGB, as we'll be reading color information from a string stored in a json file or similar. 

---
### Shadows

This one's going to be a tough one, as displaying relative distance of the views using shadow drawing is going to take some work in order to architect. For now, ensure that your views/buttons have shadows that roughly correspond to what the PM's design is asking. We'll need to revisit this implementation during our weekly chats. 

---
### Settings Menu - (`FoaasSettingsMenuView` / `FoaasSettingsMenuDelegate`)?

What we'll likely want to do is create a new custom view that "lives" under our original `Foaas` view. When we present the settings menu/view, we want a quick and smooth animation of the view upwards to reveal the setting menu below. If you really want our PM to loose their mind, have a slight parallax by animating the settings menu upwards on reveal, but over a smaller distance (this will give the impression that the view has shifted upwards and that the setting menu has shifted into place below it, instead of the feeling that the settings menu is always just statically there). 

In order to show the settings menu, users should be able to either swipe up from the bottom or tap on the disclosure indictor, so add both of those gestures in. To dismiss, tapping anywhere on the view controller (outside of the bounds of the settings view) or swiping down from the `Foaas` view, should close the settings menu. 

#### Color Palette Switch

I had envisioned this to kind of work like a paging scroll view that fades in opacity as it hits the edges of the scroll view's frame. Each subview would have a defined width/height and be of the color(s) available to the user. Paging to a different view would automatically update all of the views... so in this case the background of the `FoaasViewController`, which is still partially visible, should also update right away.

The available colors should be populated from the values contained in the style manager.

#### Sharing Options

In addition to the saving to photo roll, we now want to give uses the option to send the `Foaas` screenshot in: 

1. Email and iMessage
2. Facebook
3. Twitter

Additionally, we want to have a `Copy` option available when presenting the Email/iMessage sharing menu.

So part of this will just involve relinking the code you've implemented and having it triggered by these new buttons. I'd probably suggest doing it via delegation and would discourage using `NotificationCenter` unless you have a compelling reason. 

#### Loading the "About" section info
The details of how to get this data in place is discussed below, under **Version Management**, but it suffices to say that this will just need to be two labels that accept their text fields being updated by another object... pretty boiler plate stuff. 

#### Profanity Toggle
This one is likely a bit tricky. The toggle should immediately update the profanity in the (partially) obscured `FoaasViewController` if necessary.. in addition to everywhere else. 

As always, the mantra is "make this work first." But I'd like to find an elegant solution to this problem that doesn't require checking a `Bool` owned by some state manager. But there could also be no other way to do this -- we'll discuss further in person. 

---
### Version Management

The over-arching idea is to keep the app somewhat dynamic; we want to be able to update content for users based on RESTful requests to some endpoint that will update their local settings. 

For example, if we want to make new color schemes available:

1. The app would load up
2. Check the current version of the app via request to our endpoint
3. If the version is out of date, download a new json file
4. Replace the old settings/info with that of the new json file
5. Save these settings
6. Update the app as necessary

Right now, in order to have a proof of concept we're going to focus on color schemes and "About Info" section text. I think the easiest solution is to host an endpoint using [Fieldbook](https://fieldbook.com/) but in the coming week(s), move to using firebase. 

Should the firebase migration work well enough, it opens the possibility of adding lots of new features as we can create and maintain a database of users. 

#### Local Storage
There's a lot of ways we could do this, but ultimately I'd like to have a config file stored locally on the device from which our settings manager "reads" from in order to populate info. 

However, to start, we can effectively ignore local storage and instead:

1. make an API request
2. parse json via a settings manager
3. save those settings the `UserDefaults`

Then on app loads, following an API version check we'd: 

1. Check `UserDefaults` 
2. Load information from `UserDefaults`

---
### Refinements

These are a few bullets that can be accomplished with light-moderate difficulty and probably won't require any additional explanation:

1. Live updating of the text (letter-for-letter)
2. Changing the cursor's tint color to match the accent color of the color scheme
3. Having the `FoaasPreviewViewController` animate in with the keyboard with the same animation and timing 

#### Refactors
I'd like there to only be 1 API call per `FoaasOperation` and for you to edit that local copy as needed.

#### UX Enhancements
1. Users should not be able to tap checkmark until they've filled out all of the `FoaasFields`
2. Tapping on the "+" button while the settings menu is present should dismiss the settings in addition to showing the operation list modally. But they should happen sequentially, not at the same time.
3. Textfield should adapt to the size of its content, see this [blog post](https://medium.com/@louistur/dynamic-sizing-of-uitextview-with-autolayout-6dbcfa8e5e2d#.1k8hcwajn)
4. Center the label content on main foaas view
5. Highlighting fooas placeholder text using accent color
