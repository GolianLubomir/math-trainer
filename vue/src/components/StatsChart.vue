<template>
  <div>
    <p class="text-xl text-slate-600 font-bold text-center pb-2">Progres</p>
    <div>
      <apexchart
        height="300"
        type="area"
        :options="chartOptions"
        :series="chartSeries"
      ></apexchart>
    </div>
  </div>
</template>

<script>
import { reactive } from "vue";
import { useRouter } from "vue-router";
import { useStore } from "vuex";
import VueApexCharts from "vue3-apexcharts";

export default {
  components: {
    apexchart: VueApexCharts,
  },
  computed: {
    chartSeries() {
      const store = useStore();
      const router = useRouter();
      const gameName = router.currentRoute.value.name;
      const allScoreData = store.state.user.scores[gameName]?.all || [];
      if (allScoreData.length === 0) {
        return [];
      }
      const chartData = [];
      allScoreData.forEach((item) => {
        chartData.push(item.score);
      });
      const chartSeries = [
        {
          name: gameName,
          data: chartData,
          color: "#76c0f5",
        },
      ];
      return chartSeries;
    },
  },
  data() {
    return {
      chartOptions: reactive({
        chart: {
          id: "line-chart",
          type: "area",
          toolbar: {
            show: false,  // Set this to false to disable the toolbar
          },
        },
        fill: {
          type: "solid",
          opacity: 0.4,
        },
        stroke: {
          width: 2,
          curve: "smooth",
        },
        dataLabels: {
          enabled: false,
        },
      }),
    };
  },
};
</script>
