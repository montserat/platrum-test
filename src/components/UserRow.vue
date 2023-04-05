<template>
    <div class="row-wrapper">
        <div class="table-row">
            <div class="user-name-wrapper">
                <span
                    @click="isShowSubordinates = !isShowSubordinates"
                    class="expand-button"
                    :style="{ marginLeft: levelMargin }"
                >
                    {{ !user.subordinates.length ? '' : !isShowSubordinates ? '+' : '-' }}
                </span>
                <span class="user-name">{{ user.name }}</span>
            </div>
            <div class="user-phone-wrapper">
                <span class="user-phone">{{ user.phone }}</span>
            </div>
        </div>

        <div class="inner" v-if="isShowSubordinates">
            <User v-for="user in user.subordinates" :user="user" :key="user.id" :level="level + 1" />
        </div>
    </div>
</template>

<script>
export default {
  props: {
    user: {
      name: String,
      phone: String,
      subordinates: Array
    },
    level: Number
  },
  data: () => ({
    isShowSubordinates: false
  }),
  components: {
    User: () => import('./UserRow.vue')
  },
  computed: {
    // Вычисление динамического стиля на основе уровня позволяет сохранить индентацию остальных полей.
    // И корректную работу ellipsis для имен.
    levelMargin: function () {
      return `${this.level * 10}px`
    }
  }
}
</script>

<style>
.row-wrapper {
    display: flex;
    flex-direction: column;
}

.expand-button {
    cursor: pointer;
    margin-right: 4px;
}

.user-name-wrapper,
.user-phone-wrapper {
    display: flex;
    justify-content: center;
    width: 50%;
}

.user-name {
    white-space: nowrap;
    overflow: hidden;
    text-overflow: ellipsis;
}

.table-row {
    display: flex;
    width: 100%;
    margin-bottom: 4px;
}

.table-row:hover {
    background-color: rgba(195, 195, 195, 0.5);
}
</style>
