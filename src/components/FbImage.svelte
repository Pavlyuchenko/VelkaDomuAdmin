<script>
	import { range } from "../range";

	var canvas;
	var context;
	var drawing;
	const fillMixedText = (ctx, args, x, y) => {
		let defaultFillStyle = ctx.fillStyle;
		let defaultFont = ctx.font;

		ctx.save();
		args.forEach(({ text, fillStyle, font }) => {
			ctx.fillStyle = fillStyle || defaultFillStyle;
			ctx.font = font || defaultFont;
			x += ctx.measureText(text).width;
		});
		var x = canvas.width / 2 - x / 2;
		ctx.restore();

		args.forEach(({ text, fillStyle, font }) => {
			ctx.fillStyle = fillStyle || defaultFillStyle;
			ctx.font = font || defaultFont;
			ctx.strokeText(text, x, y);
			ctx.fillText(text, x, y);
			x += ctx.measureText(text).width;
		});
	};
	window.onload = () => {
		canvas = document.getElementById("canvas");
		const saveButton = document.getElementById("save");
		const loadInput = document.getElementById("load");
		context = canvas.getContext("2d");

		drawing = new Drawing(canvas, saveButton, loadInput);
		drawUnchanging();
		updateText();

		fillMixedText(
			context,
			[{ text: text[0], fillStyle: "#ffffff" }],
			0,
			canvas.height - 40
		);
	};

	function drawUnchanging() {
		var grV = context.createLinearGradient(0, 0, 0, context.canvas.height);
		grV.addColorStop(0.6, "rgba(0,0,0,0)");
		grV.addColorStop(1, "rgba(0, 0, 0, .7)");

		context.fillStyle = grV;
		context.fillRect(0, 0, context.canvas.width, context.canvas.height);

		let width = 600;
		let height = 50;
		context.lineWidth = 1;
		context.rotate((30 * Math.PI) / 180);
		context.rect(canvas.width - width, -330, width, height);
		context.fillStyle = "rgba(255, 138, 0, .5)";
		context.fill();
		context.strokeStyle = "#333333";
		context.stroke();

		context.fillStyle = "#333333";
		context.font = "700 30px Titillium Web";
		context.fillText("VelkáDomů.cz", canvas.width - 240, -295);

		context.rotate(-(30 * Math.PI) / 180);
		context.fillStyle = "rgba(255, 138, 0, .7)";

		let placeImage;
		switch (numberOfRows) {
			case 1:
				height = 70;
				placeImage = 180;
				break;
			case 2:
				height = 110;
				placeImage = 230;
				break;
			case 3:
				height = 160;
				placeImage = 280;
				break;
			case 4:
				height = 210;
				placeImage = 330;
				break;
			case 5:
				height = 265;
				placeImage = 380;
				break;
		}
		width = 10;
		context.fillRect(0, canvas.height - height - 20, 10, height);
		context.fillRect(
			canvas.width - width,
			canvas.height - height - 20,
			10,
			height
		);

		const base_image = new Image();
		base_image.crossOrigin = "anonymous";
		base_image.onload = function () {
			context.drawImage(
				base_image,
				canvas.width / 2 - 40,
				canvas.height - placeImage
			);
		};
		base_image.src =
			"https://ik.imagekit.io/velkadomu/trans_logo_YmbBrxG7Q0Cu.png";
	}

	var imageVar;

	let imageChosen = false;

	class Drawing {
		constructor(canvas, saveButton, loadInput) {
			saveButton.addEventListener("click", () => this.save());
			loadInput.addEventListener("change", (event) => this.load(event));

			const rect = canvas.getBoundingClientRect();

			this.offsetLeft = rect.left;
			this.offsetTop = rect.top;

			this.canvas = canvas;
			this.context = this.canvas.getContext("2d");
		}
		save() {
			const data = this.canvas.toDataURL("image/png");
			const a = document.createElement("a");
			a.href = data;
			a.download = "velkadomu.png";
			a.click();
		}
		load(event) {
			const file = [...event.target.files].pop();
			this.readTheFile(file).then((image) => this.loadTheImage(image));
		}
		loadTheImage(image) {
			imageVar = image;
			imageChosen = true;
			const img = new Image();
			const canvas = this.canvas;
			img.onload = function () {
				const context = canvas.getContext("2d");
				context.clearRect(0, 0, canvas.width, canvas.height);

				if (img.width > img.height) {
					var aspectRatio = img.width / img.height;
					var imgWidth = canvas.width * aspectRatio;
					var xOffset = -(imgWidth - canvas.width) / 2;

					context.drawImage(
						img,
						0, // clip X
						0, // clip Y
						img.width, // source width
						img.height, // source height
						xOffset, // X coordinate
						0, // Y coordinate
						imgWidth, // Width of image
						canvas.height // Height of image
					); // destination size
				} else {
					var aspectRatio = img.height / img.width;
					var imgHeight = canvas.height * aspectRatio;
					var yOffset = -(imgHeight - canvas.height) / 2;

					context.drawImage(
						img,
						0, // clip X
						0, // clip Y
						img.width, // source width
						img.height, // source height
						0, // X coordinate
						yOffset, // Y coordinate
						canvas.width, // Width of image
						imgHeight // Height of image
					); // destination size
				}
				drawUnchanging();
				colorText();
			};
			img.src = image;
		}
		readTheFile(file) {
			const reader = new FileReader();
			return new Promise((resolve) => {
				reader.onload = (event) => {
					resolve(event.target.result);
				};
				reader.readAsDataURL(file);
			});
		}
	}

	function updateText() {
		context.fillStyle = "white";
		context.font = "700 44px Titillium Web";
		context.fontWeight = "700";
		/* context.textAlign = "center"; */
		context.strokeStyle = "black";
		context.lineWidth = 5;
		/* context.strokeText(text, canvas.width / 2, canvas.height - 40);
		context.fillText(text, canvas.width / 2, canvas.height - 40); */

		/* context.strokeText(text2, canvas.width / 2, canvas.height - 90);
		context.fillText(text2, canvas.width / 2, canvas.height - 90);

		context.strokeText(text3, canvas.width / 2, canvas.height - 140);
		context.fillText(text3, canvas.width / 2, canvas.height - 140);

		context.strokeText(text4, canvas.width / 2, canvas.height - 190);
		context.fillText(text4, canvas.width / 2, canvas.height - 190);

		context.strokeText(text5, canvas.width / 2, canvas.height - 240);
		context.fillText(text5, canvas.width / 2, canvas.height - 240); */
	}

	function colorText() {
		for (let index = numberOfRows - 1; index >= 0; index--) {
			let formatted = text[numberOfRows - index - 1].split("/");
			let finalText = [];
			let i = 0;
			for (let word of formatted) {
				if (i % 2 == 0) {
					finalText.push({
						text: word,
						fillStyle: "#ffffff",
					});
					i++;
				} else {
					finalText.push({
						text: word,
						fillStyle: "#ff8a00",
					});
					i++;
				}
			}
			context.lineWidth = 5;
			context.strokeStyle = "black";
			context.font = "700 44px Titillium Web";
			fillMixedText(
				context,
				finalText,
				0,
				canvas.height - 40 - 50 * index
			);
		}
	}

	function updateCanvas() {
		if (imageVar) {
			const img = new Image();
			img.onload = function () {
				context.clearRect(0, 0, canvas.width, canvas.height);

				if (img.width > img.height) {
					var aspectRatio = img.width / img.height;
					var imgWidth = canvas.width * aspectRatio;
					var xOffset = -(imgWidth - canvas.width) / 2;

					context.drawImage(
						img,
						0, // clip X
						0, // clip Y
						img.width, // source width
						img.height, // source height
						xOffset, // X coordinate
						0, // Y coordinate
						imgWidth, // Width of image
						canvas.height // Height of image
					); // destination size
				} else {
					var aspectRatio = img.height / img.width;
					var imgHeight = canvas.height * aspectRatio;
					var yOffset = -(imgHeight - canvas.height) / 2;

					context.drawImage(
						img,
						0, // clip X
						0, // clip Y
						img.width, // source width
						img.height, // source height
						0, // X coordinate
						yOffset, // Y coordinate
						canvas.width, // Width of image
						imgHeight // Height of image
					); // destination size
				}
				drawUnchanging();
				colorText();
			};
			img.src = imageVar;
		} else {
			context.clearRect(0, 0, canvas.width, canvas.height);

			if (imageURL != "") {
				const img = new Image();
				img.onload = function () {
					context.clearRect(0, 0, canvas.width, canvas.height);

					if (img.width > img.height) {
						var aspectRatio = img.width / img.height;
						var imgWidth = canvas.width * aspectRatio;
						var xOffset = -(imgWidth - canvas.width) / 2;

						context.drawImage(
							img,
							0, // clip X
							0, // clip Y
							img.width, // source width
							img.height, // source height
							xOffset, // X coordinate
							0, // Y coordinate
							imgWidth, // Width of image
							canvas.height // Height of image
						); // destination size
					} else {
						var aspectRatio = img.height / img.width;
						var imgHeight = canvas.height * aspectRatio;
						var yOffset = -(imgHeight - canvas.height) / 2;

						context.drawImage(
							img,
							0, // clip X
							0, // clip Y
							img.width, // source width
							img.height, // source height
							0, // X coordinate
							yOffset, // Y coordinate
							canvas.width, // Width of image
							imgHeight // Height of image
						); // destination size
					}
					drawUnchanging();
					colorText();
				};
				img.crossOrigin = "anonymous";

				if (imageURL != "") {
					img.src = imageURL;
				} else {
					var imagekit = new ImageKit({
						publicKey: "public_gn7dgk7SJbBFinh/vtiiagVDgbM=",
						urlEndpoint: "https://ik.imagekit.io/velkadomu",
						authenticationEndpoint:
							"https://velkadomu.pythonanywhere.com/auth",
					});
					imagekit.upload(
						{
							file: url,
							fileName: "fb.jpg",
						},
						function (err, result) {
							img.src = imagekit.url({
								src: result.url,
							});
							imageURL = imagekit.url({
								src: result.url,
							});
						}
					);
				}
			} else {
				drawUnchanging();
				colorText();
			}
		}
	}

	let text = ["Text", "", "", "", ""];
	let numberOfRows = 1;

	let help = false;

	let url = "";
	let imageURL = "";
	function urlImage() {
		const img = new Image();
		img.onload = function () {
			context.clearRect(0, 0, canvas.width, canvas.height);

			if (img.width > img.height) {
				var aspectRatio = img.width / img.height;
				var imgWidth = canvas.width * aspectRatio;
				var xOffset = -(imgWidth - canvas.width) / 2;

				context.drawImage(
					img,
					0, // clip X
					0, // clip Y
					img.width, // source width
					img.height, // source height
					xOffset, // X coordinate
					0, // Y coordinate
					imgWidth, // Width of image
					canvas.height // Height of image
				); // destination size
			} else {
				var aspectRatio = img.height / img.width;
				var imgHeight = canvas.height * aspectRatio;
				var yOffset = -(imgHeight - canvas.height) / 2;

				context.drawImage(
					img,
					0, // clip X
					0, // clip Y
					img.width, // source width
					img.height, // source height
					0, // X coordinate
					yOffset, // Y coordinate
					canvas.width, // Width of image
					imgHeight // Height of image
				); // destination size
			}
			drawUnchanging();
			colorText();
		};
		img.crossOrigin = "anonymous";

		if (imageURL != "") {
			img.src = imageURL;
		} else {
			var imagekit = new ImageKit({
				publicKey: "public_gn7dgk7SJbBFinh/vtiiagVDgbM=",
				urlEndpoint: "https://ik.imagekit.io/velkadomu",
				authenticationEndpoint:
					"https://velkadomu.pythonanywhere.com/auth",
			});
			imagekit.upload(
				{
					file: url,
					fileName: "fb.jpg",
				},
				function (err, result) {
					img.src = imagekit.url({
						src: result.url,
					});
					imageURL = imagekit.url({
						src: result.url,
					});
				}
			);
		}
	}
</script>

<div id="container">
	<canvas id="canvas" width="800" height="800" />

	<div id="customize">
		{#if url == ""}
			Vyber obrázek: <input type="file" id="load" name="image" />
			<br />
			{#if url == "" && !imageChosen}
				nebo
				<br />
			{/if}
		{/if}
		{#if !imageChosen}
			<input
				type="text"
				placeholder="Zadel URL obrázku"
				bind:value={url}
				on:input={urlImage}
			/>
		{/if}
		<br />
		<br />
		Počet řádků:
		<input
			type="range"
			min="1"
			max="5"
			id="myRange"
			bind:value={numberOfRows}
			on:change={updateCanvas}
		/>
		{numberOfRows}
		{#if numberOfRows == 1}řádek{:else if numberOfRows == 5}řádků{:else}řádky{/if}
		<br />
		{#each range(numberOfRows) as i}
			<input type="text" bind:value={text[i]} on:input={updateCanvas} />
			<br />
		{/each}
		(Oranžový text: / ... /)
		<br />
		<button id="save" type="button"> Ulož obrázek </button>
		<br />
		<br />

		<span
			id="help"
			on:click={() => {
				help = !help;
			}}>Uřízl se ti obrázek jinak, než jsi chtěl?</span
		><br />
		{#if help}
			(<b>Pokud se ti obrázek uřízl jinak, než bys chtěl</b>, pak klikni
			na tento odkaz:
			<a href="https://www.iloveimg.com/resize-image" target="_blank"
				>Odkaz</a
			> <br />a vyber obrázek, který chceš použít. Na panelu napravo
			nastav
			<b>menší z hodnot</b> <br />
			'Width' a 'Height' na 500 (tzn. pokud je 'Height' &lt; 'Width', pak nastav
			'Height' na 500,<br />
			jinak nastav 'Width' na 500). Pak klikni na tlačítko dole
			<b>'Resize IMAGES'</b>.
			<br />
			<br />
			Nyní přejdi na stránku
			<a href="https://www.iloveimg.com/crop-image" target="_blank"
				>Odkaz</a
			>, a vlož zde obrázek, který sis <b>stáhl z předchozí stránky.</b>
			<br />
			Vpravo na panelu nastav 'Width' i 'Height' <b>na 500</b>, a pak si
			<b
				>nastav <br />
				čtverec</b
			>, který se objevil na obrázku uprostřed tak,
			<b
				>jak chceš, aby fotka <br />
				ve výsledku vypadala</b
			>. Pak jen klikni na 'Crop IMAGE' a
			<b>vlož stažený obrázek zde</b>.)
		{/if}
	</div>
</div>

<style>
	#container {
		display: flex;
		margin-top: 30px;
	}
	#canvas {
		border: 1px solid black;
	}
	#customize {
		margin-left: 20px;
	}
	input[type="text"] {
		width: 100%;
		height: 30px;
		font-size: 18px;
		margin-top: 5px;
	}

	button {
		margin-top: 20px;
	}

	a {
		color: blue;
		text-decoration: underline;
	}

	#help {
		text-decoration: underline;
		cursor: pointer;
	}
	#help:hover {
		color: blue;
	}
</style>
