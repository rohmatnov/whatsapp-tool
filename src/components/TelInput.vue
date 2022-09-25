<script setup>
import parsePhoneNumber from "libphonenumber-js/mobile";
import { nextTick, onMounted, ref } from "vue";
import DropdownCountry from "./DropdownCountry.vue";

import getCountryListMap from "../modules/countries";

const emit = defineEmits(["update:modelValue"]);
const props = defineProps(["modelValue"]);

const country = ref({});
const input = ref();

onMounted(() => {
  input.value.focus();
});

const phoneNumber = (number) => {
  let phone = parsePhoneNumber(
    `${country.value.dialCode}${number}`,
    country.value.code
  );

  let data = {
    number: phone?.number || null,
    country: phone?.country || null,
    national: phone?.formatNational() || null,
    international: phone?.formatInternational() || null,
    uri: phone?.getURI() || null,
    type: phone?.getType() || "-",
    possible: phone?.isPossible() || false,
    valid: phone?.isValid() && phone?.nationalNumber == number,
    value: number,
  };

  emit("update:modelValue", data);
};

const countryCode = (code) => {
  country.value = getCountryListMap()[code];
  nextTick(() => {
    input.value.focus();
  });
};
</script>
<template>
  <div class="flex text-sm">
    <DropdownCountry v-on:country-code="countryCode" />
    <input
      type="tel"
      ref="input"
      class="border w-full min-w-0 block rounded-r-lg py-2 px-3 focus:outline-none -ml-px flex-1 focus:ring focus:ring-emerald-100 focus:border-emerald-300 focus:z-10 dark:bg-slate-900 dark:border-slate-700 focus:dark:ring-slate-800 focus:dark:border-emerald-800"
      maxlength="15"
      v-on:input="phoneNumber($event.target.value)"
      v-bind:value="modelValue?.value"
    />
  </div>
</template>
