<script>
  import data from "./data/data.js";
  import {scaleLinear} from "d3-scale";
  import {max} from "d3-array";
  import AxisX from "./components/AxisX.svelte";
  import AxisY from "./components/AxisY.svelte";
  import Tooltip from "./components/Tooltip.svelte";

  let width = 400;
  let height = 400;
  let hoveredData;

  const margin = {top: 20, right: 40, left: 0, bottom: 20};

  // when the width changes, xScale changes
  $: xScale = scaleLinear()
    .domain([0, 100])
    .range([0,width - margin.left - margin.right]);

  const yScale = scaleLinear()
    .domain([0, max(data , d => d.hours)])
    .range([height - margin.top - margin.bottom, 0]);

  // svg start 0, 0 top left, swap the numbers in range for yScale

</script>
  <h1>Study longer, score better!</h1>
  <!-- add container for responsiveness, the width will change when clientWidth of this div changes -->
  <div class="chart-container"
        bind:clientWidth={width}
        on:mouseleave={() => {hoveredData = null;}}>
    <svg {width} {height}>
      <!-- add the x-axis to the graph -->
      <AxisX {height} {xScale} {margin}/>
      <!-- add the y-axis to the graph -->
      <AxisY {width} {yScale} {margin}/>

      <!-- add circular data points to the graph -->
        <g transform="translate({margin.left} {margin.top})">
          {#each data.sort((a,b) => a.grade - b.grade) as student}
          <circle cx={xScale(student.grade)}
                  cy={yScale(student.hours)}
                  r={hoveredData && hoveredData == student ? '20' : '10'}
                  opacity={hoveredData ? hoveredData == student ? '1' : '0.3' : '1'}
                  fill="purple"
                  stroke="black"
                  on:mouseover={() => {hoveredData = student;}}
                  on:focus={() => {hoveredData = student;}}
                  tabindex="0"/>
          {/each}
        </g>
    </svg>
    {#if hoveredData}
      <Tooltip data={hoveredData} {xScale} {yScale}/>
    {/if}
  </div>

<style>
  circle {
    transition: r 300ms ease, opacity 300ms ease;
    cursor: pointer;
  }
  /* remove the square around the circle */
  circle:focus{
    outline: none;
  }
  h1 {
    font-size: 1.5rem;
    font-weight: 600;
    margin-bottom: 0.5rem;
  }
</style>