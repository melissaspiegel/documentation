[MWM](../README.md) > Themes

# Themes

Themes in MWM allow us to deliver a consistent yet flexible brand experience across sites. Themes utilize design tokens and mixins to maintain a consistent visual language while allowing flexibility.

The flexibility of our theme system makes it possible to roll out updates globally or target specific experiences. A common use case has been A/B testing new design ideas on subsets of traffic before wider rollout.

MWM themes not only update the look and feel but also allow for variations in layout and functionality. This is achieved through a webpack configuration which compiles separate SCSS and JS assets for each theme, offering entirely independent experiences without the risk of collision. This provides an easy way to iterate on the overall experience without breaking existing features or implementations.

## Theme settings

Themes can be set with three levels of WordPress settings on the admin side:
1. Network Settings (applies to entire multisite network)
2. Post Type settings (applies to specific post types site-wide)
3. Post Meta settings (applies to individual posts)

### Hierarchy

This allows flexibility in targeting specific experiences while still maintaining a consistent brand experience overall. Settings cascade from network to post type to individual posts.

The overriding theme functionality will work from the smallest level (Post Meta) up to the largest level (Network Settings).

### Default

By default, Post Type and Post Meta settings are unset (set to empty string). They can be unset again anytime to return to the theme that’s set by the next level above them.

## Design tokens

Design tokens are a fundamental part of our theming system. All visual styles, colors, spacing etc are defined as design tokens to ensure consistency across themes. In MWM, each token is named for where or how it is used. Even if a token's value is changed, its name remains the same, allowing for seamless updates across themes and components.

By using tokens instead of hardcoded values, we can modify styles globally by simply changing the value of a token. This makes it easier to iterate on designs.

### Token values

The values of our tokens may reference existing tokens from Sample's design system to ensure consistency with other Sample products. For example: our `color-text-primary` token may reference `colors.neutral.1000 (#2D2D2D)` from IFL in one theme, while it may also reference `colors.neutral.800 (#595959)` in another theme as part of a rebranding effort. The distinction here is that our tokens represent the use of styles within our product rather than defining the actual values themselves.

### Token naming conventions

Tokens should use kebab case naming and follow the convention of: `[property]-[element]-[state]`.

Keywords like 'primary', 'secondary' etc are used to denote priority or frequency of use.

Token name examples:
- `color-text-primary` - Represents the most commonly used text color.
- `font-size-paragraph` - The font size used for paragraphs of text.
- `padding-button` - The padding applied to buttons.
- `shadow-card-hover` - The box shadow used on cards when hovered.

We do not need to establish tokens for elements that are scoped to specific partials. Instead, we should override tokens that are more global within the scope of those partials.

For example, a token for `font-size-card-paragraph` would not be needed since paragraphs within cards can simply override the global `font-size-paragraph` token.

## Theme configuration

Themes are configured using an established file and folder structure for both Sass and JavaScript.

### Theme inheritance

In both Sass and JavaScript, themes can leverage existing themes to inherit common functionality and styles. Simply import the desired styles or javascript from an existing theme in order to inherit those features.

### Adding a new Sass theme

To configure a theme in Sass:
1. Create a folder under `src/themes` with the theme name (e.g. `scss/theme/mytheme`)
2. Copy an existing `_config.scss` file from another theme, and modify the values for the theme tokens.
3. Create a `main.scss` file that imports the `_config.scss` and add any global theme styles here.
4. Create a `mytheme.scss` file in each partial directory to apply the new theme to each partial.
5. Modify the webpack config to compile the new theme assets (see other theme configurations for reference).

All `_config.scss` files need to import files from the `scss/tokens/` directory and set the values of tokens used in the theme.

- [Read more about Sass variable configuration](https://sass-lang.com/documentation/at-rules/use/#configuration)

### Adding a new JavaScript theme

To configure a theme in JavaScript:
1. Create a folder under `js/theme` with the theme name (e.g. `js/theme/mytheme`)
2. Create an `index.js` file that exports any theme-specific functions or components
3. Create a `mytheme.js` file within any partial which requires javascript.

## Theme file and folder structure

Themes are configured using the following folder structure (using Aurora and XDS themes as examples):

```
Includes/
  Partials/
    MyPartial/
      views/
        js/
          theme-aurora.js    Aurora JS for this view
          theme-xds.js       xds JS for this view
        scss/
          theme-aurora.scss  Aurora sass for this view
          theme-xds.scss     xds sass for this view
      js/
        theme-aurora.js      Aurora js for partial
        theme-xds.js         xds js for partial
      scss/
        theme-aurora.scss    Aurora sass for partial; imports sass for views
        theme-xds.scss       xds sass for partial; imports sass for views
js/
  theme-aurora/         All global JS for aurora theme
  theme-xds/            All global JS for xds theme
scss/
  tokens/               All design tokens assigned with default base values
  theme-aurora/         All global sass for aurora theme
    global/             Animations, mixins, utility classes, etc...
    _config.scss        The custom definitions for the theme tokens
    main.scss           The entrypoint for the theme stylesheet
  theme-xds/            Example of another theme (same structure as aurora)
    global/
    _config.scss
    main.scss
```

## Sass mixins and functions

Sass Mixins and functions can be found within the `scss/utilities` folder. Common theming mixins include:

| File | Mixin | Description |
|-|-|-|
| `global` | `get-var` | A helper function that gets a token's corresponding CSS variable with a customizable fallback value. |
| `global` | `set-var` | A mixin to set the value of a token's CSS variable with a specified value. Useful for overriding a token in the case of theming partials or color palettes. |
| `global` | `get-token` | A function to get the direct value of a token (rather than the CSS variable). |
| `global` | `set-token` | A function to add or update a token's direct value. **Note: This is primarily used at the base of the stylesheet, since token values are typically stored within CSS variables. Updating these values later may not have the expected effect.** |
| `global` | `set-palette-tokens` | A function to set Sass and CSS tokens for pallet variations. **Note: This is primarily used during theme configuration to set color palettes.** |
| `palette` | `default-dark-palette` | A mixin to provide standard overrides for color tokens when using a dark palette. |
| `palette` | `custom-dark-palette` | Mixin to create custom overrides for dark backgrounds. |
| `palette` | `token-dark-defaults` | Mixin to generate the default token values for a dark palette (can be used to filter out or only include certain tokens by role). |

> See the [color palette documentation](color-palettes.md) for more information on how to use the palette mixins and functions.

## Using theme tokens

To use a theme token, you can simply import the desired tokens file, and use Sass variables directly. These variables reference CSS variables that are set using the same name as the token.

For example:

```scss
@import 'scss/tokens/colors';

.example {
  color: $color-text-primary;
  // Output will be: color: var(--color-text-primary, <fallback-value>);
}
```

More examples:

```scss
// Example 1: Use Sass variables to reference CSS variables
.mwm-mod {
    background-color: $palette-color-primary;
}

.card {
    border-radius: $border-radius-card;
}

.module-1 {
    // Example 2: Using sass mixin to override CSS variables for a specific module
    @include set-var('color-text-secondary', purple);
    color: $color-text-primary;
    // Even though we aren't using the $color-text-secondary color here, all children that do will have the output with the updated variable: var(--color-text-secondary, purple);

    // Example 3: Using the standard dark palette mixin
    @include default-dark-palette;
}

.module-2 {
    .card {
        background: transparent;
        border: 1px solid $palette-color-tertiary;
    }

    // Example 4: Using a mixin to provide custom overrides on dark palettes
    @include custom-dark-palette {
        // Example 4a: Using custom values to override default values
        @include set-var('color-text-primary', #fafafa);
        @include set-var('color-text-secondary', #cacaca);
        @include set-var('color-text-tertiary', #aaa);
        @include set-var('palette-color-tertiary', #777);

        // Example 4b: Using the token default values mixin to generate the default overrides
        @include token-dark-defaults;

        .palette-layer {
            // Example 4c: Using the $token-dark-defaults for only a specific role
            @include token-dark-defaults('palette-layer', 'palette'); // This will output the values for ONLY the `palette` tokens defined in `$token-dark-defaults`

            // Example 4d: Using the $token-dark-defaults for all but the role(s) specified
            @include token-dark-defaults('palette-layer', (palette, border, text), true); // With the "true" flag, this will output all the default tokens except for palette, border, and text roles
        }
    }
}
```

## Adding new theme tokens

Theme tokens can be added in the `tokens/` directory using the `set-token` function. This will automatically assign a value to a matching CSS variable which will be assigned to the Sass variable for use within the theme.

Note: You must add `!default` after the variable assignment to ensure that it can be overridden by the `_config.scss` files if needed for theming purposes.

> Reminder: Tokens should use kebab case naming and follow the convention of: `[property]-[element]-[state]`.

Example:

```scss
$color-button: set-token('color-button', #fff) !default;
// This will output a CSS variable: --mwm--color-button: #fff
// Any reference to `$color-button` will output: var(--mwm--color-button, #fff)
```

> Note: Any time you add a new `color` token, make sure to check that against a dark palette and add to the `$token-dark-defaults` configuration map. [Read more about color palettes](color-palettes.md#token-defaults-for-dark-palettes).

## Creating SCSS Tokens and Utility Mixins for Role-Based Styling
This section provides instructions on how to create reusable tokens and utility mixins in SCSS. These practices ensure consistent styling across different roles in your project.

### Creating a Token for Role-Based Styling
Tokens are defined for specific roles to maintain styling consistency. These roles can be related to typography, colors, spacing etc. Tokens are defined in the ``tokens`` folder.

```
styles/
│
├── tokens/
│   ├── _colors.scss
│   ├── _typography.scss
│   └── _spacing.scss
│
└── ...
```

For Example: If you are creating a new token for ``font-size`` for the ``sub-heading`` role, you can create a mixin to ensure that all content with the ``sub-heading`` role consistently follows the established design.

Here is an example of setting ``font size`` for the ``sub-heading`` role.

File: ``tokens/_typography.scss``

  ```scss
// Create the tokens for the role.
$font-size-sub-heading-desktop: set-token('font-size-sub-heading', 24px) !default;
$font-size-sub-heading-mobile: set-token('font-size-sub-heading', 20px) !default;
  ```

### Creating a Utility Mixin for Role-Based Tokens
Now you can create a utility mixin in ``utilities``. This mixin can be customized to handle the default presentation for the ``sub-heading`` role.

For Example: To create a new utility mixin for the ``sub-heading`` role, you can create a mixin in ``utilities/_typography.scss``.

File: utilities/_typography.scss

```scss
@use 'tokens/typography' as type;
@use 'tokens/colors';
...
/// Example sub heading : Heading partial, mwmHeading class
///
/// @param string $breakpoint - Sets the breakpoint on when to use Desktop font size
@mixin base-sub-heading($breakpoint: 'md') {
    font-size: type.$font-size-sub-heading-mobile;
    font-weight: type.$font-weight-sub-heading-mobile;
    line-height: type.$line-height-sub-heading-mobile;
    letter-spacing: type.$letter-spacing-sub-heading-mobile;
    color: colors.$color-text-primary;

    @include breakpoint-up($breakpoint) {
        font-size: type.$font-size-sub-heading-desktop;
        font-weight: type.$font-weight-sub-heading-desktop;
        line-height: type.$line-height-sub-heading-desktop;
        letter-spacing: type.$letter-spacing-sub-heading-desktop;

    }
}
```

### Implementing Role-Based Utility Mixin
Once the mixin is set up, you can use it throughout your theme to ensure a consistent look.

For example: To apply the role-based typography to an element with the class ``mwm-heading``

File: ``partials/MyPartial/scss/theme-mytheme.scss``

```scss
  .mwm-heading{
        @include type-util.base-heading;
    }
```

## Changing the values for existing tokens

**IMPORTANT: You should NEVER change or edit an existing token value inside any of the `/tokens/*` files.**

When a theme calls for a different initial value for a token than what is originally set in any of the `/tokens/*` files, you can configure a different value within the `_config.scss` of your theme.

Updating or changing the token value directly within a token file could impact any other themes that are using the defaults configured in those files and break that theme.

Example:

You are configuring a new theme and need to use the `$color-text-primary` token with a different value.

```scss
/// tokens/_colors.scss
/// 
/// The token default is set to `#2d2d2d` but you need to change that to instead be `#111111`.

// This value should remain unchanged.
$color-text-primary: set-token('color-text-primary', #2d2d2d) !default;
```

Instead of changing the value directly within the tokens file, you should set that within the `_config.scss` of your new theme.

```scss
/// theme/NEW_THEME/_config.scss
/// 
/// In the config, you will forward the color tokens with the
/// 1. Token you want to update (i.e. `$color-text-primary`)
/// 2. With the new value for your theme (i.e. `#111111`)

// Configure theme tokens
@forward 'tokens/colors' with (
    $color-text-primary: set-token('color-text-primary', '#111111'),
);
```

This ensures that only the theme you are configuring will use this updated value for the token and all other themes remain unchanged.
