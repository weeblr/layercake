<script>
	import { LayerCake, Svg } from 'layercake';

	import AxisX from '../../_components/AxisX.svelte';

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
		padding={{ top: 20, bottom: 20 }}
		x={xKey}
		y={yKey}
		data={data}
	>
		<Svg>
			<AxisX
				baseline={true}
			/>
		</Svg>
	</LayerCake>
</div>
