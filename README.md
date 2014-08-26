wordpress-snippets
==================

WordPress snippets (will be sorted out later).




1.  Introductie

Usefull snippets.



2.	Whut



3.	wp-config.php

3.1	Sources

http://codex.wordpress.org/Editing_wp-config.php

3.2	Snippets

3.2.1	Database login per server

Check op welke server de site draait en definieer de databasegegevens.
/**
 * MySQL database name, username and password
*/
switch ($_SERVER['SERVER_NAME']) {

	// Production
	case 'website.nl':
		define('DB_NAME',     '');
		define('DB_USER',     '');
		define('DB_PASSWORD', '');
		break;

	// Staging
	case 'test.website.com':
		define('DB_NAME',     '');
		define('DB_USER',     '');
		define('DB_PASSWORD', '');
		break;

	// Development = default
	default:
		define('DB_NAME',     '');
		define('DB_USER',     '');
		define('DB_PASSWORD', '');
		break;
}

3.2.2	Relatieve site-root URL's

Overschrijf de URL's die in de database opgeslagen worden, zodat WordPress op elke server kan draaien zonder de gehele database te moeten updaten.
/**
 * Set portable and relative site-root URLs
*/
define('WP_SITEURL', 'http://' . $_SERVER['SERVER_NAME'] . '/wordpress');
define('WP_HOME',    'http://' . $_SERVER['SERVER_NAME'] . '');

3.2.3	Beperkt het aantal revisies tot 10

Zonder deze snippet zal WordPress oneindig veel revisies opslaan, waardoor de database vol en traag raakt.
/**
 * Limit the total of post/page revisions to 10
*/
define('WP_POST_REVISIONS', 10);

3.2.4	Taal

Zet de taal op Nederlands. Dit zou al goed moeten staan wanneer de Nederlandse versie van WordPress is geïnstalleerd.
/**
 * WordPress Localized Language, defaults to English
*/
define('WPLANG', 'nl_NL');

3.2.5	Verberg Contact Form 7 admin menu-item

De plugin Contact Form 7 plaatst standaard een menu-item in het admingedeelte, zichtbaar voor elke (ingelogde) gebruiker. Deze snippet maakt het menu-item alleen voor administrators zichtbaar.
/**
 * Contact Form 7 admin menu item only visible for administrators
*/
define('WPCF7_ADMIN_READ_CAPABILITY', 'manage_options');
define('WPCF7_ADMIN_READ_WRITE_CAPABILITY', 'manage_options');



4.	functions.php

4.1	Bronnen

http://codex.wordpress.org/Functions_File_Explained

4.2	Snippets

N.B. Deze snippets worden in principe in een plugin geplaatst en komen derhalve niet in functions.php te staan.

4.2.1	Reorganiseer de admin menu-items

De volgorde van menu-items in het admingedeelte is standaard niet erg logisch. Via deze snippet is de volgorde te bepalen.
/**
 * Reorder admin menu items
*/
add_filter('custom_menu_order', create_function('', 'return true;'));
add_filter('menu_order', 'riesma_menu_order_filter');
function riesma_menu_order_filter($menu) {
	$ordered_menu = array(
		'index.php',
			'separator1',
		'edit.php?post_type=page',
		'edit.php',
		//'edit.php?post_type=custom_type', // Bones default
			'separator2',
		'upload.php',
		'link-manager.php',
		'edit-comments.php',
			'separator-last',
		'themes.php',
		'plugins.php',
		'users.php',
		'tools.php',
		'options-general.php'
	);
	array_splice($menu, 1, 0, $ordered_menu);
	return array_unique($menu);
}

4.2.2	Verwijder admin menu-items

Niet elke gebruiker hoeft alle menu-items in het admingedeelte te zien. Ongebruikte items kunnen zo verwijderd worden. Standaard wordt er verschil gemaakt tussen gebruikers met admin- en niet-adminrechten.
/**
 * Remove admin menu items
*/
add_action('admin_menu', 'riesma_remove_menu_pages');
function riesma_remove_menu_pages() {

	/* When logged in as admin. */
	//if (current_user_can('administrator')) {
	if (is_admin()) {
		//remove_menu_page('edit.php?post_type=page');
		//remove_menu_page('edit.php');
		//remove_menu_page('edit.php?post_type=custom_type'); // Bones default
		//remove_menu_page('edit-comments.php');
		//remove_menu_page('upload.php');
		//remove_menu_page('link-manager.php');
		//remove_menu_page('themes.php');
		//remove_menu_page('plugins.php');
		//remove_menu_page('users.php');
		//remove_menu_page('tools.php');
		//remove_menu_page('options-general.php');
	}

	/* When not logged in as admin. */
	else {
		//remove_menu_page('edit.php?post_type=page');
		//remove_menu_page('edit.php');
		remove_menu_page('edit.php?post_type=custom_type'); // Bones default
		remove_menu_page('edit-comments.php');
		//remove_menu_page('upload.php');
		remove_menu_page('link-manager.php');
		//remove_menu_page('profile.php');
		remove_menu_page('tools.php');
	}
}

4.2.3	Pagina's (Pages) met een samenvatting

Standaard hebben alleen Posts een samenvatting, dit geeft pagina's ook die functionaliteit.
/**
 * Add excerpt to pages
*/
add_action('init', 'riesma_add_excerpts_to_pages');
function riesma_add_excerpts_to_pages() {
	add_post_type_support('page', 'excerpt');
}

4.2.4	Beperk het aantal woorden in een samenvatting

Beprek het aantal woorden tot bijvoorbeeld 20 in plaats van de standaard 55.
/**
 * Limit excerpt to 20 words instead of default 55
*/
add_filter('excerpt_length', 'riesma_excerpt_length', 999);
function riesma_excerpt_length($length) {
	return 20;
}

4.2.5	Shortcode om content alleen weer te geven voor ingelogde gebruikers

Verberg bepaalde content voor niet-geregistreerde en ingelogde gebruikers.
/**
 * Shortcode for showing content only to logged in user
*/
add_shortcode('loggedin', 'riesma_loggedin');
function riesma_loggedin($atts, $content = null) {
	if (is_user_logged_in()) return '<p>'.$content.'</p>';
	else return;
}



5.	Templatebestanden

5.1	Bronnen

http://codex.wordpress.org/Templates

5.2	Snippets

Deze snippets bestaan uit vaak twee typen: één returnt de waarde (bijv. get_bloginfo()), de ander echo't deze direct (bijv. bloginfo()). Als van een functie beide versie bestaan, staan deze erbij.

5.2.1	Site titel

bloginfo('name')

get_bloginfo('name')

5.2.2	Site slogan/omschrijving

bloginfo('description')

get_bloginfo('description')

5.2.3	Home URI

home_url($path: '/')

get_home_url($path: '/')

5.2.4	HTML title inclusief titel van huidige pagina

global $page, $paged;
wp_title(' - ', true, 'right');
bloginfo('name');
$site_description = get_bloginfo('description', 'display');
if ($site_description && (is_home() || is_front_page())) {
	echo " - $site_description";
}
if ($paged >= 2 || $page >= 2) {
	echo ' - ' . sprintf(__('Page %s', 'bones'), max($paged, $page));
}

5.2.5	Themadirectory URI

get_stylesheet_directory_uri()

5.2.6	Page URI

get_page_uri($page_id)

get_page_by_title($page_title, $output: 'OBJECT', $post_type: 'page')

5.2.7	Gegevens van (1) specifieke post

$post_id = 123;
$post = get_post($post_id);

5.2.8	Gegevens van (10) posts

$posts = get_posts(array(
	'post_type'        => 'post',
	'numberposts'      => 10,
	'offset'           => 0,
	'category'         => ,
	'orderby'          => 'post_date',
	'order'            => 'DESC',
	'include'          => ,
	'exclude'          => ,
	'meta_key'         => ,
	'meta_value'       => ,
	'post_mime_type'   => ,
	'post_parent'      => ,
	'post_status'      => 'publish',
	'suppress_filters' => true
));

foreach ($posts as $post) : setup_postdata($post);

	the_title();
	the_date();
	the_excerpt();
	the_content();
	the_post_thumbnail();

endforeach;

5.2.9	Alle categoriëen van de Posts

$categories = get_categories(array(
	'type'         => 'post',
	'child_of'     => 0,
	'parent'       => '',
	'orderby'      => 'name',
	'order'        => 'ASC',
	'hide_empty'   => 1,
	'hierarchical' => 1,
	'exclude'      => '',
	'include'      => '',
	'number'       => '',
	'taxonomy'     => 'category',
	'pad_counts'   => false
));

5.2.10	Posts (featured) afbeelding

	if (has_post_thumbnail()) {
		the_post_thumbnail($size, $attr);

		// Set in wp-admin:
		the_post_thumbnail('thumbnail');
		the_post_thumbnail('medium');
		the_post_thumbnail('large');
		the_post_thumbnail('full');
		the_post_thumbnail(160,90);
	}

5.2.11	Voeg afbeeldingsformaten toe

	add_image_size($name, $width, $height, $crop);

5.2.12	Attachments (afbeeldingen)

	$size = 'thumbnail', 'full', 'large', 'medium' of 'custom'.
	wp_get_attachment_image($attachment->ID, $size);

5.2.12 Update image sizes

	update_option( 'thumbnail_size_w', 0 );
	update_option( 'thumbnail_size_h', 0 );
	update_option( 'medium_size_w', 0 );
	update_option( 'medium_size_h', 0 );
	update_option( 'large_size_w', 0 );
	update_option( 'large_size_h', 0 );
