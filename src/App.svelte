<script>
  import LinePlot from "./LinePlot.svelte";

  let tstr = "30 40 50 60 65 70 75 80 85";
  let u1str = "0.1127 0.1166 0.1209 0.1250 0.1270 0.1290 0.1308 0.1329 0.1348";
  let u2str = "537.6 500.3 477.1 452.3 441.9 428.3 415.1 402.2 390.9";
  let u3str = "317.9 455.9 570.1 678.1 722.4 779.9 835.3 890.4 938.4";

  $: t = tstr
    .trim()
    .split(/\s+/)
    .map((s) => parseFloat(s) || 0);
  $: u1 = u1str
    .trim()
    .split(/\s+/)
    .map((s) => parseFloat(s) || 0)
    .concat(Array(t.length).fill(0))
    .slice(0, t.length);
  $: u2 = u2str
    .trim()
    .split(/\s+/)
    .map((s) => parseFloat(s) || 0)
    .concat(Array(t.length).fill(0))
    .slice(0, t.length);
  $: u3 = u3str
    .trim()
    .split(/\s+/)
    .map((s) => parseFloat(s) || 0)
    .concat(Array(t.length).fill(0))
    .slice(0, t.length);

  let plot;
</script>

<div class="container mx-auto flex min-h-screen flex-col gap-4 p-4">
  <div>
    <h1 class="text-4xl">物理学实验 3-5</h1>
  </div>
  <div>
    <p class="w-fit rounded bg-gray-100 px-3 py-2 text-sky-950">依次填入 Pt100 (V)、PN (mV)、LM35 (mV)，例如 Pt100 填入 0.1127 0.1166 0.1209...</p>
  </div>
  <div>
    <input bind:value={u1str} placeholder="Pt100 (V)" class="w-full rounded border border-gray-300 p-3 hover:shadow focus:outline-gray-600" type="text" />
  </div>
  <div>
    <input bind:value={u2str} placeholder="PN (mV)" class="w-full rounded border border-gray-300 p-3 hover:shadow focus:outline-gray-600" type="text" />
  </div>
  <div>
    <input bind:value={u3str} placeholder="LM35 (mV)" class="w-full rounded border border-gray-300 p-3 hover:shadow focus:outline-gray-600" type="text" />
  </div>
  <div>
    <p class="w-fit rounded bg-gray-100 px-3 py-2 text-sky-950">写完网站才发现svg不好打印，白写了一天，实在要打印的话试试 Inkscape 吧</p>
  </div>
  <div bind:this={plot} class="hover:shadow rounded-xl border w-fit overflow-hidden">
    <button
      on:click={() => {
        const blobstr = new XMLSerializer().serializeToString(plot.getElementsByTagName("svg")[0]);
        const blob = new Blob([blobstr], { type: "image/svg+xml" });
        const link = document.createElement("a");
        link.href = URL.createObjectURL(blob);
        link.download = "result.svg";
        link.click();
      }}
      class="disabled:bg-gray-100 rounded-br-xl border-r border-b border-gray-300 px-4 py-1.5 hover:bg-gray-50 hover:shadow hover:active:bg-gray-100"
      type="button">下载 SVG</button
    >
    <LinePlot {t} {u1} {u2} {u3} />
  </div>
</div>
