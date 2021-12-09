<template>
  <v-card>
    <v-card-title class="searchBar">
      نتیجه جستجو
      <v-spacer></v-spacer>
      <v-text-field
        v-model="search"
        append-icon="mdi-magnify"
        label="جستجو"
        single-line
        hide-details
      ></v-text-field>
    </v-card-title>
    <v-data-table
      :headers="getHeaders()"
      :items="my_json"
      :search="search"
      :footer-props="{
        'items-per-page-options': items_per_page,
        'items-per-page-text': 'تعداد سوال ها در صفحه',
        showFirstLastPage: true,
        pageText: '{0} از {1}',
      }"
    >
      <template v-slot:item="{ item }">
        <tr>
          <td>
            <v-tooltip bottom>
              <template v-slot:activator="{ on, attrs }">
                <span v-bind="attrs" v-on="on">{{
                  item.NAM_QUESTION_QSTN.substring(0, 80) + " ..."
                }}</span>
              </template>
              <span>{{ item.NAM_QUESTION_QSTN }}</span>
            </v-tooltip>
          </td>
          <td>{{ totalReplies(item.OPTIONS) }}</td>
          <td v-for="option in item.OPTIONS" :key="option.ID">
            <v-tooltip bottom>
              <template v-slot:activator="{ on, attrs }">
                <span v-bind="attrs" v-on="on">
                  {{
                    calculatePercentagOfReplies(
                      totalReplies(item.OPTIONS),
                      option.COUNTRPLY
                    )
                  }}
                </span>
              </template>
              <span>{{ option.NAM_OPTION_RSDTL }}</span>
            </v-tooltip>
          </td>
          <td
            v-for="(empty, index) in totalOptions() - item.OPTIONS.length"
            :key="index"
          ></td>
        </tr>
      </template>
    </v-data-table>
  </v-card>
</template>

<script>
export default {
  name: "DataTable",
  data() {
    return {
      search: "",
      my_json: [],
      items_per_page: [5, 10, 20, 50],
      jsonFile: "../data/test.json",
    };
  },
  components: {},
  computed: {},
  created: function () {
    fetch(this.jsonFile, {
      headers: {
        "Content-Type": "application/json",
        Accept: "application/json",
      },
    })
      .then((response) => response.json())
      .then((messages) => {
        this.my_json = messages;
      });
  },
  methods: {
    getHeaders() {
      var headerArray = [];
      let totalOptions = this.totalOptions();
      headerArray.push({
        text: "عنوان سوال",
        value: "NAM_QUESTION_QSTN",
        sortable: true,
      });
      headerArray.push({
        text: "تعداد نفرات پاسخ دهنده",
        value: "total_replies",
        sortable: true,
      });
      for (let i = 1; i <= totalOptions; i++) {
        headerArray.push({
          text: "گزینه" + i,
          value: "option_" + i,
          sortable: true,
        });
      }
      return headerArray;
    },
    totalOptions() {
      let options = [];
      //   const myObj = JSON.parse(this.my_json);

      for (let i = 0; i < this.my_json.length; i++) {
        options.push(this.my_json[i].OPTIONS.length);
      }
      var max_items = Math.max.apply(Math, options);
      return max_items;
    },
    totalReplies(options) {
      var total = 0;
      for (let i = 0; i < options.length; i++) {
        total = total + options[i].COUNTRPLY;
      }
      return total;
    },
    calculatePercentagOfReplies(totalReply, COUNTRPLY) {
      var percent = 100 / totalReply;
      var last = (COUNTRPLY * percent).toFixed(2);
      return last + "%";
    },
    filterOnlyCapsText(value, search) {
      return (
        value != null &&
        search != null &&
        typeof value === "string" &&
        value.toString().toLocaleUpperCase().indexOf(search) !== -1
      );
    },
  },
};
</script>

<style>
tr {
  border-bottom: 1px solid #333;
}
.searchBar {
  background: rgb(54, 105, 201);
  color: white;
}
.theme--light.v-label {
  color: white !important;
}
.theme--light.v-input input,
.theme--light.v-input textarea {
  color: white !important;
}
.theme--light.v-text-field > .v-input__control > .v-input__slot:before {
  border-color: rgba(255, 255, 255, 0.42) !important;
}
.mdi-magnify {
  color: rgba(255, 255, 255, 1) !important;
}
</style>
