<template>
  <v-container grid-list-lg>
    <v-layout row wrap>
      <v-flex v-for="item in statistics" :key="item.id" lg3 sm6 xs12>
        <mini-statistic :icon="item.icon" :title="item.title" :sub-title="item.subtitle" :color="item.color">
        </mini-statistic>
      </v-flex>
    </v-layout>
  </v-container>
</template>

<script>
import MiniStatistic from "../components/Statistic/MiniStatistic.vue";

export default {
  components: {
    MiniStatistic
  },
  data() {
    return {
      colortxt: "light-green lighten-3",
      colorbgtitle: "light-green lighten-2",
      urlgetdata: "",
      card1: true,
      card2: true,
      statistics: [
        {
          icon: "meeting_room",
          title: "12",
          subtitle: "Phòng còn trống",
          color: "green lighten-1"
        },
        {
          icon: "no_meeting_room",
          title: "55",
          subtitle: "Phòng đã đặt",
          color: "blue lighten-1"
        },
        {
          icon: "bookmarks",
          title: "52",
          subtitle: "Đơn thanh toán",
          color: "indigo lighten-1"
        },
        {
          icon: "monetization_on",
          title: "5,438,432",
          subtitle: "Thu theo ngày",
          color: "red lighten-1"
        }
      ],
      status: [
        { name: "Phòng đã đặt", color: "deep-orange lighten-1", num: 0 },
        { name: "Phòng chưa đặt", color: "cyan lighten-1", num: 35 },
        { name: "Nhân viên", color: "pink lighten-1", num: 3 },
        { name: "All Oder", color: "light-green lighten-1", num: 8 }
      ]
    };
  },
  watch: {},
  computed: {},
  created() {},
  methods: {
    setStatus() {}
  }
};
</script>
