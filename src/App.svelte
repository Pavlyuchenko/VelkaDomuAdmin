<script>
	import { Router, Route } from "svelte-routing";
	import TextEditor from "./components/TextEditor/TextEditor.svelte";
	import TextEditorPublikovat from "./components/TextEditor/TextEditorPublikovat.svelte";
	import Drafts from "./components/Drafts/Drafts.svelte";
	import Kontrola from "./components/Kontrola/Kontrola.svelte";
	import TitulniVyber from "./components/VyberClanku/TitulniVyber.svelte";
	import SekundarniVyber from "./components/VyberClanku/SekundarniVyber.svelte";

	import NewNavigation from "./components/NewNavigation.svelte";
	import NovyZapas from "./components/NovyZapas.svelte";
	import FbImage from "./components/FbImage.svelte";
	import Main from "./components/Main.svelte";
	import NovaRychlovka from "./components/NovaRychlovka.svelte";
	import CreateContent from "./components/CreateContent.svelte";
	import Rychlovky from "./components/Rychlovky.svelte";
	import { isAuthenticated } from "./store";
</script>

<Router>
	<NewNavigation />
	{#if $isAuthenticated}
		<Route path="/" component={Main} />
		<Route path="/drafts/:id" component={TextEditor} />
		<Route path="/drafts" component={Drafts} />
		<Route path="/drafts-kontrola/:id" component={TextEditorPublikovat} />
		<Route path="/kontrola" component={Kontrola} />
		<Route path="/titulni-clanek" component={TitulniVyber} />
		<Route path="/sekundarni-clanky" component={SekundarniVyber} />
		<Route path="/novy_zapas" component={NovyZapas} />
		<Route path="/fb-image" component={FbImage} />
		<Route path="/nova_rychlovka" component={NovaRychlovka} />
		<Route path="/create_content" component={CreateContent} />
		<Route path="/rychlovky" component={Rychlovky} />
	{/if}
</Router>
