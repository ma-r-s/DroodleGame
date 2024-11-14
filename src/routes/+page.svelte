<script>
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
	let currentIndex = $state(0); // Track the current position in the allImages array
	let images = $state([]);
	let captions = $state([]);

	let selectedImage = $state(null);
	let selectedCaption = $state(null);
	let mistakes = $state(0); // Initialize mistakes counter

	// Populate the initial puzzle
	function initializePuzzle() {
		images = allImages.slice(currentIndex, currentIndex + dif * dif).map((image) => ({
			...image,
			matched: false
		}));
		captions = shuffleArray(images.map((image) => ({ ...image })));
	}

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
			if (selectedImage.filename === selectedCaption.filename) {
				const imageIndex = images.findIndex((img) => img.filename === selectedImage.filename);
				const captionIndex = captions.findIndex((cap) => cap.filename === selectedCaption.filename);

				if (imageIndex !== -1) images[imageIndex].matched = true;
				if (captionIndex !== -1) captions[captionIndex].matched = true;

				// Clear selections after a correct match
				selectedImage = null;
				selectedCaption = null;
			} else {
				// Increment mistakes counter on mismatch
				mistakes += 1;
				// Clear selections after a mistake
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
		// Update currentIndex for the next set of images
		currentIndex += dif * dif;
		if (currentIndex >= allImages.length) {
			currentIndex = 0; // Wrap around if all images have been shown
		}

		initializePuzzle();

		// Reset selections and mistakes
		selectedImage = null;
		selectedCaption = null;
		mistakes = 0;
	}

	function increaseDifficulty() {
		dif += 1;
		newPuzzle();
	}

	function decreaseDifficulty() {
		dif -= 1;
		newPuzzle();
	}

	// Initialize the first puzzle
	initializePuzzle();
</script>

<div class="flex h-dvh flex-col">
	<!-- Top Bar -->
	<div class="flex items-center justify-between px-4 pt-4">
		<h1 class="text-xl font-bold">Droodles</h1>
		<div class="flex items-center gap-2">
			<Button disabled={dif == 3} variant="outline" size="icon" onclick={decreaseDifficulty}>
				<ChevronLeft class="h-4 w-4" />
			</Button>
			<span>Difficulty: {dif}x{dif}</span>
			<Button disabled={dif == 8} variant="outline" size="icon" onclick={increaseDifficulty}>
				<ChevronRight class="h-4 w-4" />
			</Button>
			<Button variant="outline" onclick={newPuzzle}>New Puzzle</Button>
		</div>
	</div>

	<!-- Main board area filling remaining screen space -->
	<div class="flex flex-grow flex-col items-center overflow-hidden font-serif lg:flex-row">
		<div class="flex h-full justify-center overflow-y-auto p-4 lg:w-1/2">
			<div
				class="grid aspect-square h-full w-full gap-4"
				style="grid-template-columns: repeat({dif}, minmax(0, 1fr));"
			>
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

		<div class="flex h-full justify-center overflow-y-auto p-4 lg:w-1/2">
			<div
				class="grid aspect-square h-full w-full gap-4"
				style="grid-template-columns: repeat({dif}, minmax(0, 1fr));"
			>
				{#each captions as caption}
					<button
						class:invert={caption.matched}
						class:underline={caption?.filename === selectedCaption?.filename}
						class="aspect-square w-full cursor-pointer overflow-hidden border-2 border-gray-500 bg-white decoration-wavy"
						onclick={() => selectCaption(caption)}
						disabled={caption.matched}
					>
						<p class="px-2 text-center text-[5px] md:text-xs">{caption.name}</p>
					</button>
				{/each}
			</div>
		</div>
	</div>

	<!-- Bottom Section -->
	<div class="flex justify-between px-4 pb-4">
		<Button variant="transparent" href="/www" class="p-0 overline">Why? Who? What</Button>
		<p class="font-serif text-lg">Mistakes: {mistakes}</p>
	</div>
</div>
