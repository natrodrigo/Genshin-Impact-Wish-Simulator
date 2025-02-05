<script>
	import { createEventDispatcher, onMount } from 'svelte';
	import { fade } from 'svelte/transition';
	import { t } from 'svelte-i18n';
	import OverlayScrollbars from 'overlayscrollbars';
	import { playSfx } from '$lib/helpers/audio/audio.svelte';
	import { assets, viewportHeight } from '$lib/store/stores';
	import ButtonModal from './ButtonModal.svelte';

	export let show = false;
	export let title = '';
	export let confirm = true;
	export let confirmText = '';
	export let button = 'all';
	export let sfx = true;
	export let disclaimer = false;

	let content;

	onMount(() =>
		OverlayScrollbars(content, { sizeAutoCapable: false, className: 'os-theme-light' })
	);

	const dispatch = createEventDispatcher();
	const confirmClick = () => {
		dispatch('confirm');
		if (sfx) playSfx();
	};
	const cancelClik = () => {
		dispatch('cancel');
		if (sfx) playSfx();
	};
</script>

{#if show}
	<div class="modal" transition:fade={{ duration: 80 }} style="height: {$viewportHeight}px;">
		<div class="modal-content">
			<span class="corner top-left" />
			<span class="corner top-right" />
			<span class="corner bottom-left" />
			<span class="corner bottom-right" />
			<div class="container" style="--bg-icon:url({$assets['modal-bg-icon.png']})">
				{#if title}
					<h1 class="pop-header">{title}</h1>
				{/if}
				<div class="pop-body" class:disclaimer class:large={!title && !confirm} bind:this={content}>
					<slot />
				</div>

				{#if confirm}
					<div class="pop-footer">
						{#if ['cancel', 'all'].indexOf(button) > -1}
							<ButtonModal type="cancel" on:click={cancelClik} />
						{/if}
						{#if ['confirm', 'all'].indexOf(button) > -1}
							<ButtonModal type="confirm" on:click={confirmClick}>
								{confirmText || $t('confirmButton')}
							</ButtonModal>
						{/if}
					</div>
				{/if}
			</div>
		</div>
	</div>
{/if}

<style>
	.modal {
		position: fixed;
		top: 0;
		left: 0;
		width: 100vw;
		background-color: rgba(0, 0, 0, 0.5);
		z-index: 997;
		display: flex;
		justify-content: center;
		align-items: center;
		backdrop-filter: blur(2px);
	}

	.modal-content {
		width: 35rem;
		max-width: 90%;
		background-color: #fbf6ee;
		padding: 8px;
		text-align: center;
		color: #383b40;
		position: relative;
		border-radius: 0.8rem;
	}

	:global(.mobile) .modal-content {
		max-width: 110vh;
	}

	.container {
		width: 100%;
		height: 100%;
		border: 2px solid #ddd5c8;
		background-image: var(--bg-icon);
		background-repeat: no-repeat;
		background-size: 50%;
		background-position: 50%;
	}
	.corner {
		background-image: url('data:image/webp;base64,UklGRkgFAABXRUJQVlA4WAoAAAAQAAAANgAAMQAAQUxQSAUDAAABCbRt2wYS0Kny/w+3P0T0fwL4Fh3ZUFn3RQqxyZuhoG0bpuZPuzsQImICIhX+jrAkyW7bzKp0//s6YRwAcOH8GTEBE0C5tm1FknPu+xCQWdBiZpZGclsGyAB0QONmZkqIiB/vDRpNiIgJIP6144LOBIQAaStmAqCN0E4AUphAwoCQXtEIPIiISeCiIWUUwkwECbAXfA0Pj8BSW4GwsJqTeU7uMjoa3Qtdmtt8nIH4ELgYhOx9U1hpxZw2KNQFUH23+mFBoKxI326+3UyLToG6iejNu6E8X7f3/vKHwywQsRchX96fP/74Yy5GQCVFkDI10f/wh55IHwDhYnjcvvP7u2dPao+ZXFFiTVoiVtWf/32Gvrdv/nw2/qLTFCsERiDhJu+QevH4duXe83Khb94AUskCw7CM6KmtwbiZl7ufnLJiHiJBKNy5i/gQDWjL9pu4++6wu1ffxRMEO0SsT5gHuql7P3386+P7bbPsvxodw6MRWtYVEOryej6c2/hOd9fL8vF7KfvRD4hCCMsoIKvjeH5Sx/fKbvlshHUvmlAQiJkaYNuuH8ahnPLSaYI5KDXjbNCNfS2lppc9DdRCQQcIgFrEVAsphfTCwzufEEh21t/gaCgwRfbCi7fyHtDqJAABRIvUkOV56/YCPHUpzPWC0BewVmDqEbx7e9gn97BYhGVUME+hECJcpMfD/+4W7B8PE6GsgnT0dvQzgy59fnU44EGrgQAQta4/Lq65LA+r0dZ4GLckwgMQALVsWMZ5fmZT6QDbPP/w3k0aVg3CQeWTPO8X2hNqlqVuyc82d06KUghnNjFNd8szSxF9l3Uy5/7Kb6ZIAKIm5eTH3f5xZlLe1FF9/e6mcracJaglyQ/Xdm32VopsSKUrwztD/37RcVDg7PJwHd1CRk5Zdfs8n6xr/+nzAQcleRzdBCAJcmbl0vjJnc8HTVQBwlcBBAjApnlu7//3/bCIjniLa5uu3v+m9i9m6iAKGIYHDzTgK4YUUsz0eWLQCyE0gz4NAAlfMRR7+jR/bwBWUDggHAIAAHAKAJ0BKjcAMgA+KQ6FQqGHfAYAoSygDgHbI3I4tdhHQL88A1kA2ozja3RezMIbWU3XuF8+/h9ILAYHndhrUbZy5B81xTHr3sWw+e1DsBqahnwk8nvegFg3CiwAAP7/KvhRIfulyVvAM+kd25h7/f9q48lj/8TP6ceUoTF8V1L0D7kxK/RYzWsE2gM84nd8zXc9QGLEFOT/sLorw3Yjn9KVTdhjeYf/JPghddPdW0LE5XY55VYd6No1Np9IqEu9k4ZHiHsClwKB9XL+sZKxaOfPkAx9tBWSX/EaPi0L0FT+Ia2LsLUt7Gv/94HKXgfgqAY3Dc3mrgA9914RrLuW9+/4muOcWZqBrbww2hfhuvV6OzfAOs9mDbeSCYCL8K3CPHvdT0wItSZ53JuxG9FLZCIfp0b8LJkq1OMW6TeGE5KKpvYCamNksDv1s+pAa2J18XKje5sj8hSQhZjIqh9uPJX7FZXFqLIxObNJPebhHE3gvZWar0ojALZ+1hmoJKVuJVetIsSw8Nqfb9drEU81Na8myzoyvPFjWzeCiZoWF+PHkNkeaySoSlxjDHlbQS8zky28ygG5RGkzIURZol88xyDcVVvHW05Bq5d79tZ9Nbk/ofOfzl0+Un6lPuZpn0zhr9YSAvne330OxW3f5DbKZyT+YPZstc2H5QaDYWBcyVMJ2TCXOSCq+L28GLxA8V82mMeS122MP/qzwAAAAA==');
		background-size: cover;
		width: 2.35rem;
		aspect-ratio: 1/1;
		font-size: 1.5rem;
		display: inline-block;
		position: absolute;
	}

	.top-left {
		top: -0.9rem;
		left: -0.9rem;
		transform: rotate(-90deg);
	}
	.top-right {
		top: -0.9rem;
		right: -0.9rem;
	}
	.bottom-left {
		bottom: -0.9rem;
		left: -0.9rem;
		transform: rotate(-180deg);
	}
	.bottom-right {
		bottom: -0.9rem;
		right: -0.9rem;
		transform: rotate(90deg);
	}

	.pop-header {
		font-size: 130%;
		margin: 0.4rem 7%;
		padding-bottom: 0.4rem;
		border-bottom: 0.2rem solid #ddd5c8;
		position: relative;
		z-index: +1;
	}

	:global(.mobile) .pop-header {
		font-size: 1rem;
	}

	.pop-body {
		height: 15rem;
		max-height: 45vh;
		position: relative;
		z-index: +1;
		font-size: 1.1rem;
		display: flex;
		justify-content: center;
	}

	:global(.mobile) .pop-body {
		height: 12rem;
		font-size: 0.9rem;
	}

	.disclaimer {
		height: 18rem;
	}

	.pop-body.large {
		height: 25rem;
		max-height: 70vh;
	}

	.pop-footer {
		display: flex;
		padding: 0.7rem 0;
		justify-content: space-around;
		position: relative;
		z-index: +1;
	}
	.pop-footer::before {
		width: 80%;
		content: '';
		display: block;
		border-top: 0.2rem solid #ddd5c8;
		position: absolute;
		top: 0;
		left: 50%;
		transform: translateX(-50%);
	}
</style>
