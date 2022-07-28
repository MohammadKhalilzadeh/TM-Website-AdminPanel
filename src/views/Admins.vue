<template>
  <div class="admin">
    <center>
      <div class="form">
        <h3>پنل مدیریت</h3>
        <br />
        <div class="login-fields">
          <input
            type="text"
            class="form-control"
            aria-label="Sizing example input"
            aria-describedby="inputGroup-sizing-default"
            v-bind:placeholder="$t('GetStart_field7')"
            v-model="input"
          />
          <input
            type="password"
            class="form-control"
            aria-label="Sizing example input"
            aria-describedby="inputGroup-sizing-default"
            v-bind:placeholder="$t('GetStart_field5')"
            v-model="password"
          />
        </div>
        <button @click.enter="login">
          {{ $t("Login_Btn") }}
        </button>
      </div>
    </center>
  </div>
</template>

<script>
import axios from "axios";
import VueJwtDecode from "vue-jwt-decode";

export default {
  name: "Admin",
  props: {
    msg: String,
  },
  data() {
    return {
      input: "",
      password: "",
    };
  },
  methods: {
    login() {
      let url = process.env.VUE_APP_API_URL + "employees/single/" + this.input;
      axios
        .post(url, {
          password: this.password,
        })
        .then((res) => {
          localStorage.setItem("token", res.data.token);
          let userdata = VueJwtDecode.decode(res.data.token);
          if (res.status == 200) {
            localStorage.setItem("Empid", userdata.id);
            this.input = "";
            this.password = "";
            this.$store.commit("setAuthentication", true);
            this.$router.push({
              name: "AdminPanel",
            });
          } else if (res.status == 404) {
            alert(" کاربری با این مشخصات یافت نشد ");
          } else if (res.status == 406) {
            alert(" پسورد اشتباه ");
          } else if (res.status == 500) {
            alert(" خظا در پردازش در خواست ");
          } else {
            alert(res.status + " خطای سیستم ");
          }
        });
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.admin {
  width: 100%;
  color: #80B918;
  background: url("../assets/admin.jpeg") ;
  background-repeat: no-repeat;
  background-size: cover ;
  background-attachment: fixed;
  background-position: center;
  min-height: 100vh;
  overflow: hidden;
}

.form {
  margin: 5%;
  width: 90%;
  padding: 1%;
  border-radius: 10px;
  color: white;
  background: #2626268c;
  box-shadow: 0 4px 8px 0 rgba(17, 17, 17, 0.529), 0 6px 20px 0 rgba(17, 17, 17, 0.529);
}

input {
  margin: 3% 5%;
  border-radius: 10px;
  box-shadow: 2px 2px 2px 2px rgba(58, 58, 58, 0.2),
    2px 2px 2px 0 rgba(141, 141, 141, 0.19);
  color: black;
}

.login-fields {
  width: 50%;
}

button {
  width: 10%;
  padding: 1%;
  border-radius: 15px;
  border: 1px solid white;
  background: rgba(112, 112, 112, 0.2);
  color: white;
}

button:hover {
  border: 1px solid rgba(112, 112, 112, 0.2);
  background: rgba(255, 255, 255, 0.486);
  color: white;
}
</style>
