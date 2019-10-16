<template>
  <div id="app">
    <nav class="listNav">
      <h3>Tasks Lists</h3>
      <ul>
        <li>
          <a href="#" v-on:click="listByNext">Next</a>
        </li>
        <li>
          <a href="#" v-on:click="listByInbox">Inbox</a>
        </li>
        <li>
          <a href="#" v-on:click="listByToday">Today</a>
        </li>
        <li>
          <a href="#" v-on:click="listByWeek">Week</a>
        </li>
        <li>
          Projects
          <ul>
            <li v-for="project in projects" v-bind:key="project">{{project}}</li>
          </ul>
        </li>
      </ul>
    </nav>

    <div class="listFilters">
      <h3>Tags</h3>
      <ul>
        <li v-for="tag in tags" v-bind:key="tag">{{tag}}</li>
      </ul>
    </div>

    <div class="listTasks">
      <h3>{{ui.label}}</h3>
      <ul>
        <li v-for="task in ui.tasks" v-bind:key="task.id">{{ task.description }}</li>
      </ul>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "app",
  components: {},
  data() {
    return {
      tasks: [],
      ui: {
        tasks: [],
        label: "Next"
      }
    };
  },
  mounted() {
    axios
      .get("https://taskist-cbwadixzqq-ue.a.run.app/tasks")
      .then(r => {
        this.tasks = r.data;
        this.ui.tasks = r.data;
        this.ui.label = "Next";
      })
      .catch(function() {
        this.tasks = [];
        this.ui.tasks = [];
        this.ui.label = "Fetch has gone wrong.";
        console.log("It's all gone wrong.");
      });
  },
  computed: {
    tags: function() {
      const tasks = this.tasks;
      const tags_array = [...new Set(tasks.map(task => task.tags))];
      return tags_array.flat().filter(p => p);
    },
    projects: function() {
      const tasks = this.tasks;
      const projects = [...new Set(tasks.map(task => task.project))];
      return projects.filter(p => p);
    }
  },
  methods: {
    listByNext: function() {
      this.ui.tasks = this.tasks;
    },
    listByInbox: function() {
      let inbox_tasks = this.tasks.map(task => {
        if (task.project === undefined) return task;
      });

      this.ui.tasks = inbox_tasks.filter(task => task != null);
      this.ui.label = "Inbox";
    },
    listByWeek: function(event) {
      this.ui.label = "Week";
      return event;
    },
    listByToday: function(event) {
      this.ui.label = "Today";
      return event;
    },
    listByProject: function(event) {
      this.ui.label = "Project";
      return event;
    },
    filterByTags: function(event) {
      this.ui.label = "Tags: ";
      return event;
    },
    filterByCustom: function(event) {
      this.ui.label = "Custom: ";
      return event;
    }
  }
};
</script>

<style>
body {
  margin: 0;
}
body * {
  box-sizing: border-box;
}
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  display: grid;
  grid-template-columns: repeat(8, 1fr);
  grid-template-rows: repeat(5, 1fr);
  color: #ccc;
  background-color: #2c3e50;
  height: 100vh;
  width: 100vw;
}
#app > * {
  border: 1px solid #000;
}

.listNav {
  grid-column-start: 1;
  grid-column-end: 3;
  grid-row-start: 1;
  grid-row-end: 3;
}

.listFilters {
  grid-column-start: 1;
  grid-column-end: 3;
  grid-row-start: 3;
  grid-row-end: 6;
  overflow-x: auto;
}

.listTasks {
  grid-column-start: 3;
  grid-column-end: 9;
  grid-row-start: 1;
  grid-row-end: 6;
  overflow-x: auto;
}

ul {
  margin-top: 0;
}
</style>
