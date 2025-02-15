<script lang="ts">
  import { onMount } from 'svelte'
  import { Chart, type ECMouseEvent } from 'svelte-echarts'
  import { use } from 'echarts/core'
  import EChart from './EChart.svelte'
  import type { EChartsOption, SetOptionOpts } from 'echarts'
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

  let data: Data[] = $state<Data[]>([])
  let options: EChartsOption = $state({})
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
  const initializeData = (numberOfDataPoints: number, interval: number = 1000): Data[] => {
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
    // console.log('updated data:', $state.snapshot(data))
    updateOptions(data)
    options = options
    // console.log('updated options:', $state.snapshot(options))
  }

  // オプション更新関数の定義
  const updateOptions = (data: Data[]): void => {
    /**
     * ECharts用のオプションオブジェクトを生成する関数
     * @param data - チャートに表示するデータ配列
     * @returns ECharts用のオプションオブジェクト
     */
    options = {
      title: {
        text: 'ECharts example',
      },
      xAxis: {
        type: 'category',
        axisLabel: {
          formatter: (value) =>
            new Date(value).toLocaleTimeString('en-US', {
              hour12: false,
              hour: '2-digit',
              minute: '2-digit',
              second: '2-digit',
            }),
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
    console.log('option no title: ', options.title)
    if (options.series) {
      console.log('option no series: ', options.series)
    }
  }

  onMount(() => {
    console.log('+page.svelte is mounted')
    data = initializeData(numOfData, interval)
    console.log('initialized data:', $state.snapshot(data))
    const id = setInterval(updateData, interval)
  })
</script>

<EChart {options} {data} {onclick} {onkeydown} />
<p>{options.title}</p>
<p>{data.map((d) => d.value)}</p>
<p>{data.map((d) => d.timestamp)}</p>
