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
          Projects
          <ul>
            <li v-for="project in projects" v-bind:key="project">
              <a href="#" v-on:click="listByProject($event, project)">{{ project }}</a>
            </li>
          </ul>
        </li>
      </ul>
    </nav>

    <TaskForm v-if="ui.task.decription" />

    <div class="listTasks">
      <h3>{{ ui.label }}</h3>
      <ul>
        <li v-for="task in ui.tasks" v-bind:key="task.id">
          <Task v-bind:task="task" />
        </li>
      </ul>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import Task from "./components/Task";
import TaskForm from "./components/TaskForm";

export default {
  name: "app",
  components: {
    Task,
    TaskForm
  },
  data() {
    return {
      tasks: [],
      ui: {
        task: {},
        tasks: [],
        label: "Next"
      }
    };
  },

  mounted() {
    axios
      .get("tasks.json")
      .then(r => {
        this.tasks = r.data;
        var sorted_tasks = this.tasks.sort(function(a, b) {
          var comparison = 0;
          if (a.urgency > b.urgency) {
            comparison = -1;
          } else if (a.urgency < b.urgency) {
            comparison = 1;
          }
          return comparison;
        });
        this.ui.tasks = sorted_tasks.filter(task => task != null);
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
      console.log(tasks);

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

    listByProject: function(event, p) {
      let project_tasks = this.tasks.map(task => {
        if (task.project === p) return task;
      });

      this.ui.tasks = project_tasks.filter(task => task != null);
      this.ui.label = "Project: " + p;
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

<style scoped>
body {
  font-family: san-serif;
  color: red;
}
</style>
