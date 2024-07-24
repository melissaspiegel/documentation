[MWM](README.md) > Using MWM Partials

# Using MWM Partials

Partials are reusable components that can be used within a module, within other partials, or standalone as part of a template.  Following [Atomic Design](https://bradfrost.com/blog/post/atomic-web-design), they can be an [Atom](https://bradfrost.com/blog/post/atomic-web-design/#atoms), [Molecule](https://bradfrost.com/blog/post/atomic-web-design/#molecules), or [Organism](https://bradfrost.com/blog/post/atomic-web-design/#organisms).

Partials can be an:
- Atom: `MwmCta`
- Molecule: `MwmCard`
- Organism: `MwmArticleGrid`

[[_TOC_]]

## Creating Partials

To create partials use our `mwm-cli` command. See [using-mwm-cli.md](using-mwm-cli.md) for details.

## File Structure

```markdown
MwmArticleGrid/                      # The directory and namespace for partial
    views/
        default/
            view.php                 # The partial default view (frontend)
            style.scss               # The styles for the default view
            script.js                # The javascript for the default view
        alternate/
            view.php                 # The partial "alternate" view (frontend)
            style.scss               # The styles for the alternate view
            script.js                # The javascript for the alternate view
    MwmArticleGrid.php               # The class file for the partial (controller)
    script.js                        # The javascript for this partial
    style.scss                       # The styles for this partial
```

Partials will be located in subfolders under `Includes/Partials` that match the partial class name. Example: `Includes/Partials/MwmArticleGrid/`.

> **Note**: All `scss` and `js` files within a partial are optional and the files can be removed if not needed.

### Partial Views

All partials have a default view and additional views can be added to change the HTML layout and styling.

The default view sits in the `views/default` folder of a module and is named `view.php`.

The rest of our views are subfolders (named for the view) under the `views/` folder and can hold a php file called `view.php` filled with html, a `style.scss` file, and can also include a javascript file named `script.js`. 

### Partial Class

The main partial class file is located in the root partial folder and follows the same naming conventions as the folder. Example: `Includes/Partials/MwmArticleGrid/MwmArticleGrid.php`.

This file includes:
- `model()` function
- `get_acf_fields()`
- `get_acf_field_options()`
- `set_data()`

### Partial Styles and Scripts

Partials can have styles and scripts in either the root partial or within the partial views. For more information about the different kinds of styles and scripts, see [scripts-and-styles.md](scripts-and-styles.md).

> **Note**: any admin JavaScript can be created similarly within the root partial or within the partial views by adding a `admin.js` file.

## Class Methods

### model()

The model will define the data structure for the partial's ACF fields that is returned back to the parent when called with `set_data()`

 ```php
class MwmCard extends MwmPartialAbstract {
    /**
     * The data model.
     * 
    * @return array
    */
    public function model() {
        return array(
            'copy'              => '',
            'cta'               => new MwmCta(),
            'cta_variation'     => 'Set a default value here',
        );
    }
}
```

#### Abstract `base_model()`

Each partial has standard data that is inherited from the `base_model()` in the `MwmPartialAbstract`. These attributes `classes` and `attributes` are automatically included with every partial `model()`

### get_acf_fields()

This method is responsible for compiling the ACF Wordplate fields the partial needs to configure the view. This can be a combination of other custom MWM partials, a standard Wordplate field, as well as a custom MWM partial group.

```php
public static function get_acf_fields() {

    return array(
        ...MwmPostSelect::make(), // Custom Mwm Partial
        Accordion::make( 'Toggle Manual Entry Fields' ), // Wordplate Field
        MwmManualEntry::make( 'Manual Entry', 'block' ) // Custom Mwm Partial Group
    );
}
```

If a partial needs more organization for the ACF fields layout, the fields can be separated into functions and combined within `get_acf_fields()`.

```php
// Separate Options Tab ACF Fields
public static function options_tab() {
    $fields = array(
        Select::make( 'Variation', 'mwm_mod_view' ),
        MwmColorPalettes::colorPicker()
            ->defaultValue( 'w-2' )
    );

    return parent::make_tab( 'Options', $fields );
}

// Separate Main Content Tab ACF Fields
public static function main_content_tab() {
    $fields = array(
        ...MwmModuleHeading::make(),
    );

    return parent::make_tab( 'Main Content', $fields );
}

// Separate Featured Tab ACF Fields
public static function featured_tab() {
    $fields = array(
        Repeater::make( 'Features' )
            ->fields(
                array(
                    ...MwmCard::make()
                )
            )
    );

    return parent::make_tab( 'Featured Content', $fields );
}

public static function get_acf_fields() {
    // Combine all fields from functions
    return array_merge(
        self::options_tab(),
        self::main_content_tab(),
        self::featured_tab()
    );
}
```

### get_acf_field_options()

This method returns a separate set of options available to be used on the parent partial (as needed).

File: `Includes/Partials/MwmCard/MwmCard.php`
```php
public static function get_acf_field_options() {
// displaying acf fields for the user to select these options in a parent partial
return array(
    Select::make( 'Card Variation', 'card_style' )
        ->choices(
            array(
            'article' => 'Article',
            'default' => 'Full Card',
            )
        )
    );
}
```

These options should then be called in a parent partial using the `::options()` function.

File: `Includes/Partials/MwmFeatured/MwmFeatured.php`
```php
public static function get_acf_fields() {
    return array(
        ...MwmCard::options()
    );
}
```

> **Note**: If your partial does not have any options you may return an empty array. But your partial must include this function.

### set_data()

This method passes the data from the ACF fields on the backend to the view on the frontend. This is where the values from ACF fields can be manipulated and/or stored within variables that mimic the `model()`. All data will then be passed to the `parent::set_data()` function for the view to access.

When adding child partials we use the method `add_child()` passing in the partial class and the array of data. This array of data will continue to be passed into that partial's `set_data()` function. If needed, you can also manipulate this data before it gets passed to the child partial.

```php
public function set_data( $data ) {
    // Example: how to pass data to a child partial
    $data['cards'] = $this->add_child( MwmCard::class, $data );

    // Example: how to change data before passing to a child partial
    $data['labels'] = $this->add_child( MwmLabels::class, array( 'labels' => $data['label'] ) );

    // Example: how to set custom values
    $data['foo'] = 'bar';

    return parent::set_data( $data );
}
```

## Frontend View

All partials will have a `default` view and have the ability to include additional views (as needed). These views will have access to the partial `model()` where the properties are accessible with variables. 

> **Note**: all views must be created at the start when using the `mwm-cli create partial` command. Any views to be added afterwards will need to follow our [naming conventions](file-naming-conventions.md) for the view filename.

```php
<div
    class="mwm-card mwm-card--default <?php echo esc_attr( $classes ); ?>"
    <?php echo wp_kses_post( strip_tags( $attributes ) ); ?>
    data-tn-element="<?php echo esc_attr( $tn_element ); ?>"
>
</div>
```

### Rendering Best Practices

Each partial is responsible for rendering the view for itself and needs to check to make sure the partial only renders if a certain amount of base data is passed through.

File: `Includes/Partials/MwmEyebrow/MwmEyebrow.php`
```php
<?php if ( ! empty( $module_eyebrow ) ) : ?>
	<<?php echo esc_attr( $heading_level ); ?> class="mwm-eyebrow <?php echo esc_attr( $classes ); ?>"><?php echo wp_kses_post( $module_eyebrow ); ?></<?php echo esc_attr( $heading_level ); ?>>
<?php endif; ?>
```

## Updating Partials

### Adding a new ACF Field

To add more functionality to a partial, an additional ACF field may be needed to gather more data from the backend for the views.

1. The new ACF field(s) must be added to the `get_acf_fields()` class method within the current partial
2. The field name can be used in the view as a variable or it can be redefined in the `set_data()` function (if needed)

Scenario: The `MwmHero` partial needs new functionality so the customer can choose a maximum limit of cards to display.

In the `get_acf_fields()` class method within the `MwmHero` partial, we would add a new `Number` Wordplate field:
File: `Includes/Partials/MwmHero/MwmHero.php`
```php
public static function get_acf_fields() {
    return array(
        Number::make( 'Set Max Limit', 'mod_hero_set_max_limit' )
            ->defaultValue( 6 )
            ->min( 1 )
            ->max( 10 ),
        //...
    );
}
```

In the view, the `$mod_hero_set_max_limit` variable to control the number of articles that display:
File: `Includes/Partials/MwmHero/views/default/view.php`
```php
<div class="swiper-hero-container hero-carousel">
    <div class="swiper-wrapper">
        <?php foreach ( $hero_articles as $index => $article ) : ?>
            <div class="swiper-slide hero-article-link card-wrap">
                <?php
                    // We can use the ACF field as a variable to control the number of cards
                    if ( ( $index + 1 ) <= $mod_hero_set_max_limit ) {
                        $article->no_progress()->no_copy()->tn_element( 'hero_card_' . $index )->render( 'link' );
                    }
                ?>
            </div>
        <?php endforeach; ?>
    </div>
</div>
```

### Using Partial Modifier Functions

A partial can contain public functions within the Partial Class that are accessible to the parent partial. These functions can used in two different ways:

1. By the Parent partial: used to modify the data passed to the child partial in the parent partial class
2. By the Partial itself: used in the view to modify the result (can be used to display the data differently depending on the view)

#### Example: By the Parent Partial

Scenario: The first heading on a page needs to be an `h1` in order to comply with Indeed Accessibility Guidelines.

The `MwmModuleHeading` partial needs to control its two child partials (`MwmEyebrow` and `MwmHeading`) and set the correct heading tag depending on whether this heading is a part of the first MWM Module used on a page.

File: `Includes/Partials/MwmModuleHeading/MwmModuleHeading.php`
```php
public function set_data( $data ) {
    // Helper function: Includes/Traits/IsHero
    $is_hero = $this->is_hero( $data['row_index'] );

    // Setting the eyebrow and heading data
    $data['eyebrow'] = $this->add_child( MwmEyebrow::class, $data );
    $data['heading'] = $this->add_child( MwmHeading::class, $data );

    // Use the `level()` modifier function (defined in the `MwmEyebrow` class) to
    // change the level of the eyebrow if it is the first heading item on the
    // page (part of the first MWM Module)
    if ( $is_hero ) {
        $data['eyebrow']->level( 'h1' );
    } else {
        $data['eyebrow']->level( 'h2' );
    }

    return parent::set_data( $data );
}
```

#### Example: By the Partial

Scenario: The article cards used in the `MwmHero` partial will only need to display the title and link in the content. Since the `MwmCard` partial can be used in other partials where they may want to show copy in the content we cannot simply remove that ACF field.

The `MwmHero` partial needs to render the `hero_articles` as cards using the `MwmCard` partial, and use the `no_copy()` modifier function in the view so no HTML relating to the copy is displayed.

File: `Includes/Partials/MwmHero/views/default/view.php`
```php
<div class="swiper-hero-container hero-carousel">
    <div class="swiper-wrapper">
        <?php foreach ( $hero_articles as $index => $article ) : ?>
            <div class="swiper-slide hero-article-link card-wrap">
                <?php
                    // use the `no_copy()` modifier function on the `$article` (`MwmCard`)
                    // and render the "link" view of the `MwmCard` partial
                    // Note: the `render()` function comes from the Abstract
                    $article->no_copy()->render( 'link' );
                ?>
            </div>
        <?php endforeach; ?>
    </div>
</div>
```

## Working with Color Palettes

**Note: This should ONLY ever be used on the root Partial**

To give a user the ability to select a color palette, you can use the `colorPicker()` method from the `MwmColorPalettes` class. Color palettes are a collection of four colors that can be used on an Mwm Module, these colors are defined in the `/scss/global/theme_aurora/_colors.scss` file and created as css variables in the `@mixin make-color-palette-utility-classes`:

- --mwm-theme--color-background // Background: The main background color.
- --mwm-theme--color-accent     // Accent: Color used for primary highlights.
- --mwm-theme--color-secondary  // Dark accent: A darker shade, sometimes used for alternate background elements (e.g. background text).
- --mwm-theme--color-tertiary   // Light accent: A lighter shade, sometimes used for background accents (e.g. background circles).

```php
public static function get_acf_fields() {
    return array(
            Wysiwyg::make( 'Text', 'mod_formatted_text' ),
            TrueFalse::make( 'Constrain Copy to Body Width?', 'mod_constrain_body' ),
            MwmColorPalettes::colorPicker() // This method should never be passed arguments
                ->defaultValue( 'w-2' ),
        )}
```