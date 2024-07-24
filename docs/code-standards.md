[MWM](README.md) > Code Standards

# Code Standards

## JavaScript code standards

We use the AirBnb ESLint code standards to check syntax, find problems, and enforce code style in
JavaScript. To ensure that you are following these standards, you can run eslint in a couple ways.

## SCSS code standards

We use the standard scss config to enforce consistency across our stylesheets. To ensure that you are following these standards, you can setup stylelint in a couple ways.

### VS Code

View code standards for VS Code detailed in this [document](https://wiki.sample.com/display/MKT/Configuring+Visual+Studio+Code+for+MSYS+WebDev+standards)

### PHP Storm

View code standards for PHP Storm detailed in this [document](https://wiki.sample.com/display/MKT/Configuring+PHPStorm+for+MSYS+WebDev+standards)

### Command Line

If you'd like a list of all issues in a file, you can run the following command in the commandline:

```bash
npx eslint FILENAME.js
phpcs FILENAME.php
```

If you'd like to fix any issues in the stylesheets use command:
```bash
npm run lint
```