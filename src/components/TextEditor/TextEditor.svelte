<script>
	import { onDestroy, onMount } from "svelte";
	import clickOutside from "svelte-outside-click";
	import { Link, navigate } from "svelte-routing";
	import { cookie, prezdivka } from "../../store";

	export let id;

	$: first = true;
	$: titulek = "Zadej titulek";

	$: firstPodnadpis = true;
	$: podnadpis = "Podnadpis...";

	$: blocks = [{ id: 1, content: "Tělo článku...", type: "p" }];
	$: urlObrazku = "Zadej URL Obrázku...";
	$: autorClanku = { id: 1, jmeno: "" };

	$: osnova = false;
	$: anketa = false;
	$: mainPopis = "";

	let autor = "Načítání";
	let stitky = [{ id: 1, color: "", nazev: "" }];

	let focusedEl = 0;
	$: cursorPos = 0;

	$: canDelete = true;
	$: pocetSlov = 0;
	$: pocetZnaku = 0;

	$: selectedMainStitek = { nazev: "" };
	$: dalsiStitky = "";

	let sendResponse = "";

	let nazevAnkety = "Název ankety...";
	let bodyAnkety = [{ nazev: "První bod" }, { nazev: "Druhý bod" }];

	let zdrojMainImage = "";

	onMount(() => {
		getDraft();

		document
			.getElementById("nadpish1")
			.addEventListener("paste", function (event) {
				event.preventDefault();
				document.execCommand(
					"inserttext",
					false,
					event.clipboardData.getData("text/plain")
				);
			});
		document
			.getElementById("podnadpis")
			.addEventListener("paste", function (event) {
				event.preventDefault();
				document.execCommand(
					"inserttext",
					false,
					event.clipboardData.getData("text/plain")
				);
			});

		document
			.getElementById("main-image")
			.addEventListener("paste", function (event) {
				event.preventDefault();
				document.execCommand(
					"inserttext",
					false,
					event.clipboardData.getData("text/plain")
				);
			});

		document
			.getElementById("main-popis-textarea")
			.addEventListener("paste", function (event) {
				event.preventDefault();
				document.execCommand(
					"inserttext",
					false,
					event.clipboardData.getData("text/plain")
				);
			});
		document.addEventListener(
			"keydown",
			function (e) {
				if (
					e.key == "s" &&
					(navigator.platform.match("Mac") ? e.metaKey : e.ctrlKey)
				) {
					e.preventDefault();
					sendData();
				}
			},
			false
		);
	});

	let differentSite = false;
	async function getDraft() {
		const res = await fetch(
			"https://fotbalpropal.pythonanywhere.com/draft/" + id,
			{
				method: "POST",
				headers: {
					"content-type": "application/json",
				},
				body: JSON.stringify({
					cookie: $cookie,
					prezdivka: $prezdivka,
				}),
			}
		);
		const json = await res.json();
		if (json.titulek != "None") {
			titulek = json.draft.titulek;
			podnadpis = json.draft.podnadpis;
			urlObrazku = json.draft.urlObrazek;
			mainPopis = json.draft.mainPopis;
			blocks = JSON.parse(json.draft.blocks);

			if (json.draft.anketa) {
				nazevAnkety = json.draft.nazevAnkety;
				bodyAnkety = JSON.parse(json.draft.bodyAnkety);
				anketa = json.draft.anketa;
			}

			autor = json.draft.autor;
			stitky = json.stitky;
			selectedMainStitek = json.draft.selectedStitek;

			first = false;
			firstPodnadpis = false;
			wordCount();

			if (json.logo != "VelkaDomu") {
				differentSite = json.logo;
			}

			zdrojMainImage = json.draft.zdrojObrazku;
		} else {
			autor = $prezdivka;
			stitky = json.stitky;
			if (json.draft) {
				selectedMainStitek = json.draft.selectedStitek;
			} else {
				selectedMainStitek = stitky[0];
			}

			if (json.logo != "VelkaDomu") {
				differentSite = json.logo;
			}
		}

		setTimeout(() => {
			for (var i = 1; i <= blocks.length; i++) {
				console.log(i);
				document
					.getElementById("block" + i)
					.addEventListener("paste", function (event) {
						event.preventDefault();
						document.execCommand(
							"inserttext",
							false,
							event.clipboardData.getData("text/plain")
						);
					});
			}
		}, 10);
	}

	onDestroy(() => {
		let elements = document.getElementsByClassName("editor");

		for (var i = 0; i < elements.length; i++) {
			elements[i].removeEventListener("paste", function (event) {
				event.preventDefault();
				document.execCommand(
					"inserttext",
					false,
					event.clipboardData.getData("text/plain")
				);
			});
		}
	});

	function updateIndex(event, element) {
		if (element.contains(event.target)) {
			cursorPos = getCaretIndex(element).toString();
		} else {
			cursorPos = "–";
		}
	}

	function getCaretIndex(element) {
		let position = 0;
		const isSupported = typeof window.getSelection !== "undefined";
		if (isSupported) {
			const selection = window.getSelection();
			// Check if there is a selection (i.e. cursor in place)
			if (selection.rangeCount !== 0) {
				// Store the original range
				const range = window.getSelection().getRangeAt(0);
				// Clone the range
				const preCaretRange = range.cloneRange();
				// Select all textual contents from the contenteditable element
				preCaretRange.selectNodeContents(element);
				// And set the range end to the original clicked position
				preCaretRange.setEnd(range.endContainer, range.endOffset);
				// Return the text length from contenteditable start to the range end
				position = preCaretRange.toString().length;
			}
		}
		return position;
	}

	function placeCaretAtEnd(el) {
		el.focus();
		if (
			typeof window.getSelection != "undefined" &&
			typeof document.createRange != "undefined"
		) {
			var range = document.createRange();
			range.selectNodeContents(el);
			range.collapse(false);
			var sel = window.getSelection();
			sel.removeAllRanges();
			sel.addRange(range);
		} else if (typeof document.body.createTextRange != "undefined") {
			var textRange = document.body.createTextRange();
			textRange.moveToElementText(el);
			textRange.collapse(false);
			textRange.select();
		}
	}

	function blockChange(e, id) {
		let block = blocks.find((x) => x.id === id);

		// Heading 1
		if (
			block.content.substring(0, 7) === "#&nbsp;" ||
			block.content.substring(0, 9) === "/h1&nbsp;"
		) {
			block.type = "h1";
			block.content = "";
			blocks = [...blocks];
		} else if (block.content.substring(0, 2) === "# ") {
			block.type = "h1";
			block.content = block.content.substring(2);
			blocks = [...blocks];
		} else if (block.content.substring(0, 4) === "/h1 ") {
			block.type = "h1";
			block.content = block.content.substring(4);
			blocks = [...blocks];
		}
		// Heading 2
		else if (
			block.content === "##&nbsp;" ||
			block.content === "/h2&nbsp;"
		) {
			block.type = "h2";
			block.content = "";
			blocks = [...blocks];
		} else if (block.content.substring(0, 3) === "## ") {
			block.type = "h2";
			block.content = block.content.substring(3);
			blocks = [...blocks];
		} else if (block.content.substring(0, 4) === "/h2 ") {
			block.type = "h2";
			block.content = block.content.substring(4);
			blocks = [...blocks];
		}
		// Heading 3
		else if (
			block.content === "###&nbsp;" ||
			block.content === "/h3&nbsp;"
		) {
			block.type = "h3";
			block.content = "";
			blocks = [...blocks];
		} else if (block.content.substring(0, 4) === "### ") {
			block.type = "h3";
			block.content = block.content.substring(4);
			blocks = [...blocks];
		} else if (block.content.substring(0, 4) === "/h3 ") {
			block.type = "h3";
			block.content = block.content.substring(4);
			blocks = [...blocks];
		}
		// Zvýraznění
		else if (
			block.content === "&gt;&nbsp;" ||
			block.content === "/z&nbsp;"
		) {
			block.type = "callout";
			block.content = "";
			blocks = [...blocks];
		} else if (block.content.substring(0, 5) === "&gt; ") {
			block.type = "callout";
			block.content = block.content.substring(5);
			blocks = [...blocks];
		} else if (block.content.substring(0, 3) === "/z ") {
			block.type = "callout";
			block.content = block.content.substring(3);
			blocks = [...blocks];
		}
		// Citace
		else if (block.content === "|&nbsp;" || block.content === "/c&nbsp;") {
			block.type = "citace";
			block.content = "";
			blocks = [...blocks];
		} else if (block.content.substring(0, 2) === "| ") {
			block.type = "citace";
			block.content = block.content.substring(2);
			blocks = [...blocks];
		} else if (block.content.substring(0, 3) === "/c ") {
			block.type = "citace";
			block.content = block.content.substring(3);
			blocks = [...blocks];
		}
		// Odrážka
		else if (block.content === "*&nbsp;" || block.content === "/o&nbsp;") {
			block.type = "odrazka";
			block.content = "";
			blocks = [...blocks];
		} else if (block.content.substring(0, 2) === "* ") {
			block.type = "odrazka";
			block.content = block.content.substring(2);
			blocks = [...blocks];
		} else if (block.content.substring(0, 3) === "/o ") {
			block.type = "odrazka";
			block.content = block.content.substring(3);
			blocks = [...blocks];
		}
		// Obrázek
		else if (
			block.content == "/img&nbsp;" ||
			block.content == "/image&nbsp;" ||
			block.content == "/obrazek&nbsp;"
		) {
			block.type = "obrazek";
			block.content = "";
			block.url = "Zadej URL obrázku...";
			blocks = [...blocks];

			if (id == blocks.length) {
				for (let index = id; index < blocks.length; index++) {
					blocks[index].id++;
				}

				blocks.splice(id, 0, { id: id + 1, content: "", type: "p" });

				blocks = [...blocks];
			}

			setTimeout(() => {
				let el = document.getElementById("block" + id);
				el.focus();

				document
					.getElementById("block" + id)
					.addEventListener("paste", function (event) {
						event.preventDefault();
						document.execCommand(
							"inserttext",
							false,
							event.clipboardData.getData("text/plain")
						);
					});
			}, 1);
		}
		// Twitter
		else if (
			block.content == "/twit&nbsp;" ||
			block.content == "/tweet&nbsp;"
		) {
			block.type = "twitter";
			block.content = "";
			block.tweet = "Zadej Tweet...";
			blocks = [...blocks];

			if (id == blocks.length) {
				for (let index = id; index < blocks.length; index++) {
					blocks[index].id++;
				}

				blocks.splice(id, 0, { id: id + 1, content: "", type: "p" });

				blocks = [...blocks];
			}

			setTimeout(() => {
				let el = document.getElementById("block" + id);
				el.focus();

				document
					.getElementById("block" + id)
					.addEventListener("paste", function (event) {
						event.preventDefault();
						document.execCommand(
							"inserttext",
							false,
							event.clipboardData.getData("text/plain")
						);
					});
			}, 1);

			console.log("block" + id);
		}
		// Video
		else if (
			block.content == "/vid&nbsp;" ||
			block.content == "/you&nbsp;" ||
			block.content == "/ytb&nbsp;"
		) {
			block.type = "youtube";
			block.content = "";
			block.url = "Zadej ID videa...";
			blocks = [...blocks];

			if (id == blocks.length) {
				for (let index = id; index < blocks.length; index++) {
					blocks[index].id++;
				}

				blocks.splice(id, 0, { id: id + 1, content: "", type: "p" });

				blocks = [...blocks];
			}

			setTimeout(() => {
				let el = document.getElementById("block" + id);
				el.focus();

				document
					.getElementById("block" + id)
					.addEventListener("paste", function (event) {
						event.preventDefault();
						document.execCommand(
							"inserttext",
							false,
							event.clipboardData.getData("text/plain")
						);
					});
			}, 1);
		}
		/* setTimeout(() => {
			let el = document.getElementById("block" + id);
			el.focus();
			document
				.getElementById("block" + id)
				.addEventListener("paste", function (event) {
					event.preventDefault();
					document.execCommand(
						"inserttext",
						false,
						event.clipboardData.getData("text/plain")
					);
				});
		}, 1); */
	}

	function keyDown(e, id) {
		let block = blocks.find((x) => x.id === id);

		updateIndex(e, document.getElementById("block" + id));

		if (
			!e.ctrlKey &&
			e.key == "Backspace" &&
			block.type != "p" &&
			cursorPos == 0 &&
			canDelete
		) {
			setTimeout(() => {
				let el = document.getElementById("block" + id);
				el.focus();

				document
					.getElementById("block" + id)
					.addEventListener("paste", function (event) {
						event.preventDefault();
						document.execCommand(
							"inserttext",
							false,
							event.clipboardData.getData("text/plain")
						);
					});
			}, 1);

			block.type = "p";
			blocks = [...blocks];
		} else if (
			e.key == "Backspace" &&
			(block.content == "" ||
				block.content == "<br />" ||
				block.content == "<br>") &&
			!e.ctrlKey &&
			canDelete
		) {
			if (
				block.type != "obrazek" &&
				block.type != "twitter" &&
				block.type != "youtube"
			) {
				deleteBlock(id);
			} else if (block.url == "" || block.tweet == "") {
				deleteBlock(id);
			}
		} else if (e.key == "Enter") {
			e.preventDefault();
			createBlock(id);
		} else if (e.key == "ArrowUp" && focusedEl != 1) {
			if (cursorPos == 0) {
				let el = document.getElementById("block" + (id - 1));
				el.focus();
				placeCaretAtEnd(el);
			}
		} else if (e.key == "ArrowUp" && focusedEl == 1) {
			if (cursorPos == 0) {
				createBlockAbove();
			}
		} else if (e.key == "ArrowDown" && focusedEl != blocks.length) {
			if (
				block.content.length - 1 == parseInt(cursorPos) ||
				block.content.length == 0 ||
				block.content.length == parseInt(cursorPos)
			) {
				let el = document.getElementById("block" + (id + 1));
				el.focus();
				//placeCaretAtEnd(el);
			}
		} else if (e.key == "ArrowDown" && focusedEl == blocks.length) {
			if (
				block.content.length - 1 == parseInt(cursorPos) ||
				block.content.length == 0 ||
				block.content.length == parseInt(cursorPos)
			) {
				createBlock(blocks.length);
			}
		} else if (e.key == "Delete") {
			console.log("del"); // Mby todo
		}
		canDelete = true;
		wordCount();
	}

	function createBlock(id) {
		for (let index = id; index < blocks.length; index++) {
			blocks[index].id++;
		}

		blocks.splice(id, 0, { id: id + 1, content: "", type: "p" });

		blocks = [...blocks];

		setTimeout(() => {
			let el = document.getElementById("block" + (id + 1));

			el.focus();
			el.addEventListener("paste", function (event) {
				event.preventDefault();
				document.execCommand(
					"inserttext",
					false,
					event.clipboardData.getData("text/plain")
				);
			});
			document.getElementById("options-menu" + (id + 1)).style.display =
				"none";
		}, 1);
	}

	function createBlockAbove() {
		for (let index = 0; index < blocks.length; index++) {
			blocks[index].id++;
		}

		blocks.splice(0, 0, { id: 1, content: "", type: "p" });

		blocks = [...blocks];

		setTimeout(() => {
			let el = document.getElementById("block1");

			el.focus();
			el.addEventListener("paste", function (event) {
				event.preventDefault();
				document.execCommand(
					"inserttext",
					false,
					event.clipboardData.getData("text/plain")
				);
			});
		}, 1);
	}

	function deleteBlock(id) {
		if (blocks.length != 1) {
			blocks.splice(id - 1, 1);
			blocks = [...blocks];

			for (let index = id - 1; index < blocks.length; index++) {
				blocks[index].id--;
			}
			setTimeout(() => {
				let el;
				if (id != 1) {
					el = document.getElementById("block" + (id - 1));
				} else {
					el = document.getElementById("block" + id);
				}
				el.focus();
				placeCaretAtEnd(el);
			}, 1);
		}
	}

	function turnInto(type, id) {
		let block = blocks.find((x) => x.id === id);

		block.type = type;
		document.getElementById("block" + id).focus();
		placeCaretAtEnd(document.getElementById("block" + id));

		blocks = [...blocks];
	}

	function wordCount() {
		let len = 0;
		let znaky = 0;
		for (let i = 0; i < blocks.length; i++) {
			len += blocks[i].content.split(" ").length;
			znaky += blocks[i].content.length;
			if (
				blocks[i].content.substr(blocks[i].content.length - 6) ==
				"&nbsp;"
			) {
				znaky -= 5;
			}
		}
		pocetSlov = len;
		pocetZnaku = znaky;
	}

	function getDate() {
		var today = new Date();
		var dd = String(today.getDate()).padStart(2, "0");
		var mm = String(today.getMonth() + 1).padStart(2, "0"); //January is 0!
		var yyyy = today.getFullYear();

		return dd + ". " + mm + ". " + yyyy;
	}

	function novyBod(i) {
		if (i == 0) {
			let bod = { nazev: "" };
			bodyAnkety = [...bodyAnkety, bod];

			i = bodyAnkety.length;
		} else {
			bodyAnkety.splice(i + 1, 0, { nazev: "" });
			bodyAnkety = bodyAnkety;
			i += 2;
		}

		setTimeout(() => {
			let el = document.getElementById("bod" + i);

			el.focus();
		}, 1);
	}

	function deleteBod(i) {
		if (bodyAnkety.length > 2) {
			bodyAnkety.splice(i - 1, 1);
			bodyAnkety = bodyAnkety;

			setTimeout(() => {
				let el = document.getElementById("bod" + (i - 1));

				el.focus();
			}, 1);
		}
	}

	function sendData() {
		fetch("https://fotbalpropal.pythonanywhere.com/save_draft", {
			method: "POST",
			headers: {
				"content-type": "application/json",
			},
			body: JSON.stringify({
				id: id,
				titulek: titulek,
				podnadpis: podnadpis,
				urlObrazku: urlObrazku,
				blocks: blocks,
				autor: $prezdivka,
				anketa: anketa,
				bodyAnkety: bodyAnkety,
				nazevAnkety: nazevAnkety,
				mainPopis: mainPopis,
				prezdivka: $prezdivka,
				cookie: $cookie,
				selectedStitek: selectedMainStitek.id,
				zdrojMainImage: zdrojMainImage,
			}),
		})
			.then((response) => {
				if (response.status == 200) {
					sendResponse = "Tvůj draft byl úspěšně uložen.";
					document.getElementById("success").style.display = "flex";
					setTimeout(() => {
						document.getElementById("success").style.display =
							"none";
					}, 6000);
				} else {
					sendResponse = "Tvůj draft nebyl uložen, zkus to znovu.";
					document.getElementById("error").style.display = "flex";
				}
			})
			.catch((err) => {
				console.log(err);
			});
	}

	function sendDataAndKontrola() {
		fetch(
			"https://fotbalpropal.pythonanywhere.com/save_draft_and_potvrdit",
			{
				method: "POST",
				headers: {
					"content-type": "application/json",
				},
				body: JSON.stringify({
					id: id,
					titulek: titulek,
					podnadpis: podnadpis,
					urlObrazku: urlObrazku,
					blocks: blocks,
					autor: $prezdivka,
					anketa: anketa,
					bodyAnkety: bodyAnkety,
					nazevAnkety: nazevAnkety,
					mainPopis: mainPopis,
					prezdivka: $prezdivka,
					cookie: $cookie,
					selectedStitek: selectedMainStitek.id,
					zdrojMainImage: zdrojMainImage,
				}),
			}
		)
			.then((response) => {
				if (response.status == 200) {
					sendResponse = "Tvůj draft byl úspěšně uložen.";
					document.getElementById("success").style.display = "flex";
					setTimeout(() => {
						document.getElementById("success").style.display =
							"none";
					}, 6000);
					navigate("/drafts");
				} else {
					sendResponse = "Tvůj draft nebyl uložen, zkus to znovu.";
					document.getElementById("error").style.display = "flex";
				}
			})
			.catch((err) => {
				console.log(err);
			});
	}

	let charCount = 0;
	let CHARBEFORESAVE = 25;
	function autoSave() {
		fetch("https://fotbalpropal.pythonanywhere.com/save_draft", {
			method: "POST",
			headers: {
				"content-type": "application/json",
			},
			body: JSON.stringify({
				id: id,
				titulek: titulek,
				podnadpis: podnadpis,
				urlObrazku: urlObrazku,
				blocks: blocks,
				autor: $prezdivka,
				anketa: anketa,
				bodyAnkety: bodyAnkety,
				nazevAnkety: nazevAnkety,
				mainPopis: mainPopis,
				prezdivka: $prezdivka,
				cookie: $cookie,
				selectedStitek: selectedMainStitek.id,
				zdrojMainImage: zdrojMainImage,
			}),
		});
	}
</script>

<div id="wrapper">
	<div id="flex-container">
		<aside>
			<Link to="/drafts"><span id="drafty-link">← Drafty</span></Link>
			<div id="tutorial">
				<p># nebo /h1 - Hlavní podnadpis</p>
				<p>## nebo /h2 - Sekundární podnadpis</p>
				<p>### nebo /h3 - Terciární podnadpis</p>
				<p>* nebo /o - Odrážka</p>
				<p>| nebo /c - Citace</p>
				<p>&gt; nebo /z - Zvýraznění</p>
				<p>Kurzíva - Ctrl + i (nebo &text/&)</p>
				<p>Tučně - Ctrl + b (nebo @text/@)</p>
				<p>Podtržení - Ctrl + u</p>
				<p>/img - Obrázek</p>
				<p>/tweet - Tweet (bez <b>&lt;script&gt;</b>)</p>
				<p>/ytb - Video (youtu.be/<b><u>aKKQdn26QJc</u></b>)</p>
			</div>
		</aside>
		<section>
			<div id="stitky-div">
				<div class="wrapper">
					<select
						bind:value={selectedMainStitek}
						style={"border: 2px solid " +
							selectedMainStitek?.color +
							"; color: " +
							selectedMainStitek?.color}
						name="hlavniStitek"
						id="hlavni-stitek"
						onfocus="this.size=8;"
						onblur="this.size=1;"
						onchange="this.size=1; this.blur();"
					>
						{#each stitky as stitek}
							<option
								value={stitek}
								style={"color: #333;"}
								selected={true &&
									selectedMainStitek.nazev == stitek.nazev}
							>
								{stitek.nazev}
							</option>
						{/each}
					</select>
				</div>
			</div>
			<h1
				contenteditable="true"
				on:focus|once={() => {
					if (titulek == "Zadej titulek") {
						titulek = "";
						first = false;
					}
				}}
				on:keydown={(e) => {
					if (e.key == "Enter") {
						e.preventDefault();
						let el = document.getElementById("podnadpis");
						el.focus();
					}
					if (
						(e.key == "b" || e.key == "u" || e.key == "i") &&
						e.ctrlKey
					) {
						e.preventDefault();
					}
				}}
				bind:innerHTML={titulek}
				class={first ? "first editor" : "second editor"}
				id="nadpish1"
			>
				{titulek}
			</h1>

			<div class="clanek-info">
				<span id="autor-span">{autor}</span>
				<!-- <select name="autor" id="autor" bind:value={autorClanku}>
					{#each autors as autor}
						{#if autorClanku.id == autor.id}
							<option value={autor} selected>
								{autor.jmeno}
							</option>
						{:else}
							<option value={autor}>{autor.jmeno}</option>
						{/if}
					{/each}
				</select> -->
				<span class="clanek-datum">{getDate()}</span>
			</div>
			<hr id="top-hr" />

			<p
				contenteditable="true"
				on:focus|once={() => {
					if (podnadpis == "Podnadpis...") {
						podnadpis = "";
						firstPodnadpis = false;
					}
				}}
				on:keydown={(e) => {
					if (e.key == "Enter") {
						e.preventDefault();
						let el = document.getElementById("main-image");
						el.focus();
					}
					if (
						(e.key == "b" || e.key == "u" || e.key == "i") &&
						e.ctrlKey
					) {
						e.preventDefault();
					}
				}}
				bind:innerHTML={podnadpis}
				class={firstPodnadpis
					? "first podnadpis editor"
					: "second podnadpis editor"}
				id="podnadpis"
			>
				{podnadpis}
			</p>

			<div
				class="image"
				contenteditable="true"
				id="main-image"
				bind:innerHTML={urlObrazku}
				on:focus|once={() => {
					if (urlObrazku == "Zadej URL Obrázku...") {
						urlObrazku = "";
					}
				}}
				on:keydown={(e) => {
					if (e.key == "Enter") {
						e.preventDefault();
						let el = document.getElementById("block1");
						el.focus();
					}
				}}
			>
				{urlObrazku}
			</div>
			{#if urlObrazku != "Zadej URL Obrázku..." && urlObrazku.startsWith("http")}
				<div id="zdroj-main">
					Zdroj:&nbsp;<input
						type="text"
						bind:value={zdrojMainImage}
					/>
				</div>
				<img
					src={urlObrazku}
					alt="Titulní obrázek"
					id="titulni-obrazek"
				/>
			{/if}
			<div
				id="create-first-block"
				on:click={() => {
					createBlock(0);
				}}
			/>

			{#each blocks as block}
				{#if block.type == "obrazek"}
					<div
						contenteditable="true"
						bind:innerHTML={block.url}
						on:blur={() => {
							focusedEl = 0;
						}}
						on:focus={() => {
							focusedEl = block.id;
						}}
						on:focus|once={() => {
							if (
								block.url == "Zadej URL obrázku..." ||
								block.url == "undefined"
							) {
								block.url = "";
							}
						}}
						on:keydown={(e) => keyDown(e, block.id)}
						id={"block" + block.id}
						class="image"
					>
						{block.url}
					</div>
					<div class="zdroj">
						Zdroj:&nbsp;<input
							type="text"
							bind:value={block.zdroj}
						/>
					</div>
					{#if block.url != "Zadej URL Obrázku..."}
						<img
							src={block.url}
							alt="Titulní obrázek"
							id="titulni-obrazek"
						/>
					{/if}
				{:else if block.type == "twitter"}
					<div
						contenteditable="true"
						bind:innerHTML={block.tweet}
						on:blur={() => {
							focusedEl = 0;
						}}
						on:focus={() => {
							focusedEl = block.id;
						}}
						on:focus|once={() => {
							if (
								block.tweet == "Zadej Tweet..." ||
								block.tweet == "undefined"
							) {
								block.tweet = "";
							}
						}}
						on:keydown={(e) => keyDown(e, block.id)}
						id={"block" + block.id}
						class="image twitter-blue"
					>
						{block.tweet}
					</div>
				{:else if block.type == "youtube"}
					<div
						contenteditable="true"
						bind:innerHTML={block.url}
						on:blur={() => {
							focusedEl = 0;
						}}
						on:focus={() => {
							focusedEl = block.id;
						}}
						on:focus|once={() => {
							if (
								block.url == "Zadej ID videa..." ||
								block.url == "undefined"
							) {
								block.url = "";
							}
						}}
						on:keydown={(e) => keyDown(e, block.id)}
						id={"block" + block.id}
						class="image youtube-red"
					>
						{block.url}
					</div>
					{#if block.url != "Zadej ID videa..." && block.url != ""}
						<iframe
							title="video"
							src={"https://www.youtube-nocookie.com/embed/" +
								block.url}
							frameborder="0"
							allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture"
							allowfullscreen
						/>
					{/if}
				{:else}
					<div
						class="options-container"
						on:mouseover={() =>
							(document.getElementById(
								"options-p" + block.id
							).innerHTML = "&nbsp;=")}
						on:mouseleave={() => {
							if (
								document.getElementById(
									"options-menu" + block.id
								).style.display == "none"
							) {
								document.getElementById(
									"options-p" + block.id
								).innerHTML = "&nbsp;";
							}
						}}
						use:clickOutside={() => {
							document.getElementById(
								"options-p" + block.id
							).innerHTML = "&nbsp;";
							document.getElementById(
								"options-menu" + block.id
							).style.display = "none";
						}}
					>
						<div
							contenteditable="true"
							bind:innerHTML={block.content}
							on:click={(e) => {
								updateIndex(
									e,
									document.getElementById("block" + block.id)
								);
							}}
							on:keydown={(e) => {
								if (e.key == "Enter") {
									e.preventDefault();
								} else if (
									e.key == "Backspace" &&
									block.content.length >= 1 &&
									cursorPos != 0
								) {
									canDelete = false;
								} else if (
									(e.key == "b" ||
										e.key == "u" ||
										e.key == "i") &&
									e.ctrlKey
								) {
									// e.preventDefault();
								} else if (e.metaKey && e.key == "b") {
									document.execCommand("Bold", false, null);
								} else if (e.metaKey && e.key == "u") {
									document.execCommand(
										"Underline",
										false,
										null
									);
								} else if (e.metaKey && e.key == "i") {
									document.execCommand("Italic", false, null);
								}
								if (charCount < CHARBEFORESAVE) {
									charCount++;
								} else {
									autoSave();
									charCount = 0;
								}
							}}
							on:keyup={(e) => {
								keyDown(e, block.id);
							}}
							on:input={(e) => blockChange(e, block.id)}
							on:blur={() => {
								focusedEl = 0;
							}}
							on:focus|once={() => {
								if (block.content == "Tělo článku...") {
									block.content = "";
								}
							}}
							on:focus={() => {
								focusedEl = block.id;
							}}
							class={block.type + " editor block"}
							id={"block" + block.id}
						>
							{block.content}
						</div>

						<div
							class="options"
							id={"options" + block.id}
							on:click={() => {
								if (
									document.getElementById(
										"options-menu" + block.id
									).style.display == "inline"
								) {
									document.getElementById(
										"options-menu" + block.id
									).style.display = "none";
								} else {
									document.getElementById(
										"options-menu" + block.id
									).style.display = "inline";
									document.getElementById(
										"options-p" + block.id
									).innerHTML = "&nbsp;=";
								}
							}}
						>
							<p id={"options-p" + block.id}>&nbsp;</p>
							<div
								class="options-menu"
								style="display: none;"
								id={"options-menu" + block.id}
							>
								<div
									on:click={() => {
										turnInto("p", block.id);
									}}
								>
									Text
								</div>
								<div
									on:click={() => {
										turnInto("h1", block.id);
									}}
								>
									Nadpis 1
								</div>
								<div
									on:click={() => {
										turnInto("h2", block.id);
									}}
								>
									Nadpis 2
								</div>
								<div
									on:click={() => {
										turnInto("h3", block.id);
									}}
								>
									Nadpis 3
								</div>
								<div
									on:click={() => {
										turnInto("odrazka", block.id);
									}}
								>
									Odrážka
								</div>
								<div
									on:click={() => {
										turnInto("citace", block.id);
									}}
								>
									Citace
								</div>
								<div
									on:click={() => {
										turnInto("callout", block.id);
									}}
								>
									Callout
								</div>
								{#if block.content == "" || block.content == "Tělo článku..."}
									<div
										on:click={() => {
											turnInto("obrazek", block.id);
										}}
									>
										Obrázek
									</div>
								{/if}
								{#if block.content == "" || block.content == "Tělo článku..."}
									<div
										on:click={() => {
											turnInto("twitter", block.id);
										}}
									>
										Tweet
									</div>
								{/if}
								{#if block.content == "" || block.content == "Tělo článku..."}
									<div
										on:click={() => {
											turnInto("youtube", block.id);
										}}
									>
										Video
									</div>
								{/if}
								<div
									on:click={() => {
										deleteBlock(block.id);
									}}
								>
									Smazat
								</div>
							</div>
						</div>
					</div>
					{#if block.type != "odrazka" && block.id != blocks.length}
						<br />
					{/if}
				{/if}
			{/each}
			<div
				id="new-block"
				on:click={() => {
					createBlock(blocks[blocks.length - 1].id);
				}}
			/>
			<hr />
			<div id="osnova-flex">
				<label class="container">
					Vytvořit anketu
					<input
						type="checkbox"
						bind:checked={anketa}
						on:click={() => {
							setTimeout(() => {
								if (anketa) {
									document
										.getElementById("nazev-ankety")
										.addEventListener(
											"paste",
											function (event) {
												event.preventDefault();
												document.execCommand(
													"inserttext",
													false,
													event.clipboardData.getData(
														"text/plain"
													)
												);
											}
										);
								}
							}, 1);
						}}
					/>
					<span class="checkmark" />
				</label>
				{#if pocetSlov == 1}
					<span>{pocetSlov} slovo<br />{pocetZnaku} znaků</span>
				{:else if pocetSlov == 2 || pocetSlov == 3 || pocetSlov == 4}
					<span>{pocetSlov} slova<br />{pocetZnaku} znaků</span>
				{:else}
					<span style="text-align:right;"
						>{pocetSlov}
						slov<br />{pocetZnaku}
						znaků</span
					>
				{/if}
			</div>

			<!-- <label class="container">
				Zobrazovat osnovu
				<input type="checkbox" bind:checked={osnova} />
				<span class="checkmark" />
            </label> -->
			{#if anketa}
				<div>
					<h4
						id="nazev-ankety"
						bind:innerHTML={nazevAnkety}
						contenteditable
					/>
					<div id="anketa-body-wrapper">
						{#each bodyAnkety as bod, i}
							<input
								class="bod-ankety"
								id={"bod" + (i + 1)}
								contenteditable
								bind:value={bod.nazev}
								on:keydown={(e) => {
									if (
										e.key == "Backspace" &&
										bod.nazev == ""
									) {
										console.log(bodyAnkety);
										console.log(bod.nazev);
										deleteBod(i + 1);
									}
									if (e.key == "Enter") {
										novyBod(i);
									}
								}}
								on:click={() => {
									if (
										bod.nazev == "První bod" ||
										bod.nazev == "Druhý bod"
									) {
										bod.nazev = "";
									}
								}}
							/>
						{/each}
						<input
							class="bod-ankety"
							value="+ Nový bod"
							on:keydown={(e) => {
								e.preventDefault();
							}}
							on:click={() => {
								novyBod(0);
							}}
							on:focus={() => {
								novyBod(0);
							}}
						/>
					</div>
				</div>
			{/if}

			{#if differentSite == false}
				<p id="textarea-p">
					Napiš krátký chytlavý popis článku, kdyby se stal hlavním
					článkem:
				</p>
			{:else}
				<p id="textarea-p">Zadej URL původního článku:</p>
			{/if}
			<textarea bind:value={mainPopis} id="main-popis-textarea" />

			<br />

			<div id="flex-publikovat">
				<div />
				<button
					on:click={() => {
						sendData();
					}}
					id="ulozit">Uložit</button
				>
				<button
					id="publikovat"
					on:click={() => {
						sendDataAndKontrola();
					}}>Odeslat ke kontrole</button
				>
			</div>
		</section>
		<aside />
	</div>
	<div
		id="success"
		on:click={() => {
			document.getElementById("success").style.display = "none";
		}}
	>
		{sendResponse}
		<span
			on:click={() => {
				document.getElementById("success").style.display = "none";
			}}>x</span
		>
	</div>
	<div
		id="error"
		on:click={() => {
			document.getElementById("error").style.display = "none";
		}}
	>
		{sendResponse}
		<span
			on:click={() => {
				document.getElementById("error").style.display = "none";
			}}>x</span
		>
	</div>
</div>

<style>
	[contenteditable] {
		-webkit-user-select: text;
		user-select: text;
	}
	#zdroj-main {
		margin-top: 5px;
	}
	#create-first-block {
		height: 20px;
	}
	.zdroj {
		margin-bottom: 10px;
		margin-top: -10px;
	}
	#autor-span {
		color: #ffa800;
		font-size: 16px;
		font-weight: 700;
	}
	iframe {
		margin-left: 50%;
		transform: translateX(-50%);

		width: 50%;
		height: 250px;
	}
	#textarea-p {
		margin-bottom: 0px;
	}
	textarea {
		width: 100%;
		font-family: "Titillium Web", sans-serif;
		font-size: 18px;
		font-weight: 600;

		margin-top: 10px;
		margin-bottom: 10px;
	}
	#wrapper {
		padding-left: 40px;
		padding-right: 40px;
	}
	#flex-container {
		display: flex;
		padding-bottom: 50px;
	}
	h1 {
		margin-top: 0;
	}
	section {
		width: 55%;
		margin: 0 auto;

		margin-top: 70px;

		position: relative;
	}
	aside {
		margin-top: 40px;
		width: 22.5%;

		color: #b7b7b7;
	}
	#drafty-link {
		font-weight: 600;
		font-size: 22px;
		color: #ff8a00;
		text-decoration: underline;
	}
	#tutorial {
		margin-top: 20px;
	}
	aside p {
		margin: 0;
		font-size: 1.1vw;
		font-weight: 600;
	}
	.first {
		color: #b7b7b7;
	}
	.second {
		color: #333333;
	}
	h1 {
		font-size: 2.6vw;
		outline: none;
		box-shadow: none;
	}
	.podnadpis {
		font-weight: 600;
		font-size: calc(0.5vw + 11px);
	}
	.image {
		width: 100%;
		min-height: 50px;

		display: flex;
		justify-content: center;
		flex-direction: column;
		align-content: center;

		background-color: transparent;
		border: 1px solid #8d8d8d;

		border-radius: 5px;

		margin-top: 10px;
		margin-bottom: 20px;

		box-sizing: border-box;
		padding-left: 20px;
		padding-right: 20px;
		color: #8d8d8d;
		font-weight: 600;
	}
	#main-image {
		margin-bottom: 0;
	}

	img {
		width: 100%;
		height: 30vw;
		margin-bottom: 40px;
		object-fit: cover;
	}

	p,
	div {
		font-size: calc(0.5vw + 11px);
		outline: none;
		box-shadow: none;
		text-align: justify;
	}

	.block {
		transition: 0.2s;
		border-radius: 3px;
		padding-left: 5px;
		padding-right: 5px;
		padding-top: 2px;
		padding-bottom: 2px;
	}
	.block:hover {
		background-color: #f5f5f5;
	}
	.block:focus {
		background-color: #e1e1e1;
	}

	.h1 {
		font-size: 30px;
		font-weight: 700;
	}
	.h2 {
		font-size: 28px;
		font-weight: 600;
	}
	.h3 {
		font-size: 24px;
		font-weight: 600;
	}
	.callout {
		background-color: #ff8a00;
		color: #ffffff;

		padding: 10px;
		padding-left: 15px;
		padding-right: 15px;
	}
	.callout:hover {
		background-color: #ff8a00;
	}
	.callout:focus {
		background-color: #ff8a00;
	}
	.citace {
		padding: 0px;
		padding-left: 15px;
		font-style: italic;
		position: relative;
	}
	.citace::before {
		content: "";
		position: absolute;
		left: 0px;

		width: 5px;
		height: 105%;
		background-color: #ff8a00;
	}
	.odrazka {
		padding-left: 30px;
		position: relative;
	}
	.odrazka::before {
		content: "";
		position: absolute;
		left: 15px;
		top: 50%;

		width: 5px;
		height: 5px;
		border-radius: 50%;
		background-color: #333333;
	}

	.container {
		display: block;
		position: relative;
		padding-left: 40px;
		padding-top: 0px;
		margin-bottom: 0px;
		cursor: pointer;
		font-size: 24px;
		font-weight: 600;
		-webkit-user-select: none;
		-moz-user-select: none;
		-ms-user-select: none;
		user-select: none;
	}

	.container input {
		position: absolute;
		opacity: 0;
		cursor: pointer;
		height: 0;
		width: 0;
	}

	.checkmark {
		position: absolute;
		top: 5px;
		left: 5px;
		height: 25px;
		width: 25px;
		background-color: transparent;
		border: 1px solid #ccc;
	}

	.container:hover input ~ .checkmark {
		background-color: #ccc;
	}

	.container input:checked ~ .checkmark {
		background-color: #ff8a00;
		border: 1px solid #ff8a00;
	}

	#osnova-flex {
		display: flex;
		justify-content: space-between;
		align-items: flex-start;
	}
	#osnova-flex span {
		font-size: 16px;
	}

	#ulozit {
		margin-right: 20px;
		border: 0;
		background-color: transparent;
		color: #ff8a00;
		font-size: 20px;
		font-weight: 600;
		cursor: pointer;
		transition: 0.1s;
	}
	#ulozit:hover {
		color: #333333;
	}

	#flex-publikovat div {
		flex-grow: 100;
	}
	#flex-publikovat {
		display: flex;
		justify-content: space-between;
		align-items: center;
	}

	#publikovat {
		background-color: #ff8a00;
		border: none;
		color: white;
		padding: 15px 32px;
		text-align: center;
		text-decoration: none;
		display: inline-block;
		font-size: 24px;
		font-weight: 600;

		cursor: pointer;
		transition: 0.1s;
	}
	#publikovat:hover {
		background-color: #ffffff;
		color: #ff8a00;
	}

	hr {
		margin-top: 0px;
	}

	@media only screen and (max-width: 680px) {
		aside {
			display: none;
		}
		section {
			width: 100%;
			margin-top: 25px;
		}
		h1 {
			font-size: 24px;
			font-weight: 600;
		}
		.podnadpis {
			margin-top: 20px;
		}
		.citace::before {
			width: 3px;
		}
	}

	.options-container {
		position: relative;
	}

	.options {
		position: absolute;
		left: -30px;
		top: 0px;

		line-height: 90%;
		margin: 0;
		padding: 0;
		height: 100%;
		width: 30px;

		cursor: pointer;
		font-size: 30px;
	}
	.options p {
		font-size: 30px;
		height: 100%;
		margin: 0;
		margin-top: -15px;
		padding: 0;
		position: absolute;
		top: 50%;
	}

	.options-menu {
		display: none;
		position: absolute;
		right: 100%;
		top: 0;

		padding: 0;
		margin: 0;
		height: auto;
		width: 150px;

		background-color: #ffffff;
		border: 1px solid #e0e0e0;
		border-radius: 3px;

		cursor: auto;

		box-shadow: 0px 0px 8px #cecece;
	}

	.options-menu div {
		font-size: 18px;

		cursor: pointer;
		transition: 0.1s;
		padding-left: 8px;
	}
	.options-menu div:hover {
		background-color: #e0e0e0;
	}

	#success {
		display: none;
		justify-content: space-between;
		position: fixed;
		bottom: 0;
		left: 0;
		width: 100%;
		height: 30px;
		background-color: #1aad3a;
		color: #ffffff;
		padding-left: 10px;
		cursor: pointer;
	}

	#success span {
		cursor: pointer;
		margin-right: 20px;
		font-size: 20px;
	}

	#error {
		display: none;
		justify-content: space-between;
		position: fixed;
		bottom: 0;
		left: 0;
		width: 100%;
		height: 30px;
		background-color: #c81515;
		color: #ffffff;
		padding-left: 10px;
		cursor: pointer;
	}

	#error span {
		cursor: pointer;
		margin-right: 20px;
		font-size: 20px;
	}

	#new-block {
		height: 50px;
		width: 100%;
	}

	.clanek-info {
		display: flex;
		justify-content: space-between;
		position: relative;
		margin-bottom: 10px;
	}

	#autor {
		border: 0;
		font-weight: 600;
		font-size: 16px;
		color: #ff8a00;
		cursor: pointer;
	}

	.clanek-podnadpis {
		text-align: justify;
		font-weight: 600;
		font-size: calc(0.5vw + 11px);
		margin-top: 40px;
	}
	.clanek-obrazek {
		width: 100%;
		height: 10px;

		margin-top: 15px;

		object-fit: cover;
	}
	.clanek-datum {
		font-size: 16px;
	}

	#top-hr {
		margin-bottom: 25px;
	}

	#hlavni-stitek {
		font-weight: 600;
		font-size: 14px;
	}

	#stitky-div {
		display: flex;
		align-items: flex-start;
	}
	#stitky-div input {
		margin-left: 10px;
		height: 16px;

		border: 2px solid #bfbfbf;
		color: #bfbfbf;
		font-size: 14px;
		font-weight: 600;
	}
	.wrapper {
		font-size: 0;
	}

	#nazev-ankety {
		text-align: center;
	}

	#anketa-body-wrapper {
		display: flex;
		flex-direction: column;
		width: 100%;
		align-items: center;
	}

	.bod-ankety {
		border: 1px solid #333333;
		color: #333333;

		border-radius: 5px;
		width: 60%;
		height: 35px;
		padding-left: 15px;

		margin-bottom: 15px;
		font-size: 18px;

		font-weight: 400;
	}
	.twitter-blue {
		background-color: #1da1f2;
		border-color: #1da1f2;
		color: #ffffff;
	}
	.youtube-red {
		background-color: #ff0000;
		border-color: #ff0000;
		color: #ffffff;
	}
	#titulni-obrazek {
		margin-top: 10px;
		margin-bottom: 0;
	}
</style>
