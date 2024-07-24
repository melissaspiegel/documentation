[MWM](README.md) > Localization

# MWM Localization

We localize content in a couple ways, depending on the site. We have developed a few standard appoaches to how we localize content in a scalable way.

## Translating ACF data

On all sites that use MWM, we use Advanced Custom Fields to edit and update our content. This is the data source we use to provide localized content as well.

We do not use `.po` files to translate our content, in order to avoid the need for development support. Instead, we rely on the WordPress CMS to provide translated content, which is submitted by a team of translators.

Further, in some cases localization can include omitting sections of content, [changing site configurations](mwm-settings.md) or changing images. These are all things that can be updated via ACF per locale.

## Omitting content

As a standard, our modules should omit any elements that were left blank.

For example, if an editor leaves a module heading blank, the frontend should not display the empty `<h2></h2>` tag.

This ensures optimal flexibility when localizing and editing content.

## Localization with multisite environments

Some of our sites are multisite environments. In these cases, each site in our multisite represents a locale (language/country), and unique content is provided within each of these sites.

For example, indeeddev is our most popular multisite environment. The same page may exist on both the en-us site and the de-de site on indeeddev, however each will likely have different content catered to their locales.

## Localization with WPML

A small selection of our sites use WPML for translations. This method of translating content does not require very many changes to our codebase, as it will still pull content from ACF in the same way.

[Read more about WPML](https://wpml.org/)