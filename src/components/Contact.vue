<template>
    <section id="contact" class="contact-section">
        <div class="container mx-auto">
            <h2 class="section-title">Contact</h2>
            <div class="row justify-content-center">
                <div class="col-10 col-lg-6">
                    <form @submit.prevent="submitForm" id="contactForm" class="contact-form">
                        <div class="mb-3">
                            <label for="name" class="form-label">Name</label>
                            <input type="text" class="form-control" id="name" name="name" required>
                            <div class="invalid-feedback">
                                Please provide your name.
                            </div>
                        </div>
                        <div class="mb-3">
                            <label for="email" class="form-label">Email</label>
                            <input type="email" class="form-control" id="email" name="email" required>
                            <div class="invalid-feedback">
                                Please provide a valid email address.
                            </div>
                        </div>
                        <div class="mb-3">
                            <label for="message" class="form-label">Message</label>
                            <textarea class="form-control" id="message" name="message" rows="5" required></textarea>
                            <div class="invalid-feedback">
                                Please provide a message.
                            </div>
                        </div>
                        
                        <button 
                        type="submit"
                        class="btn btn-primary btn-submit"
                        :disabled="isLoading || !isVerified"
                        >
                        {{ isLoading ? "Sending..." : "Send Message" }}
                        </button>
                           
                    </form>
                    <div ref="recaptchaContainer" class="mt-3 recaptcha-wrapper"></div>
                </div>
            </div>
        </div>
    </section>
</template>

<script setup>
import { ref, onMounted, onBeforeMount } from "vue";
import { Notyf } from "notyf";
import "notyf/notyf.min.css";

const notyf = new Notyf();
const WEB3FORMS_ACCESS_KEY = "746cdf5d-515f-4e45-8b3b-f1967b2850bb";
const subject = "New Contact Form Submission";

// Initial values for form fields
const name = ref("");
const email = ref("");
const message = ref("");

const isLoading = ref(false);

const submitForm = async () => {
  if (!name.value || !email.value || !message.value) {
    notyf.error("Please fill in all fields.");
    return;
  }

  if (!recaptchaToken.value) {
    notyf.error("Please verify that you are not a robot.");
    return;
  }

  isLoading.value = true;

  try {
    const response = await fetch("https://api.web3forms.com/submit", {
      method: "POST",
      headers: {
        "Content-Type": "application/json",
        Accept: "application/json",
      },
      body: JSON.stringify({
        access_key: WEB3FORMS_ACCESS_KEY,
        subject: subject,
        name: name.value,
        email: email.value,
        message: message.value,
      }),
    });

    const result = await response.json();

    if (result.success) {
      console.log(result);
      isLoading.value = false;
      notyf.success("Message Sent!");
      // Clear form fields after successful submission
      name.value = "";
      email.value = "";
      message.value = "";
    } else {
      notyf.error("Failed to send message. Please try again later.");
    }
  } catch (error) {
    console.log(error);
    isLoading.value = false;
    notyf.error("Failed to send message. Please try again.");
  } finally {
    // Reset reCAPTCHA after submission attempt
    if (window.grecaptcha) {
      window.grecaptcha.reset();
    }
  }
};

const SITE_KEY = "6LfLsHEsAAAAADjhFx8x7ZLQ5GJq1kutubGy_Ygt";
const recaptchaContainer = ref(null);
const recaptchaWidgetId = ref(null);
const recaptchaToken = ref("");
const isVerified = ref(false);

function onRecaptchaSuccess(token) {
  recaptchaToken.value = token;
  isVerified.value = true;
}

function onRecaptchaExpired() {
  recaptchaToken.value = '';
  isVerified.value = false;
}

function renderRecaptcha() {
  if (!window.grecaptcha) {
    console.error('reCAPTCHA not loaded');
    return;
  }

  recaptchaWidgetId.value = window.grecaptcha.render(recaptchaContainer.value, {
    sitekey: SITE_KEY,
    size: 'normal', // or 'compact'
    theme: 'dark', // or 'light'
    callback: onRecaptchaSuccess,
    'expired-callback': onRecaptchaExpired,
  });
}

function resetRecaptcha() {
  if (recaptchaWidgetId.value !== null) {
    window.grecaptcha.reset(recaptchaWidgetId.value);
    recaptchaToken.value = '';
  }
}

onMounted(() => {
  const interval = setInterval(() => {
    if (window.grecaptcha && window.grecaptcha.render) {
      renderRecaptcha();
      clearInterval(interval);
    }
  }, 1000); 

  onBeforeMount(() => {
    clearInterval(interval);
  });

});


</script>