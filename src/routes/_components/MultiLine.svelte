<script>
	import { LayerCake, Svg } from 'layercake';
	import { scaleOrdinal } from 'd3-scale';
	import { timeParse } from 'd3-time-format';

	import MultiLine from '../../_components/MultiLine.svelte';

	// This example loads csv data as json using @rollup/plugin-dsv
	import data from '../../_data/fruit.csv';

	/* --------------------------------------------
	 * Set what is our x key to separate it from the other series
	 */
	const xKey = 'month';
	const yKey = 'value';
	const zKey = 'fruit';

	const seriesNames = Object.keys(data[0]).filter(d => d !== xKey);
	const seriesColors = ['#ffe4b8', '#ffb3c0', '#ff7ac7', '#ff00cc'];

	const parseDate = timeParse('%Y-%m-%d');

	const dataLong = seriesNames.map(key => {
		return {
			[zKey]: key,
			values: data.map(d => {
				d[xKey] = typeof d[xKey] === 'string' ? parseDate(d[xKey]) : d[xKey]; // Conditional required for sapper
				return {
					[yKey]: +d[key],
					[xKey]: d[xKey]
				};
			})
		};
	});

	// Make a flat array of the `values` of our nested series
	// we can pluck the `value` field from each item in the array to measure extents
	const flatten = data => data.reduce((memo, group) => {
		return memo.concat(group.values);
	}, []);
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
		z={zKey}
		yDomain={[0, null]}
		zScale={scaleOrdinal()}
		zDomain={seriesNames}
		zRange={seriesColors}
		flatData={flatten(dataLong)}
		data={dataLong}
	>
		<Svg>
			<MultiLine/>
		</Svg>
	</LayerCake>
</div>
