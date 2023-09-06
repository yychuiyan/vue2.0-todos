## todos

### 维护基础样式和布局

### 渲染基础列表数据

1. 准备数据
2. 父传子方式，将数据渲染到页面
3. 使用v-model绑定复选框选中的状态
4. 根据选中的状态，设置完成划线的样式

`App.vue`

```js
<template>
  <div class="todoapp">
    <todo-header></todo-header>
    <todo-main :arr="list"></todo-main>
    <todo-footer></todo-footer>
  </div>
</template>
<script>
// 静态样式导入
import "./style/index.css";
import "./style/base.css";
// 引入组件
import TodoFooter from "./components/TodoFooter.vue";
import TodoMain from "./components/TodoMain.vue";
import TodoHeader from "./components/TodoHeader.vue";
export default {
  // 注册组件
  components: {
    TodoHeader,
    TodoMain,
    TodoFooter,
  },
  data() {
    return {
      list: [
        {
          id: 100,
          name: "吃饭",
          isDone: true,
        },
        {
          id: 201,
          name: "睡觉",
          isDone: false,
        },
        {
          id: 103,
          name: "打豆豆",
          isDone: true,
        },
      ],
    };
  },
};
</script>

<style scoped></style>
```

`TodoMain.vue`

```vue
<template>
  <div>
    <ul class="todo-list">
      <li :class="{ completed: obj.isDone }" v-for="obj in arr" :key="obj.id">
        <div class="view">
          <input class="toggle" type="checkbox" v-model="obj.isDone" />
          <label>{{ obj.name }}</label>
        </div>
      </li>
    </ul>
  </div>
</template>
<script>
export default {
  // 接收父组件传递过来的数据
  props: ["arr"],
  data() {
    return {};
  },
};
</script>
<style scoped></style>
```

### 添加数据

使用子传父的方式，

1. 在input里面传入一个鼠标回车事件绑定一个方法，将输入的内容绑定到一个变量中
2. 将输入的数据通过"this.$emit()"传递到父组件中
3. 父组件通过一个事件处理函数方法进行接收
4. 操作函数，判断id值，新增数据到页面列表中

`App.vue`

```html
 <todo-header @create="createFn"></todo-header>
```

```js
  methods: {
    createFn(taskName) {
      // id递增
      let id =
        this.list.length === 0 ? 100 : this.list[this.list.length - 1].id + 1;
      // 新增数据
      this.list.push({
        id: id,
        name: taskName,
        isDone: false,
      });
    },
  },

```

`Header.vue`

```vue
<template>
  <div class="header">
    <h1>todos</h1>
    <input type="checkbox" class="toggle-all" id="toggle-all" />
    <label for="toggle-all"></label>
    <input
      type="text"
      placeholder="输入任务名称-回车确认"
      class="new-todo"
      @keydown.enter="downFn"
      v-model="task"
    />
  </div>
</template>
<script>
export default {
  data() {
    return {
      // 当前任务名称
      task: "",
    };
  },
  methods: {
    downFn() {
      // 判断任务是否为空
      if (this.task.trim().length === 0) {
        alert("输入的任务不能为空");
        return;
      }
      // 通过子传父的方式，将数据传递给父组件
      this.$emit("create", this.task);
      // 提交之后输入框置为空
      this.task = "";
    },
  },
};
</script>

<style scoped></style>
```

### 删除操作

子传父的方式

1. 根据id进行数据删除，在`Main`组件中创建一个点击事件，传入参数`id`
2. 通过`$emit()`方法将数据传递给父组件，父组件接收后并声明一个事件函数方法
3. 利用findindex()方法匹配对应的索引号，使用splice方法执行删除操作

`App.vue`

```html
<todo-main :arr="list" @del="delFn"></todo-main>
```

```js
    // 删除
    delFn(id) {
      // 根据id匹配数据
      let index = this.list.findIndex((obj) => obj.id === id);
      // 删除一个
      this.list.splice(index, 1);
      alert("删除成功");
    },
```

`Main.vue`

```vue
<template>
  <div>
    <ul class="todo-list">
      <li :class="{ completed: obj.isDone }" v-for="obj in arr" :key="obj.id">
        <div class="view">
          <input class="toggle" type="checkbox" v-model="obj.isDone" />
          <label>{{ obj.name }}</label>
          <!-- 删除操作 -->
          <button class="destroy" @click="delFn(obj.id)"></button>
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
```

### 底部统计

利用父传子技术

1. 父组件声明一个`footArr`参数，子组件通过props接收传过来数据
2. 通过插值方式显示统计数量

`App.vue`

```html
<todo-footer :footArr="list"></todo-footer>
```

`Footer.vue`

```vue
<template>
  <div class="footer">
    <span class="todo-count">剩余{{ footArr.length }}个任务</span>
    <ul class="filters">
      <li>
        <a href="javascript:void(0)">全部</a>
      </li>
      <li>
        <a href="javascript:void(0)">未完成</a>
      </li>
      <li>
        <a href="javascript:void(0)">已完成</a>
      </li>
    </ul>
  </div>
</template>
<script>
export default {
  props: ["footArr"],
  data() {
    return {};
  },
};
</script>

<style scoped></style>
```

### 数据切换

子传父的方式

1. 在`footer`组件中，使用动态class，
2. 设置点击事件，点击之后高亮显示
3. 通过`$emit()`方式将`isSel`中的值传递给父组件
4. 父组件中定义一个默认变量值，通过一个事件函数进行接收子组件传递过来的参数，在事件函数中进行处理
5. 定义一个计算属性判断数据，用于切换时显示符合条件的数据

`App.vue`

```html
<template>
  <div class="todoapp">
    <todo-header :arr="list" @create="createFn"></todo-header>
    <todo-main :arr="showArr" @del="delFn"></todo-main>
    <todo-footer :footArr="showArr" @changeType="typeFn"></todo-footer>
  </div>
</template>
```

```js
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
    }
  }
```

`Footer.vue`

```vue
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
  },
};
</script>

<style scoped></style>

```

### 清空已完成数据

子传父方式

1. `footer`组件中定义一个点击事件绑定一个函数
2. 使用`this.$emit()`方式，将`clearBtn`点击事件方法传递给父组件，App.vue绑定事件函数
3. 通过过滤器方式将已完成的数据进行筛选过滤

`App.vue`

```html
    <todo-footer :footArr="showArr" @changeType="typeFn" @clear="clearFn"></todo-footer>
```

```js
    clearFn(){
      // 把未完成的数据过滤出来覆盖给数据
      this.list=this.list.filter(obj=>obj.isDone===false)
    }
```

`footer.vue`

```html
    <button class="clear-completed" @click="clearBtn">清空已完成</button>
```

```js
    clearBtn(){
      this.$emit('clear');
    }
```

### 数据缓存

1. 父组件中定义一个监听事件，深度监听
2. 使用localstorage设置缓存
3. 在data中取出缓存并赋值到list上

```js
data() {
    return {
      list: JSON.parse(localStorage.getItem('todoList'))||[],
      // 全部数据
      allSel: 'all',
    };
  },
watch:{
    list:{
      deep:true,
      handler(){
        // 只要list变化就会覆盖保存到localstorage里，转化为字符串之后存储
        localStorage.setItem('todoList',JSON.stringify(this.list))
      }
    }
```

### 全选&单选

父传子方式

1. 父组件将列表数据通过props传递到子组件中，
2. 子组件使用v-model绑定一个变量，通过计算属性进行遍历选中及取消
3. 当点击全选按钮时，其余按钮都要被选中
4. 使用短路与运算符 如果第一个操作数为真，则返回第二个操作数，如果第一个操作数为假值，则直接返回第一个。如果数组长度=0，则直接返回原始状态，如果数组长度不为0，则根据every函数方法查找复选框状态，如果都为true则全选，如果有一个为false则不选

`Header.vue`

```html
    <input type="checkbox" class="toggle-all" id="toggle-all"  v-model="isAll"/>
```

```js
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
```

