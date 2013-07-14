{{{
    "title"    : "JotForm released API-v1",
    "tags"     : [ "jotform"],
    "category" : "jotform",
    "date"     : "7-14-2013",
    "preview"  : "We have been working hard to release API for two months and we just released the first version 3 weeks ago. It is still young but it was developed with love, we examined the structure over and over again and tried our best to make it to make it as usable as possible. It is still young but looks promising"

}}}

We have been working hard to release API for two months and the first version was released almost 3 weeks ago. It is still young but it was developed with love, we examined the structure over and over again and tried our best to make it as usable as possible. 

JotForm API presents a RESTful HTTP API and all calls must be transported to [api.jotform.com](api.jotform.com). Authorization is satisfied by api-keys, that means each call must include a valid api-key to obtain resource. API-Keys can be given as query parameter
        
        http://api.jotform.com/user?apiKey={YOUR-API-KEY} 
 
or in a HTTP header named `APIKEY`.

You can learn how to get an API key and much more on [getting started](http://api.jotform.com/docs/#gettingstarted) page.

 The best news for me is that JotForm API accepts CORS requests which makes it possible to work with API on browsers with no server side code, only with Javascript! Considering javascript's popularity on these days I think this is a very promising feature.Plus, we released a javascript library to communicate with API even easier.This post is about our brand new javascript library JotFormJS and how to use it, readers with some javascript knowledge will be able to get more.

 JotFormJS
 =========
 [JotFormJS](http://js.jotform.com/JotForm.js) is an open source javascript library to communicate with API from your browser. Library creates a `JF` object on window which includes all methods making CORS Ajax request to corresponding API endpoint, additionally it includes methods to login and obtain API key of users. 

 I want to mention one important aspect of library. All library methods for communication will call success callback function defined as parameter *when you set an authorized API key*. Setting API key can be done with `JF.initialize` method,

    JF.initialize ({
        apiKey      : 'YOUR-API-KEY'
    });

 But, __please note that__ if you set a key directly with this method on your site, then your API key may be obtained by your visitors. You dont want some foreigner to get your API key, because your forms, reports, submissions ... are reachable (even editable if it has full access).

 You may ask that if i cannot initialize library because my API key will be public then what is it for? To fill this gap we implemented login method to authorize users and obtain API key for third party applications. 

 Getting Started
 ---------------

 -  Download latest [JotFormJS](http://js.jotform.com/JotForm.js) and put it to your site
 -  Or copy and paste <script src="http://js.jotform.com/JotForm.js"></script>
 -  Call `JF.initialize` method

        JF.initialize ({
            appName     : 'YOUR-APP-NAME', //default window.location.host
            accessType  : 'ACCESS-TYPE' //default 'readOnly', can be 'readOnly' or 'full'
        });

-   What does that all mean?
    -   __*appName*__    : This is your desired application name.
    -   __*accessType*__ : You should notice that on [MyAccount](http://www.jotform.com/myaccount) page an API key is created with read or full permissions. API keys with *readOnly* permission is allowed for read operations (GET requests only), while an API key with *full* permission is allowed for methods to create, delete or update your records in JotForm. 

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

After successful authorization JF.initialize method will be automatically called with user's API key and *successCallback* function will be called by passing response object. At this point you can call every endpoint defined in JotForm API depending on your applications accessType. You can get user's profile, forms, submissions, reports and much more. Please visit [api page](http://api.jotform.com) for a complete documentation including endpoints, responses, sample codes.

If user does not allow your application *errorCallback* function will be called. Below there is a very basic login function writing response to console
    
    JF.login(function(response) {
        console.log("successful login!");
        console.log(response);
    }, function() {
        console.error("could not authorize user");
    });

Features
---
For every endpoint defined in JotForm API there is a corresponding method in JotFormJS. For a complete list of available endpoints see [api page](http://api.jotform.com). Calling JF methods is straightforward. Every function passes response to callback function when successful response obtained. Below is a simple code to obtain user profile info after successful authorization.
    
    if(JF.getAPIKey() === null) {   //if user is not authorized
        JF.login(   //authorize user
            function success() {  //define success callback
                JF.getUser(function(user) {
                    console.log("user profile : ");
                    console.log(user);
                });
            }, function error() {   
                console.err("not authorized")
            }
        ); 
    }

Indeed most probably you will not need to check user status because an authorization window will automatically pop up when you make request without API key. That means method above can be written like

    JF.getUser( function(user) {
        console.log("user profile : ");
        console.log(user);
    } );

another example to get list of user's forms

    JF.getForms( function(forms) {
        console.log("user forms array : ");
        console.log(forms)
    } );

final example to get submissions according to given query. 

    JF.getSubmissions( function(submissions) {
        console.log("user submissions array : ");
        console.log(submissions);
    }, { limit: 10, offset: 0, orderBy: 'created_at', direction: 'ASC', filter:{'created_at:gte':'2013-07'} } );

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

Last but not least
------------------
We believe this is a huge improvement for JotForm. With API our users have much more control over their data and this API may inspire developers to implement many interesting, useful applications. For 1 month there have been plenty of blowing ideas from team like a blog engine, comment plugin, data backup, submission analysis .... and i am sure there will be lots. 

We are motivated to help anyone working with JotForm API, seriously do not hesitate to ask questions. We created a public repository for [api use cases](https://github.com/jotform/api-use-cases) which includes projects, code snippets, samples. In adddition we are planning to release a bulletin board for latest API news and discussions next week, see you there!
