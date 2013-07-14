{{{
    "title"    : "JotForm released API-v1",
    "tags"     : [ "jotform"],
    "category" : "jotform",
    "date"     : "7-14-2013",
    "preview"  : "We have been working hard to release API for two months and we just released the first version 3 weeks ago. It is still young but it was developed with love, we examined the structure over and over again and tried our best to make it to make it as usable as possible. It is still young but looks promising"

}}}

We have been working hard to release API for two months and we just released the first version 3 weeks ago. It is still young but it was developed with love, we examined the structure over and over again and tried our best to make it as usable as possible. 

JotForm API presents a RESTful HTTP API and all calls must be transported to [api.jotform.com](api.jotform.com). Authorization is satisfied by api-keys, that means each call must include a valid api-key to obtain resource. API-Keys can be given as query parameter
        
        http://api.jotform.com/user?apiKey={YOUR-API-KEY} 
 
or in a HTTP header named `APIKEY`.

You can learn how to get an API key and much more on [getting started](http://api.jotform.com/docs/#gettingstarted) page.

 All these are great up to now but the best news is that JotForm API accepts CORS requests which makes it possible to work with API on browsers with no server side code, only with Javascript! Considering javascript's popularity on these days I think this is a great feature. 
 We also released a javascript library to make communicating with API even easier.This post is about our brand new javascript library JotFormJS and how to use it.

 JotformJS
 =========
 JotFormJS is a javascript library to communicate with API from your browser. It has no dependencies at all and library includes a method making CORS Ajax request for every corresponding API endpoint, additionally it includes methods to login and obtain API key of users which makes it charming to develop third party applications for JotForm users.

 Getting Started
 ---------------

 -  Download latest [JotFormJS](http://js.jotform.com/JotForm.js)
 -  Call JF.initialize method


    JF. `initialize` ({
        apiKey      : 'YOUR-API-KEY',
        appName     : 'YOUR-APP-NAME', //default window.location.host
        accessType  : 'ACCESS-TYPE' //default 'readOnly', can be 'readOnly' or 'full'
    });

-   Okey but what does that all mean?
    -   __*apiKey*__     : All requests to API will be transported with this key. Simply all request will include a query paramater named apiKey. e.g. *api.jotform.com/user/forms?apiKey=__apiKey__*. Third party applications do not need to fill this parameter.
    -   __*appName*__    : This is your desired application name. It is useful for third party applications.
    -   __*accessType*__ : You should notice that on [MyAccount](http://www.jotform.com/myaccount) page an API key is created with read or full permissions. API keys with *readOnly* permission is allowed for read operations (GET requests only), while an API key with *full* permission is allowed for methods to create, delete or update your records in JotForm. 

__Important note about initialization__

Please note that if you call JF.initialize method with your API key and implement applications for your site, your API key __will be reachable__ to anyone on your website. You dont want some foreigner to get your API key, because your forms, reports, submissions ... are reachable (even editable if it has full access). This is why this library perfectly fits for third party applications. If you want to communicate with JotForm API for your own data we strongly recommend to make it on your server. There are client libraries implemented for various languages. Go to [api page](http://api.jotform.com) and click to dropdown on top to see available libraries.

You may ask that if i cannot initialize library because my API key will be public then what is it for? To fill this gap we implemented login method to authorize users and obtain API key.

Login with JotForm
------------------
JotFormJS presents a login method to be used by third party applications. With JF.login method users can be logged in through JotForm and asked for authorization.

    JF.login(
        successCallback, //function
        errorCallback    //function
    );

After you call JF.login method called an iframe will be opened to login user through JotForm.

![jotform login](/images/Untitled.png)

After login user will be redirected to authorization page. 

![authorize](/images/auth.png)

After successful authorization JF.initialize method will be automatically called with user's API key and successCallback function will be called by passing response object. At this point you can call every endpoint defined in JotForm API depending on your applications accessType. You can get user's profile, forms, submissions, reports and much more. Please visit [api page](http://api.jotform.com) for a complete documentation including endpoints, responses, sample codes.

If user does not allow your application errorCallback function will be called. Below there is a very basic login function writing response to console
    
    JF.login(function(response) {
        console.log("successful login!");
        console.log(response);
    }, function() {
        console.error("could not authorize user");
    });

Features
---
For every endpoint defined in JotForm API there is a corresponding method in JotFormJS. For a complete list of available endpoints see [api page](http://api.jotform.com). Calling JF methods is straightforward. Every function passes response to callback function given as first parameter. Below is a simple code to obtain user profile info after successful authorization.
    
    JF.getUser(function(user) {
        console.log("user profile : ");
        console.log(user);
    });
    
another example to get list of user's forms

    JF.getForms(function(forms) {
        console.log("user forms array : ");
        console.log(forms)
    });

final example to get submissions according to given query. 

    JF.getSubmissions(function(submissions) {
        console.log("user submissions array : ");
        console.log(submissions);
    }, {limit: 10, offset: 0, orderBy: 'created_at', direction: 'ASC', filter:{'created_at:gte':'2013-07'}});

For submission methods JotForm API accepts a query object as parameter. Sample query can be:
    
    {
        offset: 0,
        limit: 20,
        orderBy: 'created_at' //default undefined, 
        direction: 'ASC' //order direction, default DESC,
        filter: {
            "cretaed_at:gte": "2013" //you should wonder what gte is. This endpoint parse  filter parameter and compose response accordingly. Keywords:
                'gte' => greater than or equal
                'lte' => less than or equal
                'gt' => grater than
                'lt' => lower than
                'ne' => not equal
                'e' =>equal 
        }
    }

Foreach examples above JF.login method will be automatically called if a valid API key does not is not defined.


Last but not least
------------------
We believe this is a huge improvement for JotForm. With API our users have much more control over their data, but more importantly this API may inspire developers to implement stunning applications for JotForm users. There are plenty of blowing ideas from our team like a blog engine, comment plugin like Disqus, data backup, submission analysis .... and i am sure there will be lots. We also created a public repository for [api use cases](https://github.com/jotform/api-use-cases).
