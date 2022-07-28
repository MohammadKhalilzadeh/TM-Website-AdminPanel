<template>
  <div class="bills">
    <center>
      <h1>رسیدها</h1>

      <div class="orders-form" v-for="bill in bills" :key="bill._id">
        <br />
        <keep-alive>
          <table>
            <tr>
              <td v-if="bill.Status == 'Pending'">وضعیت : درانتظار</td>
              <td v-if="bill.Status == 'Seen'">وضعیت : در حال بررسی</td>
              <td v-if="bill.Status == 'On Way'">وضعیت : ارسال شده</td>
              <td>{{ bill.Datetime }}</td>
            </tr>
            <tr>
              <td>قیمت کل : {{ bill.TotalPrice }} ریال</td>
              <td>سهم مالک : {{ bill.PartnerShare }} ریال</td>
            </tr>
            <tr>
              <td>آدرس خریدار : {{ bill.CustomerAddress }}</td>
              <td>تلفن خریدار : {{ bill.CustomerPhone }}</td>
            </tr>
            <tr>
              <td>نام محصول : {{ bill.item.name }}</td>
              <td>قیمت محصول : {{ bill.item.price }} ریال</td>
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
  name: "bills",
  props: {
    msg: String,
  },
  data() {
    return {
      bills: [],
    };
  },
  created() {
    let url = process.env.VUE_APP_API_URL + "bills/done";
    axios
      .get(url, {
        headers: {
          Authorization: "Bearer " + localStorage.getItem("token"),
        },
      })
      .then((res) => {
        if (res.status == 200) {
          this.bills = res.data;
        } else if (res.status == 500) {
          alert(" اختلال در دریافت سفارشات ");
        } else {
          alert(" مشکل در برقراری اطلاعات با سرور ");
        }
      });
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.bills {
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

.orders-form {
  width: 80%;
  margin: 5% 10%;
  padding: 2%;
  border-radius: 15px;
  color: white;
  background: #262626bc;
  box-shadow: 0 4px 8px 0 rgba(17, 17, 17, 0.529), 0 6px 20px 0 rgba(17, 17, 17, 0.529);
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

/* From cssbuttons.io */
button {
  position: relative;
  border: none;
  background: transparent;
  padding: 0;
  cursor: pointer;
  outline-offset: 4px;
  transition: filter 250ms;
  user-select: none;
  touch-action: manipulation;
}

.shadow {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  border-radius: 12px;
  background: hsl(0deg 0% 0% / 0.25);
  will-change: transform;
  transform: translateY(2px);
  transition: transform 600ms cubic-bezier(0.3, 0.7, 0.4, 1);
}

.edge {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  border-radius: 12px;
  background: linear-gradient(
    to left,
    hsl(340deg 100% 16%) 0%,
    hsl(340deg 100% 32%) 8%,
    hsl(340deg 100% 32%) 92%,
    hsl(340deg 100% 16%) 100%
  );
}

.front {
  display: block;
  position: relative;
  padding: 12px 27px;
  border-radius: 12px;
  font-size: 1.1rem;
  color: white;
  background: hsl(345deg 100% 47%);
  will-change: transform;
  transform: translateY(-4px);
  transition: transform 600ms cubic-bezier(0.3, 0.7, 0.4, 1);
}

button:hover {
  filter: brightness(110%);
}

button:hover .front {
  transform: translateY(-6px);
  transition: transform 250ms cubic-bezier(0.3, 0.7, 0.4, 1.5);
}

button:active .front {
  transform: translateY(-2px);
  transition: transform 34ms;
}

button:hover .shadow {
  transform: translateY(4px);
  transition: transform 250ms cubic-bezier(0.3, 0.7, 0.4, 1.5);
}

button:active .shadow {
  transform: translateY(1px);
  transition: transform 34ms;
}

button:focus:not(:focus-visible) {
  outline: none;
}
</style>
