<template>
  <div>
    <h3 class="page-title">Pivot Table Demo</h3>
    <Pivot
      ref="pivot"
      toolbar
      v-bind:height="600"
      v-bind:report="'https://cdn.flexmonster.com/github/demo-report.json'"
      v-bind:shareReportConnection="{url: 'https://olap.flexmonster.com:9500'}"
      v-bind:beforetoolbarcreated="customizeToolbar"
      _v-bind:licenseKey="'XXXX-XXXX-XXXX-XXXX-XXXX'"
    ></Pivot>
  </div>
</template>

<script>
import Pivot from "@/components/Pivot.vue";
// import 'flexmonster/flexmonster.css';

export default {
  name: "PivotTableDemo",
  methods: {
    customizeToolbar: function(toolbar) {
      toolbar.showShareReportTab = true;
    }
  },
  components: {
     Pivot
  }
};
</script>
