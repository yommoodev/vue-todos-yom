<script setup>
import { uid } from "uid";
import { ref, watch, computed } from "vue";
import { Icon } from "@iconify/vue";
import TodoCreator from "../components/TodoCreator.vue";
import TodoItem from "../components/TodoItem.vue";

const todoList = ref([]);

watch(todoList, () => {
  setTodoListLocalstorage()
}, {
  deep: true
})

const todoCompleted = computed(() => {
  return todoList.value.every((todo) => todo.isCompleted)
})

const fetchTodoListLS = () => {
  const savedTodoList = JSON.parse(localStorage.getItem("todoList"));

  if (savedTodoList) {
    todoList.value = savedTodoList
  }
}

fetchTodoListLS();

const setTodoListLocalstorage = () => {
  localStorage.setItem("todoList", JSON.stringify(todoList.value));
}


const createTodo = (todo) => {
  todoList.value.push({
    id: uid(),
    todo,
    isCompleted: false,
    isEditing: null,
  });
};

const toggleTodoComplete = (todoPos) => {
  todoList.value[todoPos].isCompleted = !todoList.value[todoPos].isCompleted
}

const toggleEditTodo = (todoPos) => {
  todoList.value[todoPos].isEditing = !todoList.value[todoPos].isEditing
}

const updateTodo = (todoVal, todoPos) => {
  todoList.value[todoPos].todo = todoVal;
}

const deleteTodo = (todoId) => {
  todoList.value = todoList.value.filter((todo) => todo.id !== todoId);
}
</script>

<template>
  <main>
    <h1>Create To-do</h1>
    <TodoCreator @create-todo="createTodo">
      <template #button-content>Create</template>
    </TodoCreator>
    <ul class="todo-list" v-if="todoList.length > 0">
      <TodoItem v-for="(todo, index) in todoList" :todo="todo" :index="index" @toggle-complete="toggleTodoComplete" @edit-todo="toggleEditTodo" @update-todo="updateTodo" @delete-todo="deleteTodo"/>
    </ul>
    <p v-else class="todos-msg">
      <Icon icon="noto-v1:sad-but-relieved-face" />
      <span>You have no to-do's to complete! Add one!</span>
    </p>
    <p v-if="todoCompleted && todoList.length > 0" class="todos-msg">
      <Icon icon="noto-v1:party-popper" />
      <span>You have completed all your to-dos!</span>
    </p>
  </main>
</template>

<style scoped lang="scss">
main {
  display: flex;
  flex-direction: column;
  max-width: 500px;
  width: 100%;
  margin: 0 auto;
  padding: 40px 16px;

  h1 {
    margin-bottom: 16px;
    text-align: center;
  }

  .todo-list {
    display: flex;
    flex-direction: column;
    list-style: none;
    margin-top: 24px;
    gap: 20px;
  }

  .todos-msg {
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 8px;
    margin-top: 24px;
  }
}
</style>