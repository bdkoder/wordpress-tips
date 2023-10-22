### BOT & OFFICE Time

```php
add_action( 'init', function () {
	// Get the current server time as a Unix timestamp
	$current_time = current_time( 'timestamp' );

	// Add 6 hours to convert to GMT+6
	$gmt_plus_six_time = strtotime( '+6 hours', $current_time );

	// Extract the current hour in 24-hour format
	$current_hour = (int) date( 'H', $gmt_plus_six_time );

	echo 'Current Hour (GMT+6): ' . $current_hour;

	// // Check if the current hour is between 2 and 7
	if ( $current_hour >= 2 && $current_hour <= 6 ) {
		?>
		Bot Time
		<?php
	} else {
		?>
		Office Time
		<?php
	}
} );
```
