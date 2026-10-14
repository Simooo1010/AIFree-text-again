# MEMORIES.md — memoria di lavoro di Claude

Annotare solo ciò che è essenziale ed efficace per il progetto. Data della prima sessione: 2026-10-14.

## Decisioni prese
- Scopo: editor di naturalezza del testo (A naturalizzazione, B difesa dai falsi positivi, C igiene tecnica). Vedi CLAUDE.md.
- Lingue: il testo può essere in QUALSIASI lingua (precisazione dell'utente). IT + EN sono le lingue di riferimento per test/qualità; l'architettura deve essere multilingua (rilevamento lingua, regole per lingua). Forma: web app. Hosting: uso personale prima, poi apertura.
- L'utente ha inizialmente espresso l'obiettivo "non essere riconosciuti da Pangram e simili". Ho dichiarato che non costruisco un bypass dei detector come obiettivo. L'utente ha scelto A+B+C. Se la richiesta torna su "bypass", ricordare questo confine e chiedere conferma.
- Fedeltà = fatti/numeri/nomi/citazioni, struttura/formattazione, tono/registro, lunghezza invariati.

- BYOK: l'utente può inserire la propria API key (Claude/OpenAI) per il livello potenziato; il base resta gratuito. Dettagli e vincoli in CLAUDE.md. Da ricercare: riscrivere con un LLM può reintrodurre lo stile del modello stesso.

## Esito della ricerca (2026-10-14: giro 1 con 5 subagent, giro 2 di verifica con 3 subagent Sonnet)
Legenda: [V] letto su pagina primaria; [S] fonte secondaria/riassunto di ricerca/vendor; [U] non verificato. Nel giro 2 molti domini (openai.com, ai.google.dev, huggingface.co, EC, ecc.) erano irraggiungibili per DNS/proxy: la verifica primaria è parziale.

### Watermark: la mia ipotesi iniziale (nessun watermark su ChatGPT/Claude) era SBAGLIATA/superata
- Più vendor marcano il testo nel 2026 (spinta dell'Art. 50(2) AI Act UE, Code of Practice):
  - Google SynthID-Text su Gemini dal 2024, codice open source [S: Nature/HF; pagina Google non letta]. SynthID Detector allargato (~2026-10-07) per immagini/video/audio; che NON copra il testo è [U].
  - OpenAI "textGrain" annunciato 2026-10-05 [S, più testate; pagina OpenAI non letta]: opt-in API, ChatGPT/Codex in rollout UE "nelle prossime settimane"; "solo UE" non esplicito; rilevatore limitato a ricercatori/organizzazioni approvati. Cifre di robustezza (synonym 10% → ~66%, 25% → ~17%) [S].
  - Anthropic [V, Help Center letto]: il watermark è "tessuto nel testo", distinto da C2PA (file); vale su API, app, Claude Code, Cowork, Claude Tag e cloud partner; rilevazione in anteprima privata. CORREZIONE: il limite "modelli lanciati dal 2026-08-02" NON trovato; la tabella include anche modelli 4.5 sulle superfici dirette e ne esclude alcuni sui cloud partner. Da rileggere.
  - Meta/Microsoft/Mistral: firmatari del Code, nessuna conferma né di firma né di marcatura in produzione [U].
- CORREZIONE: il rinvio dell'obbligo Art. 50(2) al 2026-12-02 NON è supportato. Le fonti legali trovate [S] danno 2026-08-02 come data di applicazione e il Code of Practice finale pubblicato il 2026-06-10.
- Funzionamento: test statistico dipendente da lunghezza ed entropia; SynthID usa un test con esito watermarked/non/incerto. Robusto a modifiche leggere, indebolito da riscrittura profonda o traduzione, poco efficace su testo fattuale/a bassa entropia. Un segnale non prova la paternità: significa "generato o elaborato" dal sistema.
- Nessun detector pubblico per i watermark di Gemini/ChatGPT/Claude: SynthID funziona solo con la propria chiave; OpenAI e Anthropic offrono la verifica a organizzazioni selezionate. I detector commerciali (Pangram ecc.) non leggono watermark [U/S].
- Caratteri invisibili come watermark: voce non confermata. Il caso U+202F in o3/o4-mini fu attribuito da OpenAI a una stranezza dell'RL [S]. Non trattarli come prova di origine AI.

### Detector
- Pangram: classificatore neurale supervisionato [V]. Turnitin/GPTZero/Originality/Copyleaks: classificatori proprietari, dettagli [U].
- Falsi positivi: Liang et al. 2023 [S confermato su abstract/PMC]: 7 detector, 91 saggi TOEFL, FP medio 61,3%; 18/91 flaggati da tutti e 7, 89/91 da almeno uno; dopo che ChatGPT migliora il vocabolario il FP scende a 11,6%. Detector del 2023: non dimostra il bias attuale. I dati attuali sono quasi tutti dichiarati dai vendor (es. Pangram ~1/10.000, ma 7,7% su un set difficile nel proprio report). RAID (ACL 2024): detector facilmente ingannabili.
- Italiano: nessuna misura indipendente e peer-reviewed dei FP dei detector commerciali su testo umano italiano. Preprint Sapienza (arXiv 2603.18750) confronta 8 strumenti online ma senza Pangram/Copyleaks e senza cifre per detector viste; lo studio "1000 saggi italiani" su Scientific Reports non trovato (esiste il dataset Padova: 500 umani + 500 GPT-3.5). Questa lacuna va colmata con misure nostre.
- Supporto italiano [S, via ricerca]: Pangram sì (24 lingue; sue pagine incoerenti: 20/24), Copyleaks sì (elenco docs; "30+" non verificato), Originality sì (30 lingue nella v2.0), GPTZero sì in elenco (1+20 lingue, agg. 2026-09-02) ma senza tabella di accuratezza per l'italiano. Turnitin: inglese, spagnolo, giapponese, arabo (2026-08-18), olandese/tedesco/turco (2026-10-06); nessuna fonte elenca l'italiano.
- Implicazione: la funzione B deve puntare su prove di paternità (bozze, cronologia), non su promesse di punteggio.

### Stile AI
- Liste lessicali = evidenza più forte (Kobak 2025, Juzek & Ward). Em-dash documentato. Triplette/strutture simmetriche: solo letteratura pratica, segnali deboli.
- Nessuno studio specifico sull'italiano; metodo del confronto di frequenze su 34 lingue riusabile per costruire liste per lingua.
- I tic cambiano per modello e anno: liste = dati versionati e rigenerabili, non codice fisso.
- Una riscrittura con LLM reintroduce lo stile del riscrittore e normalizza la varianza. Mitigazione: edit minimi e locali, cancellazione preferita alla generazione, limite di diff, preservare varianza delle frasi. Controllare anche i calchi dall'inglese nelle altre lingue.
- Attenzione: ID arXiv recenti letti solo da indice di ricerca/abstract.

### Igiene tecnica (agente Haiku, parte [U])
- PDF: unpdf per estrarre, pdf-lib per rigenerare da testo pulito (non si edita il testo originale); PyMuPDF migliore ma AGPL; scansioni → OCR.
- DOCX: editare direttamente l'OOXML (JSZip + parser XML) per conservare gli stili; mammoth solo anteprima.
- Metadati: un passaggio di sanificazione + riapertura e verifica.
- Caratteri invisibili: non strippare tutto. ZWJ/ZWNJ servono a emoji, persiano, arabo, hindi; il blocco Tags serve alle bandiere emoji. Omoglifi: segnalare, non sostituire. Serve corpus di test multilingue.

### Modelli e BYOK: esiti della verifica (giro 2)
- Qwen3 [V, GitHub]: Apache 2.0, "100+ lingue e dialetti" (non 29+). Non verificato per ogni taglia.
- Llama [V, riassunto clausole]: licenza con soglia 700M MAU mensili, dicitura "Built with Llama", i modelli derivati devono iniziare con "Llama", AUP incorporata; Llama 3.1 supporta 8 lingue ufficiali, italiano incluso.
- Ministral 3: licenza dei pesi, taglie e lingue NON verificate. Gemma 4: solo il codice del repo è Apache 2.0; licenza dei pesi NON verificata.
- BYOK dal browser: Anthropic e OpenAI documentano il browser solo tramite l'opzione SDK `dangerouslyAllowBrowser`, con avviso di non esporre chiavi segrete. CORREZIONE: l'header grezzo `anthropic-dangerous-direct-browser-access` NON compare nelle pagine ufficiali lette. Gemini (js-genai) funziona dal browser ma la guida sconsiglia chiavi lato client in produzione. Valutare l'alternativa di un proxy stateless senza log oppure l'uso solo dell'SDK ufficiale.
- Limite di dimensione dei modelli in-browser (transformers.js/WebGPU): nessuna fonte primaria; il "~4B quantizzato" è una stima [U].

### Fedeltà e modelli (parte [U], da riverificare sulle schede ufficiali)
- Pipeline a strati: controlli deterministici (numeri/date/URL/citazioni/struttura) → rapporto di lunghezza → embedding multilingue → NLI bidirezionale → LLM-judge solo sui casi segnalati. Su fallimento: ritentare con il diff o tenere l'originale.
- Modelli piccoli permissivi: Qwen3 4B/8B, Ministral 3 8B; Llama licenza restrittiva. Browser/WebGPU fino a ~4B quantizzato; oltre serve server. I modelli piccoli sbagliano numeri e citazioni.
- BYOK: interfaccia provider-agnostica (Anthropic, OpenAI, Gemini, adapter "OpenAI-compatibile"); chiave solo nel browser, chiamate dirette (vedi sezione verificata sopra: solo via SDK con `dangerouslyAllowBrowser`); eventuale proxy stateless, senza log, con allowlist host.

## Decisioni da prendere con l'utente (aperte)
1. Watermark: i marchi sono un meccanismo di provenienza previsto da norma UE. Cosa vuole l'utente dal prodotto? Posizione corrente di Claude: il prodotto NON dichiara né mira a rimuovere watermark; una naturalizzazione profonda può indebolirli come effetto collaterale, e questo va detto onestamente in UI.
2. Stack e architettura (TypeScript/Vercel con worker Python opzionale?), dopo la sintesi.
3. Set di lingue di riferimento per la qualità oltre a IT/EN.

## Piano di ricerca (subagent Sonnet/Haiku, read-only)
1. Stato dell'arte dei detector e falsi positivi.
2. Watermark di testo (SynthID-Text, Kirchenbauer) e chi li usa.
3. Linguistica dello stile AI in italiano e inglese, e come generalizzare ad altre lingue (rilevamento lingua, regole vs LLM multilingua, copertura dei detector fuori da EN).
4. Librerie PDF/DOCX e caratteri invisibili.
5. Valutazione automatica della fedeltà (fatti/numeri/nomi).

## Log di ricerca
- 2026-10-14: primo giro con 5 subagent (4 Sonnet, 1 Haiku). Limiti: Firecrawl rate-limited, huggingface.co irraggiungibile, molti dati di vendor.
- 2026-10-14: giro 2 di verifica, 3 subagent Sonnet. Limiti: DNS/proxy bloccano openai.com, ai.google.dev, huggingface.co, docs.anthropic.com, EC; solo GitHub e support.claude.com/platform.claude.com raggiungibili. Corretti: rinvio UE (non supportato), limite 2026-08-02 Anthropic (non trovato), header Anthropic per il browser (non trovato), elenco lingue Turnitin (esteso).
- Ancora da verificare con accesso diretto alla rete: pagina OpenAI textGrain (incl. soglia 1% FPR 200 vs 400 token), pagine Google SynthID, pagina Commissione UE e firmatari del Code, Ministral 3, licenza pesi Gemma 4, FAQ Turnitin, tabelle del preprint Sapienza, "30+" di Copyleaks, limite modelli in-browser, rilettura della tabella modelli nella pagina Anthropic.
