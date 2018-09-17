<template>
  <v-container fluid grid-list-md>
    <v-layout column>
      <v-layout pl-4 pr-4 mb-1 row wrap>
        <v-flex d-flex xs12 sm12 md12>
          <v-card color="blue--text">
            <v-card-title class="align-center headline ">
              <v-list-tile-content class="font-weight-bold">
                Tìm Oder cần dùng dịch vụ
              </v-list-tile-content>
              <v-flex xs7>
                <v-text-field v-model="search" @keyup.enter="keyword = search; getData()" color="white--text blue lighten-1" label="Nhập tên khách hàng" append-icon="search"></v-text-field>
              </v-flex>
            </v-card-title>
          </v-card>
        </v-flex>

        <v-flex xs12 sm12 md12>
          <v-layout row wrap>
            <v-flex xs6>

              <!-- Thông tin chi tiết đơn đặt -->
              <v-card color="blue--text">
                <v-card-title class="align-center headline font-weight-bold">
                  <v-list-tile-content class="font-weight-bold">
                    Chi tiết đơn đặt
                  </v-list-tile-content>
                  <v-flex xs1 style="margin-right:10px">
                    <v-btn @click="lock = !lock" flat icon color="primary" :disabled="result">
                      <v-icon v-if="lock">lock</v-icon>
                      <v-icon v-else>lock_open</v-icon>
                    </v-btn>
                  </v-flex>

                </v-card-title>
                <v-divider></v-divider>

                <v-card-text>
                  <v-list dense>
                    <v-list-tile-title class="blue--text">
                      Thông tin Khách hàng :
                    </v-list-tile-title>
                    <v-divider></v-divider>
                    <v-list-tile>
                      <v-list-tile-content>Tên khách hàng :</v-list-tile-content>
                      <v-list-tile-content v-model="order.customer.name" class="blue--text align-end  body-2 font-weight-bold">{{order.customer.name}}</v-list-tile-content>
                    </v-list-tile>
                    <v-list-tile>
                      <v-list-tile-content>Số điện thoại :</v-list-tile-content>
                      <v-list-tile-content v-model="order.customer.phone" class="align-end">{{order.customer.phone}}</v-list-tile-content>
                    </v-list-tile>

                    <v-list-tile>
                      <v-list-tile-content>Phòng đang thuê :</v-list-tile-content>
                      <v-list-tile-content v-model="order.room.name" class="blue--text align-end  body-2 font-weight-bold">{{order.room.name}}</v-list-tile-content>
                    </v-list-tile>

                    <v-list-tile-title class="blue--text">
                      Dịch vụ đang sử dụng :
                    </v-list-tile-title>

                    <v-divider></v-divider>

                    <v-data-table :headers="headers1" :items="order.data.services" :total-items="infor.total" hide-actions>
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
                        <td class="text-xs-left">{{ props.item.price | currency }}</td>
                        <td class="text-xs-right">
                          <v-layout row wrap>
                            <v-spacer></v-spacer>
                            <v-btn :disabled="lock" @click="clearoneservice(props.item)" fab small flat icon color="red">
                              <v-icon>remove</v-icon>
                            </v-btn>
                          </v-layout>
                        </td>
                      </template>
                    </v-data-table>

                  </v-list>
                </v-card-text>

                <v-card-actions>
                  <v-flex xs12>
                    <v-layout align-center justify-center row wrap>

                      <v-btn @click="addservices" color="primary" dark>
                        Đồng ý Thêm dịch vụ
                        <v-icon right dark>done_all</v-icon>
                        <!-- Remove if don't want to use icon. -->
                      </v-btn>
                    </v-layout>

                  </v-flex>
                </v-card-actions>
              </v-card>
            </v-flex>

            <!-- Thông tin dịch vụ có sẵn -->
            <v-flex xs6>
              <v-layout row wrap>
                <v-flex xs12>
                  <v-card color="blue--text" justify-center>
                    <v-card-title class="align-center  headline font-weight-bold">
                      Dịch vụ hiện có
                    </v-card-title>

                    <v-divider></v-divider>
                    <v-card-text>
                      <v-text-field @keyup.enter="paginationmyservice.keyword = searchservice" v-model="searchservice" color="white--text blue lighten-1" label="Tìm dịch vụ" append-icon="search"></v-text-field>
                      <v-data-table :loading="loadingservice" :headers="headers1" :items="myservice.data" :search="paginationmyservice.keyword" :pagination.sync="paginationmyservice" :total-items="myservice.total" hide-actions>
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
                          <td class="text-xs-left">{{ props.item.price | currency }}</td>
                          <td class="text-xs-right">
                            <v-layout row wrap>
                              <v-spacer></v-spacer>
                              <v-btn @click="addOneService(props.item)" :disabled="checkService(props.item.id)" flat icon color="primary">
                                <v-icon>add</v-icon>
                              </v-btn>
                            </v-layout>
                          </td>
                        </template>
                      </v-data-table>
                      <div class="text-xs-center pt-2">
                        <v-pagination color="white--text blue darken-1" v-model="paginationmyservice.page" :length="myservice.last_page" circle></v-pagination>
                      </div>
                    </v-card-text>

                  </v-card>
                </v-flex>
              </v-layout>
            </v-flex>

          </v-layout>

        </v-flex>
      </v-layout>

      <v-dialog persistent v-model="dialoglist" max-width="600">
        <v-card>
          <v-card-title dark class="blue--text title text-uppercase">Đơn hàng cần thêm dịch vụ</v-card-title>
          <v-divider></v-divider>

          <v-card-text>
            <v-flex xs5>

            </v-flex>
            <v-data-table :loading="loading" :headers="headers" :items="infor.data" :search="pagination.keyword" :pagination.sync="pagination" :total-items="infor.total" hide-actions>
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
                <td class="text-xs-left">{{ props.item.customer.name }}</td>
                <td class="text-xs-left">{{ props.item.from.split(" ")[0] }}</td>
                <td class="text-xs-center">
                  <v-btn @click.stop="getService(props.item)" flat icon color="success">
                    <v-icon>done</v-icon>
                  </v-btn>
                </td>
              </template>
            </v-data-table>
            <div class="text-xs-center pt-2">
              <v-pagination color="white--text blue darken-1" v-model="pagination.page" :length="infor.last_page" :total-visible="5" circle></v-pagination>
            </div>
          </v-card-text>
          <v-card-actions>
            <v-spacer></v-spacer>

            <v-btn color="blue darken-1" flat="flat" @click.stop="dialoglist = false; search =''">
              Đóng
            </v-btn>

          </v-card-actions>
        </v-card>
      </v-dialog>

    </v-layout>
  </v-container>
</template>

<script>
export default {
  data() {
    return {
      Oder: {},
      search: "",
      infor: {},
      pagination: {},
      loading: false,
      headers: [
        {
          text: "Tên khách hàng",
          align: "left",
          sortable: false,
          value: "name"
        },
        {
          text: "Đặt phòng ngày",
          sortable: false,
          value: "type",
          align: "left"
        },
        { text: "Chọn Oder", sortable: false, align: "center" }
      ],
      order: {
        customer: {
          name: "-",
          phone: "-"
        },
        room: {
          name: "-",
          price: "-"
        },
        user: {
          name: "-",
          phone: "-"
        },
        from: "-",
        to: "-",
        totaldate: "-",
        totalservice: "-",
        data: {
          services: []
        }
      },
      headers1: [
        {
          text: "Tên dịch vụ",
          align: "left",
          sortable: false,
          value: "name"
        },
        {
          text: "Giá tiền",
          sortable: false,
          value: "type",
          align: "left"
        },
        { text: "Thao tác", sortable: false, align: "right", flag: true }
      ],
      myservice: {},
      paginationmyservice: {},
      searchservice: "",
      loadingservice: false,
      dialoglist: false,
      keyword: "",
      lock: true,
      result: true
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
  watch: {
    pagination: {
      handler() {
        // this.getData(true);
      },
      deep: true
    },
    search() {
      if (this.search == "") {
        this.keyword = "";
      }
    },
    paginationmyservice: {
      handler() {
        this.getMyService(true);
      },
      deep: true
    },
    searchservice() {
      if (this.searchservice == "") {
        this.paginationmyservice.keyword = "";
      }
    }
  },
  methods: {
    getMyService(withPagination = false) {
      this.loadingservice = true;
      axios
        .get("/service?limit=6", {
          params: withPagination ? this.paginationmyservice : null
        })
        .then(response => {
          this.myservice = response.data;
          this.loadingservice = false;
          // console.log("LOG", this.infor.data);
        });
    },
    getService(object) {
      this.search = "";
      this.order = object;
      // đóng dialog
      this.result = false;
      this.dialoglist = false;
      console.log(this.order);
    },
    // thêm 1 service vào oder
    addOneService(item) {
      if (this.order.data.services.length !== 0) {
        this.order.data.services.push(item);
        console.log(this.order.data.services);
      } else {
        this.$store.commit("SNACKBAR", {
          status: true,
          content: "Vui tìm khách hàng cần sử dụng dịch vụ",
          type: "error",
          timeout: 2000
        });
      }
    },
    checkService(id) {
      for (let service of this.order.data.services) {
        if (service.id === id) return true;
      }
      return false;
    },
    // xóa 1 service khỏi oder
    clearoneservice(item) {
      this.order.data.services = this.order.data.services.filter(
        t => t !== item
      );
      console.log(this.order.data.services);
    },
    addservices() {
      // xử lí add sẻ
      console.log("Xử lí thanh toán");
    },
    getData(withPagination = false) {
      if (this.keyword != "") {
        this.dialoglist = true;
        this.loading = true;
        axios
          .get("/order?limit=5&keyword=" + this.keyword, {
            params: withPagination ? this.pagination : null
          })
          .then(response => {
            this.infor = response.data;
            this.loading = false;
          });
      } else {
        // Xử lí khi chưa nhập tên khách hàng
        this.$store.commit("SNACKBAR", {
          status: true,
          content: "Chưa nhập tên khách hàng!",
          type: "error",
          timeout: 2000
        });
      }
    }
  },
  created() {
    this.getMyService();
  }
};
</script>

