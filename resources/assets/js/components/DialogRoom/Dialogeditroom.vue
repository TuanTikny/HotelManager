<template>
  <v-layout row justify-center>
    <v-dialog v-model="dialog" persistent max-width="600">
      <v-btn slot="activator" flat icon color="primary">
        <v-icon>info</v-icon>
      </v-btn>
      <v-card>
        <v-card-title style="margin-left: 20px" dark class="blue--text title text-uppercase  ">Chỉnh sửa phòng : {{room.name}}</v-card-title>
        <v-divider></v-divider>
        <v-card-text>
          <v-container grid-list-md>
            <v-layout wrap>
              <v-flex xs12 sm12 md12>
                <v-text-field color="blue lighten-1" label="Tên Phòng" :hint="texthint" v-model="roominfor.name" required></v-text-field>
              </v-flex>
              <v-flex xs12 sm6 md6>
                <v-combobox v-model="roominfor.type" :items="items" chips label="Loại phòng">
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
              <v-flex xs12 sm6 md6>
                <v-switch v-model="roominfor.status" :label='roominfor.status !=1 ? "Đang trống" :"Đã đặt" '></v-switch>
              </v-flex>
              <v-flex xs12 sm12 md12>
                <v-text-field v-model="roominfor.price" color="blue lighten-1" label="Giá phòng" :hint="texthint" required></v-text-field>
              </v-flex>
              <v-flex xs12>
                <v-textarea v-model="roominfor.note" color="blue lighten-1" type="text" name="input-5-3" label="Mô tả"></v-textarea>
              </v-flex>
            </v-layout>
          </v-container>
        </v-card-text>
        <v-card-actions>

          <v-btn flat="" color="red " @click="dialogdel=!dialogdel">Xóa Phòng</v-btn>
          <v-spacer></v-spacer>
          <v-btn flat="" color="primary" @click="getoldroom()">Trở về</v-btn>
          <v-btn flat="" color="primary" @click.native="setInfor()">Cập Nhật</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>

    <v-dialog persistent v-model="dialogdel" max-width="350">
      <v-card>
        <v-card-title dark class="red--text title text-uppercase  ">Xóa Phòng</v-card-title>
        <v-divider></v-divider>
        <v-card-text>
          Bạn có muốn xóa phòng {{roominfor.name}} ?
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="blue darken-1" flat="flat" @click="dialogdel = false">
            Trở Lại
          </v-btn>
          <v-btn color="red darken-1" flat="flat" @click="delroom()">
            Xóa
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>

  </v-layout>
</template>

<script>
export default {
  data() {
    return {
      dialog: false,
      dialogdel: false,
      texthint: "Đây là trường bắt buộc không được để trống",
      roominfor: {},
      inforold: {},
      items: ["Phòng Đôi", "Phòng Đơn", "Phòng Gia Đình", "Phòng Vip"],
      nameerror: false
    };
  },
  props: {
    room: {
      type: Object
    }
  },
  created() {},
  mounted() {
    this.roominfor = this.room;
    this.inforold = this.room;
  },
  watch: {
    roominfor() {
      return this.room;
    }
  },
  methods: {
    setInfor() {
      axios
        .put("/room/" + this.roominfor.id, {
          name: this.roominfor.name,
          type: this.roominfor.type,
          status: this.roominfor.status,
          price: this.roominfor.price,
          note: this.roominfor.note
        })
        .then(res => {
          if (!res.data.success) {
            this.$store.commit("SNACKBAR", {
              status: true,
              content: "Cập Nhật Phòng Thất Bại",
              type: "error",
              timeout: 1000
            });
            console.log(res.data);
          } else {
            this.$store.commit("SNACKBAR", {
              status: true,
              content: "Cập Nhật Phòng Thành Công",
              type: "success",
              timeout: 1000
            });
          }
        })
        .catch(error => {
          console.log(error);
        });
      this.dialog = false;
    },
    delroom() {
      axios
        .delete("/room/" + this.roominfor.id, {})
        .then(res => {
          if (!res.data.success) {
            console.log(this.nameerror);
            this.$store.commit("SNACKBAR", {
              status: true,
              content: "Xóa Thất Bại",
              type: "error",
              timeout: 1000
            });
          } else {
            this.$store.commit("SNACKBAR", {
              status: true,
              content: "Đã xóa phòng : " + this.roominfor.name,
              type: "warning",
              timeout: 1000
            });
          }
          console.log(res.data);
        })
        .catch(error => {
          console.log(error);
        });
      this.dialogdel = false;
    },
    // xử lí lấy lịch sử phòng để chèn lại khi đóng nút hủy
    getoldroom() {
      console.log(this.roominfor);
      this.inforold = this.detailroom;
      this.dialog = false;
    }
  }
};
</script>
