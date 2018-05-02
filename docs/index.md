# Introduction
## Shopbuilder

>  We offer free online stores; an easy-to-use ecommerce website builder that delivers the site in minutes.

## SDK

> Shopbuilder's sdk is typically a set of js/jQuery based functions that allows manipulation of the Shopbuilder websites.
    
**Sdk Overview:**     
1- Inject & Position Custom Content     
2- Inject CSS      
3- Inject Position & validate your **Custom Fields**     
4- Create & View your **Custom Pages**        
5- Creates updates and deletes any type of **Menu Links** in the site           
6- **Generates HTML** that are pre-styled in the Back Office (adminstration section)        
**And so much more helper functions and integrations with libraries if you wish to use**     
                     
--------------------       

# Let's Get started

**Prerequisites:**    
1- Have an approved <a href="https://developers.shopbuilder.me/user/register" target="_blank">account</a> on the developers website   
2- Get <a href="https://www.shopbuilder.me" target="_blank">a free website</a> to serve as a development store *(make sure to use the same email address for both the developers' website account and the free store merchant's account)*      
                             
*  Then head into the developers website and <a href="https://developers.shopbuilder.me/admin/apps" target="_blank">create a new app</a>, pick if you are creating a theme or a feature. A theme is used to provide a new design to a store and essentially manipulates the dom/css/js, a feature can also amend the dom but usually also need to make use of the API to access data of the store.        
              
*  When creating an app that have an impact on the front end of the store you'll do that buy injecting Javascript/CSS files from your app directly into the store. To achieve that, every app has a widget section that allows you to inject some Javascript.          
             
*  Whenever the app is installed on a store, this piece of Javascript is injected into the store as is. Yet this injection is not dynamic so if you need to update this piece of code your users will have to re-install your app (pending we handle app versions). For now to overcome this limitation and to also have a better code structure we advise to use this area of code to inject a loader script.             
                
*  A loader script will take care of dynamically injecting an external script that would hold your app's logic and thus allow you to update your app on the go without the need to edit the app on the developers website.        

**here is an example of a loader script:**

```
(function() {
var sb_website_id = window.SBsdk.SBdata.SBwebsite_data.website_id;
var user_id = window.SBsdk.SBdata.SBwebsite_data.user_id;
var user_role = window.SBsdk.SBdata.SBwebsite_data.user_role;
var page_id = window.SBsdk.SBdata.page.id;
var page_type = window.SBsdk.SBdata.page.type;
var action = window.SBsdk.SBdata.page.action;
var query_strings = SBsdk.SBdata.page.query_strings
var script = document.createElement('script');
script.async = true;

var secure = window.location.protocol === 'https:';
script.src = (secure ? 'https' : 'http') +"://linktoyourscript.yourdomain.com/widget.js?sb_website_id="+sb_website_id+"&user_id="+user_id+"&user_role="+JSON.stringify(user_role)+"&page_type="+page_type+"&action="+action+"&page_id="+page_id+"&query_strings="+JSON.stringify(query_strings);

var entry = document.getElementsByTagName('script')[0];
entry.parentNode.insertBefore(script,entry);

window.inject_custom_data_{YourAppID} = function(){
	return { 'wait' : '{YourAppID}' };
}

window.inject_css_{YourAppID}  = function(){
	return [
		{
			css : 'https://linktoyourcss.yourdomain.com/app.css',
			type : 'external'
		}
	]
};
})();
```

Feel free to use this script and replace the values according to your app's information (URIs of your codes and App ID).

As you can see the widget script can make use of SDK variables, functions and callbacks. Similarly to how any loaded JS is also able to make use of these.

Also note that you are responsible to host your codebase, make sure you host in a place that can provide SSL encryption (to avoid mixed content errors as all our live stores are delivered over https), also make sure the TTFB is low to provide the best user experience to merchants, some mechanism are in place in the SDK to timeout apps that would have a very high load time.

Finally to test the app on your **development store** simply head to **admin/settings/app-store** to see your app and **install it**. 

Once this is properly done you are ready to start coding your app.