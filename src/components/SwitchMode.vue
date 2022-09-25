<script setup>
import { onMounted, reactive, ref } from "vue";

const styles = reactive({
  modes: ["light", "dark"],
  colors: ["text-emerald-400", "dark:text-emerald-600"],
});

const dropdownTrigger = ref();
const dropdownPanel = ref();
const dropdownOpen = ref(false);

const isDarkMode = ref(false);

onMounted(() => {
  const index = [...styles.modes, undefined].indexOf(localStorage.theme);
  dropdownPanel.value.children[index].classList.add(...styles.colors);
  defaultMode();
});

const togglePanel = () => {
  dropdownOpen.value = !dropdownOpen.value;
};

const theme = (event, mode) => {
  dropdownOpen.value = false;

  const children = dropdownPanel.value.children;

  for (const child of children) {
    child.classList.remove(...styles.colors);
  }
  event.target.closest("li").classList.add(...styles.colors);

  if (styles.modes.includes(mode)) {
    localStorage.setItem("theme", mode);
  } else {
    localStorage.removeItem("theme");
  }

  defaultMode();
};

const defaultMode = () => {
  let isThemeDark = localStorage.theme === "dark";
  let isMatchDark =
    !("theme" in localStorage) &&
    window.matchMedia("(prefers-color-scheme: dark)").matches;

  isDarkMode.value = isThemeDark || isMatchDark;
  if (isDarkMode.value) {
    document.documentElement.classList.add("dark");
  } else {
    document.documentElement.classList.remove("dark");
  }

  dropdownTrigger.value.classList.remove(...styles.colors);
  if (localStorage.theme) {
    dropdownTrigger.value.classList.add(...styles.colors);
  }
};

document.addEventListener("click", (e) => {
  const isClickOutside = !dropdownPanel.value
    ?.closest("div")
    .contains(e.target);
  if (isClickOutside) {
    dropdownOpen.value = false;
  }
});
</script>

<template>
  <div>
    <div class="relative">
      <button
        class="p-2.5 border border-slate-200 focus:ring rounded-lg dark:border-slate-700 focus:dark:border-emerald-800 focus:dark:ring-slate-800 focus:ring-emerald-100 focus:border-emerald-300 text-slate-500"
        ref="dropdownTrigger"
        v-on:click="togglePanel"
      >
        <svg
          xmlns="http://www.w3.org/2000/svg"
          width="16"
          height="16"
          fill="currentColor"
          class="bi bi-brightness-high-fill"
          viewBox="0 0 16 16"
          v-show="!isDarkMode"
        >
          <path
            d="M12 8a4 4 0 1 1-8 0 4 4 0 0 1 8 0zM8 0a.5.5 0 0 1 .5.5v2a.5.5 0 0 1-1 0v-2A.5.5 0 0 1 8 0zm0 13a.5.5 0 0 1 .5.5v2a.5.5 0 0 1-1 0v-2A.5.5 0 0 1 8 13zm8-5a.5.5 0 0 1-.5.5h-2a.5.5 0 0 1 0-1h2a.5.5 0 0 1 .5.5zM3 8a.5.5 0 0 1-.5.5h-2a.5.5 0 0 1 0-1h2A.5.5 0 0 1 3 8zm10.657-5.657a.5.5 0 0 1 0 .707l-1.414 1.415a.5.5 0 1 1-.707-.708l1.414-1.414a.5.5 0 0 1 .707 0zm-9.193 9.193a.5.5 0 0 1 0 .707L3.05 13.657a.5.5 0 0 1-.707-.707l1.414-1.414a.5.5 0 0 1 .707 0zm9.193 2.121a.5.5 0 0 1-.707 0l-1.414-1.414a.5.5 0 0 1 .707-.707l1.414 1.414a.5.5 0 0 1 0 .707zM4.464 4.465a.5.5 0 0 1-.707 0L2.343 3.05a.5.5 0 1 1 .707-.707l1.414 1.414a.5.5 0 0 1 0 .708z"
          />
        </svg>

        <svg
          xmlns="http://www.w3.org/2000/svg"
          width="16"
          height="16"
          fill="currentColor"
          class="bi bi-moon-stars-fill"
          viewBox="0 0 16 16"
          v-show="isDarkMode"
        >
          <path
            d="M6 .278a.768.768 0 0 1 .08.858 7.208 7.208 0 0 0-.878 3.46c0 4.021 3.278 7.277 7.318 7.277.527 0 1.04-.055 1.533-.16a.787.787 0 0 1 .81.316.733.733 0 0 1-.031.893A8.349 8.349 0 0 1 8.344 16C3.734 16 0 12.286 0 7.71 0 4.266 2.114 1.312 5.124.06A.752.752 0 0 1 6 .278z"
          />
          <path
            d="M10.794 3.148a.217.217 0 0 1 .412 0l.387 1.162c.173.518.579.924 1.097 1.097l1.162.387a.217.217 0 0 1 0 .412l-1.162.387a1.734 1.734 0 0 0-1.097 1.097l-.387 1.162a.217.217 0 0 1-.412 0l-.387-1.162A1.734 1.734 0 0 0 9.31 6.593l-1.162-.387a.217.217 0 0 1 0-.412l1.162-.387a1.734 1.734 0 0 0 1.097-1.097l.387-1.162zM13.863.099a.145.145 0 0 1 .274 0l.258.774c.115.346.386.617.732.732l.774.258a.145.145 0 0 1 0 .274l-.774.258a1.156 1.156 0 0 0-.732.732l-.258.774a.145.145 0 0 1-.274 0l-.258-.774a1.156 1.156 0 0 0-.732-.732l-.774-.258a.145.145 0 0 1 0-.274l.774-.258c.346-.115.617-.386.732-.732L13.863.1z"
          />
        </svg>
      </button>
      <ul
        class="absolute rounded-lg bg-white w-32 text-sm right-0 shadow-lg border border-slate-200 py-1 dark:bg-slate-800 dark:text-slate-300 dark:border-slate-700"
        ref="dropdownPanel"
        v-show="dropdownOpen"
      >
        <li
          class="py-2 px-3 cursor-pointer hover:bg-emerald-100 hover:dark:bg-emerald-900"
          v-on:click="theme($event, 'light')"
        >
          <svg
            xmlns="http://www.w3.org/2000/svg"
            width="16"
            height="16"
            fill="currentColor"
            class="bi bi-brightness-high-fill inline-block mr-3"
            viewBox="0 0 16 16"
          >
            <path
              d="M12 8a4 4 0 1 1-8 0 4 4 0 0 1 8 0zM8 0a.5.5 0 0 1 .5.5v2a.5.5 0 0 1-1 0v-2A.5.5 0 0 1 8 0zm0 13a.5.5 0 0 1 .5.5v2a.5.5 0 0 1-1 0v-2A.5.5 0 0 1 8 13zm8-5a.5.5 0 0 1-.5.5h-2a.5.5 0 0 1 0-1h2a.5.5 0 0 1 .5.5zM3 8a.5.5 0 0 1-.5.5h-2a.5.5 0 0 1 0-1h2A.5.5 0 0 1 3 8zm10.657-5.657a.5.5 0 0 1 0 .707l-1.414 1.415a.5.5 0 1 1-.707-.708l1.414-1.414a.5.5 0 0 1 .707 0zm-9.193 9.193a.5.5 0 0 1 0 .707L3.05 13.657a.5.5 0 0 1-.707-.707l1.414-1.414a.5.5 0 0 1 .707 0zm9.193 2.121a.5.5 0 0 1-.707 0l-1.414-1.414a.5.5 0 0 1 .707-.707l1.414 1.414a.5.5 0 0 1 0 .707zM4.464 4.465a.5.5 0 0 1-.707 0L2.343 3.05a.5.5 0 1 1 .707-.707l1.414 1.414a.5.5 0 0 1 0 .708z"
            />
          </svg>
          <span class="inline-block">Light</span>
        </li>
        <li
          class="py-2 px-3 cursor-pointer hover:bg-emerald-100 hover:dark:bg-emerald-900"
          v-on:click="theme($event, 'dark')"
        >
          <svg
            xmlns="http://www.w3.org/2000/svg"
            width="16"
            height="16"
            fill="currentColor"
            class="bi bi-moon-stars-fill inline-block mr-3"
            viewBox="0 0 16 16"
          >
            <path
              d="M6 .278a.768.768 0 0 1 .08.858 7.208 7.208 0 0 0-.878 3.46c0 4.021 3.278 7.277 7.318 7.277.527 0 1.04-.055 1.533-.16a.787.787 0 0 1 .81.316.733.733 0 0 1-.031.893A8.349 8.349 0 0 1 8.344 16C3.734 16 0 12.286 0 7.71 0 4.266 2.114 1.312 5.124.06A.752.752 0 0 1 6 .278z"
            />
            <path
              d="M10.794 3.148a.217.217 0 0 1 .412 0l.387 1.162c.173.518.579.924 1.097 1.097l1.162.387a.217.217 0 0 1 0 .412l-1.162.387a1.734 1.734 0 0 0-1.097 1.097l-.387 1.162a.217.217 0 0 1-.412 0l-.387-1.162A1.734 1.734 0 0 0 9.31 6.593l-1.162-.387a.217.217 0 0 1 0-.412l1.162-.387a1.734 1.734 0 0 0 1.097-1.097l.387-1.162zM13.863.099a.145.145 0 0 1 .274 0l.258.774c.115.346.386.617.732.732l.774.258a.145.145 0 0 1 0 .274l-.774.258a1.156 1.156 0 0 0-.732.732l-.258.774a.145.145 0 0 1-.274 0l-.258-.774a1.156 1.156 0 0 0-.732-.732l-.774-.258a.145.145 0 0 1 0-.274l.774-.258c.346-.115.617-.386.732-.732L13.863.1z"
            />
          </svg>
          <span class="inline-block">Dark</span>
        </li>
        <li
          class="py-2 px-3 cursor-pointer hover:bg-emerald-100 hover:dark:bg-emerald-900"
          v-on:click="theme($event, 'default')"
        >
          <svg
            xmlns="http://www.w3.org/2000/svg"
            width="16"
            height="16"
            fill="currentColor"
            class="bi bi-display-fill inline-block mr-3"
            viewBox="0 0 16 16"
          >
            <path
              d="M6 12c0 .667-.083 1.167-.25 1.5H5a.5.5 0 0 0 0 1h6a.5.5 0 0 0 0-1h-.75c-.167-.333-.25-.833-.25-1.5h4c2 0 2-2 2-2V4c0-2-2-2-2-2H2C0 2 0 4 0 4v6c0 2 2 2 2 2h4z"
            />
          </svg>
          <span class="inline-block">System</span>
        </li>
      </ul>
    </div>
  </div>
</template>
