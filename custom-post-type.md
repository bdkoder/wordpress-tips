### Function to get post by condition

```php

/**
 * Function to get post by condition
 */
function get_group_control_query_posts() {
	$args = [ 
		'post_type' => array( 'post', 'movies' ),
		'posts_per_page' => 10,
		'post_status' => 'publish',
	];

	$args['tax_query'] = array(
		'relation' => 'OR',
		array(
			'taxonomy' => 'category',
			'field' => 'slug',
			'terms' => array( 'bangla', 'default-post-cat-2' ),
		),
		array(
			'taxonomy' => 'movies_categories',
			'field' => 'slug',
			'terms' => array( 'bangla', 'hindi' ),
		),
	);

	$posts = get_posts( $args );

	$posts = array_column( $posts, 'post_title', 'ID' );

	echo '<pre>';
	print_r( $posts );
	echo '</pre>';

	return $posts;
}


add_action( 'init', 'get_group_control_query_posts' );
```

### More easy

```php
$categories = array(
    'category_slug_1',
    'category_slug_2',
    'category_slug_3',
    // Add more category slugs as needed
);

$args = array(
    'post_type' => array( 'custom_post_type_1', 'custom_post_type_2' ),
    'tax_query' => array(
        'relation' => 'OR',
    ),
);

foreach ($categories as $category_slug) {
    $args['tax_query'][] = array(
        'taxonomy' => 'category', // Replace with the actual taxonomy name
        'field'    => 'slug',
        'terms'    => $category_slug,
    );
}

$query = new WP_Query( $args );

if ( $query->have_posts() ) {
    while ( $query->have_posts() ) {
        $query->the_post();
        // Output your post content here
    }
    wp_reset_postdata();
} else {
    // No posts found
}

```
