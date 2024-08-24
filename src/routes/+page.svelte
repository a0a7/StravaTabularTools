<script lang="ts">
	import { ScrollArea } from '$lib/components/ui/scroll-area/index.js';
	import '$lib/components/sidebar/resizable-handle.css';
	import ConnectPanel from '$lib/components/sidebar/ConnectPanel.svelte';
	import SetupPanel from '$lib/components/sidebar/SetupPanel.svelte';
	import Footer from '$lib/components/sidebar/Footer.svelte';
	import { onMount } from 'svelte';

	import { page } from '$app/stores';
	console.log($page.data.session);

	let onMobile: boolean;

	$: deviceTypeKnown = typeof onMobile !== 'undefined';

	onMount(() => {
		// Mobile device detection
		onMobile = window.innerWidth <= 768;
	});
</script>


<svelte:head>
	<title>Analytical Tools for Strava</title>
	<meta
		name="description"
		content="The best way to visualize your Strava runs, rides, or other activities on a beautiful & modern map."
	/>
	<meta name="keywords" content="Strava, Tools, Analysis" />
	<meta name="author" content="Alexander Weimer" />
	<meta name="subject" content="Sports" />
	<meta name="copyright" content="Alexander Weimer" />
	<meta
		property="og:description"
		content="The best way to visualize your Strava runs, rides, or other activities on a beautiful & modern map."
	/>
	<meta property="og:site_name" content="strava.tools" />
	<meta property="og:type" content="website" />
	<meta property="og:url" content="https://strava.tools/" />
	<meta property="og:image" content="" />
	<meta property="fb:admins" content="268094773018996" />

	<meta name="apple-mobile-web-app-title" content="Strava Heatmap" />
	<meta name="apple-mobile-web-app-capable" content="yes" />
	<meta name="apple-touch-fullscreen" content="yes" />
	<meta name="apple-mobile-web-app-status-bar-style" content="black" />
	<meta name="format-detection" content="telephone=no" />

	<link rel="apple-touch-icon" sizes="180x180" href="/apple-touch-icon.png" type="image/png" />
	<link rel="manifest" href="/manifest.json" />
	<link rel="mask-icon" href="/safari-pinned-tab.svg" color="#5bbad5" />
	<meta name="msapplication-TileColor" content="#5bbad5" />
	<meta name="theme-color" content="#5bbad5" />

	<meta name="google-site-verification" content="2M89RJWGTKCA8xmIIjzY9ZVbahvhk9wkiWi78YJtpi0" />
</svelte:head>
<h1 style="position: absolute; visibility: hidden;">
	Strava Multi-Activity Heatmap for Rides, runs and sports
</h1>
		<div class="w-full h-screen md:h-full md:px-5 pt-1 md:pt-5 background overflow-hidden">
			<ScrollArea class="rounded-xl sw-full overflow-y-scroll h-[calc(100vh-90px)]">
				<div class="flex flex-col md:min-h-[calc(100vh-110px)]">
					{#if $page.data.session?.access_token === undefined || $page.data.session?.access_token === null}
						<ConnectPanel />
					{:else if new Date($page.data.session?.expires) < new Date()}
						<ConnectPanel sessionExpired />
					{:else if $page.data.session?.user}
						<SetupPanel />
					{:else}
						<p class="text-center p-3">
							Something has gone wrong. If reloading the page doesn't fix the issue, please report
							it on <a
								href="https://github.com/sudolev/StravaMultiMapper"
								target="_blank"
								rel="noopener noreferrer">GitHub</a
							>
							or
							<a href="https://discord.gg/5P3AYFrwQG" target="_blank" rel="noopener noreferrer"
								>Discord</a
							>.
						</p>
					{/if}
					{#if onMobile}
						<div class="mx-5">
							<Footer bind:onMobile />
						</div>
					{/if}
				</div>
			</ScrollArea>
			{#if !onMobile}
				<div class="mx-5">
					<Footer bind:onMobile />
				</div>
			{/if}
		</div>
