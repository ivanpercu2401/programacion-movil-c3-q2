
# Explicación del Código de la Aplicación Biblioteca

Este documento proporciona una explicación línea por línea del código de Vue.js para una aplicación de biblioteca construida con Ionic.

## Sección de Template

```html
<template>
  <ion-page>
    <ion-header>
      <ion-toolbar>
        <ion-title>Biblioteca</ion-title>
      </ion-toolbar>
    </ion-header>
```
- `<template>`: Este es el elemento raíz para el componente de Vue.js.
- `<ion-page>`: Representa una sola página en la aplicación Ionic.
- `<ion-header>`: La sección de encabezado de la página, típicamente contiene navegación y títulos.
- `<ion-toolbar>`: Un contenedor para los elementos del encabezado.
- `<ion-title>`: Muestra el título de la página.

```html
    <ion-content>
      <!-- Barra de búsqueda -->
      <ion-item>
        <ion-label>Buscar</ion-label>
        <ion-input placeholder="Título o autor" v-model="searchQuery"></ion-input>
      </ion-item>
```
- `<ion-content>`: El área de contenido principal de la página.
- `<ion-item>`: Un componente utilizado para agrupar etiquetas y elementos de entrada.
- `<ion-label>`: Muestra una etiqueta para el campo de entrada.
- `<ion-input>`: Un campo de entrada para ingresar consultas de búsqueda, enlazado a `searchQuery` usando `v-model`.

```html
      <!-- Segmento para vista de lista y favoritos -->
      <ion-segment v-model="currentSegment">
        <ion-segment-button value="list">Lista</ion-segment-button>
        <ion-segment-button value="favorites">Favoritos</ion-segment-button>
      </ion-segment>
```
- `<ion-segment>`: Un control segmentado que permite cambiar entre dos vistas (lista y favoritos).
- `<ion-segment-button>`: Botones dentro del segmento, enlazados a `currentSegment`.

```html
      <!-- Lista de libros o favoritos -->
      <ion-list>
        <ion-item v-for="(book, index) in filteredBooks" :key="index" @click="toggleFavorite(book)">
          <ion-label>
            <h2>{{ book.title }}</h2>
            <p>{{ book.author }}</p>
          </ion-label>
          <ion-badge :color="book.isAvailable ? 'success' : 'danger'">
            {{ book.isAvailable ? 'Disponible' : 'Prestado' }}
          </ion-badge>
        </ion-item>
      </ion-list>
```
- `<ion-list>`: Un contenedor para una lista de elementos.
- `v-for`: Una directiva para iterar sobre `filteredBooks` y mostrar cada libro.
- `@click`: Manejador de eventos para alternar el estado de favorito del libro al hacer clic.
- `<ion-badge>`: Muestra el estado de disponibilidad del libro.

```html
      <!-- Botón para agregar un libro -->
      <ion-fab vertical="bottom" horizontal="end" slot="fixed">
        <ion-fab-button @click="showAddBookModal = true">
          <ion-icon name="add"></ion-icon>
        </ion-fab-button>
      </ion-fab>
```
- `<ion-fab>`: Un botón de acción flotante posicionado en la esquina inferior derecha.
- `<ion-fab-button>`: El botón que abre el modal para agregar un nuevo libro.

```html
      <!-- Modal para agregar libro -->
      <ion-modal :isOpen="showAddBookModal" @ionModalDidDismiss="showAddBookModal = false">
        <ion-header>
          <ion-toolbar>
            <ion-title>Nuevo Libro</ion-title>
            <ion-button slot="end" @click="showAddBookModal = false">Cerrar</ion-button>
          </ion-toolbar>
        </ion-header>
        <ion-content>
          <ion-item>
            <ion-label>Título</ion-label>
            <ion-input placeholder="Título" v-model="newBook.title"></ion-input>
          </ion-item>
          <ion-item>
            <ion-label>Autor</ion-label>
            <ion-input placeholder="Autor" v-model="newBook.author"></ion-input>
          </ion-item>
          <ion-button expand="block" @click="addBook">Agregar Libro</ion-button>
        </ion-content>
      </ion-modal>
```
- `<ion-modal>`: Una ventana modal que aparece para agregar un nuevo libro, alternada por `showAddBookModal`.
- `@ionModalDidDismiss`: Manejador de eventos para establecer la visibilidad del modal en falso cuando se cierra.
- Campos de entrada para el título y autor del libro, enlazados al objeto `newBook`.

```html
      <!-- Toast para confirmación -->
      <ion-toast :isOpen="showToast" message="Libro agregado" duration="1500" @didDismiss="showToast = false" />
    </ion-content>
  </ion-page>
</template>
```
- `<ion-toast>`: Una pequeña notificación que aparece después de agregar un libro.

## Sección de Script Setup

```javascript
<script setup>
import { ref, computed } from 'vue';
import {
  IonContent, IonHeader, IonPage, IonTitle, IonToolbar, IonItem, IonLabel, IonInput,
  IonList, IonSegment, IonSegmentButton, IonFab, IonFabButton, IonModal, IonToast, IonButton,
  IonIcon, IonBadge
} from '@ionic/vue';
```
- Importa los componentes y funciones necesarios de Vue e Ionic.

```javascript
const searchQuery = ref('');
const currentSegment = ref('list');
const books = ref([
  { title: '1984', author: 'George Orwell', isAvailable: true },
  { title: 'Cien años de soledad', author: 'Gabriel García Márquez', isAvailable: false }
]);
const favoriteBooks = ref([]);
const showAddBookModal = ref(false);
const showToast = ref(false);
const newBook = ref({ title: '', author: '', isAvailable: true });
```
- Declara variables reactivas utilizando `ref` para consultas de búsqueda, segmento actual, lista de libros, etc.

```javascript
const filteredBooks = computed(() => {
  const list = currentSegment.value === 'list' ? books.value : favoriteBooks.value;
  return list.filter(book =>
    book.title.toLowerCase().includes(searchQuery.value.toLowerCase()) ||
    book.author.toLowerCase().includes(searchQuery.value.toLowerCase())
  );
});
```
- `filteredBooks`: Una propiedad computada que devuelve la lista de libros filtrada por la consulta de búsqueda.

```javascript
function toggleFavorite(book) {
  const index = favoriteBooks.value.findIndex(favBook => favBook.title === book.title);
  if (index !== -1) {
    favoriteBooks.value.splice(index, 1);
  } else {
    favoriteBooks.value.push(book);
  }
}
```
- `toggleFavorite`: Una función para agregar o eliminar libros de la lista de favoritos.

```javascript
function addBook() {
  books.value.push({ ...newBook.value });
  newBook.value = { title: '', author: '', isAvailable: true };
  showAddBookModal.value = false;
  showToast.value = true;
}
</script>
```
- `addBook`: Una función para agregar un nuevo libro a la lista de libros y restablecer los campos de entrada.

## Sección de Estilo

```css
<style scoped>
ion-item {
  margin-bottom: 10px;
}
</style>
```
- Estilos para `ion-item`, agregando margen inferior para espaciado.


