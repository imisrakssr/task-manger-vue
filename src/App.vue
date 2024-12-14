<template>
  <div id="app" class="container mt-4">
    <h1 class="text-center mb-4">Task Manager App</h1>
    <button @click="toggleForm" class="btn btn-primary mb-3">
      {{ showForm ? 'Hide Add Task Form' : 'Show Add Task Form' }}
    </button>

    <form v-if="showForm" @submit.prevent="addTask" class="mb-4">
      <div class="mb-3">
        <input
          v-model="newTaskName"
          placeholder="Enter task name"
          class="form-control"
        />
      </div>
      <button type="submit" class="btn btn-success">Add Task</button>
    </form>

    <p v-if="!tasks.length" class="text-danger">No tasks available</p>
    <ul v-else class="list-group">
      <li
        v-for="(task, index) in tasks"
        :key="index"
        class="list-group-item d-flex justify-content-between align-items-center"
        :class="{ 'list-group-item-success': task.completed }"
      >
        <span>{{ task.name }}</span>
        <div>
          <button @click="toggleTaskCompletion(index)" class="btn btn-warning me-2">
            Mark as {{ task.completed ? 'Incomplete' : 'Complete' }}
          </button>
          <button @click="deleteTask(index)" class="btn btn-danger">Delete</button>
        </div>
      </li>
    </ul>
  </div>
</template>

<script>
import { reactive, toRefs } from 'vue';

export default {
  name: 'App',
  setup() {
    const state = reactive({
      tasks: JSON.parse(localStorage.getItem('tasks')) || [],
      newTaskName: '',
      showForm: false,
    });

    const toggleForm = () => {
      state.showForm = !state.showForm;
      state.newTaskName = ''; // Clear input field when toggling form
    };

    const addTask = () => {
      if (state.newTaskName.length < 3) {
        alert('Task name must be at least 3 characters long');
        return;
      }
      state.tasks.push({ name: state.newTaskName, completed: false });
      state.newTaskName = '';
      saveTasks();
    };

    const toggleTaskCompletion = (index) => {
      state.tasks[index].completed = !state.tasks[index].completed;
      saveTasks();
    };

    const deleteTask = (index) => {
      if (!state.tasks[index].completed) {
        const confirmDelete = confirm(
          'This task is not marked as completed. Are you sure you want to delete it?'
        );
        if (!confirmDelete) {
          return;
        }
      }
      state.tasks.splice(index, 1);
      saveTasks();
    };

    const saveTasks = () => {
      localStorage.setItem('tasks', JSON.stringify(state.tasks));
    };

    return {
      ...toRefs(state),
      toggleForm,
      addTask,
      toggleTaskCompletion,
      deleteTask,
    };
  },
};
</script>

<!-- <style>
.completed {
  background-color: lightgreen;
  border: 2px solid red;
}

button {
  margin: 5px;
}
</style> -->