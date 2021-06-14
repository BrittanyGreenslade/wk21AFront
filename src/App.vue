<template>
  <div id="app">
    <form id="candyForm" action="javascript:void(0)">
      <h2>Insert a new candy:</h2>
      <input type="text" placeholder="name" id="candyName" />
      <input type="text" placeholder="description" id="candyDesc" />
      <input type="text" placeholder="price" id="candyPrice" />
      <input type="text" placeholder="image url" id="candyImg" />
      <input @click="addCandy" type="submit" placeholder="submit" />
    </form>
    <br />
    <h2>Edit candy:</h2>
    <form id="editCandy" action="javascript:void(0)">
      <input type="text" placeholder="candy Id" id="candyId" />
      <input type="text" placeholder="updated name" id="updatedName" />
      <input type="text" placeholder="updated description" id="updatedDesc" />
      <input type="text" placeholder="updated price" id="updatedPrice" />
      <input type="text" placeholder="updated img url" id="updatedImg" />
      <input type="submit" placeholder="udpate candy" @click="editCandy" />
    </form>
    <form id="deleteForm" action="javascript:void(0)">
      <input
        type="text"
        placeholder="id of product being deleted"
        id="deleteId"
      />
      <input @click="deleteCandy" type="submit" placeholder="delete" />
    </form>
    <div v-for="candy in candies" :key="candy[4]">
      <h2>Name:{{ candy[0] }}</h2>
      <h3>Price:{{ candy[2] }}</h3>
      <p>Description: {{ candy[1] }}</p>
      <p>ID: {{ candy[4] }}</p>
      <img :src="candy[3]" alt="Candy image" />
    </div>
  </div>
</template>

<script>
import axios from "axios";
export default {
  name: "App",
  data() {
    return {
      candies: [],
    };
  },
  mounted() {
    this.getCandies();
  },
  methods: {
    getCandies() {
      axios
        .request({
          url: "http://127.0.0.1:5000/candy",
          method: "GET",
          headers: { "Content-Type": "application/json" },
        })
        .then((res) => {
          this.candies = res.data;
        })
        .catch((err) => {
          console.log(err);
        });
    },
    addCandy() {
      axios
        .request({
          url: "http://127.0.0.1:5000/candy",
          method: "POST",
          headers: { "Content-Type": "application/json" },
          data: {
            name: document.getElementById("candyName").value,
            desc: document.getElementById("candyDesc").value,
            price: document.getElementById("candyPrice").value,
            img: document.getElementById("candyImg").value,
          },
        })
        .then((res) => {
          this.candies.push(res.data);
          //I still suck at the refresh thing but how can I get the newly added candy to show on the page
          //without callling the get candies fn again after I push to the data? It wasn't working without this
          this.getCandies();
          document.getElementById("candyForm").reset();
        })
        .catch((err) => {
          console.log(err);
        });
    },
    editCandy() {
      axios
        .request({
          url: "http://127.0.0.1:5000/candy",
          method: "PATCH",
          headers: { "Content-Type": "application/json" },
          data: {
            name: document.getElementById("updatedName").value,
            candyId: document.getElementById("candyId").value,
            desc: document.getElementById("updatedDesc").value,
            price: document.getElementById("updatedPrice").value,
            img: document.getElementById("updatedImg").value,
          },
        })
        .then((res) => {
          this.candies.push(res.data);
          this.getCandies();
          document.getElementById("editCandy").reset();
          console.log(res.data);
          // for (candy in this.candies) {
          //   if (candy[4] === document.getElementById("candyId").value) {
          //     this.candies;
          //   }
          // }
        })
        .catch((err) => {
          console.log(err);
        });
    },
    deleteCandy() {
      axios
        .request({
          url: "http://127.0.0.1:5000/candy",
          method: "DELETE",
          headers: { "Content-Type": "application/json" },
          data: {
            candyId: document.getElementById("deleteId").value,
          },
        })
        .then((res) => {
          res;
          for (let i = 0; i < this.candies.length; i++) {
            if (
              this.candies[i][4] ===
              Number(document.getElementById("deleteId").value)
            ) {
              this.candies.splice(i, 1);
            }
          }
          document.getElementById("deleteForm").reset();
          this.getCandies();
        })
        .catch((err) => {
          console.log(err);
        });
    },
  },
};
</script>

<style>
img {
  width: 200px;
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
