[MWM](README.md) > MWM Settings

# MWM Settings

We use settings to configure the MWM plugin in order to serve many different web experiences across our varied portfolio of sites.

To achieve this, there are three different levels of settings to configure the MWM Plugin on our WordPress sites.

1. [Network (Global) Settings](#network-global-settings)
2. [Site Options](#site-options)
3. [Post Type Settings](#post-type-settings)

## Network (Global) Settings

These settings are used to make configurations on a global level, across all locales and often across many post types. You must have an Administrator role to view and edit these settings.

> **Note:** These settings are not limited to multisites, even though they are called Network Settings. On a single site they will still appear on the Admin Dashboard.

Examples of these settings include:

- Selecting post types to enable MWM
- Selecting post types to enable search indexing

### Configuring Network Settings

On multisites, Network Settings can be configured by navigating to the **Network Admin Dashboard** and clicking on **MWM** in the sidebar.

On single sites, the same Global MWM Settings can be made by clicking on the **MWM** options page in the **Admin Dashboard** sidebar.

### Using Network Settings

To use a network setting within the code, it's best to check whether you're on a multisite so that you can call the correct WordPress function.

Example:
```php
if ( is_multisite() ) {
  $post_types = get_site_option( 'mwm_search_index' );
} else {
  $post_types = get_option( 'mwm_search_index' );
}
```

### Updating Network Settings

Network settings code is located in `Includes/NetworkOptions.php`. These settings use the WordPress settings API.

[Read more about how to use the WordPress Settings API](https://developer.wordpress.org/plugins/settings/settings-api/)

## Site Options

MWM Site Options are applied across all post types in **one locale**. Each locale can have different values for each Site Option. A typical use case for this is translating content that exists beyond the scope of one post (such as buttons or navigation elements). Users with editor access should be able to view and edit these settings per locale.

### Configuring Site Options

You can configure these settings by navigating to the Admin Dashboard of any site and clicking on the MWM Site Options page.

### Using Site Options

The public function `MwmSiteOptions::get_option` can be used to implement these site options. This is a wrapper for the WordPress `get_option` function, which automatically sets necessary prefixes to the option name.

It is also recommended to include a default, since historical posts will not have data saved, they will not access the defaults set by ACF.

Example:

```php
use Sample\MarketingWebModules\MwmSiteOptions;

//...

$quote_type = ( new MwmSiteOptions() )->get_option( 'i18n_quote_mark' ) ?? 'double-high';
```

### Updating Site Options

The code for MWM Site Options can be found in `Includes/MwmSiteOptions`. We use Extended ACF to create these options pages.

[Read more about the Extended ACF package](https://github.com/vinkla/extended-acf)

Add a new tab, or add your option to an existing tab. All fields are added to the page in the `get_fields` function.

## Post Type Options

MWM Post Type Options can be configured for each individual post type on each locale. Different post types on the same locale can have different options. Users with editor access for the post type in question should be able to view and edit these settings.

Common uses of these options include things like configuring the local navigation for a microsite.

### Configuring Post Type Options

To configure the Post Type settings, Navigate to the WP Admin Dashboard and find the "MWM [POST TYPE] Settings" page that is nested under the post type you wish to edit.

> **Example:** Dashboard > Recruiter Hub > MWM Recruiter Hub Settings

### Using Post Type Options

The public function `PostTypeOptions::get_option` can be used to implement these options. This is a wrapper for the WordPress `get_option` function, which automatically sets necessary prefixes to the option name.

Example:

```php
use Sample\MarketingWebModules\PostTypeOptions;

//...

$nav_menu_id = PostTypeOptions::get_option( 'menu_id' );
```

### Updating Post Type Options

The code for Post Type Options can be found in `Includes/PostTypeOptions.php`. We use Extended ACF to create these options pages.

[Read more about the Extended ACF package](https://github.com/vinkla/extended-acf)

Add a new tab, or add your option to an existing tab. All fields are added to the page in the `get_fields` function.