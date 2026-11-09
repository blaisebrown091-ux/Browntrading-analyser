# Browntrading-analyser
trade analyser 
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Trading Chart Analyzer · Buy/Sell Signals</title>
    <!-- Chart.js for the candlestick chart (simulated) and line chart -->
    <script src="https://cdn.jsdelivr.net/npm/chart.js@4.4.0/dist/chart.umd.min.js"></script>
    <!-- Font Awesome for icons -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0-beta3/css/all.min.css">
    <style>
        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
            font-family: 'Inter', -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
        }

        body {
            background: #0b0e14;
            color: #e6e9ef;
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            padding: 20px;
        }

        .container {
            max-width: 1200px;
            width: 100%;
            background: #151a22;
            border-radius: 32px;
            box-shadow: 0 20px 40px rgba(0, 0, 0, 0.6), 0 0 0 1px rgba(255, 255, 255, 0.03);
            padding: 28px;
            transition: all 0.2s;
        }

        /* Header */
        .header {
            display: flex;
            flex-wrap: wrap;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 24px;
        }

        .header h1 {
            font-weight: 600;
            font-size: 1.9rem;
            letter-spacing: -0.5px;
            background: linear-gradient(135deg, #ffffff 0%, #b0c2e0 100%);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
            display: flex;
            align-items: center;
            gap: 12px;
        }

        .header h1 i {
            background: #2a3a5a;
            padding: 10px;
            border-radius: 16px;
            color: #7aa9f5;
            font-size: 1.5rem;
            -webkit-text-fill-color: #7aa9f5;
        }

        .badge {
            background: #1f2a3a;
            padding: 8px 16px;
            border-radius: 100px;
            font-size: 0.9rem;
            font-weight: 500;
            color: #8ca3c7;
            border: 1px solid #2e3e54;
        }

        .badge i {
            margin-right: 6px;
            color: #e6b04c;
        }

        /* Chart area */
        .chart-wrapper {
            background: #0f141c;
            border-radius: 24px;
            padding: 20px 15px 10px 15px;
            margin-bottom: 28px;
            border: 1px solid #232d3b;
            box-shadow: inset 0 2px 8px rgba(0, 0, 0, 0.5);
        }

        .chart-header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            padding: 0 8px 12px 8px;
            color: #9aaec9;
            font-size: 0.9rem;
            letter-spacing: 0.3px;
        }

        .pair {
            font-weight: 600;
            color: #d6e2f5;
            font-size: 1.1rem;
        }

        .pair span {
            background: #1f2d3f;
            padding: 4px 12px;
            border-radius: 40px;
            margin-left: 8px;
            font-size: 0.8rem;
            color: #7aa9f5;
            border: 1px solid #2e405b;
        }

        .timeframe {
            display: flex;
            gap: 8px;
            color: #7c8ea8;
        }

        .timeframe span {
            background: #1d2632;
            padding: 4px 10px;
            border-radius: 30px;
            cursor: default;
            border: 1px solid #2b3648;
        }

        .timeframe .active {
            background: #2a4b7a;
            color: white;
            border-color: #4e7ab0;
        }

        canvas {
            display: block;
            width: 100%;
            height: auto;
            max-height: 380px;
        }

        /* Analysis panel */
        .analysis-grid {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 20px;
            margin-bottom: 24px;
        }

        @media (max-width: 700px) {
            .analysis-grid {
                grid-template-columns: 1fr;
            }
        }

        .card {
            background: #10161f;
            border-radius: 22px;
            padding: 22px 20px;
            border: 1px solid #202b39;
            box-shadow: 0 6px 12px rgba(0, 0, 0, 0.2);
            transition: transform 0.1s ease;
        }

        .card:hover {
            border-color: #2e405b;
        }

        .card-title {
            font-size: 1rem;
            text-transform: uppercase;
            letter-spacing: 1px;
            color: #7c91ad;
            margin-bottom: 16px;
            display: flex;
            align-items: center;
            gap: 10px;
        }

        .card-title i {
            color: #e6b04c;
            font-size: 1.1rem;
            width: 22px;
        }

        .indicator-row {
            display: flex;
            justify-content: space-between;
            padding: 12px 0;
            border-bottom: 1px solid #1d2531;
        }

        .indicator-row:last-child {
            border-bottom: none;
        }

        .indicator-label {
            color: #9aaec9;
            font-weight: 400;
        }

        .indicator-value {
            font-weight: 600;
            color: #e3eaf5;
        }

        .bullish {
            color: #4cd97b;
        }

        .bearish {
            color: #f25f5c;
        }

        .neutral {
            color: #f7c35c;
        }

        .recommendation-box {
            background: #1a2330;
            border-radius: 18px;
            padding: 24px 22px;
            margin-top: 10px;
            display: flex;
            align-items: center;
            justify-content: space-between;
            border-left: 6px solid #4cd97b;
            transition: all 0.3s;
        }

        .recommendation-box.sell-signal {
            border-left-color: #f25f5c;
            background: #221a1f;
        }

        .recommendation-box.buy-signal {
            border-left-color: #4cd97b;
            background: #16231d;
        }

        .rec-text {
            display: flex;
            flex-direction: column;
            gap: 6px;
        }

        .rec-action {
            font-size: 2rem;
            font-weight: 700;
            letter-spacing: -0.5px;
            line-height: 1.2;
        }

        .rec-detail {
            font-size: 0.9rem;
            color: #9aaec9;
        }

        .rec-icon {
            font-size: 3rem;
            opacity: 0.8;
        }

        .summary-line {
            display: flex;
            gap: 20px;
            flex-wrap: wrap;
            margin-top: 18px;
            font-size: 0.9rem;
            color: #9aaec9;
        }

        .summary-line span i {
            margin-right: 6px;
            color: #6f8bb0;
        }

        .footer-note {
            text-align: center;
            font-size: 0.8rem;
            color: #5f718a;
            margin-top: 18px;
            border-top: 1px solid #1f2a36;
            padding-top: 18px;
            letter-spacing: 0.3px;
        }

        .footer-note i {
            margin-right: 6px;
            color: #e6b04c;
        }
    </style>
</head>
<body>
    <div class="container">
        <!-- Header -->
        <div class="header">
            <h1>
                <i class="fas fa-chart-line"></i> 
                TradeSight
            </h1>
            <div class="badge">
                <i class="fas fa-bolt"></i> Real-time interpretation & signal
            </div>
        </div>

        <!-- Chart Wrapper -->
        <div class="chart-wrapper">
            <div class="chart-header">
                <div class="pair">
                    BTC / USD <span>Bitcoin</span>
                </div>
                <div class="timeframe">
                    <span>1H</span>
                    <span class="active">4H</span>
                    <span>1D</span>
                    <span>1W</span>
                </div>
            </div>
            <!-- Chart canvas -->
            <canvas id="tradeChart" width="1000" height="380"></canvas>
        </div>

        <!-- Analysis cards and recommendation -->
        <div class="analysis-grid">
            <!-- Left Card: Technical Indicators -->
            <div class="card">
                <div class="card-title">
                    <i class="fas fa-calculator"></i> Technical Interpretation
                </div>
                <div class="indicator-row">
                    <span class="indicator-label">RSI (14)</span>
                    <span class="indicator-value" id="rsiValue">58.4</span>
                </div>
                <div class="indicator-row">
                    <span class="indicator-label">MACD (12,26,9)</span>
                    <span class="indicator-value bullish" id="macdValue">Bullish crossover</span>
                </div>
                <div class="indicator-row">
                    <span class="indicator-label">Moving Avg (50 vs 200)</span>
                    <span class="indicator-value bullish" id="maValue">Golden cross (50>200)</span>
                </div>
                <div class="indicator-row">
                    <span class="indicator-label">Bollinger Bands</span>
                    <span class="indicator-value neutral" id="bbValue">Price near upper band</span>
                </div>
                <div class="indicator-row">
                    <span class="indicator-label">Volume trend</span>
                    <span class="indicator-value bullish" id="volumeValue">Rising (bullish)</span>
                </div>
            </div>

            <!-- Right Card: Market Structure -->
            <div class="card">
                <div class="card-title">
                    <i class="fas fa-chart-bar"></i> Market Structure
                </div>
                <div class="indicator-row">
                    <span class="indicator-label">Support level</span>
                    <span class="indicator-value" id="supportLevel">$67,200</span>
                </div>
                <div class="indicator-row">
                    <span class="indicator-label">Resistance level</span>
                    <span class="indicator-value" id="resistanceLevel">$69,800</span>
                </div>
                <div class="indicator-row">
                    <span class="indicator-label">Trend (4H)</span>
                    <span class="indicator-value bullish" id="trendValue">Uptrend</span>
                </div>
                <div class="indicator-row">
                    <span class="indicator-label">Candle pattern</span>
                    <span class="indicator-value" id="patternValue">Bullish engulfing</span>
                </div>
                <div class="indicator-row">
                    <span class="indicator-label">Volatility</span>
                    <span class="indicator-value neutral" id="volatilityValue">Moderate</span>
                </div>
            </div>
        </div>

        <!-- Recommendation Box -->
        <div class="recommendation-box buy-signal" id="recommendationBox">
            <div class="rec-text">
                <div class="rec-action" id="recAction">BUY</div>
                <div class="rec-detail" id="recDetail">Entry: $68,200 – $68,500 · Target: $72,000 · Stop: $66,800</div>
            </div>
            <div class="rec-icon">
                <i class="fas fa-arrow-trend-up" id="recIcon"></i>
            </div>
        </div>

        <!-- Summary line with confidence and signal source -->
        <div class="summary-line">
            <span><i class="fas fa-check-circle"></i> Confidence: <strong id="confidenceValue">High (82%)</strong></span>
            <span><i class="fas fa-clock"></i> Updated: <strong>just now</strong></span>
            <span><i class="fas fa-chart-pie"></i> Signal: <strong id="signalSource">Technical + Momentum</strong></span>
        </div>

        <div class="footer-note">
            <i class="fas fa-info-circle"></i> For educational purposes only. Always do your own research before trading.
        </div>
    </div>

    <script>
        (function() {
            // ---------- CHART SETUP ----------
            const ctx = document.getElementById('tradeChart').getContext('2d');

            // Generate simulated price data that shows a bullish trend (for illustration)
            // We'll create 30 closing prices with upward drift and some noise
            const labels = [];
            const closePrices = [];
            const highPrices = [];
            const lowPrices = [];
            const openPrices = [];

            // start at 65500
            let base = 65500;
            for (let i = 0; i < 30; i++) {
                // date labels (last 30 hours / 4h intervals)
                const d = new Date();
                d.setHours(d.getHours() - (30 - i) * 4);
                labels.push(d.getHours() + ':00');

                // random walk with upward bias
                const change = (Math.random() - 0.35) * 220; // slightly positive bias
                base = base + change;
                if (base < 64000) base = 64000 + Math.random() * 500;
                if (base > 72000) base = 71500 - Math.random() * 400;

                const open = base + (Math.random() - 0.5) * 100;
                const close = base + (Math.random() - 0.5) * 100;
                const high = Math.max(open, close) + Math.random() * 150;
                const low = Math.min(open, close) - Math.random() * 150;

                closePrices.push(close);
                highPrices.push(high);
                lowPrices.push(low);
                openPrices.push(open);
            }

            // Ensure the last few candles are bullish to match analysis
            closePrices[27] = 67800;
            closePrices[28] = 68200;
            closePrices[29] = 69050;
            // adjust high/low accordingly
            highPrices[28] = 68500;
            lowPrices[28] = 67600;
            highPrices[29] = 69300;
            lowPrices[29] = 68100;
            openPrices[29] = 68300;

            // We'll use a line chart to represent close price, but also add a small "candle" style via line and points?
            // For simplicity and clarity, we'll do a line chart with fill — looks professional.
            // Also overlay a moving average for extra flavor.
            const ma50 = []; // simple moving average
            for (let i = 0; i < closePrices.length; i++) {
                if (i < 5) ma50.push(null);
                else {
                    let sum = 0;
                    for (let j = i - 4; j <= i; j++) sum += closePrices[j];
                    ma50.push(sum / 5);
                }
            }

            // Chart configuration
            const chart = new Chart(ctx, {
                type: 'line',
                data: {
                    labels: labels,
                    datasets: [
                        {
                            label: 'BTC/USD',
                            data: closePrices,
                            borderColor: '#4f9cf7',
                            backgroundColor: 'rgba(79, 156, 247, 0.08)',
                            borderWidth: 3,
                            pointRadius: 0,
                            pointHoverRadius: 6,
                            pointHoverBackgroundColor: '#ffffff',
                            pointHoverBorderColor: '#4f9cf7',
                            pointHoverBorderWidth: 2,
                            fill: true,
                            tension: 0.2,
                        },
                        {
                            label: 'MA (5)',
                            data: ma50,
                            borderColor: '#e6b04c',
                            borderWidth: 2,
                            borderDash: [5, 5],
                            pointRadius: 0,
                            fill: false,
                            tension: 0.2,
                        }
                    ]
                },
                options: {
                    responsive: true,
                    maintainAspectRatio: true,
                    plugins: {
                        legend: {
                            display: true,
                            labels: {
                                color: '#b0c2e0',
                                usePointStyle: true,
                                pointStyle: 'line',
                                boxWidth: 20,
                                padding: 15,
                            }
                        },
                        tooltip: {
                            mode: 'index',
                            intersect: false,
                            backgroundColor: '#1e2a3a',
                            titleColor: '#e6e9ef',
                            bodyColor: '#b0c2e0',
                            borderColor: '#2e405b',
                            borderWidth: 1,
                            callbacks: {
                                label: function(context) {
                                    let label = context.dataset.label || '';
                                    if (label) label += ': ';
                                    if (context.parsed.y !== null) {
                                        label += '$' + context.parsed.y.toLocaleString(undefined, {maximumFractionDigits: 0});
                                    }
                                    return label;
                                }
                            }
                        }
                    },
                    scales: {
                        x: {
                            grid: {
                                color: '#1f2a36',
                                drawBorder: false,
                            },
                            ticks: {
                                color: '#7c91ad',
                                maxTicksLimit: 12,
                            }
                        },
                        y: {
                            grid: {
                                color: '#1f2a36',
                                drawBorder: false,
                            },
                            ticks: {
                                color: '#7c91ad',
                                callback: function(value) {
                                    return '$' + value.toLocaleString();
                                }
                            }
                        }
                    },
                    elements: {
                        line: {
                            borderJoinStyle: 'round',
                        }
                    }
                }
            });

            // ---------- ANALYSIS AND RECOMMENDATION ----------
            // We'll set the interpretation dynamically based on a simple scoring system.
            // This is a simulation—in a real app, you'd fetch live data or use an API.

            // DOM elements
            const rsiEl = document.getElementById('rsiValue');
            const macdEl = document.getElementById('macdValue');
            const maEl = document.getElementById('maValue');
            const bbEl = document.getElementById('bbValue');
            const volumeEl = document.getElementById('volumeValue');
            const supportEl = document.getElementById('supportLevel');
            const resistanceEl = document.getElementById('resistanceLevel');
            const trendEl = document.getElementById('trendValue');
            const patternEl = document.getElementById('patternValue');
            const volatilityEl = document.getElementById('volatilityValue');

            const recBox =
