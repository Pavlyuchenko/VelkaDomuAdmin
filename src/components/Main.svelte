<script>
	import { navigate } from "svelte-routing";
</script>

<section>
	<div id="admin-wrapper">
		<div
			on:click={() => {
				navigate("/titulni-clanek");
			}}
			class="admin-button"
		>
			Titulní článek
		</div>
		<div
			on:click={() => {
				navigate("/sekundarni-clanky");
			}}
			class="admin-button"
		>
			Sekundární články
		</div>
		<div
			on:click={() => {
				navigate("/kontrola");
			}}
			class="admin-button"
		>
			Kontrola
		</div>
	</div>
</section>

<style>
	#admin-wrapper {
		display: flex;
		justify-content: space-between;

		margin-top: 80px;
	}
	.admin-button {
		width: 32%;
		height: 50px;
		line-height: 50px;

		background-color: #ff8a00;
		color: #ffffff;
		text-align: center;
		border-radius: 5px;

		font-size: 30px;
		font-weight: 700;

		transition: 0.15s;
		cursor: pointer;
	}
	.admin-button:hover {
		background-color: #ffffff;
		color: #ff8a00;
	}

	@media (max-width: 680px) {
		#admin-wrapper {
			flex-direction: column;
			margin-top: 10px;
		}
		.admin-button {
			width: 100%;
			margin-top: 20px;
		}
	}
</style>
