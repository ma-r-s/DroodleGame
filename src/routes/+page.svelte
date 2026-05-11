<script>
	import imageMappings from '$lib/image_mappings.json';
	import ChevronRight from 'lucide-svelte/icons/chevron-right';
	import ChevronLeft from 'lucide-svelte/icons/chevron-left';
	import RotateCcw from 'lucide-svelte/icons/rotate-ccw';
	import { Button } from '$lib/components/ui/button/index.js';

	const allImages = Object.keys(imageMappings).map((key) => ({
		filename: key,
		...imageMappings[key],
		matched: false
	}));

	const MIN_DIF = 3;
	const MAX_DIF = 8;

	let dif = $state(5);
	let currentIndex = $state(0);
	let images = $state([]);
	let captions = $state([]);
	// Snapshot of the current panel so "reset" can replay the same droodles.
	let currentPanel = $state([]);

	let selectedImage = $state(null);
	let selectedCaption = $state(null);
	let mistakes = $state(0);

	const matchedCount = $derived(images.filter((i) => i.matched).length);
	const total = $derived(images.length);
	const won = $derived(total > 0 && matchedCount === total);

	function initializePuzzle() {
		const slice = allImages
			.slice(currentIndex, currentIndex + dif * dif)
			.map((image) => ({ ...image, matched: false }));
		currentPanel = slice.map((img) => ({ ...img }));
		images = slice;
		captions = shuffleArray(slice.map((image) => ({ ...image })));
		selectedImage = null;
		selectedCaption = null;
		mistakes = 0;
	}

	function selectImage(image) {
		if (won) return;
		selectedImage = image;
		if (selectedCaption) checkMatch();
	}

	function selectCaption(caption) {
		if (won) return;
		selectedCaption = caption;
		if (selectedImage) checkMatch();
	}

	function checkMatch() {
		if (selectedImage && selectedCaption) {
			if (selectedImage.filename === selectedCaption.filename) {
				const imageIndex = images.findIndex((img) => img.filename === selectedImage.filename);
				const captionIndex = captions.findIndex(
					(cap) => cap.filename === selectedCaption.filename
				);
				if (imageIndex !== -1) images[imageIndex].matched = true;
				if (captionIndex !== -1) captions[captionIndex].matched = true;
			} else {
				mistakes += 1;
			}
			selectedImage = null;
			selectedCaption = null;
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
		currentIndex += dif * dif;
		if (currentIndex >= allImages.length) currentIndex = 0;
		initializePuzzle();
	}

	function resetPuzzle() {
		images = currentPanel.map((img) => ({ ...img, matched: false }));
		captions = shuffleArray(currentPanel.map((img) => ({ ...img, matched: false })));
		selectedImage = null;
		selectedCaption = null;
		mistakes = 0;
	}

	function increaseDifficulty() {
		if (dif >= MAX_DIF) return;
		dif += 1;
		newPuzzle();
	}

	function decreaseDifficulty() {
		if (dif <= MIN_DIF) return;
		dif -= 1;
		newPuzzle();
	}

	initializePuzzle();
</script>

<svelte:head>
	<title>Droodles</title>
</svelte:head>

<div class="flex h-dvh flex-col">
	<!-- Top Bar: wraps on narrow viewports instead of overflowing -->
	<header class="flex flex-wrap items-center justify-between gap-x-3 gap-y-2 px-4 pt-4">
		<h1 class="font-serif text-xl font-bold">Droodles</h1>
		<div class="ml-auto flex flex-wrap items-center gap-2">
			<div class="flex items-center gap-1">
				<Button
					disabled={dif <= MIN_DIF}
					variant="outline"
					size="icon"
					onclick={decreaseDifficulty}
					aria-label="Decrease difficulty"
				>
					<ChevronLeft class="h-4 w-4" />
				</Button>
				<span class="min-w-[3.5rem] text-center text-sm tabular-nums">{dif}×{dif}</span>
				<Button
					disabled={dif >= MAX_DIF}
					variant="outline"
					size="icon"
					onclick={increaseDifficulty}
					aria-label="Increase difficulty"
				>
					<ChevronRight class="h-4 w-4" />
				</Button>
			</div>
			<Button
				variant="outline"
				size="icon"
				onclick={resetPuzzle}
				aria-label="Reset this puzzle"
				title="Reset this puzzle"
			>
				<RotateCcw class="h-4 w-4" />
			</Button>
			<Button variant="outline" onclick={newPuzzle}>New Puzzle</Button>
		</div>
	</header>

	<!-- Main board: stacked on mobile, side-by-side on lg+ -->
	<div class="flex flex-grow flex-col items-center overflow-hidden font-serif lg:flex-row">
		<div class="flex h-full w-full justify-center overflow-y-auto p-2 md:p-4 lg:w-1/2">
			<div
				class="grid aspect-square h-full w-full gap-2 md:gap-4"
				style="grid-template-columns: repeat({dif}, minmax(0, 1fr));"
			>
				{#each images as image (image.filename)}
					<button
						class:bg-black={image.matched}
						class:opacity-20={image?.filename === selectedImage?.filename}
						class="aspect-square w-full cursor-pointer overflow-hidden transition-opacity"
						onclick={() => selectImage(image)}
						disabled={image.matched}
						aria-label={image.alt}
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

		<div class="flex h-full w-full justify-center overflow-y-auto p-2 md:p-4 lg:w-1/2">
			<div
				class="grid aspect-square h-full w-full gap-2 md:gap-4"
				style="grid-template-columns: repeat({dif}, minmax(0, 1fr));"
			>
				{#each captions as caption (caption.filename)}
					<button
						class:invert={caption.matched}
						class:underline={caption?.filename === selectedCaption?.filename}
						class="aspect-square w-full cursor-pointer overflow-hidden border-2 border-gray-500 bg-white decoration-wavy transition-colors"
						onclick={() => selectCaption(caption)}
						disabled={caption.matched}
					>
						<p class="px-1 text-center text-[10px] leading-tight sm:text-xs md:text-sm">
							{caption.name}
						</p>
					</button>
				{/each}
			</div>
		</div>
	</div>

	<!-- Bottom Section -->
	<footer class="flex items-center justify-between gap-3 px-4 pb-4 text-sm">
		<Button variant="transparent" href="/www" class="overline p-0">Why? Who? What?</Button>
		<p class="font-serif tabular-nums">
			{matchedCount}/{total} matched · {mistakes} mistake{mistakes === 1 ? '' : 's'}
		</p>
	</footer>

	<!-- Win Overlay -->
	{#if won}
		<div
			class="fixed inset-0 z-50 flex items-center justify-center bg-black/40 p-6 backdrop-blur-sm"
			role="dialog"
			aria-modal="true"
			aria-labelledby="win-title"
		>
			<div class="w-full max-w-md border-2 border-black bg-white p-8 font-serif text-gray-900">
				<h2 id="win-title" class="mb-2 text-2xl font-bold">You matched them all.</h2>
				<p class="mb-6 text-gray-700">
					{dif}×{dif} puzzle, {mistakes} mistake{mistakes === 1 ? '' : 's'}.
					{#if mistakes === 0}<span class="font-semibold">A clean run.</span>{/if}
				</p>
				<div class="flex flex-wrap gap-2">
					<Button onclick={newPuzzle}>Next puzzle</Button>
					<Button variant="outline" onclick={resetPuzzle}>Play this one again</Button>
				</div>
			</div>
		</div>
	{/if}
</div>
