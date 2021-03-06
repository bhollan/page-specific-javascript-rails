# Page Specific Javascript Rails

## Objectives

1. How to Write Page-Specific JS
2. Loading Page-Specific JS in the Manifest
3. Loading Page-Specific JS through javascript_include_tag
4. Creating a `content_for` page specific JS and writing JS in partials.

## Outline

As we create our application, using a single file for all of our JS can cause a lot of headaches. As the file gets bigger and bigger it becomes harder to find anything. We want to organize our JS into different files based on their functionality. One way to do this is to have page specific JS files.

## Page Specific JS in a manifest

By default, when you use the [Rails Generators](http://guides.rubyonrails.org/generators.html) to create a new resource, Rails will create a page specific JS and CSS file. From here we want to put any JS that is specific to the web pages associated with the resource. For example, say we have a Blog resource. We add JS that loads the comments for a blog after the page loads. This bit of JS should be put in the file `app/assets/javascripts/blogs.js.coffee` ([CoffeeScript](http://coffeescript.org) is the default way to create JS for Rails). From here we just need to make sure our JS file is included in the JS manifest.

**File: `app/assets/javascripts/application.js`**

```javascript
//= require blogs
```
## Controller Specific JS

When the browser loads our JavaScript, it parses the entire file and runs the JS. With a big application this can be a lot of JS. Different pages might start to have functionality we don't want to share accross the applicaiton. An option to allow for a Page specific JS file be loaded only with the pages we want is to use the name of the controller.

```erb
<%= javascript_include_tag params[:controller] %>
```

Instead of adding blogs.js to the manifest file, we could instead load the file based off of the controller's name.  We can place this in the head or below the body of our layout it will load the JS file that matches the name of the controller.

A request made to `blogs#index` would result in params:
`{controller: 'blogs', action: 'index'}` and the `javascript_include_tag` will now load the blogs.js file.  If we visit a page from a different controller, the `javascript_include_tag` will include the JS specific to that controller.

The downside of this is we'd no longer be getting the benefits of asset concatenation or caching.  The browser will have to make a separate request for this file in addition to the request for the main concatenated `application.js` file.  The benefit of this strategy could be that we're less likely to invalidate the cache for our entire JS file if it is in pieces.

## Using content_for

The final way to include JS specific to a page is to use a `content_for :js` block in your layout.  We can either put this in the head or below the body of the layout.

**File: application.html.erb**

```erb
<head>
  <meta>
  <%= javascript_include_tag 'application' %>
  <%= yield :js %>
```

Then in your view

**File: app/views/blogs/show.html.erb**

```erb
<% content_for :js do %>
  <script>
    alert('Some Page specific JS');
  </script>
<% end %>
```

Anything placed in our script tag will run only on our show page.

## Class Based Targeting

While both of those solutions work, some people find that they aren't the most elegant solutions to the problem.  One way you can continue to keep things simple with the asset pipeline is to continue to have it concatenate all of your javascript together, but wrap all your pages in a div with a specific class for that page.

```erb
<%# app/views/layouts/application.html.erb %>

<body class="<%= controller_name %> <%= action_name %>">
  <%= yield %>
</body>
```

Assuming your contact page action was inside a controller named PagesController, the rendered result would be the following:

```erb
<body class="pages contact">
  ...
</body>
```

Now you could write some javascript like this
```javascript
$(".pages.contact").click(function() {
  console.log("only runs on the contact page!")
})
```

<p data-visibility='hidden'>View <a href='https://learn.co/lessons/page-specific-javascript-rails' title='Page Specific Javascript Rails'>Page Specific Javascript Rails</a> on Learn.co and start learning to code for free.</p>
