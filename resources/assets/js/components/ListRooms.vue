<template>
  <v-layout column>
    <v-layout pl-4 pr-4 mb-1 column>
      <v-card color="blue--text">

        <v-card-title class="align-center display-1 font-weight-bold">

          <v-toolbar flat color="white">
            <v-toolbar-title style="margin-left:-25px" class="text-xs-center blue--text display-1">
              Phòng
            </v-toolbar-title>
            <dialog-add-room style="margin-left:-20px"></dialog-add-room>
            <v-spacer></v-spacer>

            <v-fab-transition>
              <v-tooltip dark="" color="blue lighten-1" bottom="">
                <v-btn slot="activator" flat fab color="blue lighten-1" dark @click="getData()">
                  <v-icon>autorenew</v-icon>
                </v-btn>
                <span>Tải lại dữ liệu phòng</span>
              </v-tooltip>
            </v-fab-transition>

          </v-toolbar>

          <v-flex xs12>
            <v-divider></v-divider>
            <v-layout pt-2 row wrap class="body-1">
              <v-flex xs12 sm7 md7 lg7>
                <v-text-field @keyup.enter="pagination.keyword = search" v-model="search" color="white--text blue lighten-1" label="Tìm phòng" append-icon="search"></v-text-field>
              </v-flex>
              <v-flex sm1 md1 lg1>

              </v-flex>
              <v-flex xs12 sm4 md4 lg4>
                <v-spacer></v-spacer>
                <v-combobox v-model="itemselect" :items="items" label="Loại phòng">
                  <template slot="selection" slot-scope="data">
                    <v-chip :selected="data.selected" :disabled="data.disabled" :key="JSON.stringify(data.item)" class="v-chip--select-multi " @input="data.parent.selectItem(data.item)">
                      <v-avatar class="accent white--text">
                        {{ data.item.slice(0, 1).toUpperCase() }}
                      </v-avatar>
                      {{ data.item }}
                    </v-chip>
                  </template>
                </v-combobox>
              </v-flex>
            </v-layout>
          </v-flex>
        </v-card-title>

        <v-card-text>

          <v-data-table :loading="loading" :headers="headers" :items="rooms.data" :search="pagination.keyword" :pagination.sync="pagination" :total-items="rooms.total" hide-actions>
            <template slot="headerCell" slot-scope="props">
              <v-tooltip bottom>
                <span slot="activator">
                  {{ props.header.text }}
                </span>
                <span>
                  {{ props.header.text }}
                </span>
              </v-tooltip>
            </template>
            <template slot="items" slot-scope="props">
              <td class="text-xs-left">{{ props.item.name }}</td>
              <td class="text-xs-left">{{ props.item.type }}</td>
              <td class="text-xs-left">{{ props.item.price | currency }}</td>
              <td class="text-xs-right">
                <v-layout row wrap>
                  <v-spacer></v-spacer>
                  <dialog-edit-room :room="props.item"></dialog-edit-room>
                </v-layout>
              </td>
            </template>
          </v-data-table>
          <div class="text-xs-center pt-2">
            <v-pagination color="white--text blue darken-1" v-model="pagination.page" :length="rooms.last_page" circle></v-pagination>
          </div>

        </v-card-text>

      </v-card>

    </v-layout>
  </v-layout>
</template>

<script>
import DialogEditRoom from "./DialogRoom/Dialogeditroom.vue";
import DialogAddRoom from "./DialogRoom/Dialogaddroom.vue";

export default {
  components: {
    DialogEditRoom,
    DialogAddRoom
  },
  data() {
    return {
      headers: [
        {
          text: "Tên Phòng",
          align: "left",
          sortable: false,
          value: "name"
        },
        { text: "Loại Phòng", sortable: false, value: "type", align: "left" },
        { text: "Giá phòng", sortable: false, value: "price", align: "left" },
        { text: "Thao tác", sortable: false, align: "center" }
      ],
      rooms: {},
      pagination: {},
      search: "",
      loading: false,
      itemselect: "",
      items: ["Phòng Đôi", "Phòng Đơn", "Phòng Gia Đình", "Phòng Vip"]
    };
  },
  filters: {
    currency(value) {
      var formatter = Intl.NumberFormat("en-US", {
        style: "currency",
        currency: "VND",
        minimumFractionDigits: 0
      });
      return formatter.format(value);
    }
  },
  created() {
    this.getData();
  },
  mounted() {},
  watch: {
    pagination: {
      handler() {
        this.getData(true);
      },
      deep: true
    },
    search() {
      this.search == ""
        ? (this.pagination.keyword = "")
        : (this.itemselect = "");
    },
    itemselect() {
      this.itemselect != ""
        ? (this.pagination.keyword = this.itemselect)
        : null;
    }
  },
  methods: {
    getData(withPagination = false) {
      this.loading = true;
      axios
        .get("/room?limit=6", {
          params: withPagination ? this.pagination : null
        })
        .then(response => {
          this.rooms = response.data;
          this.loading = false;
          // console.log("LOG", this.rooms.data);
        });
    }
  },
  computed: {}
};
</script>
