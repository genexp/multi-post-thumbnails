# Multiple Post Thumbnails

Multiple Post Thumbnails is a WordPress plugin that allows you to add as many
post thumbnails to a post type as you want. Need more than one featured image?
This is for you.

## Quick Start

### Register a new Thumbnail

First, in your theme's `functions.php` (or you can create a shim plugin that
does this on the `wp_loaded` action) register a thumbnail. To do this, create a
new `MultiPostThumbnails` instance and pass in an array of arguments:

* `label` __required__ The name of the post thumbnail to display in the admin metabox.
* `id` __required__ Used to build the CSS class for the admin meta box. Needs to be unique
  and valid in a CSS class selector (no spaces, can't start with a number, etc).
* `post_type` __required__ The post type to register this thumbnail for. Defaults
  to post.
* `priority` *optional*: The admin metabox priority. Defaults to 'low'.
* `context` *optional*: The admin metabox context. Defaults to 'side'.

> __Example:__ register a new thumbnail for posts that shows as 'Secondary Image' 
in the admin metabox.

```php
if (class_exists('MultiPostThumbnails')) {
    new MultiPostThumbnails(
        array(
            'label' => 'Secondary Image',
            'id' => 'secondary-image',
            'post_type' => 'post'
        )
    );
}
```

### Display the Thumbnail

The template tag `MultiPostThumbnails::the_post_thumbnail` is similar to
WordPress' `the_post_thumbnail` but it displays your custom 
thumbnail in a post loop. It accepts the following arguments:

* `$post_type` __required__ The post type of the thumbnail being retrieved. If 
  used in the loop, `get_post_type()` can be used to prevent hard coding the 
  post type.
* `$id` __required__ The id used to register the thubmnail (from the step above).
* `$post_id` *optional*: The Post ID of the post to get the thumbnail for. The 
  currently queried post's ID will be used if not provided.
* `$size` *optional*: Image size to display. Defaults to 'post-thumbnail', 
  which a theme sets using 
  `set_post_thumbnail_size( $width, $height, $crop_flag )`. Or, custom image
  sizes registered with `add_image_size()` can be used.
* `$attr` *optional*: Passed to `wp_get_attachment_image()`. Specified as a query
  string or array of attributes.
* `$link_to_original` *optional*: Wrap link to original image around thumbnail?

_NOTE:_ if you are calling this function outside the post loop the `$post_id`
argument is required. Leaving it off may work but may provide unexpected results
depending on the currently queried object and where you are calling it.

 > __Example:__ Display the thumbnail registered in the previous step in the
loop.

```php
<?php if (class_exists('MultiPostThumbnails')) :
    MultiPostThumbnails::the_post_thumbnail(
        get_post_type(),
        'secondary-image'
    );
endif; ?>
```

## Filters

The following filters are provided.

### `mpt_unprotect_meta`

Applying a filter to this and returning `true` (or anything that evaluates to
`true`) will make the meta for storing thumbnails protected so it doesn't show
in the Custom Fields metabox.

### `[post type]_[post id]_thumbnail_size`

This filter is used to override the `$size` argument when 
`MultiPostThumbnails::the_post_thumbnail()` or 
`MultiPostThumbnails::get_the_post_thumbnail()` is called for the given
`[post type]` and `[post id]`. Probably not very useful but provided to be
consistent with WordPress' template tags of the same name.

### `[post type]_[thumbnail id]_thumbnail_html`

This filter is used to override the thumbnail html (provided by WordPress'
`wp_get_attachment_image()` function) returned/output when
`MultiPostThumbnails::the_post_thumbnail()` or 
`MultiPostThumbnails::get_the_post_thumbnail()` is called for the given
`[post type]` and `[thumbnail id]`, where `[thumbnail id]` is the argument used 
when registering the thumbnail. 

## Other Useful Functions

For advanced themes and usage, the following functions may prove useful.

### `get_post_thumbnail_id()`

> Function signature:

```php
MultiPostThumbnails::get_post_thumbnail_id(
    $post_type,
    $id,
    $post_id
)
```

Get the WordPress post ID of the thumbnail attachment.

* `$post_type` __required__: The post type of the post the thumbnail is set on.
* `$id` __required__: The id used to register the thumbnail.
* `$post_id` __required__: The post ID of the post the thumbnail is set on.

### `get_post_thumbnail_url()`

> Function signature:

```php
MultiPostThumbnails::get_post_thumbnail_url(
    $post_type,
    $id,
    $post_id = 0,
    $size = null
)
```

Get the URL of the thumbnail.

* `$post_type` __required__: The post type of the post the thumbnail is set on.
* `$id` __required__: The id used to register the thumbnail.
* `$post_id` *optional*: The post ID of the post the thumbnail is set on. 
  Defaults to the post ID of the currently queried object.
* `$size` *optional*: The size to use. Defaults to the original size.

### `get_the_post_thumbnail()`

> Function signature:

```php
MultiPostThumbnails::get_the_post_thumbnail(
    $post_type,
    $thumb_id,
    $post_id = null,
    $size = 'post-thumbnail',
    $attr = '' ,
    $link_to_original = false
)
```

Get the thumbnail but don't echo it. This uses the same arguments as 
`MultiPostThumbnails::the_post_thumbnail()` as detailed above.

### `has_post_thumbnail()`

> Function signature:

```php
MultiPostThumbnails::has_post_thumbnail(
    $post_type,
    $id,
    $post_id = null
)
```

Does the post have the specified thumbnail?

* `$post_type` __required__: The post type of the post the thumbnail is set on.
* `$id` __required__: The id used to register the thumbnail.
* `$post_id` *optional*: The post ID of the post the thumbnail is set on. 
  Defaults to the post ID of the currently queried object.

