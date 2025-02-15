<script lang="ts">
  import { onMount } from 'svelte'
  import { Chart, type ECMouseEvent } from 'svelte-echarts'

  import { init, use } from 'echarts/core'
  import type { EChartsOption } from 'echarts'
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

  let data: { timestamp: Date; value: number; timestamp_string: string }[] = Array.from(
    { length: numRecords },
    (_, i) => {
      let timestamp_date = new Date(Date.now() - (numRecords - i) * interval)
      let timestamp_str = timestamp_date.toLocaleDateString('en-US', {
        hour12: false,
        hour: '2-digit',
        minute: '2-digit',
        second: '2-digit',
      })
      console.log('timestamp_date', timestamp_date, 'timestamp_str', timestamp_str)
      return {
        timestamp: timestamp_date,
        value: Math.random() * 100,
        timestamp_string: timestamp_str,
      }
    },
  )
  console.log('initial data:', data)

  let options = $state<EChartsOption>({
    title: {
      text: 'ECharts example',
    },
    xAxis: {
      type: 'category',
      axisLabel: {
        formatter: (value) => {
          new Date(value).toLocaleTimeString('en-US', {
            hour12: false,
            hour: '2-digit',
            minute: '2-digit',
            second: '2-digit',
          })
        },
      },
      data: data.map(({ timestamp_string }) => timestamp_string),
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
  })
  console.log('options:', options)

  const updateData = () => {
    data.shift()
    data.push({
      timestamp: new Date(),
      value: Math.random() * 100,
      timestamp_string: '',
    })
    data = [...data]
  }

  const handleClick = ({ detail }: CustomEvent<ECMouseEvent>) => {
    alert(`${detail.name} ${detail.value}`)
  }

  onMount(() => {
    const id = setInterval(updateData, interval)
    return () => clearInterval(id)
  })
</script>

<svelte:head>
  <title>Examples - svelte-echarts</title>
</svelte:head>

<Chart {init} {options} onclick={handleClick} />
