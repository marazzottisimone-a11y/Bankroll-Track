<!DOCTYPE html>  
<html>  
<head>  
  <meta charset="UTF-8">  
  <meta name="viewport" content="width=device-width, initial-scale=1.0">  
  <title>Bankroll Tracker</title>  
  <style>  
    body { font-family: Arial; margin: 20px; background:#f5f5f5; }  
    h1, h3 { text-align:center; }  
    input, select, button { padding:10px; margin:5px 0; width:100%; font-size:16px; }  
    .bet { border:1px solid #ddd; padding:10px; margin:5px 0; border-radius:5px; background:white; }  
    .profit { color:green; }  
    .loss { color:red; }  
    #history { max-height:300px; overflow-y:auto; }  
    .container { max-width:400px; margin:auto; }  
  </style>  
</head>  
<body>  
  <div class="container">  
    <h1>🎲 Bankroll Tracker</h1>  
    <h3>Saldo iniziale: <span id="startBankroll">50</span>€</h3>  
    <h3>Saldo attuale: <span id="currentBankroll">50</span>€</h3>  
    <h3>Profitto oggi: <span id="profit">0</span>€</h3>  
      
    <h3>Nuova giocata</h3>  
    <label>Gioco</label>  
    <select id="game">  
      <option>Roulette</option>  
      <option>Scommesse sportive</option>  
      <option>Blackjack</option>  
      <option>Altro</option>  
    </select>  
    <label>Puntata €</label>  
    <input type="number" id="bet" value="1">  
    <label>Vincita €</label>  
    <input type="number" id="win" value="0">  
    <label>Session rating 🙂 😐 😵</label>  
    <select id="rating">  
      <option>🙂</option>  
      <option>😐</option>  
      <option>😵</option>  
    </select>  
    <button onclick="saveBet()">Salva giocata</button>  
      
    <h3>Storico giocate</h3>  
    <div id="history"></div>  
  </div>  
  
<script>  
  // Parametri e limiti  
  let startBankroll = 50;  
  let bankroll = 50;  
  let bets = [];  
  const LIMITS = { "Roulette":10, "Scommesse sportive":2, "Blackjack":5, "Altro":5 };  
  const STOP_LOSS = -10;  
  const TAKE_PROFIT = 5;  
  const MAX_HOURS = 1;  
  let startTime = new Date();  
  
  function updateDisplay() {  
    document.getElementById('currentBankroll').innerText = bankroll.toFixed(2);  
    document.getElementById('profit').innerText = (bankroll - startBankroll).toFixed(2);  
      
    // Aggiorna storico  
    let html = '';  
    bets.forEach(bet => {  
      let cls = bet.profit>=0 ? 'profit' : 'loss';  
      html += `<div class="bet ${cls}">${bet.date} - ${bet.game} - Puntata: ${bet.bet}€ - Vincita: ${bet.win}€ - Profitto: ${bet.profit}€ - Rating: ${bet.rating}</div>`;  
    });  
    document.getElementById('history').innerHTML = html;  
  }  
  
  function saveBet() {  
    let game = document.getElementById('game').value;  
    let betAmount = parseFloat(document.getElementById('bet').value);  
    let winAmount = parseFloat(document.getElementById('win').value);  
    let rating = document.getElementById('rating').value;  
      
    // Controllo numero massimo di giocate per tipo  
    let count = bets.filter(b => b.game===game).length;  
    if(count >= LIMITS[game]) {  
      alert(`Hai raggiunto il limite giornaliero di giocate per ${game}`);  
      return;  
    }  
  
    let profitBet = winAmount - betAmount;  
    bankroll += profitBet;  
  
    // Controllo stop loss / take profit  
    if(bankroll - startBankroll <= STOP_LOSS) {  
      alert(`STOP LOSS raggiunto! Fermati per oggi.`);  
    } else if(bankroll - startBankroll >= TAKE_PROFIT) {  
      alert(`TAKE PROFIT raggiunto! Fermati per oggi.`);  
    }  
  
    // Controllo tempo massimo  
    let elapsedHours = (new Date() - startTime)/1000/3600;  
    if(elapsedHours >= MAX_HOURS) {  
      alert(`Tempo massimo di gioco raggiunto! Fermati.`);  
    }  
  
    // Salva la giocata nella sessione  
    bets.push({  
      date: new Date().toLocaleTimeString(),  
      game: game,  
      bet: betAmount,  
      win: winAmount,  
      profit: profitBet,  
      rating: rating  
    });  
  
    updateDisplay();  
  }  
  
  updateDisplay();  
</script>  
</body>  
</html>  
