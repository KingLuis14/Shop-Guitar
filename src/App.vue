<script setup>

import { ref, watch } from "vue";
import { db } from "./data/guitarras";
import Guitarra from "./components/Guitarras.vue";
import Header from "./components/Header.vue";
import Footer from "./components/Footer.vue";

const guitarras = ref(db);
const guitarraBanner = ref(db[3]);
const carrito = ref([]);

const carritoStorage = localStorage.getItem('carrito');

if(carritoStorage){
  carrito.value = JSON.parse(carritoStorage);
}

watch(carrito, ()=> {
  localStorage.setItem('carrito', JSON.stringify(carrito.value));
}, {deep: true})

const addCarrito = (guitarra) => {
  const existeCarrito = carrito.value.findIndex((producto) => producto.id === guitarra.id);

  if (existeCarrito >= 0) {
    carrito.value[existeCarrito].cantidad++;
  } else {
    carrito.value.push({ ...guitarra, cantidad: 1 });
  }

};

const incrementarCantidad = (guitarra) => {
  const index = carrito.value.findIndex((producto) => producto.id === guitarra.id);
  carrito.value[index].cantidad++;
};

const decrementarCantidad = (guitarra) => {
  const index = carrito.value.findIndex((producto) => producto.id === guitarra.id);
  if (carrito.value[index].cantidad <= 1) return;
  carrito.value[index].cantidad--;
};

const vaciarCarrito = ()=> carrito.value = [];

const deleteCarrito = (guitarra) => {
  carrito.value = carrito.value.filter((producto) => producto.id !== guitarra.id);
}
</script>

<template>

  <Header :carrito="carrito" :guitarra="guitarraBanner" @incrementar-cantidad="incrementarCantidad"
    @decrementar-cantidad="decrementarCantidad" @add-carrito="addCarrito" @delete-carrito="deleteCarrito" @vaciar-carrito="vaciarCarrito" />
  <main class="container-xl mt-5">
    <h2 class="text-center">Nuestra Colección</h2>

    <div class="row mt-5">
      <Guitarra v-for="guitarra in guitarras" :guitarra="guitarra" @add-carrito="addCarrito" />
    </div>
  </main>

  <Footer />

</template>
