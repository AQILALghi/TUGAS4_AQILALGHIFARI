<template>
  <div class="container">
    <header>
      <nav>
        <ul>
          <li @click="selectedMenu = 'Post'" :class="{ active: selectedMenu === 'Post' }" class="menu-item">Post</li>
          <li @click="selectedMenu = 'Todos'" :class="{ active: selectedMenu === 'Todos' }" class="menu-item">Todos</li>
        </ul>
      </nav>
    </header>
    <div class="content">
      <div v-if="selectedMenu === 'Post'" class="post-section">
        <select v-model="selectedUser" @change="fetchPosts" class="user-select">
          <option v-for="user in users" :key="user.id" :value="user.id">{{ user.name }}</option>
        </select>
        <div v-if="posts.length > 0" class="post-list">
          <h2>Postingan User: {{ selectedUserName }}</h2>
          <ul>
            <li v-for="post in posts" :key="post.id" class="post-item">
              <h3>{{ post.title }}</h3>
              <p>{{ post.body }}</p>
            </li>
          </ul>
        </div>
        <div v-else>
          <p>Loading...</p>
        </div>
      </div>
      <div v-else-if="selectedMenu === 'Todos'" class="todos-section">
        <!-- Menggunakan komponen Todos dan melewatkan prop todos -->
        <Todos :todos="todos">
          <!-- Ini adalah slot default untuk komponen Todos -->
          <template v-slot:default="{ todo }">
            <p>{{ todo.title }}</p>
            <!-- Anda dapat menambahkan elemen atau logika tambahan di sini -->
          </template>
        </Todos>
      </div>
    </div>
  </div>
</template>

<script>
import Todos from './Todos.vue'; // Sesuaikan dengan path file komponen Todos Anda

export default {
  components: {
    Todos
  },
  data() {
    return {
      selectedMenu: 'Post', // Default menu selection
      users: [],
      selectedUser: null,
      selectedUserName: '',
      posts: [],
      todos: [] // Isi dengan data Todos yang telah Anda dapatkan
    };
  },
  methods: {
    fetchUsers() {
      fetch('https://jsonplaceholder.typicode.com/users')
        .then(response => response.json())
        .then(data => {
          this.users = data;
        })
        .catch(error => {
          console.error('Error fetching users:', error);
        });
    },
    fetchPosts() {
      // Mengambil postingan berdasarkan user yang dipilih
      fetch(`https://jsonplaceholder.typicode.com/posts?userId=${this.selectedUser}`)
        .then(response => response.json())
        .then(data => {
          this.posts = data;
          // Mendapatkan nama user yang dipilih
          const selectedUser = this.users.find(user => user.id === parseInt(this.selectedUser));
          if (selectedUser) {
            this.selectedUserName = selectedUser.name;
          }
        })
        .catch(error => {
          console.error('Error fetching posts:', error);
        });
    }
  },
  created() {
    this.fetchUsers();
  }
};
</script>

<style scoped>
.container {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  height: 100vh;
}

.content {
  max-width: 800px;
}

header {
  background-color: #333;
  padding: 10px 0;
}

nav ul {
  list-style: none;
  padding: 0;
  margin: 0;
}

nav ul li {
  display: inline-block;
  margin-right: 20px;
  color: #fff;
  cursor: pointer;
}

nav ul li.active {
  font-weight: bold;
}

.menu-item {
  padding: 8px 12px;
  border-radius: 4px;
  background-color: #5a5a5a; /* Warna tombol netral */
  transition: background-color 0.3s ease; /* Efek transisi saat hover */
}

.menu-item:hover {
  background-color: #6e6e6e; /* Warna tombol sedikit lebih gelap saat hover */
}

.post-section,
.todos-section {
  margin-top: 20px;
}

.user-select {
  margin-bottom: 10px;
}

.post-list {
  background-color: #f4f4f4;
  padding: 10px;
  border-radius: 8px;
}

.post-item {
  margin-bottom: 20px;
}

.post-item h3 {
  margin-bottom: 5px;
}

.post-item p {
  margin-top: 5px;
}
</style>
