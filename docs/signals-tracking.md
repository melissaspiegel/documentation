[MWM](README.md) > Signals Tracking

# Signals Tracking

We use the Sample Signals library (formerly Turnstile) to track user interactions and events on MWM sites and modules. This allows us to better understand how users are engaging with our products.

## Tracked attributes

The signals API expects a few standard attributes to be passed with each tracked event:

1. **section** (optional) - The section or "landmark" of the site, such as "header", "main", "footer" etc. This helps group events.
2. **component** - The specific component or module that is triggering the event. Typically this is the module itself, such as "mwmFeatured" or "mwmTextMedia".
3. **element** - The specific element within the component that triggered the event, such as a button or a card link. This provides more granular context.
4. **action** - The type of action that occurred. Most often, this will be "click" and is assigned via data-attributes like `data-action-link` or `data-action-click`. See details on **actions** below.

## Implementation

To implement tracking, we use data attributes, which the Signals API is able to parse and use as needed. The following data attributes can be used accordingly:

```
data-tn-section="mySection"
data-tn-component="myComponent"
data-tn-element="myElement"
data-tn-action-*
data-tn-link
```

### Section

The **section** is typically part of the global page template, so it can be set statically.

```php
<aside class="my-sidebar" data-tn-section="sidebar">
  <!-- some code... -->
</aside>
```

### Component

The **component** attribute should be reserved for the top level of modules. The component name is automatically set by the Module Abstract. To set the component, use the following code snippet as an example:

```php
<div class="mwm-my-module" data-tn-component="<?php echo esc_attr( $tn_component ); ?>">
   <!-- some code -->
<div>
```

Here you can see that `$tn_component` is a variable that is automatically set by the Module Abstract, so we can simply use it within our view without worrying about setting it.

### Element

The **element** attribute will be most dynamic, since it is most often a part of our customized modules (and partials). There are often helper functions within our partials which can help set the element data attribute appropriately. The following snippet is an example of using one such function:

```php
$cta->tn_element('cardCta');
```

#### Numbering repeated elements

Many times, things like CTAs or clickable cards are repeated within one component. For example, a Card Grid module may have multiple clickable CTAs within it. When this occurs, we need to make sure that each **element** within the **component** is unique, by adding a number (index) to each. By convention, we use the underscore `_` to separate the element name from the index.

Example:

```php
foreach ( $ctas as $index => $cta ) {
  $cta->tn_element( 'cardCta_' . $index );
  $cta->render();
}
```

This example will output an element of `cardCta_0`, `cardCta_1`, etc... for each repeated CTA element.

### Actions

Choose the appropriate action depending on the use case. The action attributes do not need values assigned to them, as they are simply binary.

1. `data-tn-link` - this attribute will assign the "click" action. Use this attribute for any links that result in navigation to a URL. Using the "link" attribute will automatically populate `destinationPage` and `destinationUrl` which are sometimes useful when analyzing clicks on links.
2. `data-tn-action-click` - this attribute will also assign the "click" action. Use this attribute for any interactive elements that are clickable but do not result in navigation. This tells Signals it should track clicks on the element.
3. `Other actions` - you can use the `data-tn-action-*` attribute to track other actions, such as focus or blur events as needed.
  - Examples: `data-tn-action-focus`, `data-tn-action-blur`, `data-tn-action-change`

**Note:** Since `data-tn-link` and `data-tn-action-click` both assign the "click" action, choose the one that best semantically describes the element's purpose. If adding `data-tn-link`, there is no need to also add `data-tn-action-click` since there will be no added benefit to the extra attribute.

### References

- [Signals Readme](https://code.corp.sample.com/frontend/signals/-/blob/main/README.md)
- [Sample Signals wiki](https://wiki.sample.com/pages/viewpage.action?pageId=356714643)
- [Sample Turnstile deprecation | TURN-638](https://bugs.sample.com/browse/TURN-638)