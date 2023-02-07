<!-- eslint-disable no-undef -->
<!-- eslint-disable no-undef -->
<template>
  <v-container>
    <h1 class="display-2 text-center font-weight-bold mb-3 my-5">
      Airline Reservation System
    </h1>

    <div>
      <v-data-table
        :server-items-length="totalPassengers"
        :headers="headers"
        :items="passengers"
        :items-per-page="10"
        :options.sync="options"
        :loading="loading"
      >
      </v-data-table>
    </div>

    <v-pagination
      v-model="page"
      :length="4"
      circle
      color="red"
      prev-icon="mdi-menu-left"
      next-icon="mdi-menu-right"
    ></v-pagination>
  </v-container>
</template>
<script>
import axios from "axios";
export default {
  name: "HelloWorld",
  data() {
    return {
      page: 1,
      totalPassengers: 0,
      totalPages: 0,
      passengers: [],
      pageno: 6290,
      airlineName: " ",
      loading: true,
      name: [],
      options: {},
      headers: [
        { text: "Passenger Name", value: "name" },
        { text: "Number of Trips", value: "trips" },
        {
          text: "Airline Name",
          sortable: false,
          value: "airline",
        },
      ],
      airlineData: [],
      reqObj: {
        name: "John Doe",
        trips: 250,
        airline: 5,
      },
    };
  },
  watch: {
    options: {
      handler() {
        this.getDataFromApi();
      },
      deep: true,
    },
  },
  methods: {
    async getDataFromApi() {
      this.loading = true;
      await axios
        .get(`https://api.instantwebtools.net/v1/passenger?page=6297&size=10`)
        .then((response) => (this.airlineData = response.data))
        .catch((error) => console.log(error));
      console.log(this.airlineData);

      this.totalPassengers = this.airlineData.totalPassengers;
      this.totalPages = this.airlineData.totalPages;

      console.log(
        "The total number of passengers till date is: " + this.totalPassengers
      );
      console.log(
        "The total number of Pages in the API is: " + this.totalPages
      );
      this.loading = false;
      this.getPassengersData();
      this.fakeApiCall();
    },
    async getPassengersData() {
      await axios
        .get(`https://api.instantwebtools.net/v1/passenger?page=3889&size=10`)
        .then((response) => (this.passengers = response.data.data))
        .catch((error) => console.log(error));
      console.log("Passenger data fetched from API is: " + this.passengers);

      for (var i = 0; i < this.passengers.length; i++) {
        this.airlineName = this.passengers[i].airline;
        this.name = this.passengers[i].name;
        console.log(this.name);
        console.log(this.airlineName);
      }
    },
    fakeApiCall() {
      return new Promise((resolve) => {
        const { sortBy, sortDesc, page, itemsPerPage } = this.options;

        let items = this.getPassengersData();
        const total = items.length;

        if (sortBy.length === 1 && sortDesc.length === 1) {
          items = items.sort((a, b) => {
            const sortA = a[sortBy[0]];
            const sortB = b[sortBy[0]];

            if (sortDesc[0]) {
              if (sortA < sortB) return 1;
              if (sortA > sortB) return -1;
              return 0;
            } else {
              if (sortA < sortB) return -1;
              if (sortA > sortB) return 1;
              return 0;
            }
          });
        }

        if (itemsPerPage > 0) {
          items = this.passengers.splice(
            (page - 1) * itemsPerPage,
            page * itemsPerPage
          );
        }

        setTimeout(() => {
          resolve({
            items,
            total,
          });
        }, 1000);
      });
    },
  },
};
</script>
