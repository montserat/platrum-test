<template>
    <div class="modal-mask" @click.self="handleClose">
        <div class="modal-wrapper">
            <div class="modal-container">
                <form @submit.prevent="handleSubmit">
                    <div class="header-wrapper">
                        <span class="header">Добавление пользователя</span>
                        <button class="close-button" type="button" @click="handleClose">X</button>
                    </div>

                    <div class="form-wrapper">
                        <div class="field-wrapper">
                            <label for="name">Имя</label>
                            <input
                                placeholder="Введите имя пользователя"
                                required
                                id="name"
                                type="text"
                                v-model="form.name"
                            />
                        </div>

                        <div class="field-wrapper">
                            <label for="phone">Телефон</label>
                            <!-- Для валидации поля необходим дополнительный regex или библиотека для работы с различными форматами номеров. -->
                            <!-- Убрана проверка на длинну и паттерн из демо для упрощения тестирования.-->
                            <input
                                placeholder="Введите номер телефона"
                                id="phone"
                                required
                                type="tel"
                                v-model="form.phone"
                                maxlength="13"
                            />
                        </div>

                        <div class="field-wrapper">
                            <label for="superior">Начальник</label>
                            <select name="superior" id="superior" @change="form.superiorId = $event.target.value">
                                <option value="#"></option>
                                <option v-for="user in allUsers" :value="user.id" :key="user.id">
                                    {{ user.name }}
                                </option>
                            </select>
                        </div>
                    </div>

                    <div class="actions-wrapper">
                        <button type="submit" class="add-button">Сохранить</button>
                    </div>
                </form>
            </div>
        </div>
    </div>
</template>

<script>
import { LOCAL_STORAGE_USERS_KEY } from '../consts.js'

export default {
  props: {
    users: Array
  },
  data: () => ({
    allUsers: [],
    form: {
      name: '',
      phone: '',
      superiorId: null,
      id: Math.random().toString(36).substring(2, 9)
    }
  }),
  methods: {
    handleClose: function () {
      this.$emit('close')
    },

    addUser: function () {
      const { name, phone, id, superiorId } = this.form
      const newUser = { name, phone, id, subordinates: [] }

      if (!superiorId) {
        this.users.push(newUser)
      } else {
        let superiorUser = this.allUsers.find((user) => user.id === superiorId)

        if (superiorUser) {
          superiorUser.subordinates.push(newUser)
        }
      }

      localStorage.setItem(LOCAL_STORAGE_USERS_KEY, JSON.stringify(this.users))
    },

    handleSubmit: function () {
      this.addUser()
      this.handleClose()
    },

    flatUsers: function (users) {
      return users.reduce(
        (result, user) =>
          result.concat(user.subordinates ? [user, ...this.flatUsers(user.subordinates)] : user),
        []
      )
    }
  },
  mounted: function () {
    this.allUsers = this.flatUsers(this.users)
  }
}
</script>

<style scoped>
.modal-mask {
    position: fixed;
    z-index: 9998;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background-color: rgba(0, 0, 0, 0.5);
    cursor: pointer;
}

.modal-wrapper {
    position: absolute;
    float: left;
    left: 50%;
    top: 50%;
    transform: translate(-50%, -50%);
}

.modal-container {
    padding: 20px 30px;
    border-radius: 4px;
    background-color: #fff;
    box-shadow: 0 2px 8px rgba(0, 0, 0, 0.33);
    display: flex;
}

.header-wrapper {
    display: flex;
    justify-content: space-between;
    font-size: 1.5em;
    margin-bottom: 16px;
    gap: 60px;
}

.close-button {
    background: none;
    border: none;
    cursor: pointer;
}

.form-wrapper {
    display: flex;
    flex-direction: column;
    gap: 16px;
    margin-bottom: 16px;
}

.field-wrapper {
    display: flex;
    justify-content: space-between;
    width: 100%;
}

.field-wrapper > * {
    width: 50%;
}

.actions-wrapper {
    display: flex;
    justify-content: flex-end;
}
</style>
