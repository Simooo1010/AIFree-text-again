# MEMORIES.md — memoria di lavoro di Claude

Annotare solo ciò che è essenziale ed efficace per il progetto. Data della prima sessione: 2026-10-14.

## Decisioni prese
- Scopo: editor di naturalezza del testo (A naturalizzazione, B difesa dai falsi positivi, C igiene tecnica). Vedi CLAUDE.md.
- Lingue: il testo può essere in QUALSIASI lingua (precisazione dell'utente). IT + EN sono le lingue di riferimento per test/qualità; l'architettura deve essere multilingua (rilevamento lingua, regole per lingua). Forma: web app. Hosting: uso personale prima, poi apertura.
- L'utente ha inizialmente espresso l'obiettivo "non essere riconosciuti da Pangram e simili". Ho dichiarato che non costruisco un bypass dei detector come obiettivo. L'utente ha scelto A+B+C. Se la richiesta torna su "bypass", ricordare questo confine e chiedere conferma.
- Fedeltà = fatti/numeri/nomi/citazioni, struttura/formattazione, tono/registro, lunghezza invariati.

- BYOK: l'utente può inserire la propria API key (Claude/OpenAI) per il livello potenziato; il base resta gratuito. Dettagli e vincoli in CLAUDE.md. Da ricercare: riscrivere con un LLM può reintrodurre lo stile del modello stesso.

## Esito della ricerca (2026-10-14, 5 subagent). Fonti: report degli agenti, da ricontrollare sulle fonti primarie prima di decidere
Legenda: [V] letto su pagina primaria dall'agente; [S] secondario/vendor; [U] non verificato.

### Watermark: la mia ipotesi iniziale era SBAGLIATA/superata
- Nel 2026 più vendor marcano il testo (spinta dell'Art. 50(2) AI Act UE, Code of Practice):
  - Google SynthID-Text su Gemini dal 2024 [V], codice open source.
  - OpenAI "textGrain" annunciato 2026-10-05 [V]: opt-in API, ChatGPT/Codex solo utenti UE, in arrivo.
  - Anthropic [V, Help Center]: modelli lanciati dal 2026-08-02 marcati in modo "imperceptible" nel testo, non metadati né caratteri nascosti.
  - Meta/Microsoft/Mistral: firmatari del Code, nessuna conferma di marcatura in produzione [U].
- Obbligo Art. 50(2) rinviato al 2026-12-02 per sistemi già sul mercato: aspettarsi altri vendor.
- Funzionamento: test statistico dipendente da lunghezza ed entropia; SynthID usa un test con esito watermarked/non/incerto. Robusto a modifiche leggere, indebolito da riscrittura profonda o traduzione, poco efficace su testo fattuale/a bassa entropia. Un segnale non prova la paternità: significa "generato o elaborato" dal sistema.
- Nessun detector pubblico per i watermark di Gemini/ChatGPT/Claude: SynthID funziona solo con la propria chiave; OpenAI e Anthropic offrono la verifica a organizzazioni selezionate. I detector commerciali (Pangram ecc.) non leggono watermark [U/S].
- Caratteri invisibili come watermark: voce non confermata. Il caso U+202F in o3/o4-mini fu attribuito da OpenAI a una stranezza dell'RL [S]. Non trattarli come prova di origine AI.

### Detector
- Pangram: classificatore neurale supervisionato [V]. Turnitin/GPTZero/Originality/Copyleaks: classificatori proprietari, dettagli [U].
- Falsi positivi: Liang et al. 2023, 61,3% medio su 91 saggi TOEFL con detector 2023 [V]; i dati attuali sono quasi tutti dichiarati dai vendor (es. Pangram ~1/10.000, ma 7,7% su un set difficile nel proprio report). RAID (ACL 2024): detector facilmente ingannabili.
- Italiano: nessuna misura indipendente dei FP dei detector commerciali. Supporto dichiarato: Pangram sì (24 lingue), Copyleaks sì, Turnitin no; GPTZero/Originality non confermati.
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

### Fedeltà e modelli (parte [U], da riverificare sulle schede ufficiali)
- Pipeline a strati: controlli deterministici (numeri/date/URL/citazioni/struttura) → rapporto di lunghezza → embedding multilingue → NLI bidirezionale → LLM-judge solo sui casi segnalati. Su fallimento: ritentare con il diff o tenere l'originale.
- Modelli piccoli permissivi: Qwen3 4B/8B, Ministral 3 8B; Llama licenza restrittiva. Browser/WebGPU fino a ~4B quantizzato; oltre serve server. I modelli piccoli sbagliano numeri e citazioni.
- BYOK: interfaccia provider-agnostica (Anthropic, OpenAI, Gemini, adapter "OpenAI-compatibile"); chiave solo nel browser, chiamate dirette (Anthropic richiede un header dedicato per il browser; altri da verificare); eventuale proxy stateless, senza log, con allowlist host.

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
- 2026-10-14: primo giro con 5 subagent (4 Sonnet, 1 Haiku). Limiti: Firecrawl rate-limited, huggingface.co irraggiungibile, molti dati di vendor. Da fare: ricontrollare sulle fonti primarie le affermazioni su cui basiamo decisioni (watermark OpenAI/Anthropic, supporto lingue dei detector, schede dei modelli).
