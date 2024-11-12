<script>
	import imageMappings from '$lib/image_mappings.json';

	const allImages = Object.keys(imageMappings).map((key) => ({
		filename: key,
		...imageMappings[key]
	}));

	let dif = $state(5);
	let images = $derived(
		allImages
			.sort(() => 0.5 - Math.random())
			.slice(0, dif * dif)
			.map((image) => ({ ...image, matched: false }))
	);
	let captions = $derived(images.map((image) => ({ ...image, matched: false })));

	let selectedImage = $state(null);
	let selectedCaption = $state(null);

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
				const imageIndex = images.findIndex((img) => img.filename === selectedImage.filename);
				const captionIndex = captions.findIndex(
					(capt) => capt.filename === selectedCaption.filename
				);
				if (imageIndex !== -1) images[imageIndex].matched = true;
				if (captionIndex !== -1) captions[captionIndex].matched = true;
				// Clear selections
				selectedImage = null;
				selectedCaption = null;
			}
		}
	}
</script>

<div class="flex font-serif">
	<div class="w-1/2 p-4">
		<div class="grid gap-4" style="grid-template-columns: repeat({dif}, minmax(0, 1fr));">
			{#each images as image}
				<button
					class:bg-black={image.matched}
					class:opacity-20={image?.filename == selectedImage?.filename}
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
					class:underline={caption?.filename == selectedCaption?.filename}
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
