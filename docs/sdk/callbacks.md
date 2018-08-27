#### Dom Injection
                  
To insert Dom implement:               
`function inject_custom_data_{{appId}}(){}`         
**parameters** : no parameters provided               
**Return** (obj)       

-**Return** should be of the following structure:               
*case 1)* I can generate my dom and returned it directly.      
```
ret = {
  'data' : [
    {'id': id1, 'dom': dom1}, // descriptive unique id should be given for the dom you want to inject
    {'id': id2, 'dom': dom2},
    {'id': id3, 'dom': dom3}
  ],
  // if you have injected fields (custom text fields etc etc) that you need to validate upon submission of the page
  // give the html id of the form with the corresponding validation function name. 
  'submitHandler': {'formId1': 'submitHandler1', 'formId2': 'submitHandler2'}
};
```    
*case 2)* I dont have the dom yet, still waiting for it; The DOM is being generated from another (scope) js code **asyncrounously**.    
```
ret = { 'wait' : {{appId}} }
```


**FOR THEME APPS (better performance - speed)**          
use this function instead `function theme_inject_custom_data_{{appId}}(){}`         
**parameters** : no parameters provided               
**Return** (obj)       

-**Return** should be of the following structure:               
*case 1)* I can generate my dom and returned it directly.      
```
ret = {
  'data' : [
    {'id': id1, 'dom': dom1}, // descriptive unique id should be given for the dom you want to inject
    {'id': id2, 'dom': dom2},
    {'id': id3, 'dom': dom3}
  ]
};
```    
*case 2)* I dont have the dom yet, still waiting for it; The DOM is being generated from another (scope) js code **asyncrounously**.    
```
ret = { 'wait' : {{appId}} }
```

  ~~~~~  ~~~~~~  ~~~~~  ~~~~~~  ~~~~~  ~~~~~~  ~~~~~  ~~~~~~

**For case 1:** Inject your dom and define your submit handler:  **Example:**       
```
  
window.submissionCallbackExample = function (){
 // validate your custom data before submission 
 // If your validation suceeds or fails
 // YOUR SUBMIT HANDLER SHOULD RETURN
 // TRUE OR FALSE respectively

  return true; // validation success
};

window.inject_custom_data_456 = function(){

  var dom = window.SBsdk.SBfunctions.generateDom('textfield', {
    class: 'textfield-dummy-class',
    wrapperClass: 'textfield-wrapper-dummy-class',
    Title: 'Title',
    required: 1,
    placeholder: 'placeholder',
    id: 'i-am-an-id',
    name: 'hello',
    size: 60,
    maxlength: 255,
    value: 'default value'
  });

  id = 'validate-test';

  ret = {
    'data' : [{'id': id, 'dom': dom}], 
     // dom will be appended after body by default unless positioned
    'submitHandler': {'form-id': 'submissionCallbackExample'}
     // (optional) you can skip this if you have no form
  };
  
  return ret;
};
```

**For case 2:**        
- Return wait *(to wait for the dom to arrive)*         
- call `window.SBsdk.SBfunctions.injectCustomDataCallback(appId, returnCallback);` once you have the dom needed; with the `appId` and the `returncallback`.     
-the return callback should be of the following structure as well:   
```
ret = {
  'data' : [
    {'id': id1, 'dom': dom1}, // descriptive unique id should be given for the dom you want to inject
    {'id': id2, 'dom': dom2},
    {'id': id3, 'dom': dom3}
  ],
  // if you have injected fields (custom text fields etc etc) that you need to validate upon submission of the page
  // give the html id of the form with the corresponding validation function name. 
  'submitHandler': {'formId1': 'submitHandler1', 'formId2': 'submitHandler2'}
};
```

**Example for case 2:**       
     
```
// if for Some reason you don't need to return the data at this level
// and you want to provide the data from another scope asynchronously:

//Step1:
  
  window.submissionCallbackExample = function(){
    // validate your custom data before submission 
    // If your validation suceeds or fails
    // YOUR SUBMIT HANDLER SHOULD RETURN
    // TRUE OR FALSE respectively
    return true;
  }

  window.inject_custom_data_123 = function(){
    // you return wait with the {{appId}}
    // to tell the hook to wait for the dom of this function
    return { 'wait' : '123' };
  }

// Now to populate data of appId 123 in some other scope or function:
// All that you have to do is call: window.SBsdk.SBfunctions.injectCustomDataCallback
// Ex.

//Step2:
// prepare the data in this structure:
  jQuery.ajax(
    url: url,
    type: GET,
    success: function(data){
      var returnOf123Callback = {
       'data' : [{'id': '123Id-1', 'dom': '<h1>'+data+'</h1>'}, {'id': '123Id-2', 'dom': '<h1>dom2</h1>'}],
        'submitHandler': 'submissionCallbackExample', // (optional)
      };
      // call this function giving it the appId and the Dom data
      window.SBsdk.SBfunctions.injectCustomDataCallback(123, returnOf123Callback);
    }
  );
```
            
---------------------
          
#### Form Submission Callback

If what is needed is to validate a form **without** the use of inject_custom_data_{{appId}}:                     
1-     
call: `injectCustomFormCallbacks(form_callbacks);`        
where the **form_callbacks** is of the structure:      
`form_callbacks = {'formId1': 'submitHandler1', 'formId2': 'submitHandler2'}`     

2-        
define your callback functions  `function submitHandler1()` & `function submitHandler2()`   
                
This callback function should `return true` or `return false`         
OR should `return 'wait'` in case the result is returned asynchronously like ajax result for example..       
3-          
after getting the needed result           
call `errorsWaitedToValidateCallback(appId, result);` where the `result should be either true or false`      
       
**Example:** 
```
//Example of an ajax form
function callback_hello(){
  alert('HELLOOOO');
}
// page ajax events
window.page_events_4705647385 = function(data){
   if($('form[action="/ajax_register/login/ajax"]').length){
       id = $('form[action="/ajax_register/login/ajax"]').attr('id');
       obj = {};
       obj[id] = 'callback_hello';
       window.SBsdk.SBfunctions.injectCustomFormCallbacks(obj);
   }
}
```  

--------------------------
#### Dom Position
         
To position DOM implement:                
`function inject_position_data_{{appId}}(appId, appId_dom_data){}`           
**parameters** :                   
-**appId** will contain the app id                       
-**appId_dom_data** will contain all the Dom data for this specific app (coming from dom inject) to be able to position them                          
                        
**Return** `an array of the elements that have been positioned`            
              
**Examples**:                       

``` 
// Step1 inject the data.
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

// Step2 To position the previously populated dom .. we only need to know the id of each dom

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
      

**FOR THEME APPS (better performance - speed)**    
`function theme_inject_position_data_{{app_id}}(appId, appId_dom_data){}`      
// same as `function inject_position_data_{{appId}}(appId, appId_dom_data){}`

------------------


#### Inject CSS
              
To inject custom css implement:                
`function inject_css_{{appId}}(){}`           

**parameters** : no parameters provided          
**Return**:  `you can return inline or external css`   
  
Look at the example to see how to achieve this. 

**Examples**: 
``` 
  window.inject_css_123 = function(){
    return [
      {
        css : 'body{ background-color: darkgray; }',
        type : 'inline'
        // adds a style tag to the head with the css written
      },
      {
        css : '{{path-to-your-css}}/test789.css',
        type : 'external'
        // adds a link tag stylesheet with href *test789.css* to the head
      }, ...
    ]
  };

```   
      
               
----------------------
               
#### Page Ajax Events
             
Respond to page ajax events.    

**A)** To act upon page ajax events implement:            
`function page_events_{{appId}}(data){}`    

**Return** `No return` // if not asyncronous
**Parameter:**   
- **data** an array containing information about the ajax that has occured       

**data** you might get should look something like this:
```
'id' => '',
'type' => 'user',
'action' => 'signup',
'query_strings' => $query_strings_text,
'wrapper_id' => 'modalContent',
'wrapper_class' =>
``` 
  
Look at the example to see how to achieve this. 

**Examples**: 
``` 
  window.page_events_111 = function(data){
    // Act on data.
  };

```   
            
**B)** To wait for asyncronous calls:     
in the implementation of function: `function page_events_{{appId}}(){}` simply `return 'wait';` 

this will keep on waiting for function `pageEventWaitedCallback(appId);` that you need to call later on to stop the waiting
  
**Example**
``` 
  var know_result = '';
  window.page_events_111 = function(data){
    jQuery.ajax({
      url: url,
      type: GET,
      success: function(e){
        know_result = true;
        // end the waiting process
        window.SBsdk.SBfunctions.pageEventWaitedCallback(111);
      },
      error: function(e){
        know_result = false;
        // end the waiting process
        window.SBsdk.SBfunctions.pageEventWaitedCallback(111);
      }
    });
    // In this example
    // after a page event happened, wait for the response of ajax to set the variable
    return 'wait';
  };
```
         
**C)** Events executed after ajax requests:    
                    
in your implementation of the `function page_events_{{app_id}}(data){}`               
             
*in the parameter* `data`                    
you can get information after an ajax about the following events:                                  
                          
-After changing the product varaition within add-to-cart form (For example at product view page or product drawer)           
                 
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
    
-After updating cart form, for example changing number of purchased products would trigger this event. (Cart form usually found in a block)
    
```
 {
    'id' => '',
    'type' => 'cart',
    'action' => 'update',
  }
```
      
-After pressing quick-edit in orders listing page

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
    
-When any ajax request has been fired within checkout page 

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

-When user login/forgot-password/signup forms has been opened in a popup'

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
    
-After creating or updating an address profile'

```
  {
    'id' => $profile->uuid,
    'type' => 'shipping-customer-address || billing-customer-address',
    'action' => 'new || updated',
    'query_strings' => $query_strings_text
  }
```

-When opening add/edit profile forms or when deleting an address profile

```
 {
    'id' => isset($profile->profile_id) ? $profile->uuid : '',
    'type' =>  'shipping-customer-address || billing-customer-address',
    'action' => 'open edit || open add || delete',
    'query_strings' => $query_strings_text
  }
```
      
     
-Panels page events    

a) Event triggered after the ajax of Design my page button.
```
{       
  'id' => 'panels_ipe_edit_control_form',
  'type' =>  'panels',
  'action' => 'start-design'
}

```
b) Event triggered after saving the designed page
```
{
  'id' => 'panels_ipe_edit_control_form',
  'type' =>  'panels',
  'action' => 'save-design',
}

```
c) Event triggered after updating or adding a box of a panel pane
```
{
  'id' => 'panels_ipe_edit_control_form',
  'type' =>  'panels',
  'action' => 'save-design',
}

```
d) Event triggered after saving the style of a panel pane          
```
{
  'id' => '{{form_id}}',
  'type' =>  'panels-pane',
  'action' => 'styles-save-pane',
}
```

----------------------
               


          
#### Google Map Marker Callback

- To alter the icon of the google marker, use `theme_app_map_marker_{{appId}}`.                     
- This callback function should be defined as soon as your js file loads. Thus you have to define it globally.
- You can always access the google map data. [click here for more information](/sdk/globals/#website-data)


```
// in your js file 
// as you can notice in the definition of this function.. it is not wrapped inside any function (it is defined globally).
window.theme_app_map_marker_{{appId}} = function(){
  options = {
    image_url : '', // absolute url (REQUIRED)
    scale: { 
      width: 50, // default value if was not set
      height: 50 // default value if was not set
    }, // (Optional)
    origin:{
      width: 0, // default value if was not set
      height: 0 // default value if was not set
    }, // (Optional)
    anchor:{
      width: 0, // default value if was not set
      height: 0 // default value if was not set
    }, // (Optional)
  }
  return options;
}

document.addEventListener("DOMContentLoaded", function(event) {
 ... 
});
```

       
**Example:** 
```
// suppose the app id of my theme is 123123123, then

window.theme_app_map_marke_123123123 = function(){
  options = {
    image_url : 'https://openclipart.org/image/2400px/svg_to_png/192591/map-marker.png',
  }
  return options; 
};
// then you will be able to see the image marker that you have set with image of dimension 50x50 
```  

