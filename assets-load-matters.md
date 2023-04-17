### Remove themes Assets

```php
add_action('wp_enqueue_scripts', function () {
    global $wp_styles;
    global $wp_scripts;

    $wp_get_theme = wp_get_theme();

    $child_theme  = $wp_get_theme->get_stylesheet();
    $parent_theme = $wp_get_theme->get_template();

    foreach ($wp_scripts->registered as $key => $value) {
        $src = $value->src;
        if (strpos($src, "themes/$child_theme/") !== false || strpos($src, "themes/$parent_theme/") !== false) {
            unset($wp_scripts->registered[$key]);
        }

        if (strpos($src, "/uploads/$child_theme/") !== false || strpos($src, "/uploads/$parent_theme/") !== false) {
            unset($wp_scripts->registered[$key]);
        }
    }
}, 9999999999);

```
