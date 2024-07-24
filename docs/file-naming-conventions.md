[MWM](README.md) > File naming conventions

# File naming conventions

:warning:&nbsp; **Important**: This framework relies on consistently following file naming conventions to function correctly.

### Example file and folder structure of a Module named "Article Grid"

```markdown
MwmArticleGrid/                      # The directory and namespace for module
    views/
        default.view.php             # The module default view (frontend)
    MwmArticleGrid.php               # The class file for the module (controller)
```

### Example file and folder structure of a Partial named "Article Grid"

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
    style.scss                       # The sass for this partial
```

### Module/Partial Name

Module and Partial names should be descriptive, but not too specific to an individual design.

- Example: A design has a section called "Hot Topics", but the module itself could be named "Three Up" since its purpose is to display a row of three cards.

> **Note**: All names *must* go through the Creative Team.

### Module/Partial Folders

Module/Partial folder, class filename, and class name *must* match, be prefixed with "Mwm", and use **PascalCase**.

- Examples:
  - Correct: `MwmArticleGrid/MwmArticleGrid.php`
  - Incorrect: `swm-article-grid/swm-article-grid.php`
  - Incorrect: `MwmArticleGrid/article_grid.php`

### Module/Partial ACF

ACF Flex layout name *must* match the class name for the module or partial, but use **snake_case** instead.

- Examples:
  - Correct: `swm_article_grid`
  - Incorrect: `MwmArticleGrid`
  - Incorrect: `article_grid`

### Module/Partial View

View files should be `default.view.php` in the views folder.

- Examples:
  - Correct: `views/default.view.php`
  - Incorrect: `views/swm-article-grid.php`
  - Incorrect: `views/MwmArticleGrid.view.php`

### Partial Assets

JavaScript and Sass files must be named `script.js` and `style.scss` in the Partial root or view folder.

- Examples:
  - Correct: `script.js` | `style.scss`
  - Incorrect: `MwmArticleGrid.js` | `MwmArticleGrid.scss`
  - Incorrect: `articleGrid.js` | `articleGrid.scss`

> **Note**: Modules will never house JavaScript or Sass files. These will always be within the Partial folder.
