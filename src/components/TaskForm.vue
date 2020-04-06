<template>
<form action="update" method="post">
  taskform
</form>
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
