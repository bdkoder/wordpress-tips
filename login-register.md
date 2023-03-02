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


### custom wordpress register form for ajax
```php
function custom_register_form_ajax() {
  $username = (!empty($_POST['username'])) ? trim($_POST['username']) : '';
  $email    = (!empty($_POST['email'])) ? trim($_POST['email']) : '';
  $password = (!empty($_POST['password'])) ? trim($_POST['password']) : '';
  $password_confirm = (!empty($_POST['password_confirm'])) ? trim($_POST['password_confirm']) : '';
?>
  <form action="<?php echo esc_url($_SERVER['REQUEST_URI']); ?>" method="post">
    <div class="form-group">
      <label for="username"><?php _e('Username', 'custom'); ?> <strong>*</strong></label>
      <input type="text" name="username" id="username" class="form-control" value="<?php echo  $username; ?>" required>
    </div>
    <div class="form-group">
      <label for="email"><?php _e('Email', 'custom'); ?> <strong>*</strong></label>
      <input type="email" name="email" id="email" class="form-control" value="<?php echo  $email; ?>" required>
    </div>
    <div class="form-group">
      <label for="password"><?php _e('Password', 'custom'); ?> <strong>*</strong></label>
      <input type="password" name="password" id="password" class="form-control" value="<?php echo  $password; ?>" required>
    </div>
    <div class="form-group">
      <label for="password_confirm"><?php _e('Confirm Password', 'custom'); ?> <strong>*</strong></label>
      <input type="password" name="password_confirm" id="password_confirm" class="form-control" value="<?php echo  $password_confirm; ?>" required>
    </div>
    <div class="form-group">
      <input type="hidden" name="custom_register_nonce" value="<?php echo wp_create_nonce('custom-register-nonce'); ?>" />
      <input type="submit" name="submit" class="btn btn-primary" value="<?php _e('Register', 'custom'); ?>">
    </div>
  </form>
<?php
}
```

### custom wordpress register with ajax
```php
function custom_register_user() {
  if (isset($_POST['username']) && wp_verify_nonce($_POST['custom_register_nonce'], 'custom-register-nonce')) {
    $username = sanitize_user($_POST['username']);
    $email    = sanitize_email($_POST['email']);
    $password = esc_attr($_POST['password']);
    $password_confirm = esc_attr($_POST['password_confirm']);
    $error = array();
    if (empty($username) || empty($email) || empty($password) || empty($password_confirm)) {
      $error[] = 'Please fill all fields';
    }
    if (strlen($username) < 4) {
      $error[] = 'Username too short. At least 4 characters is required';
    }
    if (username_exists($username)) {
      $error[] = 'Username already taken';
    }
    if (!validate_username($username)) {
      $error[] = 'Invalid username';
    }
    if (!is_email($email)) {
      $error[] = 'Invalid email';
    }
    if (email_exists($email)) {
      $error[] = 'Email already taken';
    }
    if ($password !== $password_confirm) {
      $error[] = 'Password do not match';
    }
    if (empty($error)) {
      $userdata = array(
        'user_login'  =>  $username,
        'user_email'  =>  $email,
        'user_pass'   =>  $password
      );
      $user = wp_insert_user($userdata);
      echo 'Registration complete. Goto <a href="' . get_site_url() . '/login">login page</a>.';
    } else {
      foreach ($error as $error) {
        echo '<p class="error">' . $error . '</p>';
      }
    }
    die();
  }
}
```

### custom wordpress register jquery code  for ajax
```js
function custom_register_jquery() {
?>
  <script type="text/javascript">
    jQuery(document).ready(function($) {
      $('form[name="registerform"]').on('submit', function(e) {
        e.preventDefault();
        var username = $('form[name="registerform"] #username').val();
        var email = $('form[name="registerform"] #email').val();
        var password = $('form[name="registerform"] #password').val();
        var password_confirm = $('form[name="registerform"] #password_confirm').val();
        var custom_register_nonce = $('form[name="registerform"] input[name="custom_register_nonce"]').val();
        $.ajax({
          type: 'POST',
          dataType: 'json',
          url: '<?php echo admin_url('admin-ajax.php'); ?>',
          data: {
            'action': 'custom_register_user',
            'username': username,
            'email': email,
            'password': password,
            'password_confirm': password_confirm,
            'custom_register_nonce': custom_register_nonce
          },
          success: function(data) {
            $('form[name="registerform"] .error').remove();
            if (data) {
              $('form[name="registerform"]').prepend(data);
            }
          }
        });
      });
    });
  </script>
<?php
}
```

### check if user looged in
```php
function _is_user_logged_in() {
  $current_user = wp_get_current_user();
  if (0 == $current_user->ID) {
    return false;
  } else {
    return true;
  }
}

echo _is_user_logged_in();
```
