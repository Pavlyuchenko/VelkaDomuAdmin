<script>
	import { navigate } from "svelte-routing";
</script>

<section>
	<div
		on:click={() => {
			navigate("/drafts");
		}}
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
	>
		<img
			src="https://ik.imagekit.io/velkadomu/rychlovka_jqlaLBvenA.png"
			alt="pass"
		/>
		<p>Napiš rychlovku</p>
	</div>
</section>

<style>
	section {
		display: flex;
		margin-top: 100px;
		justify-content: space-between;
	}
	div {
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
	div:hover {
		background-color: #ffa800;
		color: #ffffff;
	}
	div:hover img {
		display: none;
	}
	div:hover p {
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
		img {
			display: none;
		}
		p {
			position: relative;
			font-size: 20px;
			top: 50%;
			transform: translateY(-50%) translateX(-50%);
		}
		div:hover p {
			position: relative;
			font-size: 20px;
			top: 50%;
			transform: translateY(-50%) translateX(-50%);
		}
	}
</style>
