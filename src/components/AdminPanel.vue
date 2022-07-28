<template>
  <div class="adminPanel">
    <ul>
      <!-- <li v-if="employess.educations">
        <router-link class="navs" to="/panel/classroom">کلاس‌ها</router-link>
      </li> -->
      <li v-if="employess.product">
        <router-link class="navs" to="/panel/products">محصولات</router-link>
      </li>
      <!-- <li v-if="employess.allies">
        <router-link class="navs" to="/panel/interns">کارآموزان</router-link>
      </li> -->
      <!-- <li v-if="employess.allies">
        <router-link class="navs" to="/panel/employee">کارکنان</router-link>
      </li> -->
      <li v-if="employess.orders">
        <router-link class="navs" to="/panel/orders">سفارشات</router-link>
      </li>
      <li v-if="employess.bills">
        <router-link class="navs" to="/panel/bills">صورتحساب‌ها</router-link>
      </li>
      <li v-if="employess.requests">
        <router-link class="navs" to="/panel/requests">درخواست‌ها</router-link>
      </li>
      <li v-if="employess.identifying">
        <router-link class="navs" to="/panel/identifications" >احراز هویت‌ها</router-link>
      </li>
      <!-- <li v-if="employess.advertise">
        <router-link class="navs" to="/panel/advertise">تبلیغات</router-link>
      </li> -->
      <li v-if="employess.tickets">
        <router-link class="navs" to="/panel/tickets">تیکت‌ها</router-link>
      </li>
      <li v-if="employess.allies">
        <router-link class="navs" to="/panel/allies">همکاران</router-link>
      </li>
      <li v-if="employess.allies">
        <router-link class="navs" :to="{ name:'Freelancers'}">Freelancers</router-link>
      </li>
      <!-- <li v-if="employess.settings">
        <router-link class="navs" to="/panel/settings">تنظیمات</router-link>
      </li> -->
    </ul>
    <router-view />
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "AdminPanel",
  props: {
    msg: String,
  },
  data() {
    return {
      employess: {},
      url: localStorage.getItem("Empid"),
    };
  },
  mounted() {
    let newurl = process.env.VUE_APP_API_URL + "employees/" + this.url;

    axios
      .get(newurl, {
        headers: { Authorization: "Bearer " + localStorage.getItem("token") },
      })
      .then((response) => {
        if (response.status == 200) {
          this.employess = response.data;
          console.log(response.data);
        }
      });
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

.adminPanel{
  background: url("../assets/admin.jpeg") ;
  background-repeat: no-repeat;
  background-size: cover ;
  background-attachment: fixed;
  background-position: center;
  min-height: 100vh;
}

ul {
  list-style-type: none;
  margin: 0;
  padding: 0;
  width: 10%;
  position: fixed;
  height: 100%;
  overflow: auto;
  font-family: "IranNastaliq", Arial, sans-serif;
  color: white;
  background: #2626268c;
  box-shadow: 0 4px 8px 0 rgba(17, 17, 17, 0.529), 0 6px 20px 0 rgba(17, 17, 17, 0.529);
}

.navs {
  display: block;
  color: #fff;
  padding: 8px 16px;
  text-decoration: none;
}

.navs:hover:not(.active) {
  background-color: #d9173c;
  color: white;
  box-shadow: 0 4px 8px 0 #df1a3a8c, 0 6px 20px 0 #ce1b3880;
}
</style>
