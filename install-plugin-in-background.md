### Install Plugin In Background

```php

function install_plugin_in_background( $plugin_slug ) {
	// Include necessary WordPress files
	if ( ! function_exists( 'download_url' ) ) {
		require_once( ABSPATH . 'wp-admin/includes/file.php' );
	}
	if ( ! function_exists( 'plugins_api' ) ) {
		require_once( ABSPATH . 'wp-admin/includes/plugin-install.php' );
	}
	if ( ! function_exists( 'activate_plugin' ) ) {
		require_once( ABSPATH . 'wp-admin/includes/plugin.php' );
	}
	if ( ! function_exists( 'get_plugins' ) ) {
		require_once( ABSPATH . 'wp-admin/includes/plugin.php' );
	}

	// Setup filesystem, so we can extract the plugin.
	global $wp_filesystem;
	WP_Filesystem();

	// Get plugin information from the WordPress plugin repository
	$api = plugins_api(
		'plugin_information',
		array(
			'slug'   => $plugin_slug,
			'fields' => array(
				'sections' => false,
			),
		)
	);

	if ( is_wp_error( $api ) ) {
		return 'Plugin information not found: ' . $api->get_error_message();
	}

	// Download the plugin from the provided URL
	$download_url = $api->download_link;
	$temp_file    = download_url( $download_url );

	if ( is_wp_error( $temp_file ) ) {
		return 'Error downloading plugin: ' . $temp_file->get_error_message();
	}

	// Unzip the plugin
	$result = unzip_file( $temp_file, WP_PLUGIN_DIR );

	// Remove temporary file after extracting
	@unlink( $temp_file );

	if ( is_wp_error( $result ) ) {
		return 'Error unzipping plugin: ' . $result->get_error_message();
	}

	// Determine the plugin file path
	$all_plugins = get_plugins();
	$plugin_file = '';
	foreach ( $all_plugins as $file => $plugin_data ) {
		if ( strpos( $file, $plugin_slug . '/' ) === 0 ) {
			$plugin_file = $file;
			break;
		}
	}

	if ( empty( $plugin_file ) ) {
		return 'Plugin file not found.';
	}

	// Activate the plugin
	$activate = activate_plugin( WP_PLUGIN_DIR . '/' . $plugin_file );

	if ( is_wp_error( $activate ) ) {
		return 'Error activating plugin: ' . $activate->get_error_message();
	}

	return 'Plugin installed and activated successfully.';
}

add_action( 'init', function () {

	// Example usage
	$plugin_slug = 'hello-dolly';  // Change this to the plugin slug
	$response    = install_plugin_in_background( $plugin_slug );

	error_log( $response );

} );

```
