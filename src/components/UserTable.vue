<template>
    <div class="table">
        <div class="header">
            <div v-for="column in columns" :key="column.key" class="column" @click="sortByKey(column.key)">
                <i v-if="!!orders[column.key]">{{ orders[column.key] > 0 ? '▲' : '▼' }}</i>
                {{ column.name }}
            </div>
        </div>

        <UserRow v-for="user in users" :user="user" :key="user.id" :level="0" />
    </div>
</template>

<script>
import UserRow from './UserRow.vue'

export default {
  components: { UserRow },
  props: ['columns', 'users'],
  data: () => ({
    orders: {}
  }),
  methods: {
    resetOtherOrders: function (key) {
      Object.keys(this.orders).forEach(
        (orderKey) => (this.orders[orderKey] = orderKey === key ? this.orders[orderKey] : null)
      )
    },

    sortByKey: function (key) {
      // Сбрасываем сохраненный порядок для других колонок.
      // Итерация на основе объекта orders на случай возможного добавляения других колонок.
      this.resetOtherOrders(key)

      if (!this.orders[key]) {
        this.orders[key] = 1
      } else {
        this.orders[key] *= -1
      }

      // В идеале обеспечить соотвествие колонок и полей в данных пользователей с помощью модели или интерфейса.
      this.sortUsers(this.users, key, this.orders[key])
    },

    sortUsers: function (users, key, order) {
      if (users.length === 1 && users[0].subordinates) {
        this.sortUsers(users[0].subordinates, key, order)
      }

      users.sort((a, b) => {
        if (a.subordinates) {
          this.sortUsers(a.subordinates, key, order)
        }

        if (b.subordinates) {
          this.sortUsers(b.subordinates, key, order)
        }

        return a[key].localeCompare(b[key]) * order
      })
    }
  }
}
</script>

<style>
.table {
    display: flex;
    flex-direction: column;
    gap: 10px;
    border: 2px solid black;
    border-radius: 4px;
    background: white;
    padding: 2px;
    min-width: 300px;
}

.column {
    display: flex;
    justify-content: center;
    flex: 50%;
    padding: 4px;
    cursor: pointer;
    background: black;
    color: white;
}

.header {
    display: flex;
    gap: 2px;
}
</style>
