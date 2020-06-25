<template>
  <div>
    <input
      type="text"
      class="todo-input"
      placeholder="Enter here"
      v-model="newTodo"
      @keyup.enter="addTodo"
    />
    <todo-item
      v-for="(todo, index) in todosFiltered"
      v-bind:key="todo.id"
      v-bind:todo="todo"
      v-bind:index="index"
      v-bind:checkAll="!anyRemaining"
    ></todo-item>

    <div class="extra-container">
      <div>
        <label>
          <input type="checkbox" :checked="!anyRemaining" @change="checkAllTodos" /> Check
          All
        </label>
      </div>
      <div>{{ remaining }} items left</div>
    </div>

    <div class="extra-container">
      <div>
        <button
          :class="{ active: this.$store.state.filter == 'all' }"
          @click="changeFilter('all')"
        >All</button>
        <button
          :class="{ active: this.$store.state.filter == 'active' }"
          @click="changeFilter('active')"
        >Active</button>
        <button
          :class="{ active: this.$store.state.filter == 'completed' }"
          @click="changeFilter('completed')"
        >Completed</button>
      </div>

      <div>
        <transition name="fade">
          <button v-if="showClearCompletedButton" @click="clearCompleted">Clear Completed</button>
        </transition>
      </div>
    </div>
  </div>
</template>

<script>
import TodoItem from "./TodoItem";

export default {
  name: "todo-list",
  components: {
    TodoItem
  },
  data() {
    return {
      newTodo: "",
      idForTodo: 3,
      beforeEditCache: ""
    };
  },
  computed: {
    remaining() {
      return this.$store.state.todos.filter(todo => !todo.completed).length;
    },
    anyRemaining() {
      return this.remaining != 0;
    },
    todosFiltered() {
      if (this.$store.state.filter == "all") {
        return this.$store.state.todos;
      } else if (this.$store.state.filter == "active") {
        return this.$store.state.todos.filter(todo => !todo.completed);
      } else if (this.$store.state.filter == "completed") {
        return this.$store.state.todos.filter(todo => todo.completed);
      }

      return this.$store.state.todos;
    },
    showClearCompletedButton() {
      return this.$store.state.todos.filter(todo => todo.completed).length > 0;
    }
  },
  methods: {
    addTodo() {
      if (this.newTodo.length == 0) {
        return;
      }

      this.$store.dispatch("addTodo", {
        id: this.idForTodo,
        title: this.newTodo
      });

      this.newTodo = "";
      this.idForTodo++;
    },
    checkAllTodos() {
      this.$store.commit("checkAll", event.target.checked);
    },
    clearCompleted() {
      this.$store.commit("clearCompleted");
    },
    changeFilter(filter) {
      this.$store.commit("updateFilter", filter);
    }
  },
  props: {
    msg: String
  }
};
</script>

<style lang="scss">
.todo-input {
  width: 100%;
  padding: 10px 18px;
  font-size: 18px;
  margin-bottom: 16px;
  &:focus {
    outline: 0;
  }
}

.todo-item {
  margin-bottom: 12px;
  display: flex;
  align-items: center;
  justify-content: space-between;
}

.remove-item {
  cursor: pointer;
  margin-left: 14px;
  &:hover {
    color: black;
  }
}

.todo-item-left {
  // later
  display: flex;
  align-items: center;
}
.todo-item-label {
  padding: 10px;
  border: 1px solid white;
  margin-left: 12px;
}
.todo-item-edit {
  font-size: 18px;
  color: #2c3e50;
  margin-left: 12px;
  width: 100%;
  padding: 10px;
  border: 1px solid #ccc; //override defaults
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  &:focus {
    outline: none;
  }
}

.completed {
  text-decoration: line-through;
  color: gray;
}

.extra-container {
  display: flex;
  align-items: center;
  justify-content: space-between;
  font-size: 16px;
  border-top: 1px solid lightgrey;
  padding-top: 14px;
  margin-bottom: 14px;
}
button {
  font-size: 14px;
  background-color: white;
  appearance: none;
  &:hover {
    background: lightgreen;
  }
  &:focus {
    outline: none;
  }
}

.active {
  background: lightgreen;
}

// CSS Transitions
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.2s;
}
.fade-enter,
.fade-leave-to {
  opacity: 0;
}
</style>
