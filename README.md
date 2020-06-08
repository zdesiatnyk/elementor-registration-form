# ELEMENTOR REGISTRATION FORM
How to create a WP plugin that adds WP registration functionality into an Elementor Form.

First Create Your Plugin Metadata <br>

<?php 
/**
 * Plugin Name: Elementor Form User Registration
 * Description: Creates a new user using an Elementor Form
 * Version: 0.9
 * Author: Zach Desiatnyk
 * Author URI: http://www.zachdesiatnyk.com
 */ <br>
 
 Once Complete, move onto your plugin. Here is what I created but here is the reference to the official [Elementor Form API](https://developers.elementor.com/forms-api/). Create your parameters which will consist of Elementor's method to create new users and the name of your function (which we are about to create).

add_action( 'elementor_pro/forms/new_record', 'choice_elementor_form_user_registration', 10, 2);

 Create your function. Make sure your function parameters match Elementors, which are $record and $handler, which are pretty obvious to see what they do. Also, make sure your get_form_settings **MATCHES** the name of your elementor form.

function choice_elementor_form_user_registration ($record, $handler) {
    $form_name = $record->get_form_settings( 'form_name' );

    if ( 'REGISTRATION_FORM' !== $form_name ) {
        return;
    }
    
All we are doing here is creating a variable that will grab the data from your form fields and then transfer that data into Elementor's formatted data for record keeping. You then create the variables for all your form fields, which must match the label of your elementor form fields. Once this is complete, the $user varaible will create a new user with the data provided from yuor form fields, that have been sanitzed using WP's sanitize function.

    $form_data = $record->get_formatted_data();
    $username = $form_data(sanitize_text_field(['username']));
    $password = $form_data(sanitize_text_field(['password']));
    $email = $form_data(sanitize_text_field(['email']));
    $user = wp_create_user($username, $password, $email);

  Simple error handling message.
  
    if (is_wp_error($user)){ 
        $ajax_handler->add_error_message("Cannot Create User: ".$user->get_error_message());
        $ajax_handler->is_success = false;
        return;
    }
    
  This just inputs the First Name's and Last Names's into WP's User Creation process. Its not necessary.

    $fName = $form_data(sanitize_text_field(["firstname"]));
    $lName = $form_data(sanitize_text_field(["lastname"]));
    wp_update_user(array("ID"=>$user,"first_name"=>$fName,"last_name"=>$lName));
};
