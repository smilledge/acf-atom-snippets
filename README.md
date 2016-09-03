Atom Snippets for Wordpress ACF (Advanced Custom Fields)
=====

This is an Atom port of my [Sublime ACF snippets](https://github.com/smilledge/acf-sublime-snippets) package. Most of the fields are the same, however the `:` separator has been changed to `-` in the Atom version.

# Install

Clone this repo into your Atom packages folder and restart Atom.

## Mac

`git clone https://github.com/smilledge/acf-atom-snippets.git /Users/$(whoami)/.atom/advanced-custom-fields-snippets`

# Snippets

## Basic Fields and conditionals

`field` / `field-sub` / `field-option` / `field-a` / `field-h1` / `field-p`

Get a field, sub-field or options field by it's name.

```
<?php if (get_field('field_name')) : ?>
    <?php the_field('field_name'); ?>
<?php endif; ?>
```

`field-if` / `field-elseif`

If statements using the value of an ACF field.

```
<?php if (get_field('field_name')) : ?>
<?php else : ?>
<?php endif; ?>
```

## Date field

Get the value of a date field and format the date string.

```
<?php if (get_field('field_name')) :
    $date = DateTime::createFromFormat('Ymd', get_field('field_name')); ?>
    <?php echo $date->format('d-m-Y'); ?>
<?php endif; ?>
```

## Image and file field

`field-image`

Display image field with a return value set to URL.

```
<?php if (get_field('field_name')) : ?>
    <img src="<?php the_field('field_name'); ?>" alt="<?php the_field(''); ?>">
<?php endif; ?>
```

`field-image-id`

Display image field with a return value set to ID.

```
<?php
    if (get_field('field_name')) {
        $attachment_id = get_field('field_name');
        wp_get_attachment_image($attachment_id, 'full');
    }
?>
```

`field-image-object`

Display image field with a return value set to object.

```
<?php if (get_field('field_name')) :
    $image = get_field('field_name'); ?>
    <!-- Full size image -->
    <img src="<?php echo $image['url']; ?>" alt="<?php echo $image['alt']; ?>"/>
    <!-- Thumbnail image -->
    <img src="<?php echo $image['sizes']['thumbnail']; ?>" alt="<?php echo $image['alt']; ?>"/>
<?php endif; ?>
```

`field-file`

Display file field with a return value set to URL.

```
<?php if (get_field('field_name')) : ?>
    <a href="<?php the_field('field_name'); ?>" >Download File</a>
<?php endif; ?>
```

`field-file-id`

Display file field with a return value set to ID.

```
<?php if (get_field('field_name')) :
    $attachment_id = get_field('field_name');
    $url = wp_get_attachment_url($attachment_id);
    $title = get_the_title($attachment_id); ?>
    <a href="<?php echo $url; ?>" ><?php echo $title; ?></a>
<?php endif; ?>
```

`field-file-object`

Display file field with a return value set to object.

```
<?php if (get_field('field_name')) :
    $file = get_field('field_name'); ?>
    <a href="<?php echo $file['url']; ?>"><?php echo $file['title']; ?></a>
<?php endif; ?>
```

## Relationship field

`field-relationship`

Get a relationship field and loop over all returned posts.

```
<?php $posts = get_field('field_name'); ?>
<?php if ($posts): ?>
    <ul>
        <?php foreach ($posts as $post) :
            setup_postdata($post); ?>
            <li>
                <a href="<?php the_permalink(); ?>"><?php the_title(); ?></a>
            </li>
        <?php endforeach; wp_reset_postdata(); ?>
    </ul>
<?php endif; ?>
```

`field-post`

Get a post relationship field.

```
<?php $post = get_field('field_name');
    if ($post) : setup_postdata($post); ?>
    <a href="<?php the_permalink(); ?>"><?php the_title(); ?></a>
<?php wp_reset_postdata(); endif; ?>
```

## Location field

`field-location`

Get the street address from a location field.

```
<?php if (get_field('field_name')) :
    $location = get_field('field_name'); ?>
    <?php echo $location['address']; ?>
<?php endif; ?>
```

`field-location-staticmap`

Get a location field and convert it to a static Google Map.

```
<?php if (get_field('field_name')) :
    $location = get_field('field_name');
    $coordinates = isset($location['coordinates']) ? $location['coordinates'] : $location; ?>
    <img src="http://maps.google.com/maps/api/staticmap?markers=<?php echo $coordinates; ?>&size=500x300&sensor=false" alt="">
<?php endif; ?>
```

`field-location-map`

Get a location field and convert it to an interactive Google Map. Also adds a marker to the location. The CSS is used to prevent rendering issues with map controls caused by most responsive CSS grids.

```
<?php if (get_field('field_name')) :
    $location = get_field('field_name');
    $coordinates = isset($location['coordinates']) ? $location['coordinates'] : $location; ?>

    <script src="https://maps.googleapis.com/maps/api/js?v=3.exp&sensor=false"></script>
    <script>
        google.maps.event.addDomListener(window, 'load', function() {
            var map = new google.maps.Map(document.getElementById('map-canvas'), {
                zoom: 16,
                center: new google.maps.LatLng(<?php echo $coordinates; ?>),
                mapTypeId: google.maps.MapTypeId.ROADMAP,
                scrollwheel: false
            });
            new google.maps.Marker({
                    position: new google.maps.LatLng(<?php echo $coordinates; ?>),
                    map: map
            });
        });
    </script>

    <style>
        #map-canvas img {
            max-width: inherit;
        }
    </style>

    <div id="map-canvas" style="width:500px;height:300px;"></div>
<?php endif; ?>
```

## Repeater field

`field-repeater`

Get and loop over a repeater field.

```
<?php if (have_rows('field_name')) : ?>
    <?php while(have_rows('field_name')) : the_row(); ?>
        <?php the_sub_field('sub_field_name'); ?>
    <?php endwhile; ?>
<?php endif; ?>
```

`field-repeater-grid`

Loop over a repeater filed and seperate results into rows. The second tabstop is the row length.

```
<?php if (get_field('field_name')) : ?>
    <div class="items">
        <?php foreach (array_chunk(get_field('field_name'), 2) as $row): ?>
            <div class="row">
                <?php foreach ($row as $item): ?>
                    <div class="item">
                        <?php echo $item['field_name']; ?>
                    </div>
                <?php endforeach; ?>
            </div>
        <?php endforeach; ?>
    </div>
<?php endif; ?>
```

## Gravity Forms Field

`field-form`

Display a gravity form. The parameters for gravity_form() are outlined in the [Gravity Forms documentation](http://www.gravityhelp.com/documentation/page/Embedding_A_Form#Function_Call).

```
<?php if (get_field('field_name')) {
    $form = get_field('field_name');
    gravity_form_enqueue_scripts($form->id, true);
    gravity_form($form->id, display_title, display_description, false, field_values, enable_ajax, 1);
} ?>
```

## Query posts

`field-query`

Query a post type on a field value and loop over posts.

```
<?php
    $args = array(
        'post_type' => 'post',
        'post_status' => 'publish',
        'posts_per_page' => 10,
        'meta_key' => 'field_name',
        'meta_value' => 'field_value'
    );
    $query = new WP_Query($args);
?>
<?php if($query->have_posts()) : ?>
    <ul>
        <?php while ($query->have_posts()) :
            $query->the_post(); ?>
            <li>
                <a href="<?php the_permalink(); ?>"><?php the_title(); ?></a>
            </li>
        <?php endwhile; ?>
    </ul>
<?php endif; ?>
<?php wp_reset_query(); ?>
```

## Misc

`field-dump`

`var_dump` the field contents wrapped in `<pre>` tags. Useful for deubbing.

```
<pre>
    <?php
        var_dump(get_field('field_name'));
        die();
    ?>
</pre>
```
