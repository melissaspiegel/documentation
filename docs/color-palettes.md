[MWM](../README.md) > Color Palettes

# Color palettes

MWM color palettes are subthemes that are applied at the module level. This allows for easy color customization on a per-module basis while adhering to the overall themed look and feel. Each theme can have its own unique color palettes, but the system supporting these palettes remains consistent across themes.

## Color palette structure

Each color palette has four variations that serve different purposes in design:

| Variation | Description |
|-|-|
| Primary | The color that is most prominantly used (often used for module background color) |
| Secondary | Often used to highlight important elements and make them stand out (e.g. headings, shapes, buttons, etc) |
| Tertiary | Sometimes used for alternate background elements (e.g. background text) |
| Quaternary | Sometimes used for background accents (e.g. background circles and patterns) |

These four values are stored in a Sass map called `$palettes` with an arbitrary palette number as the key and the four colors as a nested list of variation names (primary, secondary, etc...) as keys and color references as values.

Example:

```scss
$palettes: (
    'palette-1': (
        'primary': #fff,
        'secondary': #1f5f7b,
        'tertiary': #cde6f9,
        'quaternary': #f5fafe,
    ),
    // ...
);
```

## Using color palette tokens

The color palette tokens are defined to set a color scheme and change the values for any element using these tokens when the color palette changes for a given theme.

```scss
$palette-color-primary
$palette-color-secondary
$palette-color-tertiary
$palette-color-quaternary
```

The role for these tokens is defined [above](#color-palette-structure).

These tokens are used the same way as the [theme tokens](./themes.md#using-theme-tokens).

## Dark palettes

The default color palettes are built in a "light" scheme so text can present darker, however when a color palette is dark, the styles need a way to know to update the text to be light against the darker backgrounds.

> Note: **The palette utility `util/palette as palette-util` needs to be imported after the `config` in order to work**

### Token defaults for dark palettes

The configuration for the default dark palettes is set in the `$token-dark-defaults` map found in `utilities/_palette.scss`.

By default, the `$token-dark-defaults` map assigns the tokens (by their role - Ex. palette, text, button, etc.) to use a specific token (for the type - Ex. palette or palette-layer) that is set for the "on dark" variation (Ex. `text-color-primary` uses the `text-color-primary-on-dark` token value). The variables will be generated using the `palette` type and will generate the `palette-layer` type by setting the token value to `initial` (see Example 1 below), but this can be customized in this config.

```scss
$token-dark-defaults: (
     // Role
    'palette': (
        // Type
        palette: (
            // Token: Token on dark
            'palette-color-secondary': 'palette-on-dark',
        )
    ),
    // Role
    'text': (
        // Type
        palette: (
            // Token: Token on dark
            'text-color-primary': 'text-color-primary-on-dark',
            'text-color-secondary': 'text-color-secondary-on-dark',
            'text-color-tertiary': 'text-color-tertiary-on-dark',
        )
    ),
    // Role
    'focus': (
        // Type
        palette: (
            // Token: Token on dark
            'focus-primary-color': 'focus-color-on-dark'
        ),
        // Example 1: The `focus-primary-color` token needs the value to be the same as the default token for the "palette-layer" type
        // Note that none of the other roles have this type defined.
        // Type
        'palette-layer': (
            // Token: Token on dark
            'focus-primary-color': 'focus-color-on-dark'
        )
    ),
);
```

This config would result in an output like this:
```scss
.selector.mwm-palette-10 {
    --mwm--palette-color-secondary: #fff;
    --mwm--text-color-primary: #fff;
    --mwm--text-color-secondary: #fff;
    --mwm--text-color-tertiary: #fff;
    --mwm--focus-primary-color: #fff;
}

.selector.mwm-palette-10 .palette-layer {
    --mwm--palette-color-secondary: initial;
    --mwm--text-color-primary: initial;
    --mwm--text-color-secondary: initial;
    --mwm--text-color-tertiary: initial;
    --mwm--focus-primary-color: #fff; // This is #fff instead of initial because of the configuration map
}
```

#### Adding new tokens to the default config

If you need to configure a new token to be included in the default dark palette overrides, you can add it to the `$token-dark-defaults` map. You will find the role that applies for your token, and set the `palette` (and `palette-layer` if necessary) to the dark token set in `tokens/colors`.

### Default dark palette overrides

The `default-dark-palette` mixin is the standard behavior for adding dark themes to modules. This mixin uses the values set in the `$token-dark-defaults` configuration map to set all the defined tokens for each role the their "dark" variation.

To use the `default-dark-palette` mixin, simply include it in your module (Ex. `@include default-dark-palette;`);

Example output:
```scss
.selector.mwm-palette-10 {
    --mwm--text-color-primary: #fff;
    --mwm--text-color-secondary: #fff;
    --mwm--text-color-tertiary: #fff;
    --mwm--link-color: #fff;
    --mwm--link-hover-color: #fff;
    --mwm--link-active-color: #fff;
    --mwm--field-background-color: #fff;
    --mwm--field-border-color: #fff;
}
```

#### Palette Layer Class

Some modules have an component (like a card) when on a dark background retain their "light" background-color and still require the default token values for any token that is used within the `.palette-layer` class.

For this reason, we need to set all the tokens back to their initial values so they are not overridden by the "light" colors defined in the `$token-dark-defaults` configuration map.

Example output:
```scss
.selector.mwm-palette-10 .palette-layer {
  --mwm--text-color-primary: initial;
  --mwm--text-color-secondary: initial;
  --mwm--text-color-tertiary: initial;
  --mwm--link-color: initial;
  --mwm--link-hover-color: initial;
  --mwm--link-active-color: initial;
  --mwm--field-background-color: initial;
  --mwm--field-border-color: initial;
}
```

### Custom dark palette overrides

When the default overrides do not work for your module, you can create a custom dark palette override using the `custom-dark-palette`. Include the mixin in your module and you can set any tokens you need to override or apply any other custom styles.

```scss
@include custom-dark-palette {
        // Example 1: Setting a token to a specific value on dark palettes
        @include set-var('text-color-primary', #fafafa);
        @include set-var('text-color-secondary', #cacaca);
        @include set-var('text-color-tertiary', #aaa);
        @include set-var('palette-color-tertiary', #777);

        // Example 2: Setting a style unrelated to tokens
       .card {
            background-color: transparent;
       }
    }
```

#### Using default dark tokens

Sometimes when we need a custom dark palette, we only really need it to override a couple items and we don't want to have to redefine all the default values set in the `$token-dark-defaults` map that the `default-dark-palette` mixin uses.

To include these values in your custom dark palette, you can use the `token-dark-defaults` mixin. This mixin will output the default values for each role defined in the `$token-dark-defaults` map.

```scss
@include custom-dark-palette {
        // Example 1: Use the token-dark-defaults mixin to generate the default overrides
        @include token-dark-defaults; // May also be written as `@include token-dark-defaults('palette');`

        .palette-layer {
            // Example 2: Use the token-dark-defaults mixin to generate the default overrides for only the "palette-layer" type
            @include token-dark-defaults('palette-layer');
        }
    }
```

##### Filtering default tokens

Additionally, sometimes that are cases where we may only want to include OR exclude the default token values for a specific role. You can still use the `token-dark-defaults` mixin if you need to either include certain tokens or filter certain tokens out.

To filter the default tokens, you can pass the `$type` and `$roles` and `$exclude` parameters to the mixin.

| Parameter | Type | Values |
|-|-|-|
| `$type` | `string` | Either `palette` or `palette-layer` depending on the values you need for your module. |
| `$roles` | `list` or `string` | A Sass list - `(palette, text)` or string - `'palette'` of token roles to be included or excluded. |
| `$exclude` | `booleean` | If set to `true`, the values provided for the `$roles` will be excluded from the tokens. Otherwise, *ONLY* these tokens will be included. |

```scss
// @include token-dark-defaults($type, $roles, $exclude);
@include token-dark-defaults('palette', (palette), true);
```

```scss
@include custom-dark-palette {
        // Example 1: Use the token-dark-defaults mixin to generate the default overrides for ONLY the "text" role tokens
        // Note: When using the filter feature, the "palette" type must be used.
        @include token-dark-defaults('palette', 'text'); // This will output the values for ONLY the "palette" type `text` tokens defined in `$token-dark-defaults`

        .palette-layer {
            // Example 2: Using the $token-dark-defaults for only a specific role
            // Note: This example uses a Sass list and the `$exclude` flag
            @include token-dark-defaults('palette-layer', (palette, border, text), true); // With the "true" flag, this will output all the "palette-layer" default tokens except for palette, border, and text roles
        }
    }
```

## Defining new color palettes

## Mapping palettes across themes