[MWM](/README.md) > Using MWM Modules

# Using MWM Modules

Our modules are our top level elements that wrap a group of html elements or partials together. They are the only thing we use that spans the entirety of a page's available width. If you have any experience with Atomic Design, they are akin to [organisms](https://bradfrost.com/blog/post/atomic-web-design/#organisms).

The modules behave like a wrapper for our partials so they work within FlexibleContent ACF fields.

## Creating Modules

To create modules use our `swm-cli` command. See [using-swm-cli.md](/docs/using-swm-cli.md) for details.

## File Structure

```markdown
MwmArticleGrid/                      # The directory and namespace for module
    views/
        default.view.php             # The module default view (frontend)
    MwmArticleGrid.php               # The class file for the module (controller)
```

Modules will be located in subfolders under `Includes/Modules` that match the module class name. Example: `Includes/Modules/MwmArticleGrid/`.

### Default View

All modules contain a default view that is located under a `views` folder named `default.view.php`.

### Module Class

The main module class file is located in the root module folder and follows the same naming conventions as the folder. Example: `Includes/Modules/MwmArticleGrid/MwmArticleGrid.php`.

This file includes:
- Main partial class (named the same as the module) imported as an alias of `Partial`
- A `$root_partial` variable set to the `Partial::class`
- Methods: `get_acf()` and `get_data()`

## Class Methods

### get_acf()

This method is responsible for adding the main partial ACF fields to the FlexibleContent Layout for the module.

```php
//...
public function get_acf() {
    self::$layout = Layout::make( 'Article Grid Module', 'swm_article_grid' )
        ->layout( 'block' )
        ->fields( Partial::make() );

    return parent::get_acf();
}
```

### get_data()

This method is responsible for retrieving all ACF fields, modifying data, and returning that data for the view.

The `parent::get_data()` method sets up default variables for the view file including: `tn_component`, `mod_id`, `mod_classes`, and `partial`.

If needed, you can modify the data being passed to the view.

```php
// ...
public function get_data() {
    $data = parent::get_data();

    // Example: how to modify data for module
    $data['variation'] = $data['swm_mod_view'];
    
    return $data;
}
```

## Module View

Modules will only have a single default view and is responsible for creating the wrapper for the root partial and setting a variation (when needed).

```php
<div
id="<?php echo esc_attr( $mod_id ); ?>"
class="swm-mod swm-article-grid <?php echo esc_attr( $mod_classes ); ?>" data-tn-component="<?php echo esc_attr( $tn_component ); ?>">
    <div class="swm-mod__container">
        <div class="swm-mod__content">
            <?php $partial->render( $variation ); ?>
        </div>
    </div>
</div>
```
