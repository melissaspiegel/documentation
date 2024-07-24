[MWM](README.md) > Gated Content Partial

# Gated Content Partial

The MwmGatedContent partial is a convenient way to gate content on any module or partial.

## How does it work?

The MwmGatedContent partial view adds special markup around the content which works with JavaScript to show or hide content when gated. The JavaScript is set up to look for specific criteria to determine whether the gate should be lifted. Custom fields are set up to allow the editor to choose what criteria are best for gating their content.

There are two states that this partial can output to the page:

1. Show content when gated
2. Hide content when gated

If an object is shown when gated, it will be hidden when ungated, and vice-versa.

Further GatedContent integration can be done to add an overlay, or popup form CTAs. These customizations should be done on the module level, as those are specific to module behavior.

## How do I use MwmGatedContent on my module?

This partial works very similarly to other MWM partials, you'll want to do some of the standard procedures:

1. Import the partial with a `use` statement at the top of your module or partial controller:
    - `use Indeed\MarketingWebModules\Partials\MwmGatedContent\MwmGatedContent;`
2. Add partial fields to your module or partial ACF:
    - `MwmGatedContent::make()`
    - This comes with two fields:
        1. A TrueFalse field, which will toggle the gated content on or off
        2. A Select field, which allows the user to choose what condition gates the content (e.g. cookie, gnav sign-in)
3. Build the partial, passing in data from the gated content fields
    - `$data[ 'gated_content' ] = $this->add_child( MwmGatedContent::class, $gated_content_data );`
4. Render the partial
    - In the view file, don't forget to render the partial.
    - `$gated_content->render();`

The unique part of this partial is its ability to accept inner content. Similar to slot-fills in React, the `content()` function accepts a callback. Anything echoed inside that callback will be captured using the output buffer, and echoed within the Gated Content container. This allows you to easily add HTML markup within the Gated Content container without having to require new files or manually pass large strings as arguments.

Example:
```php
$name = "World";

$gated_content
    ->content(
        function() use ( $name ) {
            ?>
            <div>
                Hello <?php wp_kses_post( $name ); ?>
            </div>
            <?php
        }
    )
    ->render();
```

In this example, the div with "Hello World" will be printed within the Gated Content container, so that it works pefectly with our JavaScript. Notice that `use` is added to the function in order to use variables defined outside the callback. This can include variables that were passed to the view from the module or partial controller.

By default, gated content will be shown when gated, and hidden when ungated. To hide content when gated, you can use the `->hide()` modifier function.

Example:
```php
$name = "World";

$gated_content
    ->content(
        function() use ( $name ) {
            ?>
            <div>
                Hello <?php wp_kses_post( $name ); ?>
            </div>
            <?php
        }
    )
    ->hide()
    ->render();
```

## Dispatching custom event

If you want the gated content to update on the fly (for example, if you've given the user a cookie and the gate should be lifted), you can trigger a custom event. The Gated Content script is listening for this event and will update things once it's recieved.

1. Event name: `gatedcontent`
2. Event detail: `type: GATED_CONTENT_TYPE`

You'll need to pass a `type` property along with the event, which should match the gated content type (e.g. 'cookie' or 'gnav').

Example:
```js
const event = new CustomEvent( 'gatedcontent', {
    detail: {
        type: 'gnav'
    }
});
document.dispatchEvent( event );
```
