<script>
	import Nadpis from "./Nadpis.svelte";
	import { onMount } from "svelte";
	import { navigate } from "svelte-routing";

	onMount(() => {
		getInfo();

		document.getElementById("date").valueAsDate = new Date();
	});

	let stitky = [];
	let kanaly = [];
	let kluby = [];

	async function getInfo() {
		const res = await fetch(
			"https://fotbalpropal.pythonanywhere.com/get_zapasy_info"
		);
		const json = await res.json();
		stitky = json.stitky;
		kanaly = json.kanaly;
		kluby = json.kluby;

		selectedStitek = stitky[0];
		selectedKanal = kanaly[0];
		domaci = kluby[2];
		hoste = kluby[2];
	}

	let date;
	let time = "18:00";
	let domaciSkore = "0";
	let hosteSkore = "0";
	$: selectedStitek = "";
	$: selectedKanal = "";
	$: domaci = "";
	$: hoste = "";

	function changeTeam() {
		for (let i = 0; i < kluby.length; i++) {
			if (kluby[i].stitek == selectedStitek.nazev) {
				if (domaci.stitek != selectedStitek.nazev) {
					domaci = kluby[i];
				}
				if (hoste.stitek != selectedStitek.nazev) {
					hoste = kluby[i];
				}
				break;
			}
		}
	}

	function addZapas() {
		fetch("https://fotbalpropal.pythonanywhere.com/add_zapas", {
			method: "POST",
			headers: {
				"content-type": "application/json",
			},
			body: JSON.stringify({
				datum: date,
				cas: time,
				stitek: selectedStitek,
				kanal: selectedKanal,
				domaci: domaci,
				hoste: hoste,
				tipRedakce: domaciSkore + ":" + hosteSkore,
			}),
		})
			.then((response) => {
				navigate("/");
			})
			.catch((err) => {
				console.log(err);
			});
	}
</script>

<Nadpis text="Nový zápas" />
<section>
	<div id="stitek-div">
		{#if selectedStitek != ""}
			<img src={selectedStitek.logo} alt="" id="stitek" />
		{/if}
		<select
			bind:value={selectedStitek}
			on:click={() => {
				changeTeam();
			}}
		>
			{#each stitky as stitek}
				<option value={stitek}>{stitek.nazev}</option>
			{/each}
		</select>
	</div>
	<div id="tymy-div">
		<div class="tym-div">
			{#if domaci != ""}
				<img
					src={"https://ik.imagekit.io/velkadomu/tr:h-400,w-400" +
						domaci.logo}
					alt=""
					id="club-image"
				/>
			{/if}
			<select bind:value={domaci}>
				{#each kluby as klub}
					{#if klub.stitek == selectedStitek.nazev || selectedStitek.nazev == "Evropská liga" || selectedStitek.nazev == "Liga mistrů"}
						<option value={klub}>{klub.nazev}</option>
					{/if}
				{/each}
			</select>
			<input type="number" min="0" max="9" bind:value={domaciSkore} />
		</div>
		<div class="tym-div">
			{#if hoste != ""}
				<img
					src={"https://ik.imagekit.io/velkadomu/tr:h-400,w-400" +
						hoste.logo}
					alt=""
					id="club-image"
				/>
			{/if}
			<select bind:value={hoste}>
				{#each kluby as klub}
					{#if klub.stitek == selectedStitek.nazev || selectedStitek.nazev == "Evropská liga" || selectedStitek.nazev == "Liga mistrů"}
						<option value={klub}>{klub.nazev}</option>
					{/if}
				{/each}
			</select>
			<input type="number" min="0" max="9" bind:value={hosteSkore} />
		</div>
	</div>
	<div id="date-div">
		<input type="time" id="time" name="time" bind:value={time} />
		<input type="date" id="date" name="date" bind:value={date} />
	</div>

	<select id="kanal" bind:value={selectedKanal}>
		{#each kanaly as kanal}
			<option value={kanal}>{kanal.nazev}</option>
		{/each}
	</select>

	<button id="pridat-zapas" on:click={addZapas}>Přidat zápas</button>
</section>

<style>
	#pridat-zapas {
		float: right;
		background-color: #ff8a00;
		color: #ffffff;
		border: 0;
		width: 175px;
		height: 40px;

		font-size: 24px;
		font-weight: 700;

		cursor: pointer;
		transition: 0.15s;
	}
	#pridat-zapas:hover {
		background-color: #ffffff;
		color: #ff8a00;
	}
	#club-image {
		height: 250px;
		width: auto;
	}
	section {
		padding: 0px calc((100% - 45rem) / 2) 0px calc((100% - 45rem) / 2);
	}
	#stitek-div {
		display: flex;
		flex-direction: column;
		align-items: center;
	}
	#stitek-div select {
		font-size: 18px;
		font-weight: 600;
		color: #333333;
		min-width: 100px;
		text-align-last: center;
	}
	#stitek {
		width: 80px;
	}
	#tymy-div {
		display: flex;
		justify-content: space-between;
	}
	.tym-div {
		display: flex;
		flex-direction: column;
		width: 50%;
		align-items: center;
	}
	.tym-div select {
		font-size: 35px;
		font-weight: 700;
		min-width: 80%;
		padding-top: 5px;
		padding-bottom: 5px;
		text-align-last: center;
		color: #333333;
		border-radius: 5px;
	}
	.tym-div input {
		width: 60px;
		font-size: 50px;
		font-weight: 700;
		color: #333333;
		text-align: center;
		padding-top: 15px;
		padding-left: 15px;
		padding-bottom: 15px;
	}

	img {
		width: 200px;
	}

	#date-div {
		display: flex;
		flex-direction: column;
		align-items: center;
	}
	#date-div input {
		font-size: 18px;
		font-weight: 700;
	}

	input {
		margin-top: 10px;
		border-radius: 5px;
		font-family: "Titillium Web", sans-serif;
		border: 1px solid #333333;
	}

	#kanal {
		font-size: 16px;
		font-weight: 500;
	}
</style>
