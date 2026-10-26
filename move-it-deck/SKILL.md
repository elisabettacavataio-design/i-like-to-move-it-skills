---
name: "move-it-deck"
description: "Crea presentazioni animate in HTML (Vite + React, canvas 1920×1080, step a click, video locali, offline) come 'AI Like to Move It'. Usala per talk e deck in motion."
---

# move-it-deck · presentazioni animate in HTML

Una presentazione in cui ogni slide è in motion, costruita come una piccola app web e consegnata come **un solo file `deck/index.html` + una cartella `deck/media/`**. Si apre con un doppio clic, funziona offline, si comanda col clicker.

## 0 · Prima di scrivere codice

Chiedi (una domanda alla volta, con AskUserQuestion se disponibile) solo ciò che non sai:
1. **Talk**: titolo, durata, pubblico, lingua (titoli e testi possono avere lingue diverse).
2. **Scaletta**: atti e slide, cosa deve *succedere* in ognuna (non solo cosa deve esserci).
3. **Identità visiva**: palette, font (vanno self-hosted), un elemento firma (es. una stellina), cosa evitare.
4. **Media**: quali video/immagini esistono già, quali no. Quelli mancanti diventano **placeholder espliciti** con il nome file atteso: non inventare mai immagini di progetti o risultati attribuiti alla persona.
5. **Vincoli**: cartella autonoma, nessun deploy, nessuna operazione git senza richiesta, solo copie selezionate degli asset dei clienti, crediti/disclaimer su materiali di terzi.

Poi proponi la struttura e lavora **una slide alla volta**, mostrando uno screenshot dopo ogni modifica.

## 1 · Struttura del progetto

```
progetto/
  package.json          scripts: dev · build · present
  vite.config.js        vite-plugin-singlefile, publicDir:false, outDir:'deck', emptyOutDir:false
  index.html            root di sviluppo
  src/
    main.jsx            precarica i font (document.fonts.load) poi monta <Deck/>
    engine/
      Deck.jsx          canvas, tastiera, step, transizioni, chrome, prefetch
      Media.jsx         video/immagini/placeholder, poster, sync, prefetch
      Orbs.jsx          sfondo persistente tra le slide
      ui.jsx            Star, Rise, Rv, Pad, cx, useTick, useAfter, useCountUp…
    content/
      slides.js         ordine slide: { id, title, act, component, steps, theme, transition }
      copy.js           TUTTI i testi (+ CREDITS per slide)
      media.js          registro media: id → { type, file, autoplay, muted, loop, delay, sync }
    slides/act1.jsx…    componenti slide, uno per atto
    styles/             base.css (token, font) · motion.css · deck.css · act1..N.css
    fonts/              woff2 locali (vengono inlinati)
  deck/
    index.html          BUILD: JS, CSS e font inline
    media/              video, immagini, posters/, versions/
  SPEAKER-NOTES.md      per ogni slide: click [N], tempi, cosa dire
  README.md
```

Versioni bloccate (es. `vite@5.4.10`, `react@18.3.1`). In `vite.config.js` servi `/media` da `deck/media` (sirv) in dev e definisci `__BUILD__` con data e ora: mostralo nel pannello di aiuto (tasto `?`) per capire al volo quale build è aperta.

## 2 · Il motore (Deck)

- **Canvas fisso 1920×1080** scalato con `transform: scale()` per stare nella finestra. Tutte le misure in px sul canvas.
- **Stato `{ i, s }`**: slide e step. → avanza lo step, poi la slide; ← torna indietro. Hash `#N.S` per ricaricare dove eri.
- **Tastiera**: frecce, Spazio, PagSu/PagGiù (i clicker), numero+Invio, G indice, F fullscreen, B nero, H nasconde il chrome, K/P/R/M play, replay e audio del video, ? aiuto.
- **Solo la slide attiva è montata** (più quella in uscita durante la transizione). Transizioni: fade, rise, zoom, curtain, cut.
- **Chrome**: atto in basso a sinistra; contatore, puntini degli step e crediti in basso a destra.
- **Step governati dalla speaker**: ogni rivelazione importante va su un click (`step >= n`), non su un timer. I timer (`useAfter`) solo per micro-animazioni d'ingresso.

## 3 · Media

- `<Media id="…"/>` legge il registro. Se il file manca mostra un **placeholder** con etichetta e percorso atteso: basta copiare il file con quel nome, senza ricompilare.
- Video: `preload="auto"`, `playsInline`, autoplay all'ingresso (con `delay` opzionale), pausa all'uscita. Se il browser blocca l'audio, riparti muto e mostra «premi M».
- **Poster** automatico `media/posters/<nome>.jpg` (primo fotogramma): niente riquadro nero all'avvio.
- **Prefetch**: monta in un contenitore invisibile i media della slide precedente e delle due successive (associati per prefisso della chiave, es. `s08-…`).
- **Sync**: i video con lo stesso `sync` si riallineano al primo del gruppo se scartano più di 0,15 s. Per mostrare parti diverse di due video senza che si incrocino: stessa durata e uno sfasato di mezzo giro.
- **Codifica per la proiezione** (ffmpeg): H.264, `-crf 22`, 30 fps, `-g 30` (un keyframe al secondo), `-movflags +faststart`; verticali a 720×1280, orizzontali a 1280–1600 px. Togli l'audio dove non serve (`-an`). Non ricodificare se il file diventa più pesante.

## 4 · Motion: regole che funzionano

- Classi d'ingresso riutilizzabili (`.a-up`, `.a-fade`, `.a-pop`, `.a-blur`) con ritardo in `--d`.
- **Attenzione**: un'animazione con `fill-mode: both` sovrascrive `opacity`/`transform` statici dello stesso elemento. Metti lo stato su un wrapper.
- Una maschera `overflow:hidden` (come un `Rise`) taglia tutto ciò che sporge: decorazioni fuori dalla maschera.
- Per gli elementi che seguono un tracciato usa `offset-path` + `offset-distance` (senza margini negativi: usa `offset-anchor: center`).
- Per un morph tra layout: anima `left/top/transform` di ogni elemento separatamente, non cambiare `display` o `flex-direction`.
- Niente `filter: blur` su elementi grandi e persistenti: usa `radial-gradient` per le luci. `will-change: transform` solo dove serve.
- Ogni cambio deve sembrare una conseguenza: un segnale visivo (mirino, ghost del formato precedente, contatore che scorre) rende leggibile la trasformazione.
- `prefers-reduced-motion`: durate a 0,01 ms e nessun loop.
- Niente corsivo né effetti cartoon se l'identità non li prevede; niente testo che anticipa lo step successivo.

## 5 · Verifica (sempre, dopo ogni modifica)

- `npm run build`, poi Playwright (Chromium) su `deck/index.html#N.S`: screenshot a più istanti dell'animazione, montati in un'unica immagine e **guardati**.
- Giro completo a freddo (profilo nuovo): nessun errore in console; per ogni slide `video.readyState` e `paused`; misura i tempi di partenza dei video con ritardo.
- Controlla misure reali con `getBoundingClientRect` quando qualcosa "deve stare sotto" o "seguire" qualcos'altro.

## 6 · Consegna

- Lavora nella cartella dell'utente; se costruisci altrove, copia i file modificati e **verifica con lo sha** che siano arrivati uguali.
- Aggiorna `SPEAKER-NOTES.md` (numero di click per slide, tempi, cosa dire) e il README (come sostituire un media, rigenerare un poster, gestire crediti e versioni).
- Riferisci data e ora della build.
- Il giorno dell'evento: copiare tutta `deck/`, aprire `index.html` con Chrome/Edge, F per lo schermo intero, fare un giro veloce di tutte le slide per scaldare la cache, provare audio e clicker.

## 7 · Extra che fanno la differenza

- **Rewind delle versioni**: salva uno screenshot a ogni iterazione importante (`media/versions/`) e mostra in loop una slide che torna indietro fino alla prima. Racconta il processo meglio di qualsiasi testo.
- **Crediti**: un piccolo disclaimer accanto al numero di slide solo dove servono (`CREDITS` in `copy.js`).
- **Reel e prototipi** per le slide-vetrina si possono costruire con lo stesso metodo: pagina HTML 1080×1920, animazioni Web Animations API controllate dal tempo, frame catturati con Playwright e montati con ffmpeg.