<template>
  <div class="todoapp">
    <todo-header :arr="list" @create="createFn"></todo-header>
    <todo-main :arr="showArr" @del="delFn"></todo-main>
    <todo-footer :footArr="showArr" @changeType="typeFn" @clear="clearFn"></todo-footer>
  </div>
</template>
<script>
// 静态样式导入
import './style/index.css';
import './style/base.css';
// 引入组件
import TodoFooter from './components/TodoFooter.vue';
import TodoMain from './components/TodoMain.vue';
import TodoHeader from './components/TodoHeader.vue';
export default {
  // 注册组件
  components: {
    TodoHeader,
    TodoMain,
    TodoFooter,
  },
  data() {
    return {
      list: JSON.parse(localStorage.getItem('todoList'))||[],
      // 全部数据
      allSel: 'all',
    };
  },
  methods: {
    // 新增
    createFn(taskName) {
      // id递增
      let id = this.list.length === 0 ? 100 : this.list[this.list.length - 1].id + 1;
      // 新增数据
      this.list.push({
        id: id,
        name: taskName,
        isDone: false,
      });
    },
    // 删除
    delFn(id) {
      // 根据id匹配数据
      let index = this.list.findIndex(obj => obj.id === id);
      // 删除一个
      this.list.splice(index, 1);
      alert('删除成功');
    },
    typeFn(str) {
      this.allSel = str;
    },
    clearFn(){
      // 把未完成的数据过滤出来覆盖给数据
      this.list=this.list.filter(obj=>obj.isDone===false)
    }
  },
  computed: {
    showArr(){
      if (this.allSel === 'yes') {
        // 从所有数据中筛选出已完成的任务
        return this.list.filter(obj => obj.isDone === true)
      } else if (this.allSel === 'no') {
        return this.list.filter(obj => obj.isDone === false)
      } else {
        return this.list // 全部显示
      }
    }
  },
  watch:{
    list:{
      deep:true,
      handler(){
        // 只要list变化就会覆盖保存到localstorage里，转化为字符串之后存储
        localStorage.setItem('todoList',JSON.stringify(this.list))
      }
    }
  }
}
</script>

<style scoped></style>
