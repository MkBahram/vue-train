<template>
  <v-card>
    <v-card-title>
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
    <v-data-table :headers="getHeaders()" :items="my_json" :search="search">
      <template slot="body" slot-scope="props">
        <tbody>
          <tr v-for="(item, index) in props.items" :key="index">
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
              {{
                calculatePercentagOfReplies(
                  totalReplies(item.OPTIONS),
                  option.COUNTRPLY
                )
              }}
            </td>
            <td
              v-for="(empty, index) in totalOptions() - item.OPTIONS.length"
              :key="index"
            ></td>
          </tr>
        </tbody>
      </template>
    </v-data-table>
  </v-card>
</template>

<script>
import Json from "../data/test.json";

export default {
  name: "DataTable",
  data() {
    return {
      search: "",
      my_json: Json,
      items_per_page: [5, 10, 20, 50],
    };
  },
  computed: {},
  methods: {
    getHeaders() {
      var headerArray = [];
      let totalOptions = this.totalOptions();
      headerArray.push({ text: "عنوان سوال", sortable: "desc" });
      headerArray.push({ text: "تعداد نفرات پاسخ دهنده" });
      for (let i = 1; i <= totalOptions; i++) {
        headerArray.push({ text: "گزینه" + i });
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
      var total = options.reduce((acc, val) => {
        return acc + parseInt(val.COUNTRPLY);
      }, 0);
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
</style>
