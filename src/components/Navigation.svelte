<script>
	import { Link } from "svelte-routing";

	let showNav = false;
	let showPrihlaseni = false;
	let showRegistrace = false;

	let email;
	let password;

	let emailReg;
	let passwordReg;
	let prezdivkaReg;
</script>

<style>
	#header {
		display: flex;
		justify-content: space-between;
		align-items: center;

		margin-bottom: 10px;
	}

	#header__logo-text {
		color: #333333;

		font-size: 30px;
		font-weight: 700;
		margin-left: 10px;

		vertical-align: middle;
		display: inline-block;
	}

	#header__logo-svg {
		vertical-align: middle;
		display: inline-block;
	}

	#header__navigation {
		color: #333333;
		font-size: 18px;
		font-weight: 600;

		display: flex;
		justify-content: space-between;
		min-width: 300px;
	}

	#header__navigation span {
		transition: 0.1s;
	}

	#header__navigation span:hover {
		color: #ff8a00;
	}

	hr {
		position: absolute;
		left: 0;
		width: 100%;
		display: inline;
		border-top: thin solid #e0e0e0;
		border-right: 0;
		border-left: 0;
	}

	@media only screen and (max-width: 680px) {
		#header {
			justify-content: center;
			margin-left: 0px;
			margin-right: 0px;
			margin-bottom: 0px;
		}
		#header__navigation {
			display: none !important;
		}
		svg {
			width: 38px;
		}
		#header__logo-text {
			font-size: 26px;
		}
	}

	#nav {
		position: fixed;
		padding: 10px;
		left: 10px;
		bottom: 0;

		z-index: 11;

		width: 65px;
		height: 65px;

		transition: 0.3s ease-in-out;

		cursor: pointer;
	}

	#nav-bg {
		position: fixed;
		top: 0;
		left: 0;
		height: 100vh;
		width: 100vw;

		z-index: 7;

		background: #333333;
		opacity: 0.95;

		animation: show 0.3s;
	}

	@keyframes show {
		0% {
			opacity: 0;
		}
		100% {
			opacity: 0.95;
		}
	}
	#mobile-nav {
		width: 65px;
		position: fixed;
		padding: 10px;
		left: 10px;
		bottom: 0;
		height: 65px;
		z-index: 10;
	}

	#mobile-nav div {
		position: relative;

		width: 100%;
		height: 100%;

		color: #ffffff;

		font-size: 24px;
		font-weight: 600;
	}

	#nav-text-1 {
		position: absolute;
		top: -50px;
		transform: translateX(-50%);
		left: 50%;
		color: #ffffff;
	}
	#nav-text-2 {
		position: absolute;
		right: -120px;
		top: 0px;
		color: #ffffff;
	}
	#nav-text-3 {
		position: absolute;
		bottom: -30px;
		right: -80px;
		color: #ffffff;
	}
	#nav-text-4 {
		position: absolute;
		bottom: -30px;
		left: -60px;
		color: #ffffff;
	}
	#nav-text-5 {
		position: absolute;
		top: 0px;
		left: -120px;
		color: #ffffff;
	}

	@keyframes appear {
		0% {
			opacity: 0;
		}
		99% {
			opacity: 0;
		}
		100% {
			opacity: 1;
		}
	}
	/* prihlaseni*/

	#login {
		position: fixed;
		width: 85%;
		height: 50%;
		min-height: 440px;
		top: 50%;
		left: 50%;
		transform: translateX(-50%) translateY(-50%);
		z-index: 11;

		border-radius: 5px;
		transition: 0.15s;
		background-color: #ffffff;

		padding-left: 20px;
		padding-right: 20px;
		padding-top: 10px;
	}

	#registrace {
		position: fixed;
		width: 85%;
		height: 50%;
		min-height: 530px;
		top: 50%;
		left: 50%;
		transform: translateX(-50%) translateY(-50%);
		z-index: 11;

		border-radius: 5px;
		transition: 0.15s;
		background-color: #ffffff;

		padding-left: 20px;
		padding-right: 20px;
		padding-top: 10px;
	}

	h2 {
		color: #ff8a00;
		font-size: 38px;
		margin: 0;
	}

	#create_account {
		margin: 0;
		font-weight: 600;
		padding: 0;
		margin-bottom: 20px;
	}

	.label {
		font-size: 24px;
		font-weight: 600;
		margin-bottom: 5px;
	}

	input {
		width: calc(100% - 4px - 20px);
		border: 2px solid #ff8a00;
		border-radius: 5px;
		height: 30px;
		font-size: 16px;
		padding: 5px;
		padding-left: 10px;
	}

	u {
		cursor: pointer;
		transition: 0.15s;
	}
	u:hover {
		color: #ff8a00;
	}

	#login-logo {
		position: absolute;
		bottom: 10px;
		right: 10px;

		width: 70px;
		padding: 10px;

		cursor: pointer;
	}
	#login-forgot {
		position: absolute;
		bottom: 40px;
		left: 20px;
	}

	#login-heslo {
		position: relative;
	}

	#login-heslo svg {
		position: absolute;
		right: 15px;
		top: 50%;
		transform: translateY(-50%);
	}

	#register-heslo {
		position: relative;
	}

	#register-heslo svg {
		position: absolute;
		right: 15px;
		top: 50%;
		transform: translateY(-50%);
	}

	#zavrit-prihlaseni {
		position: absolute;
		right: 20px;
		top: 10px;

		color: #ff8a00;
		font-size: 30px;
		font-weight: 700;
	}
</style>

<header id="header">
	<Link to="/">
		<svg
			xmlns="http://www.w3.org/2000/svg"
			width="54"
			height="53"
			viewBox="0 0 54 53"
			fill="none"
			id="header__logo-svg">
			<path
				d="M21.7099 1.84346C24.8643 -0.448336 29.1357 -0.448337 32.2901 1.84346L50.2416 14.886C53.396 17.1778 54.7159 21.2401 53.5111 24.9483L46.6542 46.0517C45.4493 49.7599 41.9937 52.2705 38.0947 52.2705H15.9053C12.0063 52.2705 8.55068 49.7599 7.34582 46.0517L0.48893 24.9483C-0.715938 21.2401 0.603984 17.1778 3.75837 14.886L21.7099 1.84346Z"
				fill="#FF8A00" />
		</svg>
		<span id="header__logo-text">VelkáDomů.cz</span>
	</Link>
	<nav id="header__navigation">
		<div>
			<Link to="/kalendar"><span>Kalendář</span></Link>
		</div>
		<div>
			<Link to="/kalendar"><span>Kontakt</span></Link>
		</div>
		<div>
			<Link to="/hledat"><span>Hledat</span></Link>
		</div>
	</nav>
</header>
<hr />
<svg
	xmlns="http://www.w3.org/2000/svg"
	width="107"
	height="107"
	viewBox="0 0 107 107"
	fill="none"
	id="nav"
	on:click={() => {
		showNav = !showNav;
		showRegistrace = false;
		showPrihlaseni = false;
	}}
	style={showNav && 'left: 50%; transform: translateX(-50%);bottom: 60px;'}>
	<path
		d="M44.6832 6.40576C49.9405 2.5861 57.0595 2.5861 62.3168 6.40576L95.5648 30.5618C100.822 34.3815 103.022 41.152 101.014 47.3323L88.3142 86.4177C86.3061 92.598 80.5468 96.7824 74.0484 96.7824H32.9516C26.4532 96.7824 20.6939 92.598 18.6858 86.4177L5.98618 47.3323C3.97807 41.152 6.17794 34.3815 11.4353 30.5618L44.6832 6.40576Z"
		fill="#FF8A00" />
	<path d="M35 43H71M35 54H71M35 65H71" stroke="white" stroke-width="3" />
</svg>
{#if showNav}
	<div
		id="mobile-nav"
		style={showNav && 'left: 50%; transform: translateX(-50%);bottom: 60px; animation: .3s appear;'}>
		<div>
			<Link to="/kalendar"><span id="nav-text-1">Kalendář</span></Link>
			<span
				id="nav-text-2"
				on:click={() => {
					showRegistrace = true;
					showNav = false;
				}}>Registrace</span>
			<Link to="/kontakt"><span id="nav-text-3">Kontakt</span></Link>
			<Link to="/o-nas"><span id="nav-text-4">O nás</span></Link>
			<span
				id="nav-text-5"
				on:click={() => {
					showPrihlaseni = true;
					showNav = false;
				}}>Přihlášení</span>
		</div>
	</div>
{/if}
{#if showPrihlaseni}
	<div id="login">
		<h2>Přihlášení</h2>
		<span
			id="zavrit-prihlaseni"
			on:click={() => {
				showPrihlaseni = false;
			}}>X</span>
		<p id="create_account">
			nebo
			<u
				on:click={() => {
					showPrihlaseni = false;
					showRegistrace = true;
				}}>vytvoř účet</u>
		</p>

		<p class="label">Email</p>
		<input type="text" id="email" bind:value={email} />
		<p class="label">Heslo</p>
		<div id="login-heslo">
			<input type="password" id="password" bind:value={password} />
			<svg
				xmlns="http://www.w3.org/2000/svg"
				width="26"
				height="15"
				viewBox="0 0 26 15"
				fill="none"
				style="cursor: pointer"
				on:click={() => {
					if (document.getElementById('password').type == 'password') {
						document.getElementById('password').type = 'text';
					} else {
						document.getElementById('password').type = 'password';
					}
				}}>
				<path
					d="M1 7C7.91761 16.6462 18.0038 16.0151 25 7"
					stroke="#FF8A00"
					stroke-width="2" />
				<path
					d="M25 8C18.0824 -1.6462 7.99622 -1.01513 1 8"
					stroke="#FF8A00"
					stroke-width="2" />
				<circle
					cx="13"
					cy="7.5"
					r="3"
					stroke="#FF8A00"
					stroke-width="2" />
			</svg>
		</div>

		<u id="login-forgot">Zapomenuté heslo</u>
		<svg
			id="login-logo"
			xmlns="http://www.w3.org/2000/svg"
			width="82"
			height="82"
			viewBox="0 0 82 82"
			fill="none"
			on:click={() => {}}>
			<path
				d="M32.1832 6.40577C37.4405 2.58611 44.5595 2.5861 49.8168 6.40576L71.1765 21.9245C76.4338 25.7442 78.6337 32.5147 76.6256 38.695L68.4669 63.8049C66.4588 69.9853 60.6995 74.1697 54.2011 74.1697H27.7989C21.3006 74.1697 15.5412 69.9853 13.5331 63.8049L5.3744 38.6951C3.36628 32.5147 5.56615 25.7442 10.8235 21.9245L32.1832 6.40577Z"
				fill="#FF8A00" />
			<path
				d="M58.4142 42.4142C59.1953 41.6332 59.1953 40.3668 58.4142 39.5858L45.6863 26.8579C44.9052 26.0768 43.6389 26.0768 42.8579 26.8579C42.0768 27.6389 42.0768 28.9052 42.8579 29.6863L54.1716 41L42.8579 52.3137C42.0768 53.0948 42.0768 54.3611 42.8579 55.1421C43.6389 55.9232 44.9052 55.9232 45.6863 55.1421L58.4142 42.4142ZM24 43H57V39H24V43Z"
				fill="white" />
		</svg>
	</div>
{/if}

{#if showRegistrace}
	<div id="registrace">
		<h2>Registrace</h2><span
			id="zavrit-prihlaseni"
			on:click={() => {
				showRegistrace = false;
			}}>X</span>
		<p id="create_account">
			nebo
			<u
				on:click={() => {
					showPrihlaseni = true;
					showRegistrace = false;
				}}>přihlásit se</u>
		</p>

		<p class="label">Email</p>
		<input type="text" id="email" bind:value={emailReg} />
		<p class="label">Přezdívka</p>
		<input type="text" id="prezdivka" bind:value={prezdivkaReg} />

		<p class="label">Heslo</p>
		<div id="register-heslo">
			<input type="password" id="passwordReg" bind:value={passwordReg} />
			<svg
				xmlns="http://www.w3.org/2000/svg"
				width="26"
				height="15"
				viewBox="0 0 26 15"
				fill="none"
				on:click={() => {
					if (document.getElementById('passwordReg').type == 'password') {
						document.getElementById('passwordReg').type = 'text';
					} else {
						document.getElementById('passwordReg').type = 'password';
					}
				}}>
				<path
					d="M1 7C7.91761 16.6462 18.0038 16.0151 25 7"
					stroke="#FF8A00"
					stroke-width="2" />
				<path
					d="M25 8C18.0824 -1.6462 7.99622 -1.01513 1 8"
					stroke="#FF8A00"
					stroke-width="2" />
				<circle
					cx="13"
					cy="7.5"
					r="3"
					stroke="#FF8A00"
					stroke-width="2" />
			</svg>
		</div>

		<svg
			id="login-logo"
			xmlns="http://www.w3.org/2000/svg"
			width="82"
			height="82"
			viewBox="0 0 82 82"
			fill="none"
			on:click={() => {
				register();
				showRegister = false;
			}}>
			<path
				d="M32.1832 6.40577C37.4405 2.58611 44.5595 2.5861 49.8168 6.40576L71.1765 21.9245C76.4338 25.7442 78.6337 32.5147 76.6256 38.695L68.4669 63.8049C66.4588 69.9853 60.6995 74.1697 54.2011 74.1697H27.7989C21.3006 74.1697 15.5412 69.9853 13.5331 63.8049L5.3744 38.6951C3.36628 32.5147 5.56615 25.7442 10.8235 21.9245L32.1832 6.40577Z"
				fill="#FF8A00" />
			<path
				d="M58.4142 42.4142C59.1953 41.6332 59.1953 40.3668 58.4142 39.5858L45.6863 26.8579C44.9052 26.0768 43.6389 26.0768 42.8579 26.8579C42.0768 27.6389 42.0768 28.9052 42.8579 29.6863L54.1716 41L42.8579 52.3137C42.0768 53.0948 42.0768 54.3611 42.8579 55.1421C43.6389 55.9232 44.9052 55.9232 45.6863 55.1421L58.4142 42.4142ZM24 43H57V39H24V43Z"
				fill="white" />
		</svg>
	</div>
{/if}

{#if showNav || showPrihlaseni || showRegistrace}
	<div
		id="nav-bg"
		on:click={() => {
			showNav = false;
			showPrihlaseni = false;
			showRegistrace = false;
		}} />
{/if}
