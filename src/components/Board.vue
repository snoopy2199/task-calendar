<template>
  <div class="tc-board">
    <div class="tc-board__calendar">
      <div v-for="i in 10" class="tc-board__block" :key="i">
        <div class="tc-board__block-title">
          <span @click="editing = `block${i}`">{{task[`block${i}`].title}}</span>
          <input
            v-model="task[`block${i}`].title"
            class="tc-board__input tc-board__block-input"
            :class="{'tc-board__block-input_is-show': editing === `block${i}`}"
            @keypress.enter="editing = null; saveToLocalStorage();"
          >
        </div>
        <draggable
          class="tc-board__list"
          v-model="task[`block${i}`].list"
          :options="{group:'task'}"
          @start="drag=true"
          @end="drag=false"
          @update="saveToLocalStorage()"
          @remove="saveToLocalStorage()"
        >
          <div
            v-for="item in task[`block${i}`].list"
            :key="item.id"
            class="tc-board__task"
            :class="{'tc-board__task_is-done': item.isDone}"
          >
            <span class="tc-board__task-name">{{item.name}}</span>
            <span class="tc-board__task-button" @click="doneTask(item)">V</span>
            <span class="tc-board__task-button" @click="removeTask(`block${i}`, item)">X</span>
          </div>
        </draggable>
      </div>
    </div>
    <div class="tc-board__backlog tc-board__block">
      <div class="tc-board__block-title">Backlog</div>
      <draggable
        class="tc-board__list"
        v-model="task.backlog.list"
        :options="{group:'task'}"
        @start="drag=true"
        @end="drag=false"
        @update="saveToLocalStorage()"
        @remove="saveToLocalStorage()"
      >
        <div
          v-for="item in task.backlog.list"
          :key="item.id"
          class="tc-board__task"
          :class="{'tc-board__task_is-done': item.isDone}"
        >
          <span class="tc-board__task-name">{{item.name}}</span>
          <span class="tc-board__task-button" @click="doneTask(item)">V</span>
          <span class="tc-board__task-button" @click="removeTask('backlog', item)">X</span>
        </div>
      </draggable>
      <div class="tc-board__tool">
        <input
          v-model="temp"
          placeholder="Create a task"
          class="tc-board__input"
          @keypress.enter="createTask()"
        >
        <button @click="createTask()" class="tc-board__button">Create</button>
      </div>
    </div>
  </div>
</template>

<script>
import draggable from 'vuedraggable';

const STORAGE_KEY = 'tsy-task-calendar';

export default {
  name: 'Board',
  components: {
    draggable,
  },
  data() {
    const save = localStorage.getItem(STORAGE_KEY);

    let task;
    if (save) {
      task = JSON.parse(save);
    } else {
      task = { backlog: { list: [] } };
      for (let i = 1; i <= 10; i += 1) {
        task[`block${i}`] = {
          title: `Day ${i}`,
          list: [],
        };
      }
    }

    return { task, temp: '', editing: null };
  },
  methods: {
    createTask() {
      const newTask = this.temp && this.temp.trim();
      if (!newTask) return;

      this.task.backlog.list.push({
        id: Date.now(),
        name: newTask,
        isDone: false,
      });
      this.temp = '';

      this.saveToLocalStorage();
    },
    removeTask(block, task) {
      this.task[block].list.splice(this.task[block].list.indexOf(task), 1);
      this.saveToLocalStorage();
    },
    doneTask(task) {
      task.isDone = !task.isDone;
      this.saveToLocalStorage();
    },
    saveToLocalStorage() {
      localStorage.setItem(STORAGE_KEY, JSON.stringify(this.task));
    },
  },
};
</script>

<style scoped lang="scss">
.tc-board {
  height: 100vh;
  display: grid;
  grid-template-columns: 5fr 1fr;
}

.tc-board__calendar {
  height: 100vh;
  display: grid;
  grid-template-columns: repeat(5, 1fr);
  grid-template-rows: 1fr 1fr;
  background-color: #f7f7f7;
}

.tc-board__backlog {
  background-color: #65b2c3;
  color: white;
}

.tc-board__block {
  padding: 10px 15px;
}

.tc-board__block-title {
  font-weight: bold;
  font-size: 16px;
  text-align: center;
  position: relative;
}

.tc-board__list {
  margin-top: 15px;
  height: 80%;
}

.tc-board__tool {
  position: absolute;
  bottom: 20px;
  margin-left: 10px;
}

.tc-board__input {
  width: 100%;
  border-radius: 5px;
  line-height: 1.5;
  margin: 5px 0;
  padding: 5px;
  box-sizing: border-box;
  border: solid 1px #ccc;
}

.tc-board__button:focus,
.tc-board__input:focus {
  outline: none;
}

.tc-board__button {
  border-radius: 5px;
  padding: 5px;
  float: right;
}

.tc-board__task {
  background-color: #fff;
  box-shadow: 0 2px 2px 0 rgba(0, 0, 0, 0.2), 0 1px 3px 0 rgba(0, 0, 0, 0.19);
  border-radius: 5px;
  color: #2c3e50;
  margin: 5px 0;
  padding: 5px;
  font-size: 14px;
  display: grid;
  grid-template-columns: auto 20px 20px;
}

.tc-board__task_is-done {
  opacity: 0.6;
  box-shadow: 0 1px 1px 0 rgba(0, 0, 0, 0.1), 0 1px 2px 0 rgba(0, 0, 0, 0.05);

  .tc-board__task-name {
    text-decoration: line-through;
  }
}

.tc-board__block-input {
  position: absolute;
  left: 0;
  top: -2px;
  display: none;
}

.tc-board__block-input_is-show {
  display: block;
}

.tc-board__task-button {
  display: none;
  cursor: pointer;
  padding: 0 5px;
}

.tc-board__task:hover .tc-board__task-button {
  display: inline-block;
}
</style>
