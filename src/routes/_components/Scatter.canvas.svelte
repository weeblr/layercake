<script>
	import { LayerCake, Svg, Canvas } from 'layercake';

	import ScatterCanvas from '../../_components/Scatter.canvas.svelte';

	// This example loads csv data as json using @rollup/plugin-dsv
	import data from '../../_data/points.csv';

	const xKey = 'myX';
	const yKey = 'myY';

	data.forEach(d => {
		d[yKey] = +d[yKey];
	});

	const r = 3;
	const padding = 10;
</script>

<style>
	/*
		The wrapper div needs to have an explicit width and height in CSS.
		It can also be a flexbox child or CSS grid element.
		The point being it needs dimensions since the <LayerCake> element will
		expand to fill it.
	*/
	.chart-container {
		width: 100%;
		height: 100%;
	}
</style>

<div class="chart-container">
	<LayerCake
		padding={{ top: 10 }}
		x={xKey}
		y={yKey}
		xPadding={[padding, padding]}
		yPadding={[padding, padding]}
		data={data}
	>
		<Canvas>
			<ScatterCanvas
				r={r}
				fill={'#0cf'}
			/>
		</Canvas>
	</LayerCake>
</div>
