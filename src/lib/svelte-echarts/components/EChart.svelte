<script lang="ts">
  import type {
    EChartsType as BaseEchartsType,
    EChartsOption,
    EChartsInitOpts,
    EChartsType,
    SetOptionOpts,
  } from 'echarts'
  import type { EChartsType as CoreEchartsType } from 'echarts/core'
  import { init, use } from 'echarts/core'
  import { onMount } from 'svelte'
  import { ThemeRiverChart } from 'echarts/charts'
  import { CanvasRenderer } from 'echarts/renderers'
  import { BarChart } from 'echarts/charts'
  import {
    DatasetComponent,
    GridComponent,
    TitleComponent,
    TooltipComponent,
    TransformComponent,
  } from 'echarts/components'

  let { options, initOptions, optionalOptions, theme, data, onclick, onkeydown, debugMode } =
    $props()
  // define the element in which echarts will draw charts
  let elementId: string = 'chart'
  let element: HTMLDivElement | null = null

  // chart component created by EChart
  let chart: CoreEchartsType | undefined = undefined

  use([
    BarChart,
    DatasetComponent,
    GridComponent,
    TooltipComponent,
    TransformComponent,
    CanvasRenderer,
    TitleComponent,
  ])

  const initChart = (elemId: string, initOpt: EChartsInitOpts) => {
    element = document.getElementById(elemId) as HTMLDivElement | null // type assertion required
    if (element && initOpt) {
      chart = init(element, theme, initOpt)
      chart.setOption(initOpt)
    }
  }

  onMount(() => {
    initChart(elementId, initOptions)
  })

  // the chart is updated with Runes update
  // options are props which are transfered from parent component (+page.svelte).
  // options are defined as $state() runes, so when options changes,
  // $effect function catches the change of them and reactively run the follwoing process.
  $effect(() => {
    if (chart) {
      chart.setOption(options, optionalOptions)
    }
  })
</script>

<div id={elementId} class="chart-area"></div>

{#if debugMode}
  <div
    class="chart-debug-viewer"
    {onclick}
    {onkeydown}
    aria-label="interactive chart"
    role="button"
    tabindex="0"
  >
    <h2 class="chart-title">Chart debug viewer</h2>
    <div>
      Options: <pre>{JSON.stringify(options, null, 2)}</pre>
    </div>
    <div>
      Data (first 5 items): <pre>{JSON.stringify(data.slice(0, 10), null, 2)}</pre>
    </div>
  </div>
{/if}

<style>
  .chart-area {
    background-color: #cccccc;
    padding: 50px;
    border-radius: 20px;
    width: 800px;
    height: 800px;
  }
  .chart-debug-viewer {
    background-color: #dddddd;
    padding: 30px;
    border-radius: 10px;
  }
</style>
