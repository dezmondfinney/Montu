<template>
  <div class="task">
    <div>{{ task.id }}</div>
    <div>{{ task.description }}</div>
    <div>{{ task.entry }}</div>
    <div>{{ task.modified }}</div>
    <div>{{ task.project }}</div>
    <div>{{ task.status }}</div>
    <div>{{ task.uuid }}</div>
    <div>{{ task.urgency }}</div>
    <span v-if="task.annoations">annotations for this task</span>
  </div>
</template>

<script>
export default {
  name: "Task",
  props: {
    msg: String,
    task: Object
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.task {
  border: 1px solid red;
}
</style>
