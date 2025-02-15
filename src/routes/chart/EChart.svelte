<script lang="ts">
  let { options, data, onclick, onkeydown } = $props()
  console.log('options:', options, 'data:', data)
</script>

<div
  class="chart-area"
  {onclick}
  {onkeydown}
  aria-label="interactive chart"
  role="button"
  tabindex="0"
>
  <h2 class="chart-title">Here is the chart</h2>
  <p>{data.map((d) => d.value)}</p>
  <p>{data.map((d) => d.timestamp)}</p>
</div>

<style>
  .chart-area {
    background-color: #dddddd;
    padding: 30px;
    border-radius: 10px;
  }
</style>
