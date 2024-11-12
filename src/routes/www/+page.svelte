<script>
	import { Button } from '$lib/components/ui/button/index.js';
	import imageMappings from '$lib/image_mappings.json';

	// Load images for the background effect
	const backgroundImages = Object.keys(imageMappings).map((key) => ({
		src: `/droodles/${key}`,
		alt: imageMappings[key].alt
	}));
</script>

<div
	class="relative flex min-h-screen items-center justify-center overflow-hidden bg-gray-50 px-4 py-6 font-serif"
>
	<!-- Scrolling Background Images -->
	<div class="scrolling-background">
		{#each backgroundImages as image}
			<img src={image.src} alt={image.alt} class="opacity-50" />
		{/each}
		{#each backgroundImages as image}
			<!-- Duplicate for seamless scrolling -->
			<img src={image.src} alt={image.alt} class="opacity-50" />
		{/each}
	</div>

	<!-- Main Content Container with White Background -->
	<div class="relative z-10 w-full max-w-[800px] rounded-lg bg-white p-8 text-gray-800 shadow-lg">
		<!-- Back Button -->
		<div class="mb-4 flex items-center justify-between">
			<h1 class="text-3xl font-semibold text-gray-900">What? Who? Why?</h1>
			<Button variant="transparent" href="/" class="underline">Back</Button>
		</div>
		<p class="mb-10 text-sm text-gray-500">MARS - 2024</p>

		<!-- Quote Section -->
		<blockquote class="mb-6 border-l-4 border-gray-300 pl-4 italic text-gray-700">
			"Droodles are small silly drawings in a square box that you don’t understand until you ask,
			and then it’s too late to wish you hadn’t."
		</blockquote>

		<!-- Main Content -->
		<section class="mb-8">
			<h2 class="mb-2 text-lg font-medium text-gray-700">What Makes Droodles Special?</h2>
			<p class="text-base leading-relaxed text-gray-600">
				<span class="font-medium italic">Droodles</span> are seemingly simple doodles with a unique
				charm that lies in their multiple interpretations. They are playful, abstract images that
				prompt viewers to imagine a story or meaning, making them a delightful blend of art and
				humor. Created by <span class="font-semibold">Roger Price</span> in the 1950s, these quirky illustrations
				invite curiosity and creative thinking.
			</p>
		</section>

		<section class="mb-8">
			<h2 class="mb-2 text-lg font-medium text-gray-700">Who Created Droodles and Why?</h2>
			<p class="text-base leading-relaxed text-gray-600">
				Roger Price, a comedian, writer, and artist, designed Droodles as a way to inspire laughter
				and thought. He aimed to create something simple yet profound—images that could spark
				multiple interpretations and make people see the world differently. Droodles became a
				cultural icon, beloved for their humor and ingenuity, and continue to inspire new
				generations with their minimalist charm.
			</p>
		</section>

		<section class="mb-8">
			<h2 class="mb-2 text-lg font-medium text-gray-700">Why Droodles Matter</h2>
			<p class="text-base leading-relaxed text-gray-600">
				In a world that often feels overly complex, Droodles remind us of the joy of simplicity.
				Each Droodle is a little puzzle, a challenge to look beyond the obvious. They encourage us
				to pause, think, and perhaps see something unexpected in a simple line or shape. Droodles
				are more than just art; they're a way to exercise creativity and enjoy a moment of
				lighthearted wonder.
			</p>
		</section>
	</div>
</div>

<style>
	/* Background scroll animation */
	@keyframes scroll {
		from {
			transform: translateY(0);
		}
		to {
			transform: translateY(-100%);
		}
	}

	/* Styling for the background image container */
	.scrolling-background {
		position: absolute;
		top: 0;
		left: 0;
		width: 100%;
		height: 200%;
		display: grid;
		grid-template-columns: repeat(10, 1fr); /* More columns to make images smaller */
		opacity: 0.15; /* Lower opacity for subtle effect */
		animation: scroll 50s linear infinite; /* Scroll animation */
	}

	.scrolling-background img {
		width: 100%;
		height: auto;
		object-fit: cover;
		transform: scale(0.5); /* Scale down images to half size */
	}
</style>
