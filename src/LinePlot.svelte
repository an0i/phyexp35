<script>
  import * as d3 from "d3";

  export let t = [30, 40, 50, 60, 65, 70, 75, 80, 85];
  /** @type number[] */
  export let u1;
  /** @type number[] */
  export let u2;
  /** @type number[] */
  export let u3;
  export let width = 595;
  export let height = 842;
  export let gap = 60;

  $: sectionHeight = (height - gap * 4) / 3.0;
  $: x = d3.scaleLinear(d3.extent(t), [gap, width - gap]);
  $: y1 = d3.scaleLinear(d3.extent(u1), [sectionHeight * 1 + gap * 1, sectionHeight * 0 + gap * 1]);
  $: y2 = d3.scaleLinear(d3.extent(u2), [sectionHeight * 2 + gap * 2, sectionHeight * 1 + gap * 2]);
  $: y3 = d3.scaleLinear(d3.extent(u3), [sectionHeight * 3 + gap * 3, sectionHeight * 2 + gap * 3]);

  let gx1, gx2, gx3, gy1, gy2, gy3;
  $: d3.select(gx1).call(d3.axisBottom(x));
  $: d3.select(gx2).call(d3.axisBottom(x));
  $: d3.select(gx3).call(d3.axisBottom(x));
  $: d3.select(gy1).call(d3.axisLeft(y1));
  $: d3.select(gy3).call(d3.axisLeft(y3));
  $: d3.select(gy2).call(d3.axisLeft(y2));

  /**
   * @param {number[]} xArray
   * @param {number[]} yArray
   */
  function calculateLinearRegression(xArray, yArray) {
    if (xArray.length !== yArray.length) {
      throw new Error("xArray and yArray must have the same length");
    }

    const n = xArray.length;

    // 计算 x 和 y 的平均值
    const xMean = xArray.reduce((sum, x) => sum + x, 0) / n;
    const yMean = yArray.reduce((sum, y) => sum + y, 0) / n;

    // 计算斜率 k 和截距 b
    let numerator = 0;
    let denominator = 0;
    for (let i = 0; i < n; i++) {
      numerator += (xArray[i] - xMean) * (yArray[i] - yMean);
      denominator += (xArray[i] - xMean) ** 2;
    }
    const slope = numerator / denominator;
    const intercept = yMean - slope * xMean;

    // 计算相关系数 r
    let ssTotal = 0;
    let ssResidual = 0;
    for (let i = 0; i < n; i++) {
      const yPredicted = slope * xArray[i] + intercept;
      ssTotal += (yArray[i] - yMean) ** 2;
      ssResidual += (yArray[i] - yPredicted) ** 2;
    }
    const rSquared = 1 - ssResidual / ssTotal;
    const correlation = Math.sqrt(rSquared);

    return { k: slope, b: intercept, r: correlation, y1: 30 * slope + intercept, y2: 85 * slope + intercept };
  }
  $: l1 = calculateLinearRegression(t, u1);
  $: l2 = calculateLinearRegression(t, u2);
  $: l3 = calculateLinearRegression(t, u3);
</script>

<svg {width} {height} class="bg-white">
  <g bind:this={gx1} transform="translate(0,{sectionHeight * 1 + gap * 1})" />
  <g bind:this={gx2} transform="translate(0,{sectionHeight * 2 + gap * 2})" />
  <g bind:this={gx3} transform="translate(0,{sectionHeight * 3 + gap * 3})" />
  <g bind:this={gy1} transform="translate({gap},0)" />
  <g bind:this={gy2} transform="translate({gap},0)" />
  <g bind:this={gy3} transform="translate({gap},0)" />
  <g fill="white" stroke="currentColor" stroke-width="1.5">
    {#each u1 as d, i}
      <circle cx={x(t[i])} cy={y1(d)} r="2.5" />
    {/each}
  </g>
  <g fill="white" stroke="currentColor" stroke-width="1.5">
    {#each u2 as d, i}
      <circle cx={x(t[i])} cy={y2(d)} r="2.5" />
    {/each}
  </g>
  <g fill="white" stroke="currentColor" stroke-width="1.5">
    {#each u3 as d, i}
      <circle cx={x(t[i])} cy={y3(d)} r="2.5" />
    {/each}
  </g>
  <path
    fill="none"
    stroke="currentColor"
    stroke-width="1.5"
    d={d3.line(
      (d) => x(d[0]),
      (d) => y1(d[1])
    )([
      [30, l1.y1],
      [85, l1.y2],
    ])}
  />
  <path
    fill="none"
    stroke="currentColor"
    stroke-width="1.5"
    d={d3.line(
      (d) => x(d[0]),
      (d) => y2(d[1])
    )([
      [30, l2.y1],
      [85, l2.y2],
    ])}
  />
  <path
    fill="none"
    stroke="currentColor"
    stroke-width="1.5"
    d={d3.line(
      (d) => x(d[0]),
      (d) => y3(d[1])
    )([
      [30, l3.y1],
      [85, l3.y2],
    ])}
  />
</svg>
