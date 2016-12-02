# Bite Your Thumb: Week 1 MVP
---

### Links:

#### [FoaasAPI](http://www.foaas.com/)
#### [Week 1 MVP Info](https://github.com/AccessLite/BoardingPass/blob/master/Week%201%20-%20MVP/Week1MVP_TechLeadInstructions.md)

---
### Adapting to the keyboard

This one is a request from our PM that we're going to push through for this week. We need to ensure that none of our text fields in the `OperationPreviewViewController` get obstructed by the keyboard when it appears (as currently can, and does, happen).

I would recommend that you embed all of your existing UI elements in a scroll view. Add a reference to the bottom constraint of the scroll view in storyboard so that you can adjust it later in code. Then add an observer to your `OperationPreviewViewController` to listen for `UIKeyboardWillShowNotification` and `UIKeyboardWillHideNotification` notifications, similar to how you added a notification observer to the `FoaasViewController` (check out the [Apple doc](https://developer.apple.com/library/content/documentation/StringsTextFonts/Conceptual/TextAndWebiPhoneOS/KeyboardManagement/KeyboardManagement.html#//apple_ref/doc/uid/TP40009542-CH5-SW16) if needed). You can then call on the bottom constraint of the scroll view and adjust it's position by adding/subtracting the height of the frame of the keyboard. 

It won't be a perfect experience yet, but this next stepp will please the PM. 

### Sharing a `Foaas` with others

This is another request from the PM that we push through as part of the week 2 MVP experience. Add a tap gesture to the view in order to start this sharing flow.

1. Use of `UIActivityViewController` to do allow users to share the `Foaas` they create
2. We should allow users to share this via email or iMessage 
3. For now, copy the text of the `Foaas` message and subtitle and add it to a user's pasteboard
4. Pass this info along in the share action so that it gets pasted into the body of a user's email or text

### Creating a screenshot

Now, the following is a request of mine: I'd like for us to do a beta version of saving the `FoaasViewController` as a screenshot to our camera roll. Let's add this option as a long-press gesture on the view for now. 

1. Add the `NSPhotoLibraryUsageDescription` key to your plist along with a relevant message
1. Make use of `UIGraphicsBeginImageContextWithOptions(_:_:_:)` to create a screenshot of the `FoaasViewController`. 
2. Save it to the user's camera roll using `UIImageWriteToSavedPhotosAlbum(_:_:_:_:)`
3. Present an alert using `UIAlertViewController` to indicate success/failure to the user

Please use this function as the `selector` parameter of `UIImageWriteToSavedPhotosAlbum` 

```swift
internal func createScreenShotCompletion(image: UIImage, didFinishSavingWithError: NSError?, contextInfo: UnsafeMutableRawPointer?) {
  // check if error != nil
  // present appropriate message in UIAlertViewController
  // double check that the image actually gets saved to the camera roll
}
```

### Animating the button

1. `Use UIView.animate(withDuration:animations:)` to do a simple press-down effect on the octobutton. 
2. The effect should scale the image down 20% in `0.1s`, and then return it to its normal size
3. Where you're hooking up the `IBAction` for this, make sure you select `TouchDown` and not `TouchUpInside`

Your `IBAction` code will look something like
```swift
    // create references to the different transforms
    let newTransform = CGAffineTransform(scaleX: 0.8, y: 0.8)
    let originalTransform = sender.imageView!.transform
    
    UIView.animate(withDuration: 0.1, animations: {
      // animate to newTransform
    }, completion: { (complete) in
      // return to original transform
    })
```
---
# Code Refactors

### `FoaasBuilder`

We're creating a new class specifically meant to generating the path components to our `URL`s


### Other MANDATORY Requirements:
1. Bug fixes listed in your Week 1 PR Code Comments. 
1. The text of the `FoaasViewController` label should be bound by the top of the octo-image at the bottom of the screen. Meaning, the text should not be large enough so that it would be covered by the image (or going further than the bottom edge of the screen!). 
  - You may want to test your code using the `/madison` endpoint, as it seems to produce the longest text.
2. You must implement something to "live preview" the `Foaas` message in your `OperationPreviewViewController`. For now, it is acceptable that the preview text updates on `textFieldDidEndEditing` or `textFieldShouldReturn`. 
3. Your app must make proper use of the icons provided by Design. 
4. Your repo MUST HAVE A .gitignore ALWAYS. If I haven't already corrected this for you in a PR, please add one on. 

Failure to meet these mandatory requirements will immediately disqualify you from continuing to next week. 
