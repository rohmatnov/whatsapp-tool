<script setup>
import { onMounted, ref } from "vue";
import TelInput from "./components/TelInput.vue";
import SwitchMode from "./components/SwitchMode.vue";

const phone = ref("");
const message = ref("");

onMounted(() => {
  Toastify.multiple = false;
});

const openWhatsapp = () => {
  if (!validate()) return;

  let numberWithoutPlus = phone.value.number.substr(1);
  let href = `https://wa.me/${numberWithoutPlus}`;
  if (message.value.length) {
    href += `?text=${encodeURIComponent(message.value)}`;
  }

  Object.assign(document.createElement("a"), {
    target: "_blank",
    href,
  }).click();

  phone.value = "";
  message.value = "";
};

const validate = () => {
  if (!phone?.value.value) {
    toast("The phone number field is required");
    return;
  }

  if (!/^[0-9]*$/g.test(phone?.value.value)) {
    toast("The phone number field must only contain numbers");
    return;
  }

  if (!phone.value.valid) {
    toast("The phone number format is invalid");
    return;
  }

  return true;
};

const toast = (msg) => {
  let html = `<div class="bg-red-500 rounded-lg text-red-100 px-4 py-3 dark:bg-red-900 dark:text-red-300">${msg}</div>`;
  Toastify({
    text: html,
    duration: 3000,
    gravity: "bottom",
    escapeMarkup: false,
    className:
      "py-2 px-6 text-sm left-1/2 w-full sm:right-auto !-translate-x-1/2 block fixed cursor-pointer max-w-sm transition-all duration-300 ease-[cubic-bezier(0.215,0.61,0.355,1)] z-50 -bottom-32 whitespace-no-wrap text-center opacity-0",
  }).showToast();
};
</script>

<template>
  <div
    class="min-h-screen flex flex-col justify-center bg-green-50 text-slate-700 sm:py-8 sm:px-6 dark:bg-slate-800 dark:text-slate-300"
  >
    <div
      class="max-w-5xl min-h-screen sm:min-h-0 mx-auto w-full shadow-lg rounded-lg bg-white py-8 px-6 dark:bg-slate-900"
    >
      <div
        class="mb-4 border-b border-slate-200 pb-4 relative dark:border-slate-800"
      >
        <h1 class="text-2xl font-semibold mb-1">Whatsapp Tool</h1>
        <p class="text-sm text-slate-500">
          Simple tool to start whatsapp chat without saving phone number
        </p>
        <SwitchMode class="absolute -top-2 right-0" />
      </div>

      <div>
        <div class="mb-2">
          <label for="phone" class="text-sm mb-1 block"> Phone Number </label>
          <div>
            <TelInput v-model="phone" />
          </div>
        </div>

        <div class="mb-2">
          <label for="greeting" class="text-sm mb-1 block">
            Greeting Message (optional)
          </label>
          <div>
            <textarea
              class="w-full py-2 px-3 border border-slate-200 rounded-lg focus:outline-none focus:ring focus:ring-emerald-100 focus:border-emerald-300 dark:bg-slate-900 dark:border-slate-700 focus:dark:border-emerald-800 focus:dark:ring-slate-800"
              id="greeting"
              v-model="message"
            ></textarea>
          </div>
        </div>

        <div>
          <button
            class="bg-emerald-600 text-white rounded-lg py-2 px-4 flex items-center focus:outline-none hover:bg-emerald-500 active:bg-emerald-700 active:ring active:ring-emerald-100 dark:bg-emerald-900 hover:dark:bg-emerald-800 dark:text-slate-300 active:dark:ring-slate-800"
            v-on:click="openWhatsapp"
          >
            <svg
              xmlns="http://www.w3.org/2000/svg"
              width="16"
              height="16"
              fill="currentColor"
              class="bi bi-whatsapp inline-block mr-2 h-5 w-5"
              viewBox="0 0 16 16"
            >
              <path
                d="M13.601 2.326A7.854 7.854 0 0 0 7.994 0C3.627 0 .068 3.558.064 7.926c0 1.399.366 2.76 1.057 3.965L0 16l4.204-1.102a7.933 7.933 0 0 0 3.79.965h.004c4.368 0 7.926-3.558 7.93-7.93A7.898 7.898 0 0 0 13.6 2.326zM7.994 14.521a6.573 6.573 0 0 1-3.356-.92l-.24-.144-2.494.654.666-2.433-.156-.251a6.56 6.56 0 0 1-1.007-3.505c0-3.626 2.957-6.584 6.591-6.584a6.56 6.56 0 0 1 4.66 1.931 6.557 6.557 0 0 1 1.928 4.66c-.004 3.639-2.961 6.592-6.592 6.592zm3.615-4.934c-.197-.099-1.17-.578-1.353-.646-.182-.065-.315-.099-.445.099-.133.197-.513.646-.627.775-.114.133-.232.148-.43.05-.197-.1-.836-.308-1.592-.985-.59-.525-.985-1.175-1.103-1.372-.114-.198-.011-.304.088-.403.087-.088.197-.232.296-.346.1-.114.133-.198.198-.33.065-.134.034-.248-.015-.347-.05-.099-.445-1.076-.612-1.47-.16-.389-.323-.335-.445-.34-.114-.007-.247-.007-.38-.007a.729.729 0 0 0-.529.247c-.182.198-.691.677-.691 1.654 0 .977.71 1.916.81 2.049.098.133 1.394 2.132 3.383 2.992.47.205.84.326 1.129.418.475.152.904.129 1.246.08.38-.058 1.171-.48 1.338-.943.164-.464.164-.86.114-.943-.049-.084-.182-.133-.38-.232z"
              />
            </svg>
            Open Whatsapp
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<style lang="postcss">
.toastify.on {
  @apply opacity-100;
}
</style>
