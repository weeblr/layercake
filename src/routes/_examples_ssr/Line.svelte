<script>
	import { LayerCake, ScaledSvg, Html } from 'layercake';

	import Line from '../../_components/Line.svelte';
	import Area from '../../_components/Area.svelte';
	import AxisX from '../../_components/AxisX.html.svelte';
	import AxisY from '../../_components/AxisY.html.svelte';

	// This example loads csv data as json using @rollup/plugin-dsv
	import data from '../../_data/points.csv';

	const xKey = 'myX';
	const yKey = 'myY';

	data.forEach(d => {
		d[yKey] = +d[yKey];
	});
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
		ssr={true}
		percentRange={true}
		padding={{ right: 10, bottom: 20, left: 25 }}
		x={xKey}
		y={d => d[yKey]}
		yDomain={[0, null]}
		data={data}
	>
		<Html>
			<AxisX/>
			<AxisY
				ticks={4}
			/>
		</Html>
		<ScaledSvg>
			<Line/>
			<Area/>
		</ScaledSvg>
	</LayerCake>
</div>
