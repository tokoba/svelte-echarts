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

  let { options, initOptions, optionalOptions, data, onclick, onkeydown } = $props()
  // define the element in which echarts will draw charts
  let elementId: string = $state('chart')
  let element: HTMLDivElement | null = null

  // chart component created by EChart
  let chart: CoreEchartsType | undefined = undefined
  let theme: string | object | null = 'dark'

  use([
    BarChart,
    DatasetComponent,
    GridComponent,
    TooltipComponent,
    TransformComponent,
    CanvasRenderer,
    TitleComponent,
  ])

  const initChart = (elementId: string, initOpt: EChartsInitOpts) => {
    element = document.getElementById(elementId) as HTMLDivElement | null // type assertion required
    if (element && initOpt) {
      chart = init(element, theme, initOpt)
      console.log('chart is initialized')
      chart.setOption(initOpt)
    }
  }

  onMount(() => {
    initChart(elementId, initOptions)
  })

  $effect(() => {
    if (chart) {
      chart.setOption(options, optionalOptions)
    }
  })
</script>

<div id={elementId} class="chart-area"></div>

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
