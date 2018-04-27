   3rd Party app general usage:
--------------------------------------------
         
Note that to check all the *functions* and *globals* that can be used from the sdk:       
you can `console.log(window.SBsdk);`         
            
   
the **functions** are found in  `console.log(window.SBsdk.SBfunctions)`             
the **Globals** are found in  `console.log(window.SBsdk.SBdata)`              
              
   Page information:
-------------------------------------------- 
        
>  * Page Info            
```        
console.log(window.SBsdk.SBdata.page);      
//you will get the following:       
id: '', // id of product for example (if there is no id by default it will be empty)     
type: '', // possible values: Product | Basic Page | Collection | Order       
action: '', // possible values: Add | Edit | View         
query_strings: '' // ex. ?a=34&b=34        
``` 
                                  
*Extra information are given in the page data in some pages*            
                   
```
 { // checkout complete page
  type: 'checkout',
  action: 'complete',
  order: {
    total_amount: '',
    currency_code: '',
    currency_symbol: '',
  }
 { // checkout page
  type: 'checkout',
  action: 'checkout',
  order: {
    total_amount: '',
    currency_code: '',
  }
{ // order history page
  type: 'order',
  action: 'history view',
  order: {
    order_id: '',
  },
}

```

Events executed on page load:
----------------------------
               
*found in `window.SBsdk.SBdata.page.init_event`:*         
             
- A specific case event. Its executed only when user login through login popup and a query string after-login-event is available       
               
```
{
    id: 'Value of query string after-login-event',
    type: 'user',
    action: 'after login',
    query_strings: '" . $query_strings_text . "'
 }
```
           
- When shipbuilder is configured to redirect products, this event will be executed when redirect happens:      
       
```
{
    id: 'Product url alias',
    type: 'product',
    action: 'product-redirect',
    query_strings: '" . $query_strings_text . "'
  }
```     
     
- When trying to add an out-of-stock product to cart within product drawer: 
     
```
{
    id: 'Product url alias',
    type: 'cart',
    action: 'out-of-stock',
    query_strings: '" . $query_strings_text . "'
}
```
          
- After adding a product to cart within product drawer the page will refresh and fire this event
       
```
{
    id: 'Product url alias',
    type: 'cart',
    action: 'new item',
    query_strings: '" . $query_strings_text . "'
}
```
     
Events executed after ajax requests:
------------------------------------- 
                    
in your implementation of the `function page_events_{{app_id}}(data){}`             
           
*in the parameter* `data`                  
you can get information after an ajax about the following events:                                
                      
- After changing the product varaition within add-to-cart form (For example at product view page or product drawer)           
             
```
 {
    'id' => $node->uuid,
    'type' =>  'product',
    'action' => 'product-variation-change',
    'query_strings' => $query_strings_text,
    'wrapper_id' => 'node-' . $node->nid,
    'wrapper_class' => '',
  },
```

- After updating cart form, for example changing number of purchased products would trigger this event. (Cart form usually found in a block)

```
 {
    'id' => '',
    'type' => 'cart',
    'action' => 'update',
  }
```
      
 - After pressing quick-edit in orders listing page

```
{
    'id' => $order->uuid,
    'type' =>  'order',
    'action' => 'quick-edit',
    'query_strings' => $query_strings_text,
    'wrapper_id' => '',
    'wrapper_class' => 'views-megarow-content-' . $order_id,
  }
```
    
- When any ajax request has been fired within checkout page 

```
  {
    'id' => $order->uuid,
    'type' =>  'checkout',
    'action' => 'ajax-complete',
    'query_strings' => $query_strings_text,
    'order' => array(
      'total_amount' =>  '',
      'currency_code' => '',
      'currency_symbol' => '',
    )
  }
```

- When user login/forgot-password/signup forms has been opened in a popup'

```
{
    'id' => '',
    'type' => 'user',
    'action' => 'login || password|| signup',
    'query_strings' => $query_strings_text,
    'wrapper_id' => 'modalContent',
    'wrapper_class' => '',
  }
```
    
- After creating or updating an address profile'

```
  {
    'id' => $profile->uuid,
    'type' => 'shipping-customer-address || billing-customer-address',
    'action' => 'new || updated',
    'query_strings' => $query_strings_text
  }
```

- When openning add/edit profile forms or when deleting an address profile

```
 {
    'id' => isset($profile->profile_id) ? $profile->uuid : '',
    'type' =>  'shipping-customer-address || billing-customer-address',
    'action' => 'open edit || open add || delete',
    'query_strings' => $query_strings_text
  }
```
         
   maintain data from before login to after login:
--------------------------------------------------
        
call `SBsdk.SBfunctions.notify_after_popup_login(str data)`; while you are logged out with the data you want to maintain (send your data as a string)             
              
use the login popup of the sdk to login..            
after logging in you will be able to get/access your data in `SBsdk.SBdata.page.init_event` where:                
                
SBsdk.SBdata.page.init_event = {
id: 'YOUR DATA',
type: 'user',
action: 'after login',
query_strings: '?after-login-event=YOUR DATA'
}
                             
```    
console.log(window.SBsdk.SBdata.page);      
//you will get the following:       
id: '', // id of product for example (if there is no id by default it will be empty)     
type: '', // possible values: Product | Basic Page | Collection | Order       
action: '', // possible values: Add | Edit | View         
query_strings: '' // ex. ?a=34&b=34         
```
                
         

   
To create a new page :       
----------------------       
            
**to create a front office page use the url of /app_page/:**          
                         
>  /app_page/{name of page}/{query string 1}/{query string 2}/ ...          
                   
`How to use:`          
* 1-  **inject** a new menu title (check: https://gl.eweev.com/ShopBuilder/sb_api/wikis/SB-api-SDK/#add-menu-links-) Or **Update** a menu title url               
This **menu title** should have the **url** of your new page `/app_page/{name of your page}/`             
* 2-  now to get your page information check `window.SBsdk.SBdata.page`       
you will find the name of your page in `window.SBsdk.SBdata.page.id`        
you will find the querystrings of your page in `window.SBsdk.SBdata.page.query_strings`       
you can know the type of your page using `window.SBsdk.SBdata.page.type` in this case it will be **custom-front**      
* 3-  use the pages info (window.SBsdk.SBdata.page) to inject your data and enjoy     
               
            
**to create a back office page use the url of /admin/app_page/:**                    
                         
>  /admin/app_page/{name of page}/{query string 1}/{query string 2}/ ...           
              
`How to use:`          
* 1-  **inject** a new menu title (check: https://gl.eweev.com/ShopBuilder/sb_api/wikis/SB-api-SDK/#add-menu-links-) Or **Update** a menu title url               
This **menu title** should have the **url** of your new page `/admin/app_page/{name of your page}/`             
* 2-  now to get your page information check `window.SBsdk.SBdata.page`       
you will find the name of your page in `window.SBsdk.SBdata.page.id`        
you will find the querystrings of your page in `window.SBsdk.SBdata.page.query_strings`       
you can know the type of your page using `window.SBsdk.SBdata.page.type` in this case it will be **custom-back**  
* 3-  use the pages info (window.SBsdk.SBdata.page) to inject your data and enjoy      
    
   
   