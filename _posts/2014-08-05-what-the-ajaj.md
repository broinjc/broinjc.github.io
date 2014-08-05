---
layout: post
title: "What the AJAJ?"
modified:
categories: 
excerpt:
tags: [django, ajax, json, csrf, tutorial]
image:
  feature:
date: 2014-08-05T11:22:20-07:00
---

#AJAX with Django

Have you ever wanted to update just portion of a web page without having to reload the *entire* page from the server?

![ Load one thing from the server ](checkerboard.png)

Today we will work through a basic tutorial on how to use AJAX with Django. We'll be building a simple talk app with posts and comments.

##What the AJAJ?!

Let's look at the jargon,

 - **AJAX** — Asynchronous JavaScript and XML
 - **XML** — eXtensible Markup Language
 - **JSON** — JavaScript Object Notation

You are more than welcome to read up on `XMLHttpRequest` objects [here](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest) and about how the javascript library, [jQuery](http://api.jquery.com/?s=ajax), can be used to abstract it away. 

**Asynchronous** — Imagine a single lane highway. If one car stops the others are stuck. This is synchronous behavior. Add multiple lanes to this imaginary highway and you get asynchronous behavior. 

**JavaScript** — This is an object oriented programming language. It uses **JSON** to pass data around. JSON looks like this,

    {
        "json": "is so cool",
        "yes this is": true,
        "arrays": [1,2,3],
        "are nice": {
            "so is": ["nested","JSON"]
        }
    }

It takes a little getting used to, but in the end it's pretty simple. Just put a bunch of `{}[]:,""` around all your data. Go [practice](http://jsonlint.com/).

Here is a simple diagram illustrating how this happens in a Django application.

![ How it works in Django ](overview.png)

##Let's build it!

####Set up your django project.

 - `virtualenv env`
 - `source env/bin/activate`
 - `pip install django`
 - `django-admin.py startproject realpython`
 - `cd realpython`
 - `python manage.py startapp talk`

##Not so fast sparky!

If you can pass the following test then you are allowed to build the talk app. 

Let's add two numbers.

##The Silly Way

 - url
 - view
 - template

An interesting way to add two numbers could work like this. Setup an url `url(r'^add/<num1>/<num2>$', 'add'),` then define a `view`:

    def add(req, num1, num2):
               tmpl_vars = {'sum':num1 + num2}
            return render(req, 'talk/index.html', tmpl_vars)

Then create the template to show the results:
    
    `{{ sum }}`


##The Awesomer Way

 - template
 - view
 - url

Let's try to add our numbers *without* page refresh. Going backwards first modify your template. Add a form

    <form id="addition_form">

...and a Cross Site Scripting Token. We will talk about this in a bit.

        `{% csrf_token %}`

All your appropriate inputs...

        <input type="number" id="num1" required/>
        <input type="number" id="num2" required/>
        <input type='submit' id='add' class="tiny button" />

And then close the freaking thing...

    </form>

Next let's add a listener to the form so when it get's submitted

    $('#addition_form').on('submit', function(e){

We don't want it to actually go to a page. So we preven the event default.

      e.preventDefault();

Next up is our fancy AJAX call...

      $.ajax({

Where are we going?

        url : "add/",

How will we get there?

        type : "POST",

Who are we taking with us?

        data : { num1 : $('#num1').val(), num2 : $('#num2').val() }, 

What should we do when we get back?

        success : function(json) {
          // Have a party
        },

What if we get a flat tire or something?

        error : function(xhr,errmsg,err) {
          // Oh crap!
          console.log(xhr.status + ": " + xhr.responseText);

Then write this lovely computer grammar...

        }
      });
    };

And your done. You just married a form to some javascript. And the javascript is cheating on the form with the server. Or something of the sorts...

###Modify your view to return json

    from django.http import HttpResponse, HttpResponseRedirect
    import json # muy importante
    
    def add(request):
        response_data = {}
        if request.method == 'POST':
            try:
                result = float(request.POST.get('num1')) + float(request.POST.get('num2'))
                response_data['result'] = result
            except ValueError as (errno,strerror):
                response_data['result'] = 'Was that even a number?'
                response_data['error'] = '{0} - {1}'.format(errno, strerror)

            return HttpResponse(
                    json.dumps(response_data),
                    content_type="application/json"
                )
        else:
            # Hit the road, Jack!
            return HttpResponseRedirect('/')

And fix your url. You don't need to pass in those numbers any more. It will all happen magically behind the scenes.

    url(r'^add/$', 'add'),

##We are not quite done. Cookies are delicious!

Allow me to digress and mention Cross-Site Request Forgeries. CSRF happens when an attaker steals a victims cookies using another website a victim might have open in their browser. Django defends against this by using CSRF tokens in forms. Since we are handling data asynchronously we will need to handle CSRF issues with javascript. You can read more about this on the [Coding Horror](http://blog.codinghorror.com/preventing-csrf-and-xsrf-attacks/) blog. They've got a great article.

All that being said, you need some javascript that will watch your back. Check out this [gist](https://gist.github.com/broinjc/db6e0ac214c355c887e5)

Put the code at the end of your other javascript and it will take care of you.

##Happy coding!

Once you pass the test you can take Part 2: The AJAX/Django talk app.
