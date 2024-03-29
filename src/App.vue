<template>
  <div id="app">
    <div id="createField">
      <input type="text" v-model="newItemName" placeholder="Enter name">
      <button @click="createItem">Create</button>
    </div>

    <div v-for="item in data" :key="item.id" class="itemTile">
      <div>
        <span v-if="item.editing">
          <input type="text" v-model="item.newName">
          <button @click="saveEdit(item)">Save</button>
          <button @click="cancelEdit(item)">Cancel</button>
        </span>
        <span v-else>
          {{ item.name }}
          <button @click="editItem(item)">Edit</button>
        </span>
      </div>
      <div>Updated at: {{ formatDate(item.updated_at) }}</div>
      <div>Created at: {{ formatDate(item.created_at) }}</div>
      <button @click="deleteItem(item.id)">Delete</button>


    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      data: null,
      newItemName: ''
    };
  },
  mounted() {
    this.fetchData();
  },
  methods: {
    fetchData() {
      axios.get('http://template.test/api/things')
        .then(response => {
          this.data = response.data.map(item => ({
            ...item,
            editing: false,
            newName: ''
          }));
        })
        .catch(error => {
          console.error('Error fetching data:', error);
        });
    },
    createItem() {
      axios.post('http://template.test/api/thing', { name: this.newItemName })
        .then(() => {
          this.fetchData();
          this.newItemName = '';
        })
        .catch(error => {
          console.error('Error creating item:', error);
        });
    },
    deleteItem(id) {
      axios.delete(`http://template.test/api/thing/${id}`)
        .then(() => {
          this.fetchData();
        })
        .catch(error => {
          console.error('Error deleting item:', error);
        });
    },
    editItem(item) {
      item.editing = true;
      item.newName = item.name;
    },
    saveEdit(item) {
      axios.put(`http://template.test/api/thing/${item.id}`, { name: item.newName })
        .then(() => {
          this.fetchData();
        })
        .catch(error => {
          console.error('Error updating item:', error);
        });
    },
    cancelEdit(item) {
      item.editing = false;
    },
    formatDate(dateString) {
      const date = new Date(dateString);
      const day = date.getDate();
      const month = date.getMonth() + 1;
      const year = date.getFullYear();
      const hours = date.getHours();
      const minutes = date.getMinutes();
      
      const formattedDate = `${day}/${month}/${year} ${hours}:${minutes}`;
      
      return formattedDate;
    }
  }
};
</script>

<style>
.itemTile, #createField{
  margin-bottom: 10px;
}
.itemTile{
  padding: 5px;
  border: 1px solid black;
  border-radius: 5px;
}
</style>
