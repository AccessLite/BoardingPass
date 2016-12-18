# Week 3 MVP - Tech Lead Notes
---

### Navigation Re-design

So design is coming up with a big ask of us here in somewhat re-doing our navigation UI. Based on the new designs, I'm going to make a few suggestions on how to proceed: 

1. Removing the octo image and replacing it with the "+" icon will be trivial (as will re-positioning it), and we still want it to basically do the same thing: modally present the list of possible operations. 
2. The navigation controller's navbar will need to be hidden, however. Fortunately we should be able to call `setNavigationBarHidden(Bool, animated: Bool)` on the navcontroller to hide it in each view controller. 
3. If we're hiding the nav bar, we're going to have to hook up our `dismiss` functionality to the buttons that we're adding to replace the bar's nav items. 
4. In the preview view, the "back arrow" button should obviously pop the view controller back to the operation list. 
5. clicking on the "checkmark" button is effectively our "Done" button and should dismiss the nav stack and show an updated `FoaasViewController`

---
### Style Manager

We'll need something to coordinate app-wide stylistic changes to both color and font. 

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

This one's going to be a tough one, as displaying relative distance of the views using shadow drawing is going to take some work in order to architect. For now, ensure that your views/buttons have shadows that roughly correspond to what the PM's design is asking. We'll need to revisit this implementation once I've thought of it for a bit. 

---
### Settings Menu - `FoaasSettingsMenuView` / `FoaasSettingsMenuDelegate`

What we'll likely want to do is create a new custom view that "lives" under our original `Foaas` view. When we present the settings menu/view, we want a quick and smooth animation of the view upwards to reveal the setting menu below. If you really want our PM to loose their mind, have a slight parallax by animating the settings menu upwards on reveal, but over a smaller distance (this will give the impression that the view has shifted upwards and that the setting menu has shifted into place below it, instead of the feeling that the settings menu is always just statically there). 

In order to show the settings menu, users should be able to either swipe up from the bottom or tap on the disclosure indictor, so add both of those gestures in. To dismiss, tapping anywhere on the view controller (outside of the bounds of the settings view) or swiping down from the `Foaas` view, should close the settings menu. 

- color palette switching (page/swipe)
- sharing options
- re-linking screenshot/share
- loading "about" section info
- profanity toggle switch, updates app-wide

- version management
- downloading config files
- loading config/storing user defaults
- loading color palettes from config

- live updating the text
- changing cursor color to accent 
- refined keyboard behavior (animation/timing)

#### refactoring
- only 1 API call per operation, edit local copy, send message info in notification bundle

#### UX
- users should not be able to tap checkmark until they've filled out all of the `foaasfields`
- tapping on the "+" button while the settings menu is present should dismiss the settings in addition to showing the operation list modally
- textfield should adapt to the size of its content, link blog post
- centering label content on main view
- highlighting fooas placeholder text using accent color
