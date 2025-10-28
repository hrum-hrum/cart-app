<script setup>
import { ref, reactive, computed, onBeforeMount } from 'vue'

const lsKey = ref('__cart-app-basket__')

const basket = reactive([
  {
    id: 1,
    name: 'Blue Flower Print Crop Top',
    color: 'Yellow',
    size: 'M',
    price: 29.0,
    quantity: 1,
    imageUrl: './assets/crop-top.png',
  },
  {
    id: 2,
    name: 'Levender Hoodie',
    color: 'Levender',
    size: 'XXL',
    price: 119.0,
    quantity: 1,
    imageUrl: './assets/hoodie.png',
  },
  {
    id: 3,
    name: 'Black Sweatshirt',
    color: 'Black',
    size: 'XXL',
    price: 123.0,
    quantity: 1,
    imageUrl: './assets/crop-top.png',
  },
])

onBeforeMount(() => {
  if (!localStorage.getItem(lsKey.value)) return

  const savedBasket = [...JSON.parse(localStorage.getItem(lsKey.value))]
  if (savedBasket && savedBasket.length) {
    basket.splice(0, basket.length)
    savedBasket.forEach((element) => {
      basket.push(element)
    })
  }
})

const increaseItemQuantity = (item) => {
  item.quantity++
  localStorage.setItem(lsKey.value, JSON.stringify(basket))
}

const decreaseItemQuantity = (item) => {
  if (item.quantity > 0) {
    item.quantity--
    localStorage.setItem(lsKey.value, JSON.stringify(basket))
  }
}

const removeItem = (index) => {
  basket.splice(index, 1)
  localStorage.setItem(lsKey.value, JSON.stringify(basket))
}

const totalPrice = computed(() => {
  const sum = basket.reduce((sum, item) => {
    return sum + item.price * item.quantity
  }, 0)
  const sumInUSD = new Intl.NumberFormat('en-US', {
    style: 'currency',
    currency: 'USD',
  }).format(sum)

  return sumInUSD
})
const totalTax = computed(() => {
  const sum = basket.reduce((sum, item) => {
    return sum + item.price * item.quantity
  }, 0)

  const sumInUSD = new Intl.NumberFormat('en-US', {
    style: 'currency',
    currency: 'USD',
  }).format(sum * 0.1)

  return sumInUSD
})
</script>

<template>
  <div class="container basket">
    <table class="basket-table">
      <thead class="basket-table__header">
        <tr>
          <th>Product Details</th>
          <th>Price</th>
          <th>Quantity</th>
          <th>Subtotal</th>
          <th>Action</th>
        </tr>
      </thead>
      <tbody class="basket-table__body">
        <tr v-for="(item, index) in basket" :key="`product_${item.id}`">
          <td>
            <div class="basket-item">
              <div class="basket-item__image">
                <img :src="item.imageUrl" :alt="item.name" />
              </div>
              <div class="basket-item__info">
                <h2 class="basket-item__info-h2">{{ item.name }}</h2>
                <p class="basket-item__info-p">Color: {{ item.color }}</p>
                <p class="basket-item__info-p">Size: {{ item.size }}</p>
              </div>
            </div>
          </td>
          <td>
            <p class="basket-item__price">${{ item.price }}</p>
          </td>
          <td>
            <div class="basket-item__quantity">
              <button class="quantity-button" @click="decreaseItemQuantity(item)">–</button>
              <input type="number" :value="item.quantity" min="1" />
              <button class="quantity-button" @click="increaseItemQuantity(item)">+</button>
            </div>
          </td>
          <td>
            <p class="basket-item__price">${{ item.price * item.quantity }}</p>
          </td>
          <td>
            <button class="btn btn-delete" @click="removeItem(index)" aria-label="Удалить">
              <svg
                class="w-6 h-6 text-gray-800 dark:text-white"
                aria-hidden="true"
                xmlns="http://www.w3.org/2000/svg"
                width="24"
                height="24"
                fill="none"
                viewBox="0 0 24 24"
              >
                <path
                  stroke="currentColor"
                  stroke-linecap="round"
                  stroke-linejoin="round"
                  stroke-width="2"
                  d="M5 7h14m-9 3v8m4-8v8M10 3h4a1 1 0 0 1 1 1v3H9V4a1 1 0 0 1 1-1ZM6 7h12v13a1 1 0 0 1-1 1H7a1 1 0 0 1-1-1V7Z"
                />
              </svg>
            </button>
          </td>
        </tr>

        <tr v-if="!basket.length">
          <td colspan="5">
            <p class="basket-table__empty">No items</p>
          </td>
        </tr>

        <tr v-if="basket.length">
          <td colspan="5">
            <div class="basket-table__summary">
              <p class="basket-table__total">
                Total <b>{{ totalPrice }}</b>
              </p>
              <p>Tax {{ totalTax }}</p>
            </div>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<style src="./App.css"></style>
