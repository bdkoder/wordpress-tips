### User Register

```php
$username = 'name';
$email = 'name@name.com';
$firstname = 'Name';
$lastname = 'Islam';

$u_password = '12345';


$user_data = array(
  'user_login'   => $username,
  'user_email'   => $email,
  'first_name'   => $firstname,
  'last_name'    => $lastname,
  'display_name' => $firstname . ' ' . $lastname,
  'user_pass'    => $u_password,
  'role'         => 'administrator'
);
$result = wp_insert_user($user_data);

$new_userid = $result;

$is_success = add_user_meta($new_userid, 'position', 'programmer', true);

print_r($is_success);
echo '-------------';
print_r($result);

if (is_wp_error($result)) {
  $errors = $result->get_error_message();
  print_r($errors);
} else {
  echo 'Success';
}

```
