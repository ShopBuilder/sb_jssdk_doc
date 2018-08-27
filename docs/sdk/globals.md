the **Globals** are found in  `window.SBsdk.SBdata`                    
                
To see all of the globals available:          
- open your shopbuilder website        
- open the console and type `console.log(window.SBsdk.SBdata)`         
      
---------------------------------
             
#### Page Data

Page data gives you information about the page you are in.       
All of the page data are found in `window.SBsdk.SBdata.page`     
                 
```   
// @Code        
console.log(window.SBsdk.SBdata.page);          

// @Result
{         
  id: '', // id of product for example (if there is no id by default it will be empty)              
  type: '', // ex. homepage
  action: '', // ex. Add | Edit | View            
  query_strings: '' //ex. ?a=34&b=34         
}      
``` 
        
**page type common values:**  `window.SBsdk.SBdata.page.type`               
1- homepage       
2- collection (for the collection page)     
3- shop_builder_display (for the product page)       
4- page (for basic page)        
5- order (for order page)                
       

**page action common values:** `window.SBsdk.SBdata.page.action`               
1- view             
2- edit      
3- add                          
     
                                  
*In some pages, you might find other extra information in the page data*            
                   
```
 { // In the checkout complete page
  type: 'checkout',
  action: 'complete',
  order: {
    total_amount: '',
    currency_code: '',
    currency_symbol: '',
  }
 { // In the checkout page
  type: 'checkout',
  action: 'checkout',
  order: {
    total_amount: '',
    currency_code: '',
  }
{ // In the order history page
  type: 'order',
  action: 'history view',
  order: {
    order_id: '',
  },
}

```
      
             
---------------------------------
      
#### Website Data    

- Website Data gives information about your website `window.SBsdk.SBdata.SBwebsite_data`    

```  
// @Code       
console.log(window.SBsdk.SBdata.SBwebsite_data);      
// @Result 
{
  website_id //This is gona be the website id ex.1234568789
  user_id //id of user that is currently logged ex. 30       
  user_role //Array of user roles ex. ["Authenticated", "merchant"]                
  map_marker //Array of user roles ex. ["Authenticated", "merchant"]                
}
```
     

- **Google Map Marker information:**
 
>  1- To access the marker information being used by the theme use: `window.SBsdk.SBdata.SBwebsite_data.map_marker.theme`.                  
>  2- To customize your marker (ie. change the default theme) [click here](/sdk/callbacks/#google-map-marker-callback)           
>  3- To access the customized marker information that was set by an app use:  `window.SBsdk.SBdata.SBwebsite_data.map_marker.custom`.           

---------------------------------
      
#### Page Data Events         
                 
Page data events are information of events that are executed on page load:         
found in `window.SBsdk.SBdata.page.init_event`               
         
Cases where we have this page data event:     

1- After adding a product to cart within product drawer, the page will refresh and fire this event        
                  
```
{
    id: 'Product url alias',
    type: 'cart',
    action: 'new item',
    query_strings: '" . $query_strings_text . "'
}
```      

2- A specific case event is executed only when user login through login popup and a query string after-login-event is available       
               
```
{
    id: 'Value of query string after-login-event',
    type: 'user',
    action: 'after login',
    query_strings: '" . $query_strings_text . "'
 }
```
           
3- If shopbuilder is configured to redirect products, this event will be executed when the redirect happens:      
       
```
{
    id: 'Product url alias',
    type: 'product',
    action: 'product-redirect',
    query_strings: '" . $query_strings_text . "'
  }
```     
     
4- When trying to add an out-of-stock product to cart within product drawer: 
     
```
{
    id: 'Product url alias',
    type: 'cart',
    action: 'out-of-stock',
    query_strings: '" . $query_strings_text . "'
}
```

             
------------------
             