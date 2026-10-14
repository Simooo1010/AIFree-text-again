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

## Decisioni aperte
- Ruolo dell'AI nella pipeline (API vs locale vs ibrido): decidere dopo la ricerca.
- Stack e architettura: dopo la ricerca.
- Hosting: prima uso personale, poi apertura al pubblico.

## Git
Sviluppo sul branch `claude/gifted-curie-nuvgf5`. Commit chiari, push con `git push -u origin <branch>`, PR in bozza.
