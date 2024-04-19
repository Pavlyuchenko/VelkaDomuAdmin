<script>
	import { navigate } from "svelte-routing";
	import { onMount } from "svelte";

	onMount(() => {
		alert("This admin page is no longer in use.\nTo test it out, login using email test@user.cz and password testuser.\n Feel free to play around, I got the database archived.");
	});
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

<section id="create">
	<div
		on:click={() => {
			navigate("/drafts");
		}}
		class="option"
	>
		<img
			src="https://ik.imagekit.io/velkadomu/clanek_a5vjBglEA4M_.png"
			alt="pass"
		/>
		<p>Napiš článek</p>
	</div>
	<div
		on:click={() => {
			navigate("/novy_zapas");
		}}
		class="option"
	>
		<img
			src="https://ik.imagekit.io/velkadomu/Kalend___5AXWEppEHy.png"
			alt="pass"
		/>
		<p>Vytvoř zápas</p>
	</div>
	<div
		on:click={() => {
			navigate("/nova_rychlovka");
		}}
		class="option"
	>
		<img
			src="https://ik.imagekit.io/velkadomu/rychlovka_jqlaLBvenA.png"
			alt="pass"
		/>
		<p>Napiš rychlovku</p>
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

	#create {
		display: flex;
		margin-top: 100px;
		justify-content: space-between;
	}
	.option {
		width: 31%;
		text-align: center;
		border: 2px solid #ffa800;
		border-radius: 5px;
		font-weight: 600;
		font-size: 30px;

		position: relative;
		cursor: pointer;

		-webkit-transition: background-color 0.15s linear;
		-ms-transition: background-color 0.15s linear;
		transition: background-color 0.15s linear;
	}
	.option:hover {
		background-color: #ffa800;
		color: #ffffff;
	}
	.option:hover img {
		display: none;
	}
	.option:hover p {
		bottom: 50%;
		transform: translateY(50%) translateX(-50%);
		font-size: 38px;
	}

	img {
		width: 100%;
		margin-bottom: 50px;
	}
	p {
		margin: 0;
		position: absolute;
		bottom: 10px;
		left: 50%;
		transform: translateX(-50%);
		transition: 0.15s;
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
		img {
			display: none;
		}
		p {
			position: relative;
			font-size: 20px;
			top: 50%;
			transform: translateY(-50%) translateX(-50%);
		}
		option:hover p {
			position: relative;
			font-size: 20px;
			top: 50%;
			transform: translateY(-50%) translateX(-50%);
		}
	}
</style>
