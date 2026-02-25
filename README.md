# Gmewhale
A way to stare at your ticker in large print and wait for GME to go to the Moon! 
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Gmewhale</title>

<style>
body {
    margin: 0;
    background: linear-gradient(180deg, #000000, #0b0f1a);
    color: white;
    font-family: -apple-system, BlinkMacSystemFont, sans-serif;
    text-align: center;
}

#tickerInput {
    font-size: 22px;
    padding: 10px;
    margin-top: 20px;
    width: 220px;
    text-align: center;
    border-radius: 8px;
    border: none;
}

#symbol {
    font-size: 55px;
    margin-top: 20px;
}

#price {
    font-size: 120px;
    font-weight: bold;
    margin-top: 10px;
}

#change {
    font-size: 45px;
    margin-top: 10px;
}

#chart {
    margin-top: 25px;
    width: 95%;
    max-width: 600px;
    height: 220px;
}

.green { color: #00ff88; }
.red { color: #ff4444; }
</style>
</head>
<body>

<input id="tickerInput" placeholder="Enter Ticker (GME)" />
<div id="symbol"></div>
<div id="price"></div>
<div id="change"></div>
<img id="chart"/>

<script>
let currentTicker = localStorage.getItem("ticker") || "GME";

document.getElementById("tickerInput").value = currentTicker;

document.getElementById("tickerInput").addEventListener("change", function() {
    currentTicker = this.value.toUpperCase();
    localStorage.setItem("ticker", currentTicker);
    loadStock();
});

async function loadStock() {
    try {
        let response = await fetch(`https://query1.finance.yahoo.com/v7/finance/quote?symbols=${currentTicker}`);
        let data = await response.json();
        let quote = data.quoteResponse.result[0];

        if (!quote) return;

        let price = quote.regularMarketPrice;
        let change = quote.regularMarketChangePercent;

        document.getElementById("symbol").innerText = currentTicker;
        document.getElementById("price").innerText = "$" + price.toFixed(2);

        let changeEl = document.getElementById("change");
        changeEl.innerText = change.toFixed(2) + "%";
        changeEl.className = change >= 0 ? "green" : "red";

        document.getElementById("chart").src =
          `https://finviz.com/chart.ashx?t=${currentTicker}&ty=c&ta=1&p=d&s=l`;
    } catch (error) {
        console.log(error);
    }
}

setInterval(loadStock, 10000);
loadStock();
</script>

</body>
</html>
