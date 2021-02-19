<template>
  <div class="home">
    <ToolBar
      :title="title"
      :description="description"
      :btnText="btnText"
      :btnLink="btnLink"
    />

    <v-container class="my-5">
      <v-menu custom rounded offset-y color="grey">
        <template v-slot:activator="{ attrs, on }">
          <v-btn color="grey" class="white--text ma-5" v-bind="attrs" v-on="on">
            {{ sort }}
          </v-btn>
        </template>

        <v-list>
          <v-list-item v-for="item in itemList" :key="item" link>
            <v-list-item-title
              v-text="item"
              @click="sortBy(item)"
            ></v-list-item-title>
          </v-list-item>
        </v-list>
      </v-menu>

      <div>
        <v-card
          flat          
          v-for="reservation in computedBookings"
          :key="reservation.id"
          align="start"
        >
          <v-layout
            row
            wrap
            :class="`pa-3 reservation ${!reservation.favorite}`"
          >
            <v-col xs="6" sm="3" md="3">
              <div>{{ reservation.title }}</div>
              <div class="caption grey--text">
                {{ reservation.date | datetime }}
              </div>
            </v-col>
            <v-col sm="3" md="3" class="d-none d-sm-flex">
              <div>
                Ranking <br />
                <v-rating
                  color="amber"
                  :value="reservation.ranking"
                  dense
                  readonly
                  size="14"
                ></v-rating>
              </div>
            </v-col>
            <v-col xs="3" sm="3" md="3">
              <v-btn
                text
                v-if="reservation.favorite"
                @click="addFavorite(reservation.id)"
              >
                Add Favorites <v-icon> mdi-heart-settings </v-icon></v-btn
              >
              <v-btn text disabled v-if="!reservation.favorite">
                Add Favorites <v-icon> mdi-heart-settings </v-icon></v-btn
              >
            </v-col>
            <v-col xs="3" sm="3" md="3">
              <v-btn>Edit</v-btn>
            </v-col>
          </v-layout>
        </v-card>
        <v-pagination
          class="my-2"
          v-model="pagination.current"
          :length="numOfPage"
          :total-visible="7"
          @input="onPageChange"
          circle
        >
        </v-pagination>
      </div>
    </v-container>
  </div>
</template>

<script>
import ToolBar from '../components/ToolBar'
export default {
  name: "Home",
  data() {
    return {
      pagination: {
        current: 1,
        total: 0,
        perPage: 5,
      },
      api: "https://localhost:5001/api/Bookings",
      itemList: [
        "By Date Ascending",
        "By Date Descending",
        "By Alphabetic Ascending",
        "By Alphabetic Descending",
        "By Ranking",
      ],
      sort: "Sort By",
      title: "Reservation List",
      description: "Lorem, ipsum dolor sit amet consectetur adipisicing elit. Similique illum recusandae itaque, ipsam sequi reiciendis reprehenderit ab perspiciatis",
      btnText: "Create Reservation",
      btnLink: "/AddReservation",
    };
  },
  components: {
    ToolBar
  },

  mounted() {
    this.loadReservations();
  },

  computed: {
    bookings() {
      return this.$store.getters.Reservations;
    },

    offset() {
      return (this.pagination.current - 1) * this.pagination.perPage;
    },

    numOfPage() {
      return Math.ceil(this.bookings.length / this.pagination.perPage);
    },

    limit() {
      return this.offset + this.pagination.perPage;
    },

    computedBookings() {
      if (this.offset > this.bookings.length) {
        this.pagination.current = this.numOfPage;
      }
      return this.bookings.slice(this.offset, this.limit);
    },
  },

  methods: {
    loadReservations() {
      this.$store.dispatch("loadReservations", this.api);
    },

    sortBy(prop) {
      this.sort = prop;

      if (prop === "By Ranking") {
        this.bookings.sort((a, b) => (a.ranking < b.ranking ? -1 : 1));
      }
      if (prop == "By Alphabetic Ascending") {
        this.bookings.sort((a, b) => (a.title > b.title ? -1 : 1));
      }
      if (prop == "By Alphabetic Descending") {
        this.bookings.sort((a, b) => (a.title < b.title ? -1 : 1));
      }
      if (prop == "By Date Ascending") {
        this.bookings.sort((a, b) => (a.date > b.date ? -1 : 1));
      }
      if (prop == "By Date Descending") {
        this.bookings.sort((a, b) => (a.title < b.title ? -1 : 1));
      }
    },

    onPageChange(page) {
      this.pagination.current = page;
    },
    
  },
};
</script>

<style>
.reservation.true {
  border-left: 4px solid red;
}
.reservation.false {
  border-left: 5px solid white;
}
</style>
