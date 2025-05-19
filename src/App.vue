<script setup>
import { ref, reactive, onMounted, watch } from 'vue';
import { db } from './data/guitarras.js';
import Guitarra from './components/Guitarra.vue';
import Header from './components/Header.vue';
import Footer from './components/Footer.vue';
const guitarras = ref([])
const carrito = ref([])
const guitarra = ref([])
watch(carrito, (nuevoCarrito) => {
  guardarLocalStorage()
}, { deep: true })
onMounted(() => {
  guitarras.value = db
  guitarra.value = db[3]
  const carritoLocalStorage = localStorage.getItem('carrito')
  if (carritoLocalStorage) {
    carrito.value = JSON.parse(carritoLocalStorage)
  }
})
const guardarLocalStorage = () => {
  localStorage.setItem('carrito', JSON.stringify(carrito.value))
}
const agregarCarrito = (guitarra) => {
  const existeCarrito = carrito.value.findIndex(producto => producto.id === guitarra.id)
  if (existeCarrito !== -1) {
    carrito.value[existeCarrito].cantidad++
    return
  }
  guitarra.cantidad = 1
  carrito.value.push(guitarra)
  guardarLocalStorage()
}
const decrementarCantidad = (id) => {
  const guitarra = carrito.value.find(producto => producto.id === id)
  if (guitarra.cantidad === 1) {
    const index = carrito.value.indexOf(guitarra)
    carrito.value.splice(index, 1)
    return
  }
  guitarra.cantidad--
}
const incrementarCantidad = (id) => {
  const guitarra = carrito.value.find(producto => producto.id === id)
  if (guitarra.cantidad >= 5) {
    return
  }
  guitarra.cantidad++
}
const eliminarProducto = (id) => {
  carrito.value = carrito.value.filter(producto => producto.id !== id)
}
const vaciarCarrito = () => {
  carrito.value = []
}
</script>
<template>
  <Header :carrito="carrito" :guitarra="guitarra" @incrementar-cantidad="incrementarCantidad"
    @decrementar-cantidad="decrementarCantidad" @agregar-carrito="agregarCarrito" @eliminar-producto="eliminarProducto"
    @vaciar-carrito="vaciarCarrito" />
  <main class="container-xl mt-5">
    <h2 class="text-center">Nuestra Colección</h2>
    <div class="row mt-5">
      <Guitarra v-for="guitarra in guitarras" v-bind:guitarra="guitarra" @agregar-carrito="agregarCarrito" />
    </div>
  </main>
  <Footer />
</template>
