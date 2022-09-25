<script setup>
import { nextTick, onMounted, reactive, ref, watch } from "vue";
import { createPopper } from "@popperjs/core";

import getCountryListMap from "../modules/countries";

const emit = defineEmits(["countryCode"]);

const dropdownTrigger = ref("");
const dropdownPanel = ref("");
const dropdownOpen = ref(false);

const country = ref({});
const countries = ref([]);

const query = ref("");

const popper = reactive({
  instance: null,
  placement: null,
});

onMounted(() => {
  countries.value = Object.values(getCountryListMap());

  fetch("https://ipinfo.io/?token=7f5897601c9f81")
    .then((res) => res.json())
    .then((data) => {
      country.value = getCountryListMap()[data.country];
      emit("countryCode", data.country);
    });

  popper.instance = createPopper(dropdownTrigger.value, dropdownPanel.value, {
    placement: "bottom-start",
    modifiers: [
      {
        name: "topLogger",
        enabled: true,
        phase: "main",
        fn({ state }) {
          if (popper.placement != state.placement) {
            popper.placement = state.placement;
            const list = document.querySelector("[popper-list]");
            const search = document.querySelector("[popper-input]");

            if (popper.placement === "bottom-start") {
              list.classList.remove("order-1");
              search.classList.add("order-1");

              list.classList.add("order-2");
              search.classList.remove("order-2");
            } else {
              list.classList.add("order-1");
              search.classList.remove("order-1");

              list.classList.remove("order-2");
              search.classList.add("order-2");
            }
          }
        },
      },
    ],
  });
});

watch(query, (q) => {
  countries.value = search(q);
});

const search = (q) => {
  let text = q.toLowerCase();
  let list = Object.values(getCountryListMap());

  dropdownPanel.value.querySelector("[popper-list]").scroll({ top: 0 });

  return list
    .filter(({ country }) => country.toLowerCase().includes(text))
    .sort(
      (a, b) =>
        b.country.toLowerCase().startsWith(text) -
        a.country.toLowerCase().startsWith(text)
    );
};

const toBaseImage = (svg) => `data:image/svg+xml;base64,${window.btoa(svg)}`;

const togglePanel = () => {
  dropdownOpen.value = !dropdownOpen.value;
  popper.instance.update();
  query.value = "";

  nextTick(() => {
    if (dropdownOpen.value) {
      let code = country.value.code.toLowerCase();
      let target = dropdownPanel.value.querySelector(
        `[popper-list] [data-list="list-${code}"]`
      );

      let parent = target.parentNode;
      let offset = target.offsetTop - parent.offsetHeight / 2;

      dropdownPanel.value.querySelector("input").focus();

      parent.scroll({ top: offset });
      target.classList.add("bg-emerald-100", "dark:bg-emerald-900");

      dropdownTrigger.value.classList.add("btn-countries-show");
      dropdownTrigger.value.classList.remove("btn-countries-hide");
    } else {
      resetTriggerStyle();
    }
  });
};

const selectCountry = (countryCode) => {
  country.value = getCountryListMap()[countryCode];
  dropdownOpen.value = false;

  let actives = dropdownPanel.value.querySelectorAll(
    `[popper-list] .bg-emerald-100`
  );

  for (const active of actives) {
    active.classList.remove("bg-emerald-100", "dark:bg-emerald-900");
  }

  resetTriggerStyle();

  emit("countryCode", countryCode);
};

const highlight = (str) => {
  if (query.value.length) {
    let regex = new RegExp(query.value, "gi");
    return str.replace(regex, "<span class='text-red-500'>$&</span>");
  }

  return str;
};

const resetTriggerStyle = () => {
  dropdownTrigger.value?.classList.remove("btn-countries-show");
  dropdownTrigger.value?.classList.add("btn-countries-hide");
};

document.addEventListener("click", (e) => {
  const isClickOutside = !dropdownPanel.value
    ?.closest("div.relative")
    .contains(e.target);

  if (isClickOutside) {
    dropdownOpen.value = false;
    resetTriggerStyle();
  }
});
</script>

<template>
  <div class="relative">
    <button
      ref="dropdownTrigger"
      v-on:click="togglePanel"
      class="py-2 pl-3 pr-5 rounded-l-lg border border-slate-200 items-center focus:outline-none after:content-[''] after:absolute after:border-4 after:border-transparent after:top-1/2 after:right-2 dark:border-slate-700 btn-countries-hide"
      tabindex="-1"
    >
      <div class="flex">
        <img
          v-bind:src="toBaseImage(country.flag)"
          alt="flag"
          class="w-6 h-4 my-1 inline-block pointer-events-none"
        />
        <span class="inline-block ml-1.5 min-w-[16px] pointer-events-none">
          {{ country.dialCode }}
        </span>
      </div>
    </button>

    <div
      ref="dropdownPanel"
      v-show="dropdownOpen"
      class="absolute z-50 bg-white border border-slate-200 w-60 text-sm max-h-[320px] overflow-hidden rounded-lg shadow-md flex flex-col dark:bg-slate-800 dark:border-slate-700"
    >
      <div
        class="p-3 -my-px border-y border-slate-200 h-16 dark:border-slate-700"
        popper-input
      >
        <input
          type="search"
          v-model="query"
          class="py-2 px-3 rounded-lg w-full border border-slate-200 focus:outline-none focus:border-emerald-300 focus:ring focus:ring-emerald-100 dark:bg-transparent dark:border-slate-700 focus:dark:ring-slate-700 dark:focus:border-emerald-800"
        />
      </div>
      <div class="max-h-[256px] overflow-y-auto" popper-list>
        <div
          v-for="(row, index) in countries"
          v-bind:key="index"
          class="px-3 py-2 hover:bg-emerald-100 cursor-pointer flex flex-wrap transition-colors duration-100 hover:dark:bg-emerald-900 dark:text-slate-300"
          v-on:click="selectCountry(row.code)"
          v-bind:data-list="`list-${row.code.toLowerCase()}`"
        >
          <div class="pt-0.5">
            <img
              v-bind:src="toBaseImage(row.flag)"
              alt=""
              class="h-4 w-6 mr-2"
            />
          </div>
          <div class="flex-1">
            <span class="text-slate-600 dark:text-slate-400">
              ({{ row.dialCode }})
            </span>
            <span v-html="highlight(row.country)"> </span>
          </div>
        </div>
        <div v-show="!countries.length">
          <div class="px-3 py-2 text-center text-slate-600 dark:text-slate-400">
            Not found.
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style lang="postcss">
.btn-countries-show {
  @apply ring ring-emerald-300 border-emerald-300;
}
.dark .btn-countries-show {
  @apply border-emerald-800 ring-slate-800;
}
.btn-countries-show::after {
  @apply border-b-slate-700 -translate-y-full;
}
.btn-countries-hide::after {
  @apply border-t-slate-700 -translate-y-1/2;
}
</style>
