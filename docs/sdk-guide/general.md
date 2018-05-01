## Get ready, set, code !    
     
**"Before we start make sure you have completed the [setup](/#lets-get-started)"**       
        
All the sdk *functions* and *global* are found in **window.SBsdk**           
        
**Let's test that together:**        
Go to your website, open your console and type       
`console.log(window.SBsdk);`               
            
the **functions** are found in  `console.log(window.SBsdk.SBfunctions)`             
the **Globals** are found in  `console.log(window.SBsdk.SBdata)`              
        
--------------------


#### Example        
      
In your websites home page     
`console.log(window.SBsdk.SBdata.page); // page helps get the page's information`         
you will recieve an object containing information of the page you are in:         
```
{
  type: "homepage", // type of the page (we are in home page)
  action: "view",   // we are viewing the home page
  query_strings: "",// no query strings
}
```
