<template>
  <form @submit.prevent="onSubmitHandler" class="form-container">
    <h1 class="form-heading">Create New Contact</h1>
    <div class="form-group">
      <label for="name" class="form-label">Name</label>
      <div class="relative">
        <input v-model="name" type="text" id="name" class="form-input" placeholder="name"/>
      </div>
    </div>
    <div class="form-group">
      <label for="email" class="form-label">Email Address</label>
      <div class="relative">
        <input v-model="email" type="email" id="email" class="form-input" placeholder="example@gmail.com"/>
      </div>
    </div>
    <div class="form-group">
      <label for="phone" class="form-label">Phone Number</label>
      <div class="relative">
        <input v-model="phone" type="tel" id="phone" class="form-input" placeholder="09xxxxxxxxx"/>
      </div>
    </div>
    <button type="submit" class="button-create">CREATE</button>
    <router-link to="/">
      <button class="button-cancel">CANCEL</button>
    </router-link>
  </form>
</template>

<script>
import { ref } from 'vue';
import {useRouter} from "vue-router";
import axios from 'axios';
import Swal from 'sweetalert2';

export default {
  setup() {
    const name = ref('');
    const email = ref('');
    const phone = ref('');
    const router = useRouter();


    const Toast = Swal.mixin({
      toast: true,
      position: 'top-end',
      showConfirmButton: false,
      timer: 3000,
      timerProgressBar: true,
      didOpen: (toast) => {
        toast.addEventListener('mouseenter', Swal.stopTimer);
        toast.addEventListener('mouseleave', Swal.resumeTimer);
      }
    });

    const apiCreateContact = async (contact) => {
      try {
        await axios.post('http://localhost:3000/contacts', contact);
        Toast.fire({
          icon: 'success',
          title: 'Contact Created!'
        });
        router.push('/');
      } catch (error) {
        console.error('Error creating contact:', error);
      }
    };

    const onSubmitHandler = () => {
      const contact = { name: name.value, email: email.value, phone: phone.value };
      apiCreateContact(contact);
    };

    return {
      name,
      email,
      phone,
      onSubmitHandler
    };
  }
};
</script>

<style>
.form-container {
  width: 300px;
  border: 2px solid #e5e7eb;
  padding: 15px 20px;
  border-radius: 4px;
  margin: 10rem auto;
}

.form-heading {
  color: #374151;
  font-size: 24px;
  font-weight: bold;
  margin-bottom: 20px;
}

.form-group {
  margin-bottom: 15px;
}

.form-label {
  display: block;
  margin-bottom: 10px;
  font-size: 16px;
  font-weight: 500;
  color: #374151;
}

.form-input {
  width: 270px;
  padding: 8px 16px;
  border: 1px solid #d1d5db;
  border-radius: 4px;
  font-size: 16px;
  color: #1f2937;
  background-color: #f9fafb;
  transition: border-color 0.15s ease-in-out, box-shadow 0.15s ease-in-out;
}

.button-create {
  color: #ffffff;
  background-color: #4b5563;
  padding: 8px 16px;
  border-radius: 4px;
  font-size: 14px;
  margin-top: 10px;
  cursor: pointer;
  border: none;
}

.button-cancel {
  color: #ffffff;
  background-color: #ef4444;
  padding: 8px 16px;
  border-radius: 4px;
  font-size: 14px;
  cursor: pointer;
  border: none;
  margin-left: 10px;
}
</style>
