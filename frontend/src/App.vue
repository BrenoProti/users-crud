<template>
  <div class="users">
    <div class="container">
      <section>
        <h5 class="title">Create new User</h5>
        <form @submit.prevent="createUser">
          <input required type="text" placeholder="Name" v-model="form.name" />
          <input
            required
            type="email"
            placeholder="E-mail"
            v-model="form.email"
          />
          <button type="submit">Send</button>
        </form>
      </section>
      <section>
        <h5 class="title">Users List</h5>

        <ul>
          <transition-group
            enter-active-class="animate__animated animate__bounceIn"
            leave-active-class="animate__animated animate__bounceOut"
            :appear="true"
            mode="out-int"
          >
            <li v-for="user in users" :key="user.id">
              <p>{{ user.name }}</p>
              <small>{{ user.email }}</small>
              <a class="destroy" @click="deleteUser(user.id)"></a>
            </li>
          </transition-group>
        </ul>
      </section>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue';
import axios from '@/utils/axios';
import 'animate.css';

interface User {
  id: string;
  email: string;
  name: string;
}

export default defineComponent({
  data() {
    return {
      users: [] as User[],
      form: {
        name: '',
        email: ''
      }
    };
  },
  created() {
    this.fetchUsers();
  },
  methods: {
    async fetchUsers() {
      try {
        const { data } = await axios.get('/users');
        this.users = data;
      } catch (error) {
        console.warn(error);
      }
    },
    async createUser() {
      try {
        const { data } = await axios.post('/users', this.form);
        this.users.push(data);
        this.form.name = '';
        this.form.email = '';
      } catch (error) {
        console.warn(error);
      }
    },
    async deleteUser(id: User['id']) {
      try {
        await axios.delete(`/users/${id}`).then(() => this.fetchUsers());
      } catch (error) {
        console.warn(error);
      }
    }
  }
});
</script>

<style scoped>
li {
  transition: all 500ms;
}
.container {
  margin: 4rem auto;
  max-width: 500px;
  width: 90%;
  display: grid;
  grid-gap: 2.5rem;
}

.title {
  font-size: 1rem;
  font-weight: 500;
  margin: 0.7rem 0;
}

form {
  display: grid;
  grid-gap: 1rem;
}

input {
  background: transparent;
  border: 1px solid #999fc6;
  border-radius: 1rem;
  padding: 0.6rem;
  outline: none;
  color: #e1e8ef;
}

input::placeholder {
  color: #999fc6;
}

button {
  background-color: #2d6cea;
  color: #e1e8ef;
  border: none;
  border-radius: 1rem;
  padding: 0.6rem 1.5rem;
  width: max-content;
  transition: all 0.3s linear;
  outline: none;
  cursor: pointer;
  box-shadow: 0 0 5px 3px rgba(45, 108, 234, 0.3);
}

button:hover {
  background-color: #1b5cdc;
}

p {
  margin: 0;
}

ul {
  padding: 0;
  margin: 0;
  display: grid;
  grid-gap: 1rem;
}

li {
  background-color: #2b3a4e;
  padding: 1.2rem 1rem;
  border-radius: 1rem;
  position: relative;
  list-style: none;
  color: #8b98a8;
}

.destroy {
  background-color: #d53e6b;
  width: 24px;
  height: 24px;
  border-radius: 0.5rem;
  cursor: pointer;
  transition: all 0.2s linear;
  position: absolute;
  top: 50%;
  transform: translateY(-50%);
  right: 1.3rem;
}

.destroy:before,
.destroy:after {
  content: '';
  width: 3px;
  height: 13px;
  background-color: #ececf6;
  border-radius: 1rem;
  position: absolute;
  top: 50%;
  left: 50%;
}

.destroy:before {
  transform: translate(-50%, -50%) rotate(45deg);
}

.destroy:after {
  transform: translate(-50%, -50%) rotate(130deg);
}

.destroy:hover {
  background-color: #984848;
}
</style>
