[MWM](README.md) > Templates

# Templates
There are two templates that come with MWM:
1. **Sample Web Modules Page Template** - This is our completely modular page template.
2. **Sample Web Modules Post Template** - This is our content-focused template using a combination of Gutenberg and ACF.

## Adding templates
It should be a rare need to add new templates, but in the case that we need to, it is simple.
Add new templates to this array in `/marketing-web-modules.php`.

```php
$this->templates = array(
	// Add new templates here!
	// just file without any sub folders
	'new-template-file.php' => 'New Template',
);
```
