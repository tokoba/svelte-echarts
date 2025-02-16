# Svelte 5 + svelte-echarts

This project is for my learning ECharts library and Svelte 5 Runes Compatible.
(Originally svelte-echarts is Svelte 4 compatible)

I almost always use Svelte 5 in my recent projects, so I wanted to try ECharts in Svelte 5 environment, this is my motivation for this project.

# svelte-echarts (Original project)

A simple [Apache ECharts](https://echarts.apache.org/) component for [Svelte](https://svelte.dev/)! Check out this [demo](https://bherbruck.github.io/svelte-echarts/).

## ⌨️ Usage [demo](https://bherbruck.github.io/svelte-echarts/)

Thanks to @bherbruck, we can use ECharts in svelte components.

This is how to try this repository.

```bash
git clone https://github.com/tokoba/svelte-echarts.git
npm install
npm run dev
```

and open the development server with your browser.
All the svelte-echarts componets will be installed via package managers (npm, yarn, pnpm) as you can install the dependencies written in the package.json.
I tried to use as new packages as possible as of 2025 Feb.

You can see the following bar chart of which data is updated every 1 second.

![Screenshot](/static/echart-screenshot-dark-theme.png)

## disclaimer

I am a kind of newbie in front-end devlopment, I tried to read Svelte official docs and EChart official docs but there could be some bugs or typo in my project. If you find something, feel free to contact me. I cannot assure I can fix them all but I will try.

## Memo

1. I could not implement event handlers, resize. I wiil try later.
2. I rewrote almost all the code (+page.svelte, EChart.svelte). At first I tried to modify Chart.svelte(original one) but when I used Svelte 5 runes in Chart.svelte there were many errors and I could not solve them all.
3. The final goal is to implement backend server (using Rust + Axum, Python + Flash, Python + FastAPI, Go + Gin etc) and asynchronously fetch the data from the server and update the charts automatically.
   for that goal, I need to make a communication specification doc between front-end and backend-server, it would take several days.
