[MWM](README.md) > Taxonomies

# MWM Taxonomies

MWM registers a handful of Taxonomies for each enabled post type.

The following 'Base' Taxonomies are registered:

* Category
* Topic
* Content Type
* Duration
* Actions
* Franchise
* Author

You may notice the word 'base'; As we register these taxonomies per post type, each post type gets its own unique taxonomy.

## Base Taxonomies

As MWM was first built with one post type in mind, we had hard coded most taxonomies to work with that one post type (recruiter_hub_pt).
As MWM grew, we added more post types and had to update how we register taxonomies.  We now append the post type to the taxonomy 
base name with the exception of (category and author).

For instance the original 'Category' taxonomy was registered as `rh_category` for the post type `recruiter_hub_pt`.  We have left
taxonomies backwards compatible with the `recruiter_hub_pt` and added a helper function to convert taxonomies.

Below is a table that demonstrates what we actually register depending on the post type

| Base Taxonomy | Recruiter Hub Post Type | Non-Recruiter Hub Post Type |
|---------------|-------------------------|-----------------------------|
| category      | rh_category             | <post_type>_mwm_category    |
| topic         | topic                   | <post_type>_topic           |
| content_type  | content_type            | <post_type>_content_type    |
| duration      | duration                | <post_type>_duration        |
| actions       | actions                 | <post_type>_actions         |
| franchise     | franchise               | <post_type>_fanchise        |
| author        | mwm_author              | <post_type>_mwm_author      |


## Helper Function

As mentioned we have a helper function to convert to the correct Taxonomy.

```php
Sample\MarketingWebModules\Taxonomies\Taxonomies::get_post_type_taxonomy( $post_type, $base_taxonomy );
```

Where you can pass in the current Post Type, and what the base taxonomy you want to look up.

## ACF Registration

When you need to register a field and want to use the Taxonomy field and use the correct Taxonomy based on the Post Type, 
we have a custom ACF field named `MwmTaxonomy` to help you build this.

Ex:
```php
use Sample\MarketingWebModules\Acf\Fields\MwmTaxonomy;
...

MwmTaxonomy::make( 'Choose a Category', 'category' )
    ->base_taxonomy( 'category' )
```

Notice the use of the `base_taxonomy()` method.  This will tell ACF to run the `get_post_type_taxonomy()` helper method and 
return the correct taxonomy name based on the post type.  We have two ACF hooks that use this function.  

```php
add_filter( 'acf/fields/taxonomy/query', array( $this->RegisterAcf, 'mwm_taxonomy_query' ), 10, 3 );
add_filter( 'acf/load_field/type=taxonomy', array( $this->RegisterAcf, 'mwm_taxonomy_load_field' ) );
```

Each of these does the base taxonomy lookup and converts to the proper taxonomy based on post type.
