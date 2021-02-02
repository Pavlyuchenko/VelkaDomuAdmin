<script>
	import { Router, Route } from "svelte-routing";
	import TextEditor from "./components/TextEditor/TextEditor.svelte";
	import TextEditorPublikovat from "./components/TextEditor/TextEditorPublikovat.svelte";
	import Drafts from "./components/Drafts/Drafts.svelte";
	import Kontrola from "./components/Kontrola/Kontrola.svelte";
	import TitulniVyber from "./components/VyberClanku/TitulniVyber.svelte";
	import SekundarniVyber from "./components/VyberClanku/SekundarniVyber.svelte";

	import NewNavigation from "./components/NewNavigation.svelte";
	import NovyZapas from "./components/Kalendar/NovyZapas.svelte";
	import FbImage from "./components/FbImage.svelte";
	import Main from "./components/Main.svelte";
	import NovaRychlovka from "./components/Rychlovky/NovaRychlovka.svelte";
	import CreateContent from "./components/CreateContent.svelte";
	import Rychlovky from "./components/Rychlovky/Rychlovky.svelte";
	import { isAuthenticated } from "./store";
	import UpravitRychlovku from "./components/Rychlovky/UpravitRychlovku.svelte";
	import VydaneClanky from "./components/VydaneClanky/VydaneClanky.svelte";
	import Statistiky from "./components/Statistiky.svelte";
	import Kalendar from "./components/Kalendar/Kalendar.svelte";

	let login = false;
</script>

<Router>
	<NewNavigation bind:this={login} />
	<Route path="/fb-image" component={FbImage} />
	{#if $isAuthenticated}
		<Route path="/" component={Main} />
		<Route path="/drafts/:id" component={TextEditor} />
		<Route path="/clanky" component={VydaneClanky} />
		<Route path="/drafts" component={Drafts} />
		<Route path="/drafts-kontrola/:id" component={TextEditorPublikovat} />
		<Route path="/clanek-edit/:id" let:params>
			<TextEditorPublikovat id={params.id} redirect={"/clanky"} />
		</Route>
		<Route path="/kontrola" component={Kontrola} />
		<Route path="/titulni-clanek" component={TitulniVyber} />
		<Route path="/sekundarni-clanky" component={SekundarniVyber} />
		<Route path="/novy_zapas" component={NovyZapas} />
		<Route path="/kalendar" component={Kalendar} />
		<Route path="/nova_rychlovka" component={NovaRychlovka} />
		<Route path="/rychlovka/:id" component={UpravitRychlovku} />
		<Route path="/create_content" component={CreateContent} />
		<Route path="/statistiky" component={Statistiky} />
		<Route path="/rychlovky">
			<Rychlovky {login} />
		</Route>
	{/if}
</Router>
