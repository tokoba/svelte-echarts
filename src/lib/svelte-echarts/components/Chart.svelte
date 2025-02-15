<script lang="ts">
  import type { EChartsType as BaseEchartsType, EChartsOption, SetOptionOpts } from 'echarts'
  import type { EChartsType as CoreEchartsType } from 'echarts/core'
  import type { EChartsInitOpts } from 'echarts'
  import { onMount } from 'svelte'
  import { EVENT_NAMES, type EventHandlers } from '$lib/svelte-echarts/constants/events'
  import { init } from 'echarts/core'

  let {
    options,
    notMerge,
    lazyUpdate,
    silent,
    replaceMerge,
    transition,
    onclick = $bindable(),
  } = $props()
  let theme: string | object | null = 'light'
  let initOptions: EChartsInitOpts = {}
  let propOption = $state(options)
  console.log(propOption)
  console.log('initOptions', options)

  let chart: (BaseEchartsType | CoreEchartsType) | undefined = undefined

  let element: HTMLDivElement

  const initChart = (options: EChartsOption) => {
    console.log('initChart initOptions', options, initOptions)
    if (chart) chart.dispose()

    chart = init(element, theme, initOptions)
    console.log('chart:', chart, element)

    EVENT_NAMES.forEach((eventName) => {
      // @ts-expect-error
      chart!.on(eventName, (event) => onclick({ name: eventName, value: event }))
    })
  }

  onMount(() => {
    console.log('initOptions2', options)
    const resizeObserver = new ResizeObserver(() => {
      if (!chart) initChart(options)
      else chart.resize()
    })
    resizeObserver.observe(element)

    return () => {
      resizeObserver.disconnect()
      chart?.dispose()
    }
  })

  $effect(() => {
    if (chart) chart.setOption(options, { notMerge, lazyUpdate, silent, replaceMerge, transition })
  })
</script>

<div bind:this={element} style="width: 100%; height: 100%"></div>
