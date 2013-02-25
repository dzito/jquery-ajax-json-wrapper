jquery-ajax-json-wrapper
========================
This script is a wrapper around jQuery.ajax() designed to handle a custom JSON response from all server endpoints, and is intended to make it more flexible and tolerant of unexpected exceptions.

Here's a common shortcoming of *$.ajax()* this script is intended to work around:
  1. Your script makes an ajax GET request, and expects to receive an HTML fragment to replace an element on the page.
  1. The user's session has timed out since the last request, so the server redirects this request to the login page, which sends an unexpected full page of HTML to the client.
  1. Your script doesn't know what to do with this HTML: what we really want in this case is for it to pass the redirect to the client, taking them to the login page.

### What does this custom JSON response look like? ###

Say we want to POST the contents of a form to the server. Pass any parameters  as you normally would to ```jQuery.ajax()```

**Make the request like so:**

```javascript
var $form = $("#WeHeartQuizzes");
$.jsonRequest({
        type: $form.attr('method'),
        url: $form.attr('action'),
        data: $form.serialize()
});
```

**Response from server if successful:**

```
{
  redirect: null
  status:   "ok"
  data:     {}
  html:     ""
  flash:    { "notice" : "Thank you for taking our quiz!" }
}
```

**Response from server if there was something wrong with the form data:**

```
{
  redirect: null
  status:   "form-error"
  data:     {}
  html:     "<form><!-- a new form to replace the old one --></form>"
  flash:    { "error" : "Please enter your first name and try again." }
}
```

**Response in the case outlined above, when a user is being redirected to the login page:**

```
{
  redirect: "/login"
  status:   "ok"
  data:     {}
  html:     ""
  flash:    { "notice" : "Your session has timed out. Please login again." }
}
```

In the case of a redirect, this script will automatically update the client's ```window.location``` to send them to the appropriate page.

### Success / Error messages ###

When a ```flash``` is present, by default this script will update or append a container on the page with the message:

```html
<div id="flash_messages" class="success">
    Congratulations!
</div>
```

... or ...

```html
<div id="flash_messages" class="error">
    Something went wrong...
</div>
```


