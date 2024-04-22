<template>
  <form @submit.prevent="onSubmitHandler" class="form-container">
    <h1 class="form-heading">Edit Contact</h1>
    <div class="form-group">
      <label for="name" class="form-label">Name</label>
      <div class="relative">
        <input v-model="name" type="text" id="name" class="form-input" placeholder="Name"/>
      </div>
    </div> 
    <div class="form-group">
      <label for="email" class="form-label">Email Address</label>
      <div class="relative">
        <input v-model="email" type="text" id="email" class="form-input" placeholder="example@gmail.com"/>
      </div>
    </div>
    <div class="form-group">
      <label for="phone" class="form-label">Phone Number</label>
      <div class="relative">
        <input v-model="phone" type="text" id="phone" class="form-input" placeholder="09xxxxxxxxx"/>
      </div>
    </div>
    <button type="submit" class="button-edit">Edit</button>
    <router-link to="/">
      <button class="button-cancel">CANCEL</button>
    </router-link>
  </form>
</template>

<script>
import { ref, onMounted } from 'vue';
import { useRoute, useRouter } from 'vue-router';
import axios from 'axios';
import Swal from 'sweetalert2';

export default {
  setup() {
    const name = ref('');
    const email = ref('');
    const phone = ref('');
    const route = useRoute();
    const router = useRouter();
    const id = route.params.id; 

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

    const getSingleContact = async () => {
      try {
        const { data } = await axios.get(`http://localhost:3000/contacts/${id}`);
        name.value = data.name;
        email.value = data.email;
        phone.value = data.phone;
      } catch (error) {
        console.error('Error fetching contact:', error);
      }
    };

    const apiEditContact = async (contact) => {
      try {
        await axios.patch(`http://localhost:3000/contacts/${id}`, contact);
        Toast.fire({
          icon: 'success',
          title: 'Contact Edited!'
        });
        router.push('/');
      } catch (error) {
        console.error('Error editing contact:', error);
      }
    };

    const onSubmitHandler = () => {
      const contact = { name: name.value, email: email.value, phone: phone.value };
      apiEditContact(contact);
    };

    onMounted(() => {
      getSingleContact();
    });

    return {
      name,
      email,
      phone,
      onSubmitHandler
    };
  }
};
</script>

<style scoped>
.button-edit{
  color: #ffffff;
  background-color: #4b5563;
  padding: 8px 16px;
  border-radius: 4px;
  font-size: 16px;
  margin-top: 10px;
  cursor: pointer;
  border: none;
}

</style>
