# ELEMENTOR REGISTRATION FORM
How to create a WP plugin that adds WP registration functionality into an Elementor Form.

1. First Create Your Plugin Metadata <br>

<?php<br> 
/*<br>
 * Plugin Name: Elementor Form User Registration<br>
 * Description: Creates a new user using an Elementor Form<br>
 * Version: 0.9<br>
 * Author: Zach Desiatnyk<br>
 * Author URI: http://www.zachdesiatnyk.com<br>
 */ <br><br>
 
2. Once Complete, move onto your plugin. Here is what I created but here is the reference to the official a[href]:after { content : "" } Create your parameters which will consist of Elementor's method to create new users and the name of your function (which we are about to create). <br><br>

add_action( 'elementor_pro/forms/new_record', 'choice_elementor_form_user_registration', 10, 2);<br><br>

3. Create your function. Make sure your function parameters match Elementors, which are $record and $handler, which are pretty obvious to see what they do. Also, make sure your get_form_settings **MATCHES** the name of your elementor form.<br><br>

function choice_elementor_form_user_registration ($record, $handler) {<br>
    $form_name = $record->get_form_settings( 'form_name' );<br>

    if ( 'REGISTRATION_FORM' !== $form_name ) {<br>
        return;<br>
    }<br><br>
    
4. All we are doing here is creating a variable that will grab the data from your form fields and then transfer that data into Elementor's formatted data for record keeping. You then create the variables for all your form fields, which must match the label of your elementor form fields. Once this is complete, the $user varaible will create a new user with the data provided from yuor form fields, that have been sanitzed using WP's sanitize function.<br><br>

    $form_data = $record->get_formatted_data();<br>
     $username = sanitize_text_field($form_data['username']);<br>
    $password = sanitize_text_field($form_data['password']);<br>
    $email = sanitize_text_field($form_data['email']);<br>
    $user = wp_create_user($username, $password, $email);<br><br>

  5. Simple error handling message.<br><br>
  
    if (is_wp_error($user)){ <br>
        $ajax_handler->add_error_message("Cannot Create User: ".$user->get_error_message());<br>
        $ajax_handler->is_success = false;<br>
        return;<br>
    }<br><br>
    
  6. This just inputs the First Name's and Last Names's into WP's User Creation process. Its not necessary.<br><br>

    $fName = sanitize_text_field($form_data["firstname"]);<br>
    $lName = sanitize_text_field($form_data["lastname"]);<br>
    wp_update_user(array("ID"=>$user,"first_name"=>$fName,"last_name"=>$lName));<br>
};
