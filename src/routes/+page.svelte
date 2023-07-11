<script lang="ts">
	// let GeoPattern: any = require('geopattern');
	import { fade } from 'svelte/transition';
	import { quintOut } from 'svelte/easing';
	import * as GeoPattern from 'geopattern';
	let canvas = document.createElement('canvas');
	let value = 'Start typing...';
	document.body.style.backgroundColor = GeoPattern.generate(value).color;
	$: globalPattern = GeoPattern.generate(value);
	function textChangeHandler() {
		let temporary = GeoPattern.generate(value);
		document.body.style.backgroundColor = temporary.color;
		globalPattern = temporary;
	}
    let saveButtonPNG: HTMLAnchorElement;
    let saveButtonSVG: HTMLAnchorElement;
	function preparePNGFile() {
		if (!canvas) {
			canvas = document.createElement('canvas');
		}

		let ctx = canvas.getContext('2d');
		let img = new Image();
		let pattern = GeoPattern.generate(value);

		img.onload = function () {
			canvas.width = window.screen.width;
			canvas.height = window.screen.height;
			if(ctx !== null) ctx.drawImage(img, 0, 0);
			saveButtonPNG.download = 'pattern.png';
			try {
				saveButtonPNG.href = canvas.toDataURL('image/png');
			} catch (err) {
				// The above is a security error in IE, so hide the save button
				saveButtonPNG.style.display = 'none';
			}
		};

		img.src = pattern.toDataUri();
	}
</script>

<main>
	<textarea bind:value autocomplete="off" spellcheck="false" wrap="soft" on:input={textChangeHandler} />
	{#key globalPattern}
		<div
			class="pattern-overlay"
			style="background-image: {globalPattern.toDataUrl()};"
			transition:fade={{ delay: 0, duration: 1000, easing: quintOut }}
		/>
        <a download="pattern.svg" class="save-button" href={globalPattern.toDataUri()}>Save</a>
	{/key}
</main>

<style lang="postcss">
	:global(body) {
		overflow: hidden;
	}
	.pattern-overlay {
		z-index: -1;
		position: absolute;
		top: 0;
		left: 0;
		width: 100%;
		height: 100%;
		margin: 0;
		padding: 0;
		opacity: 1;
		background-repeat: repeat;
	}

	textarea {
		position: absolute;
		left: 50%;
		top: 50%;
		width: 100%;
		transform: translate(-50%, -50%);
		text-align: center;
		color: #f7f7f7;
		background: rgba(0, 0, 0, 0);
		border: 0;
		outline: 0;
		text-shadow: 0 0 5px rgba(255, 255, 255, 0.75);
		font-size: 30pt;
		overflow-wrap: break-word;
        resize: none;
	}
	textarea::placeholder {
		color: #f7f7f7;
	}

	.save-button {
		/* padding: 0px; */
		margin: 0px;
		position: absolute;
		bottom: 0;
        left: 0;
		width: 100%;
		text-align: center;
		text-decoration: none;
		font-size: 32px;
		border: none;
		background-color: rgba(255, 255, 255, 0.2);
		transition: 0.1s;
		cursor: pointer;
		color: white;
		/* box-shadow: 0 0 1px rgba(255, 255, 255, 0.75); */
		-webkit-backdrop-filter: blur(4px);
		-moz-backdrop-filter: blur(4px);
		-o-backdrop-filter: blur(4px);
		-ms-backdrop-filter: blur(4px);
		backdrop-filter: blur(4px);
	}

	.save-button:hover {
		background: rgba(255, 255, 255);
		color: black;
		transition: 0.2s;
		box-shadow: 0 0 20px rgba(255, 255, 255, 0.75);
	}
</style>
