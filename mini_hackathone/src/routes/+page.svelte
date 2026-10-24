<script lang="ts">
	type TarotCard = { name: string; symbol: string; meaning: string; reversed: string; note: string };
	type DrawnCard = TarotCard & { reversed: boolean; place: string };

	const deck: TarotCard[] = [
		{ name: '愚者', symbol: '✧', meaning: '新しい始まり。好奇心のままに一歩を踏み出すと、思いがけない景色に出会えそう。', reversed: '準備不足や見切り発車に注意。心が弾む方へ進む前に、足元を確かめて。', note: '自由・冒険・可能性' },
		{ name: '魔術師', symbol: '∞', meaning: '必要なものは、もう手元にあります。自分の力を信じて、考えを形にしていく時。', reversed: '自信のなさが機会を遠ざけているかも。完璧を待たず、小さく始めてみて。', note: '創造・意志・実現' },
		{ name: '女教皇', symbol: '☾', meaning: '答えは静けさの中にあります。すぐに結論を出さず、心の奥の声に耳を傾けて。', reversed: '考えすぎて本音を見失いそう。情報から少し離れ、自分の感覚を取り戻して。', note: '直感・静寂・洞察' },
		{ name: '女帝', symbol: '❀', meaning: '愛情や豊かさが育つ時。自分をいたわることが、まわりにも優しさを広げます。', reversed: '誰かに与えすぎていませんか。まず自分の心を満たすことを大切に。', note: '実り・慈愛・美' },
		{ name: '皇帝', symbol: '♜', meaning: '落ち着いて土台を整えれば、望む形に近づけます。決めたことを着実に進めて。', reversed: '思い通りにしようと力みすぎているかも。柔軟さを取り戻すと道が見えます。', note: '安定・責任・秩序' },
		{ name: '法王', symbol: '☼', meaning: '信頼できる人の助言にヒントがありそう。誠実な対話が、次の扉を開きます。', reversed: 'いつもの正解に縛られていませんか。自分に合うやり方を探してみて。', note: '学び・信頼・導き' },
		{ name: '恋人', symbol: '♡', meaning: '心が通い合う予感。大切な選択は、損得よりも自分が本当に望む方を。', reversed: '気持ちが揺れて決めきれない時。相手の期待より、自分の本音を確かめて。', note: '絆・選択・調和' },
		{ name: '戦車', symbol: '➶', meaning: '追い風が吹いています。迷いを振り切って行動すれば、状況を動かせそう。', reversed: '急ぎすぎると空回りに。いったん呼吸を整えて、進む方向を見直して。', note: '前進・意志・勝利' },
		{ name: '力', symbol: '♌', meaning: 'やさしさと粘り強さが力になります。焦らず向き合えば、難しいことも乗り越えられる。', reversed: '自分を責めすぎているかも。強くあろうとせず、助けを借りることも大切です。', note: '勇気・思いやり・忍耐' },
		{ name: '隠者', symbol: '✦', meaning: 'ひとりの時間が大切な気づきを連れてきます。答えを急がず、内側を照らして。', reversed: '閉じこもりすぎているサイン。信頼できる誰かに、少しだけ心を開いてみて。', note: '探求・内省・知恵' },
		{ name: '運命の輪', symbol: '◉', meaning: '流れが変わるタイミング。訪れたチャンスを受け取る準備をしておきましょう。', reversed: '思い通りにいかなくても、今は流れの途中。変化に合わせて計画を整えて。', note: '転機・好機・めぐり合わせ' },
		{ name: '正義', symbol: '⚖', meaning: '誠実な判断がよい結果につながります。事実を見つめ、公平な選択を。', reversed: '判断が偏っていないか見直して。曖昧にしていたことを整理する時です。', note: '公平・真実・決断' },
		{ name: '吊るされた男', symbol: '◇', meaning: '今は立ち止まることにも意味があります。視点を変えると、別の道が見つかりそう。', reversed: '報われない我慢を続けているかも。手放せるものがないか考えてみて。', note: '視点・受容・気づき' },
		{ name: '死神', symbol: '☽', meaning: 'ひとつの章が終わり、新しい余白が生まれます。手放すことで次へ進める時。', reversed: '終わったことに心が留まっているようです。小さな区切りをつけてみて。', note: '終わり・再生・変化' },
		{ name: '節制', symbol: '⚗', meaning: '違うものを混ぜ合わせることで、ちょうどよい答えにたどり着けます。', reversed: '無理なバランスを保とうとしていませんか。生活や気持ちを少し整えて。', note: '調和・回復・ほどよさ' },
		{ name: '悪魔', symbol: '♧', meaning: '惰性や執着に気づくチャンス。自分を縛るものの正体を見つめてみましょう。', reversed: '重荷から抜け出す兆し。小さな選択を変えることで、自由を取り戻せます。', note: '欲望・執着・解放' },
		{ name: '塔', symbol: 'ϟ', meaning: '予想外の変化が、本当に必要なものを教えてくれます。壊れた後には再建できます。', reversed: '変化を先延ばしにしていませんか。守りたいものを見極める時です。', note: '刷新・解放・真実' },
		{ name: '星', symbol: '✳', meaning: '希望の光が見えています。すぐに結果が出なくても、願いを育て続けて。', reversed: '自信をなくしやすい時。小さく叶ったことに目を向けて、希望をつなぎ直して。', note: '希望・癒し・インスピレーション' },
		{ name: '月', symbol: '☾', meaning: 'まだ見えていないことがありそう。想像で決めつけず、ゆっくり確かめましょう。', reversed: '不安の正体が少しずつ見えてきます。事実をひとつずつ拾えば大丈夫。', note: '夢・揺らぎ・無意識' },
		{ name: '太陽', symbol: '☀', meaning: '喜びや手応えを感じられる日。素直な気持ちを分かち合うと、幸せが広がります。', reversed: '少し疲れがたまっているかも。休息をとれば、本来の明るさが戻ってきます。', note: '成功・活力・祝福' },
		{ name: '審判', symbol: '♬', meaning: '過去の経験が今につながる時。心に響く呼びかけに応えて、新しい一歩を。', reversed: '後悔にとらわれず、これから選べることに目を向けて。やり直す機会はあります。', note: '目覚め・再出発・応答' },
		{ name: '世界', symbol: '◎', meaning: 'ひとつの目標が実を結びそう。ここまで歩いてきた自分を、誇りに思って。', reversed: 'あと少しで完成です。急がず最後の仕上げに心を配りましょう。', note: '完成・達成・統合' }
	];
	const places = ['過去', '現在', '未来'];
	let drawn: DrawnCard[] = [];
	let drawing = false;

	function drawCards() {
		if (drawing) return;
		drawing = true;
		drawn = [];
		const shuffled = [...deck].sort(() => Math.random() - 0.5).slice(0, 3);
		setTimeout(() => {
			drawn = shuffled.map((card, index) => ({ ...card, reversed: Math.random() < 0.28, place: places[index] }));
			drawing = false;
			setTimeout(() => document.querySelector('.reading')?.scrollIntoView({ behavior: 'smooth', block: 'start' }), 80);
		}, 650);
	}
</script>

<svelte:head>
	<title>月灯りのタロット</title>
	<meta name="description" content="過去・現在・未来を3枚のカードで占う、月灯りのタロットリーディング。" />
</svelte:head>

<section class="hero">
	<div class="eyebrow"><span></span> YOUR MOMENT OF CLARITY <span></span></div>
	<h1>心の声に、<br /><em>耳を澄ませて。</em></h1>
	<p class="intro">カードが映し出すのは、あなたの中にある答え。<br />深呼吸して、今の気持ちを思い浮かべてみて。</p>
	<div class="moon" aria-hidden="true">☾</div>
	<div class="deck" class:shuffling={drawing} aria-label="裏向きのタロットカード">
		<div class="back back-left"><span>✳</span></div><div class="back back-right"><span>✧</span></div><div class="back back-center"><span>☾</span></div>
	</div>
	<p class="hint">心が落ち着いたら、カードを引いてください</p>
	<button class="draw-button" onclick={drawCards} disabled={drawing}>
		<span>{drawing ? 'カードをめくっています…' : drawn.length ? 'もう一度カードを引く' : 'カードを引く'}</span><b>→</b>
	</button>
	<div class="spread-label"><span></span> THREE CARD SPREAD <span></span></div>
</section>

{#if drawn.length}
	<section class="reading" aria-live="polite">
		<div class="section-heading"><span class="small-star">✳</span><p>YOUR READING</p><h2>カードからのメッセージ</h2></div>
		<div class="cards">
			{#each drawn as card, i (card.name)}
				<article class="reading-card" style={`--delay: ${i * 100}ms`}>
					<p class="position">{card.place}</p>
					<div class="tarot-card" class:reversed={card.reversed}>
						<div class="card-frame"><span class="roman">{['0', 'I', 'II', 'III', 'IV', 'V', 'VI', 'VII', 'VIII', 'IX', 'X', 'XI', 'XII', 'XIII', 'XIV', 'XV', 'XVI', 'XVII', 'XVIII', 'XIX', 'XX', 'XXI'][deck.indexOf(card)]}</span><span class="card-symbol">{card.symbol}</span><span class="card-name">{card.name}</span></div>
					</div>
					<p class="orientation">{card.reversed ? 'REVERSED · 逆位置' : 'UPRIGHT · 正位置'}</p>
					<p class="keywords">{card.note}</p>
					<p class="message">{card.reversed ? card.reversed : card.meaning}</p>
				</article>
			{/each}
		</div>
		<p class="closing">カードは未来を決めるものではなく、<br />今のあなたを見つめるための小さな道しるべです。</p>
	</section>
{/if}

<style>
	.hero { text-align: center; padding: 2.4rem 0 1rem; position: relative; overflow: hidden; }
	.eyebrow,.spread-label { display:flex; align-items:center; justify-content:center; gap:.8rem; color:var(--gold); font-size:.63rem; letter-spacing:.23em; }
	.eyebrow span,.spread-label span { height:1px; width:2rem; background:var(--gold); opacity:.55; }
	h1 { font-family: 'Yu Mincho', 'Hiragino Mincho ProN', serif; font-weight:500; font-size:clamp(2.3rem, 9vw, 3.5rem); line-height:1.35; letter-spacing:.11em; margin:1.25rem 0 .9rem; }
	h1 em { color:var(--gold); font-style:normal; }
	.intro { color:var(--muted); font-size:.91rem; line-height:2; margin:0; }
	.moon { color:var(--gold); font-size:2.4rem; margin:1.15rem auto .35rem; line-height:1; text-shadow:0 0 24px #d3b46b66; }
	.deck { width:9rem; height:7.3rem; margin:0 auto; position:relative; }
	.back { position:absolute; width:4.25rem; height:6.4rem; top:.35rem; left:50%; margin-left:-2.1rem; background:linear-gradient(145deg,#292c4a,#17192f); border:1px solid #c3a65b; border-radius:7px; padding:4px; box-shadow:0 7px 18px #0005; transition:transform .45s ease; }
	.card-frame { height:100%; border:1px solid #b9a05b88; border-radius:4px; display:flex; align-items:center; justify-content:center; flex-direction:column; color:#dfc879; }
	.back::after { content:''; position:absolute; inset:8px; border:1px solid #c9ac5b77; border-radius:50% 50% 3px 3px; }
	.back span { position:absolute; z-index:1; top:50%; left:50%; transform:translate(-50%,-50%); font-size:1.2rem; color:#dac478; }
	.back-left { transform:rotate(-17deg) translateX(-1rem); }.back-right { transform:rotate(17deg) translateX(1rem); }.back-center { z-index:1; }
	.shuffling .back-left { transform:translateX(-1.5rem) rotate(-27deg) translateY(-8px); }.shuffling .back-right { transform:translateX(1.5rem) rotate(27deg) translateY(-8px); }.shuffling .back-center { transform:translateY(-12px); }
	.hint { color:var(--muted); font-size:.75rem; margin:.05rem 0 1.1rem; }
	.draw-button { display:inline-flex; align-items:center; gap:2.25rem; padding:.85rem 1.45rem; border:1px solid var(--gold); border-radius:3px; background:linear-gradient(110deg,#c4a553,#a9893e); color:#171628; font:inherit; font-size:.9rem; letter-spacing:.06em; cursor:pointer; box-shadow:0 6px 22px #c0a25426; transition:transform .2s, box-shadow .2s; }
	.draw-button:hover:not(:disabled) { transform:translateY(-2px); box-shadow:0 9px 28px #c0a25440; }.draw-button:disabled { opacity:.75; cursor:wait; }.draw-button b { font-size:1.15rem; font-weight:400; }
	.spread-label { margin-top:2.1rem; color:var(--muted); font-size:.58rem; }.spread-label span { width:1.5rem; opacity:.3; }
	.reading { padding:2.5rem 0 1rem; border-top:1px solid var(--line); margin-top:1.4rem; scroll-margin-top:1.5rem; }
	.section-heading { text-align:center; margin-bottom:1.8rem; }.small-star { color:var(--gold); font-size:1.1rem; }.section-heading p { margin:.25rem 0; color:var(--gold); font-size:.57rem; letter-spacing:.25em; }.section-heading h2 { font:500 1.3rem 'Yu Mincho','Hiragino Mincho ProN',serif; margin:.35rem 0; letter-spacing:.1em; }
	.cards { display:grid; grid-template-columns:repeat(3,minmax(0,1fr)); gap:1rem; }
	.reading-card { text-align:center; animation:rise .55s both; animation-delay:var(--delay); }.position { color:var(--gold); letter-spacing:.16em; font-size:.8rem; margin:0 0 .75rem; }
	.tarot-card { width:min(100%,9rem); aspect-ratio: .66; margin:0 auto; background:linear-gradient(145deg,#303352,#191b31); border:1px solid #c2a552; border-radius:7px; padding:5px; box-shadow:0 8px 25px #0006; }
	.tarot-card .card-frame { position:relative; }.roman { position:absolute; top:6%; font: .65rem Georgia,serif; }.card-symbol { font:2.6rem Georgia,serif; text-shadow:0 0 22px #e6cf8d66; }.card-name { font: .78rem 'Yu Mincho','Hiragino Mincho ProN',serif; margin-top:.25rem; }
	.tarot-card.reversed { transform:rotate(180deg); }.orientation { color:var(--muted); font-size:.54rem; letter-spacing:.06em; margin:.7rem 0 .2rem; }.keywords { color:var(--gold); font: .7rem 'Yu Mincho','Hiragino Mincho ProN',serif; margin:0 0 .65rem; }
	.message { margin:0; text-align:left; font-size:.77rem; line-height:1.9; color:#ded9d1; }
	.closing { text-align:center; color:var(--muted); border-top:1px solid var(--line); margin:2rem 0 0; padding:1.4rem 0 0; font: .76rem/2 'Yu Mincho','Hiragino Mincho ProN',serif; }
	@keyframes rise { from { opacity:0; transform:translateY(14px); } to { opacity:1; transform:translateY(0); } }
	@media (max-width:520px) { .hero { padding-top:1.6rem; }.cards { gap:.65rem; }.tarot-card { width:100%; }.card-symbol { font-size:2rem; }.card-name { font-size:.68rem; }.message { font-size:.7rem; line-height:1.75; }.keywords { font-size:.62rem; min-height:2.3em; }.orientation { font-size:.46rem; } }
</style>
