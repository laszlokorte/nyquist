<script>
	let fmin = 0
	let fmax = 10
	let fsample = 30
	
	let showDiracs = true
	
	$: if(fmax < fmin) {
		fmax = fmin
	}
	
	$: bandwidth = fmax - fmin + 1
	$: bandwidthCenter = Math.round((fmax + fmin) / 2)
	$: frequencies = Array(bandwidth).fill(null).map((_,i) => fmin+i)
	$: signal = (t) => frequencies.reduce((sum, f) => sum + Math.sin(f*t*4*Math.PI)/(Math.abs(f-bandwidthCenter)||1), 0) / 2
	
	$: samples = Array(1000).fill(null).map((_,i,all) => i/all.length - 0.5).map((t) => signal(t))
	
	$: timeDiracs = Array(fsample).fill(null).map((_,i) => i/fsample)
	$: freqDiracs = Array(Math.ceil(240/fsample)).fill(null).map((_,i, all) => fsample*(i - Math.floor(all.length/2)))
</script>

<style>
	article {
		margin: 0 auto;
		max-width: 60em;
	}
	
	h1, h2 {
		margin: 0;
	}
	
	dl {
		display: grid;
		gap: 1em;
		grid-template-columns: max-content max-content max-content;
		align-items: center;
	}
	
	dd {
		margin: 0;
	}
	
	input {
		margin: 0;
		padding: 0.3em;
	}
	
	figure {
		margin: 0;
		display: grid;
		grid-template-columns: [full-start] repeat(auto-fill, minmax(20em, 1fr)) [full-end];
		gap: 1em;
	}
	
	figcaption {
		grid-column: full;
	}
	
	svg {
		border: 1px solid gray;
	}
	
	label input[type=checkbox] {
		margin-right: 0.5em;
	}
	
	dt:empty + dd {
		grid-column: span 2;
	}
</style>
<article>

	
<h1>
	Nyquist–Shannon sampling theorem
</h1>

<dl>
	<dt><label for="fmin">Min Signal Frequency</label></dt>
	<dd><input id="fmin" type="range" min="0" max={50} bind:value={fmin} /></dd>
	<dd><output>{fmin}/s</output></dd>
	<dt><label for="fmax">Max Signal Frequency</label></dt>
	<dd><input id="fmax" type="range" min={0} max="50" bind:value={fmax} /></dd>
	<dd><output>{fmax}/s</output></dd>
	<dt><label for="fsample">Sample Frequency</label></dt>
	<dd><input id="fsample" type="range" min={1} max="60" bind:value={fsample} /></dd>
	<dd><output>{fsample}/s</output></dd>
	<dt></dt>
	<dd><label><input type="checkbox" bind:checked={showDiracs} />Show Dirac Impulses/Sha Train</label>
</dd>
</dl>

<figure>
	<svg viewBox="-110 -110 220 220">
		<g color="black" font-size="10">
			<line x1="-100" x2="100" y1="0" y2="0" stroke="currentColor" vector-effect="non-scaling-stroke" />
			<line y1="-100" y2="100" x1="0" x2="0" stroke="currentColor" vector-effect="non-scaling-stroke" />
			<polygon points="102 0 95 -3 95 3" fill="currentColor" />
			<polygon points="0 -102 -3 -95 3 -95" fill="currentColor" />
			<text x="95" y="-5" text-anchor="end">t</text>
			<text y="-95" x="5" text-anchor="start">s(t)</text>
		</g>
		<g>
			<polyline points={samples.flatMap((s, i, all) => [(i/all.length-0.5)*200, -s*20])} stroke="red" fill="none" vector-effect="non-scaling-stroke" />
		</g>
			
		{#if showDiracs}
		<g>
			{#each timeDiracs as di}
				<line x1={di*100} x2={di*100} y1="0" y2="-30" stroke="black" vector-effect="non-scaling-stroke" />
				<line x1={-di*100} x2={-di*100} y1="0" y2="-30" stroke="black" vector-effect="non-scaling-stroke" />
				<circle cx={di*100} cy="-30" r="1" />
				<circle cx={-di*100} cy="-30" r="1" />
			{/each}
		</g>
		{/if}
	</svg>
	<svg viewBox="-110 -110 220 220">
		<g color="black" font-size="10">
			<line x1="-100" x2="100" y1="0" y2="0" stroke="currentColor" vector-effect="non-scaling-stroke" />
			<line y1="-100" y2="100" x1="0" x2="0" stroke="currentColor" vector-effect="non-scaling-stroke" />
			<polygon points="102 0 95 -3 95 3" fill="currentColor" />
			<polygon points="0 -102 -3 -95 3 -95" fill="currentColor" />
			<text x="95" y="-5" text-anchor="end">f</text>
			<text y="-95" x="5" text-anchor="start">S(f)</text>
		</g>
		<g>
			
		{#each freqDiracs as di, i}
			<path d="M{di+fmin} 0 C {di+(fmin+fmax)/2} -20 {di+fmin} -40 {di+(fmin+fmax)/2} -40 C {di+fmax} -40 {di+(fmin+fmax)/2} -20 {di+fmax} 0" fill={di==0?'blue' : 'none'} fill-opacity="0.5" stroke="blue" vector-effect="non-scaling-stroke" stroke-dasharray={di==0?'none' : '3 3'} />
			<path d="M{di+-fmin} 0 C {di+-(fmin+fmax)/2} -20 {di+-fmin} -40 {di+-(fmin+fmax)/2} -40 C {di+-fmax} -40 {di+-(fmin+fmax)/2} -20 {di+-fmax} 0" fill={di==0?'blue' : 'none'} fill-opacity="0.5" stroke="blue" vector-effect="non-scaling-stroke" stroke-dasharray={di==0?'none' : '3 3'} />
		{/each}
		</g>
		{#if showDiracs}
		<g>
			{#each freqDiracs as di}
				<line x1={di} x2={di} y1="0" y2="-30" stroke="black" vector-effect="non-scaling-stroke" />
				<line x1={-di} x2={-di} y1="0" y2="-30" stroke="black" vector-effect="non-scaling-stroke" />
				<circle cx={di} cy="-30" r="1" />
				<circle cx={-di} cy="-30" r="1" />
			{/each}
		</g>
		{/if}
	</svg>
	
	<figcaption>
		<h2>
			Explanation
		</h2>
		<p>
			On the left side you can see the continous signal in the time domain (t) in red. The timesteps at which is signal is samples are represented via vertical lines with a black dots at the top. They represent the Sha-Function which consists of the sum of many dirac impulses. Technically sampling the signal corresponds to a multiplication of the signal with the Sha-function.
		</p>
		<p>
			On the right side you see the spectral domain with the signals spectrum represented via blue spikes. Only the spikes that are filled in blue represent the original frequencies of the continous signal. The other spikes are aliases of the true spectrum. They are caused by the multiplication of the signal with the Sha-function in the time domain(the sampling). A multiplication in the time domain corresponds to a convolution in the spectral domain. The spectrum of the Sha-function is also the Sha-function but with the inverse distance between spikes. Convolving the original spectrum with the Sha-function reproduces the original signal around each spike of the Sha-function
		</p>
		<p>
			Play around the the min and max frequency of the signal to see how the spectrum changes. Play around with the sample frequency (the distance between the Sha-spikes) to see how increasing the distance in the time domain reduces the distance in the spectral domain. The original signal time domain can only be reconstructed from the spectral domain if the aliases do not overlap.
		</p>
		<p>
			In general the sample frequency must be at least twice as high as the highest frequency of the signal. This is known as the <a href="https://en.wikipedia.org/wiki/Nyquist%E2%80%93Shannon_sampling_theorem">NyquistShannon sampling theorem</a>: f<sub>sample</sub> &gt; f<sub>max</sub>
		</p>
	</figcaption>
</figure>
	
</article>