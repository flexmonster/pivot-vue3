<template>
  <div>
    <h3 class="page-title">
      Integrating
      <a
        class="title-link"
        target="blank"
        href="https://www.flexmonster.com/doc/integration-with-amcharts/?r=rm_vue"
        >with amCharts</a
      >
    </h3>
    <Pivot
      ref="pivot"
      toolbar
      v-bind:height="600"
      v-bind:report="'https://cdn.flexmonster.com/github/demo-report.json'"
      v-bind:reportcomplete="reportComplete"
      v-bind:shareReportConnection="{
        url: 'https://olap.flexmonster.com:9500',
      }"
      v-bind:beforetoolbarcreated="customizeToolbar"
      v-bind:licenseFilePath="'https://cdn.flexmonster.com/jsfiddle.charts.key'"
    ></Pivot>
    <div class="chart-container">
      <div id="amcharts-container" style="width: 100%; height: 500px"></div>
    </div>
  </div>
</template>

<script>
// amCharts imports
import * as am4core from "@amcharts/amcharts4/core";
import * as am4charts from "@amcharts/amcharts4/charts";
import am4themes_animated from "@amcharts/amcharts4/themes/animated";

/* Apply amCharts theme */
am4core.useTheme(am4themes_animated);

//Importing Flexmonster's connector for Amcharts:
import "flexmonster/lib/flexmonster.amcharts";
import Pivot from "@/components/Pivot.vue";

export default {
  name: "WithAmcharts",
  methods: {
    customizeToolbar: function (toolbar) {
      toolbar.showShareReportTab = true;
    },
    reportComplete: function () {
      this.$refs.pivot.flexmonster.off("reportcomplete");
      this.drawChart();
    },

    drawChart: function () {
      //Running Flexmonster's getData method for amCharts
      this.$refs.pivot.flexmonster.amcharts.getData(
        {},
        this.createChart.bind(this),
        this.updateChart.bind(this)
      );
    },

    createChart: function (chartData, rawData) {
      /* Apply amCharts theme */
      am4core.useTheme(am4themes_animated);

      /* Create chart instance */
      let chart = am4core.create("amcharts-container", am4charts.PieChart);

      /* Add data processed by Flexmonster to the chart */
      chart.data = chartData.data;

      /* Set an inner radius to transform a pie chart into a donut chart */
      chart.innerRadius = am4core.percent(50);

      /* Create and configure series for a pie chart */
      var pieSeries = chart.series.push(new am4charts.PieSeries());
      pieSeries.dataFields.category =
        this.$refs.pivot.flexmonster.amcharts.getCategoryName(rawData);
      pieSeries.dataFields.value =
        this.$refs.pivot.flexmonster.amcharts.getMeasureNameByIndex(rawData, 0);
      pieSeries.slices.template.stroke = am4core.color("#fff");
      pieSeries.slices.template.strokeWidth = 2;
      pieSeries.slices.template.strokeOpacity = 1;

      /* Create initial animation */
      pieSeries.hiddenState.properties.opacity = 1;
      pieSeries.hiddenState.properties.endAngle = -90;
      pieSeries.hiddenState.properties.startAngle = -90;

      this.chart = chart;
    },

    updateChart: function (chartData, rawData) {
      this.chart.dispose();
      this.createChart(chartData, rawData);
    },
  },
  beforeUnmount() {
    if (this.chart) {
      this.chart.dispose();
    }
  },
  components: {
    Pivot
  }
};
</script>
