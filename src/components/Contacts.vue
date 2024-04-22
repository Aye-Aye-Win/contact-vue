<template>
  <div>
    <router-link to="/create">
      <button class="button">Create New Contact</button>
    </router-link>
    <div class="table-container">
      <table class="table">
        <thead>
          <tr>
            <th scope="col">Name</th>
            <th scope="col">Email Address</th>
            <th scope="col">Phone Number</th>
            <th scope="col">Actions</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="contact in contacts" :key="contact.id">
            <td>{{ contact.name }}</td>
            <td>{{ contact.email }}</td>
            <td>{{ contact.phone }}</td>
            <td>
              <router-link :to="`/edit/${contact.id}`">
              <button class="edit-btn">Edit</button>
              </router-link>
              <button @click="deleteContact(contact.id)" class="del-btn">Delete</button>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>

<script>
import { ref, onMounted } from 'vue';
import axios from 'axios';
import Swal from 'sweetalert2';

export default {
  setup() {
    const contacts = ref([]);

    const swalWithButtons = Swal.mixin({
      customClass: {
        confirmButton: 'confirm-button',
        cancelButton: 'cancel-button'
      },
      buttonsStyling: false
    });

    const getContacts = async () => {
      try {
        const { data } = await axios.get('http://localhost:3000/contacts');
        contacts.value = data;
      } catch (error) {
        console.error('Error fetching contacts: ', error);
      }
    };

    const deleteContact = async (id) => {
      swalWithButtons.fire({
        title: 'Are you sure?',
        text: "You won't be able to revert this!",
        icon: 'warning',
        showCancelButton: true,
        confirmButtonText: 'Yes, delete it!',
        cancelButtonText: 'No, cancel!',
        reverseButtons: true
      }).then(async (result) => {
        if (result.isConfirmed) {
          swalWithButtons.fire('Deleted!', 'Your file has been deleted.', 'success');
          try {
            await axios.delete(`http://localhost:3000/contacts/${id}`);
            getContacts();
          } catch (error) {
            console.error('Error deleting contact: ', error);
          }
        } else if (result.dismiss === Swal.DismissReason.cancel) {
          swalWithButtons.fire('Cancelled', 'Your imaginary file is safe :)', 'error');
        }
      });
    };

    onMounted(getContacts);

    return {
      contacts,
      deleteContact,
    };
  },
};
</script>

<style>
.button {
  font-size: 16px;
  padding: 8px 16px;
  margin: 12px 20px;
  border: none;
  border-radius: 4px;
  background-color: #4a5568;
  color: #fff;
  cursor: pointer;
}

.table-container {
  overflow-x: auto;
  margin-top: 10px;
}

.table {
  width: 100%;
  border-collapse: collapse;
}

.table th,
.table td {
  font-size: 1.25rem;
  padding: 0.75rem;
  border-bottom: 1px solid #e2e8f0;
}

tbody{
  text-align: center;
}

.edit-btn {
  font-size: 16px;
  padding: 8px 16px;
  margin-right: 15px;
  color: #e2e8f0;
  background-color: #48bb78;
  border-radius: 4px;
  border: none;
  cursor: pointer;
}

.del-btn {
  font-size: 16px;
  padding: 8px 16px;
  color: #e2e8f0;
  background-color: #f56565;
  border-radius: 4px;
  border: none;
  cursor: pointer;
}

.confirm-button {
  background-color: #48bb78;
  color: #fff;
  padding: 0.5rem 1rem;
  border-radius: 0.375rem;
  cursor: pointer;
}

.cancel-button {
  background-color: #f56565;
  color: #fff;
  padding: 0.5rem 1rem;
  border-radius: 0.375rem;
  cursor: pointer;
}
</style>
