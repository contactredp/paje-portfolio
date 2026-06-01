<script setup>
	import {Notyf} from 'notyf';
	import {ref, onMounted, onBeforeUnmount} from 'vue';

	const notyf = new Notyf();

	const name = ref("");
	const email = ref("");
	const message = ref("");
	const isLoading = ref(false);

	// Web3Forms Access Key used to authenticate form submissions.
	const WEB3FORMS_ACCESS_KEY = "f95b4418-b8fa-4055-99b7-104da5746790";

	// Email subject that will appear when a form submission is received.
	const subject = "New message from Portfolio Contact Form";

	// The submitForm() function handles the contact form submission.
	const submitForm = async () => {

		// Ensure the user completes the reCAPTCHA challenge before submitting the form.
        // Check if a reCAPTCHA token exists
        // recaptchaToken.value - stores the verification token returned by Google reCAPTCHA
		if(!recaptchaToken.value) {
			notyf.error('Please verify that you are not a robot');
			// Stop the form submission process
			return;
		}

		// While the email is being sent, disable the button and change it text to "Sending..."
		isLoading.value = true;

		try {

			// fetch() API is a built-in JavaScript function used to send HTTP requests to a server.
			const response = await fetch("https://api.web3forms.com/submit", {
				method: "POST",
				headers: {
					"Content-Type": "application/json",
					// Indicates that the request accepts a JSON response.
					Accept: "application/json"
				},
				// Convert the form data into a JSON string and include the access key.
				body: JSON.stringify({
					access_key: WEB3FORMS_ACCESS_KEY,
					subject: subject,
					name: name.value,
					email: email.value,
					message: message.value
				})
			})

			// Convert the API response into a JS Object
			const result = await response.json();

				// Check if the form submission was successful.
				if (result.success) {
					console.log(result);
					isLoading.value = false;
					notyf.success("Message Sent!");
				}
		} catch (error) {
			console.log(error);
			isLoading.value = false;
			notyf.error("Failed to send message.");
		} finally {
			// Reset the reCAPTCHA widget after the submission process completes, whether the request succeeds or fails.
			resetRecaptcha();
		}

	}

	/*reCAPTCHA Integration*/

    const SITE_KEY = '6LfySActAAAAABm6WnTMJ6nIWFHIlb7uNuOaxr8_';  // Replace with your site key
    // The location where the reCAPTCHA checkbox will appear
    const recaptchaContainer = ref(null);
    // The ID of the reCAPTCHA widget after it is created
    const recaptchaWidgetId = ref(null);
    // Stores the token generated when the user completes reCAPTCHA
    const recaptchaToken = ref('');

    // Runs when the user successfully completes the reCAPTCHA challenge
    function onRecaptchaSuccess(token) {
        recaptchaToken.value = token;
    }

    // Runs when the reCAPTCHA verification expires
    // Token typically expires after about 2 minutes (120 seconds) if the form has not been submitted
    function onRecaptchaExpired() {
        recaptchaToken.value = '';
    }

    // Creates and displays the reCAPTCHA widget
    function renderRecaptcha() {
    	// Check if the Google reCAPTCHA script is available
    	// window - refers to the browser window object
        if (!window.grecaptcha) {
            console.error('reCAPTCHA not loaded');
            return;
        }

        // Creates the reCAPTCHA widget and save its ID
        // Google returns a unique widget ID after creating the reCAPTCHA widget
            // render() generates the reCAPTCHA checkbox inside recaptchaContainer.value
            // Call window.grecaptcha.render() to create the widget, then store the widget ID returned by Google in recaptchaWidgetId.value.
        recaptchaWidgetId.value = window.grecaptcha.render(recaptchaContainer.value, {
            sitekey: SITE_KEY,
            size: 'normal',
            callback: onRecaptchaSuccess,
            'expired-callback': onRecaptchaExpired,
        });
    }

    // Function the resets the reCAPTCHA widget
    function resetRecaptcha() {
        if (recaptchaWidgetId.value !== null) {
        	window.grecaptcha.reset(recaptchaWidgetId.value);
        	recaptchaToken.value = '';
        }
    }

    onMounted(() => {
    	// Check if the Google reCAPTCHA library has finished loading, since it is loaded asynchronously from index.html.
        // This makes sure that the reCAPTCHA widget is rendered only after the library is available.
        // setInterval() is a JavaScript function that repeatedly executes a block of code at a specified time interval.
    const interval = setInterval(() => {
        if (window.grecaptcha && window.grecaptcha.render) {
            renderRecaptcha();
            // Stop checking once the widget has been rendered
            clearInterval(interval);
        }
        // Check every 100 milliseconds if the Google reCAPTCHA library has loaded
    }, 100);
    
    // Run cleanup code before the component is removed
      onBeforeUnmount(() => {
      	// Stop checking once the widget has been rendered
          clearInterval(interval);
      });
  });  
</script>

<template>
	<!-- ***** Contact ***** -->
	<section class="contact" id="contact">
	  <div class="row">
	    <h2>Get in Touch</h2>
	    <div class="contact__content">

	      <form @submit.prevent="submitForm" class="contact__form">
	        <div class="contact__form-group">
	          <input v-model="name" type="text" class="contact__input" placeholder="First Name M.I. Last Name">
	        </div>
	        <div class="contact__form-group">
	          <input v-model="email" type="email" class="contact__input" placeholder="Email">
	        </div>
	        <div class="contact__form-group">
	          <textarea v-model="message" class="contact__input contact__textarea" placeholder="Message" rows="6"></textarea>
	        </div>
	        <div class="contact__bottom">
	          <div class="contact__socials">
	            <a href="https://www.linkedin.com/" target="_blank" class="contact__social-link">
	              <img src="/images/linkedin.svg" alt="LinkedIn" class="contact__social-icon">
	            </a>
	            <a href="https://about.gitlab.com/" target="_blank" class="contact__social-link">
	              <img src="/images/gitlab-logo-black-and-white.png" alt="GitLab" class="contact__social-icon">
	            </a>
	            <a href="https://github.com/" target="_blank" class="contact__social-link">
	              <img src="/images/github.svg" alt="GitHub" class="contact__social-icon">
	            </a>
	          </div>
	          <button type="submit" class="btn btn--pink contact__btn" :disabled="isLoading">{{isLoading ? "Sending..." : "Submit"}}</button>
	        </div>
			<!-- The location where the reCAPTCHA checkbox will appear -->
            <div class="d-flex justify-content-end mt-2">
            	<!-- Creates a reference to the div so the reCAPTCHA widget can be rendered into it. -->
            	<div ref="recaptchaContainer"></div>
            </div>
	      </form>

	    </div>
	  </div>
	</section>

</template>

<style scoped>
	
</style>