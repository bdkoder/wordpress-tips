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

### HTML
```html
<div id="plugin-install-container">
    <button id="install-plugin-button">Install Plugin</button>
    <div id="progress-bar-container" style="display: none;">
        <div id="progress-bar" style="width: 0%; height: 20px; background-color: green;"></div>
    </div>
    <div id="install-status"></div>
</div>
```

### Action
```js
document.addEventListener('DOMContentLoaded', function () {
    const installButton = document.getElementById('install-plugin-button');
    const progressBarContainer = document.getElementById('progress-bar-container');
    const progressBar = document.getElementById('progress-bar');
    const installStatus = document.getElementById('install-status');

    installButton.addEventListener('click', function () {
        progressBarContainer.style.display = 'block';
        progressBar.style.width = '0%';
        installStatus.innerHTML = 'Starting installation...';

        const steps = [
            'Downloading plugin...',
            'Unzipping plugin...',
            'Activating plugin...',
            'Installation complete!'
        ];

        let currentStep = 0;

        function updateProgress() {
            if (currentStep < steps.length) {
                installStatus.innerHTML = steps[currentStep];
                progressBar.style.width = ((currentStep + 1) / steps.length) * 100 + '%';
                currentStep++;
                setTimeout(updateProgress, 1000); // Simulate progress
            } else {
                installStatus.innerHTML = 'Plugin installed and activated successfully.';
            }
        }

        updateProgress();

        // Trigger AJAX call to install plugin
        jQuery.ajax({
            url: ajax_object.ajax_url,
            type: 'POST',
            data: {
                action: 'install_plugin',
                plugin_slug: 'hello-dolly' // Change this to the plugin slug
            },
            success: function (response) {
                console.log(response);
                installStatus.innerHTML = response;
            },
            error: function (error) {
                console.error(error);
                installStatus.innerHTML = 'Error installing plugin.';
            }
        });
    });
});
```

### Action Handler
```php
function install_plugin_ajax_handler() {
    if ( ! current_user_can( 'install_plugins' ) ) {
        wp_send_json_error( 'You do not have permission to install plugins.' );
    }

    $plugin_slug = isset( $_POST['plugin_slug'] ) ? sanitize_text_field( $_POST['plugin_slug'] ) : '';

    if ( empty( $plugin_slug ) ) {
        wp_send_json_error( 'Plugin slug is required.' );
    }

    $response = install_plugin_in_background( $plugin_slug );

    if ( strpos( $response, 'successfully' ) !== false ) {
        wp_send_json_success( $response );
    } else {
        wp_send_json_error( $response );
    }
}
add_action( 'wp_ajax_install_plugin', 'install_plugin_ajax_handler' );
```

