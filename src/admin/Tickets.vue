<template>
  <div class="tickets">
    <center>
      <h1>تیکت‌ها</h1>

      <div class="ticket-paper" v-for="ticket in tickets" :key="ticket._id">
        <table>
          <tr>
            <th>{{ ticket.firstname }}</th>
            <th>{{ ticket.lastname }}</th>
          </tr>
          <tr>
            <th>{{ ticket.email }}</th>
            <th v-if="ticket.seen">بررسی شده</th>
            <th v-else>نیاز به رسیدگی</th>
          </tr>
        </table>
        <table>
          <tr>
            <td>{{ ticket.desc }}</td>
          </tr>
        </table>

        <button @click="seen(ticket._id)">بررسی شد</button>
      </div>
    </center>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "Tickets",
  props: {
    msg: String,
  },
  data() {
    return {
      tickets: [],
    };
  },
  created() {
    let url = process.env.VUE_APP_API_URL + "tickets";
    axios
      .get(url, {
        headers: {
          Authorization: "Bearer " + localStorage.getItem("token"),
        },
      })
      .then((res) => {
        if (res.status == 200) {
          this.tickets = res.data;
        } else {
          alert(" خطا در دریافت اطلاعات ");
        }
      });
  },
  methods: {
    seen(id) {
      let url = process.env.VUE_APP_API_URL + "tickets/" + id;
      axios
        .patch(
          url,
          {
            seen: true,
          },
          {
            headers: {
              Authorization: "Bearer " + localStorage.getItem("token"),
            },
          }
        )
        .then(function (res) {
          if (res.status == 200) {
            window.location.reload();
            alert(" وضعیت تیکت تغییر یافت ");
          } else {
            alert(" خطا در پردازش درخواست ");
          }
        });
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.tickets {
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

.ticket-paper {
  width: 70%;
  margin: 5% 10%;
  padding: 2%;
  border-radius: 15px;
  color: white;
  background: #262626bc;
  box-shadow: 0 4px 8px 0 rgba(17, 17, 17, 0.529), 0 6px 20px 0 rgba(17, 17, 17, 0.529);
}

table {
  border: 2px solid #d51637;
  padding: 1%;
}

th {
  width: 50%;
  border: 2px solid #d51637;
  padding: 1%;
}

td {
  width: 100%;
  padding: 1%;
}

table {
  width: 98%;
  margin: 1%;
}

button {
  width: 30%;
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
