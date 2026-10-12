<script setup>
import { ref, onMounted, onBeforeUnmount } from 'vue';
import { Notyf } from 'notyf';
import 'notyf/notyf.min.css';

const notyf = new Notyf();

// Config Keys
const WEB3FORMS_ACCESS_KEY = "5fd65a97-edbb-4914-9801-7157cb7c2184";
const SITE_KEY = '6LcNqustAAAAAHD0MC9AuJIQ_7mk4pmopJw9SOJ4'; // Replace with your reCAPTCHA v2 site key

// Form Fields
const clientName = ref('');
const email = ref('');
const contactNumber = ref('');
const concern = ref('Select Transaction');
const message = ref('');

// UI & reCAPTCHA States
const isLoading = ref(false);
const recaptchaContainer = ref(null);
const recaptchaWidgetId = ref(null);
const recaptchaToken = ref('');

// reCAPTCHA Callbacks
function onRecaptchaSuccess(token) {
  recaptchaToken.value = token;
}

function onRecaptchaExpired() {
  recaptchaToken.value = '';
}

function renderRecaptcha() {
  if (!window.grecaptcha || !recaptchaContainer.value) return;

  if (recaptchaWidgetId.value === null) {
    recaptchaWidgetId.value = window.grecaptcha.render(recaptchaContainer.value, {
      sitekey: SITE_KEY,
      size: 'normal',
      callback: onRecaptchaSuccess,
      'expired-callback': onRecaptchaExpired,
    });
  }
}

function resetRecaptcha() {
  if (recaptchaWidgetId.value !== null && window.grecaptcha) {
    window.grecaptcha.reset(recaptchaWidgetId.value);
    recaptchaToken.value = '';
  }
}

// Submit Handler
const submitForm = async () => {
  if (!recaptchaToken.value) {
    notyf.error('Please verify that you are not a robot');
    return;
  }

  isLoading.value = true;

  try {
    const response = await fetch("https://api.web3forms.com/submit", {
      method: "POST",
      headers: {
        "Content-Type": "application/json",
        Accept: "application/json"
      },
      body: JSON.stringify({
        access_key: WEB3FORMS_ACCESS_KEY,
        subject: "New Contact Form Submission",
        name: clientName.value,
        email: email.value,
        phone: contactNumber.value,
        concern: concern.value,
        message: message.value,
        "g-recaptcha-response": recaptchaToken.value
      })
    });

    const result = await response.json();

    if (result.success) {
      notyf.success("Message Sent Successfully!");
      // Clear Form Fields
      clientName.value = '';
      email.value = '';
      contactNumber.value = '';
      concern.value = 'Select Transaction';
      message.value = '';
    } else {
      notyf.error(result.message || "Failed to send message.");
    }
  } catch (error) {
    console.error(error);
    notyf.error("An error occurred. Please try again.");
  } finally {
    isLoading.value = false;
    resetRecaptcha();
  }
};

let interval = null;

onMounted(() => {
  interval = setInterval(() => {
    if (window.grecaptcha && window.grecaptcha.render) {
      renderRecaptcha();
      clearInterval(interval);
    }
  }, 100);
});

onBeforeUnmount(() => {
  if (interval) clearInterval(interval);
});
</script>

<template>
  <!-- Start of Contact Form section -->
  <div class="row p-3 text-center" id="contact">
    <h3 class="text-dark">Contact Me</h3>
    <div class="col-md-4 mt-md-3 mx-auto" id="form-col">
      <form class="bg-dark p-3 rounded" @submit.prevent="submitForm">
        
        <!-- Client Name -->
        <div class="mb-3 text-start">
          <label for="clientName" class="form-label text-white">Client's Name</label>
          <input
            type="text"
            class="form-control"
            id="clientName"
            v-model="clientName"
            required
          />
        </div>

        <!-- Email Address -->
        <div class="mb-3 text-start">
          <label for="emailInput" class="form-label text-white">Email address</label>
          <input
            type="email"
            class="form-control"
            id="emailInput"
            v-model="email"
            required
          />
        </div>

        <!-- Contact Number -->
        <div class="mb-3 text-start">
          <label for="contactNumber" class="form-label text-white">Contact Number</label>
          <input
            type="tel"
            class="form-control"
            id="contactNumber"
            v-model="contactNumber"
            required
          />
        </div>

        <!-- Concern Select -->
        <div class="mb-3 text-start">
          <label for="concernSelect" class="form-label text-white">Concern</label>
          <select
            class="form-select"
            id="concernSelect"
            v-model="concern"
            required
          >
            <option disabled value="Select Transaction">Select Transaction</option>
            <option value="Quotation">Quotation</option>
            <option value="Appointment">Appointment</option>
            <option value="Follow-up">Follow-up</option>
          </select>
        </div>

        <!-- Message Textarea -->
        <div class="mb-3 text-start">
          <label for="messageInput" class="form-label text-white">Message</label>
          <textarea
            class="form-control"
            rows="7"
            id="messageInput"
            v-model="message"
            required
          ></textarea>
        </div>

        <!-- reCAPTCHA Widget Container -->
        <div class="mb-3 d-flex justify-content-center">
          <div ref="recaptchaContainer"></div>
        </div>

        <!-- Submit Button -->
        <button
          type="submit"
          class="btn btn-info btn-lg w-100 mt-2"
          :disabled="isLoading"
        >
          {{ isLoading ? "Submitting..." : "Submit" }}
        </button>

      </form>
    </div>
  </div>
</template>