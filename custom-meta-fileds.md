### Custom Meta Fields

```php
/**
 * Important class
 * Generate Custom Post Type
 * Generate Custom Meta Boxes
 */

class Techfyd_Custom_Meta_Box {
	private $screen = array(
		'page',
	);

	public $prefix = 'techfyd_meta_';

	private $meta_fields = [ 
		[ 
			'type'  => 'heading',
			'label' => 'Page Header Information',
		],
		[ 
			'type'  => 'text',
			'label' => 'Header Title',
			'id'    => 'header_title',
		],
		[ 
			'type'  => 'textarea',
			'label' => 'Header Description',
			'id'    => 'header_description',
		],
		[ 
			'type'  => 'text',
			'label' => 'Image Link',
			'id'    => 'image_link',
		],
	];

	public function __construct() {
		// custom post type
		add_action( 'add_meta_boxes', [ $this, 'add_meta_boxes' ] );
		add_action( 'save_post', [ $this, 'save_fields' ] );
	}

	public function add_meta_boxes() {
		foreach ( $this->screen as $single_screen ) {
			add_meta_box(
				'new',
				__( 'Meta Data', 'techfyd' ),
				array( $this, 'meta_box_callback' ),
				$single_screen,
				'normal',
				'default'
			);
		}
	}

	public function meta_box_callback( $post ) {
		wp_nonce_field( 'techfyd_meta_data', 'techfyd_meta_nonce' );
		$this->field_generator( $post );
	}
	public function field_generator( $post ) {
		$output = '';
		foreach ( $this->meta_fields as $meta_field ) {
			$meta_id    = isset( $meta_field['id'] ) ? $meta_field['id'] : '';
			$meta_type  = isset( $meta_field['type'] ) ? $meta_field['type'] : '';
			$label      = '<label for="' . $this->prefix . $meta_id . '">' . $meta_field['label'] . '</label>';
			$meta_value = get_post_meta( $post->ID, $this->prefix . $meta_id, true );

			if ( empty( $meta_value ) ) {
				if ( isset( $meta_field['default'] ) ) {
					$meta_value = $meta_field['default'];
				}
			}
			switch ( $meta_field['type'] ) {
				case 'checkbox':
					$input = sprintf(
						'<input %s id=" %s" name="%s" type="checkbox" value="1">',
						$meta_value === '1' ? 'checked' : '',
						$this->prefix . $meta_id,
						$this->prefix . $meta_id
					);
					break;

				case 'textarea':
					$input = sprintf(
						'<textarea style="%s" id="%s" name="%s" rows="5">%s</textarea>',
						'width: 100%',
						$this->prefix . $meta_id,
						$this->prefix . $meta_id,
						$meta_value
					);
					break;

				case 'heading':
					$input = sprintf(
						'<h3 class="fields-heading">%s</h3>',
						$meta_field['label']
					);
					break;

				case 'list':
					$input = $this->format_list( $meta_value );
					break;

				default:
					$input = sprintf(
						'<input %s id="%s" name="%s" type="%s" value="%s">',
						$meta_field['type'] !== 'color' ? 'style="width: 100%"' : '',
						$this->prefix . $meta_id,
						$this->prefix . $meta_id,
						$meta_field['type'],
						$meta_value
					);
			}
			$output .= $this->format_rows( $label, $input, $meta_type );
		}
		echo '<table class="form-table"><tbody>' . $output . '</tbody></table>';
	}

	public function format_rows( $label, $input, $meta_type ) {
		if ( $meta_type == 'heading' ) {
			return '<tr><td colspan="2">' . $input . '</td></tr>';
		} else {
			return '<tr><th>' . $label . '</th><td>' . $input . '</td></tr>';
		}
	}

	public function format_list( $meta_value ) {
		if ( empty( $meta_value ) ) {
			return 'Empty Data';
		}
		$html = '<ul>';
		foreach ( $meta_value as $value ) {
			$html .= '<li>' . $value['Name'] . '</li>';
		}
		$html .= '</ul>';
		return $html;
	}

	public function save_fields( $post_id ) {
		if ( ! isset( $_POST['techfyd_meta_nonce'] ) )
			return $post_id;
		$nonce = $_POST['techfyd_meta_nonce'];
		if ( ! wp_verify_nonce( $nonce, 'techfyd_meta_data' ) )
			return $post_id;
		if ( defined( 'DOING_AUTOSAVE' ) && DOING_AUTOSAVE )
			return $post_id;
		foreach ( $this->meta_fields as $meta_field ) {
			if ( isset( $_POST[ $this->prefix . $meta_field['id'] ] ) ) {
				switch ( $meta_field['type'] ) {
					case 'email':
						$_POST[ $this->prefix . $meta_field['id'] ] = sanitize_email( $_POST[ $this->prefix . $meta_field['id'] ] );
						break;
					case 'text':
						$_POST[ $this->prefix . $meta_field['id'] ] = sanitize_text_field( $_POST[ $this->prefix . $meta_field['id'] ] );
						break;
					case 'textarea':
						$_POST[ $this->prefix . $meta_field['id'] ] = sanitize_text_field( $_POST[ $this->prefix . $meta_field['id'] ] );
						break;
					case 'number':
						$_POST[ $this->prefix . $meta_field['id'] ] = sanitize_text_field( $_POST[ $this->prefix . $meta_field['id'] ] );
						break;
					case 'list':
						$_POST[ $this->prefix . $meta_field['id'] ] = rest_sanitize_array( $_POST[ $this->prefix . $meta_field['id'] ] );
						break;
					default:
						$_POST[ $this->prefix . $meta_field['id'] ] = sanitize_text_field( $_POST[ $this->prefix . $meta_field['id'] ] );
						break;
				}
				update_post_meta( $post_id, $this->prefix . $meta_field['id'], $_POST[ $this->prefix . $meta_field['id'] ] );
			} else if ( $meta_field['type'] === 'checkbox' ) {
				update_post_meta( $post_id, $this->prefix . $meta_field['id'], '0' );
			}
		}
	}
}

if ( class_exists( 'Techfyd_Custom_Meta_box' ) ) {
	new Techfyd_Custom_Meta_box;
}
```
