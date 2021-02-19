<script>
	import { Link } from "svelte-routing";
	import { onMount } from "svelte";

	import Navigation from "../NewNavigation.svelte";
	import { cookie, prezdivka } from "../../store";

	onMount(() => {
		getDrafts();
	});

	let drafts;
	let next_id = 0;

	async function getDrafts() {
		const res = await fetch(
			"https://velkadomu.pythonanywhere.com/drafts_kontrola",
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
		drafts = await res.json();

		let objToArr = [];
		for (let i = 0; i < drafts.length; i++) {
			objToArr.push(drafts[i].id);
		}

		for (let i = 0; i <= objToArr.length; i++) {
			if (objToArr[i] > next_id) {
				next_id = objToArr[i];
			}
		}
		next_id++;
	}

	async function deleteDraft(id) {
		const res = await fetch(
			"https://velkadomu.pythonanywhere.com/delete_draft/" + id,
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
	}
</script>

<div id="wrapper">
	<section>
		<h2>Rozpracované články</h2>
		<table>
			<tr>
				<th>Titulek</th>
				<th>Autor</th>
				<th>Uloženo</th>
				<th>Smazat</th>
			</tr>

			{#if drafts}
				{#each drafts as draft}
					<tr>
						<td>
							<span>
								<Link to={"/drafts-kontrola/" + draft.id}>
									<span>{draft.titulek}</span>
								</Link>
							</span>
						</td>
						<td>{draft.autor}</td>
						<td>{draft.time_saved}</td>
						<td>
							<div on:click={deleteDraft(draft.id)}>
								<svg
									xmlns="http://www.w3.org/2000/svg"
									xmlns:xlink="http://www.w3.org/1999/xlink"
									version="1.1"
									id="Capa_1"
									x="0px"
									y="0px"
									width="408.483px"
									height="408.483px"
									viewBox="0 0 408.483 408.483"
									style="enable-background:new 0 0 408.483 408.483;"
									xml:space="preserve"
								>
									<g>
										<g>
											<path
												d="M87.748,388.784c0.461,11.01,9.521,19.699,20.539,19.699h191.911c11.018,0,20.078-8.689,20.539-19.699l13.705-289.316    H74.043L87.748,388.784z M247.655,171.329c0-4.61,3.738-8.349,8.35-8.349h13.355c4.609,0,8.35,3.738,8.35,8.349v165.293    c0,4.611-3.738,8.349-8.35,8.349h-13.355c-4.61,0-8.35-3.736-8.35-8.349V171.329z M189.216,171.329    c0-4.61,3.738-8.349,8.349-8.349h13.355c4.609,0,8.349,3.738,8.349,8.349v165.293c0,4.611-3.737,8.349-8.349,8.349h-13.355    c-4.61,0-8.349-3.736-8.349-8.349V171.329L189.216,171.329z M130.775,171.329c0-4.61,3.738-8.349,8.349-8.349h13.356    c4.61,0,8.349,3.738,8.349,8.349v165.293c0,4.611-3.738,8.349-8.349,8.349h-13.356c-4.61,0-8.349-3.736-8.349-8.349V171.329z"
											/>
											<path
												d="M343.567,21.043h-88.535V4.305c0-2.377-1.927-4.305-4.305-4.305h-92.971c-2.377,0-4.304,1.928-4.304,4.305v16.737H64.916    c-7.125,0-12.9,5.776-12.9,12.901V74.47h304.451V33.944C356.467,26.819,350.692,21.043,343.567,21.043z"
											/>
										</g>
									</g>
									<g />
									<g />
									<g />
									<g />
									<g />
									<g />
									<g />
									<g />
									<g />
									<g />
									<g />
									<g />
									<g />
									<g />
									<g />
								</svg>
							</div>
						</td>
					</tr>
				{/each}
			{/if}
		</table>
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
		padding-right: 35px;
	}

	svg {
		width: 20px;
		height: 20px;
		cursor: pointer;
	}
</style>
