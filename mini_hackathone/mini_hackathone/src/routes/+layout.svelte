<script lang="ts">
	import '../app.css';
	let { children } = $props();
</script>


<div class="shell">
	<header>
		<a class="brand" href="/"><span>☾</span> 月灯りのタロット</a>
		<div class="header-note">MAJOR ARCANA · 3 CARD READING</div>
	</header>

	<main>
		{@render children()}
	</main>

	<footer>Take a breath. Trust your own light. <span>✧</span></footer>
</div>

<style>
	.shell {
		max-width: 54rem;
		margin: 0 auto;
		padding: 1.15rem 1.5rem 3rem;
		min-height: 100dvh;
		display: flex;
		flex-direction: column;
	}

	header {
		display: flex;
		align-items: center;
		justify-content: space-between;
		gap: 1rem;
		flex-wrap: wrap;
		padding: 0 0 1rem;
		border-bottom: 1px solid #ffffff12;
	}

	.brand {
		font-weight: 700;
		font-size: .93rem;
		text-decoration: none;
		color: var(--fg);
		letter-spacing: .08em;
	}
	.brand span { color: var(--gold); font-size: 1.2rem; vertical-align: -1px; margin-right: .3rem; }
	.header-note { color: #777684; font-size: .56rem; letter-spacing: .16em; }
	@media(max-width:460px) { .header-note { font-size:.48rem; letter-spacing:.1em; } .shell { padding-inline:1rem; } }
	main {
		flex: 1;
		padding-top: .25rem;
	}

	footer {
		padding-top: 2rem;
		font-size: 0.68rem;
		color: #777684;
		text-align: center;
		letter-spacing:.08em;
	}
	footer span { color:var(--gold); margin-left:.35rem; }
</style>
