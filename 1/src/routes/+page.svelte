<script>
	import { scaleLinear, line } from 'd3';

	// temperature deviations in some region, per month, compared to 1961 - 1990 (in ºCelsius)
	const months = [
		{ name: 'Jan', tempDev: 4.3 },
		{ name: 'Feb', tempDev: 4.4 },
		{ name: 'Mar', tempDev: 1.5 },
		{ name: 'Apr', tempDev: 0.9 },
		{ name: 'May', tempDev: 1.1 },
		{ name: 'Jun', tempDev: 0.7 },
		{ name: 'Jul', tempDev: 1.0 },
		{ name: 'Aug', tempDev: 3.1 },
		{ name: 'Sep', tempDev: 0.6 },
		{ name: 'Okt', tempDev: 3.1 },
		{ name: 'Nov', tempDev: 2.4 },
		{ name: 'Dec', tempDev: 0.3 }
	];

	const width = 600;
	const height = 600;
	const innerWidth = 400;
	const innerHeight = 400;

	const radialScale = scaleLinear([0, 5], [0, innerHeight / 2]);
	const labelScale = scaleLinear([0, 5], [0, innerHeight / 2 + 20]); // small offset for month labels
	const ticks = [1, 2, 3, 4, 5];

	function angleToCoordinate(angle, value, scale) {
		const x = Math.cos(angle) * scale(value);
		const y = Math.sin(angle) * scale(value);
		return { x: innerWidth / 2 + x, y: innerHeight / 2 - y };
	}

	const tempCoordinates = months.map((d, i) => {
		const angle = Math.PI / 3 - (2 * Math.PI * i) / months.length;
		const x = angleToCoordinate(angle, d.tempDev, radialScale).x;
		const y = angleToCoordinate(angle, d.tempDev, radialScale).y;
		return [x, y];
	});
	const d = line()([tempCoordinates, [tempCoordinates[0]]].flat());
</script>

<svg {width} {height}>
	<g style="transform: translate(100px, 100px)">
		<g class="circles">
			{#each ticks as tick}
				<circle cx={innerWidth / 2} cy={innerHeight / 2} r={radialScale(tick)} />
				<text x={innerWidth / 2 + 5} y={innerHeight / 2 - radialScale(tick) - 2}>
					{tick}º
				</text>
			{/each}
		</g>
		{#each months as month, i}
			{@const angle = Math.PI / 3 - (2 * Math.PI * i) / months.length}
			<g class="lines">
				<line
					x1={innerWidth / 2}
					y1={innerHeight / 2}
					x2={angleToCoordinate(angle, 5, radialScale).x}
					y2={angleToCoordinate(angle, 5, radialScale).y}
				/>
			</g>
			<g class="labels">
				<text
					x={angleToCoordinate(angle, 5, labelScale).x}
					y={angleToCoordinate(angle, 5, labelScale).y}
				>
					{month.name}
				</text>
			</g>
		{/each}
		<g class="values">
			<path {d} />
		</g>
	</g>
</svg>

<style>
	circle {
		fill: none;
		stroke: grey;
	}
	.circles text {
		fill: grey;
		font-size: 0.8rem;
	}
	line {
		stroke: grey;
	}
	.labels text {
		text-anchor: middle;
		fill: grey;
	}
	path {
		fill: teal;
		fill-opacity: 0.5;
		stroke: grey;
	}
</style>
