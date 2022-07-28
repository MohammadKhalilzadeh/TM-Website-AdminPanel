<template>
  <div class="identify">
    <center>
      <h1>احرازهویت‌ها</h1>
      
      <div class="searchbox">
        <input type="text" name="search" id="search" placeholder="search..." v-model="search" @keydown.enter="findit">
        <div class="results" v-for="result in results" :key="result._id" >

          <div class="row row-cols-3">
            <div class="col">
            نام : 
              {{ result.firstname }}
            </div>
            <div class="col">
              <button v-if="result.verified == false" @click="verifyit(result._id)"> تایید نشده </button>
              <p v-else> تایید شده </p>
            </div>
            <div class="col">
            نام خانوادگی : 
              {{ result.lastname }}
            </div>

            <div class="col">
            ایمیل : 
              {{ result.email }}
            </div>
            <div class="col">
            مود : 
              {{ result.role }}
            </div>
            <div class="col">
            تلفن : 
              {{ result.phone }}
            </div>

            <div class="col">
            شهر : 
              {{ result.city }}
            </div>
            <div class="col">
            استان : 
              {{ result.province }}
            </div>
            <div class="col">
            کدپستی : 
              {{ result.postalcode }}
            </div>
          </div>

          <div class="row row-cols-1">
            <div class="col">
            آدرس : 
              {{ result.address }}
            </div>
          </div>

          <div class="row row-cols-2">
            <div class="col">
            شماره شبا : 
              {{ result.sheba }}
            </div>
            <div class="col">
            شماره کارت : 
              {{ result.card }}
            </div>
          </div>

          <div class="row row-cols-2" v-if="result.imgs.length > 0">
            <div class="col">
              <img :src="urladrs + result.imgs[0]" alt="image1" srcset="">
            </div>
            <div class="col">
              <img :src="urladrs + result.imgs[1]" alt="image2" srcset="">
            </div>
          </div>

        </div>
      </div>
    </center>
  </div>
</template>

<script>
import axios from "axios";
export default {
  name: "Identify",
  props: {
    msg: String,
  },
  data(){
    return {
      urladrs: process.env.VUE_APP_API_URL ,
      search:"",
      results:[],
      resulttxt:null,
    }
  },
  mounted(){
      let url = process.env.VUE_APP_API_URL + "users";
    axios
      .get(url, {
        headers: {
          Authorization: "Bearer " + localStorage.getItem("token"),
        },
      })
      .then((res) => {
        if (res.status == 200) {
          this.results = res.data;
          console.log(this.results);
        } else if (res.status == 500) {
          alert(" اختلال در دریافت سفارشات ");
        } else {
          alert(" مشکل در برقراری اطلاعات با سرور ");
        }
      });
  },
  methods:{
    findit(){
      
      let url = process.env.VUE_APP_API_URL + "users/search/" + this.search;
      axios
        .post(url)
        .then((res) => {
          if (res.status == 200) {
            this.results = []
            this.results.push(res.data)
            console.log(this.results);
            if(this.results.length < 1){
              return
            }

            this.results.forEach((item, index) => {
              if(index > 0) {
                document.getElementById('results').innerHTML += '<p> ${item.name} </p>'
              }
            })
            return

          } else {
            alert(res);
          }
        });
    },

    verifyit(id){
      console.log(localStorage.getItem("token"));
      let url = process.env.VUE_APP_API_URL + "users/verify/" + id;
      axios
        .put(url, {
        headers: { Authorization: "Bearer " + localStorage.getItem("token") },
      })
        .then((res) => {
          if (res.status == 200) {
            console.log(res.data);
            window.location.reload();
          } else {
            console.log(res);
          }
        });
    }
  },

};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.identify {
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

.searchbox{
  width: 80%;
  padding: 2%;
  color: white;
  background: #2626268c;
  box-shadow: 0 4px 8px 0 rgba(17, 17, 17, 0.529), 0 6px 20px 0 rgba(17, 17, 17, 0.529);
}

input{
  width: 60%;
  margin: 2% 20%;
  padding: 1%;
  border-radius: 15px;
}

.results{
  width: 96%;
  margin: 2% 2%;
  padding: 2%;
  border-radius: 10px;
  background-color: rgba(255, 255, 255, 0.599);
  box-shadow: 0 4px 8px 0 rgba(17, 17, 17, 0.529), 0 6px 20px 0 rgba(17, 17, 17, 0.529);
  color: black;
}

.col {
  margin: 1% 0%;
  padding: 1% 1%;
  border: 1px solid black;
}

button {
  width: 90%;
  margin: 1% 5%;
  padding: 1%;
  border-radius: 20px;
  background: rgb(148, 23, 23);
}

button:hover {
  width: 90%;
  margin: 1% 5%;
  padding: 1%;
  border-radius: 20px;
  background: rgb(23, 148, 23);
  transition-duration: 500ms;
}

img{
  width: 100%;
}

</style>
