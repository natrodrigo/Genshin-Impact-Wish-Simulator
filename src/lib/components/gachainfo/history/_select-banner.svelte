<script>
	import { getContext } from 'svelte';
	import { fade } from 'svelte/transition';
	import { t } from 'svelte-i18n';
	import { assets, bannerList } from '$lib/store/stores';
	import Reset from './_reset.svelte';

	export let v2 = false;
	export let banner;
	let showSelectList = false;

	$: list = $bannerList.filter((item, i, arr) => i === arr.findIndex((v) => v.type === item.type));
	//  check if beginner banner already gone, push it to hostory list
	$: if (list.findIndex(({ type }) => type === 'beginner') < 0) list.unshift({ type: 'beginner' });
	$: nowOpenIndex = list.findIndex(({ type }) => type === banner.toLocaleLowerCase());
	$: selected = nowOpenIndex < 0 ? 2 : nowOpenIndex;

	const selectBanner = getContext('selectBanner');
	const select = (bn) => {
		showSelectList = !showSelectList;
		selectBanner(bn);
	};
</script>

<div class="selectType" class:v2>
	<div
		style={v2 ? `background-image: url(${$assets['history-select-bg.webp']})` : ''}
		class="wish-type"
		class:v2
	>
		<span> {$t('history.selectWish')} </span>
		<div class="select-box">
			<div class="selected" on:click={() => (showSelectList = !showSelectList)}>
				<span>{@html $t(`wish.banner.${banner}`)}</span>

				{#if v2}
					<span class="arrow icon {showSelectList ? 'up' : 'down'}" />
				{:else}
					<i class="arrow gi-caret-{showSelectList ? 'up' : 'down'}" />
				{/if}
			</div>

			{#if showSelectList}
				<div class="select-list" transition:fade={{ duration: 200 }}>
					{#each list as { type }, i}
						<a
							class="item"
							href="/"
							class:active={selected === i}
							on:click|preventDefault={() => select(type)}
						>
							{@html $t(`wish.banner.${type}`)}
						</a>
					{/each}
				</div>
			{/if}
		</div>
	</div>

	{#if v2}
		<div class="reset">
			<Reset {banner} v2 />
		</div>
	{/if}
</div>

<style>
	.selectType {
		width: 100%;
		display: flex;
		align-items: center;
	}

	.reset {
		text-align: right;
	}

	.wish-type {
		display: flex;
		align-items: center;
		justify-content: center;
		width: 100%;
	}

	.wish-type span {
		padding: 5px;
	}

	.select-box {
		width: 270px;
		max-width: 100%;
		position: relative;
		margin: 0 15px;
	}

	.selected .arrow {
		position: absolute;
		top: 50%;
		right: 1rem;
		font-size: 2rem;
		transform: translateY(-50%);
	}

	.selected,
	.select-list {
		width: 100%;
		border: 0.15rem solid #b5b2ae;
		background-color: #dbd7d3;
		border-radius: 0.25rem;
	}
	.selected,
	.item {
		display: block;
		color: #757575;
		text-decoration: none;
		padding: 6px 15px;
		transition: all 0.2s;
	}
	.item.active,
	.item:hover {
		background-color: #efebe7;
	}

	.select-list {
		position: absolute;
		z-index: +1;
		top: 130%;
		left: 50%;
		transform: translateX(-50%);
	}

	@media screen and (max-width: 495px) {
		.wish-type:not(.v2) {
			flex-direction: column;
		}
	}

	:global(.mobile) .wish-type {
		font-size: 0.9em;
	}

	/* V2 */
	.wish-type.v2 {
		background-size: contain;
		background-repeat: no-repeat;
		aspect-ratio: 201/11;
		justify-content: flex-start;
		font-size: calc(0.016 * var(--content-width));
		margin-bottom: calc(0.005 * var(--content-width));
	}

	.wish-type.v2 > span {
		padding: 0 calc(0.02 * var(--content-width));
		width: calc(0.215 * var(--content-width));
		text-align: right;
		line-height: 90%;
	}

	.v2 .select-box {
		width: calc(0.525 * var(--content-width));
		margin: 0;
	}

	.v2 .selected {
		color: var(--text-color);
		background-color: transparent;
		border: transparent;
	}

	.v2 .select-list {
		background-color: #646975;
		border: 0;
		border-radius: 1rem;
		overflow: hidden;
	}
	.v2 .item {
		color: #fff;
		padding: calc(0.02 * var(--content-width)) calc(0.03 * var(--content-width));
	}

	.v2 .item.active,
	.v2 .item:hover {
		background-color: #717887;
	}

	.selectType.v2 .reset {
		width: calc(0.08 * var(--content-width));
	}

	.arrow.icon {
		width: calc(0.02 * var(--content-width));
		background-image: url('data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAACIAAAASCAMAAAAJ4/xdAAAAY1BMVEVHcExZUlJZUVFVTU1YUVFYUVFZUVFZUlJZUlJYTU1ZUFBZUVFYUVFYT09ZRUVXTk5YUVFZUlJZQ0NYUVFZUVFZUlJZUVFZUlJYTk5ZUVFZUlJYT09ZUVFYUlJOTk5OTk5ZUlKPb9bhAAAAIHRSTlMAz+kiZfbk8foSSKpVNAgqsokLxqTbcsAbhNc6j5YGDUB/3r0AAACpSURBVBgZfcEFEoMwAATAC0Rxrcv9/5UFmjBAobtYKytj0hJ/JIYDk+CQtJxYiQM1Z1fsKVIudAV+uJYrZ4eNuOfGLcbKPaKnFL0ox0Km6EX5PaKnMsxODG4xEPf07Anek8HZYeBaBg+MmopB98KkSBlUDeAEgytmNYPWIaVnJRakpddB8cskWEkMvww0J7rERq456SE5Ehf8uAiOJJAJq+sGO94PbUWGD0o8IC9WAY0jAAAAAElFTkSuQmCC');
		background-size: contain;
		background-repeat: no-repeat;
	}
	.v2 .arrow.icon.up {
		transform: translateY(-50%) rotate(0);
	}

	.v2 .arrow.icon.down {
		transform: translateY(-50%) rotate(180deg);
	}
</style>
