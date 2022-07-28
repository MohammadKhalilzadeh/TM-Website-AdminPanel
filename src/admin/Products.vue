<template>
  <div class="product">
    <center>
      <h2>محصولات</h2>

      <div class="product-maker">
        <div class="row row-cols-auto" style="width: 100%">
          <div class="col">
            <input type="text" placeholder="نام" v-model="form.name" />
          </div>
          <div class="col">
            <select
              class="custom-select"
              v-model="form.unit"
              @change="handleChange1($event)"
            >
              <option value="number">تعدادی</option>
              <option value="weigh">وزنی</option>
            </select>
          </div>
          <div class="col">
            <input type="text" placeholder="قیمت" v-model="form.price" />
          </div>
          <div class="col">
            <select
              class="custom-select"
              v-model="form.available"
              @change="handleChange2($event)"
            >
              <option :value="true">موجود</option>
              <option :value="false">ناموجود</option>
            </select>
          </div>
          <div class="col">
            <select
              class="custom-select"
              v-model="form.owner"
              @change="handleChange3($event)"
            >
              <option
                :value="ally.sheba"
                v-for="ally in allies"
                :key="ally._id"
              >
                {{ ally.name }}
              </option>
            </select>
          </div>
          <div class="col1">
            <input type="file" @change="handleFileUpload" />
          </div>
          <div class="col1">
            <textarea
              name="message"
              rows="5"
              cols="30"
              placeholder="توضیحات"
              v-model="form.description"
            ></textarea>
          </div>
        </div>
        <progress max="100" :value.prop="uploadPercentage"></progress>
        <button id="create" v-on:click="submit">ایجاد</button>
      </div>

      <div class="product-maker">
        <h3>لیست محصولات</h3>

        <br />

        <table>
          <tr>
            <th>نام</th>
            <th>موجودی</th>
            <th>عکس</th>
            <th>عملیات</th>
          </tr>
          <tr v-for="product in products" :key="product._id">
            <td>{{ product.name }}</td>
            <td>
              <select
                class="custom-select"
                v-model="product.available"
                @change="updateavaiablity($event, product._id)"
              >
                <option :value="true">موجود</option>
                <option :value="false">ناموجود</option>
              </select>
            </td>
            <td style="width: 20%">
              <img :src="url + product.imagename" alt="" style="width: 100%" />
            </td>
            <td>
              <button id="delete" v-on:click="deletep(product._id)">حذف</button>
            </td>
          </tr>
        </table>
      </div>
    </center>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "Product",
  props: {
    msg: String,
  },
  data: function () {
    return {
      form: {
        name: "",
        unit: "number",
        price: null,
        available: true,
        owner: "trademaster",
        description: "",
        image: null,
      },
      update: true,
      file: null,
      products: [],
      allies: [],
      url: process.env.VUE_APP_API_URL,
      uploadPercentage: 0,
    };
  },
  methods: {
    submit() {
      let formData = new FormData();
      formData.append("image", this.form.image, this.form.image.name);
      formData.append("unit", this.form.unit);
      formData.append("price", this.form.price);
      formData.append("available", this.form.available);
      formData.append("owner", this.form.owner);
      formData.append("description", this.form.description);
      formData.append("name", this.form.name);
      console.log(this.form);
      let url = process.env.VUE_APP_API_URL + "products";
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
        })
        .then(function (res) {
          if (res.status == 200) {
            window.location.reload();
            alert("کالای جدید ایجاد شد");
          } else {
            alert(res.status + " : " + " مشکل در پردازش درخواست ");
          }
        });
    },
    handleChange1(event) {
      this.form.unit = event.target.value;
    },
    handleChange2(event) {
      this.form.available = event.target.value;
    },
    handleChange3(event) {
      this.form.owner = event.target.value;
    },
    updateavaiablity(event, id) {
      axios
        .patch(
          process.env.VUE_APP_API_URL + "products/" + id,
          {
            value: event.target.value,
            field:"available",
          },
          {
            headers: {
              Authorization: "Bearer " + localStorage.getItem("token"),
            },
          }
        )
        .then((response) => {
          console.log(response);
          if (response.status == 200) {
            alert(" به روز رسانی انجام شد ");
            window.location.reload();
          } else {
            alert(response.status + " : " + " خطای سیستم ");
          }
        });
    },
    deletep(id) {
      let url = process.env.VUE_APP_API_URL + "products/" + id;
      axios
        .delete(url, {
          headers: {
            Authorization: "Bearer " + localStorage.getItem("token"),
          },
        })
        .then((response) => {
          console.log(response);
          if (response.status == 200) {
            alert(" محصول حذف شد ");
            window.location.reload();
          } else if (response.status == 404) {
            alert(" کالا با این مشخصات فات نشد ");
          } else {
            alert(response.status + " : " + " خطای سیستم ");
          }
        });
    },
    handleFileUpload(event) {
      this.form.image = event.target.files[0];
    },
    submitFile(id) {
      let formData = new FormData();
      formData.append("image", this.file, this.file.name);
      console.log(id);
      axios
        .post(
          process.env.VUE_APP_API_URL + "products/image/" + id,
          formData,
          {
            onUploadProgress: (uploade) => {
              console.log(
                "Uploaded: " +
                  Math.round((uploade.loaded / uploade.total) * 100) +
                  "%"
              );
            },

            headers: {
              Authorization: "Bearer " + localStorage.getItem("token"),
            },
          }
          // {
          // headers: {
          //     'Content-Type': 'multipart/form-data'
          // }
          // },
        )
        .then((res) => {
          console.log(res);
        })
        .catch((err) => {
          console.log(err);
        });
    },
  },
  created() {
    let url = process.env.VUE_APP_API_URL + "products";
    axios.get(url).then((res) => {
      this.products = res.data;
    });

    let url2 = process.env.VUE_APP_API_URL + "ally";
    axios.get(url2).then((res) => {
      this.allies = res.data;
    });
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.product {
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

.product-maker {
  padding: 2%;
  width: 79%;
  margin: 5% 10%;
  border-radius: 15px;
  color: white;
  background: #262626bc;
  box-shadow: 0 4px 8px 0 rgba(17, 17, 17, 0.529), 0 6px 20px 0 rgba(17, 17, 17, 0.529);
}

input,
select,
option {
  color: #000;
  padding: 0;
  width: 90%;
  margin: 5%;
  border-radius: 15px;
  height: 40px;
  text-align: center;
}

.col1 {
  width: 100%;
}

textarea {
  width: 99%;
  margin: 1%;
  border-radius: 7px;
}

#create {
  width: 30%;
  background: #d51637;
  color: #fff;
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
@media screen and (min-width: 900px) {
  .col {
    width: 20%;
    margin: 0px;
    padding: 0px;
  }
}
</style>
