[MWM](README.md) > Gated Content Article

# Gated Content Article

MWM allows you to gate content on an article.  You can gate content copy text, and you can gate asset links such as a PDF.

## How does it work?

Gated content works by adding a 'Gated Content Breakpoint' block within the article.  All text below the breakpoint will
be hidden until the user has signed up.  In addition to gating content text, you can also gate assets such as a 
PDF.  By selecting a PDF or document on the Form Confirmation CTA, the asset will open in a new window once a user has 
filled out the required form.

Essentially all gated content is tied to the form on the page.  This can work on any Marketo Form ID.  The page template
looks for a JS Event `form_submit_success` that fires once a Marketo form has been successfully submitted.  The article 
JS will then un-gate the page content and/or download any assets tied to the form Confirmation CTA.

## How do I Gate Content on my Article?

To Gate content you need two things:
* `Gate Content` set to 'yes' on the General Tab.
* A `Form Module` added to the Bottom Module Settings Tab.

For there to be anything to Gate, you'll need to add one or both of the following:
* A `Gated Content Breakpoint` within the article content
* A Document Link for the Confirmation CTA in the `Form Module` on the Bottom Module Settings tab.

## Dispatching and Listing for the Event

During a successful form submit, a JS event will be dispatched with the name `form_submit_success`.  This is coming from
the JS File: `Includes/Partials/MwmMarketoForm/MarketoForm.js`.  

>Should another form frameworks be added, we should also dispatch the same event so that it can be listened by the 
> Article Template, or any other partial that may need it.

Currently, the Event Listener is only on the Article Page in the file `js/mwmGatedPostContent.js`.  In this file we 
listen for the event, and then switch a flag `isSignedUp` to true, and follow the flow to un-hide content or
open a download link.

### Related Files

```markdown
js/
    mwmGatedPostContent.js           # The javascript file responsible for hiding and showing Article Gated Content
Includes/Partials/MwmMarketoForm/
    MarketoForm.js                   # The javascript file responsible for triggering the Event
```

## Gated Cookies

At this time we have removed the Gated Cookies.  This means that on every page refresh, you will be asked to fill in 
the form to see the gated content.  
