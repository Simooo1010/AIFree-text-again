# MEMORIES.md — memoria di lavoro di Claude

Annotare solo ciò che è essenziale ed efficace per il progetto. Data della prima sessione: 2026-10-14.

## Decisioni prese
- Scopo: editor di naturalezza del testo (A naturalizzazione, B difesa dai falsi positivi, C igiene tecnica). Vedi CLAUDE.md.
- Lingue: il testo può essere in QUALSIASI lingua (precisazione dell'utente). IT + EN sono le lingue di riferimento per test/qualità; l'architettura deve essere multilingua (rilevamento lingua, regole per lingua). Forma: web app. Hosting: uso personale prima, poi apertura.
- L'utente ha inizialmente espresso l'obiettivo "non essere riconosciuti da Pangram e simili". Ho dichiarato che non costruisco un bypass dei detector come obiettivo. L'utente ha scelto A+B+C. Se la richiesta torna su "bypass", ricordare questo confine e chiedere conferma.
- Fedeltà = fatti/numeri/nomi/citazioni, struttura/formattazione, tono/registro, lunghezza invariati.

## Ipotesi da verificare nella ricerca (NON ancora verificate)
- SynthID-Text è usato solo sui modelli Google? ChatGPT e Claude non applicano watermark statistici pubblici? Se vero, il "timbro" è stile, non watermark.
- I detector (Pangram, GPTZero, Turnitin) sono classificatori di stile, non lettori di watermark.
- Falsi positivi più alti sui non madrelingua inglesi (Liang et al., 2023): verificare e cercare studi su Pangram e sull'italiano.
- Riscrittura profonda necessaria per rompere un watermark = tensione con la fedeltà.

## Piano di ricerca (subagent Sonnet/Haiku, read-only)
1. Stato dell'arte dei detector e falsi positivi.
2. Watermark di testo (SynthID-Text, Kirchenbauer) e chi li usa.
3. Linguistica dello stile AI in italiano e inglese, e come generalizzare ad altre lingue (rilevamento lingua, regole vs LLM multilingua, copertura dei detector fuori da EN).
4. Librerie PDF/DOCX e caratteri invisibili.
5. Valutazione automatica della fedeltà (fatti/numeri/nomi).

## Log di ricerca
(vuoto)
