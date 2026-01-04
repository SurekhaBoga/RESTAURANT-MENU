<template>
  <div class="app">
    <!-- HEADER -->
    <header class="main-header">
      <img src="../assets/logo.jpg" alt="Restaurant Logo" class="logo" />
      <span>Royal Spice Restaurant</span>
    </header>

    <!-- SEARCH -->
    <div class="search-bar">
      <input v-model="search" placeholder="Search food items" />
    </div>

    <!-- MENU -->
    <div class="menu">
      <div v-for="section in filteredMenu" :key="section.title" class="section">
        <h2>{{ section.title }}</h2>

        <div class="items">
          <div v-for="item in section.items" :key="item.name" class="card">
            <div>
              <div class="food-name">{{ item.name }}</div>

              <div v-if="!item.variants" class="price">
                ₹ {{ item.price }}
              </div>

              <select
                v-if="item.variants"
                v-model="item.selected"
                class="variant"
              >
                <option :value="null" disabled>Select</option>
                <option v-for="v in item.variants" :key="v.label" :value="v">
                  {{ v.label }} - ₹{{ v.price }}
                </option>
              </select>
            </div>

            <button class="add" @click="addToCart(item)">ADD</button>
          </div>
        </div>
      </div>
    </div>

    <!-- CART BUTTON -->
    <button class="cart-button" @click="showCart = true">
      CART ({{ cart.length }})
    </button>

    <!-- CART -->
    <div class="cart" v-if="showCart">
      <div class="cart-header">
        <h3>Your Cart</h3>
        <button @click="showCart = false">✕</button>
      </div>

      <div v-if="cart.length === 0" style="color: black">
        Cart is empty
      </div>

      <div v-for="(item, index) in cart" :key="index" class="cart-item">
        <div class="cart-info">
          <div class="cart-name">{{ item.name }}</div>
          <div class="cart-variant" v-if="item.variant">
            {{ item.variant }}
          </div>
        </div>

        <div class="cart-controls">
          <button @click="decrease(index)">−</button>
          <span class="qty">{{ item.quantity }}</span>
          <button @click="increase(index)">+</button>
        </div>
      </div>

      <div class="total">Total: ₹ {{ total }}</div>

      <button class="order" @click="checkout" style="margin-bottom: 5px">
        Place Order
      </button>
      <button class="clear" @click="clearCart">Clear Cart</button>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'
import { menuData } from '../data/menuData'

const search = ref('')
const showCart = ref(false)
const cart = ref([])

/* 👇 menu now comes from external file */
const menu = ref(menuData)

const filteredMenu = computed(() =>
  menu.value.map(section => ({
    ...section,
    items: section.items.filter(i =>
      i.name.toLowerCase().includes(search.value.toLowerCase())
    ),
  }))
)

const total = computed(() =>
  cart.value.reduce((sum, i) => sum + i.price * i.quantity, 0)
)

function addToCart(item) {
  let price = item.price
  let variant = ''

  if (item.variants) {
    if (!item.selected) item.selected = item.variants[0]
    price = item.selected.price
    variant = item.selected.label
  }

  const found = cart.value.find(
    i => i.name === item.name && i.variant === variant
  )

  if (found) found.quantity++
  else {
    cart.value.push({
      name: item.name,
      price,
      variant,
      quantity: 1,
    })
  }

  showCart.value = true
}

function increase(i) {
  cart.value[i].quantity++
}

function decrease(i) {
  cart.value[i].quantity > 1
    ? cart.value[i].quantity--
    : cart.value.splice(i, 1)
}

function clearCart() {
  cart.value = []
}

function checkout() {
  alert(`Order placed successfully!\nTotal ₹${total.value}`)
  clearCart()
  showCart.value = false
}
</script>


<style lang="scss" scoped>
.logo {
  height: 88px;
  width: auto;
}

.main-header {
  font-family: "Micro 5", sans-serif;
  font-size: 52px;
  background-color: #ffffff !important;
  color: rgb(108, 71, 71);
  padding: 18px;
  font-weight: 700;
  text-align: center;
  position: sticky;
  top: 0;
  z-index: 10;

  display: flex;
  align-items: center;
  justify-content: center;
  gap: 12px;
}

.app {
  width: 100%;
  background: #f0f9ff;
  font-family: Arial, sans-serif;
}

/* HEADER */
.header {
  background: #1e40af;
  color: white;
  padding: 20px;
  text-align: center;
}

.cart{
  margin-top: 7.5rem;
}

.cart-header {
  display: flex;
  justify-content: space-between; /* h3 left, button right */
  align-items: center; /* vertically centered */
  padding: 0 10px;
  margin-bottom: 10px;
}

.cart-header h3 {
  color: #000;
  margin: 0;
  font-size: 18px;
}

.cart-header button {
  background: transparent;
  border: none;
  font-size: 20px;
  cursor: pointer;
  color: #000;
}

/* SEARCH */
.search-bar {
  background: #f0f9ff;
  padding: 12px;
  text-align: center;
}
.search-bar input {
  width: 280px;
  padding: 8px;
  font-size: 14px;
}

/* MENU */
.menu {
  padding: 20px;
}
.section h2 {
  color: #1e3a8a;
}
.items {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: 12px;
}

/* CARD */
.card {
  background: white;
  border-left: 6px solid #2563eb;
  padding: 12px;
  display: flex;
  justify-content: space-between;
}
.food-name {
  font-weight: bold;
  color: #000;
}
.price {
  color: #15803d;
}
.add {
  background: #22c55e;
  color: white;
  border: none;
  padding: 6px 14px;
}

/* CART */
.cart-button {
  position: fixed;
  bottom: 20px;
  right: 20px;
  background: #1e40af;
  color: white;
  padding: 14px 20px;
}

.cart {
  position: fixed;
  right: 0;
  top: 0;
  width: 380px;
  height: 100%;
  background: #e0f2fe;
  padding: 15px;
}

.cart-item {
  background: white;
  padding: 12px;
  margin-bottom: 10px;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.cart-name {
  font-weight: bold;
  color: #000;
}

.variant {
  background-color: whitesmoke;
  color: #000;
}

.cart-variant {
  font-size: 12px;
  color: #2563eb;
}

.cart-controls {
  display: flex;
  align-items: center;
  gap: 8px;
}

.cart-controls button {
  background: #2563eb;
  color: white;
  border: none;
  width: 28px;
  height: 28px;
}

.qty {
  font-size: 18px;
  font-weight: bold;
  color: #000;
}

.total {
  font-weight: bold;
  font-size: 18px;
  margin-top: 12px;
  color: #000;
}

.order {
  background: #16a34a;
  color: white;
  width: 100%;
  padding: 10px;
  margin-top: 10px;
}

.clear {
  background: #64748b;
  color: white;
  width: 100%;
  padding: 10px;
}
</style>
