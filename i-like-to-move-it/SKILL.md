---
name: i-like-to-move-it
description: Crea "The Motion Dictionary", un playground HTML per esplorare e imparare le animazioni di base: scegli un movimento, piega i parametri, copia il CSS o il prompt per Claude.
---

# i-like-to-move-it · The Motion Dictionary

Claude può costruire anche gli strumenti per **esplorare e imparare** la motion. Questa skill crea un playground interattivo delle animazioni di base, in un unico file HTML che funziona offline.

**Riferimento:** `motion-dictionary.html`, in questa cartella, è la versione mostrata al talk. Aprilo prima di iniziare e usalo come modello di struttura, tono e qualità. Non copiarlo così com'è: adatta contenuti, identità visiva e lingua a chi lo chiede.

## Struttura (quattro sezioni, una sola pagina)

1. **Hero:** cos'è, in tre passi: *Scegli un movimento · Piega i parametri · Copia CSS o prompt*.
2. **Dizionario:** "Sedici movimenti di base". Una griglia di carte, ognuna con anteprima live in loop, nome, una frase semplice e una categoria, filtrabili per categoria:
   - *Entrata:* Fade, Slide, Scale, Pop, Blur, Wipe
   - *Enfasi:* Bounce, Rotate, Shake, Flip, Squash & Stretch
   - *Loop:* Float, Pulse
   - *Testo e sequenze:* Stagger, Typewriter, Text Rise
   
   Clic su una carta: si apre nel Lab.
3. **Lab:** "Piega il movimento". Selettore del movimento, palco grande con l'anteprima, pannello parametri:
   - *tempo:* durata, ritardo, avanti e indietro (alternate);
   - *easing* con curva disegnata: Linear, Ease, Ease-in, Ease-out, Ease-in-out, Smooth, Expo, Back, Anticipation, Steps, La tua. Ognuno ha una frase che ne descrive la personalità;
   - *movimento:* parametri specifici (direzione, distanza, scala, gradi, stagger…).
   
   Sotto: il **CSS** generato e il **prompt per Claude** che descrive lo stesso movimento a parole, ciascuno col suo pulsante Copia. Spazio per rilanciare l'animazione.
4. **Easing:** "Stesso viaggio, carattere diverso". Lo stesso movimento con curve diverse, fianco a fianco.
5. **Glossario:** "Le parole per chiederlo". I termini (easing, overshoot, stagger, keyframe, loop…) spiegati in una frase, per saper chiedere a Claude quello che si ha in mente.

Nella barra in alto: navigazione tra le sezioni e un interruttore **Slow-mo** (×0.25) per vedere i movimenti al rallentatore.

## Regole

- **Un solo file HTML:** CSS e JS inline, font inclusi o di sistema, nessuna libreria o CDN, funziona offline.
- **Animazioni leggere:** solo `transform` e `opacity` (più `filter` dove serve, come in Blur), Web Animations API o keyframe CSS.
- **Accessibilità:** rispetta `prefers-reduced-motion` (anteprime ferme, si avviano a richiesta), contrasti leggibili, tutto usabile da tastiera.
- **Linguaggio semplice:** ogni movimento spiega *cosa comunica* e *quando usarlo*, non solo cosa fa.
- **Personalizzazione:** chiedi se usare colori, font ed elemento firma del brand. Il dizionario diventa un piccolo design system della motion.
- **Verifica:** apri la pagina in un browser e controlla ogni carta, ogni preset di easing e i pulsanti Copia prima di consegnarla.
