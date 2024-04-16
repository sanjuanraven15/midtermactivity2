<template>
  <div class="product-list">
    <center><h1>Product List</h1></center>
    <hr />
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
        <tr
          v-for="(item, index) in items"
          :key="index"
          :class="{ removing: item.removing }"
        >
          <td :class="{ 'added-product-transition': addedProduct === item }">
            {{ item.name }}
          </td>
          <td :class="{ 'added-product-transition': addedProduct === item }">
            {{ item.description }}
          </td>
          <td :class="{ 'added-product-transition': addedProduct === item }">
            {{ formatPrice(item.cost) }}
          </td>
          <td>
            <button
              @click="openModalForEditing(item, index)"
              class="edit-button"
            >
              Edit
            </button>

            |
            <button @click="confirmDelete(item)" class="delete-button">
              Delete
            </button>
          </td>
        </tr>
      </tbody>
    </table>

    <!-- Modal for adding new products -->
    <div class="modal" :class="{ active: showForm }">
      <div class="modal-content">
        <span class="close" @click="closeModal">&times;</span>
        <h2>Add Product</h2>
        <form @submit.prevent="addProduct">
          <div>
            <label for="productName">Product Name:</label>
            <input
              type="text"
              id="productName"
              v-model="newProduct.name"
              required
            />
          </div>
          <div>
            <label for="productDescription">Description:</label>
            <textarea
              id="productDescription"
              v-model="newProduct.description"
              required
            ></textarea>
          </div>
          <div>
            <label for="productPrice">Price:</label>
            <input
              type="number"
              id="productPrice"
              v-model.number="newProduct.cost"
              required
            />
          </div>
          <button type="submit" class="submit-button">Submit</button>
        </form>
      </div>
    </div>

    <!-- Popup for editing product -->
    <div class="edit-product-popup" v-if="editMode">
      <h2>Edit Product</h2>
      <span class="close" @click="cloeseEdit">&times;</span>
      <form @submit.prevent="submitEdit">
        <div>
          <label for="editProductName">Product Name:</label>
          <input
            type="text"
            id="editProductName"
            v-model="editFormData.name"
            required
          />
        </div>
        <div>
          <label for="editProductDescription">Description:</label>
          <textarea
            id="editProductDescription"
            v-model="editFormData.description"
            required
          ></textarea>
        </div>
        <div>
          <label for="editProductPrice">Price:</label>
          <input
            type="number"
            id="editProductPrice"
            v-model.number="editFormData.cost"
            required
          />
        </div>
        <button type="submit">Submit</button>
      </form>
    </div>

    <!-- Button to open the modal -->
    <div class="add-button-container">
      <button class="add-button" @click="openModal">Add Product</button>
    </div>

    <!-- Notification Modal -->
    <div
      class="notification-modal"
      v-if="notification.show"
      :class="[notification.type]"
    >
      <p>{{ notification.message }}</p>
    </div>
  </div>
</template>

<script>
import Swal from "sweetalert2";

export default {
  data() {
    return {
      items: [
        { name: "Pencil", description: "A writing instrument", cost: 8 },
        { name: "Ballpen", description: "An ink-based pen", cost: 10 },
        { name: "Marker", description: "For highlighting", cost: 15 },
        {
          name: "Bond Paper",
          description: "Standard paper for writing",
          cost: 1,
        },
        {
          name: "Sticky Notes",
          description: "For leaving reminders",
          cost: 10,
        },
      ],
      newProduct: {
        name: "",
        description: "",
        cost: null,
      },
      showForm: false,
      notification: {
        show: false,
        message: "",
        type: "",
      },
      addedProduct: null,
      editMode: false,
      editFormData: {
        name: "",
        description: "",
        cost: null,
      },
    };
  },
  methods: {
    confirmDelete(product) {
      Swal.fire({
        title: "Are you sure?",
        text: "Do you want to remove this item from the list?",
        icon: "warning",
        showCancelButton: true,
        confirmButtonColor: "#d33",
        cancelButtonColor: "#3085d6",
        confirmButtonText: "Yes, remove it!",
      }).then((result) => {
        if (result.isConfirmed) {
          this.removeProduct(product);
        }
      });
    },
    removeProduct(product) {
      const index = this.items.findIndex((item) => item.name === product.name);
      if (index !== -1) {
        this.items[index].removing = true;
        setTimeout(() => {
          this.items.splice(index, 1);
        }, 500); 
      }
    },
    addProduct() {
      if (
        !this.newProduct.name ||
        !this.newProduct.description ||
        !this.newProduct.cost
      ) {
        this.showNotification("Please fill in all fields.", "error");
        return;
      }

      this.addedProduct = {
        name: this.newProduct.name,
        description: this.newProduct.description,
        cost: this.newProduct.cost,
      };

      this.items.push(this.addedProduct);

      this.newProduct.name = "";
      this.newProduct.description = "";
      this.newProduct.cost = null;

      this.closeModal();
      this.showNotification("Product added successfully!", "success");

      // Set a timeout to remove the added product after the transition effect
      setTimeout(() => {
        this.addedProduct = null;
      }, 3000);
    },
    formatPrice(price) {
      return "₱" + price;
    },
    openModal() {
      this.showForm = true;
      this.$nextTick(() => {
        document.querySelector(".modal").classList.add("active");
      });
    },
    closeModal() {
      this.showForm = false;
      this.$nextTick(() => {
        document.querySelector(".modal").classList.remove("active");
      });
    },
    cloeseEdit() {
      this.editMode = false;
    },
    showNotification(message, type) {
      this.notification.message = message;
      this.notification.type = type;
      this.notification.show = true;
      setTimeout(() => {
        this.notification.show = false;
      }, 3000);
    },
    openModalForAdding() {
      this.newProduct = {
        name: "",
        description: "",
        cost: null,
      };
      this.editMode = false;
      this.openModal();
    },
    submitForm() {
      if (this.editMode) {
        this.updateProduct();
      } else {
        this.addProduct();
      }
    },
    openModalForEditing(product, index) {
      this.editFormData = {
        index: index,
        name: product.name,
        description: product.description,
        cost: product.cost,
      };
      this.editMode = true;
    },
    submitEdit() {
      const index = this.editFormData.index;
      if (
        index !== undefined &&
        index !== null &&
        index >= 0 &&
        index < this.items.length
      ) {
        // Update existing product
        this.items[index] = { ...this.editFormData };
        this.showNotification("Product updated successfully!", "success");
        this.editMode = false;
      } else {
        this.showNotification("Invalid product index.", "error");
      }
    },
  },
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

.delete-button {
  padding: 8px 20px;
  background-color: #dc3545;
  color: #fff;
  border: none;
  cursor: pointer;
  transition: background-color 0.3s ease;
  border-radius: 5px;
}

.delete-button:hover {
  background-color: #bf858a;
}

.removing {
  opacity: 0;
  transition: opacity 0.5s ease;
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

/* Bouncy Transition effect */
@keyframes bounceIn {
  0% {
    opacity: 0;
    transform: scale(0.1);
  }
  70% {
    transform: scale(1.2);
  }
  100% {
    opacity: 1;
    transform: scale(1);
  }
}

.added-product-transition {
  animation: bounceIn 0.5s ease;
}

.edit-product-popup {
  position: fixed;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  background-color: #f9f9f9; 
  padding: 50px;
  border-radius: 12px; 
  box-shadow: 0 0 15px rgba(0, 0, 0, 0.4); 
  z-index: 999;
  max-width: 400px; 
  width: 90%;
}

.edit-product-popup h2 {
  margin-bottom: 20px;
  font-size: 20px; 
}

.edit-product-popup form div {
  margin-bottom: 20px; 
}

.edit-product-popup label {
  display: block;
  font-weight: bold;
}

.edit-product-popup input[type="text"],
.edit-product-popup input[type="number"],
.edit-product-popup textarea {
  width: 100%;
  padding: 12px; 
  border: 1px solid #ccc;
  border-radius: 8px; 
}

.edit-product-popup input[type="text"]:focus,
.edit-product-popup input[type="number"]:focus,
.edit-product-popup textarea:focus {
  outline: none;
  border-color: #007bff;
}

.edit-product-popup button[type="submit"] {
  width: 100%; 
  padding: 12px 0;
  background-color: #007bff;
  color: #fff;
  border: none;
  border-radius: 8px;
  cursor: pointer;
}

.edit-product-popup button[type="submit"]:hover {
  background-color: #0056b3;
}

</style>
