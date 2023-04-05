<template>
    <div class="container" id="app">
        <div class="button-wrapper">
            <button class="button" @click="clearUsers">Отчистить</button>
            <button class="button" @click="showModal = !showModal">Добавить</button>
        </div>

        <AddUserModal v-if="showModal" @close="showModal = false" :users="users" />

        <UserTable :users="users" :columns="columns" />
    </div>
</template>

<script>
import UserTable from './components/UserTable.vue'
import AddUserModal from './components/AddUserModal.vue'
import { LOCAL_STORAGE_USERS_KEY } from './consts'

export default {
  name: 'App',
  components: { UserTable, AddUserModal },
  data: () => ({
    showModal: false,
    users: [],
    columns: [
      { name: 'Имя', key: 'name' },
      { name: 'Телефон', key: 'phone' }
    ]
  }),
  mounted: function () {
    try {
      // LocalStorage может быть изменен со стороны и полученная переменная возможно не распарсится
      // В связи с этим необходимо обработать ошибку и сбросить сломанные данные.
      this.users = JSON.parse(localStorage.getItem(LOCAL_STORAGE_USERS_KEY) || '[]')
    } catch (e) {
      this.clearUsers()
    }
  },
  methods: {
    clearUsers: function () {
      this.users = []
      localStorage.removeItem(LOCAL_STORAGE_USERS_KEY)
    }
  }
}
</script>

<style>
body {
    display: flex;
    flex-direction: column;
    margin-top: 48px;
    align-items: center;
    justify-content: center;
    width: 100%;
    margin: 0;
    padding: 0;
}

.container {
    display: flex;
    flex-direction: column;
    max-width: 1200px;
}

.button-wrapper {
    display: flex;
    justify-content: flex-end;
}

.button {
    padding: 8px;
    margin: 8px;
    margin-right: 0;
    width: 120px;
    border-radius: 8px;
    background: black;
    color: white;
    cursor: pointer;
}
</style>
