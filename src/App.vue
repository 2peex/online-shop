<template>
  <div id="app" class="container">
    <h1 class="title">🛒 Vue Drag & Drop Shop</h1>
    <div class="grid">
      <div class="products">
        <h2 class="section-title">Products</h2>
        <div class="product-list">
          <div v-for="product in products" :key="product.id" draggable="true" @dragstart="startDrag(product)"
            class="product-card">
            <img :src="product.image" class="product-image" alt="product" />
            <span class="product-name">{{ product.name }}</span>
            <span class="product-price">${{ product.price }}</span>
            <button @click="addToCart(product)" class="add-to-cart-btn">Add to Cart</button>
          </div>
        </div>
      </div>
      <div class="cart" @dragover.prevent @drop="onDrop">
        <h2 v-if="totalQuantity > 0" class="section-title">🛒 Cart ({{ totalQuantity }} items)</h2>
        <ul v-if="cart.length">
          <li v-for="item in cart" :key="item.id" class="cart-item">
            <div class="cart-item-info">
              <img :src="item.image" class="cart-item-image" alt="item" />
              <span>{{ item.name }} - ${{ item.price }} (x{{ item.quantity }})</span>
            </div>
            <button @click="removeFromCart(item.id)" class="remove-btn">Remove</button>
          </li>
        </ul>
        <p v-else class="empty-cart">Cart is empty</p>
        <div class="total-price">Total: ${{ totalPrice }}</div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed, createApp } from 'vue';

const products = ref([
  { id: 1, name: "Laptop", price: 1000, image: "../public/laptop.jpg" },
  { id: 2, name: "Phone", price: 500, image: "../public/phone.jpg" },
  { id: 3, name: "Headphones", price: 200, image: "../public/headphones.jpg" },
  { id: 4, name: "Smartwatch", price: 300, image: "../public/smartw.jpg" },
  { id: 6, name: "Smartwatch", price: 340, image: "../public/smartw.jpg" },
  { id: 5, name: "Tablet", price: 700, image: "../public/tablet.jpg" },
 
]);

const cart = ref([]);
let draggedProduct = ref(null);

function startDrag(product) {
  draggedProduct.value = product;
}

function onDrop() {
  if (draggedProduct.value) {
    addToCart(draggedProduct.value);
    draggedProduct.value = null;
  }
}

function addToCart(product) {
  const existingItem = cart.value.find(item => item.id === product.id);
  if (existingItem) {
    existingItem.quantity++;
  } else {
    cart.value.push({ ...product, quantity: 1 });
  }
}

function removeFromCart(productId) {
  const index = cart.value.findIndex(item => item.id === productId);
  if (index !== -1) {
    if (cart.value[index].quantity > 1) {
      cart.value[index].quantity--;
    } else {
      cart.value.splice(index, 1);
    }
  }
}

const totalQuantity = computed(() => {
  return cart.value.reduce((sum, item) => sum + item.quantity, 0);
});

const totalPrice = computed(() => {
  return cart.value.reduce((sum, item) => sum + item.price * item.quantity, 0);
});
</script>


<style scoped>
.container {
  max-width: 1200px;
  margin: 0 auto;
  padding: 20px;
}

.title {
  text-align: center;
  font-size: 2.5rem;
  font-weight: bold;
  margin-bottom: 20px;
}

.grid {
  display: grid;
  grid-template-columns: 2fr 1fr;
  gap: 20px;
}

.products,
.cart {
  background-color: white;
  padding: 10px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
  border-radius: 8px;
  color: #2c2c2c;
}

.section-title {
  font-size: 1.5rem;
  font-weight: 400;
  margin-bottom: 15px;
}

.product-list {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 20px;
}

.product-card {
  padding: 15px;
  border-radius: 8px;
  display: flex;
  flex-direction: column;
  align-items: start;
  cursor:grab;
  transition: background-color 0.3s ease;
}

.product-card:hover {
  background-color: #55e5b8b5;
}

.product-image {
  width: 360px;
  height: 220px;
  object-fit: cover;
  margin-bottom: 10px;
  border-radius: 8px;

}

.product-name {
  font-weight: 400;
  
}

.product-price {
  color: #272727;
  margin-bottom: 10px;
}

.add-to-cart-btn {
  background-color: #3b3b3b;
  color: white;
  padding: 8px 16px;
  border-radius: 4px;
  cursor: pointer;
  border: none;
  transition: background-color 0.3s;
}

.add-to-cart-btn:hover {
  background-color: #0056b3;
}

.cart {
  background-color: #f9f9f9;
}

.cart-item {
  display: flex;
  justify-content: space-between;
  padding: 10px;
  background-color: #e0f7fa;
  border-radius: 6px;
  margin-bottom: 10px;
}

.cart-item-info {
  display: flex;
  align-items: center;
}

.cart-item-image {
  width: 40px;
  height: 40px;
  object-fit: cover;
  margin-right: 10px;
}

.remove-btn {
  background-color: #dc3545;
  color: white;
  padding: 5px 10px;
  border-radius: 4px;
  cursor: pointer;
  border: none;
  transition: background-color 0.3s;
}

.remove-btn:hover {
  background-color: #c82333;
}

.empty-cart {
  color: #888;
  font-style: italic;
}

.total-price {
  font-size: 1rem;
  font-weight: 400;
  margin-top: 20px;
}
</style>