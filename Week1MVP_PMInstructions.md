# Bite Your Thumb: Week 1 MVP (PM)
---

Refer to [v1 of the storyboard](./foaasOcto.pdf)

---

### Main Screen
The main screen will be used to display the current __BYT__ (Bite Your Thumb) message. We should follow these guidelines:

1. The main text label font that will display the message itself should be a maximum of `60pt`, and a minimum of `30pt`. It should also be `left` aligned and `Medium` emphasis.
2. The subtitle text label will always display who the message is from in the form of "From,\nName". The text should be scaled to `80%` of whatever the main text label is. We'd like to avoid scrolling, but if you have to we're making this allowance for this week's MVP. 
3. The icon on the bottom should be a `60x60pt` button with our Octo logo. Tapping on the logo should take you to a list of your available operations. The icon itself should also be centered vertically, and `8pt` from the bottom of the screen (with respect to margins). 
4. The list of operations should be simple: just a single line of the name of the operation in `Bold 18pt`
5. Tapping on the list item should bring you to the details of the operation: 
  - The name at the top should be the name of the operation
  - The preview window should get the full operation string, placing the proper placeholders inline with the rest of the text. 
  - At the bottom, there should be text fields for entering the placeholder values. 
6. As you type in to each field, the placeholder should update with the current text. If a user deletes the text, the placeholder text should return to both the text field and the inline preview. 
  - Only the input fields should scroll with the keyboard. The preview text box should be scrollable. 
7. Tapping "Select/Done" in the menu bar should dismiss the view and show the final message on the main screen. 
  - Make sure that the fonts get updated as needed
  
---
### Other

Assets for the app (including icon, and launch image) will be slacked out later this week once our designers have finished. 

Depending on time, we may push the operations preview screen to next week. But be under the assumption that it will be completed this week.
