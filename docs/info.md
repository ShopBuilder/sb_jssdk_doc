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
    
   
initialize a carousel:
-----------------------        
`window.SBsdk.SBfunctions.carousel (wrapper_selector, options)`;"    
all options supported with their default values         
`options = { dots: false, speed: 300, slidesToShow: 1, slidesToScroll: 1, variableWidth: false, centerMode: false, adaptiveHeight: false, prevArrow: html of prev elt, nextArrow: html of next elt}`         
     
   
initialize a carousel special for magnific popups:       
-----------------------              
`window.SBsdk.SBfunctions.magnific_popup_carousel (wrapperSelector, options)`;            

**wrapperSelector**  is the selector of magnific popup wrapper      
ex                  
`wrapperSelector = '.field-name-field-image-product  .mfp-gallery-image'`         
                 
all options supported with their default values                    
`options = { dots: false, speed: 300, slidesToShow: 1, slidesToScroll: 1, variableWidth: false, centerMode: false, adaptiveHeight: false, prevArrow: html of prev elt, nextArrow: html of next elt}`                    
             
            
```
Example 

var carousel_options = {
    slidesToShow: 4,
    slidesToScroll: 4,
    nextArrow: '<i class="fa fa-arrow-right"></i>',
    prevArrow: '<i class="fa fa-arrow-left"></i>',
  };
  window.SBsdk.SBfunctions.magnific_popup_carousel('.field-name-field-image-product  .mfp-gallery-image', carousel_options);
```
            
  Use select2 for the existing (selects)
-------------------------------------------- 
        
>  *  To transform your existing select to select2 use the function of `window.SBsdk.SBfunctions.sbsdk_select_style (selector, options)`;   
         
*where*:                     
**Selector** is your select field selector ex: `#selector` or `.selector`       
and **options**: `(optional)`               
> `options = {`         
>   `dropdownCssClass: 'class',` // class that is added for the dropdown     
>   `addAsterisk: defaultValueOfSelectField,` // adding astericks for the default value     
>   `searchable: {placeholder: 'hello'},` // adding a search field with an optional placeholder          
> `}`              
                 
**Note:1 that if you want to call use this library for a select loaded after ajax call this function in page_event_HOOK implementation**             
           
**Note:2 if you want to generate a select2 field, explore the different select2 that can be used:** https://gl.eweev.com/ShopBuilder/sb_api/wikis/SB-api-SDK/#-dom-generation       
       
```   
//example     
window.SBsdk.SBfunctions.sbsdk_select_style('select#edit-field-gender-und',
{
  dropdownCssClass: 'hello-dropdown-css-class',
  addAsterisk:  '_none',
  searchable: {placeholder: 'hello'}
});

```
           
Generate a top action button:        
-------------------------------         
`window.SBsdk.SBfunctions.add_top_action_button(options);`        
where            
options = {**'class'**: 'class', **'id'**: 'idddsss', **'name'**: 'naaameeee', **'Title'**: 'Titleeee'} 
         
![top_action_btn_](/img/top_action_btn_.png)
              

If wanted to add a form submission callback unrelated to inject_custom_data_HOOK :                    
-------------------------------------
         
simply 
1-     
call: `injectCustomFormCallbacks(form_callbacks);`        
where the **form_callbacks** is of the structure:      
`form_callbacks = {'formId1': 'submitHandler1', 'formId2': 'submitHandler2'}`     

2-        
define your callback functions  `function submitHandler1()` & `function submitHandler2()`
           
This callback function should `return true` or `return false`      
OR should `return 'wait'` in case the result is returned asynchronously like ajax result for example..        
simply after that you have the result needed        
call `errorsWaitedToValidateCallback(appId, result);` where the `result should be either true or false`      
        
```
//Example of an ajax form
function callback_hello(){
  alert('HELLOOOO');
}
window.page_events_4705647385 = function(data){
   if($('form[action="/ajax_register/login/ajax"]').length){
       id = $('form[action="/ajax_register/login/ajax"]').attr('id');
       obj = {};
       obj[id] = 'callback_hello';
       window.SBsdk.SBfunctions.injectCustomFormCallbacks(obj);
   }
}
```      

Delete Menu links:             
-------------------
     
To delete menu links use the function of `window.SBsdk.SBfunctions.delete_menu_links (menuLinksData)`;          
where: menuLinksData is of the following structure
>         **Structure of the Parameter that should be given**    
>           menuLinksData = [
>           {
>              menu_type: 'menu_type',
>              menu_urls: ['/menu-url1', 'menu_title2']
>           },
>           {
>              menu_type: 'menu_type',
>              menu_urls: ['/menu-url1', 'menu_title2']
>           },
>           ];
                     
`menu_urls` : array of all the urls of the menu links to be deleted.               
`menu_type` is a string that specifies in which menu do you want to add the menu link.        
It can have the following values:              
*  **menu** : the front office main menu
*  **menu_secondary** the front office secondary menu
*  **footer** the front office footer
*  **footer_secondary** the front office secondary footer
*  **navigation_menu** the back office navbar menu
              
``` 
//Example:
  var menuLinksData = [
        {
           menu_type: 'menu',
           menu_urls: ['/', '/aboutus']
        },
        {
           menu_type: 'footer',
           menu_urls: ['/', '/aboutus']
        },
        {
          menu_type: 'navigation_menu',
          menu_urls: ['/admin/theme_design', '/admin/content']
        }
        ];
window.SBsdk.SBfunctions.delete_menu_links(menuLinksData);
```

Update Menu links:             
-------------------
     
To update menu links use the function of `window.SBsdk.SBfunctions.update_menu_links (menuLinksData)`;          
where: menuLinksData is of the following structure
>         **Structure of the Parameter that should be given**    
>          var  menuLinksData = [
>           {
>             menu_type: 'menu_type',
>             data: [
>               {
>                menu_link:        {menu_title: 'menu_title1' ,  menu_url: '/menu-url1' },
>                update_menu_link: {menu_title: 'menu_title1_new' ,  menu_url: '/menu-url1_new' },
>               },
>               {
>                menu_link:        {menu_title: 'menu_title1' ,  menu_url: '/menu-url1' },
>                update_menu_link: {menu_title: 'menu_title1_new' ,  menu_url: '/menu-url1_new' },
>               }
>             ]
>           },
>           {
>             menu_type: 'menu_type2',
>             data: [
>               {
>                menu_link:        {menu_title: 'menu_title1' ,  menu_url: '/menu-url1' },
>                update_menu_link: {menu_title: 'menu_title1_new' ,  menu_url: '/menu-url1_new' },
>               }
>             ]
>           },
>           ];
                     
`menu_title` : is the title of the menu link.           
`menu_url` : is the url of the menu link.           
`menu_link` : old menu links data
`update_menu_link` : new menu links data        
`menu_type` is a string that specifies in which menu do you want to add the menu link.        
It can have the following values:              
*  **menu** : the front office main menu
*  **menu_secondary** the front office secondary menu
*  **footer** the front office footer
*  **footer_secondary** the front office secondary footer
*  **navigation_menu** the back office navbar menu
              
``` 
//Example:
var  menuLinksData = [
          {
            menu_type: 'menu',
            data: [
              {
               menu_link:        {menu_title: 'Contact Us' ,  menu_url: '/contact' },
               update_menu_link: {menu_title: 'Contact Me' ,  menu_url: '/contact-me' },
              },
              {
               menu_link:        {menu_title: 'Collections ' ,  menu_url: '/collection/Collections' },
               update_menu_link: {menu_title: 'My Collection' ,  menu_url: '/collection/Collections' },
              }
            ]
          },
          {
            menu_type: 'footer',
            data: [
              {
               menu_link:        {menu_title: 'Contact Us' ,  menu_url: '/contact' },
               update_menu_link:{menu_title: 'Contact meeee' ,  menu_url: '/contact' },
              }
            ]
          },
          {
            menu_type: 'navigation_menu',
            data: [
              {
               menu_link:        {menu_title: 'Collections' ,  menu_url: '/admin/collections' },
               update_menu_link:{menu_title: 'My collections' ,  menu_url: '/admin/collections' },
              }
            ]
          },
    ];
  window.SBsdk.SBfunctions.update_menu_links(menuLinksData);
```
 
Add Menu links:             
-------------------
     
To add menu links use the function of `window.SBsdk.SBfunctions.inject_menu_links (menuLinksData)`;          
where: menuLinksData is of the following structure      
//Note that order starts from value 0          
         
>         **Structure of the Parameter that should be given**    
>         menuLinksData = [          
>         {             
>            menu_type: 'menu_type',           
>            data: [          
>              {menu_title: 'menu_title1' ,  menu_url: '/menu-url1', id:"id1", order: '1', class:'class1 class2' },      
>              {menu_title: 'menu_title2' ,  menu_url: '/menu-url2', id:"id2", order: '1', class:'class1 class2' ,       
>                 sub_menu: [        
>                   {menu_title: 'menu_sub_title' ,  menu_url: '/menu-sub-url' }    
>                 ]        
>               }       
>            ]       
>         },        
>         {        
>            menu_type: 'menu_type',       
>            data: [        
>              {menu_title: 'menu_title' ,  menu_url: '/menu-url', id:"id3", order: '1', class:'class1 class2' }      
>             ]         
>         },         
>         ];        
                     
`menu_title` : is the title of the menu link.           
`menu_url` : is the url of the menu link.           
`sub_menu` : is an array of the menu links.        
`menu_type` is a string that specifies in which menu do you want to add the menu link.        
It can have the following values:              
*  **menu** : the front office main menu
*  **menu_secondary** the front office secondary menu
*  **footer** the front office footer
*  **footer_secondary** the front office secondary footer
*  **navigation_menu** the back office navbar menu
              
``` 
//Example:
  var menuLinksData = [
        {
           menu_type: 'menu',
           data: [
             {menu_title: 'menu_title1' ,  menu_url: '/menu-url1', id:"id1", order: '1', class:'class1 class2'  },
             {menu_title: 'menu_title2' ,  menu_url: '/menu-url2', id:"id2", order: '1', class:'class1 class2', 
                sub_menu: [
                  {menu_title: 'menu_sub_title' ,  menu_url: '/menu-sub-url' }
                ]
              }
           ]
        },
        {
           menu_type: 'footer',
           data: [
             {menu_title: 'menu_title' ,  menu_url: '/menu-url', id:"id3" , order: '1', class:'class1 class2' }
            ]
        },
        ];
window.SBsdk.SBfunctions.inject_menu_links (menuLinksData);
```
 
   Validating user token:
--------------------------------------------      
                  
>  * To verify the user token  **call :** `window.SBsdk.SBfunctions.verify_user_token(appID, user_token);`   
         
>  * Website Id & User Info  
```        
console.log(window.SBsdk.SBdata.SBwebsite_data);      
//you will get the following:       
website_id // This is gona be the website id ex.1234568789
user_id // id of user ex. 30       
user_role // Array of user roles ex. ["Authenticated", "merchant"]                
```


       
   Dialog:
--------------------------------------------      
                  
>  * To have a dialog:       
call SBsdk.SBfunctions.dialog(content, options);          
               
>  * *Where:*     
**content** is a string with the dom needed
**options** is a an object          
     
//options = { wrapperClass: '' , Title Str, SaveBtn: Str nameSaveBtn, buttons: ['button1name','button2name'], lockDialog: 1 }  (Optional)                 
OR        
//options = { wrapperClass: '' , Title:{name: '', class: ''}, SaveBtn: {name: '', class: ''}, buttons: [{name:'', class:''},{name:'', class:''}], lockDialog: 1}(Optional)          
                    
1- To hook after the dialog has been opened                    
                             
`$(document).on("sBsdkDialogReady",function(e) {});`                     
          
2- `lockDialog`: disables the close of a dialog upon an open of another dialog                        
Ex.      
```    
    
SBsdk.SBfunctions.dialog('<p>Dom content here</p>', {Title: "Title","SaveBtn" : 'save'}); 

window.SBsdk.SBfunctions.dialog('<div class="hello">heyyy<div>', {Title: 'hello Title', SaveBtn: 'save me', buttons: [{name:'hello', class: 'helloClass'}, {name:'hey', class: 'heyClass'}]});       
 
window.SBsdk.SBfunctions.dialog('<div class="hello">heyyy<div>', {Title: 'hello Title', SaveBtn: 'save me', buttons: ['hello', 'hello2'] });  
                    
```        
             
Date Actions:
-------------------
*Prototype:*                
`function SBsdk.SBfunctions.date_actions(selector, action, value)`        
                        
> **Selector** ex `Selector = '#inputSelector'` || `Selector = '#inputSelector'`           
> **action** can be 'set' || 'get'             
> **value** use in case of `action = 'set'` ex. `value= '12/13/2018'`    
         
           
*Example:*                     
```
jQuery('body').append(window.SBsdk.SBfunctions.generateDom('textfield', {
      wrapperClass: 'wrapper-date',
      Title: 'Title',
      type: 'date',
      id: 'datee',
      name: 'date',
      maxlength: 255,
})
);
SBsdk.SBfunctions.refresh();
SBsdk.SBfunctions.date_actions('#datee', 'set', '5/30/1994');
console.log(SBsdk.SBfunctions.date_actions('#datee', 'get'));
```       
                 
 Dialog actions
--------------------------------------------  
The function prototype:            
`SBsdk.SBfunctions.dialog_actions(dialog_wrapperClass, action);`   
the action is by default set to **remove**                
          
// to close a specific dialog wether locked or not              
`SBsdk.SBfunctions.dialog_actions(dialog_wrapperClass);`        
             
//to close all dialogs from page..          
`SBsdk.SBfunctions.dialog_actions();`               
      
//To close all dialogs but not the locked dialogs          
`ex. SBsdk.SBfunctions.dialog_actions('.sbsdkDialog:not(.dialog-locked)');`         
      

       
   View page:
--------------------------------------------      
        
For a better experience for the users:      
                  
>  * By default the page will be hidden so,      
after inserting all that you need into your page      
**call :** `window.SBsdk.SBfunctions.SBsdk_page_loaded();` to view your page      
        
      
User Forms: Login - Register - Forget Pass:
------------------------------------------         

*calling:*              
`SBsdk.SBfunctions.SBsdk_user_forms(action, type);`                      
where the parameter:            
**action** can be:                      
1- **'login'**         
2- **'register'**                  
3- **'forget-password'**           
             
**type** (optional): by default it takes you to the `action` page           
give it **'popup'** to open the `action` popup        
                      
*example calling:*           
`SBsdk.SBfunctions.SBsdk_user_forms('register', 'popup');`            
will open the register  popup              
               

   Messages:
--------------------------------------------         
         
>  * messages:       
```
console.log (window.SBsdk.SBfunctions.SBmessages);      
// This will list all the functions that can be used to print a message   
```    
**Prototype:**             
*   `window.SBsdk.SBfunctions.SBmessages.set_message(msg, type, options);`         
**parameters**:     
1-   *msg* is a String           
2-   *type* is a String -- *possible values*: success, warning, error            
3-   *options*  -- (Optional)                 
        options = {        
           positionInWrapperId: 'the-id-of-wrapper-you-want-to-append-this-to',       
           className : 'give-custom-class-name-for-the-alert-div',       
           scrollToError : 1 (Only for type error)      
        }                
                       
**Examples**: 
``` 
    window.SBsdk.SBfunctions.SBmessages.set_message('hello there error', 'error');
    window.SBsdk.SBfunctions.SBmessages.set_message('hello there success', 'success');
    window.SBsdk.SBfunctions.SBmessages.set_message('hello there warning', 'warning');
    window.SBsdk.SBfunctions.SBmessages.set_message('hello there warning in navbar position', 'warning', { positionInWrapperId :'navbar'});     
    window.SBsdk.SBfunctions.SBmessages.set_message('error', 'error', {className: 'sdk-errors'});
```   
            
**Preview**:        
![screen_shot](/img/screen_shot.png)      
                                      
     

         
   Dom Generation:
--------------------------------------------   
         
>  * Generate Dom:       
**Prototype:**             
*   `window.SBsdk.SBfunctions.SBmessages.generateDom(type, options);`         
**parameters**:     
1-   *type* is a String      
2-   *options* is an obj      

| preview | type | options |
| ------- | ------- | -------- |
| no preview |  textfield  |`"wrapperClass", "type": "password" || "date" (keep empty if textfield)), "class", "name", "value", "Title", required: 1, "placeholder", "id", "size", "maxlength"`|
| ![normal_select](/img/normal_select.png) |   select (common options for all selects)  |`"wrapperClass", "class", "id", "Title", required: 1, "options" :[{value, selected, label, class}] , 'select2': {options of a normal select2 library}, multiple: 1, name  `|
| ![multipleSelect](/img/multipleSelect.png) |   select (multiple)  | ` multiple: 1, name  `|
| ![singleSelect2](/img/singleSelect2.png) |   select2 (single)  | ` "select2": {dropdownCssClass: 'basetheme-select2-dropdown',addAsterisk: defaultValueOfSelectField,searchable: {placeholder: 'hello'}} `|
| no preview |   select2 (image)  | `"options" :[{value:'val', selected:1, label:'label', ` **imageSelect2: 'url', customHtml: "html"** `},{...],  "select2": {` **image: 1** `, options of the previous select2(single)} `|
| ![multipleselect2](/img/multipleselect2.png) |   select2 (multiple)  | `'select2': {options of the previous select2(single)},  multiple: 1, name `|
| ![widget](/img/widget.png) |   select2 (widget)  | ` 'select2': {dropdownCssClass, image: 1},  multiple: 1, name, widget: { emptyText, addButtonClass}  `|
| no preview |   upload   |`"wrapperClass", "class", "name", "value", "Title", required: 1, "id", "size"`|
| no preview | textarea | `"wrapperClass", "id" , "Title","required", "value" ,"class", "name","rows", "cols","placeholder", "maxlength"` |
| ![wysiwyg](/img/wysiwyg.png) | wysiwyg | `"wrapperClass" (wrapper of wysiwyg), "Title","required", "value" (string contains markup --can be html), "class" (Required)`|
| no preview | link | `download : 1, "wrapperClass", "class", "href", "id", "Title", "target", wrapper: 1, "type"` (note that if you want a link with no wrapper .. remove the wrapper option ) (**type** can be "btn", "delete" or "add") |
| no preview | fieldset | `"wrapperClass", "mainTitle", "subTitle","content"` |
| ![sectionTitle](/img/sectionTitle.png) | section_title | `"Title"` |
| ![sectionSubtitle](/img/sectionSubtitle.png) | section_subtitle | `"Title"` |
| ![sectionSubtitle](/img/sectionSubtitle.png) | section_subtitle | `"Title"` |
| ![Screenshot_at_2018-01-29_09_11_17](/img/Screenshot_at_2018-01-29_09_11_17.png) | image_upload |  `"Title", "TitleClass", "wrapperId" , multiple : 1, inputFileData:{ max, id, name, size, class }, deleteFileData:{name, id, class} ,uploadFileData:{ name, id, class}, "values"(if there are already images)` values can be an array of objs (multple image upload) or an array (single image) |
| ![radios](/img/radios.png) | radio | `"wrapperClass"(required) , "name": "common for all radios", disabled:1 , data: [{"Title", "class", "id", "value", "checked"},{"Title", "class", "id", "value", "checked"}]` |
| ![checkbxes](/img/checkbxes.png) | checkbox | `"wrapperClass", "toggle": 1 (optional) , data: [{"Title", "class", "id",  "name", "value", "checked", "disabled"},{"Title", "class", "id",  "name", "value", "checked", "disabled"}]` |
| no preview | button | `"Title", "class", "id"` |

**Examples**:  
```
Ex Radio

window.SBsdk.SBfunctions.generateDom('radio', {
 "wrapperClass": 'hellooooo', // wrapper class
"name" : "name_radios[name_radios]" , // (required) name that is provided for all the radio inputs 
   data: [ 
     {"Title":'ok', "class": 'ddd', "id": 'dddff',  "value":  1, "checked": 1},
     {"Title": 'ok1', "class": 'aaa', "id": 'ssd', "value": 0}
   ]
});


   
Ex. checkboxes:

window.SBsdk.SBfunctions.generateDom('checkbox', {
  "wrapperClass": 'hellooooo',
  "toggle": 1, // optional in case you wanted a toggle like checkbox
   data: [ 
     {"Title":'ok', "class": 'ddd', "id": 'dddff',  "value":  1, "checked": 1},
     {"Title": 'ok1', "class": 'aaa', "id": 'ssd', "value": 0, "disabled": 1}
   ]
});


Ex. text field     
                  
window.SBsdk.SBfunctions.generateDom('textfield', {
      wrapperClass: 'sb-manage-input',
      Title: 'Title',
      required: 1,
      placeholder: 'placeholder',
      id: 'i-am-an-id',
      name: 'hello',
      size: 60,
      maxlength: 255,
      value: 'default value'
});
                     
// this will generate                  
// <input class="text-full form-control form-text  required " placeholder="placeholder" type="text" id="i-am-an-id" name="hello" value="default value" size="60" maxlength="255"> 
      
    
Ex. Fieldsets
window.SBsdk.SBfunctions.generateDom('fieldset', {
      wrapperClass: 'fieldset-custom-wrapper', 
      mainTitle: 'Hello fieldset',
      subTitle: '<img ..><img ..>',
      content: 'fieldset content fieldset content fieldset content '
});
     
         
Ex. normal select      
       
 window.SBsdk.SBfunctions.generateDom('select', {
       required:1,
       Title : 'hello select',
       wrapperClass: 'select-dummy-wrapper-class',
       class: 'select-dummy-class',
       id: 'id-select',
       options: [
           {'value': 'one', 'label' : 'one'},
           {'value': 'two', 'selected' : 'selected' , 'label' : 'two'}
       ]
});                                  
// this will generate                    
// <div class="form-item form-type-select form-group select-dummy-wrapper-class "><label class="control-label" for="id-select">hello select <span class="form-required" title="This field is required.">*</span> </label><select class=" select-dummy-class " id=" id-select "><option "="" value="one">one</option><option "="" value="two" selected="selected">two</option></select></div>           
      
Ex. for a select2 library -- select2
// before to $('select').select2(options) where options = {'dropdownCssClass':'sb-intro-theme-drop'}     
             
// After:     
        
window.SBsdk.SBfunctions.generateDom('select', {
  select2: {'dropdownCssClass':'sb-intro-theme-drop'} ,
  options: [
     {'value': 'one', 'label' : 'one'},
     {'value': 'two', 'selected' : 'selected' , 'label' : 'two'}
  ]
});
        
//if you want a normal select field dont add {{select2}} option   
// if you want the select2 but you dont have options for it:     select2: {}      
      
Ex. Multiple select2

           
window.SBsdk.SBfunctions.generateDom('select', {
  select2: {} , // Dont include if you want a normal multiple select
  multiple: 1,
  name : 'multi[]',
  options: [
     {'value': 'one', 'label' : 'one'},
     {'value': 'two', 'selected' : 'selected' , 'label' : 'two'}
  ]
});

Ex. Multiple select2 widget:

window.SBsdk.SBfunctions.generateDom('select', {
  select2: {} , // Dont include if you want a normal multiple select
  multiple: 1,
  name : 'multi[]',
  widget: {
            emptyText: 'Type To Add A new X', 
            addButtonClass: 'add-x-functionality'
  },
  options: [
     {'value': 'one', 'label' : 'one'},
     {'value': 'two', 'selected' : 'selected' , 'label' : 'two'}
  ]
});
        
Ex. Multiple select2 widget:

window.SBsdk.SBfunctions.generateDom('select', {
  select2: {
     image:1,
     dropDownCss: 'drop-down-class',
  } , // Dont include if you want a normal multiple select
  multiple: 1,
  name : 'multi[]',
  widget: {
            emptyText: 'Type To Add A new X', 
            addButtonClass: 'add-x-functionality'
  },
  options: [
     {
      'value': 'one', 
      'label' : 'one',
       class: 'class1',
       imageSelect2: 'https://../img-url.png',
       customHtml : '<div>Good bye</div>'
     },

     {'value': 'two',
      'selected' : 'selected' ,
       class: 'class2',
      'label' : 'two',
       imageSelect2: 'https://../img-url.png',
       customHtml : '<div>Good Morning</div>'
     }
  ]
});
    
Ex. Image upload:

window.SBsdk.SBfunctions.generateDom('image_upload', {
  Title: Image Upload Section Title , // Title of the upload
  TitleClass: 'image-upload-section-title-class'  , // class of the title
  multiple: 1, // if we want to upload multiple images (dont use if you want a single image uploader)
  wrapperId : 'wrapper-Id-of-the-image-uploader', 
  inputFileData: { // info needed for the input type file (that uploads the image)
            name: 'a', 
            id: 'hello',
            max: '1234556',
            size: '1234',
  },
  uploadFileData: { // upload button that is clicked automatically after the upload
            name: 'a1', 
            id: 'hello1',
  },             
  // *values* is used if there are already existing images added         
  // if multiple image upload then:          
  values: [
    {viewImgData:{ width, height, src, alt }, deleteFileData:{ id, name}, muliple:1 },
    { viewImgData:{ width, height, src, alt }, deleteFileData:{ id, name}, muliple:1},
    { viewImgData:{ width, height, src, alt }, deleteFileData:{ id, name}, muliple:1 },
  ]       
   // if single image upload
   values: { viewImgData:{ width, height, src, alt }, deleteFileData:{ id, name} }          
  //(Where viewImgData is all the information for the image)
  //(and   deleteFileData is all the information for the delete button of the image)
  // and *multiple* is required for muliple image upload    
});
        
wysiwyg example:        
                 
window.SBsdk.SBfunctions.generateDom('wysiwyg', {"wrapperClass": 'hello', "class": 'class-editor' , "Title": 'title',"required":1, "value": '<h2>Hello</h2><p>world!</p>'});
      
```            
  Radios actions: (ie. radio that is generated using generateDom)           
------------------------------------------------------         
`SBsdk.SBfunctions.radio_action(inputSelector_wrapperSelector, action);`         
                          
`action` can be `reset, disable, enable or check`        
             
for the actions `reset, disable, enable` the inputSelector_wrapperSelector should be the wrapperClass of the radios            
     
for the actions `check` the inputSelector_wrapperSelector should be the radio input        
                 
*note that when radios are disabled you can not check radios*              
              
                         
```
SBsdk.SBfunctions.radio_action('.shopbuilder-radio', 'reset');
SBsdk.SBfunctions.radio_action('.shopbuilder-radio', 'disable');
SBsdk.SBfunctions.radio_action('.shopbuilder-radio', 'enable');
SBsdk.SBfunctions.radio_action('.shopbuilder-radio input:last', 'check');
```
        
selects actions:
--------------------------
*if you generate a select with out the option of select2 then you can simply do your operations using jQuery or js to append options del options .. etc*             
However if you have chosen the option of the select2 then have a look at this function:

`SBsdk.SBfunctions.select2_action(select_selector, action, array_of_options)`
        
`action` parameter can be **append** or **reset**      
append appends on existing options                
reset removes old options and adds new options            

Example:
```
// normal options 
options = [
{class:'aa', value: 'abcd' , label: 'ra', imageSelect2: 'img-src.png',customHtml: ''},
{class:'bb', value: 'abcdabcd', selected: 1, label: 'ma', imageSelect2: 'img-src.png',customHtml: 'editme'},
{class:'cc', value: 'abcdabcdabcd', selected: 1, label: 'fa', imageSelect2: 'img-src.png',customHtml: 'editme'},
];
// parm(0) is the select_selector ("class") ex. ".selector_select"
// parm(1) is the action which is append..
// parm(2) is the new options wanted to be appended..
SBsdk.SBfunctions.select2_action('.selector_select', 'append', options);
```       


  Checkboxes actions: (ie. checkbox that is generated using generateDom)  
--------------------------------------------                 
`SBsdk.SBfunctions.checkbox_action(inputSelector, action);`                   
**inputSelector**: ex "input.input-selector" OR "input#input-selector"                 
**action**: enable, disable, check, uncheck                
Ex:             
```
SBsdk.SBfunctions.checkbox_action('.input-selector', 'uncheck');        
SBsdk.SBfunctions.checkbox_action('.input-selector', 'check');        
SBsdk.SBfunctions.checkbox_action('.input-selector', 'disable');        
SBsdk.SBfunctions.checkbox_action('.input-selector', 'enable');        
```

to get or set the wysiwyg data:
----------------------------
`var data = SBsdk.SBfunctions.wysiwyg_data(class_of_wysiwyg);` // gets the data      
`var data = SBsdk.SBfunctions.wysiwyg_data(class_of_wysiwyg, 'set', rawHtml);` // set data       

**Example**          
```
SBsdk.SBfunctions.wysiwyg_data('.pop-up', 'set', '<p>hello</p>'); // sets the html inside the wysiwyg           
SBsdk.SBfunctions.wysiwyg_data('.pop-up'); // returns the html inside the wysiwyg that is <p>hello</p>         
```

wysiwyg configuration:
----------------------------
                 
by default you will have the cofiguration used in Shopbuilder plateform that is               
```
config = {};
config.toolbar = ["Bold", "Italic", "Underline", "JustifyLeft", "JustifyCenter", "JustifyRight", "JustifyBlock", "BidiLtr", "BidiRtl", "BulletedList", "NumberedList", "Outdent", "Indent", "Undo", "Redo", "Link", "Unlink", "Anchor", "Image", "Cut", "Copy", "Paste", "PasteText", "PasteFromWord", "RemoveFormat", "Format", "FontSize", "video_filter"];
```
                          
To change the configuration of all of your wysiwyg use:       
`window.SBsdk.SBfunctions.wysiwyg_configuration(config);`      
                 
         
*The config parameter can be given the same values as a ckeditor config*                      
  
**1-To get all of the supported items in a wysiwyg**         
`config = {};`         
`window.SBsdk.SBfunctions.wysiwyg_configuration(config);`           
             
`config = {}` equivalent to:
```
			config= {};
			// Toolbar configuration generated automatically by the editor based on config.toolbarGroups.
			config.toolbar = [
				{ name: 'document', groups: [ 'mode', 'document', 'doctools' ], items: [ 'Source', '-', 'Save', 'NewPage', 'Preview', 'Print', '-', 'Templates' ] },
				{ name: 'clipboard', groups: [ 'clipboard', 'undo' ], items: [ 'Cut', 'Copy', 'Paste', 'PasteText', 'PasteFromWord', '-', 'Undo', 'Redo' ] },
				{ name: 'editing', groups: [ 'find', 'selection', 'spellchecker' ], items: [ 'Find', 'Replace', '-', 'SelectAll', '-', 'Scayt' ] },
				{ name: 'forms', items: [ 'Form', 'Checkbox', 'Radio', 'TextField', 'Textarea', 'Select', 'Button', 'ImageButton', 'HiddenField' ] },
				'/',
				{ name: 'basicstyles', groups: [ 'basicstyles', 'cleanup' ], items: [ 'Bold', 'Italic', 'Underline', 'Strike', 'Subscript', 'Superscript', '-', 'CopyFormatting', 'RemoveFormat' ] },
				{ name: 'paragraph', groups: [ 'list', 'indent', 'blocks', 'align', 'bidi' ], items: [ 'NumberedList', 'BulletedList', '-', 'Outdent', 'Indent', '-', 'Blockquote', 'CreateDiv', '-', 'JustifyLeft', 'JustifyCenter', 'JustifyRight', 'JustifyBlock', '-', 'BidiLtr', 'BidiRtl', 'Language' ] },
				{ name: 'links', items: [ 'Link', 'Unlink', 'Anchor' ] },
				{ name: 'insert', items: [ 'Image', 'Flash', 'Table', 'HorizontalRule', 'Smiley', 'SpecialChar', 'PageBreak', 'Iframe' ] },
				'/',
				{ name: 'styles', items: [ 'Styles', 'Format', 'Font', 'FontSize' ] },
				{ name: 'colors', items: [ 'TextColor', 'BGColor' ] },
				{ name: 'tools', items: [ 'Maximize', 'ShowBlocks' ] },
				{ name: 'others', items: [ '-' ] },
				{ name: 'about', items: [ 'About' ] }
			];
			// Toolbar groups configuration.
			config.toolbarGroups = [
				{ name: 'document', groups: [ 'mode', 'document', 'doctools' ] },
				{ name: 'clipboard', groups: [ 'clipboard', 'undo' ] },
				{ name: 'editing', groups: [ 'find', 'selection', 'spellchecker' ] },
				{ name: 'forms' },
				'/',
				{ name: 'basicstyles', groups: [ 'basicstyles', 'cleanup' ] },
				{ name: 'paragraph', groups: [ 'list', 'indent', 'blocks', 'align', 'bidi' ] },
				{ name: 'links' },
				{ name: 'insert' },
				'/',
				{ name: 'styles' },
				{ name: 'colors' },
				{ name: 'tools' },
				{ name: 'others' },
				{ name: 'about' }
			];
```        
           
**2-you can customize your configuration to get the desired wysiwyg**     
               
**Example**:                      
*// must be the 1st function to execute before any wysiwyg injection*        
`config= {};`           
`config.toolbar = [ 'Cut', 'Copy', 'Paste', 'PasteText', 'PasteFromWord', '-', 'Undo', 'Redo' ]`             
`window.SBsdk.SBfunctions.wysiwyg_configuration(config);`             
               
// then insert your wysiwyg               
`dom = window.SBsdk.SBfunctions.generateDom('wysiwyg', {"wrapperClass": 'hello', "class": 'class-editor' , "Title": 'title',"required":1, "value": '<h2>Hello</h2><p>world!</p>'});`     
                                 
// to get the value of the inputed                             
`data = SBsdk.SBfunctions.wysiwyg_data('.class-editor');`          
                                
   Refresh:
--------------------------------------------      
         
incase of **ajax** or *inserting dom* using **dom generation** functions        
if there are some libraries not being initialized       
call the refresh function:           
`window.SBsdk.SBfunctions.refresh();`         
                 
         
        
   Dom Injection:
--------------------------------------------   
         
         
>  * To insert Dom       
`implement: function inject_custom_data_{{appId}}(){}`    
**parameters** : no parameters provided          
**Return** must be of the following format:

Step 1-   *generate your dom and specify the name of your submit handler*     
          
`//Case 1) If the DOM being generated inside function.
ret = 
{
  'data' : [{'id': id1, 'dom': dom1}, {'id': id2, 'dom': dom2}, {'id': id3, 'dom': dom3}],
  'submitHandler': 'submissionCallbackExample', // for single submit handler per page
  'submitHandler': {'formId1': 'submitHandler1', 'formId2': 'submitHandler2'} // for multiple form per page
};
//OR Case 2)
//if the dom is being available asynchronously from another function
ret = { 'wait' : {{appId}} }`     
    
Step 2-  *Define your submit Handler*        
Your submit handler return must be either `(boolean) true` or `(boolean) false`    
                        
`//Case 1) If the DOM being generated inside function.
ret = 
{
  'data' : [{'id': id1, 'dom': dom1}, {'id': id2, 'dom': dom2}, {'id': id3, 'dom': dom3}],
  'submitHandler': 'submissionCallbackExample',
};
//OR Case 2)
//if the dom is being available asynchronously from another function
ret = { 'wait' : {{appId}} }`  

**Example For Step1 Case 1)**:          
     
```
  
  window.submissionCallbackExample = function (){
   // here you must add your listeners to validate your custom data before submission 
    return true;
  };

  window.inject_custom_data_456 = function(){

   dom1 = window.SBsdk.SBfunctions.generateDom('textfield', {class: 'textfield-dummy-class',wrapperClass: 'textfield-wrapper-dummy-class',Title: 'Title',required,placeholder: 'placeholder',id: 'i-am-an-id',name: 'hello',size: 60,maxlength: 255,value: 'default value'});
    dom1 += window.SBsdk.SBfunctions.generateDom('link', {wrapperClass: 'link-wrapper', class: 'link-class', href: '#', id: 'link-id', Title: 'Hello link'});

    dom = '<h1>I Love You</h1>';

    id1 = 'textfield';

    id = 'returnedID';

    ret = {
      'data' : [{'id': id, 'dom': dom}, {'id': id1, 'dom': dom1}], 
       // dom will be appended after body by default unless positioned
      'submitHandler': 'submissionCallbackExample', 
       // (optional) you can skip this if you have no form
    };

    ret = {
      'data' : [{'id': id, 'dom': dom}, {'id': id1, 'dom': dom1}], 
       // dom will be appended after body by default unless positioned
      'submitHandler': {'formId1': 'submitHandler1', 'formId2': 'submitHandler2'}
       // (optional) you can skip this if you have no form
    };

    return ret;
  };

```


**Example For Step1 Case 2)**:          
     
```
// if for Some reason you don't need to return the data at this level
// and you want to provide the data from another scope asynchronously:

//Step1:
  
   window.inject_custom_data_123 = function(){
    // you return wait with the {{appId}}
    // to tell the hook to wait for the dom of this function
    return { 'wait' : '123' };
  }

// Now to populate data of 123 in some other scope or function:
// All that you have to do is call: window.SBsdk.SBfunctions.injectCustomDataCallback
// Ex.

//Step2:
// prepare the data in this structure:
var returnOf123Callback = {
     'data' : [{'id': '123Id-1', 'dom': '<h1>dom1</h1>'}, {'id': '123Id-2', 'dom': '<h1>dom2</h1>'}],
      'submitHandler': 'submissionCallbackExample', // (optional)
    };
// call this function giving it the appId and the Dom data
window.SBsdk.SBfunctions.injectCustomDataCallback(123, returnOf123Callback);
```
         


**Example For Step2**:          
     
```
//case 1 -- If no errors your submit handler must return true
  
  window.submissionCallbackExample = function (){
   // here you must add your listeners to validate your custom data before submission 
    return true;
  };

//case 2 -- in case of errors your submit handler must return an object containing those errors     
 
  window.submissionCallbackExample1 = function (){
    return false;
  };
```
       
   Dom Position:
--------------------------------------------   
         

>  * To position DOM :                
`implement: function inject_position_data_{{appId}}(appId, appId_dom_data){}`          
**parameters** :                   
`appId` // will contain the app id                       
`appId_dom_data` // will have all the Dom data for this specific app to be able to position them                          
                        
**Return**          
`an array of the elements that have been positioned`           
              
**Examples**:                       
``` 
// To inject the data
  window.inject_custom_data_568 = function(){
    // populate the dom
    dom1 = '<h1>DOMM 1</h1>';
    dom2 = '<h1>DOMM body</h1>';
    dom3 = '<h1>DOMM 2</h1>';
    // specify a descriptive id for each dom
    id1 = 'collectionDom'; // will be positioned in inject_position_data_568
    id2 = 'bodyyy';        // will not be positioned in inject_position_data_568  
                           // and so will be appeneded to the body
    id3 = 'productDom';    // will be positioned in inject_position_data_568

    ret = {
      'data' : [{'id': id1, 'dom': dom1}, {'id': id2, 'dom': dom2}, {'id': id3, 'dom': dom3}],
      'submitHandler': 'submissionCallbackExample', // optional in case wanted to handle submission
    };

    return ret;
  };

// To position the previously populated dom .. we only need to know the id of each dom

  window.inject_position_data_568 = function(appId, appId_dom_data){


    var title = document.createElement('div');
    title.className = 'collectionDom';
     // Step:1) GET THE DOM: appId_dom_data[ID]
    title.innerHTML = appId_dom_data['collectionDom'];
     // Step:2) insert it where wanted
    document.getElementById('block-system-main').appendChild(title);

    var title2 = document.createElement('div');
    title2.className = 'productDom';
    title2.innerHTML = appId_dom_data['productDom'];
    document.getElementById('block-system-main').appendChild(title2);

     // Step:3) return an array of keys of the positioned ids
    return ['collectionDom', 'productDom'];
  };

```   
                                      
   
  To reattach the js of a Drupal Theme on elements injected using sdk :
--------------------------------------------   
in the Drupal theme js file:           
`implement: function theme_function_{{theme_descriptive_name}}(sdk){}`        
**Return** the machine name of the theme u are in                   
**parameters** :                       
*  `typeof sdk == 'undefined'` the sdk did not call this function (the call happened from inside the theme itself)                 
*  else `sdk = 'sdk'` if the sdk called this function                   
         
Step 1:
in the theme, outside the behaviors define a function:
```
  // to be called inside the theme and available for the sdk
  window.theme_function_{{HOOK}} = function(sdk){
    if(typeof sdk != 'undefined' && sdk == 'sdk'){
      // the sdk is the one calling this function
    }else{
      // theme is calling this function
    }
   
  // YOUR CODE THAT WILL EXECUTE FOR THE THEME AND WILL BE REATTACHED ON SDK LOAD.

  // DONT FORGET TO RETURN THE THEME MACHINE NAME FOR EXTRA INFO

  return {{THEME_MACHINE_NAME}}
}
```

Step 2: 
call this function inside the js file of the theme where its wanted (Whether inside behaviors or outside it)

Ex.
```
 Drupal.behaviors.{{theme_descriptive_name}} = {
    attach: function (context) {

      // theme is calling this function
      window.{{theme_descriptive_name}}();

    }
 }
```

    
   Css Injection:
--------------------------------------------   
              
>  * To inject custom css :       
`implement: function inject_css_{{appId}}(){}`    
**parameters** : no parameters provided          
**Return** 
`you can return inline or external css`   
  
Look at the example to see how to achieve this. 

**Examples**: 
``` 
  window.inject_css_789 = function(){

    return [
      {
        css : 'body{background-color: darkgray;}',
        type : 'inline'
        // adds a style tag to the head with the css written
      },
      {
        css : 'https://yourWebsite.com/{{path}}/test789.css',
        type : 'external'
        // adds a link tag stylesheet with href *test789.css* to the head
      },
      {
        css : 'h1{color: aqua !important;}',
        type : 'inline'
        // adds a style tag to the head with the css written
      },
    ]
  };

```   
      
        
   Respond to page ajax events:
--------------------------------------------   
              
>  * To act upon page ajax events :       
`implement: function page_events_{{appId}}(){}`    
**parameters** : data array  
Parameter example 
```
'id' => '',
'type' => 'user',
'action' => 'signup',
'query_strings' => $query_strings_text,
'wrapper_id' => 'modalContent',
'wrapper_class' =>
```
**Return** 
`No return`   
  
Look at the example to see how to achieve this. 

**Examples**: 
``` 
  window.page_events_111 = function(data){
    // Act on data.
  };

```   
              
>  * wait for asyncronous :       
`in the implementation of function: function page_events_{{appId}}(){}` simply `return 'wait';`    
this will keep on waiting for a function that you need to call later on
  
Look at the example to see how to achieve this. 

**Example**
``` 
  window.page_events_111 = function(data){
    // Act on data.
   return 'wait';
  };  
```
    
// this will keep on waiting (ex call it after dom injection to make sure the function page events has injected the data before proceeding)       
      
`pageEventWaitedCallback(appId);`          
**appId** is a reference for the app that have waited for it                   
              
```
window.SBsdk.SBfunctions.pageEventWaitedCallback(111); // indicates that the wait is done.
```   
      
throbber/loader
--------------------------------------------      
`SBsdk.SBfunctions.loader(action, time_in_ms);`         
1- SBsdk.SBfunctions.loader() // add a throbber to the page      
2- SBsdk.SBfunctions.loader('remove') // removes a throbber from the page         
3- SBsdk.SBfunctions.loader('remove', 100) // removes a throbber from the page after 100 ms                  
4- the throbber is in the following markup: `<div class="ajax-progress ajax-progress-throbber sdk-throbber"></div>` (Given for styling purposes)                 
              
**Note:**                       
> *Consider the following senario*               
> **app1 calls:**         
> `SBsdk.SBfunctions.loader()`        
> **app2 calls:**         
> `SBsdk.SBfunctions.loader()`        
> `SBsdk.SBfunctions.loader('remove')`        
> 
> The Throbber/Loader Will not be removed from the page unless all apps that registered the throbber calls the `remove` action      
> 
> thus to remove the throbber app1 has to call `SBsdk.SBfunctions.loader('remove')` 

           

   Request a rendered product raw HTML:
--------------------------------------------   
Product node ID is used in the request.
Example {base_url}/sb/product_view/23

   SDK Ready Event:
--------------------------------------------
Once the SDK has finished performing all operations the custom `sBsdkReady`event is triggered and can be listened to with the following code
``` javascript
jQuery(document).on("sBsdkReady",function(e) {
   // Your code here
});
```

Sample (Learning purposes)
---------------- 
create a new app      
and install it and paste this sample code in the area specified for js widget        
[sample.txt](/tutorial/sample.txt)