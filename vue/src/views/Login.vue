<template>
  <div class="blabl">
    <div>
      <h2
        class="mt-6 text-center text-3xl font-bold tracking-tight text-gray-900"
      >
        Prihláste sa do svojho účtu
      </h2>
      <p class="mt-2 text-center text-sm text-gray-600">
        Nový používateľ? 
        {{ " " }}
        <router-link
          :to="{ name: 'Register' }"
          class="font-medium text-teal-700 hover:text-teal-500"
          >Zaregistrujte sa</router-link
        >
      </p>
    </div>
    <form class="mt-8 space-y-6" @submit="login">
      <div
        v-if="errorMsg"
        class="flex items-center justify-between py-3 px-5 bg-red-500 text-white rounded"
      >
        {{ errorMsg }}
        <span @click="errorMsg = ''">
          <svg
            xmlns="http://www.w3.org/2000/svg"
            fill="none"
            viewBox="0 0 24 24"
            stroke-width="1.5"
            stroke="currentColor"
            class="w-6 h-6 flex items-center justify-center rounded-full transition-colors cursor-pointer hover:bg-[rgba(0,0,0,0.2)]"
          >
            <path
              stroke-linecap="round"
              stroke-linejoin="round"
              d="M6 18L18 6M6 6l12 12"
            />
          </svg>
        </span>
      </div>
      <input type="hidden" name="remember" value="true" />
      <div class="-space-y-px rounded-md shadow-sm">
        <div>
          <label for="email-address" class="sr-only">Email</label>
          <input
            id="email-address"
            name="email"
            type="email"
            autocomplete="email"
            required=""
            v-model="user.email"
            class="relative block w-full appearance-none rounded-none rounded-t-md border border-gray-300 px-3 py-2 text-gray-900 placeholder-gray-500 focus:z-10 focus:border-indigo-500 focus:outline-none focus:ring-indigo-500 sm:text-sm"
            placeholder="Email"
          />
        </div>
        <div>
          <label for="password" class="sr-only">Heslo</label>
          <input
            id="password"
            name="password"
            type="password"
            autocomplete="current-password"
            required=""
            v-model="user.password"
            class="relative block w-full appearance-none rounded-none rounded-b-md border border-gray-300 px-3 py-2 text-gray-900 placeholder-gray-500 focus:z-10 focus:border-indigo-500 focus:outline-none focus:ring-indigo-500 sm:text-sm"
            placeholder="Heslo"
          />
        </div>
      </div>

      <div class="flex items-center justify-between">
        <!--<div class="flex items-center">
          <input
            id="remember-me"
            name="remember-me"
            type="checkbox"
            v-model="user.remember"
            class="h-4 w-4 text-teal-600 focus:ring-teal-600 border-gray-300 rounded"
          />
          <label for="remember-me" class="ml-2 block text-sm text-gray-900">
            Remember me
          </label>
        </div>-->
      </div>

      <div>
        <button
          type="submit"
          class="group relative flex w-full justify-center rounded-md border border-transparent bg-teal-600 py-2 px-4 text-sm font-medium text-white hover:bg-teal-700 focus:outline-none focus:ring-2 focus:ring-indigo-500 focus:ring-offset-2"
        >
          <span class="absolute inset-y-0 left-0 flex items-center pl-3">
            <LockClosedIcon
              class="h-5 w-5 text-teal-800 group-hover:text-teal-500"
              aria-hidden="true"
            />
          </span>
          Prihlásiť
        </button>
      </div>
    </form>
  </div>
</template>

<script setup>
import { LockClosedIcon } from "@heroicons/vue/20/solid";
import store from "../store";
import { useRouter } from "vue-router";
import { ref } from "vue";

const router = useRouter();

const user = {
  email: "",
  password: "",
  remember: false,
};
let errorMsg = ref("");

function login(ev) {
  ev.preventDefault();

  store
    .dispatch("login", user)
    .then(() => {
      router.push({
        name: "Home",
      });
      store.dispatch("fetchScores");
      store.dispatch("getSpentTime");
    })
    .catch((err) => {
      console.log("Error:", err);
      errorMsg.value = "Prihlasovacie údaje sú nesprávne.";
    });
}
</script>
