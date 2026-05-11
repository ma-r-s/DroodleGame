<script>
	import { Button } from '$lib/components/ui/button/index.js';
	import imageMappings from '$lib/image_mappings.json';

	// Subsample the catalog so the scrolling background doesn't render hundreds of <img>
	// tags. Every Nth droodle is enough for the visual texture; the full set is overkill.
	const keys = Object.keys(imageMappings);
	const STRIDE = Math.max(1, Math.floor(keys.length / 60));
	const backgroundImages = keys
		.filter((_, i) => i % STRIDE === 0)
		.map((key) => ({ src: `/droodles/${key}`, alt: imageMappings[key].alt }));
</script>

<svelte:head>
	<title>Droodles · Why? Who? What?</title>
</svelte:head>

<div
	class="relative flex min-h-dvh items-center justify-center overflow-hidden bg-gray-50 px-4 py-6 font-serif"
>
	<!-- Subtle scrolling backdrop of droodles -->
	<div class="scrolling-background" aria-hidden="true">
		{#each backgroundImages as image}
			<img src={image.src} alt="" loading="lazy" />
		{/each}
		{#each backgroundImages as image}
			<img src={image.src} alt="" loading="lazy" />
		{/each}
	</div>

	<div class="relative z-10 w-full max-w-[800px] rounded-lg bg-white p-6 text-gray-800 shadow-lg sm:p-8">
		<div class="mb-4 flex items-center justify-between">
			<h1 class="text-2xl font-semibold text-gray-900 sm:text-3xl">Why? Who? What?</h1>
			<Button variant="transparent" href="/" class="underline">Back</Button>
		</div>
		<p class="mb-10 text-sm text-gray-500">MARS · 2024</p>

		<blockquote class="mb-6 border-l-4 border-gray-300 pl-4 italic text-gray-700">
			"Droodles are small silly drawings in a square box that you don't understand until you ask,
			and then it's too late to wish you hadn't."
		</blockquote>

		<section class="mb-8">
			<h2 class="mb-2 text-lg font-medium text-gray-700">What is a droodle?</h2>
			<p class="text-base leading-relaxed text-gray-600">
				A droodle is a small abstract drawing with a deadpan caption that does not explain what
				you are looking at. The joke happens in the gap between the two: a few black lines, a
				punchline, an answer that is either obvious in retrospect or completely impossible. The
				form was invented by
				<span class="font-semibold">Roger Price</span> in 1953 and published in several books
				across the decade.
			</p>
		</section>

		<section class="mb-8">
			<h2 class="mb-2 text-lg font-medium text-gray-700">Who was Roger Price?</h2>
			<p class="text-base leading-relaxed text-gray-600">
				A comedian, writer, and editor who specialized in formally constrained absurdity. He
				designed droodles to invite multiple interpretations from minimal input. He also
				co-created
				<a
					class="font-medium underline"
					href="https://en.wikipedia.org/wiki/Mad_Libs"
					target="_blank"
					rel="noopener">Mad Libs</a
				>, which shares the same instinct: small, tightly-bounded nonsense produces more
				laughter per page than almost anything else.
			</p>
		</section>

		<section class="mb-8">
			<h2 class="mb-2 text-lg font-medium text-gray-700">Why this game?</h2>
			<p class="text-base leading-relaxed text-gray-600">
				Because the droodles deserve a wider audience than the secondhand book market gives them.
				Match a drawing to its caption. Some are pure geometry. Some are weirdly profound. None
				of them can be solved by reasoning.
			</p>
		</section>

		<p class="text-xs text-gray-500">
			Droodles © the estate of Roger Price (1918-1990). This is a non-commercial tribute. If you
			enjoy them, buy the
			<a
				class="underline"
				href="https://tallfellow.com/droodles-compendium/"
				target="_blank"
				rel="noopener">Droodles Compendium</a
			>.
		</p>
	</div>
</div>

<style>
	@keyframes scroll {
		from {
			transform: translateY(0);
		}
		to {
			transform: translateY(-50%);
		}
	}

	.scrolling-background {
		position: absolute;
		top: 0;
		left: 0;
		width: 100%;
		height: 200%;
		display: grid;
		grid-template-columns: repeat(10, 1fr);
		opacity: 0.15;
		animation: scroll 60s linear infinite;
	}

	.scrolling-background img {
		width: 100%;
		height: auto;
		object-fit: cover;
		transform: scale(0.5);
	}

	@media (prefers-reduced-motion: reduce) {
		.scrolling-background {
			animation: none;
		}
	}
</style>
