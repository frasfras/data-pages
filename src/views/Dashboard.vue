<template>
  <div>
    <h1>Dashboard</h1>
    <v-data-table
      :headers="headers"
      :items="items"
      :items-per-page="5"
      class="elevation-1"
      @click:row="selectRow"
    ></v-data-table>
    <v-snackbar v-model="snackbar">
      You have selected {{ currentItem }}
      <v-btn color="pink" text @click="snackbar = false">
        Close
      </v-btn>
    </v-snackbar>
  </div>
</template>
//:headers="headers" //:items="desserts"
<script>
import axios from "axios";

export default {
  name: "DashboardPage",
  data() {
    return {
      headers: [
        { text: "Id", value: "id" },
        { text: "Hole", value: "Hole" },
        { text: "Greens", value: "Greens", sortable: false, width: "100" },
        { text: "Fairway", value: "Fairway", name: "url", width: "180" },
        { text: "Roughs", value: "Roughs", sortable: false },
      ],
      items: [],
      dialog: false, // used to toggle the dialog
      editedItem: {},
      currentItem: "",
      snackbar: false,
      headers1: [
        {
          text: "Dessert (100g serving)",
          align: "left",
          sortable: false,
          value: "name",
        },
        { text: "Calories", value: "calories" },
        { text: "Fat (g)", value: "fat" },
        { text: "Carbs (g)", value: "carbs" },
        { text: "Protein (g)", value: "protein" },
        { text: "Iron (%)", value: "iron" },
      ],
      desserts: [
        {
          name: "Frozen Yogurt",
          calories: 159,
          fat: 6.0,
          carbs: 24,
          protein: 4.0,
          iron: "1%",
        },
        {
          name: "Ice cream sandwich",
          calories: 237,
          fat: 9.0,
          carbs: 37,
          protein: 4.3,
          iron: "1%",
        },
        {
          name: "Eclair",
          calories: 262,
          fat: 16.0,
          carbs: 23,
          protein: 6.0,
          iron: "7%",
        },
        {
          name: "Cupcake",
          calories: 305,
          fat: 3.7,
          carbs: 67,
          protein: 4.3,
          iron: "8%",
        },
        {
          name: "Gingerbread",
          calories: 356,
          fat: 16.0,
          carbs: 49,
          protein: 3.9,
          iron: "16%",
        },
        {
          name: "Jelly bean",
          calories: 375,
          fat: 0.0,
          carbs: 94,
          protein: 0.0,
          iron: "0%",
        },
        {
          name: "Lollipop",
          calories: 392,
          fat: 0.2,
          carbs: 98,
          protein: 0,
          iron: "2%",
        },
        {
          name: "Honeycomb",
          calories: 408,
          fat: 3.2,
          carbs: 87,
          protein: 6.5,
          iron: "45%",
        },
        {
          name: "Donut",
          calories: 452,
          fat: 25.0,
          carbs: 51,
          protein: 4.9,
          iron: "22%",
        },
        {
          name: "KitKat",
          calories: 518,
          fat: 26.0,
          carbs: 65,
          protein: 7,
          iron: "6%",
        },
      ],
    };
  },
   mounted() {
    this.loadItems()
  },
  methods: {
    selectRow(event) {
      this.snackbar = true;
      this.currentItem = event.name;
    },


    loadItems() {
      this.items = [];
     

      axios
        .get(`https://api.airtable.com/v0/appneKtre6Ux4ESiq/waterplan`, {
          headers: { Authorization: "Bearer keyjVpG4zXCD49VfC"  },
        })
        .then((response) => {
          // load the API response into items for datatable
          this.items = response.data.records.map((item) => {
            return {
              id: item.id,
              ...item.fields,
            };
          });
        })
        .catch((error) => {
        //  console.log(error);
        });
    },
	saveItem(item) {
        /* this is used for both creating and updating API records
         the default method is POST for creating a new item */

        let method = "post"
        let url = `https://api.airtable.com/v0/appneKtre6Ux4ESiq/waterplan`
        let id = item.id

        // airtable API needs the data to be placed in fields object
        let data = {
            fields: item
        }

        if (id) {
            // if the item has an id, we're updating an existing item
            method = "patch"
            url = `https://api.airtable.com/v0/appneKtre6Ux4ESiq/waterplan/${id}`

            // must remove id from the data for airtable patch to work
            delete data.fields.id
        }

        // save the record
        axios[method](url,
            data,
            { headers: { 
                Authorization: "Bearer keyjVpG4zXCD49VfC",
                "Content-Type": "application/json"
            }
        }).then((response) => {
            if (response.data && response.data.id) {
                // add new item to state
                this.editedItem.id = response.data.id
                if (!id) {
                    // add the new item to items state
                    this.items.push(this.editedItem)
                }
                this.editedItem = {}
            }
            this.dialog = !this.dialog
        })
    },
  },
};
</script>

<style></style>
