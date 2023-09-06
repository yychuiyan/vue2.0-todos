<template>
  <div>
    <ul class="todo-list">
      <li :class="{ completed: obj.isDone }"
        v-for="obj in arr" :key="obj.id">
        <div class="view">
          <input class="toggle" type="checkbox"
            v-model="obj.isDone" />
          <label>{{ obj.name }}</label>
          <!-- 删除操作 -->
          <button class="destroy"
            @click="delFn(obj.id)"></button>
        </div>
      </li>
    </ul>
  </div>
</template>
<script>
export default {
  props: ["arr"],
  data() {
    return {};
  },
  methods: {
    delFn(id) {
      // 子传父的方式，将点击事件传递给父组件进行处理
      this.$emit("del", id);
    },
  },
};
</script>

<style scoped></style>
