<script>
	import Nadpis from "../Nadpis.svelte";
	import { onMount } from "svelte";
	import { navigate } from "svelte-routing";
	import { cookie, prezdivka } from "../../store";

	onMount(() => {
		getClanky();
	});

	async function getClanky() {
		const res = await fetch(
			"https://velkadomu.pythonanywhere.com/titulni-clanek",
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
		clanky = json.clanky;
		id = json.id;
	}

	let clanky;
	let id = 0;

	function setHlavniClanek() {
		fetch("https://velkadomu.pythonanywhere.com/set-hlavni-clanek", {
			method: "POST",
			headers: {
				"content-type": "application/json",
			},
			body: JSON.stringify({
				id: id,
				cookie: $cookie,
				prezdivka: $prezdivka,
			}),
		})
			.then(() => {
				navigate("/");
			})
			.catch((err) => {
				console.log(err);
			});
	}
</script>

<div id="main">
	<div id="wrapper">
		<Nadpis text="Titulní článek" />
		<section>
			{#if clanky}
				{#each clanky as clanek}
					<div
						id={clanek.id == id && "chosen"}
						class="border"
						on:click={() => (id = clanek.id)}
					>
						<div id="green">
							<img
								src={"https://ik.imagekit.io/velkadomu/tr:h-260,w-420" +
									clanek.obrazek}
								alt=""
							/>
							<div class="checkmark" />
						</div>
						<h2>{clanek.titulek}</h2>
					</div>
				{/each}
			{:else}
				Abys mohl vybírat titulní článek, musíš mít status superadmina.
			{/if}
		</section>
	</div>
	<div id="button" on:click={setHlavniClanek}>Uložit</div>
</div>

<style>
	#main {
		padding: 0px calc((100% - 85rem) / 2) 80px calc((100% - 85rem) / 2);
	}
	#wrapper {
		margin-top: 80px;
	}
	section {
		margin-top: 50px;
		display: flex;
		justify-content: space-between;
		flex-wrap: wrap;
	}
	section div {
		width: 30%;
		height: 100%;

		position: relative;

		cursor: pointer;
	}
	section div img {
		border: 10px solid #ffffff;
	}
	h2 {
		margin-top: 5px;
		margin-bottom: 40px;
	}
	img {
		width: 100%;
	}
	#chosen img {
		border: 10px solid #20e500;
	}
	section #green {
		width: 100%;
		height: 100%;
	}
	#chosen #green::before {
		content: "";
		display: block;
		position: absolute;
		margin: auto;
		top: 0;
		left: 0;
		bottom: 0;
		right: 0;

		width: 100px;
		height: 100px;
		border-radius: 50%;
		background-color: #20e500;
	}

	#chosen .checkmark {
		display: inline-block;
	}

	.checkmark {
		display: none;
		position: absolute;
		width: 15px;
		height: 30px;
		left: calc(50% - 4px);
		top: calc(50% - 7px);
		transform: translateY(-50%) translateX(-50%);
	}
	.checkmark:after {
		content: "";
		display: block;
		width: 15px;
		height: 30px;
		border: solid #ffffff;
		border-width: 0 10px 10px 0;
		transform: rotate(45deg);
	}

	#button {
		position: fixed;

		bottom: 20px;
		left: calc(50% - 50px);

		width: 100px;
		height: 40px;

		background-color: #20e500;
		text-align: center;
		color: #ffffff;
		font-weight: 700;
		font-size: 24px;
		line-height: 40px;

		cursor: pointer;
	}
</style>
