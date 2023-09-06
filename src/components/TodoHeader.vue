<template>
  <div class="header">
    <h1>todos</h1>
    <input type="checkbox" class="toggle-all" id="toggle-all"  v-model="isAll"/>
    <label for="toggle-all"></label>
    <input type="text" placeholder="输入任务名称-回车确认" class="new-todo" @keydown.enter="downFn" v-model="task" />
  </div>
</template>
<script>
export default {
  props:['arr'],
  data() {
    return {
      // 当前任务名称
      task: '',
    };
  },
  methods: {
    downFn() {
      // 判断任务是否为空
      if (this.task.trim().length === 0) {
        alert('输入的任务不能为空');
        return;
      }
      // 通过子传父的方式，将数据传递给父组件
      this.$emit('create', this.task);
      // 提交之后输入框置为空
      this.task = '';
    },
  },
  computed:{
    isAll:{
      set(checked){
        // 勾选时为true，否则为false
        this.arr.forEach(obj=>obj.isDone=checked)
      },
      get(){
      // 单选影响全选
      return this.arr.length!==0 && this.arr.every(obj=>obj.isDone===true)
      }
    }
  }
}
</script>

<style scoped></style>
