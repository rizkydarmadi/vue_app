<template>
  <div class="container mt-10 mx-auto">
    <div v-if="!isLoggedIn" class="lg:col-span-1 border-r border-gray-300 pr-4">
      <form @submit.prevent="login">
        <!-- Your login form fields -->
        <div class="mb-4">
          <label for="username" class="block text-sm font-medium leading-6 text-gray-900">Username:</label>
          <input v-model="username" type="text" id="username" name="username" />
        </div>
        <div class="mb-4">
          <label for="password" class="block text-sm font-medium leading-6 text-gray-900">Password:</label>
          <input v-model="password" type="password" id="password" name="password" />
        </div>
        <button type="submit" class="bg-indigo-600 text-white py-2 px-4 rounded">Login</button>
      </form>
    </div>

    <div v-else class="lg:col-span-3">
      <div class="container mt-10 mx-auto">
        <div class="grid grid-cols-1 lg:grid-cols-3 gap-8 container mx-auto max-w-5xl px-4">
          <div class="lg:col-span-1 border-r border-gray-300 pr-4">
            <form @submit.prevent="login">
              <h2 class="font-medium mb-4">Freight Calculator</h2>
              <div class="sm:col-span-3">
                <label for="country" class="block text-sm font-medium leading-6 text-gray-900">Origin</label>
                <div class="mt-2">
                  <select v-model="selectedOrigin" id="country" name="country" autocomplete="country-name"
                    class="block w-full rounded-md border-0 py-1.5 text-gray-900 shadow-sm ring-1 ring-inset ring-gray-300 focus:ring-2 focus:ring-inset focus:ring-indigo-600 sm:max-w-xs sm:text-sm sm:leading-6">
                    <option v-for="country in countries" :key="country.id">{{ country.country_name }}</option>
                  </select>
                </div>
              </div>
              <div class="col-span-full">
                <label for="about" class="block text-sm font-medium leading-6 text-gray-900">Destination</label>
                <div class="mt-2">
                  <textarea id="about" name="about" rows="3"
                    class="block w-full rounded-md border-0 py-1.5 text-gray-900 shadow-sm ring-1 ring-inset ring-gray-300 placeholder:text-gray-400 focus:ring-2 focus:ring-inset focus:ring-indigo-600 sm:text-sm sm:leading-6" />
                </div>
              </div>
              <div class="sm:col-span-3">
                <label for="category" class="block text-sm font-medium leading-6 text-gray-900">Product Category</label>
                <div class="mt-2">
                  <select v-model="selectedCategory" id="category" name="category" autocomplete="category"
                    class="block w-full rounded-md border-0 py-1.5 text-gray-900 shadow-sm ring-1 ring-inset ring-gray-300 focus:ring-2 focus:ring-inset focus:ring-indigo-600 sm:max-w-xs sm:text-sm sm:leading-6">
                    <option v-for="category in categories" :key="category.id">{{ category.category_title }}</option>
                  </select>
                </div>
              </div>

              <div class="border-b border-gray-900/10 pb-12">
                <label for="country" class="block text-sm font-medium leading-6 text-gray-900 mb-4">Total Weight</label>
                <div class="grid grid-cols-1 gap-x-6 gap-y-8 sm:grid-cols-6">
                  <div class="sm:col-span-3">
                    <div class="">
                      <input type="number" id="number-input" aria-describedby="helper-text-explanation"
                        class="block w-full rounded-md border-0 py-1.5 text-gray-900 shadow-sm ring-1 ring-inset ring-gray-300 placeholder:text-gray-400 focus:ring-2 focus:ring-inset focus:ring-indigo-600 sm:text-sm sm:leading-6" />

                    </div>
                  </div>

                  <div class="sm:col-span-2">
                    <select id="weight" name="weight" autocomplete="country-name"
                      class="block w-full rounded-md border-0 py-1.5 text-gray-900 shadow-sm ring-1 ring-inset ring-gray-300 focus:ring-2 focus:ring-inset focus:ring-indigo-600 sm:max-w-xs sm:text-sm sm:leading-6">
                      <option>Kg</option>
                    </select>
                  </div>
                </div>
              </div>


              <div class="mt-6 flex items-center justify-end gap-x-6">
                <button type="submit"
                  class="rounded-md bg-indigo-600 px-3 py-2 text-sm font-semibold text-white shadow-sm hover:bg-indigo-500 focus-visible:outline focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-indigo-600">Calculate</button>
              </div>
            </form>
          </div>

          <div class="lg:col-span-2">
            <h1 class="block text-lg font-medium">This is a right compomnet</h1>
          </div>
        </div>
      </div>
      <h1 class="block text-lg font-medium">This is a right component</h1>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';
import axios from 'axios';
import VueCookies from 'vue-cookies';

const isLoggedIn = ref(false);
const username = ref('');
const password = ref('');
const countries = ref([]); // Declare countries variable
const categories = ref([]);

const login = async () => {
  try {
    const response = await axios.post('http://localhost:8000/api/token/', {
      username: username.value,
      password: password.value,
    });

    const token = response.data.access;
    VueCookies.set('jwtToken', token, '7d'); // Set the token in cookies with a 7-day expiration

    // Set the login status to true
    isLoggedIn.value = true;

    // You can perform additional actions after a successful login if needed

    // Fetch data after login
    fetchCountries();
    fetchCategories();
  } catch (error) {
    console.error('Error during login:', error);
  }
};

const fetchCountries = async () => {
  try {
    const token = VueCookies.get('jwtToken');
    const response = await axios.get('http://localhost:8000/api/countries/', {
      headers: {
        Authorization: `Bearer ${token}`,
      },
    });
    // Your existing logic for handling countries
    countries.value = response.data;
  } catch (error) {
    console.error('Error fetching countries:', error);
  }
};

const fetchCategories = async () => {
  try {
    const token = VueCookies.get('jwtToken');
    const response = await axios.get('http://localhost:8000/api/categories/', {
      headers: {
        Authorization: `Bearer ${token}`,
      },
    });
    categories.value = response.data;
  } catch (error) {
    console.error('Error fetching categories:', error);
  }
};

// Check if the user is already logged in
onMounted(() => {
  const token = VueCookies.get('jwtToken');
  if (token) {
    isLoggedIn.value = true;
    fetchCountries();
    fetchCategories();
  }
});
</script>