
<script>
	let input1 = $state('0');
	let input2 = $state('0');

	// 整数として有効な入力のみ BigInt に変換
	let num1 = $derived(
		/^-?\d+$/.test(input1) ? BigInt(input1) : 0n
	);
	let num2 = $derived(
		/^-?\d+$/.test(input2) ? BigInt(input2) : 0n
	);

	let addition = $derived(num1 + num2);
	let subtraction = $derived(num1 - num2);
	let multiplication = $derived(num1 * num2);

	// 0による除算・剰余を防止
	let division = $derived(
		num2 === 0n ? null : num1 / num2
	);
	let remainder = $derived(
		num2 === 0n ? null : num1 % num2
	);
</script>

<svelte:head>
	<title>BigInt四則演算計算機</title>
</svelte:head>

<h1>BigInt四則演算計算機</h1>

<div>
	<label for="num1">数値1</label>
	<input
		id="num1"
		type="number"
		step="1"
		bind:value={input1}
		oninput={(e) => input1 = e.currentTarget.value}
		placeholder="数値1"
	/>

	<label for="num2">数値2</label>
	<input
		id="num2"
		type="number"
		step="1"
		bind:value={input2}
		oninput={(e) => input2 = e.currentTarget.value}
		placeholder="数値2"
	/>
</div>

<div>
	<p>{input1 || '0'} + {input2 || '0'} = {addition.toString()}</p>
	<p>{input1 || '0'} - {input2 || '0'} = {subtraction.toString()}</p>
	<p>{input1 || '0'} × {input2 || '0'} = {multiplication.toString()}</p>
	<p>
		{input1 || '0'} ÷ {input2 || '0'} =
		{division === null
			? '0による除算はできません'
			: division.toString()}
	</p>
	<p>
		{input1 || '0'} % {input2 || '0'} =
		{remainder === null
			? '0による剰余計算はできません'
			: remainder.toString()}
	</p>
</div>
