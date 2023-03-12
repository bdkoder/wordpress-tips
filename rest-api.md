### Important

```php
$url_request = wp_remote_request( $url );
$data_body = wp_remote_retrieve_body( $url_request );
$data = json_decode($data_body);
```


### Insert data into WordPress database table from a custom rest api 

```php
function newsletter_signup(WP_REST_Request $request) {
  global $wpdb;
  $table_name = $wpdb->prefix . 'options';
  $parameters = $request->get_params();
  $email = $parameters['email'];

     if($email) {
      $insert = $wpdb->insert($table_name, array(
        "option_name" => 'client_email_id' ,
        "option_value" => $email
        ));
    if($insert) {
       return rest_ensure_response( 'You are registered' );
      } else {
      return rest_ensure_response( 'Please complete form' );
     }
     }
}

 add_action( 'rest_api_init', 'register_api_route' );
function register_api_route() {
  
      register_rest_route( 'wp/v2', 'newsletter/signup', array(
        'methods' => 'POST',
        'callback' => 'newsletter_signup',
        )
      );
           
}
```

### API key authentication
By default, the WP REST API does not support API key authentication, but you can add this functionality to your WordPress site by installing and configuring a plugin that adds API key support. Once you have installed the plugin, you can use the following steps to verify WP REST API authentication using an API key:

Generate an API Key: You can generate an API key in the WordPress admin panel. Navigate to Users -> Your Profile -> API Key, and then click on the Generate API Key button. This will create a new API key that you can use to authenticate your requests.

Include the API Key in Your Requests: To authenticate your requests, you will need to include the API key in the headers of your HTTP requests. The exact format of the headers will depend on the plugin that you are using, but it will typically look something like this:

X-API-KEY: {your-api-key}

Verify the API Key: In your plugin, you will need to write code that verifies the API key in the headers of the HTTP requests. The exact code that you write will depend on the plugin that you are using, but it will typically involve checking the API key against a list of valid API keys that are stored in your WordPress database.
For example, if you are using the WP API Key Authentication plugin, you can verify the API key in your code using the following function:

```php
function my_authorized_callback( $request ) {
    $api_key = $request->get_header( 'X-API-KEY' );
    $valid_keys = get_option( 'wp_api_key_authentication_keys' );

    if ( in_array( $api_key, $valid_keys ) ) {
        // Your code here
    } else {
        return new WP_Error( 'rest_forbidden', __( 'Invalid API key' ), array( 'status' => 401 ) );
    }
}
```
This function checks the API key in the headers of the HTTP request against the list of valid API keys that are stored in the WordPress database. If the API key is valid, it executes your code. If the API key is invalid, it returns an error.

These are the basic steps to verify WP REST API authentication using an API key. The exact details of how you implement this functionality will depend on the plugin that you are using and the specific requirements of your application.

### Here's an example of how to make a REST API request with an API key in WordPress using PHP:

```php
$api_key = 'your-api-key';
$url = 'https://your-wordpress-site.com/wp-json/wp/v2/posts';

$headers = array(
    'X-API-KEY' => $api_key
);

$args = array(
    'headers' => $headers
);

$response = wp_remote_get( $url, $args );

if ( is_wp_error( $response ) ) {
    // Handle error
} else {
    $body = wp_remote_retrieve_body( $response );
    $data = json_decode( $body );
    // Your code here
}
```
In this example, we're using the wp_remote_get() function to make a GET request to the WP REST API endpoint for posts. We're including the API key in the headers of the request using the X-API-KEY header. If the request is successful, we retrieve the response body using wp_remote_retrieve_body() and parse it into a JSON object using json_decode(). We can then use the data in our code as needed.

Note that the exact code that you use will depend on the plugin that you are using to add API key support to your WordPress site and the specific requirements of your application. This is just an example to give you an idea of how to make a REST API request with an API key in WordPress using PHP.

