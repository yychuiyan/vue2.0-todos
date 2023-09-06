<template>
  <!-- 事件委托 -->
  <div class="footer" @click="fn">
    <span class="todo-count">剩余{{ footArr.length }}个任务</span>
    <ul class="filters">
      <li>
        <a
          href="javascript:void(0)"
          :class="{ selected: isSel === 'all' }"
          @click="isSel = 'all'">全部</a>
      </li>
      <li>
        <a href="javascript:void(0)" :class="{ selected: isSel === 'no' }" @click="isSel = 'no'">
          未完成
        </a>
      </li>
      <li>
        <a href="javascript:void(0)" :class="{ selected: isSel === 'yes' }" @click="isSel = 'yes'">已完成</a>
      </li>
    </ul>
    <!-- 清空已完成 -->
    <button class="clear-completed" @click="clearBtn">清空已完成</button>
  </div>
</template>
<script>
export default {
  props: ['footArr'],
  data() {
    return {
      isSel: 'all',
    };
  },
  methods: {
    fn() {
      // 传递给父组件
      this.$emit('changeType', this.isSel);
    },
    clearBtn(){
      this.$emit('clear');
    }

  },
};
</script>

<style scoped></style>
