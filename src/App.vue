<template>
  <div class="" id="app">
    <h2>Tarefas</h2>
    <TaskProgressBar :progress="progress" />
    <NewTask @taskAdded="addTask" />
    <TaskGrid
      :tasks="tasks"
      @taskDeleted="deleteTask"
      @taskStateChanged="toggleTaskState"
    />
  </div>
</template>
<script>
import TaskProgressBar from "./components/TaskProgressBar.vue";
import NewTask from "./components/NewTask.vue";
import TaskGrid from "./components/TaskGrid.vue";

export default {
  components: {
    TaskProgressBar,
    NewTask,
    TaskGrid,
  },
  data() {
    return {
      tasks: [],
    };
  },
  computed: {
    progress() {
      let total = this.tasks.length;
      let done = this.tasks.filter((t) => !t.pending).length;
      return Math.round((done / total) * 100) || 0;
    },
  },
  watch: {
    tasks: {
      deep: true,
      handler() {
        localStorage.setItem("tasks", JSON.stringify(this.tasks));
      },
    },
  },
  methods: {
    addTask(task) {
      const sameTask = (t) => t.name === task.name;
      const isNewTask = this.tasks.filter(sameTask).length == 0;
      if (isNewTask) {
        this.tasks.push({
          name: task.name,
          pending: task.pending || true,
        });
      }
    },
    deleteTask(i) {
      this.tasks.splice(i, 1);
    },
    toggleTaskState(i) {
      this.tasks[i].pending = !this.tasks[i].pending;
    },
  },
  created() {
    const json = localStorage.getItem("tasks");
    const array = JSON.parse(json);
    this.tasks = Array.isArray(array) ? array : [];
  },
};
</script>
<style scoped>
body {
  font-family: "Lato", sans-serif;
  background: linear-gradient(to right, rgb(22, 34, 42), rgb(51, 86, 104));
  color: #fff;
}
#app {
  display: flex;
  flex: 1;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  height: 100vh;
}

#app h2 {
  margin-bottom: 10px;
  font-weight: 400;
  font-size: 3rem;
}
</style>
