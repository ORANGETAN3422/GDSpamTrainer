<script>
    import { onMount, onDestroy } from "svelte";

    export let clickTimes = [];
    export let releaseTimes = [];
    export let testActive = false;

    let pathData = "";
    let points = [];

    let speed = 0.6; // pixels per ms

    let animationFrame;
    let startTime;
    let goingUp = false;
    let lastFrameTime;

    let width = 600;
    let height = 200;
    let lineColor = "#7CFC00";

    let scrollOffset = 0;

    let waveX = 0;
    let waveY = 0;
    let waveAngle = 0;
    let smoothedWaveAngle = 0;

    let isPanning = false;
    let panStartX = 0;
    let initialScrollOffset = 0;

    function lerpAngle(a, b, t) {
        let diff = b - a;
        while (diff < -180) diff += 360;
        while (diff > 180) diff -= 360;
        return a + diff * t;
    }

    function updatePath() {
        const now = performance.now();

        if (!startTime) {
            startTime = now;
            lastFrameTime = now;
            if (points.length === 0) {
                points = [{ x: 0, y: height / 2 }];
            }
        }

        let currentX = points[points.length - 1].x;
        let currentY = points[points.length - 1].y;

        // Determine current state
        let events = [];
        clickTimes.forEach((t) => events.push({ time: t, type: "down" }));
        releaseTimes.forEach((t) => events.push({ time: t, type: "up" }));
        events.sort((a, b) => a.time - b.time);

        let lastType = "up";
        for (let i = 0; i < events.length; i++) {
            if (events[i].time <= now) {
                lastType = events[i].type;
            } else {
                break;
            }
        }
        goingUp = lastType === "down";

        const deltaTime = now - lastFrameTime;
        lastFrameTime = now;

        currentX += deltaTime * speed;
        currentY += deltaTime * (goingUp ? -speed : speed);

        if (currentY < 0 + 10) {
            currentY = 0 + 10;
            goingUp = false;
        }
        if (currentY > height - 10) {
            currentY = height - 10;
            goingUp = true;
        }

        points.push({ x: currentX, y: currentY });

        // Compute arrow position & angle
        if (points.length >= 2) {
            let prev = points[points.length - 2];
            let dx = currentX - prev.x;
            let dy = currentY - prev.y;
            waveAngle = Math.atan2(dy, dx) * (180 / Math.PI);
            const smoothingFactor = 0.5;
            smoothedWaveAngle = lerpAngle(
                smoothedWaveAngle,
                waveAngle,
                smoothingFactor,
            );
        } else {
            waveAngle = 0;
        }

        waveX = currentX;
        waveY = currentY;

        // Generate Path
        let d = `M${points[0].x},${points[0].y}`;
        for (let i = 1; i < points.length; i++) {
            d += ` L${points[i].x},${points[i].y}`;
        }
        pathData = d;

        if (testActive) {
            scrollOffset = currentX - width / 2;
            if (scrollOffset < 0) scrollOffset = 0;
        }

        if (testActive) {
            animationFrame = requestAnimationFrame(updatePath);
        }
    }

    function startAnimation() {
        if (!animationFrame) {
            animationFrame = requestAnimationFrame(updatePath);
        }
    }

    function stopAnimation() {
        if (animationFrame) {
            cancelAnimationFrame(animationFrame);
            animationFrame = undefined;
        }
    }

    onMount(() => {
        if (testActive) {
            startAnimation();
        }
    });

    onDestroy(() => {
        stopAnimation();
    });

    $: if (testActive) {
        stopAnimation();
        startTime = undefined;
        lastFrameTime = undefined;
        scrollOffset = 0;
        points = [];
        pathData = "";
        waveX = 0;
        waveY = 0;
        smoothedWaveAngle = 0;
        startAnimation();
    } else {
        stopAnimation();
    }

    //Handle panning when testActive is false
    function handleMouseDown(e) {
        if (!testActive) {
            isPanning = true;
            panStartX = e.clientX;
            initialScrollOffset = scrollOffset;
        }
    }

    function handleMouseMove(e) {
        if (isPanning && !testActive) {
            const deltaX = e.clientX - panStartX;
            scrollOffset = initialScrollOffset - deltaX;
            if (scrollOffset < 0) scrollOffset = 0;
        }
    }

    function handleMouseUp() {
        isPanning = false;
    }
</script>

<!-- svelte-ignore a11y_no_static_element_interactions -->
<svg
    {width}
    {height}
    style="border: solid 1px #333; cursor: {testActive ? 'default' : 'grab'};"
    on:mousedown={handleMouseDown}
    on:mousemove={handleMouseMove}
    on:mouseup={handleMouseUp}
    on:mouseleave={handleMouseUp}
>
    <g transform={`translate(${-scrollOffset},0)`}>
        <path d={pathData} stroke={lineColor} fill="none" stroke-width="7" />
        {#if points.length > 1}
            <g
                transform={`translate(${waveX},${waveY}) rotate(${smoothedWaveAngle})`}
            >
                <polygon points="10,0 -6,-8 -6,8" fill={lineColor} />
            </g>
        {/if}
    </g>
</svg>
