<script>
    import { onMount } from "svelte";

    export let clickIntervals;
    let canvas;
    let width = 600;
    let height = 250;
    let padding = 40;

    let lineColor = "#7CFC00";

    function drawGraph() {
        if (!canvas) return;

        const dpr = window.devicePixelRatio || 1;

        canvas.width = width * dpr;
        canvas.height = height * dpr;

        canvas.style.width = `${width}px`;
        canvas.style.height = `${height}px`;

        const ctx = canvas.getContext("2d");
        if (!ctx) return;

        // Scale context to account for high DPI
        ctx.setTransform(dpr, 0, 0, dpr, 0, 0);
        ctx.clearRect(0, 0, width, height);

        const maxVal = Math.max(...clickIntervals);
        const stepX = (width - 2 * padding) / (clickIntervals.length - 1);
        const graphHeight = height - 2 * padding;

        // Draw Y-axis labels and horizontal lines
        ctx.fillStyle = lineColor;
        ctx.font = "12px sans-serif";
        const numYLabels = 5;
        for (let i = 0; i <= numYLabels; i++) {
            const val = (maxVal / numYLabels) * i;
            const y = height - padding - (val / maxVal) * graphHeight;

            ctx.fillText(val.toFixed(0), 5, y + 4);
            ctx.beginPath();
            ctx.moveTo(padding, y);
            ctx.lineTo(width - padding, y);
            ctx.strokeStyle = "#cccccc22";
            ctx.stroke();
        }

        // Draw X axis labels
        ctx.fillStyle = lineColor;
        ctx.font = "12px sans-serif";
        ctx.textAlign = "center";

        // Draw the graph line
        ctx.beginPath();
        ctx.strokeStyle = lineColor;
        ctx.lineWidth = 2;

        clickIntervals.forEach((val, i) => {
            const x = padding + i * stepX;
            const y = height - padding - (val / maxVal) * graphHeight;
            if (i === 0) {
                ctx.moveTo(x, y);
            } else {
                ctx.lineTo(x, y);
            }
        });
        ctx.stroke();

        // Draw dots at each data point
        ctx.fillStyle = lineColor;
        clickIntervals.forEach((val, i) => {
            const x = padding + i * stepX;
            const y = height - padding - (val / maxVal) * graphHeight;
            ctx.beginPath();
            ctx.arc(x, y, 3, 0, Math.PI * 2);
            ctx.fill();
        });
    }

    onMount(() => {
        drawGraph();
    });

    $: clickIntervals, drawGraph();
</script>

<canvas bind:this={canvas}></canvas>
