<script>
	import { LayerCake, Svg, flatten } from 'layercake';
	import { stack } from 'd3-shape';
	import { scaleBand, scaleOrdinal } from 'd3-scale';

	import BarStacked from '../../_components/BarStacked.svelte';

	// This example loads csv data as json using @rollup/plugin-dsv
	import data from '../../_data/fruitOrdinal.csv';

	const xKey = [0, 1];
	const yKey = 'year';
	const zKey = 'key';

	const seriesNames = Object.keys(data[0]).filter(d => d !== yKey);
	const seriesColors = ['#00bbff', '#8bcef6', '#c4e2ed', '#f7f6e3'];

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
		x={xKey}
		y={d => d.data[yKey]}
		z={zKey}
		yScale={scaleBand().paddingInner([0.05]).round(true)}
		yDomain={['2016', '2017', '2018', '2019']}
		zScale={scaleOrdinal()}
		zDomain={seriesNames}
		zRange={seriesColors}
		flatData={flatten(series)}
		data={series}
	>
		<Svg>
			<BarStacked/>
		</Svg>
	</LayerCake>
</div>
