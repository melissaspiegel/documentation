[MWM](README.md) > Scripts and Styles

# Scripts and Styles

Within MWM, we use styles and JavaScript in three main areas: global, partial, and admin.

We follow the [BEM naming conventions](http://getbem.com/naming/). For more information see our [naming conventions](file-naming-conventions.md).

## Global

### Styles

Styles can be found under the `scss` folder in the root MWM project.

```markdown
scss/                              # The main styles directory.
    base                           # Directory for all base styles shared across themes.
        modules/                   
            _background-text       
            _carousel-controls     
        _buttons                   
        _forms                     
        _icons                     # Indeed icon styles.
        _nav                       # Local nav styles.
    global                         # Directory for all global assets.
        animations/                # Directory for all global animations.
            fade/                  # Directory for all fade animations.
            _maskedTextReveal      
        mixins/                    # Directory for all global mixins.
            _base                  # Base module/component mixins.
            _colors                # Color and palette mixins.
            _typography            # Font, type, and unit mixins.
            _utilities             # Utility mixins.
        utilities/                 
            index.scss             # Global utility classes.
        variables/                 # Directory for all global variables.
            _bootstrap-override    # Variables set to override bootstrap defaults.
            _colors                # Default and global color variables.
            _layout                # Grid and content layout variables.
            _typography            # Font and type variables.
        _imports                   # The global file that is intended to be imported into stylesheets.
    template                       # Directory for all template-specific styles.
        _page-template             # Styles for the MWM Modular Page template.
        _post-template             # Styles for the MWM Post template.
    theme                          # Directory for all theme-specific styles.
        aurora/                    # Directory for all aurora theme styles.
            mixins/                # Directory for all theme-specific mixins.
                _base              # Base theme module/component mixins.
                _colors            # Theme color and palette mixins.
                _typography        # Theme font/text mixins.
            typography/            # Directory for all theme typography styles.
            utilities/             # Directory for all theme utility classes.
                _palettes          # Color palette utility classes.
            variables/             # Directory for all theme variables.
                _colors            
    main.scss                      # The master file that imports all styles.
```

#### Stylelint

MWM is set up with stylelinting, in order to fix any issues in the stylesheets use command:
```bash
npm run lint
```

### Scripts

Scripts can be found under the `js` folder in the root MWM project.

[See TODO](#global-scripts)

## Partial

```markdown
MwmFooBar/                # The directory and namespace for partial
    views/
        default/
            style.scss    # The "scoped" styles for the default view
            script.js     # The "scoped" javascript for the default view
        alternate/
            style.scss    # The "scoped" styles for the alternate view
            script.js     # The "scoped" javascript for the alternate view
    script.js             # The "global" javascript for this partial
    style.scss            # The "global" styles for this partial
```

> Follow the [naming conventions](file-naming-conventions.md) for the scss and js filenames.

### Styles

A Partial can have "global" root styles that apply to all views as well as separate styles for each view.

> **Note**: Each stylesheet must import the global sass variables and mixins from `/scss/legacy/global/imports` (the actual path of this may vary depending on if the stylesheet is from the global root or the partial views)

#### Global Root

This stylesheet will always start with the name of the module in `spinal-case` (Ex. MwmCard => `mwm-card`).

```scss
@import "../../../../scss/legacy/global/imports";

.mwm-card {
    // your styles here...
}
```

> **Note**: if you are using a stylesheet within a view folder, make sure you import that stylesheet in the root partial stylesheet.

```scss
@import "../../../../scss/legacy/global/imports";

.mwm-card {
    // your styles here...

    @import 'views/default/style';
}
```

#### Partial Views

This stylesheet will always start with the Sass ampersand followed by the view name including two beginning dashes (following the [BEM naming conventions](http://getbem.com/naming/)) (Ex. MwmCard: Default view => `&--default`).

```scss
@import "../../../../../scss/legacy/global/imports";

&--default {
    // your styles here...
}
```

### Scripts

A Partial can have "global" root scripts that apply to the partial as a whole as well as separate scripts for each view.

> **Note**: Unlike the styles, these script files do NOT have to be imported into the root script file.


## Admin

In order to add custom JavaScript or styles to the backend side of WordPress, these assets are separate and must be added/updated differently from the other global and partial assets.

### Styles

Admin styles can be found in the `/scss-admin/admin.scss` file in the root MWM project.

> **Note**: there is currently no configuration for admin styles scoped within a Partial and all admin styles must be added in this file.

### Scripts

#### Global Admin

Global admin scripts can be added under the `/js-admin` folder in the root MWM project. General MWM admin updates can be added into the `marketing-web-modules-admin.js` file. Otherwise, you can create a new, specific file within this folder for your admin script.

#### Partial Admin

Partials also have the ability to add scripts to the admin by adding a `admin.js` file in either the root Partial or scoped within the individual views.

```markdown
MwmFooBar/                # The directory and namespace for partial
    views/
        default/
            admin.js      # The "scoped" admin javascript for the default view
        alternate/
            admin.js      # The "scoped" admin javascript for the alternate view
    admin.js              # The "global" admin javascript for this partial
```


## TODO

### <a id="global-scripts"></a>Global Scripts
@TODO Add tree
```markdown

```

@TODO Do we want to call out specific files for what they do and how to use them?
- custom-elements
- customSearch.js
- helpers.js
- marketing-web-modules.js
- post-template.js
- rh-cookies.js
- rh-gated-content.js
- rh-scroll-modal.js
- socialShare.js
- swiperConfig.js