<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover">
<meta name="theme-color" content="#141516">
<meta name="apple-mobile-web-app-capable" content="yes">
<title>Ahorra Quest</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Baloo+2:wght@500;700;800&display=swap" rel="stylesheet">
<style>
  :root {
    --bg: #141516;
    --panel: #1f2123;
    --panel-2: #2a2d30;
    --line: #34383c;
    --text: #f1f3f2;
    --muted: #8d9693;
    --green: #3ddc6a;
    --green-dark: #1f9c47;
    --gold: #ffd24a;
    --red: #ff6b5e;
  }
  * { box-sizing: border-box; -webkit-tap-highlight-color: transparent; }
  html, body { margin: 0; background: var(--bg); color: var(--text); }
  body {
    font-family: "Baloo 2", "Trebuchet MS", system-ui, sans-serif;
    min-height: 100vh;
    padding: env(safe-area-inset-top, 0px) 16px calc(env(safe-area-inset-bottom, 0px) + 28px);
    display: flex; justify-content: center;
    background: radial-gradient(120% 60% at 50% 0%, #22262a 0%, var(--bg) 60%);
  }
  .app { width: 100%; max-width: 440px; padding-top: 14px; }

  /* Cabecera */
  .top { display: flex; align-items: center; justify-content: space-between; gap: 12px; }
  .stage-badge { background: var(--panel); border: 2px solid var(--line); border-radius: 14px; padding: 6px 14px; line-height: 1.1; }
  .stage-badge small { display: block; color: var(--muted); font-size: 12px; font-weight: 500; }
  .stage-badge b { font-size: 20px; font-weight: 800; color: var(--gold); }
  .goal-pct { text-align: right; }
  .goal-pct small { display: block; color: var(--muted); font-size: 12px; }
  .goal-pct b { font-size: 24px; font-weight: 800; }

  /* Zona principal: torre vertical + personaje */
  .hero { display: grid; grid-template-columns: 92px 1fr; gap: 16px; margin-top: 16px; align-items: stretch; }
  .tower-col { display: flex; flex-direction: column; align-items: center; gap: 6px; }
  .flag { font-size: 14px; font-weight: 700; color: var(--gold); text-align: center; line-height: 1.1; max-width: 92px; word-break: break-word; }
  .base { font-size: 13px; color: var(--muted); }
  .tower {
    position: relative; width: 78px; flex: 1; min-height: 340px;
    background: #0c0d0e; border: 3px solid var(--line); border-radius: 26px; overflow: hidden;
  }
  .fill {
    position: absolute; left: 0; right: 0; bottom: 0; height: 0%;
    background: linear-gradient(90deg, var(--green-dark) 0%, var(--green) 35%, #8dffaa 52%, var(--green) 68%, var(--green-dark) 100%);
    box-shadow: 0 0 22px rgba(61,220,106,.7);
    transition: height 1s cubic-bezier(.2,.9,.25,1);
  }
  .fill::before {
    content: ""; position: absolute; left: 0; right: 0; top: 0; height: 10px;
    background: rgba(255,255,255,.45); border-radius: 50% / 100% 100% 0 0;
  }
  .tick { position: absolute; left: 0; right: 0; height: 0; border-top: 2px dashed rgba(255,255,255,.28); z-index: 2; }
  .tick span { position: absolute; right: 6px; bottom: 2px; font-size: 11px; font-weight: 700; text-shadow: 0 1px 3px rgba(0,0,0,.9); }

  .side { display: flex; flex-direction: column; align-items: center; justify-content: space-between; text-align: center; }
  .bubble {
    position: relative; background: var(--text); color: #17191a; font-weight: 700;
    padding: 10px 14px; border-radius: 18px; font-size: 16px; line-height: 1.2;
    min-height: 58px; width: 100%; display: flex; align-items: center; justify-content: center;
  }
  .bubble::after {
    content: ""; position: absolute; bottom: -10px; left: 50%; margin-left: -10px;
    border: 10px solid transparent; border-top-color: var(--text); border-bottom: 0;
  }
  .bubble.pop { animation: pop .35s ease-out; }
  @keyframes pop { 0% { transform: scale(.85); } 60% { transform: scale(1.05); } 100% { transform: scale(1); } }
  #mascot { width: 150px; height: 150px; margin-top: 10px; cursor: pointer; overflow: visible; }
  #mascot .body { transform-origin: 50% 90%; animation: idle 2.4s ease-in-out infinite; }
  @keyframes idle { 0%,100% { transform: translateY(0) scaleY(1); } 50% { transform: translateY(-5px) scaleY(1.03); } }
  #mascot.happy .body { animation: jump .55s ease-out 2; }
  @keyframes jump { 0% { transform: translateY(0); } 40% { transform: translateY(-26px) rotate(-6deg); } 70% { transform: translateY(0) rotate(4deg); } 100% { transform: translateY(0); } }
  #mascot.sad .body { animation: none; transform: translateY(4px) scaleY(.96); }
  .mouth-happy, .mouth-sad, .eyes-sad { display: none; }
  #mascot.happy .mouth-idle, #mascot.sad .mouth-idle { display: none; }
  #mascot.happy .mouth-happy { display: block; }
  #mascot.sad .mouth-sad, #mascot.sad .eyes-sad { display: block; }
  #mascot.sad .eyes-idle { display: none; }
  .shadow { width: 100px; height: 12px; border-radius: 50%; background: rgba(0,0,0,.45); margin-top: -6px; }
  .money small { display: block; color: var(--muted); font-size: 13px; margin-top: 8px; }
  .money b { font-size: 36px; font-weight: 800; line-height: 1; display: block; }
  .money .missing { color: var(--muted); font-size: 14px; margin-top: 4px; }

  /* Paneles */
  .panel { background: var(--panel); border: 2px solid var(--line); border-radius: 20px; padding: 16px; margin-top: 18px; }
  .panel h2 { margin: 0 0 10px; font-size: 18px; font-weight: 700; }
  .panel-head { display: flex; align-items: center; justify-content: space-between; gap: 8px; margin-bottom: 6px; }
  .panel-head h2 { margin: 0; }
  button {
    font-family: inherit; font-weight: 800; font-size: 18px; color: var(--text);
    background: var(--panel-2); border: 2px solid var(--line); border-radius: 14px;
    padding: 12px 4px; cursor: pointer; touch-action: manipulation;
  }
  button:active { transform: translateY(2px); }
  button:focus-visible, input:focus-visible, textarea:focus-visible { outline: 3px solid var(--gold); outline-offset: 2px; }
  .quick { display: grid; grid-template-columns: repeat(4, 1fr); gap: 8px; }
  .quick button { border-bottom-width: 5px; }
  input, textarea {
    font-family: inherit; font-weight: 700; font-size: 18px; color: var(--text);
    background: #0c0d0e; border: 2px solid var(--line); border-radius: 14px; padding: 10px 14px; width: 100%; min-width: 0;
  }
  .row { display: flex; gap: 8px; margin-top: 10px; }
  .go { background: var(--green); color: #082712; border-color: #8dffa9; border-bottom: 5px solid var(--green-dark); padding: 10px 18px; }
  .out { background: transparent; color: var(--red); border-color: #5a3532; padding: 10px 14px; }
  .small-btn { font-size: 14px; padding: 6px 12px; color: var(--muted); background: transparent; }

  .history { list-style: none; margin: 0; padding: 0; }
  .history li { display: flex; justify-content: space-between; align-items: baseline; gap: 10px; padding: 9px 2px; border-bottom: 1px solid var(--line); font-size: 16px; }
  .history li:last-child { border-bottom: 0; }
  .history .what { display: flex; flex-direction: column; min-width: 0; }
  .history .note { font-weight: 700; overflow-wrap: anywhere; }
  .history .when { color: var(--muted); font-size: 13px; }
  .plus { color: var(--green); font-weight: 800; white-space: nowrap; }
  .minus { color: var(--red); font-weight: 800; white-space: nowrap; }
  .empty { color: var(--muted); font-size: 15px; margin: 0; }

  .field { display: flex; align-items: center; justify-content: space-between; gap: 10px; font-size: 15px; color: var(--muted); margin-top: 10px; }
  .field:first-of-type { margin-top: 0; }
  .field input { flex: 0 0 130px; font-size: 17px; padding: 8px 12px; }
  .field input.wide { flex: 0 0 160px; }
  .backup { display: grid; grid-template-columns: 1fr 1fr; gap: 8px; margin-top: 14px; }
  .backup button { font-size: 15px; padding: 10px 6px; }
  .hint { color: var(--muted); font-size: 13px; line-height: 1.3; margin: 10px 0 0; }
  #backupBox { display: none; margin-top: 10px; font-size: 13px; height: 110px; resize: none; }
  #applyBackup { display: none; width: 100%; margin-top: 8px; font-size: 15px; padding: 10px; }
  .reset { width: 100%; margin-top: 12px; font-size: 14px; color: var(--
