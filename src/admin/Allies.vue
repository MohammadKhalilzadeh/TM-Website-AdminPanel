<template>
  <div class="allies">
    <center>
      <h3>مدیریت برندهای همکار</h3>

      <div class="ally-form">
        <div class="row row-cols-auto">
          <div class="col">
            <input type="text" placeholder="نام" v-model="form.name" />
          </div>
          <div class="col">
            <input type="text" placeholder="تلفن" v-model="form.phone" />
          </div>
          <div class="col">
            <input type="text" placeholder="ایمیل" v-model="form.email" />
          </div>
          <div class="col" style="width: 100%">
            <input type="text" placeholder="شماره شبا" v-model="form.sheba" />
          </div>
          <div class="col" style="width: 100%">
            <input type="text" placeholder="آدرس" v-model="form.address" />
          </div>
          <div class="col" style="width: 100%">
            <input type="file" @change="handleFileUpload" />
          </div>
        </div>
        <progress max="100" :value.prop="uploadPercentage"></progress>
        <button id="create" v-on:click="submit()">ایجاد</button>
      </div>

      <div class="ally-form">
        <h4>لیست همکاران</h4>
        <br />
        <keep-alive>
          <table>
            <tr>
              <th>نام</th>
              <th>تلفن</th>
              <th>آدرس</th>
              <th>ایمیل</th>
              <th>عملیات</th>
            </tr>
            <tr v-for="ally in allies" :key="ally._id">
              <td>{{ ally.name }}</td>
              <td>{{ ally.phone }}</td>
              <td>{{ ally.address }}</td>
              <td>{{ ally.email }}</td>
              <td>
                <button id="delete" v-on:click="deleteally(ally._id)">
                  حذف
                </button>
              </td>
            </tr>
          </table>
        </keep-alive>
      </div>
    </center>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "Allies",
  props: {
    msg: String,
  },
  data() {
    return {
      form: {
        name: "",
        phone: "",
        address: "",
        email: "",
        sheba: "",
        image: null,
      },
      allies: [],
      uploadPercentage: 0,
    };
  },
  methods: {
    submit() {
      let formData = new FormData();
      formData.append("image", this.form.image, this.form.image.name);
      formData.append("name", this.form.name);
      formData.append("phone", this.form.phone);
      formData.append("email", this.form.email);
      formData.append("sheba", this.form.sheba);
      formData.append("address", this.form.address);
      console.log(this.form);
      let url = process.env.VUE_APP_API_URL + "ally";
      console.log(url);
      axios
        .post(url, formData, {
          onUploadProgress: function (progressEvent) {
            this.uploadPercentage = parseInt(
              Math.round((progressEvent.loaded / progressEvent.total) * 100)
            );
          }.bind(this),
          headers: {
            "Content-Type": "multipart/form-data",
            Authorization: "Bearer " + localStorage.getItem("token"),
          },
        },)
        .then(function (res) {
          console.log(res);
          if (res.status == 200) {
            window.location.reload();
            alert(" برند جدید ایجاد شد ");
          } else {
            alert(" خطا در ایجاد برند ");
          }
        });
    },
    async deleteally(id) {
      let url = process.env.VUE_APP_API_URL + "ally/" + id;
      await axios
        .delete(url, {
          headers: {
            Authorization: "Bearer " + localStorage.getItem("token"),
          },
        })
        .then((response) => {
          console.log(response);
          if (response.status == 200) {
            window.location.reload();
          } else {
            alert(" برند مورد نظر حذف شد ");
          }
        });
    },
    handleFileUpload(event) {
      this.form.image = event.target.files[0];
    },
  },
  created() {
    let url = process.env.VUE_APP_API_URL + "ally";
    axios
      .get(url, {
        headers: {
          Authorization: "Bearer " + localStorage.getItem("token"),
        },
      })
      .then((res) => {
        if (res.status == 200) {
          this.allies = res.data;
        } else {
          alert(" مشکل در دریافت اطلاعات ");
        }
      });
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.allies {
  padding: 3%;
  background: url("../assets/admin.jpeg") ;
  background-repeat: no-repeat;
  background-size: cover ;
  background-attachment: fixed;
  background-position: center;
  color: #fff;
  font-family: "IranNastaliq", Arial, sans-serif;
}

.ally-form {
  width: 80%;
  margin: 5% 10%;
  padding: 2%;
  border-radius: 15px;
  color: white;
  background: #262626bc;
  box-shadow: 0 4px 8px 0 rgba(17, 17, 17, 0.529), 0 6px 20px 0 rgba(17, 17, 17, 0.529);
}

input {
  padding: 3%;
  width: 90%;
  margin: 5%;
  border-radius: 15px;
  height: 40px;
}

#create {
  width: 30%;
  background: #d51637;
  color: white;
  margin: 3%;
  padding: 1%;
  border-radius: 20px;
  border: 2px solid #d51637;
}

#create:hover {
  background: #d51637;
  color: white;
  box-shadow: 0 4px 8px 0 #df1a3a8c, 0 6px 20px 0 #ce1b3880;
}

#delete {
  width: 90%;
  background: #d51637;
  color: white;
  margin: 3%;
  padding: 1%;
  border-radius: 20px;
  border: 2px solid #d51637;
}

#delete:hover {
  background: #d51637;
  color: white;
  box-shadow: 0 4px 8px 0 #df1a3a8c, 0 6px 20px 0 #ce1b3880;
}

table,
th,
td {
  border: 2px solid #d51637;
  padding: 1%;
}

table {
  width: 98%;
  margin: 1%;
}

progress {
  width: 100%;
}

@media screen and (min-width: 900px) {
  .col {
    width: 33%;
    margin: 0px;
    padding: 0px;
  }
}
</style>
