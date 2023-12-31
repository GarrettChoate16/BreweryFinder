<template>
  <body>
    <header>
      <img
        class="logo"
        v-bind:src="brewery.logo"
        onerror="this.onerror=null; this.src='/icons/altBarrel.png'"
      />
      <h1>
        {{ brewery.name }}
      </h1>
    </header>
    <div class="brewery-info">
      <div class="contact-info">
        <p class="address">
          {{ brewery.streetAddress }}, {{ brewery.city }}, {{ brewery.state }}
          {{ brewery.zip }}
        </p>
        <p class="phone">{{ brewery.phone }}</p>
        <p class="email">{{ brewery.email }}</p>
        <p>
          <a class="link" target="_blank" v-bind:href="brewery.website"
            >Website</a
          >
          |
          <a class="link" target="_blank" v-bind:href="brewery.mapLink">Map</a>
        </p>
      </div>
      <div class="photo-container">
        <img class="photo" v-bind:src="brewery.photo" onerror="this.onerror=null; this.src='/icons/altBrewery.jpg'"/>
      </div>
      <p class="description">{{ brewery.description }}</p>
    </div>
    <div class="beer-list-container">
      <hr />
      <div class="beer-list-title-container">
        <h3 class="beer-list-title">Beer List</h3>
      </div>
      <div class="beer-list">
        <beer v-for="beer in beers" v-bind:key="beer.id" v-bind:item="beer" />
      </div>
      <div
        class="addBeer-container"
        v-if="
          this.$store.state.user.role == 'admin' ||
          this.$store.state.user.role == 'brewery'
        "
      >
        <router-Link
          v-if="this.$store.state.user.role == 'admin'"
          class="addBeer"
          v-bind:to="{ name: 'addBeer', params: { Id: breweryId } }"
          >Add Beer
        </router-Link>
      </div>
    </div>
  </body>
</template>

<script>
import Beer from "../components/Beer.vue";
import beerService from "../services/BeerService";
import breweryService from "../services/BreweryService";

export default {
  components: { Beer },
  name: "breweryInfoView",
  data() {
    return {
      breweryId: this.$route.params.BreweryId,
      brewery: {},
      beers: [],
      newBeer: {},
    };
  },
  methods: {
    loadBrewery(id) {
      breweryService
        .getBrewery(id)
        .then((response) => {
          console.log("Reached loadBrewery in BreweryInfoView");
          console.log(response);
          this.brewery = response.data;
        })
        .catch((error) => {
          if (error.response) {
            console.log("Error loading brewery: ", error.response.status);
          } else if (error.request) {
            console.log(
              "Error loading brewery: unable to communicate to server"
            );
          } else {
            console.log("Error loading brewery: make request");
          }
        });
    },
    loadBeers(breweryId) {
      beerService
        .listBeers(breweryId)
        .then((response) => {
          console.log(response);
          console.log("Reached loadBeers in BreweryInfoView");
          this.beers = response.data;
        })
        .catch((error) => {
          if (error.response) {
            console.log("Error loading beer: ", error.response.status);
          } else if (error.request) {
            console.log("Error loading beer: unable to communicate to server");
          } else {
            console.log("Error loading beer: make request");
          }
        });
    },
    createNewBeer() {
      {
        beerService
          .addBeer(this.newBeer.brewerId)
          .then(() => {
            console.log("Reached createNewBeer in AddBeerView");
            this.newBeer = {};
            this.showMessage = false;
            this.$router.push({ name: "breweryInfo" });
          })
          .catch((error) => {
            console.log("reached catch");
            if (error.response) {
              console.log("Error adding beer: ", error.response.status);
            } else if (error.request) {
              console.log("Error adding beer: unable to communicate to server");
            } else {
              console.log("Error adding beer: make request");
            }
          });
      }
    },
    cancelForm() {
      this.newBeer = {};
      this.showMessage = false;
      this.$router.push({ name: "breweryInfo" });
    },
  },

  created() {
    console.log("Reached created in BreweryInfoView");
    const brewId = this.$route.params.breweryId;
    this.loadBrewery(brewId);
    this.loadBeers(brewId);
  },
};
</script>

<style scoped>
header {
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: row;
  background-color: #4a2328;
  border-radius: 10px;
  color: #fef9e1;
  padding: 10px;
  box-shadow: inset;
  box-shadow: 6px 12px 9px grey;
}

body {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  margin-left: 10vw;
  margin-right: 10vw;
}

.beer-list-container {
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: column;
}

.description {
  width: 30vw;
  text-align: justify;
  font-size: 14pt;
  margin: 20px;
  font-family: ser;
}

div,
p {
  font-family: sans-serif;
}

p {
  margin: 5px;
}

h2,
h3 {
  margin-top: 10px;
  margin-bottom: 10px;
}

.contact-info {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  margin-bottom: 20px;
  margin-top: 10px;
  font-size: 14pt;
  flex-wrap: wrap;
}

.logo {
  margin-right: 15px;
  height: 75px;
  padding-top: 10px;
  padding-bottom: 10px;
}

.photo {
  border-radius: 10px;
  max-width: 500px;
}

.photo-container {
  display: flex;
  justify-content: center;
}

.address,
.phone,
.email,
.link {
  text-decoration: none;
  text-align: center;
}

.link {
  color: blue;
}

.beer-list-title {
  font-weight: bold;
  font-size: 20pt;
  display: flex;
  justify-content: center;
  align-items: center;
  margin: 30px;
  background-color: #4a2328;
  color: #fef9e1;
  border-radius: 10px;
  padding: 10px;
  box-shadow: inset;
  box-shadow: 6px 12px 9px grey;
  font-family: serif;
}

.beer-list-title-container {
  display: flex;
  justify-content: center;
  align-items: center;
}

.beer-list {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  width: 35vw;
}

.addBeer {
  background-color: black;
  border-radius: 25px;
  cursor: pointer;
  color: #ffffff;
  font-family: sans-serif;
  font-size: 17px;
  font-weight: bold;
  padding: 16px 31px;
  text-decoration: none;
}

.addBeer-container {
  display: flex;
  justify-content: center;
  margin-top: 20px;
}

.brewery-info {
  background-color: #c570615c;
  border-radius: 10px;
  padding-left: 50px;
  padding-right: 50px;
  padding-bottom: 25px;
  padding-top: 25px;

  width: 30vw;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  margin: 30px;
}

hr {
  width: 55vw;
  margin: 0;
  border-color: #4a2328;
}

.beers {
  margin-top: 10px;
}
</style>
