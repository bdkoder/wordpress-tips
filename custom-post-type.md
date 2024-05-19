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

### Easy and Final Step
```php

/**
 * Function to get post by condition
 * Final RND
 */
function get_group_control_query_posts() {

	$args = array(
		'post_type' => array( 'post', 'movies' ),
		'posts_per_page' => 10,
		'post_status' => 'publish',
		'tax_query' => array(
			'relation' => 'OR',
		),
	);

	// $args['tax_query'][] = array(
	// 	'taxonomy' => 'movies_categories',
	// 	'field' => 'slug',
	// 	'terms' => array( 'bangla', 'hindi' ),
	// );

	// $args['tax_query'][] = array(
	// 	'taxonomy' => 'category',
	// 	'field' => 'slug',
	// 	'terms' => array( 'bangla', 'default-post-cat-2' ),
	// );

	// 2nd
	// $term = get_term( 22 );
	// $taxonomy_name = $term->taxonomy;

	// $args['tax_query'][] = array(
	// 	'taxonomy' => $taxonomy_name,
	// 	'field' => 'term_id',
	// 	'terms' => array( 22, 21 ),
	// );


	// new final rnd

	$data = get_option( 'linkboss_custom_query', '' );
	$cat = $data['__categories'];
	$args['tax_query'] = array_merge( $args['tax_query'], $cat );



	$posts = get_posts( $args );
	$posts = array_column( $posts, 'post_title', 'ID' );

	echo '<pre>';
	print_r( $posts );
	echo '</pre>';


	return $posts;
}
```
### Pages also merge

```php


if ( ! is_admin() ) {
	add_action( 'init', function () {

		$query_data = get_option( 'linkboss_custom_query', '' );
		$post_type = isset ( $query_data['post_sources'] ) && ! empty ( $query_data['post_sources'] ) ? $query_data['post_sources'] : array ( 'post', 'page' );

		$posts_args = array (
			'numberposts' => 11,
			'post_type' => $post_type,
			'post_status' => array ( 'publish', 'draft', 'trash' ),
			'tax_query' => array (
				'relation' => 'OR',
			),
		);

		$cat = $query_data['__categories'];
		if ( ! empty ( $cat ) ) {
			$posts_args['tax_query'] = array_merge( $posts_args['tax_query'], $cat );
		}

		$posts = get_posts( $posts_args );

		// Query for pages without taxonomy filtering
		$page_args = [ 
			'post_type' => 'page',
			'posts_per_page' => 10,
			'post_status' => 'publish',
		];

		$pages = get_posts( $page_args );

		// Merge the results
		$all_posts = array_merge( $posts, $pages );

		$all_posts = array_column( $all_posts, 'ID', 'post_title' );

		if ( ! empty ( $all_posts ) ) {
			echo '<pre>';
			print_r( $all_posts );
			echo '</pre>';
		} else {
			echo '<pre>';
			echo 'No posts found';
			echo '</pre>';
		}
	} );
}
```

### Custom Post Type with Category
```php
// custom post type 'movies' with category
function create_post_type() {
	register_post_type('movies',
		array(
			'labels' => array(
				'name' => __('Movies'),
				'singular_name' => __('Movie')
			),
			'public' => true,
			'has_archive' => true,
			'taxonomies' => array('category'),
			'show_in_rest' => true,
			'supports'     => array( 'title', 'editor', 'author', 'thumbnail', 'excerpt', 'comments' )
		)
	);
}
add_action('init', 'create_post_type');
```
### Custom Post Type Response
```php
function custom_publish_post_function( $post_id ) {
	// Perform custom actions when a post is published
	error_log( 'Post ID: ' . $post_id );
}

// default post type
add_action( 'publish_post', 'custom_publish_post_function' );

// 'movies' post type
add_action( 'publish_movies', 'custom_publish_post_function' );
```
