#### SDK Ready

Once the SDK has finished performing all operations the custom `sBsdkReady` event is triggered and can be listened to with the following code
```
jQuery(document).on("sBsdkReady",function(e) {
   // Your code here
});
```

#### Dialog
          
To hook or get notified after the [dialog](/sdk/functions/#dialog) has been opened                     
                                 
`jQuery(document).on("sBsdkDialogReady",function(e) {});`                     

**Example:**       
```
SBsdk.SBfunctions.dialog('<div class="dialog-A">Dom content here</div>',{
  Title: "Title",
  "SaveBtn" : 'save'
});
// calling the dialog will fire the sBsdkDialogReady Event
jQuery(document).on("sBsdkDialogReady",function(e) {
  // do something
});
``` 
