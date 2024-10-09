<template>
  <div>
    <button @click="openAddPopUp()">Add User</button>
    <ul>
      <li v-for="user in users" :key="user._id" class="user-card">
        <div class="user-info">
          <p><strong>{{ user.name }}</strong></p>
          <p>Email: {{ user.email }}</p>
        </div>
        <div class="user-actions">
          <button @click="openEditPopup(user)">Edit</button>
          <button @click="deleteUser(user._id)">Delete</button>
        </div>
      </li>
    </ul>

    <!-- Edit Modal-->
    <div v-if="showEditModal" class="modal">
      <div class="modal-content">
        <span class="close" @click="closeEditPopup">&times;</span>
        <h2>Edit User</h2>
        <form @submit.prevent="submitEdit" class="editForm">
          <label for="name">Name</label>
          <input v-model="selectedUser.name" id="name" type="text" required />

          <label for="email">Email</label>
          <input v-model="selectedUser.email" id="email" type="email" required />

          <button type="submit">Update</button>
        </form>
      </div>
    </div>
    <!-- Add Modal-->
    <div v-if="showAddModal" class="modal">
      <div class="modal-content">
        <span class="close" @click="closeAddPopUp">&times;</span>
        <h2>Add User</h2>
        <form @submit.prevent="submitAdd" class="editForm">
          <label for="name">Name</label>
          <input v-model="name" type="text" required />

          <label for="email">Email</label>
          <input v-model="email" type="email" required />

          <button type="submit">Update</button>
        </form>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      users: [],
      showEditModal: false,
      selectedUser: null,
      showAddModal: false,
      name: '',
      email: '',
    };
  },
  methods: {
    fetchUsers() {
      fetch('https://user-api-x2uk.onrender.com/users')
        .then(response => response.json())
        .then(data => {
          this.users = data;
        })
        .catch(error => console.error('Error fetching users:', error));
    },
    openAddPopUp(){
      this.showAddModal = true;
    },
    closeAddPopUp(){
     this.showAddModal = false; 
    },
    openEditPopup(user) {
      this.selectedUser = { ...user }; 
      this.showEditModal = true;
    },
    closeEditPopup() {
      this.showEditModal = false;
      this.selectedUser = null;
    },
    submitAdd() {
      fetch(`https://user-api-x2uk.onrender.com/post`, {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json',
        },
        body: JSON.stringify({
          name: this.name,
          email: this.email,
        }),
      })
        
        .then(response => response.json())
        .then(this.closeAddPopUp())
        

        .catch(error => console.error('Error adding user:', error));
    },
    submitEdit() {
      fetch(`https://user-api-x2uk.onrender.com/update/${this.selectedUser._id}`, {
        method: 'PATCH',
        headers: {
          'Content-Type': 'application/json',
        },
        body: JSON.stringify({
          name: this.selectedUser.name,
          email: this.selectedUser.email,
        }),
      })
        .then(response => response.json())
        .then(this.closeEditPopup())

        .catch(error => console.error('Error updating user:', error));
    },
    deleteUser(userId) {
      fetch(`https://user-api-x2uk.onrender.com/delete/${userId}`, {
        method: 'DELETE',
      })
        .then(() => {
          this.users = this.users.filter(user => user._id !== userId);
        })
        .catch(error => console.error('Error deleting user:', error));
    }
  },
  mounted() {
    this.fetchUsers();
  }
};
</script>

<style scoped>
.user-card {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 10px;
  margin-bottom: 10px;
  border: 1px solid #ddd;
  border-radius: 8px;
}

.user-info {
  flex: 1;
}

.user-actions button {
  margin-left: 10px;
  padding: 5px 10px;
}

.modal {
  display: flex;
  flex-direction: column;
  display: block;
  position: fixed;
  z-index: 1;
  left: 0;
  top: 0;
  width: 100%;
  height: 100%;
  overflow: auto;
  background-color: rgb(0, 0, 0);
  background-color: rgba(0, 0, 0, 0.4);
  
}

.editForm{
  display: flex;
  flex-direction: column;
  gap: 10px;
}
.modal-content {
  background-color: #fefefe;
  margin: 15% auto;
  padding: 20px;
  border: 1px solid #888;
  width: 300px;
}

.close {
  color: #aaa;
  float: right;
  font-size: 28px;
  font-weight: bold;
}

.close:hover,
.close:focus {
  color: black;
  text-decoration: none;
  cursor: pointer;
}
</style>
