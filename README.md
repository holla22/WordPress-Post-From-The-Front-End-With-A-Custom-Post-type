# WordPress: Post From The Front End With A Custom Post type

## This is an old tutorial I’ve written back in 2012 and not on my website anymore. Saving it here because a lot of people found it useful.

<a class="bmc-button" target="_blank" href="https://www.buymeacoffee.com/z33man"><img src="https://cdn.buymeacoffee.com/buttons/default-orange.png" alt="Buy Me A Coffee" height="41" width="174"></a>

### Create the custom post type.

Today I want to show you how to create a custom post type for wordpress and then write code to post directly to the custom post type from the front end of your website.

First thing we need to do is create a function called ty_post_type_init(). We will then add the custom post type code as follows.

**First we need to create the labels for the post type.**

	$labels = array(
		'name' => _x('Custom Posts', 'the custom posts', 'your_text_domain'),
		'singular_name' => _x('Custom Post', 'the custom post', 'your_text_domain'),
		'add_new' => _x('Add New', 'custom-post', 'your_text_domain'),
		'add_new_item' => __('Add New Custom Post', 'your_text_domain'),
		'edit_item' => __('Edit Custom Post', 'your_text_domain'),
		'new_item' => __('New Custom Post', 'your_text_domain'),
		'all_items' => __('All Custom Posts', 'your_text_domain'),
		'view_item' => __('View Custom Post', 'your_text_domain'),
		'search_items' => __('Search Custom Post', 'your_text_domain'),
		'not_found' =>  __('No custom posts found', 'your_text_domain'),
		'not_found_in_trash' => __('No custom posts found in Trash', 'your_text_domain'),
		'parent_item_colon' => '',
		'menu_name' => __('Custom Posts', 'your_text_domain')
	);
  
The next step is to add the arguments code; this is where we control certain aspects of the custom post type. For example you can add support for the title, editor, categories, thumbnails and more. You can also set things like the slug for the URL and the menu position.

**Here is the arguments code.**

	$args = array(
		'labels' => $labels,
		'public' => true,
		'publicly_queryable' => true,
		'show_ui' => true,
		'show_in_menu' => true,
		'query_var' => true,
		'rewrite' => array( 'slug' => _x( 'custom-post', 'URL slug', 'your_text_domain' ) ),
		'capability_type' => 'post',
		'has_archive' => true,
		'hierarchical' => false,
		'menu_position' => null,
		'supports' => array( 'title', 'editor', 'author', 'thumbnail', 'excerpt', 'comments' )
	);
  
**Next we create the custom post type with the following function.**


	register_post_type('custom_posts', $args);

**Now all that’s left for this part is to add an action to register the post on init.**

	add_action( 'init', 'ty_post_type_init' );
  

**This is what the code looks like so far.**

	// register post type for custom posts
	function ty_post_type_init() {
		$labels = array(
		  'name' => _x('Custom Posts', 'the custom posts', 'your_text_domain'),
		  'singular_name' => _x('Custom Post', 'the custom post', 'your_text_domain'),
		  'add_new' => _x('Add New', 'custom-post', 'your_text_domain'),
		  'add_new_item' => __('Add New Custom Post', 'your_text_domain'),
		  'edit_item' => __('Edit Custom Post', 'your_text_domain'),
		  'new_item' => __('New Custom Post', 'your_text_domain'),
		  'all_items' => __('All Custom Posts', 'your_text_domain'),
		  'view_item' => __('View Custom Post', 'your_text_domain'),
		  'search_items' => __('Search Custom Post', 'your_text_domain'),
		  'not_found' =>  __('No custom posts found', 'your_text_domain'),
		  'not_found_in_trash' => __('No custom posts found in Trash', 'your_text_domain'),
		  'parent_item_colon' => '',
		  'menu_name' => __('Custom Posts', 'your_text_domain')

		);
		$args = array(
		  'labels' => $labels,
		  'public' => true,
		  'publicly_queryable' => true,
		  'show_ui' => true,
		  'show_in_menu' => true,
		  'query_var' => true,
		  'rewrite' => array( 'slug' => _x( 'custom-post', 'URL slug', 'your_text_domain' ) ),
		  'capability_type' => 'post',
		  'has_archive' => true,
		  'hierarchical' => false,
		  'menu_position' => null,
		  'supports' => array( 'title', 'editor', 'author', 'thumbnail', 'excerpt', 'comments' )
		);

		register_post_type('custom_posts', $args);
	}

	add_action( 'init', 'ty_post_type_init' );
  
### Create the form for the front end posting.

**We need to create the form here to be able to post in the front end. We will be using this in our template files or as a short code, depending on the style of coding.**

	<form id="custom-post-type" name="custom-post-type" method="post" action="">

		<p><label for="title">Post Title</label><br />

		<input type="text" id="title" value="" tabindex="1" size="20" name="title" />

		</p>

		<p><label for="description">Post Description</label><br />

		<textarea id="description" tabindex="3" name="description" cols="50" rows="6"></textarea>

		</p>

		<p><?php wp_dropdown_categories( 'show_option_none=Category&tab_index=4&taxonomy=category' ); ?></p>

		<p><label for="post_tags">Post Tags</label>

		<input type="text" value="" tabindex="5" size="16" name="post-tags" id="post-tags" /></p>

		<p align="right"><input type="submit" value="Publish" tabindex="6" id="submit" name="submit" /></p>

		<input type="hidden" name="post-type" id="post-type" value="custom_posts" />

		<input type="hidden" name="action" value="custom_posts" />

		<?php wp_nonce_field( 'name_of_my_action','name_of_nonce_field' ); ?>

	</form>
  
  
You will notice the wp_dropdown_categories() function. This is used to be able to select categories in the front end via a dropdown menu.

Next we need to add a nonce field; this will be used for security checks so that we know the posted data came from our form. We do this by using the function wp_nonce_field(). You can read more about this here http://codex.wordpress.org/Function_Reference/wp_nonce_field

**Next I’m just checking if the the form was posted, if it was run the function ty_save_post_data().**

	if($_POST){
		ty_save_post_data();
	}
  
**Our complete code for this function looks like this.**

	function ty_front_end_form() {

	?>
		<form id="custom-post-type" name="custom-post-type" method="post" action="">

		<p><label for="title">Post Title</label><br />

		<input type="text" id="title" value="" tabindex="1" size="20" name="title" />

		</p>

		<p><label for="description">Post Description</label><br />

		<textarea id="description" tabindex="3" name="description" cols="50" rows="6"></textarea>

		</p>

		<p><?php wp_dropdown_categories( 'show_option_none=Category&tab_index=4&taxonomy=category' ); ?></p>

		<p><label for="post_tags">Post Tags</label>

		<input type="text" value="" tabindex="5" size="16" name="post-tags" id="post-tags" /></p>

		<p align="right"><input type="submit" value="Publish" tabindex="6" id="submit" name="submit" /></p>

		<input type="hidden" name="post-type" id="post-type" value="custom_posts" />

		<input type="hidden" name="action" value="custom_posts" />

		<?php wp_nonce_field( 'name_of_my_action','name_of_nonce_field' ); ?>

		</form>
		<?php

		if($_POST){
			ty_save_post_data();
		}

	}
  
  add_shortcode('custom-post','ty_front_end_form');
  
Now we need to add a function with code to handle the posted form data and then create the new post for us.

**The first code for our function looks like this.**


	if ( empty($_POST) || !wp_verify_nonce($_POST['name_of_nonce_field'],'name_of_my_action') )
	{
		print 'Sorry, your nonce did not verify.';
		exit;

	}
  
In the code above we check for an empty post variable and check that our nonce is correct. If it fails we give them a failure message.

**Next we do some really minor form validation just to check that the fields are set before creating the post. We don’t want those fields really to be empty especially the title and description part.**

	// Do some minor form validation to make sure there is content
	if (isset ($_POST['title'])) {
		$title =  $_POST['title'];
	} else {
		echo 'Please enter a title';
		exit;
	}
	if (isset ($_POST['description'])) {
		$description = $_POST['description'];
	} else {
		echo 'Please enter the content';
		exit;
	}

	if(isset($_POST['post_tags'])){
		$tags = $_POST['post_tags'];
	}else{
		$tags = "";
	}
  
**Next we create an array with the necessary types for the post to be created.**


	// Add the content of the form to $post as an array
	$post = array(
		'post_title' => wp_strip_all_tags( $title ),
		'post_content' => $description,
		'post_category' => $_POST['cat'],  // Usable for custom taxonomies too
		'tags_input' => $tags,
		'post_status' => 'publish',            // Choose: publish, preview, future, etc.
		'post_type' => $_POST['post-type']  // Use a custom post type if you want to
	);
  
You will see above for the title part we are striping all tags. We don’t want html or any JavaScript tags in the title.

**We then just use the wp_insert_post() function to create the actual post.**


	wp_insert_post($post);  // http://codex.wordpress.org/Function_Reference/wp_insert_post
  
**All that’s left is to set the location, in our case the home URL and then redirect there when we are done with the post.**

	$location = home_url(); // redirect location, should be login page

	echo "<meta http-equiv='refresh' content='0;url=$location' />"; 
	exit;
  
**Here is what the completed function looks like now.**

	function ty_save_post_data() {

		if ( empty($_POST) || !wp_verify_nonce($_POST['name_of_nonce_field'],'name_of_my_action') )
		{
		  print 'Sorry, your nonce did not verify.';
		  exit;

		}else{

		// Do some minor form validation to make sure there is content
		if (isset ($_POST['title'])) {
		  $title =  $_POST['title'];
		} else {
		  echo 'Please enter a title';
		  exit;
		}
		if (isset ($_POST['description'])) {
		  $description = $_POST['description'];
		} else {
		  echo 'Please enter the content';
		  exit;
		}

		if(isset($_POST['post_tags'])){
		  $tags = $_POST['post_tags'];
		}else{
		  $tags = "";
		}

		// Add the content of the form to $post as an array
		$post = array(
		  'post_title' => wp_strip_all_tags( $title ),
		  'post_content' => $description,
		  'post_category' => $_POST['cat'],  // Usable for custom taxonomies too
		  'tags_input' => $tags,
		  'post_status' => 'publish',            // Choose: publish, preview, future, etc.
		  'post_type' => $_POST['post-type']  // Use a custom post type if you want to
		);
		wp_insert_post($post);  // http://codex.wordpress.org/Function_Reference/wp_insert_post

		  $location = home_url(); // redirect location, should be login page

		  echo "<meta http-equiv='refresh' content='0;url=$location' />"; 
		  exit;
		} // end IF

	}

Please Like the article and look out for more awesome tutorials coming soon. :)
