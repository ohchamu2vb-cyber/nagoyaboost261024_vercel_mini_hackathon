
<script>
    import Chart from "chart.js/auto";

    let canvas = $state();
    let chart = $state(null);

    let records = $state([]);

    let condition = $state("普通");
    let temperature = $state("36.5");
    let memo = $state("");

    let errorMessage = $state("");

    const conditionOptions = [
        "とても良い",
        "良い",
        "普通",
        "少し不調",
        "不調",
        "とても不調"
    ];

    // 入力値の妥当性
    let isTemperatureValid = $derived(
        temperature.trim() !== "" &&
        Number.isFinite(Number(temperature)) &&
        Number(temperature) >= 30 &&
        Number(temperature) <= 45
    );

    let canSave = $derived(
        isTemperatureValid && condition !== ""
    );

    // グラフ用に日時の古い順に並べる
    let sortedRecords = $derived(
        [...records].sort(
            (a, b) =>
                new Date(a.timestamp).getTime() -
                new Date(b.timestamp).getTime()
        )
    );

    let temperatureLabels = $derived(
        sortedRecords.map((record) =>
            new Date(record.timestamp).toLocaleString("ja-JP", {
                month: "2-digit",
                day: "2-digit",
                hour: "2-digit",
                minute: "2-digit"
            })
        )
    );

    let temperatureValues = $derived(
        sortedRecords.map((record) => record.temperature)
    );

    // グラフを初期化
    $effect(() => {
        if (!canvas) return;

        chart = new Chart(canvas, {
            type: "line",
            data: {
                labels: [],
                datasets: [
                    {
                        label: "体温（℃）",
                        data: [],
                        borderColor: "#e76f51",
                        backgroundColor: "rgba(231, 111, 81, 0.15)",
                        borderWidth: 2,
                        pointRadius: 4,
                        pointHoverRadius: 6,
                        pointBackgroundColor: "#e76f51",
                        fill: true,
                        tension: 0.25,
                        spanGaps: false
                    }
                ]
            },
            options: {
                responsive: true,
                maintainAspectRatio: false,
                interaction: {
                    intersect: false,
                    mode: "index"
                },
                plugins: {
                    legend: {
                        display: true,
                        position: "top"
                    },
                    tooltip: {
                        callbacks: {
                            label: (context) =>
                                `体温: ${context.parsed.y} ℃`
                        }
                    }
                },
                scales: {
                    x: {
                        title: {
                            display: true,
                            text: "記録日時"
                        },
                        ticks: {
                            maxRotation: 45,
                            minRotation: 0,
                            autoSkip: true,
                            maxTicksLimit: 10
                        }
                    },
                    y: {
                        title: {
                            display: true,
                            text: "体温（℃）"
                        },
                        suggestedMin: 35,
                        suggestedMax: 38,
                        ticks: {
                            callback: (value) => `${value} ℃`
                        }
                    }
                }
            }
        });

        return () => {
            chart?.destroy();
            chart = null;
        };
    });

    // 記録が変更されるたびにグラフを更新
    $effect(() => {
        if (!chart) return;

        chart.data.labels = temperatureLabels;
        chart.data.datasets[0].data = temperatureValues;
        chart.update();
    });

    // 現在日時・体調・体温を記録
    function addRecord() {
        errorMessage = "";

        if (!canSave) {
            errorMessage = "体温を30.0〜45.0℃の範囲で入力してください。";
            return;
        }

        const now = new Date();

        records = [
            ...records,
            {
                id: `${now.getTime()}-${Math.random()}`,
                timestamp: now.toISOString(),
                condition,
                temperature: Number(Number(temperature).toFixed(1)),
                memo: memo.trim()
            }
        ];

        memo = "";
    }

    // 記録を削除
    function deleteRecord(id) {
        records = records.filter((record) => record.id !== id);
    }

    // 日時を表示用に整形
    function formatDate(timestamp) {
        return new Date(timestamp).toLocaleString("ja-JP", {
            year: "numeric",
            month: "2-digit",
            day: "2-digit",
            hour: "2-digit",
            minute: "2-digit",
            second: "2-digit"
        });
    }

    // 最新の体温
    let latestRecord = $derived(
        sortedRecords.length > 0
            ? sortedRecords[sortedRecords.length - 1]
            : null
    );

    // 記録の平均体温
    let averageTemperature = $derived(
        records.length > 0
            ? (
                records.reduce(
                    (sum, record) => sum + record.temperature,
                    0
                ) / records.length
            ).toFixed(1)
            : "--"
    );
</script>

<svelte:head>
    <title>体調・体温記録</title>
    <meta
        name="viewport"
        content="width=device-width, initial-scale=1"
    />
    <meta
        name="description"
        content="毎日の体調と体温を記録してグラフで確認する健康管理ページ"
    />
</svelte:head>

<main>
    <header class="page-header">
        <div>
            <p class="eyebrow">DAILY HEALTH LOG</p>
            <h1>体調・体温記録</h1>
            <p class="subtitle">
                毎日の体調を記録して、自分の変化をチェックしよう。
            </p>
        </div>
    </header>

    <section class="summary-grid" aria-label="記録の概要">
        <article class="summary-card">
            <span class="summary-label">記録件数</span>
            <strong class="summary-value">{records.length}<small>件</small></strong>
        </article>

        <article class="summary-card">
            <span class="summary-label">最新の体温</span>
            <strong class="summary-value">
                {latestRecord
                    ? latestRecord.temperature.toFixed(1)
                    : "--"}
                <small>℃</small>
            </strong>
        </article>

        <article class="summary-card">
            <span class="summary-label">平均体温</span>
            <strong class="summary-value">
                {averageTemperature}<small>℃</small>
            </strong>
        </article>

        <article class="summary-card">
            <span class="summary-label">最新の体調</span>
            <strong class="condition-value">
                {latestRecord ? latestRecord.condition : "未記録"}
            </strong>
        </article>
    </section>

    <section class="panel input-panel">
        <div class="section-heading">
            <div>
                <p class="eyebrow">NEW RECORD</p>
                <h2>今日の記録</h2>
            </div>
            <span class="live-badge">現在日時を自動記録</span>
        </div>

        <p class="current-time">
            <span class="time-icon">◷</span>
            記録ボタンを押した時点の日時が保存されます。
        </p>

        <div class="form-grid">
            <div class="form-field">
                <label for="condition">今日の体調</label>
                <select id="condition" bind:value={condition}>
                    {#each conditionOptions as option}
                        <option value={option}>{option}</option>
                    {/each}
                </select>
            </div>

            <div class="form-field">
                <label for="temperature">体温（℃）</label>
                <div class="temperature-input">
                    <input
                        id="temperature"
                        type="number"
                        min="30"
                        max="45"
                        step="0.1"
                        bind:value={temperature}
                        placeholder="36.5"
                    />
                    <span>℃</span>
                </div>
                <p class="field-hint">30.0〜45.0℃で入力</p>
            </div>

            <div class="form-field memo-field">
                <label for="memo">メモ（任意）</label>
                <textarea
                    id="memo"
                    bind:value={memo}
                    rows="3"
                    placeholder="例：少し頭痛がある、よく眠れた"
                ></textarea>
            </div>
        </div>

        {#if errorMessage}
            <p class="error-message" role="alert">{errorMessage}</p>
        {/if}

        <button
            class="primary-button"
            onclick={addRecord}
            disabled={!canSave}
        >
            ＋ この内容を記録する
        </button>
    </section>

    <section class="panel chart-panel">
        <div class="section-heading">
            <div>
                <p class="eyebrow">TEMPERATURE TREND</p>
                <h2>体温の推移</h2>
            </div>
            <span class="record-count">{records.length}件の記録</span>
        </div>

        {#if records.length === 0}
            <div class="empty-chart">
                <div class="empty-icon">⌁</div>
                <p>まだ体温の記録がありません。</p>
                <span>上のフォームから最初の記録を追加してください。</span>
            </div>
        {/if}

        <div class="chart-container">
            <canvas bind:this={canvas}></canvas>
        </div>

        <p class="chart-note">
            ※ 記録した体温を日時順に表示します。表示範囲はデータに応じて調整されます。
        </p>
    </section>

    <section class="panel history-panel">
        <div class="section-heading">
            <div>
                <p class="eyebrow">RECORD HISTORY</p>
                <h2>記録一覧</h2>
            </div>
            <span class="record-count">{records.length}件</span>
        </div>

        {#if records.length === 0}
            <div class="empty-history">
                記録はまだありません。
            </div>
        {:else}
            <div class="history-list">
                {#each [...sortedRecords].reverse() as record (record.id)}
                    <article class="history-item">
                        <div class="history-main">
                            <span class="history-date">
                                {formatDate(record.timestamp)}
                            </span>
                            <div class="history-details">
                                <strong class="history-temperature">
                                    {record.temperature.toFixed(1)}<small>℃</small>
                                </strong>
                                <span class="condition-tag">{record.condition}</span>
                            </div>
                            {#if record.memo}
                                <p class="history-memo">{record.memo}</p>
                            {/if}
                        </div>
                        <button
                            class="delete-button"
                            onclick={() => deleteRecord(record.id)}
                            aria-label={`${formatDate(record.timestamp)}の記録を削除`}
                        >
                            削除
                        </button>
                    </article>
                {/each}
            </div>
        {/if}
    </section>

    <footer>
        <p>
            記録は現在のページを開いている間だけ保持されます。
            ページを再読み込みすると消去されます。
        </p>
    </footer>
</main>

<style>
    :global(*) {
        box-sizing: border-box;
    }

    :global(html) {
        width: 100%;
        min-height: 100%;
        background: #f5f7fa;
    }

    :global(body) {
        margin: 0;
        width: 100%;
        min-width: 320px;
        min-height: 100vh;
        font-family:
            -apple-system, BlinkMacSystemFont, "Segoe UI",
            "Noto Sans JP", sans-serif;
        color: #263447;
        background: #f5f7fa;
    }

    :global(button),
    :global(input),
    :global(select),
    :global(textarea) {
        font: inherit;
    }

    main {
        width: 100%;
        max-width: none;
        min-height: 100vh;
        margin: 0;
        padding: clamp(16px, 3vw, 40px);
    }

    .page-header {
        width: 100%;
        margin-bottom: 28px;
    }

    .eyebrow {
        margin: 0 0 8px;
        color: #6c82a0;
        font-size: 11px;
        font-weight: 800;
        letter-spacing: 0.15em;
    }

    h1 {
        margin: 0;
        font-size: clamp(26px, 3vw, 38px);
        font-weight: 800;
        letter-spacing: -0.04em;
    }

    .subtitle {
        margin: 10px 0 0;
        color: #77869a;
        font-size: 14px;
        line-height: 1.7;
    }

    .summary-grid {
        display: grid;
        grid-template-columns: repeat(4, minmax(0, 1fr));
        gap: 16px;
        margin-bottom: 24px;
    }

    .summary-card {
        display: flex;
        min-width: 0;
        min-height: 118px;
        flex-direction: column;
        justify-content: center;
        gap: 12px;
        padding: 22px;
        border: 1px solid #e8edf3;
        border-radius: 16px;
        background: #fff;
        box-shadow: 0 3px 14px rgb(32 52 76 / 3%);
    }

    .summary-label {
        color: #7a889a;
        font-size: 13px;
    }

    .summary-value {
        font-size: clamp(23px, 2.5vw, 32px);
        font-weight: 800;
        letter-spacing: -0.04em;
        overflow-wrap: anywhere;
    }

    .summary-value small {
        margin-left: 4px;
        font-size: 14px;
        font-weight: 600;
    }

    .condition-value {
        font-size: clamp(15px, 1.5vw, 20px);
        overflow-wrap: anywhere;
    }

    .panel {
        width: 100%;
        margin-bottom: 24px;
        padding: clamp(18px, 2.5vw, 32px);
        border: 1px solid #e8edf3;
        border-radius: 18px;
        background: #fff;
        box-shadow: 0 3px 14px rgb(32 52 76 / 3%);
    }

    .section-heading {
        display: flex;
        align-items: center;
        justify-content: space-between;
        flex-wrap: wrap;
        gap: 12px;
        margin-bottom: 20px;
    }

    h2 {
        margin: 0;
        font-size: clamp(19px, 2vw, 24px);
        font-weight: 750;
    }

    .live-badge,
    .record-count {
        display: inline-flex;
        align-items: center;
        border-radius: 999px;
        padding: 7px 12px;
        background: #f0f4fa;
        color: #5c7290;
        font-size: 12px;
        font-weight: 650;
    }

    .current-time {
        margin: 0 0 24px;
        color: #6e7f94;
        font-size: 13px;
    }

    .time-icon {
        margin-right: 5px;
        font-size: 17px;
    }

    .form-grid {
        display: grid;
        grid-template-columns: repeat(2, minmax(0, 1fr));
        gap: 22px;
    }

    .form-field {
        display: flex;
        min-width: 0;
        flex-direction: column;
        gap: 9px;
    }

    .form-field label {
        color: #47566b;
        font-size: 13px;
        font-weight: 700;
    }

    .form-field input,
    .form-field select,
    .form-field textarea {
        width: 100%;
        min-width: 0;
        border: 1px solid #dce4ee;
        border-radius: 10px;
        padding: 12px 14px;
        color: #263447;
        background: #fff;
        outline: none;
        transition: border-color 0.15s, box-shadow 0.15s;
    }

    .form-field input:focus,
    .form-field select:focus,
    .form-field textarea:focus {
        border-color: #668bc5;
        box-shadow: 0 0 0 3px rgb(102 139 197 / 13%);
    }

    .temperature-input {
        display: flex;
        align-items: center;
        gap: 10px;
    }

    .temperature-input input {
        flex: 1;
    }

    .temperature-input span {
        color: #718096;
        font-weight: 700;
    }

    .field-hint {
        margin: 0;
        color: #8b98a9;
        font-size: 11px;
    }

    .memo-field {
        grid-column: 1 / -1;
    }

    .form-field textarea {
        resize: vertical;
        line-height: 1.6;
    }

    .primary-button {
        display: block;
        width: 100%;
        min-height: 50px;
        margin-top: 24px;
        border: 0;
        border-radius: 11px;
        color: #fff;
        background: #496fa8;
        font-size: 14px;
        font-weight: 750;
        cursor: pointer;
        transition: background 0.15s, opacity 0.15s;
    }

    .primary-button:hover:not(:disabled) {
        background: #365b91;
    }

    .primary-button:disabled {
        opacity: 0.5;
        cursor: not-allowed;
    }

    .error-message {
        margin: 16px 0 0;
        color: #c43f3f;
        font-size: 13px;
    }

    .chart-container {
        position: relative;
        width: 100%;
        height: clamp(260px, 36vw, 440px);
    }

    .empty-chart {
        display: flex;
        align-items: center;
        justify-content: center;
        flex-direction: column;
        gap: 8px;
        padding: 16px 0 24px;
        color: #78889c;
        text-align: center;
    }

    .empty-icon {
        color: #9aabc0;
        font-size: 38px;
    }

    .empty-chart p {
        margin: 0;
        color: #53657b;
        font-size: 14px;
        font-weight: 700;
    }

    .empty-chart span {
        font-size: 12px;
        line-height: 1.7;
    }

    .chart-note {
        margin: 14px 0 0;
        color: #8a98a9;
        font-size: 11px;
        line-height: 1.7;
    }

    .history-list {
        display: flex;
        flex-direction: column;
    }

    .history-item {
        display: flex;
        align-items: center;
        justify-content: space-between;
        gap: 16px;
        padding: 20px 0;
        border-bottom: 1px solid #edf0f5;
    }

    .history-item:first-child {
        padding-top: 4px;
    }

    .history-item:last-child {
        padding-bottom: 4px;
        border-bottom: 0;
    }

    .history-main {
        flex: 1;
        min-width: 0;
    }

    .history-date {
        display: block;
        margin-bottom: 10px;
        color: #8190a3;
        font-size: 12px;
        overflow-wrap: anywhere;
    }

    .history-details {
        display: flex;
        align-items: center;
        flex-wrap: wrap;
        gap: 12px;
    }

    .history-temperature {
        font-size: 24px;
        font-weight: 800;
    }

    .history-temperature small {
        margin-left: 3px;
        font-size: 13px;
    }

    .condition-tag {
        padding: 6px 10px;
        border-radius: 7px;
        color: #4c6b93;
        background: #edf3fb;
        font-size: 12px;
        font-weight: 650;
    }

    .history-memo {
        margin: 10px 0 0;
        color: #68788d;
        font-size: 13px;
        line-height: 1.7;
        overflow-wrap: anywhere;
        white-space: pre-wrap;
    }

    .delete-button {
        flex-shrink: 0;
        padding: 8px 12px;
        border: 1px solid #e4e9f0;
        border-radius: 8px;
        color: #8a5960;
        background: #fff;
        font-size: 12px;
        cursor: pointer;
    }

    .delete-button:hover {
        border-color: #d7a5a9;
        background: #fff6f6;
    }

    .empty-history {
        padding: 28px 12px;
        border-radius: 10px;
        color: #8190a3;
        background: #f8fafc;
        font-size: 13px;
        text-align: center;
    }

    footer {
        padding: 0 0 12px;
        color: #8b98a9;
        font-size: 11px;
        line-height: 1.8;
        text-align: center;
    }

    footer p {
        margin: 0;
    }

    @media (max-width: 800px) {
        .summary-grid {
            grid-template-columns: repeat(2, minmax(0, 1fr));
            gap: 12px;
        }

        .summary-card {
            min-height: 100px;
            padding: 17px;
        }

        .form-grid {
            gap: 18px;
        }
    }

    @media (max-width: 520px) {
        main {
            padding: 16px 12px;
        }

        .page-header {
            margin-bottom: 20px;
        }

        .summary-grid {
            gap: 9px;
            margin-bottom: 16px;
        }

        .summary-card {
            min-height: 92px;
            padding: 13px;
            gap: 9px;
            border-radius: 12px;
        }

        .summary-label {
            font-size: 11px;
        }

        .summary-value {
            font-size: 25px;
        }

        .condition-value {
            font-size: 15px;
        }

        .panel {
            margin-bottom: 16px;
            padding: 18px 14px;
            border-radius: 14px;
        }

        .section-heading {
            align-items: flex-start;
        }

        .live-badge,
        .record-count {
            font-size: 10px;
        }

        .form-grid {
            grid-template-columns: minmax(0, 1fr);
        }

        .memo-field {
            grid-column: auto;
        }

        .chart-container {
            height: 280px;
        }

        .history-item {
            align-items: flex-start;
            gap: 10px;
        }

        .history-temperature {
            font-size: 22px;
        }
    }
</style>
