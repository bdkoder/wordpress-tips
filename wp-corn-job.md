### Run Corn Job in Every 5 minutes [WordPress]
```php

function create_file_within_wp_content() {
	$time = current_datetime();
	$time = $time->format( 'H-i-s' );

	$file = WP_CONTENT_DIR . '/test-' . $time . '.txt';
	if ( ! file_exists( $file ) ) {
		$fp = fopen( $file, 'w' );
		fwrite( $fp, 'test' );
		fclose( $fp );
	}
}

// add_action( 'init', 'create_file_within_wp_content' );


// See http://codex.wordpress.org/Plugin_API/Filter_Reference/cron_schedules
add_filter( 'cron_schedules', 'isa_add_every_five_minutes' );
function isa_add_every_five_minutes( $schedules ) {
	$schedules['every_five_minutes'] = array(
		// 'interval' => 60 * 5,
		'interval' => 30,
		'display'  => __( 'Every 5 Minutes', 'textdomain' )
	);
	return $schedules;
}

// Schedule an action if it's not already scheduled
if ( ! wp_next_scheduled( 'isa_add_every_five_minutes' ) ) {
	wp_schedule_event( time(), 'every_five_minutes', 'isa_add_every_five_minutes' );
}

// Hook into that action that'll fire every five minutes
add_action( 'isa_add_every_five_minutes', 'create_file_within_wp_content' );

register_deactivation_hook( __FILE__, 'isa_deactivate' );

function isa_deactivate() {
	$timestamp = wp_next_scheduled( 'every_five_minutes' );
	wp_unschedule_event( $timestamp, 'every_five_minutes' );
}

// echo '<pre>';
// print_r( _get_cron_array() );
// echo '</pre>';
```
