[MWM](README.md) > WP PHPUnit Tests

# WP PHPUnit Tests

:bulb:&nbsp; See the [WordPress Developer Handbook: Writing PHP Tests](https://make.wordpress.org/core/handbook/testing/automated-testing/writing-phpunit-tests/) for a general usage guide.

---

## Setup WordPress unit test environment

The wpdev environment has PHPUnit setup globally already. You just need to run some commands to integrate it with a WordPress test environment.

Use the following commands to setup PHPUnit with the WordPress test library.

### wpdev commands

```bash
# Connect to wpdev docker
~/wpdev/wpdev ssh

# Navigate to project root (use whichever site you are testing on)
# example: cd indeeddev.wpengine.com/wp-content/plugins/marketing-web-modules
cd [SITE_PATH]/wp-content/plugins/marketing-web-modules

# Run the wp test installation script (MUST include database docker instance)
bin/install-wp-tests.sh wp_tests root root wpdev-db
```

---

## Setup Your Code Editor

The WordPress Test library is separate from your WordPress installation, so you'll need to let your Code Editor know about it.

You can download the library locally in order to include these files, even though you are actually accessing them within docker.

Download the test library to your user directory with the following command:
```
svn co https://unit-tests.svn.wordpress.org/trunk ~/wordpress-tests
```

### VS Code

1. Go to the PHP Intelephense extension settings, and find "Include Paths".
2. Add the path to the library you just installed. Example: `/Users/YOUR_LDAP/wordpress-tests`.
3. If you're using the wpdev docker environment, you can use the PHPUnit VS Code extension to run tests from your IDE. Use the following settings to set that up:

#### User Settings
```json
"phpunit.command": "docker exec wpdev-wp",
"phpunit.docker.container": "wpdev-wp",
"phpunit.phpunit": "phpunit",
"phpunit.clearOutputOnRun": false,
```

#### Workspace Settings (must be set per project)
```json
"phpunit.paths": {
    "${workspaceFolder}": "/var/www/html/indeedempweb.wpengine.test/wp-content/plugins/marketing-web-modules",
},
"phpunit.args": [
    "--configuration /var/www/html/indeedempweb.wpengine.test/wp-content/plugins/marketing-web-modules/phpunit.xml"
],
```

---

## Run PHPUnit

NOTE: If you have integrated PHPUnit with VS Code, you can now run unit tests directly in your IDE. Otherwise, you can use the following command lines to run tests in terminal.

First, you'll need to connect to the `wpdev-php-xdebug` environment in order to run the tests from the project root. See setup instructions above on how to connect to each environment respectively.

Once you're connected to the environment and have navigated to the project root you can run the following commands:

### PHPUnit with VVV

```bash
# Run all tests
phpunit

# Run group-specific tests
# phpunit --group <group name>
phpunit --group theme
phpunit --group acf

# Run one of the following commands to run a report in your desired format:
phpunit --coverage-text                            # Show report summary in terminal
phpunit --coverage-html tests/phpunit/reports/html # Interactive HTML report with dashboard
```

:bulb:&nbsp; More info on reports can be found here: https://phpunit.readthedocs.io/en/7.0/textui.html#command-line-options


## Tips & Tricks

1. **Naming**: Both test files and test methods must begin with the prefix "test-". Follow the [WordPress Guide](https://make.wordpress.org/core/handbook/testing/automated-testing/writing-phpunit-tests/) when determining the best name for your test class or method. This will help us keep our tests organized.
2. **grouping**: Use the `@group` keyword in phpdoc comments to group similar tests that can be run together.
