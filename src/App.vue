<template>
  <div>
    <header>
      <nav>
        <ul>
          <li><button @click="activeTab = 'todos'">Todos</button></li>
          <li><button @click="fetchUsers">Post</button></li>
        </ul>
      </nav>
    </header>

    <main id="content">
      <!-- Konten akan ditampilkan di sini -->
      <div v-if="activeTab === 'todos'">
        <h2>Todos</h2>
        <div class="todo-component">
          <div class="container todo-container">
            <!-- Your to-do list content here -->
            <h1 style="text-align: center;">To do List Inola 𖹭</h1>
            <div style="display: flex;">
                <input type="text" v-model="newTask" placeholder="Add new list...">
                <button @click="addTask">Add</button>
            </div>

            <div v-for="(task, index) in incompleteTasks" :key="index" class="task" :class="{ 'completed': task.completed }">
                <input type="checkbox" v-model="task.completed" class="checkbox">
                <div v-if="!task.editing" class="task-text">{{ task.title }}</div>
                <div v-else class="edit-mode">
                    <input v-model="task.title" class="edit-input">
                    <div class="button-group">
                        <button class="update-btn" @click="updateTask(index)">Update</button>
                        <button class="cancel-btn" @click="cancelTask(index)">Cancel</button>
                    </div>
                </div>
                <div v-if="!task.editing" class="button-group">
                    <button class="edit-btn" @click="editTask(index)">Edit</button>
                    <button class="delete-btn" @click="deleteTask(index)">Delete</button>
                </div>
            </div>
            

            <button class="filter-btn" @click="showIncomplete = !showIncomplete">
                {{ showIncomplete ? 'Show All List' : 'Show Incomplete List Only' }}
            </button>
          </div>
        </div>
      </div>

      <div v-else-if="activeTab === 'posts'">
        <h2>User Posts</h2>
        <div class="post-component">
          <select v-model="selectedUser">
            <option v-for="user in users" :key="user.id" :value="user.id">{{ user.name }}</option>
          </select>
          <div class="post-container">
            <div v-if="selectedUser !== null" v-for="post in userPosts" :key="post.id" class="post">
              <h3>{{ post.title }}</h3>
              <p>{{ post.body }}</p>
            </div>
            <div v-if="userPosts.length === 0" class="no-posts">
              No posts available for selected user.
            </div>
          </div>
        </div>
      </div>

    </main>
  </div>
</template>

<script>
export default {
  data() {
    return {
      activeTab: 'todos',
      tasks: [],
      newTask: '',
      showIncomplete: true,
      users: [],
      selectedUser: null,
      userPosts: []
    };
  },
  methods: {
    addTask() {
      if (this.newTask.trim() !== '') {
        this.tasks.push({ title: this.newTask, completed: false });
        this.newTask = '';
      }
    },
    cancelTask(index) {
      this.tasks[index].editing = false;
    },
    editTask(index) {
      this.tasks[index].editing = true;
    },
    updateTask(index) {
      this.tasks[index].editing = false;
    },
    deleteTask(index) {
      this.tasks.splice(index, 1);
    },
    fetchUsers() {
      fetch('https://jsonplaceholder.typicode.com/users')
        .then(response => response.json())
        .then(users => {
          this.users = users;
          this.activeTab = 'posts';
        })
        .catch(error => console.error('Error fetching users:', error));
    },
    fetchUserPosts() {
      fetch(`https://jsonplaceholder.typicode.com/posts?userId=${this.selectedUser}`)
        .then(response => response.json())
        .then(posts => {
          this.userPosts = posts;
        })
        .catch(error => console.error('Error fetching user posts:', error));
    }
  },
  watch: {
    selectedUser() {
      this.fetchUserPosts();
    }
  },
  computed: {
    incompleteTasks() {
      if (this.showIncomplete) {
        return this.tasks.filter(task => !task.completed);
      } else {
        return this.tasks;
      }
    }
  }
};
</script>

<style>

body {
    font-family: cursive;
    background-color: #A3966A;
    margin: 0;
    padding: 0;
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100vh;
  }

header {
    background-color: #482E1D;
    padding: 10px;
    width: 100%;
    display: flex;
    justify-content: center;
    border-radius: 15px;
}

nav ul {
    list-style: none;
    padding: 0;
    margin: 0;
    display: flex;
}

nav li {
    margin: 0 10px;
}

nav button {
    background-color: #F0DAAE;
    color: #482E1D;
    border: none;
    padding: 10px 20px;
    cursor: pointer;
    font-family: cursive;
    border-radius: 15px;
}

nav button:hover {
    background-color: #90553C;
    color: #F0DAAE;
}

main {
    width: 100%;
    width: 600px;
    padding: 20px;
    border-radius: 10px;
    font-family:cursive;
  }

.todo-container, .post-container {
    padding: 20px;
    border-radius: 10px;
    box-shadow: 0px 0px 10px #482E1D;
    background-color: #895D2B;
    font-family: cursive;
}

.todo-container {
    margin-bottom: 20px;
    height:auto;
}

.todo-container h2, .post-container h2 {
    color: #482E1D;
}

.todo-component input[type="text"] {
    width: calc(100% - 95px);
    padding: 10px;
    border: #482E1D;
    border-radius: 5px 0 0 5px;
    outline: none;
    color: #482E1D;
}

.todo-component button {
    padding: 10px 15px;
    margin: 0 5px;
    background-color: #482E1D;
    color: #F0DAAE;
    border: none;
    border-radius: 5px;
    cursor: pointer;
    outline: none;
}

.todo-component button:hover {
    background-color: #90553C;
}

.task {
    display: flex;
    align-items: center;
    margin: 10px 0;
    padding: 10px;
    border-radius: 5px;
    background-color: #F0DAAE;
    color: #482E1D;
}

.task input[type="checkbox"] {
    margin-right: 10px;
}

.task-text {
    flex: 1;
    cursor: pointer;
    color: #482E1D;
    font-weight: bold;
}

.edit-input {
    flex: 1;
    margin-right: 10px;
    color: #482E1D;
}

.edit-mode {
    display: flex;
    align-items: center;
}

.button-group {
    display: flex;
    align-items: center;
}

.edit-btn, .delete-btn, .cancel-btn {
    margin-left: 5px;
}

.update-btn {
    margin-right: 5px;
}

.completed .task-text {
    text-decoration: line-through;
    opacity: 0.6;
}

.filter-btn {
    margin-top: 20px;
    padding: 10px 15px;
    background-color: #482E1D;
    color: #F0DAAE;
    border: none;
    border-radius: 5px;
    cursor: pointer;
    outline: none;
}

.filter-btn:hover {
    background-color: #90553C;
}

.checkbox {
    -webkit-appearance: none;
    -moz-appearance: none;
    appearance: none;
    width: 15px;
    height: 15px;
    border: 1px solid #482E1D;
    border-radius: 5px;
    margin-right: 5px;
}

.checkbox:checked {
    background-color: #90553C;
}

.post-component select {
    width:auto;
    padding: 2px;
    border-radius: 5px;
    border: 1px solid #482E1D;
    margin-bottom: 20px;
    font-family: cursive;
    color: #482E1D;
}

.post {
    background-color: #F0DAAE;
    padding: 20px;
    border-radius: 10px;
    margin-bottom: 10px;
}

.post h3 {
    color: #3f481d;
}

.no-posts {
    text-align: center;
    color: #482E1D;
}

.post-container {
  height: 10px;
  width:100%;
}
</style>
