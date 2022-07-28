<template>
  <div class="freelancers">
    <center>
      <h1>Freelancers</h1>

    <div class="flsform">
        <div class="row row-cols-auto">
            <div class="col">
                <input type="text" placeholder="Fullname" v-model="form.fullname" />
            </div>
            <div class="col">
                <input type="text" placeholder="Team" v-model="form.team" />
            </div>
            <div class="col">
                <input type="text" placeholder="Country" v-model="form.country" />
            </div>
            <div class="col">
                <progress max="100" :value.prop="uploadPercentage"></progress>
            </div>
        </div>
        <div class="col" style="width: 100%">
            <input type="file" @change="handleFileUpload" />
        </div>
        <button id="create" v-on:click="submit()">ADD</button>

    </div>

    <div class="flsform" style="width:85%">
        <h4>Freelancers List</h4>
        <br />
        <keep-alive>
          <table>
            <tr>
              <th>Full Name</th>
              <th>Team</th>
              <th>Country</th>
              <th>Image</th>
              <th>Op</th>
            </tr>
            <tr v-for="fl in fls" :key="fl._id">
              <td>{{ fl.fullname }}</td>
              <td>{{ fl.team }}</td>
              <td>{{ fl.country }}</td>
              <td>
                <img :src="url + fl.image" alt="" style="width: 100%" />
              </td>
              <td>
                <button id="delete" v-on:click="deleteally(fl._id)">
                  Delete
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
  name: "Freelancers",
  props: {
    msg: String,
  },
  data() {
    return {
      form: {
          fullname:'',
          team:'',
          country:'',
          image:null,
      },
      fls:[],
      uploadPercentage: 0,
      url: process.env.VUE_APP_API_URL,
    };
  },
  created() {
    let url = process.env.VUE_APP_API_URL + "fls";
    axios
      .get(url, {
        headers: {
          Authorization: "Bearer " + localStorage.getItem("token"),
        },
      })
      .then((res) => {
        if (res.status == 200) {
          this.fls = res.data;
        } else {
          alert(" Unable to get data ");
        }
      });
  },
  methods: {
    submit() {
        let formData = new FormData();
      formData.append("image", this.form.image, this.form.image.name);
      formData.append("fullname", this.form.fullname);
      formData.append("country", this.form.country);
      formData.append("team", this.form.team);
      let url = process.env.VUE_APP_API_URL + "fls";
      axios
        .post(
          url, formData, {
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
          if (res.status == 200) {
            window.location.reload();
            alert(" Added ");
          } else {
            alert("Error ");
          }
        });
    },
    handleFileUpload(event) {
      this.form.image = event.target.files[0];
    },
    async deleteally(id) {
      let url = process.env.VUE_APP_API_URL + "fls/" + id;
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
            alert(" Deleted ");
          }
        });
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.freelancers {
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

.flsform{
    width: 70%;
    margin: 5% 15%;
    padding: 2%;
    border: 2px solid #d51637;
    border-radius: 10px;
    color: white;
    background: #262626bc;
    box-shadow: 0 4px 8px 0 rgba(17, 17, 17, 0.529), 0 6px 20px 0 rgba(17, 17, 17, 0.529);
}

.row{
    width: 100%;
    padding: 1%;
}

.col{
    width: 50%;
    padding: 1%;
}

input {
  padding: 3%;
  width: 90%;
  margin: 5%;
  border-radius: 15px;
  height: 40px;
}

table {
  border: 2px solid #d51637;
  padding: 1%;
}

th {
  width: 20%;
  border: 2px solid #d51637;
  padding: 1%;
}

td {
  width: 20%;
  padding: 1%;
}

table {
  width: 98%;
  margin: 1%;
}

button {
  width: 80%;
  background: #d51637;
  color: white;
  margin: 1%;
  padding: 1%;
  border-radius: 20px;
  border: 2px solid #d51637;
}

button:hover {
  background: #d51637;
  color: white;
  box-shadow: 0 4px 8px 0 #df1a3a8c, 0 6px 20px 0 #ce1b3880;
}
</style>
