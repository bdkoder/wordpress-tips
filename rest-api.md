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
