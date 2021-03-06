<script lang="ts">
  import type { wordObject } from "./words";
  import { afterUpdate, beforeUpdate, onMount } from "svelte";
  import Highcharts from "highcharts/es-modules/masters/highmaps.src";
  import "highcharts/es-modules/masters/modules/data.src";

  export let words = [];
  export let currentCountry: undefined | wordObject;

  let chart: typeof Highcharts;
  let mapData;

  console.log(Highcharts);

  /* afterUpdate(()=>{ */
  /*   if(chart){ */
  /*   chart.update({ */
  /*     series:{ */
  /*       data: [ */
  /*         [currentCountry.key, 600], */
  /*         ...words.map((word) => { */
  /*         return [word.key, 1]; */
  /*       }) */
  /*     ] */
  /*     } */
  /*   }) */
  /* } */
  /* }) */
  beforeUpdate(() => {
    if (chart) {
      console.log(words);

      chart.update({
        series: {
          data: [
            [currentCountry.key, 600],
            ...words.map((word) => {
              return [word.key, 1];
            }),
          ],
        },
      });
    }
  });

  onMount(async () => {
    const response = await fetch(
      "https://code.highcharts.com/mapdata/custom/europe.geo.json"
    );
    mapData = await response.json();

    chart = Highcharts.mapChart("container", {
      chart: {
        map: mapData,
      },

      title: {
        text: "GeoJSON in Highmaps",
      },

      mapNavigation: {
        enabled: true,
        buttonOptions: {
          verticalAlign: "bottom",
        },
      },
      colorAxis: {
        tickPixelInterval: 100,
      },

      series: [
        {
          data: [[currentCountry?.key, 100]],
          dataLabels: {
            enabled: true,
            color: "#FFFFFF",
            formatter: function () {
              if (this.point.value && this.point.value !== 600) {
                return this.point.name;
              }
            },
          },
        },
      ],
    });
    console.log(chart);
  });
</script>

<main>
  <div id="container" />
</main>

<style>
  #container {
    width: 100vw;
    height: 60vh;
    display: flex;
  }
  .highcharts-container {
    margin: 0 auto;
    width: 100%;
  }
</style>
