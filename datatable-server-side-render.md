
### SQL Data
```sql
-- --------------------------------------------------------
-- Host:                         127.0.0.1
-- Server version:               5.7.33 - MySQL Community Server (GPL)
-- Server OS:                    Win64
-- HeidiSQL Version:             11.2.0.6213
-- --------------------------------------------------------

/*!40101 SET @OLD_CHARACTER_SET_CLIENT=@@CHARACTER_SET_CLIENT */;
/*!40101 SET NAMES utf8 */;
/*!50503 SET NAMES utf8mb4 */;
/*!40014 SET @OLD_FOREIGN_KEY_CHECKS=@@FOREIGN_KEY_CHECKS, FOREIGN_KEY_CHECKS=0 */;
/*!40101 SET @OLD_SQL_MODE=@@SQL_MODE, SQL_MODE='NO_AUTO_VALUE_ON_ZERO' */;
/*!40111 SET @OLD_SQL_NOTES=@@SQL_NOTES, SQL_NOTES=0 */;

-- Dumping structure for table element_pack_dev.wp_students
CREATE TABLE IF NOT EXISTS `wp_students` (
  `id` int(2) NOT NULL AUTO_INCREMENT,
  `name` varchar(50) CHARACTER SET utf8 NOT NULL DEFAULT '',
  `class` varchar(10) CHARACTER SET utf8 NOT NULL DEFAULT '',
  `mark` int(3) NOT NULL DEFAULT '0',
  `gender` varchar(6) CHARACTER SET utf8 NOT NULL DEFAULT 'male',
  UNIQUE KEY `id` (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=36 DEFAULT CHARSET=latin1;

-- Dumping data for table element_pack_dev.wp_students: ~35 rows (approximately)
/*!40000 ALTER TABLE `wp_students` DISABLE KEYS */;
INSERT INTO `wp_students` (`id`, `name`, `class`, `mark`, `gender`) VALUES
	(1, 'John Deo', 'Four', 75, 'female'),
	(2, 'Max Ruin', 'Three', 85, 'male'),
	(3, 'Arnold', 'Three', 55, 'male'),
	(4, 'Krish Star', 'Four', 60, 'female'),
	(5, 'John Mike', 'Four', 60, 'female'),
	(6, 'Alex John', 'Four', 55, 'male'),
	(7, 'My John Rob', 'Five', 78, 'male'),
	(8, 'Asruid', 'Five', 85, 'male'),
	(9, 'Tes Qry', 'Six', 78, 'male'),
	(10, 'Big John', 'Four', 55, 'female'),
	(11, 'Ronald', 'Six', 89, 'female'),
	(12, 'Recky', 'Six', 94, 'female'),
	(13, 'Kty', 'Seven', 88, 'female'),
	(14, 'Bigy', 'Seven', 88, 'female'),
	(15, 'Tade Row', 'Four', 88, 'male'),
	(16, 'Gimmy', 'Four', 88, 'male'),
	(17, 'Tumyu', 'Six', 54, 'male'),
	(18, 'Honny', 'Five', 75, 'male'),
	(19, 'Tinny', 'Nine', 18, 'male'),
	(20, 'Jackly', 'Nine', 65, 'female'),
	(21, 'Babby John', 'Four', 69, 'female'),
	(22, 'Reggid', 'Seven', 55, 'female'),
	(23, 'Herod', 'Eight', 79, 'male'),
	(24, 'Tiddy Now', 'Seven', 78, 'male'),
	(25, 'Giff Tow', 'Seven', 88, 'male'),
	(26, 'Crelea', 'Seven', 79, 'male'),
	(27, 'Big Nose', 'Three', 81, 'female'),
	(28, 'Rojj Base', 'Seven', 86, 'female'),
	(29, 'Tess Played', 'Seven', 55, 'male'),
	(30, 'Reppy Red', 'Six', 79, 'female'),
	(31, 'Marry Toeey', 'Four', 88, 'male'),
	(32, 'Binn Rott', 'Seven', 90, 'female'),
	(33, 'Kenn Rein', 'Six', 96, 'female'),
	(34, 'Gain Toe', 'Seven', 69, 'male'),
	(35, 'Rows Noump', 'Six', 88, 'female');
/*!40000 ALTER TABLE `wp_students` ENABLE KEYS */;

/*!40101 SET SQL_MODE=IFNULL(@OLD_SQL_MODE, '') */;
/*!40014 SET FOREIGN_KEY_CHECKS=IFNULL(@OLD_FOREIGN_KEY_CHECKS, 1) */;
/*!40101 SET CHARACTER_SET_CLIENT=@OLD_CHARACTER_SET_CLIENT */;
/*!40111 SET SQL_NOTES=IFNULL(@OLD_SQL_NOTES, 1) */;

```

### PHP Code
```php
/**
 * Data Table Server Side Processing Test
 */

// Add DataTables Resources

function add_datatables_scripts() {
	wp_register_script('datatables', 'https://cdn.datatables.net/1.10.13/js/jquery.dataTables.min.js', array('jquery'), true);
	wp_enqueue_script('datatables');
}

function add_datatables_style() {
	wp_register_style('datatables_style', 'https://cdn.datatables.net/1.10.13/css/dataTables.bootstrap.min.css');
	wp_enqueue_style('datatables_style');
}

add_action('wp_enqueue_scripts', 'add_datatables_scripts');
add_action('wp_enqueue_scripts', 'add_datatables_style');


function student_data_table() {

	ob_start(); ?>

	<table id="student-table" class="table table-striped table-hover">
		<thead>
			<tr>
				<th>Student Name</th>
				<th>Class</th>
				<th>Mark</th>
				<th>Gender</th>
			</tr>
		</thead>
	</table>

<?php return ob_get_clean();
}

add_action('wp_print_footer_scripts', function () {
?>
	<script>
		(function myFunction() {
			$ = jQuery;
			$(document).ready(function() {
				$('#student-table').dataTable({
					"processing": true,
					"serverSide": true,
					"pageLength": 5,
					"ajax": 'http://192.168.1.111/element-pack-dev/wp-admin/admin-ajax.php?action=student_datatables'
				});
			});
		})();
	</script>
<?php
});

add_shortcode('student_data_table', 'student_data_table');

add_action('wp_ajax_student_datatables', 'datatables_server_side_callback');
add_action('wp_ajax_nopriv_student_datatables', 'datatables_server_side_callback');


function datatables_server_side_callback() {

	header("Content-Type: application/json");
	$request = $_GET;

	global $wpdb;

	$defaults = [
		'number'  => 5,
		'offset'  => 0,
		'orderby' => 'id',
		'order'   => 'ASC',
		'offset'  => $request['start'],
	];

	$args = wp_parse_args($defaults);
	$items = $wpdb->get_results(
		$wpdb->prepare(
			"SELECT * FROM {$wpdb->prefix}students
            ORDER BY {$args['orderby']} {$args['order']}
            LIMIT %d, %d",
			$args['offset'],
			$args['number']
		)
	);


	$totalData = $wpdb->get_var("SELECT COUNT(*) FROM {$wpdb->prefix}students");

	if ($totalData) {

		foreach ($items as $key => $value) {
			$nestedData = array();
			$nestedData[] = $value->name;
			$nestedData[] = $value->class;
			$nestedData[] = $value->mark;
			$nestedData[] = $value->gender;

			$data[] = $nestedData;
		}

		wp_reset_query();

		$json_data = array(
			"draw" => intval($request['draw']),
			"recordsTotal" => intval($totalData),
			"recordsFiltered" => intval($totalData),
			"data" => $data
		);

		echo json_encode($json_data);
	} else {

		$json_data = array(
			"data" => array()
		);

		echo json_encode($json_data);
	}

	wp_die();
}

```
