# Marketing Web Modules

**A modular template system for building marketing pages at Indeed.**

The [**Marketing Web Modules Page Template**](/docs/templates.md) is entirely comprised of Advanced Custom Fields Flexible Content layouts, which allow us to build in a modular, componentized way. By adding, removing, or adapting modules, our marketing team can build any number of page designs with greatly reduced developer support.

MWM provides a framework for easy **"plug-and-play" development** of modules. See detailed instructions for creating new modules and partials [using the MWM CLI](/docs/using-mwm-cli.md) for more information.

## Contents

* [Installation instructions](#installation)
* [Development](#development)
* [Deployment instructions](#deployment)
* [Deployment Pipeline](docs/deployment-pipeline.md)
* [Code Standards](docs/code-standards.md)
* [Naming conventions](docs/file-naming-conventions.md)
* [Templates](docs/templates.md)
* [MWM Settings](docs/mwm-settings.md)
* [Scripts and Styles](docs/scripts-and-styles.md)
* [Themes](docs/themes.md)
* [Color Palettes](docs/color-palettes.md)
* [Creating New Modules/Partials](docs/using-mwm-cli.md)
* [Using MWM Modules](docs/using-mwm-modules.md)
* [Using MWM Partials](docs/using-mwm-partials.md)
* [Gated Content - Module](docs/gated-content-partial.md)
* [Gated Content - Article](docs/gated-content-article.md)
* [Migrations](docs/migrations.md)
* [WP PHPUnit Tests](docs/php-unit-tests.md)
* [Search Index](docs/search-index.md)
* [Localization](docs/localization.md)
* [Taxonomies](docs/taxonomies.md)
* [Signals Tracking](docs/signals-tracking.md)
* [Chart.js](docs/chartjs.md)
* [CSS Naming Conventions](docs/css-naming-conventions.md)
* [Contributing](docs/contributing.md)
* [Creating Modules with CLI](docs/creating-modules-with-cli.md)
* [Motion Control GIF Freezeframes](docs/motion-control-gif-freezeframes.md)

## <a name="installation"></a>Installation instructions

### Requires

1. Composer 1.8.x >= 2.3.7
2. Node 14.x.x >= 14.17.0
3. npm 6.4.x >= 6.14.17
4. PHP 7.3 >= 7.4.33

### Clone Repo

```bash
cd marketing-web-modules
composer install
npm config set registry https://nexus.corp.indeed.com/repository/npm/
npm i
npm run dev
```

## <a name="deployment"></a>Deployment instructions

### Git Branches

| Branch | Description |
| --- | --- |
| `origin/development` | This is our primary working branch. All work should be based and branched off from here. |
| `origin/staging` | This is our staging branch. All feature branches should be merged into staging before development. |


## <a name="development"></a>Development

Note: You should NOT be creating feature branches from staging. All feature branches should be branched off of development.

Advanced Custom Fields (ACF)

Advanced Custom Fields, or ACF is the backbone to our backend experience. Currently, we mostly utilize this through an abstraction called Extended ACF.

Extended ACF

Extended ACF is how we add different fields to a module or partial.

Local Assets

	1.	Update or Add to wp-config.php the following lines setting ENVIRONMENT = ‘development’.

  ```php
# if ENVIRONMENT === development : Use local assets, otherwise use S3 assets
if ( ! defined( 'ENVIRONMENT' ) ) {
    define( 'ENVIRONMENT', 'development' );
}
  ```

 	2.	Run the commands listed below to compile assets during development using Webpack.

### NPM Commands

  ```bash
npm run watch                     # Watch and compile css and js files
npm run dev                       # Build assets for local development
npm run build                     # Build assets for production: minified css and js files
npm run deploy                    # Builds and Deploys assets to S3 (This should not be done locally)
npm run bump-staging              # Bumps version numbers (This should not be done locally)
npm run bump-prod                 # Bumps version numbers to the next minor number
npm run bump-prod-major           # Bumps version numbers to the next major number 
  ```

### JS unit test commands ( mocha/chai )
```bash
npm test                          # run all unit tests on each save of JS files
npm test-once                     # run all unit tests once
mocha test/FILENAME.js            # run a specific unit test once
mocha test/FILENAME.js --watch    # run a specific unit test on each save of JS files
```
Deployment instructions

Staging

All asset building and deployment is handled in Gitlab CI. Locally we only need to merge into staging or development then bump the version number (production only) for the updated assets.

To deploy to Staging follow the below example.

```bash
git checkout staging
git pull
git merge jira/ldap/WORDPRESS-#### 
git push
npm run bump-staging
```
After the push Gitlab will take over the deployment. See Deployment Pipeline.

Production

To deploy to Production follow the below example.

First, merge the current development branch into your feature branch.

```bash
git checkout development
git pull
git checkout jira/ldap/WORDPRESS-####
git merge development
git push
```
Then, merge your feature branch into development to trigger the deployment.

```bash
git checkout development
git merge jira/ldap/WORDPRESS-#### 
npm run bump-prod
git commit -am "bump to v[version number]"
git push
```
After the push Gitlab will take over the deployment. See Deployment Pipeline.

Deployment of Assets

CSS, JS, and Image files are typically deployed via our deployment pipeline. If you must deploy locally, you’ll need to add a credentials.json file in the root folder.
Then you can run the command:
  
  ```bash
npm run bump-<environment> (optional)
npm run deploy
  ```

Example credentials.json

```json
{
  "accessKeyId": "SECRETKEY_ID",
  "secretAccessKey": "SECRETKEY"
}
```

Gitlab CI

Gitlab CI is how we test and deploy our code to the world.

	•	Deployment Guide
	•	Gitlab CI Pipelines

Best Practices

We have some robust coding standards. Be sure to have the following installed and available within your code editor:

	•	PHP Code Sniffer
	•	ESLint
	•	WordPress Coding Standards