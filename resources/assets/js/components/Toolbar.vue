<template>
  <div>
    <v-toolbar :clipped-left="$vuetify.breakpoint.lgAndUp" color="blue lighten-1" dark app fixed>
      <v-toolbar-title style="margin-left:-20px">
        <v-toolbar-side-icon @click.stop="drawer = !drawer">
        </v-toolbar-side-icon>

        <span class="hidden-sm-and-down">
          {{$store.state.user.isadmin ? "Quản lý : "+$store.state.user.name : "Nhân viên : "+$store.state.user.name}}
        </span>
      </v-toolbar-title>
      <!-- <v-text-field flat solo-inverted hide-details prepend-inner-icon="search" label="Tìm kiếm" class="hidden-sm-and-down"></v-text-field> -->
      <v-spacer></v-spacer>

      <!-- <v-btn icon>
        <v-icon>more_vert</v-icon>
      </v-btn> -->

      <!-- <v-btn icon @click="dialog = true">
        <v-icon color="white">power_settings_new</v-icon>
      </v-btn> -->

      <!-- <v-btn icon large>
        <v-avatar size="32px" tile>
          <img src="images/person.png">
        </v-avatar>
      </v-btn> -->

      <div class="text-xs-center">
        <v-menu v-model="menu" :close-on-content-click="false" :nudge-width="200" offset-x>

          <v-btn icon slot="activator" dark>
            <v-icon>more_vert</v-icon>
          </v-btn>

          <v-card>
            <v-list>
              <v-list-tile avatar>
                <v-list-tile-avatar>
                  <img src="https://cdn.vuetifyjs.com/images/john.jpg" alt="John">
                </v-list-tile-avatar>

                <v-list-tile-content>
                  <v-list-tile-title>John Leider</v-list-tile-title>
                  <v-list-tile-sub-title>Founder of Vuetify.js</v-list-tile-sub-title>
                </v-list-tile-content>

                <v-list-tile-action>
                  <v-btn :class="fav ? 'red--text' : ''" icon @click="fav = !fav">
                    <v-icon>favorite</v-icon>
                  </v-btn>
                </v-list-tile-action>
              </v-list-tile>
            </v-list>

            <v-divider></v-divider>

            <v-list>
              <v-list-tile>
                <v-list-tile-action>
                  <v-switch v-model="message" color="purple"></v-switch>
                </v-list-tile-action>
                <v-list-tile-title>Enable messages</v-list-tile-title>
              </v-list-tile>

              <v-list-tile>
                <v-list-tile-action>
                  <v-switch v-model="hints" color="purple"></v-switch>
                </v-list-tile-action>
                <v-list-tile-title>Enable hints</v-list-tile-title>
              </v-list-tile>
            </v-list>

            <v-card-actions>
              <v-spacer></v-spacer>

              <v-btn flat @click="menu = false">Cancel</v-btn>
              <v-btn color="primary" flat @click="menu = false">Save</v-btn>
            </v-card-actions>
          </v-card>
        </v-menu>
      </div>

    </v-toolbar>

    <v-navigation-drawer :clipped="$vuetify.breakpoint.lgAndUp" v-model="drawer" fixed app>
      <v-list dense>
        <template v-for="item in items">
          <v-layout v-if="item.heading" :key="item.heading" row align-center>
            <v-flex xs6>
              <v-subheader v-if="item.heading">
                {{ item.heading }}
              </v-subheader>
            </v-flex>
            <v-flex xs6 class="text-xs-center">
              <a href="#!" class="body-2 black--text">EDIT</a>
            </v-flex>
          </v-layout>
          <v-list-group v-else-if="item.children" v-model="item.model" :key="item.text" :prepend-icon="item.model ? item.icon : item['icon-alt']" append-icon="">
            <v-list-tile slot="activator">
              <v-list-tile-content>
                <v-list-tile-title>
                  {{ item.text }}
                </v-list-tile-title>
              </v-list-tile-content>
            </v-list-tile>
            <v-list-tile v-for="(child, i) in item.children" :key="i" v-if="child.role.includes(is_admin)" @click="goto(child.name)">
              <v-list-tile-action v-if="child.icon">
                <v-icon>{{ child.icon }}</v-icon>
              </v-list-tile-action>
              <v-list-tile-content>
                <v-list-tile-title>
                  {{ child.text }}
                </v-list-tile-title>
              </v-list-tile-content>
            </v-list-tile>
          </v-list-group>
          <v-list-tile v-else-if="item.role.includes(is_admin)" :key="item.text" @click="goto(item.name)">
            <v-list-tile-action>
              <v-icon>{{ item.icon }}</v-icon>
            </v-list-tile-action>
            <v-list-tile-content>
              <v-list-tile-title>
                {{ item.text }}
              </v-list-tile-title>
            </v-list-tile-content>
          </v-list-tile>
        </template>
      </v-list>
    </v-navigation-drawer>

    <v-dialog persistent v-model="dialog" max-width="350">
      <v-card>
        <v-card-title class="white--text blue lighten-1 headline">Thoát Ứng Dụng</v-card-title>
        <v-card-text>
          Bạn có muốn đăng xuất khỏi tài khoản này ?
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="blue darken-1" flat="flat" @click="dialog = false">
            Trở Lại
          </v-btn>
          <v-btn color="blue darken-1" flat="flat" @click="logout()">
            Đăng Xuất
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </div>
</template>

<script>
export default {
  data: () => ({
    dialog: false,
    drawer: null,
    fav: true,
    menu: false,
    message: false,
    hints: true,
    items: [
      {
        text: "Thống kê",
        icon: "donut_small",
        name: "Home",
        role: ["admin", "user"]
      },
      {
        text: "Đặt Phòng",
        icon: "book",
        name: "Booking",
        role: ["admin", "user"]
      },
      {
        text: "Gọi dịch vụ",
        icon: "room_service",
        name: "Oderservice",
        role: ["admin", "user"]
      },
      {
        text: "Thanh Toán",
        icon: "payment",
        name: "Billbook",
        role: ["admin", "user"]
      },
      {
        icon: "keyboard_arrow_up",
        "icon-alt": "keyboard_arrow_down",
        text: "Quản lý chung",
        model: true,
        children: [
          {
            text: "Phòng và Dịch Vụ",
            icon: "meeting_room",
            name: "Roomsmg",
            role: ["admin"]
          },
          {
            text: "Nhân Viên",
            icon: "assignment_ind",
            name: "Usersmg",
            role: ["admin"]
          },
          {
            text: "Khách Hàng",
            icon: "supervisor_account",
            name: "Customer",
            role: ["admin", "user"]
          }
        ]
      }
    ]
  }),
  props: {
    source: String
  },
  methods: {
    goto(val) {
      // console.log(val);
      this.$router.replace({ name: val });
    },
    logout() {
      this.$store.dispatch("signOut");
      this.dialog = false;
      this.$store.commit("SNACKBAR", {
        status: true,
        content: "Đã Đăng Xuất Khỏi Ứng Dụng",
        type: "warning",
        timeout: 2000
      });
    }
  },
  computed: {
    is_admin() {
      return this.$store.state.user.isadmin ? "admin" : "user";
    }
  }
};
</script>
