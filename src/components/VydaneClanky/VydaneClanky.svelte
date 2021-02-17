<script>
	import { Link } from "svelte-routing";
	import { onMount } from "svelte";
	import { cookie, prezdivka } from "../../store";

	onMount(() => {
		getDrafts();
	});

	let drafts;
	let next_id = 0;

	async function getDrafts() {
		const res = await fetch(
			"https://fotbalpropal.pythonanywhere.com/vydane_clanky",
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
		let data = await res.json();
		drafts = data.drafts;
	}

	/* async function deleteDraft(id) {
		const res = await fetch(
			"https://fotbalpropal.pythonanywhere.com/delete_draft/" + id,
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
		getDrafts();
	} */
</script>

<div id="wrapper">
	<section>
		<h2>Vydané články</h2>
		<div style="overflow-x:auto;">
			<table>
				<tr>
					<th>Titulek</th>
					<th>Autor</th>
					<th>Uloženo</th>
					<th>Zhlédnuto</th>
					<th>Přesměrování</th>
				</tr>

				{#if drafts}
					{#each drafts as draft}
						<tr>
							<td>
								<span>
									<Link to={"/clanek-edit/" + draft.id}>
										<span>{draft.titulek}</span>
									</Link>
								</span>
							</td>
							<td>{draft.autor}</td>
							<td>{draft.time_saved}</td>
							<td>{draft.viewers}</td>
							<td>{draft.redirect}</td>
						</tr>
					{/each}
				{/if}
			</table>
		</div>
	</section>
</div>

<style>
	#wrapper {
		padding-left: 40px;
		padding-right: 40px;
	}
	h2 {
		font-size: 34px;
		font-weight: 600;
		color: #333;
		margin-bottom: 40px;
	}
	section {
		margin-left: auto;
		margin-right: auto;

		text-align: center;
		position: relative;
		width: 100%;
	}
	table {
		text-align: left;
		border-collapse: collapse;

		width: 100%;
		white-space: nowrap;

		border: 1px solid #e3e3e3;
	}
	th {
		color: #ff8a00;
		font-size: 18px;
		font-weight: 600;

		padding-bottom: 5px;
		padding-left: 10px;
		padding-top: 5px;
	}
	tr:first-child {
		border-bottom: 1px solid #e3e3e3;
	}
	tr:nth-child(2n) {
		background-color: #f4f4f4;
	}
	tr:nth-child(2n + 1) {
		background-color: #ffffff;
	}

	tr {
		font-size: 20px;

		color: #333;
	}
	td:not(:first-child) {
		padding-top: 7px;
		padding-bottom: 7px;
	}
	td {
		padding-left: 10px;
	}

	button {
		position: absolute;
		right: 0;
		bottom: -60px;

		background-color: #ff8a00;
		border: none;
		color: white;
		padding: 10px 21px;
		text-align: center;
		text-decoration: none;
		display: inline-block;
		font-size: 18px;
		font-weight: 600;

		cursor: pointer;
		transition: 0.1s;
	}

	button:hover {
		background-color: #ffffff;
		color: #ff8a00;
	}

	td:first-child span {
		margin: 0;
		display: block;
		width: 100%;
		transition: 0.1s;
	}

	td:first-child span:hover {
		color: #ff8a00;
	}

	th:last-child {
		text-align: right;
		padding-right: 20px;
	}
	td:last-child {
		text-align: right;
		padding-right: 65px;
	}
	td:nth-last-child(2) {
		padding-left: 45px;
	}
	svg {
		width: 20px;
		height: 20px;
		cursor: pointer;
	}

	@media only screen and (max-width: 680px) {
		#wrapper {
			padding: 0;
		}
	}
</style>
