### SQL Log
```php
add_action( 'shutdown', 'sql_logger' );
function sql_logger() {
	global $wpdb;
	$log_file = fopen( ABSPATH . '/wp-content/sql_log.txt', 'a' );
	fwrite( $log_file, "//////////////////////////////////////////\n\n" . date( "F j, Y, g:i:s a" ) . "\n" );
	foreach ( $wpdb->queries as $q ) {
		fwrite( $log_file, $q[0] . " - ($q[1] s)" . "\n\n" );
	}
	fclose( $log_file );
}
```
