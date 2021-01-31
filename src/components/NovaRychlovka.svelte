<script>
	import { navigate } from "svelte-routing";

	import Nadpis from "./Nadpis.svelte";

	let nadpis = "";
	let text = "";

	function addRychlovka() {
		fetch("http://127.0.0.1:8000/nova_rychlovka", {
			method: "POST",
			headers: {
				"content-type": "application/json",
			},
			body: JSON.stringify({
				titulek: nadpis,
				body: text,
			}),
		})
			.then((response) => {
				if (response.status == 200) {
					navigate("/");
				}
			})
			.catch((err) => {
				console.log(err);
			});
	}
</script>

<section>
	<Nadpis text="Nová rychlovka" />
	<input type="text" bind:value={nadpis} placeholder="Nadpis" />
	<textarea bind:value={text} placeholder="Text" />

	<button
		on:click={() => {
			if (nadpis != "" && text != "") {
				addRychlovka();
			}
		}}>Publikovat</button
	>
</section>

<style>
	section {
		padding: 0px calc((100% - 60rem) / 2) 0px calc((100% - 60rem) / 2);
		margin-top: 50px;
		display: flex;
		flex-direction: column;
		position: relative;
	}

	input {
		border: 1px solid #9a9a9a;
		border-radius: 5px;

		font-family: "Titillium Web";
		font-weight: 600;
		font-size: 22px;

		padding: 10px;
	}

	textarea {
		margin-top: 20px;

		border: 1px solid #9a9a9a;
		border-radius: 5px;

		font-family: "Titillium Web";
		font-weight: 600;
		font-size: 16px;

		padding: 10px;
	}

	button {
		width: 100px;

		background-color: #ff8a00;
		color: #ffffff;
		font-weight: 500;
		font-size: 16px;

		border: 0;
		border-radius: 5px;
		padding: 10px 0;

		margin-left: calc(100% - 100px);
		margin-top: 20px;

		cursor: pointer;
		transition: 0.15s;
	}

	button:hover {
		background-color: #ffffff;
		color: #ff8a00;
	}
</style>
