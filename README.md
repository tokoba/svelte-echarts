# svelte-echarts

A simple ECharts component for Svelte

## 💿 Installation

```bash
npm i -D svelte-echarts echarts
```

## ⌨️ Usage

```html
<script>
  import { Chart } from '$lib'

  const options = {
    xAxis: {
      data: ['Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat', 'Sun'],
      type: 'category',
    },
    yAxis: {
      type: 'value',
    },
    series: [
      {
        data: [820, 932, 901, 934, 1290, 1330, 1320],
        type: 'bar',
      },
    ],
  }
</script>

<div class="app">
  <Chart {options} />
</div>

<style>
  .app {
    width: 100vw;
    height: 100vh;
  }
</style>
```
