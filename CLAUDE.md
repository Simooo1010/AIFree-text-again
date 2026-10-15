# CLAUDE.md — AIFree-text-again

Istruzioni per qualunque agente (editor o reader) che lavora in questo repo. Evolve con il progetto: aggiungere qui le nuove regole stabilite dall'utente.

## Stato
Fase di pianificazione e ricerca. **Nessuna implementazione finché l'utente non dà il via esplicito.**

## Scopo
App web che migliora la **naturalezza** dei testi AI-assistiti. Input: testo, PDF, DOCX. **Il testo può essere in qualsiasi lingua**: niente logica o regole hardcoded su una sola lingua; rilevare la lingua e usare regole/modelli per lingua, con IT ed EN come lingue di riferimento per test e qualità.

Tre funzioni:
- **A. Naturalizzazione stilistica**: tic AI (frasi fatte, simmetrie, vocabolario ricorrente, tono uniforme).
- **B. Difesa dai falsi positivi**: l'utente carica testo proprio, l'app mostra quali passaggi sembrano "AI" e perché, suggerisce ritocchi, aiuta a documentare la paternità.
- **C. Igiene tecnica**: caratteri invisibili (zero-width), spazi/punteggiatura anomali, metadati PDF/DOCX.

### Confine etico (non negoziabile)
- I punteggi dei detector (Pangram, GPTZero, ecc.) sono **solo una metrica interna di ricerca e verifica**. Non sono l'obiettivo di ottimizzazione né una promessa di prodotto.
- Non costruire né ottimizzare funzioni il cui scopo dichiarato è battere i detector in contesti valutativi (frode accademica o simili).
- Posizionamento: "editor di naturalezza del testo". Termini d'uso futuri vietano l'uso fraudolento.
- Se una richiesta futura sposta il progetto oltre questo confine, fermarsi e chiedere all'utente.

### Fedeltà
Dopo l'elaborazione restano invariati: fatti, numeri, nomi, citazioni; struttura e formattazione; tono e registro dell'autore; lunghezza approssimativa. Le parole sono libere di cambiare.

## Regole sui subagent (primordiali)
- **Sonnet o Haiku**: ricerca, attività read-only, attività leggere, a basso sforzo o a basso rischio.
- **Opus (o Fable se disponibile)**: costruzione, implementazione, verifica del codice e dell'app, fix degli errori trovati, cioè tutto ciò che richiede precisione.
- **Sforzo massimo: High.** Mai max né ultra (rapporto qualità/costo troppo basso).
- Parallelizzare i subagent indipendenti nello stesso turno.

## Preferenze di comunicazione
- Rispondere nella lingua dell'utente (oggi: italiano).
- Tono diretto, senza riempitivi. Segnalare apertamente le falle logiche invece di assecondare.
- Per chiarire il contesto, usare AskUserQuestion.

## Modello di servizio: BYOK
- Livello base gratuito: regole deterministiche + eventuali modelli piccoli/open source (llama, gemma, qwen).
- Livello potenziato opzionale: l'utente inserisce la propria API key (Anthropic, OpenAI, ecc.). Il costo resta suo.
- Vincoli: la chiave non va mai loggata né salvata sul server per default (preferire browser/sessione); interfaccia provider-agnostica; dichiarare in UI che il testo viene inviato al provider; il prompt di sistema descrive il compito onestamente (editing di naturalezza).
- Un modello più grande non garantisce qualità: conta la pipeline (analisi, riscrittura mirata, verifica di fedeltà), da misurare.

## Architettura e stack (decisa, nessun codice ancora)
- **Principio:** tutto il trattamento del testo avviene nel browser (Web Worker). Testo e chiavi BYOK restano sul dispositivo. Vercel serve file statici e, solo più avanti, un proxy stateless senza log.
- **Stack:** TypeScript strict, pnpm workspaces + Turborepo, Vitest + fast-check, Zod. Frontend Vite + React (SPA statica), IndexedDB (Dexie) per la cronologia. Formati: unpdf (estrazione PDF), pdf-lib (rigenerazione da testo pulito), tesseract.js (OCR opt-in), JSZip + @xmldom/xmldom (patch OOXML), mammoth solo per anteprima. Python solo offline in `tools/`.
- **Layout:** `packages/{core,hygiene,style,fidelity,rewrite,providers,formats,cli}`, `packs/<lingua>/<semver>/`, `apps/web`, `apps/proxy`, `tools/{lexgen,eval}`, `corpora/` (solo manifest e licenze). Il motore è una libreria testabile con CLI prima dell'interfaccia web.
- **Pipeline:** ingest → analisi (lingua per blocco, span protetti) → igiene → analisi di stile (nessun "punteggio AI") → proposte di modifica con budget di diff (default ≤15%) → gate di fedeltà (span protetti identici, lunghezza, embedding, NLI, giudice solo sui casi segnalati; al fallimento un retry, poi si tiene l'originale) → controllo anti-stile-del-riscrittore → revisione utente → export con sanificazione metadati e verifica dopo la riapertura.
- **Liste lessicali:** dati versionati e rigenerabili per lingua, non codice fisso.
- **Livello gratuito iniziale:** solo regole. Modelli (WebLLM, endpoint locale, BYOK) dalla fase P6.
- **PDF:** per scritture che richiedono shaping (arabo, indiane) non rigenerare con pdf-lib; esportare in DOCX o con la stampa PDF del browser.
- **Paternità (funzione B):** catena di hash locale esportabile. Valore probatorio debole, da dichiarare onestamente in UI. Timestamp esterno rinviato.
- **Corpus di valutazione:** solo fonti con licenza libera (testi umani pre-2022, testi AI generati da noi). Niente testi donati né API commerciali dei detector per ora.
- **Regole non negoziabili:** nessuna fase usa un punteggio di detector come input o obiettivo; i detector vivono solo in `tools/eval/detectors` (opt-in, manuale, mai in CI, mai nei prompt né nella UI). Nessuna funzione né promessa di rimozione dei watermark: la UI dice che una riscrittura profonda può indebolirli come effetto collaterale. Gli span protetti (numeri, date, URL, citazioni, nomi) non hanno bypass.
- **Roadmap:** P0 monorepo e CLI skeleton → P1 igiene e corpus dei casi limite → P2 motore di stile e pack IT/EN → P3 span protetti e fedeltà → P4 DOCX/PDF/OCR/metadati → P5 web app local-first → P6 BYOK e modelli → P7 pipeline lessici e valutazione → P8 lancio pubblico. Dettagli in MEMORIES.md.

## Decisioni aperte
- Ricontrollo con accesso diretto alla rete dei dati non verificati (vedi MEMORIES.md, Log di ricerca).
- Via libera esplicito dell'utente per iniziare la fase P0.
- Hosting: prima uso personale, poi apertura al pubblico.

## Git
Sviluppo sul branch `claude/gifted-curie-nuvgf5`. Commit chiari, push con `git push -u origin <branch>`, PR in bozza.
