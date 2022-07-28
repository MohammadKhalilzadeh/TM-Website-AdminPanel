<template>
  <div class="requests">
    <center>
      <h1>درخواست‌ها</h1>
    </center>
  </div>
</template>

<script>
export default {
  name: "Requests",
  props: {
    msg: String,
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.requests {
  padding: 3%;
  background: url("../assets/admin.jpeg") ;
  background-repeat: no-repeat;
  background-size: cover ;
  background-attachment: fixed;
  background-position: center;
  color: #fff;
  overflow: hidden;
  font-family: "IranNastaliq", Arial, sans-serif;
}
</style>
