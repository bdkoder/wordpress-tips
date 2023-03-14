### Add Post Programmatically

```php
function add_custom_post() {
	$post = array(
		'post_title'   => 'My Post',
		'post_content' => 'This is my post.',
		'post_status'  => 'publish',
		'post_author'  => 1,
		'post_type'    => 'post'
	);
	$post_id = wp_insert_post($post);
	update_post_meta($post_id, 'my_meta_key', 'my_meta_value');
}
// add_action('init', 'add_custom_post');

// add post with custom post id
function _add_custom_post() {
	$post = array(
		'ID'           => 2005,
		'post_title'   => 'My Post 2',
		'post_content' => 'This is my post.',
		'post_status'  => 'publish',
		'post_author'  => 1,
		'post_type'    => 'post'
	);
	$post_id = wp_insert_post($post);
	update_post_meta($post_id, 'my_meta_key', 'my_meta_value');
}
// add_action('init', '_add_custom_post');
```
