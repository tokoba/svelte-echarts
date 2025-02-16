<script lang="ts">
  import { onMount } from 'svelte'
  import { use } from 'echarts/core'
  import EChart from '$lib/svelte-echarts/components/EChart.svelte'
  import type { EChartsOption, EChartsInitOpts, SetOptionOpts } from 'echarts'
  import { CanvasRenderer } from 'echarts/renderers'
  import { BarChart } from 'echarts/charts'
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
  /* data initializer uses the following optional parameters */
  type DataInitializeOption = {
    numberOfDataPoints: number
    interval_msec: number
  }

  let data: DataArray = $state<DataArray>([])
  let options: EChartsOption = $state({})
  let initOptions: EChartsInitOpts = $state({})
  let optionalOptions: SetOptionOpts = $state({})
  // usually 'light' and 'dark' are used as theme name however you can define
  // customized theme by yourself.
  // use registerTheme() first and user your registered theme.
  // ref: https://echarts.apache.org/handbook/en/concepts/style/
  let theme: string | object | null = 'dark'
  let dataInitOption: DataInitializeOption = {
    numberOfDataPoints: 10,
    interval_msec: 1000,
  }
  // if you want to check the value which is transfered to child component, set debugMode: true
  let debugMode: boolean = $state(false)

  /* event handlers */
  const onclick = () => {
    console.log('clicked')
    // actual event handlers have not been implemented
  }
  const onkeydown = () => {
    console.log('onkeydown')
    // actual event handlers have not been implemented
  }

  /* data generator */
  const initializeData = (
    numberOfDataPoints: number = 10,
    interval_msec: number = 1000,
  ): DataArray => {
    return Array.from({ length: numberOfDataPoints }, (_, i) => ({
      timestamp: new Date(Date.now() - (numberOfDataPoints - i) * interval_msec),
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

  // function for initial options
  // ref: https://echarts.apache.org/en/api.html#echarts.init
  const initializeInitialOptions = (data: DataArray): EChartsInitOpts => {
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

  // function for optional options
  // ref: https://echarts.apache.org/en/api.html#echartsInstance.setOption
  const initializeOptionalOptions = (): SetOptionOpts => {
    return {
      notMerge: true,
      replaceMerge: 'series',
      lazyUpdate: false,
      silent: false,
    }
  }

  // function for "data-related" options - xAxis, yAxis, series data etc
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

  onMount(() => {
    // optional options are usually static parameters which does not need to be updated.
    // optional options are generated only at the startup
    optionalOptions = initializeOptionalOptions()

    // initial options does not have to be updated
    // data and initial options are generated at the startup
    data = initializeData(dataInitOption.numberOfDataPoints, dataInitOption.interval_msec)
    initOptions = initializeInitialOptions(data)

    // data and options should be updated ... usually these data are stored in the
    // backend server and front-end (this svelte program) should fetch the data using
    // GET method etc. Currently front-end generates time-series data itself.
    const id = setInterval(updateData, dataInitOption.interval_msec)
  })
</script>

<EChart
  {options}
  {initOptions}
  {optionalOptions}
  {theme}
  {data}
  {onclick}
  {onkeydown}
  {debugMode}
/>
