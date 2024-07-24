[MWM](README.md) > Migrations

# Migrations

Migrations allow us to make changes to our database.  In most cases this will be used to rename or restructure ACF 
fields that we use.  The most common scenarios are as follows:

* Moving fields into or out of field groups
* Changing field keys
* Changing site option keys
* Replacing fields with another set of fields or different functionality

## Running Migrations

To run all of your outstanding migrations, execute the wp command:

```
wp mwm-migrate up
```

If you would like to see which migrations have already ran you can use the command:

```
wp mwm-migrate list
```

## Rolling Back Migrations

To roll back the latest migration operation, you can use the following command:

```
wp mwm-migrate down
```
 >Note: If the last migration added multiple files, this will roll back each of those files.

You may roll back a limited number of migrations by providing the `step` argument to the `down` command.  For example 
this will roll back the last five migrations regardless of when they ran:

```
wp mwm-migrate down --step=5
```

## Creating Migrations

The easiest way to create a new migration is to use our [CLI command](using-mwm-cli.md)

```php
npm link
mwm-cli create migration what-you-are-changing
```

> Note: you may need to run 'npm install' in the 'bin/cli' folder for the cli commands to work

Migration files are stored in the `/migration` folder.  Files must be named as follows: 
`YYYY_MM_DD_HHMM_what_you_are_changing.php`.  For example:
>2021_05_03_1430_update_cta_url_name.php

### Migration Structure

A migration class contains two methods: `up` and `down`.  The `up` method is used to update the data in your database, 
while the `down` method should reverse the operations performed by the `up` method.

>Note: In some instances a `down` method may not be necessary.  This depends on if it's possible to roll back, as you 
> may be editing meta_values with no way to know what the original values were.

Other methods may be added, to help organize the file, as needed.

Example:
```php
<?php

use Sample\MarketingWebModules\Abstracts\MwmMigrationAbstract;

/**
 * Class Example
 */
class Example extends MwmMigrationAbstract {

	/**
	 * Run the migration
	 */
	public function up() {

	}

	/**
	 * Reverse the migration
	 */
	public function down() {

	}
}
```

### Migration Helper Methods

As we manipulate a lot of ACF fields and structures, we have added a handful of Helper Methods to quickly update metadata.

| Method                                                   | Description                                                                   |
|----------------------------------------------------------|-------------------------------------------------------------------------------|
| addMetaData( $post_id, $meta_key $meta_value, $mod_name) | This function adds metadata to a post                                         |
| updateMetaData( $post_id, $meta_key $meta_value )        | This function updates a meta_value on a post                                  |
| deleteMetaData( $post_id, $meta_key )                    | This function deletes metadata from a post                                    |
| updateMetaKey( $post_id, $old, $new, $mod_name )         | This function updates the meta_key on a post, keeping the meta_value the same |
| copyMetaData( $post_id, $old, $new, $mod_name )          | This function copies one meta_value to a new (or existing) meta_key           |
| updateFieldKey( $key, $post_id, $mod_name )              | This function updates the meta_value for the ACF Field Key                    |

### Examples

Lets take a look at a real life example.  For this migration `migrations/2022_12_02_1217_update_call_to_action_headers.php`
we updated the MwmCallToAction partial to use our standard MwmModuleHeader partial.  To do this we to know what current
fields are on the MwmCallToAction, and what fields they coorisponds to in the MwmModuleHeader.

In this example we need to move the old fields to the new fields:

| Old Field   | New Field                                                          |
|-------------|--------------------------------------------------------------------|
| mod_title   | module_heading                                                     |
| mod_eyebrow | module_eyebrow                                                     |
| mod_content | module_subcopy                                                     |
| mod_cta     | cta_repeater - This is a special case that we'll have to deal with |

Let's jump into the file, which has been trimmed to only show important bits:
```php
public function up() {
    // Gets all posts that have an MwmCallToAction module added
    $posts = $wpdb->get_results( "SELECT * FROM `${table_prefix}postmeta` WHERE meta_value LIKE '%mwm_call_to_action%'" );

    foreach ( $posts as $post ) {
        $modules = unserialize( $post->meta_value );
        foreach ( $modules as $index => $module ) {
            $key_prefix = "rh_mod_page_content_$index";

            // Essentially we loop through all the posts above, and then go through each module to see if it is the MwmCallToAction module
            if ( $module === 'mwm_call_to_action' ) {
                // Update mod_title to module_header
                // As you may notice $key_prefix is the specific index and parent ACF group
                // In this case we are really moving the meta_key
                // From: rh_mod_page_content_${index}_mod_title
                // To:   rh_mod_page_content_${index}_module_heading
                $this->updateMetaKey( $post->post_id, "${key_prefix}_mod_title", "${key_prefix}_module_heading" );

                // Do the same for mod_eyebrow to module_eyebrow, and mod_content to module_subcopy
                $this->updateMetaKey( $post->post_id, "${key_prefix}_mod_eyebrow", "${key_prefix}_module_eyebrow" );
                $this->updateMetaKey( $post->post_id, "${key_prefix}_mod_content", "${key_prefix}_module_subcopy" );

                // Grab the original CTA field and check if it exists
                $cta = get_post_meta( $post->post_id, "${key_prefix}_mod_cta", true );
                if ( ! empty( $cta ) ) {
                    // Since we have to move the CTA into a CTA Repeater field this is not a simple 1 to 1 update
                    // Our CTA Repeater field has 2 fields: cta_link and variation

                    // First add the parent CTA Repeater field
                    $this->addMetaData( $post->post_id, "${key_prefix}_cta_repeater", '1');

                    // Add the variation, this is taken from the original field mod_cta_style
                    $this->updateMetaKey( $post->post_id, "${key_prefix}_mod_cta_style", "${key_prefix}_cta_repeater_0_variation" );
                    // Next update mod_cta to cta_link
                    $this->updateMetaKey( $post->post_id, "${key_prefix}_mod_cta", "${key_prefix}_cta_repeater_0_cta_link" );
                    // Notice both of these were under the ACF field 'cta_repeater' and includes the index that they belong to
                    // ie: cta_repeater_0_cta_link
                }//end if
            }//end if
        }//end foreach
    }//end foreach
}

// An example of the down method, whose goal is to but the data as it was before the up() migration.  Again trimmed to
// show only important parts.
public function down() {
    $posts = $wpdb->get_results( "SELECT * FROM `${table_prefix}postmeta` WHERE meta_value LIKE '%mwm_call_to_action%'" );
    foreach ( $posts as $post ) {
        $modules = unserialize( $post->meta_value );
        foreach ( $modules as $index => $module ) {
            if ( $module === 'mwm_call_to_action' ) {
                $key_prefix = "rh_mod_page_content_$index";

                // Update Meta Keys going from the 'new' fields back to the 'old' fields
                $this->updateMetaKey( $post->post_id, "${key_prefix}_module_heading", "${key_prefix}_mod_title" );
                $this->updateMetaKey( $post->post_id, "${key_prefix}_module_eyebrow", "${key_prefix}_mod_eyebrow" );
                $this->updateMetaKey( $post->post_id, "${key_prefix}_module_subcopy", "${key_prefix}_mod_content" );

                // Delete CTA Repeater as its no longer needed
                $this->deleteMetaData( $post->post_id, "${key_prefix}_cta_repeater" );

                // Move the style and cta back
                $this->updateMetaKey( $post->post_id, "${key_prefix}_cta_repeater_0_variation", "${key_prefix}_mod_cta_style" );
                $this->updateMetaKey( $post->post_id, "${key_prefix}_cta_repeater_0_cta_link", "${key_prefix}_mod_cta" );
            }//end if
        }//end foreach
    }//end foreach
}
```

In another example, we can use an SQL Update statement to do a simple find and replace.  In these cases you do not need to loop through posts or data, and can
simply run the SQL statement.

In this example we are changing the module name from 'mwm_content_columns' to 'mwm_card_grid'.

```php
public function up() {
    global $wpdb;
 
    // Replace all instances of "mwm_content_columns" with "mwm_card_grid"
    $wpdb->query(
        "UPDATE IGNORE $wpdb->postmeta
        SET meta_value = REPLACE(meta_value, 's:19:\"mwm_content_columns', 's:13:\"mwm_card_grid')
        WHERE meta_key = 'rh_mod_page_content'
            AND meta_value LIKE '%s:19:\"mwm\_content\_columns%'"
    );
}
```
