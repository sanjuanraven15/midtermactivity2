<template>
  <div class="product-list">
    <center><h1>Product List</h1></center>
    <hr>
    <table class="item-list">
      <thead>
        <tr>
          <th>Product Name</th>
          <th>Description</th>
          <th>Price</th>
          <th>Action</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(item, index) in items" :key="index">
          <td>{{ item.name }}</td>
          <td>{{ item.description }}</td>
          <td>{{ formatPrice(item.cost) }}</td>
          <td>
            <button class="edit-button">Edit</button>
            <button class="remove-button">Remove</button>
          </td>
        </tr>
      </tbody>
    </table>

    <!-- Modal for adding new products -->
    <div class="modal" :class="{ 'active': showForm }">
      <div class="modal-content">
        <span class="close" @click="closeModal">&times;</span>
        <h2>Add Product</h2>
        <form @submit.prevent="addProduct">
          <div>
            <label for="productName">Product Name:</label>
            <input type="text" id="productName" v-model="newProduct.name" required>
          </div>
          <div>
            <label for="productDescription">Description:</label>
            <textarea id="productDescription" v-model="newProduct.description" required></textarea>
          </div>
          <div>
            <label for="productPrice">Price:</label>
            <input type="number" id="productPrice" v-model.number="newProduct.cost" required>
          </div>
          <button type="submit" class="submit-button">Submit</button>
        </form>
      </div>
    </div>

    <!-- Button to open the modal -->
    <div class="add-button-container">
      <button class="add-button" @click="openModal">Add Product</button>
    </div>

    <!-- Notification Modal -->
    <div class="notification-modal" v-if="notification.show" :class="[notification.type]">
      <p>{{ notification.message }}</p>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      items: [
        { name: 'Pencil', description: 'A writing instrument', cost: 8 },
        { name: 'Ballpen', description: 'An ink-based pen', cost: 10 },
        { name: 'Marker', description: 'For highlighting', cost: 15 },
        { name: 'Bond Paper', description: 'Standard paper for writing', cost: 1 },
        { name: 'Sticky Notes', description: 'For leaving reminders', cost: 10 }
      ],
      newProduct: {
        name: '',
        description: '',
        cost: null
      },
      showForm: false,
      notification: {
        show: false,
        message: '',
        type: ''
      }
    };
  },
  methods: {
    addProduct() {
      if (!this.newProduct.name || !this.newProduct.description || !this.newProduct.cost) {
        this.showNotification('Please fill in all fields.', 'error');
        return;
      }

      this.items.push({
        name: this.newProduct.name,
        description: this.newProduct.description,
        cost: this.newProduct.cost
      });

      this.newProduct.name = '';
      this.newProduct.description = '';
      this.newProduct.cost = null;

      this.closeModal();
      this.showNotification('Product added successfully!', 'success');
    },
    formatPrice(price) {
      return '₱' + price;
    },
    openModal() {
      this.showForm = true;
      this.$nextTick(() => {
        document.querySelector('.modal').classList.add('active');
      });
    },
    closeModal() {
      this.showForm = false;
      this.$nextTick(() => {
        document.querySelector('.modal').classList.remove('active');
      });
    },
    showNotification(message, type) {
      this.notification.message = message;
      this.notification.type = type;
      this.notification.show = true;
      setTimeout(() => {
        this.notification.show = false;
      }, 3000);
    }
  }
};
</script>

<style scoped>
/* Modal Styles */
.modal {
  display: none;
  position: fixed;
  z-index: 1;
  left: 0;
  top: 0;
  width: 100%;
  height: 100%;
  overflow: auto;
  background-color: rgba(0, 0, 0, 0.4);
}

.modal.active {
  display: flex;
  align-items: center;
  justify-content: center;
}

/* Modal Content */
.modal-content {
  background-color: #fff;
  border-radius: 8px;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
  padding: 20px;
  max-width: 400px;
  width: 90%;
  position: relative;
}

/* Close Button */
.close {
  color: #000;
  font-size: 24px;
  position: absolute;
  top: 10px;
  right: 10px;
  padding: 5px;
  z-index: 1;
}

.close:hover,
.close:focus {
  color: #000;
  text-decoration: none;
  cursor: pointer;
}

/* Modal Style */
.modal-content form {
  margin-top: 20px;
}

.modal-content form div {
  margin-bottom: 15px;
}

.modal-content label {
  display: block;
  font-weight: bold;
}

.modal-content input[type="text"],
.modal-content input[type="number"],
.modal-content textarea {
  width: calc(100% - 20px);
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 4px;
  margin-top: 5px;
}

.modal-content input[type="text"]:focus,
.modal-content input[type="number"]:focus,
.modal-content textarea:focus {
  outline: none;
  border-color: #007bff;
}

/* Add Button */
.add-button {
  padding: 10px 20px;
  background-color: #007bff;
  color: #fff;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}

.add-button:hover {
  background-color: #0056b3;
}

.submit-button {
  padding: 10px 20px;
  background-color: #007bff;
  color: #fff;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  float: right;
}

.submit-button:hover {
  background-color: #0056b3;
}

/* Product List Styles */
.product-list {
  max-width: 800px;
  margin: 0 auto;
}

.item-list {
  width: 100%;
  border-collapse: collapse;
  margin-bottom: 20px;
}

.item-list th {
  text-align: left;
  padding: 8px;
  background-color: #f2f2f2;
}

.item-list td {
  padding: 8px;
}

.add-button-container {
  text-align: center;
  margin-top: 20px;
}

.edit-button,
.remove-button {
  padding: 8px 16px;
  margin-right: 5px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}

.edit-button {
  background-color: #28a745;
  color: #fff;
}

.remove-button {
  background-color: #dc3545;
  color: #fff;
}

.edit-button:hover,
.remove-button:hover {
  opacity: 0.8;
}

/* Notification Modal Styles */
.notification-modal {
  position: fixed;
  bottom: 10px; 
  right: 10px;
  background-color: rgba(0, 0, 0, 0.8);
  color: #fff;
  padding: 10px 20px;
  border-radius: 4px;
  z-index: 999;
  display: flex;
  align-items: center;
  justify-content: center;
}

.notification-modal.error {
  background-color: #dc3545;
}

.notification-modal.success {
  background-color: #28a745;
}

</style>
