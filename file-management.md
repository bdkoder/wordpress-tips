### Create File into wp-content Folder
```php
// create a file to wp-content folder name "test.json"
  $file = WP_CONTENT_DIR . '/test.txt';
  if ( ! file_exists( $file ) ) {
    $fp = fopen( $file, 'w' );
    fwrite( $fp, 'test' );
    fclose( $fp );
  }
```
