<script setup>
import { ref, onMounted } from 'vue';
import axios from 'axios';
import VueCookies from 'vue-cookies';

const isLoggedIn = ref(false);
const username = ref('');
const password = ref('');
const countries = ref([]); // Declare countries variable
const categories = ref([]); // Declare categories variable

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
