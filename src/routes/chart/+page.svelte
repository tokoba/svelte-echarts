<script lang="ts">
  import { onMount } from 'svelte'
  import { Chart, type ECMouseEvent } from 'svelte-echarts'
  import { use } from 'echarts/core'
  import EChart from './EChart.svelte'
  import type { EChartsOption, EChartsInitOpts, SetOptionOpts } from 'echarts'
  import { CanvasRenderer } from 'echarts/renderers'
  import { BarChart, LineChart } from 'echarts/charts'
  import {
    DatasetComponent,
    GridComponent,
    TitleComponent,
    TooltipComponent,
    TransformComponent,
  } from 'echarts/components'

  use([
    BarChart,
    DatasetComponent,
    GridComponent,
    TooltipComponent,
    TransformComponent,
    CanvasRenderer,
    TitleComponent,
  ])

  /* format of the data is two dimensional */
  type Data = {
    timestamp: Date
    value: number
  }
  type DataArray = Data[]

  let data: DataArray = $state<DataArray>([])
  let options: EChartsOption = $state({})
  let initOptions: EChartsInitOpts = $state({})
  let optionalOptions: SetOptionOpts = $state({})
  let interval: number = 1000
  let numOfData: number = 10

  /* event handlers */
  const onclick = () => {
    console.log('clicked')
  }
  const onkeydown = () => {
    console.log('onkeydown')
  }

  /* data generator */
  const initializeData = (numberOfDataPoints: number, interval: number = 1000): DataArray => {
    return Array.from({ length: numberOfDataPoints }, (_, i) => ({
      timestamp: new Date(Date.now() - (numberOfDataPoints - i) * interval),
      value: Math.random() * 100,
    }))
  }

  /* updater */
  const updateData = () => {
    let newData: Data = {
      timestamp: new Date(),
      value: Math.random() * 100,
    }
    data = [...data.slice(1), newData]
    updateOptions(data)
  }

  // functions for options
  const initializeOptions = (data: DataArray): EChartsInitOpts => {
    return {
      devicePixelRatio: window.devicePixelRatio,
      renderer: 'svg',
      ssr: false,
      useDirtyRect: false,
      useCoarsePointer: true,
      pointerSize: 10,
      width: 'auto',
      height: 'auto',
      locale: 'EN',
    }
  }

  // ref: https://echarts.apache.org/en/api.html#echartsInstance.setOption
  const initializeOptionalOptions = (): SetOptionOpts => {
    return {
      notMerge: true,
      replaceMerge: 'series',
      lazyUpdate: false,
      silent: false,
    }
  }

  // options should be updated with data array
  const updateOptions = (data: DataArray): void => {
    options = {
      title: {
        text: 'ECharts bar chart example',
      },
      xAxis: {
        type: 'category',
        axisLabel: {
          formatter: (value) => {
            return value
          },
        },
        data: data.map(({ timestamp }) =>
          timestamp.toLocaleTimeString('en-US', {
            hour12: false,
            hour: '2-digit',
            minute: '2-digit',
            second: '2-digit',
          }),
        ),
      },
      yAxis: {
        type: 'value',
      },
      series: [
        {
          type: 'bar',
          data: data.map(({ value }) => value),
        },
      ],
    }
  }

  $effect(() => {
    // console.log('effect: options:', $state.snapshot(options), 'data', $state.snapshot(data))
  })

  onMount(() => {
    console.log('+page.svelte is mounted')
    data = initializeData(numOfData, interval)
    initOptions = initializeOptions(data)
    optionalOptions = initializeOptionalOptions()
    const id = setInterval(updateData, interval)
  })
</script>

<EChart {options} {initOptions} {optionalOptions} {data} {onclick} {onkeydown} />
