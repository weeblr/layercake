<script>
	import { LayerCake, Svg, flatten, uniques } from 'layercake';
	import { stack } from 'd3-shape';
	import { scaleBand, scaleOrdinal } from 'd3-scale';

	import ColumnStacked from '../../_components/ColumnStacked.svelte';

	// This example loads csv data as json using @rollup/plugin-dsv
	import data from '../../_data/fruitOrdinal.csv';

	const xKey = 'year';
	const yKey = [0, 1];
	const zKey = 'key';

	const seriesNames = Object.keys(data[0]).filter(d => d !== xKey);
	const seriesColors = ['#00e047', '#7ceb68', '#b7f486', '#ecfda5'];

	data.forEach(d => {
		seriesNames.forEach(name => {
			d[name] = +d[name];
		});
	});

	const stackData = stack()
		.keys(seriesNames);

	const series = stackData(data);
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
 			x={d => d.data[xKey]}
 			y={yKey}
 			z={zKey}
 			xScale={scaleBand().paddingInner([0.02]).round(true)}
 			xDomain={uniques(data, xKey)}
			zScale={scaleOrdinal()}
			zDomain={seriesNames}
			zRange={seriesColors}
 			flatData={flatten(series)}
 			data={series}
	>
		<Svg>
			<ColumnStacked/>
		</Svg>
	</LayerCake>

</div>
