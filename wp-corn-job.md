### Run Corn Job in Every 5 minutes [WordPress Propr Class]
```php
class CustomCronJob {

    public function __construct() {
        add_action('init', array($this, 'createFileWithinWpContent'));

        add_filter('cron_schedules', array($this, 'addEveryFiveMinutes'));

        if (!wp_next_scheduled('isa_add_every_five_minutes')) {
            wp_schedule_event(time(), 'every_five_minutes', 'isa_add_every_five_minutes');
        }

        add_action('isa_add_every_five_minutes', array($this, 'createFileWithinWpContent'));

        register_deactivation_hook(__FILE__, array($this, 'deactivate'));
    }

    public function createFileWithinWpContent() {
        $time = current_datetime();
        $time = $time->format('H-i-s');
        $file = WP_CONTENT_DIR . '/test-' . $time . '.txt';

        if (!file_exists($file)) {
            $fp = fopen($file, 'w');
            fwrite($fp, 'test');
            fclose($fp);
        }
    }

    public function addEveryFiveMinutes($schedules) {
        $schedules['every_five_minutes'] = array(
            'interval' => 300, // 5 minutes in seconds
            'display' => __('Every 5 Minutes', 'textdomain')
        );
        return $schedules;
    }

    public function deactivate() {
        $timestamp = wp_next_scheduled('isa_add_every_five_minutes');
        wp_unschedule_event($timestamp, 'isa_add_every_five_minutes');
    }
}

new CustomCronJob();
```

### Run Corn Job in Every 5 minutes [Basic Test Method]
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

### Multi Job
```php

/**
 * Description of Corn
 * 
 * @since 0.0.0
 */
class Corn {
	private static $instance = null;

	/**
	 * Get Instance
	 * 
	 * @since 0.0.0
	 */
	public static function get_instance() {
		if ( ! isset( self::$instance ) ) {
			self::$instance = new self();
		}

		return self::$instance;
	}

	/**
	 * Construct
	 * 
	 * @since 0.0.0
	 */
	public function __construct() {
		// Add your custom cron jobs here
		$this->create_cron_job( 'custom_job_1', 30, 'custom_job_callback' ); // Runs every 5 minutes
		$this->create_cron_job( 'custom_job_2', 300, 'custom_job_callback_2' ); // Runs every 10 minutes

		register_deactivation_hook( __FILE__, array( $this, 'deactivate' ) );
	}

	/**
	 * Create a custom cron job
	 * 
	 * @param string $hook The unique hook name for this job.
	 * @param int $interval The interval in seconds.
	 * @param string $callback The name of the callback method.
	 */
	public function create_cron_job( $hook, $interval, $callback ) {
		// Add a filter for custom schedule with the provided interval and $hook
		add_filter( 'cron_schedules', function ($schedules) use ($hook, $interval) {
			$schedules[ $hook ] = array(
				'interval' => $interval,
				'display'  => __( 'Custom Schedule', 'textdomain' )
			);
			return $schedules;
		}, 10, 1 );

		if ( ! wp_next_scheduled( $hook ) ) {
			wp_schedule_event( time(), $hook, $hook ); // Use $hook for the schedule name
		}

		add_action( $hook, array( $this, $callback ) );
	}

	/**
	 * Callback for your custom cron job
	 */
	public function custom_job_callback() {
		// Your custom job logic here for task 1
		$time = current_datetime();
		$time = $time->format( 'H-i-s' );
		$file = WP_CONTENT_DIR . '/test-' . $time . '.txt';

		if ( ! file_exists( $file ) ) {
			$fp = fopen( $file, 'w' );
			fwrite( $fp, 'test' );
			fclose( $fp );
		}
	}

	/**
	 * Callback for the second custom cron job
	 */
	public function custom_job_callback_2() {
		// Your custom job logic here for task 2
		// Implement the logic for your second task here
		$time = current_datetime();
		$time = $time->format( 'H-i-s' );
		$file = WP_CONTENT_DIR . '/test-' . $time . '--2.txt';

		if ( ! file_exists( $file ) ) {
			$fp = fopen( $file, 'w' );
			fwrite( $fp, 'test' );
			fclose( $fp );
		}
	}

	/**
	 * Add a custom schedule
	 * 
	 * @param array $schedules The existing schedules.
	 * @param int $interval The interval in seconds.
	 * @return array The modified schedules.
	 */
	public function add_custom_schedule( $schedules ) {
		print_r( $schedules );
		$schedules['custom'] = array(
			'interval' => 30, // Change the interval as needed
			'display'  => __( 'Custom Schedule', 'textdomain' )
		);

		return $schedules;
	}

	/**
	 * Deactivate and unschedule all custom cron jobs
	 */
	public function deactivate() {
		$custom_jobs = array( 'custom_job_1', 'custom_job_2' ); // Add your custom job names

		foreach ( $custom_jobs as $hook ) {
			$timestamp = wp_next_scheduled( $hook );
			wp_unschedule_event( $timestamp, $hook );
		}
	}
}

if ( class_exists( 'Corn' ) ) {
	Corn::get_instance();
}
```
