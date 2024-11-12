<script lang="ts">
	import imageMappings from '$lib/image_mappings.json';
	import ChevronRight from 'lucide-svelte/icons/chevron-right';
	import ChevronLeft from 'lucide-svelte/icons/chevron-left';
	import { Button } from '$lib/components/ui/button/index.js';

	// Initialize images in original order, no shuffling
	const allImages = Object.keys(imageMappings).map((key) => ({
		filename: key,
		...imageMappings[key],
		matched: false
	}));

	let dif = $state(5);
	let images = $state(allImages.slice(0, dif * dif));
	let captions = $state(images.map((image) => ({ ...image })));

	let selectedImage = $state(null);
	let selectedCaption = $state(null);

	// Use Maps for fast lookup of images and captions
	const imageMap = $derived(new Map(images.map((image) => [image.filename, image])));
	const captionMap = $derived(new Map(captions.map((caption) => [caption.filename, caption])));

	function selectImage(image) {
		selectedImage = image;
		if (selectedCaption) checkMatch();
	}

	function selectCaption(caption) {
		selectedCaption = caption;
		if (selectedImage) checkMatch();
	}

	function checkMatch() {
		if (selectedImage && selectedCaption) {
			if (selectedImage.name === selectedCaption.name) {
				imageMap.get(selectedImage.filename).matched = true;
				captionMap.get(selectedCaption.filename).matched = true;
				selectedImage = null;
				selectedCaption = null;
			}
		}
	}
	function shuffleArray(array) {
		for (let i = array.length - 1; i > 0; i--) {
			const j = Math.floor(Math.random() * (i + 1));
			[array[i], array[j]] = [array[j], array[i]];
		}
		return array;
	}

	function newPuzzle() {
		// Shuffle allImages and pick the first `dif * dif` items
		const shuffledImages = shuffleArray([...allImages]);
		images = shuffledImages.slice(0, dif * dif).map((image) => ({ ...image, matched: false }));
		captions = images.map((image) => ({ ...image }));
		selectedImage = null;
		selectedCaption = null;
	}

	function increaseDifficulty() {
		if (dif < 8) dif += 1; // Adjust this max limit as desired
		newPuzzle();
	}

	function decreaseDifficulty() {
		if (dif > 2) dif -= 1; // Adjust this min limit as desired
		newPuzzle();
	}
</script>

<div class="flex items-center justify-between p-4">
	<h1 class="text-xl font-bold">Droodles</h1>
	<div class="flex items-center gap-2">
		<Button variant="outline" size="icon" onclick={decreaseDifficulty}>
			<ChevronLeft class="h-4 w-4" />
		</Button>
		<span>Difficulty: {dif}x{dif}</span>
		<Button variant="outline" size="icon" onclick={increaseDifficulty}>
			<ChevronRight class="h-4 w-4" />
		</Button>
		<Button variant="outline" onclick={newPuzzle}>New Puzzle</Button>
	</div>
</div>

<div class="flex font-serif">
	<div class="w-1/2 p-4">
		<div class="grid gap-4" style="grid-template-columns: repeat({dif}, minmax(0, 1fr));">
			{#each images as image}
				<button
					class:bg-black={image.matched}
					class:opacity-20={image?.filename === selectedImage?.filename}
					class="aspect-square w-full cursor-pointer overflow-hidden"
					onclick={() => selectImage(image)}
					disabled={image.matched}
				>
					<img
						src={'/droodles/' + image.filename}
						alt={image.alt}
						class="h-auto w-full"
						class:invert={image.matched}
					/>
				</button>
			{/each}
		</div>
	</div>

	<div class="w-1/2 p-4">
		<div class="grid gap-4" style="grid-template-columns: repeat({dif}, minmax(0, 1fr));">
			{#each captions as caption}
				<button
					class:invert={caption.matched}
					class:underline={caption?.filename === selectedCaption?.filename}
					class="aspect-square w-full cursor-pointer overflow-hidden border-2 border-gray-500 bg-white decoration-wavy"
					onclick={() => selectCaption(caption)}
					disabled={caption.matched}
				>
					<p class="px-2 text-center text-xs">{caption.name}</p>
				</button>
			{/each}
		</div>
	</div>
</div>
