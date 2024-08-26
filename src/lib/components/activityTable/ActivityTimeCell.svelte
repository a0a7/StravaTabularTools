<script lang="ts">
	import * as Tooltip from '$lib/components/ui/tooltip/index.js';
	import '$lib/components/sidebar/svg-styles.css';

	export let value: number;
	let timeString: string;
	let fullTimeString: string;

	$: if (value) {
		if (value === 0) {
			timeString = '-';
			fullTimeString = '-';
		}
		const hours = Math.floor(value / 3600);
		const minutes = Math.floor((value % 3600) / 60);
		const seconds = value % 60;

		if (hours > 0) {
			timeString = `${hours}h ${minutes}m`;
		} else if (minutes > 0) {
			timeString = `${minutes}m`;
		} else {
			timeString = `${seconds}s`;
		}
		fullTimeString = `${hours}h ${minutes}m ${seconds}s`;
	}
</script>
<div class="flex">
	<Tooltip.Root openDelay={50}>
		<Tooltip.Trigger>
			<p class="inline-block">{timeString}</p>
		</Tooltip.Trigger>
		<Tooltip.Content class="bg-card text-foreground drop-shadow-md">
			{#if fullTimeString === '-'}
				<p class="inline-block">No time tracked</p>
			{:else}
				<p class="inline-block">{fullTimeString}</p>
			{/if}
		</Tooltip.Content>
	</Tooltip.Root>
</div>