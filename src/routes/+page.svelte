<script lang="ts">
  import { onMount } from 'svelte'
  import { Chart, type ECMouseEvent } from 'svelte-echarts'
  import { use } from 'echarts/core'
  import type { EChartsOption, SetOptionOpts } from 'echarts'
  import { BarChart } from 'echarts/charts'
  import {
    DatasetComponent,
    GridComponent,
    TitleComponent,
    TooltipComponent,
    TransformComponent,
  } from 'echarts/components'
  import { CanvasRenderer } from 'echarts/renderers'

  use([
    BarChart,
    DatasetComponent,
    GridComponent,
    TooltipComponent,
    TransformComponent,
    CanvasRenderer,
    TitleComponent,
  ])

  let interval = 1000
  let numRecords = 10

  type Data = {
    timestamp: Date
    value: number
  }

  let data: Data[] = $state<Data[]>([])
  data = generateDateArray(numRecords, interval)

  function generateDateArray(numRecords: number, interval: number = 1000): Data[] {
    return Array.from({ length: numRecords }, (_, i) => {
      return {
        timestamp: new Date(Date.now() - (numRecords - i) * interval),
        value: Math.random() * 100,
      }
    })
  }

  // options をリアクティブに更新するための関数
  /**
   * data 変更時に ECharts のオプションを更新する関数。
   *
   * @param data - データオブジェクトの配列
   * @returns 更新された ECharts オプション
   */
  function updateOptions(data: Data[]): EChartsOption {
    return {
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
  }

  //   let options = $derived<EChartsOption>({
  //     title: {
  //       text: 'ECharts example',
  //     },
  //     xAxis: {
  //       type: 'category',
  //       axisLabel: {
  //         formatter: (value) =>
  //           new Date(value).toLocaleTimeString('en-US', {
  //             hour12: false,
  //             hour: '2-digit',
  //             minute: '2-digit',
  //             second: '2-digit',
  //           }),
  //       },
  //       data: data.map(({ timestamp }) =>
  //         timestamp.toLocaleTimeString('en-US', {
  //           hour12: false,
  //           hour: '2-digit',
  //           minute: '2-digit',
  //           second: '2-digit',
  //         }),
  //       ),
  //     },
  //     yAxis: {
  //       type: 'value',
  //     },
  //     series: [
  //       {
  //         type: 'bar',
  //         data: data.map(({ value }) => value),
  //       },
  //     ],
  //   })

  // options をリアクティブに更新するためのストア
  let options = $state()

  const updateData = () => {
    data.shift()
    data.push({
      timestamp: new Date(),
      value: Math.random() * 100,
    })
    data = [...data]
    // console.log('data updated', data)
    options = updateOptions(data)
    // console.log('options:', options)
  }

  const handleClick = (event: any) => {
    console.log('handleClick event:', event)
    if ('click' === event.name) {
      alert(`${event.name} ${event.value}`)
    }
  }

  onMount(() => {
    const id = setInterval(updateData, interval)
    return () => clearInterval(id)
  })

  let notMerge: SetOptionOpts['notMerge'] = true
  let lazyUpdate: SetOptionOpts['lazyUpdate'] = false
  let silent: SetOptionOpts['silent'] = false
  let replaceMerge: SetOptionOpts['replaceMerge'] = undefined
  let transition: SetOptionOpts['transition'] = undefined
</script>

<svelte:head>
  <title>Examples - svelte-echarts</title>
</svelte:head>

<Chart
  {options}
  {notMerge}
  {lazyUpdate}
  {silent}
  {replaceMerge}
  {transition}
  onclick={handleClick}
/>
