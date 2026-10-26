---
name: move-it-workflow
description: Workflow passo passo per creare reel, ADV, video portfolio e motion graphics con Claude in HTML/CSS/JS: formati prima di tutto, storyboard statico, motion in bassa risoluzione, export finale. Risparmia token.
---

# move-it-workflow · motion video con Claude, una tappa alla volta

Il metodo nato da *AI Like to Move It, Move It*. Il video è una pagina HTML animata, con una timeline che si può fermare a qualsiasi istante. I fotogrammi vengono catturati da un browser e montati con ffmpeg.

Regola d'oro: **niente motion finché la grafica statica non è approvata, niente risoluzione piena finché la motion non è approvata.** Ogni tappa finisce con un output piccolo da approvare. Più l'errore si scopre presto, meno token costa correggerlo.

---

## Tappa 0 · Le domande (sempre, prima di tutto)

Chiedi con AskUserQuestion, in un solo giro, solo quello che non sai già:

1. **Che tipo di video?** Brand storytelling / social · ADV / promo · Portfolio / showcase · Demo di prodotto o UI · Explainer · Evento / annuncio · Altro.
2. **Quali formati servono?** 9:16 (1080×1920) · 4:5 (1080×1350) · 1:1 (1080×1080) · 16:9 (1920×1080) · 1.91:1 (1200×628). Qual è il **formato principale**? I formati decidono l'architettura: chiederli dopo costa una riprogettazione.
3. **Durata** (es. 6 · 15 · 20 · 30 s) ed eventuali versioni corte.
4. **Audio:** musica, voce, nessuno? Il video deve funzionare anche muto (sottotitoli, testo a schermo)?
5. **Asset e vincoli:** logo, font, palette, foto/video, claim **veri** utilizzabili, CTA, note legali.
6. **Consegna:** dove va pubblicato, peso massimo, loop sì/no, eventuale copertina (primo fotogramma).

Poi riassumi in 5 righe (tipo · formati · durata · audio · CTA) e fai confermare.

### Adatta il metodo al tipo di video
| Tipo | Cosa conta di più |
|---|---|
| Social / storytelling | Gancio nei primi 1–2 s, testo leggibile senza audio, 9:16 principale, ritmo serrato |
| ADV / promo | Solo claim veri forniti dal cliente, logo e CTA nel finale (end card ≥ 2 s), più formati, versioni 6/15/30 s |
| Portfolio / showcase | Inquadrature a tutto schermo e focus sugli asset, crediti, nessun dato riservato, struttura riutilizzabile come template |
| Demo prodotto / UI | UI vera (registrazione o ricostruzione fedele), cursore, zoom sui dettagli, una funzione per scena |
| Explainer | Una idea per scena, diagrammi semplici, testo minimo, tempi lunghi per leggere |

---

## Tappa 1 · Obiettivo e script

- **Una frase di obiettivo:** cosa deve succedere a chi guarda.
- **Script a tabella:** scena · durata (s) · testo a schermo · cosa si vede · **direzione**.
- La direzione è creativa, non tecnica: obiettivo, 3–4 parole di carattere (morbido/rotondo/spigoloso, lento/veloce, tante/poche pause, delicato/forte) e un riferimento "come…" (*come una superficie che riflette la luce*).
- La somma delle durate deve tornare con la durata richiesta.
- **Output:** solo testo. Si approva prima di disegnare.

## Tappa 2 · Design system minimo

Un file `tokens.css` con colori, font (self-hosted), dimensioni del testo, raggi, ombre, **easing e durate di riferimento**. Tutte le scene usano i token, così un cambio di colore o di ritmo è una riga.

Se esiste un progetto del brand (sito, CSS, JS), estrai da lì palette, font, easing e tono: il linguaggio visivo si trasferisce da un medium all'altro. Asset mancanti: placeholder espliciti con il nome atteso, mai immagini inventate.

## Tappa 3 · Storyboard statico (i frame, prima della motion)

- Per ogni scena disegna il **fotogramma chiave**, cioè lo stato finale della scena, come grafica statica, **solo nel formato principale**.
- Mettili tutti in **un solo file** `storyboard.html`: una griglia di frame numerati (S1, S2, …) con sotto la durata e la direzione.
- Fai uno screenshot dell'intera griglia (miniature di circa 270 px di larghezza) e mostra quello, non un'immagine per frame.
- Si itera qui su layout, gerarchia, testi, colori, crop delle immagini: correggere un frame statico costa pochissimo.
- **Gate:** non si passa alla motion finché lo storyboard non è approvato.

## Tappa 4 · Motion in bassa risoluzione

- Una sola pagina `scene-<formato>.html`, con il canvas al formato pieno (es. 1080×1920) e le scene in sequenza su **una timeline unica e deterministica** (vedi sotto).
- Anima a partire dai frame approvati: ingressi, transizioni, micro-movimenti, secondo la direzione di ogni scena.
- **Bozza a mezza risoluzione:** cattura a 0,5× (540×960 per il 9:16) e a 15 fps, per avere una bozza veloce da rivedere.
- Per controllare tu stesso: **contact sheet**. Una sola immagine con 8–12 fotogrammi piccoli a istanti scelti (inizio, metà e fine di ogni scena), invece di tanti screenshot grandi o del video intero.
- Mostra alla persona la bozza mp4 a bassa risoluzione e il contact sheet numerato.

## Tappa 5 · Iterare con intelligenza

- **Chiedi feedback puntuale:** scena + timecode + cosa deve *sentire* (es. "S3, 0:07, troppo lento, deve sembrare uno scatto"). Se il feedback è vago, proponi 2 alternative su quella sola scena.
- **Modifiche chirurgiche:** cambia solo i parametri o la scena interessata, con modifiche puntuali al file. Mai riscrivere tutta la pagina.
- **Ricontrolla solo quello che è cambiato:** cattura gli istanti della scena modificata, non tutto il video.
- Raggruppa più correzioni in un solo giro, quando la persona le dà insieme.
- Dopo due giri sulla stessa scena senza convergere, torna allo storyboard di quella scena: il problema è il frame, non la motion.

## Tappa 6 · Export finale e altri formati

1. **Formato principale a piena risoluzione:** 1080 px sul lato corto, 30 fps (60 solo se la motion è molto veloce e la piattaforma lo supporta).
2. **Altri formati:** non è un ritaglio, il layout **si ricompone**.
   - Stessa timeline e stessi token, cambiano solo le posizioni (CSS per formato o variabili sul contenitore).
   - Approva ogni formato con un contact sheet prima di renderizzarlo.
   - Durate identiche tra i formati, così restano sincronizzabili.
3. **Codifica:** H.264 `-crf 18–20`, `-pix_fmt yuv420p`, `-movflags +faststart`, keyframe ogni secondo (`-g 30`).
   - Audio AAC 128–192k, aggiunto alla fine con ffmpeg.
   - Senza audio: `-an`.
4. **QA:**
   - Safe area 9:16: niente testo importante nei 250 px in alto e nei 350 px in basso, coperti dall'interfaccia social.
   - Testo minimo circa 40 px sul canvas 1080.
   - Primo fotogramma usabile come copertina.
   - Loop pulito se richiesto.
   - Durata e peso nei limiti; claim e CTA identici a quelli approvati.

---

## Specifiche tecniche

### Timeline deterministica
Tutte le animazioni partono da una sola funzione che porta la pagina a un istante preciso:

```js
// tutte le animazioni create con element.animate(...) o in CSS
window.seek = (t) => {                       // t in secondi
  document.getAnimations().forEach(a => { a.pause(); a.currentTime = t * 1000 })
  window.onTime?.(t)                         // per sequenze di immagini, contatori, testi
}
window.DURATION = 20
```
Niente `setTimeout`, `requestAnimationFrame` libero o `Math.random()` non fissato: ogni fotogramma deve essere riproducibile.

### Cattura e montaggio
```js
// Playwright: viewport = formato; scale 0.5 per la bozza, 1 per il finale
for (let i = 0; i < DURATION * fps; i++) {
  await page.evaluate(t => window.seek(t), i / fps)
  await page.screenshot({ path: `frames/f${String(i).padStart(5, '0')}.jpg`, type: 'jpeg', quality: 92 })
}
```
```bash
ffmpeg -framerate 30 -i frames/f%05d.jpg -c:v libx264 -crf 19 -pix_fmt yuv420p -g 30 -movflags +faststart out.mp4
```
Prima di catturare, attendi `document.fonts.ready` e il decode di tutte le immagini. Per i video dentro la scena, usa sequenze di fotogrammi estratte con ffmpeg e aggiornate in `onTime`, così restano sincronizzati.

### Risparmio di token (riassunto)
- Le domande e lo script si chiudono **prima** di scrivere codice.
- Storyboard statico in un solo file, mostrato come una sola griglia.
- Token di design condivisi: i cambi globali sono una riga.
- Bozza motion a 0,5× e 15 fps. Per controllare, contact sheet piccoli invece di frame grandi o video interi.
- Modifiche puntuali ai file, mai riscritture. Non rileggere file lunghi già noti.
- Asset referenziati da file, non incollati in base64 nel codice sorgente.
- Rendering pieno una volta sola, a motion approvata. Gli altri formati solo dopo il principale.
- Una tappa per risposta. Alla fine di ogni risposta: cosa è fatto, cosa serve dalla persona, qual è la prossima tappa.

### Struttura della cartella
```
reel-<nome>/
  brief.md          tappa 0–1: risposte, obiettivo, script
  tokens.css        tappa 2
  assets/           logo, font, immagini (copie selezionate, mai interi progetti dei clienti)
  storyboard.html   tappa 3
  scene-9x16.html   tappa 4+ (una per formato)
  render.cjs        cattura fotogrammi
  export/           bozze e video finali
```
Nessun deploy e nessuna operazione git senza richiesta esplicita.
