# Specifiche funzionali

**Prodotto di lavoro:** pianificatore di viaggi web (Django) e app mobile  
**Riferimento analizzato:** [Wanderlog](https://wanderlog.com/) (Travelchime Inc.)  
**Versione documento:** 1.1  
**Data:** 7 ottobre 2026  
**Stato:** bozza aggiornata dopo esplorazione dell’editor autenticato

Questo documento descrive cosa deve fare il prodotto. Non è un clone del marchio, dei testi, delle guide o del codice di Wanderlog. Le funzioni sono ricostruite da pagine pubbliche, help center e FAQ, e vanno reimplementate con contenuti, dati e identità propri.

---

## 1. Scopo

Realizzare un unico sistema per pianificare un viaggio dall’idea al rientro:

- itinerario giorno per giorno e mappa nello stesso schermo;
- luoghi, prenotazioni, documenti, budget e checklist in un solo piano;
- collaborazione in tempo reale tra compagni di viaggio;
- guide e suggerimenti per scegliere cosa vedere;
- accesso da browser e da app, con le stesse informazioni sincronizzate.

Il sito è l’applicazione Django (interfaccia web e API). L’app mobile consuma la stessa API.

## 2. Fonti e limiti dell’analisi

Fonti usate:

- homepage e pagine prodotto (`/`, `/trip-planner-ai`, `/trip-planner-mobile-app`, `/hotels`, `/extension`, `/pro`);
- pagine destinazione e itinerari pubblici (esempio: Paris trip planner e itinerari di più giorni);
- [FAQ](https://wanderlog.com/blog/faq/);
- [Help Center](https://help.wanderlog.com/hc/en-us).

Il 7 ottobre 2026 l’editor è stato percorso da un account già autenticato, interfaccia in italiano, su un viaggio di prova “Viaggio a Paris” (1–4 ottobre, nessun luogo ancora inserito). Dove l’editor contraddice le FAQ, vale l’editor.

Limiti rimasti:

- non sono stati salvati luoghi, spese o prenotazioni, per non modificare il viaggio;
- la scheda di un luogo già inserito (orari, foto, mezzo della tratta) non è stata aperta;
- la pagina Offerte in navigazione non è stata aperta;
- non sono stati ispezionati contratti, privacy policy completi né il codice sorgente.

## 3. Cosa fa Wanderlog

Wanderlog è un pianificatore gratuito, con un livello Pro, disponibile su web, iOS e Android, più un’estensione Chrome. Il piano di viaggio è un documento collaborativo affiancato a una mappa.

Funzioni osservate:

| Area | Comportamento |
| --- | --- |
| Piano e diario | Due viste dello stesso viaggio. Il piano è panoramica, itinerario e budget. Il diario è il percorso dei luoghi visitati, con foto, note e linee sulla mappa |
| Piano di viaggio | Date, copertina, sezioni riordinabili, note, salvataggio automatico, annulla e ripeti. Visibilità: pubblico, amici (follower reciproci), privato (proprietario e chi ha il link). Default osservato: amici |
| Luoghi | Liste con ricerca, luoghi consigliati, copia e sposta multiplo tra liste e giorni. Scheda pubblica con foto, categoria, voto e recensioni |
| Itinerario | Un blocco per giorno, sottotitolo, “Compila giorno” per destinazione, ottimizzazione Pro |
| Mappa del piano | Google Maps affiancata all’editor. Ricerca, livelli per liste e giorni, hotel, export Pro, apertura della zona in Google Maps |
| Percorsi | Trasporto predefinito del viaggio: “auto + camminata breve” oppure “trasporto pubblico + camminata breve”. Ottimizzazione Pro |
| Prenotazioni | Tipi: volo, alloggio, auto, ristorante, treno, autobus, traghetto, crociera, allegato. Inoltro a un indirizzo `trip+…` del viaggio, con controllo dello stato di import. Sincronizzazione Gmail riservata a Pro |
| Alloggi | Ricerca che confronta più siti, Airbnb incluso, e dichiara di non ordinare in base alle commissioni |
| Budget | Totale a zero finché non ci sono spese. Budget con valuta (euro sull’account italiano) e importo. Spesa con valuta, collegamento a un elemento, pagatore, divisione e data opzionale |
| Checklist | Voce aggiungibile dentro una lista o un giorno, insieme a luogo e nota |
| Guide | Nell’editor, carosello Esplora: guide Wanderlog, guide della community, Tripadvisor, Lonely Planet, Google e pagine web. Un viaggio si può convertire in guida senza cancellarlo |
| Diario | Copertina, riepilogo, anno e durata. Fermate scelte sulla mappa, con foto e note. Condivisione distinta dal piano. Eliminare il diario elimina anche il piano |
| Collaborazione | Invito via link, email o utente. Ruoli “può modificare” e “solo lettura”. Gestione compagni |
| AI | Pannello nel viaggio: esempi legati alla destinazione, allegato, nuova chat, avviso di inaccuratezza. 10 messaggi gratuiti, poi invito a Pro |
| Offline e Pro | Paywall osservato: prova di 7 giorni, poi 39,99 USD/anno, un solo piano annuale, rinnovo automatico salvo disdetta almeno 24 ore prima |
| Home autenticata | Viaggi recenti e in arrivo, ricerca alloggi con date e ospiti, mappa dei luoghi già visitati, creazione guida, guide consigliate, cambio lingua |
| Altro | Stampa, modalità sola lettura, comprimi sezioni, supporto in chat, estensione Chrome promossa dentro il piano con prova di 7 giorni |

Lacune ancora utili come estensioni, dopo l’editor:

- totale unico che converte spese in valute diverse: il form ha la valuta per spesa, il riepilogo convertito non è stato visto;
- doppio conteggio di pacchetti volo + hotel nel budget, segnalato dalle recensioni;
- bici come mezzo: nel viaggio comparivano solo i due preset auto/trasporto pubblico con camminata breve;
- meteo previsto sul giorno;
- vista calendario del piano;
- attività senza un luogo;
- abbonamento mensile: nel paywall web c’è solo l’annuale;
- campi treno europei (carrozza, posto): il tipo treno c’è, il dettaglio del form no.

Struttura dell’editor, vista sul viaggio vuoto:

- barra: stato “Salvato”, annulla, ripeti, selettore Piano / Diario, condividi, menu del viaggio;
- menu del viaggio: impostazioni, diario, consigli, domande frequenti, sola lettura, stampa, comprimi tutte le sezioni;
- impostazioni: privacy, trasporto predefinito, converti in guida (il viaggio resta), elimina viaggio;
- colonna sinistra: Esplora, Note, liste luoghi (una nasce “Senza titolo”), Itinerario, Budget, indice dei giorni;
- ogni lista o giorno accetta luogo, nota e checklist; le liste mostrano luoghi consigliati della città;
- giorno: sottotitolo, compila giorno (chiede la destinazione), ottimizza Pro, inserisci giorno dopo, elimina, colore;
- selezione multipla: copia in, sposta in, elimina;
- mappa a destra, vuota finché non ci sono luoghi, con ricerca, livelli, hotel ed export Pro;
- home: viaggi recenti, ricerca alloggi, mappa “luoghi visitati” con conteggio paesi e città, crea guida.

## 4. Prodotto da costruire

### 4.1 Principi

1. Un piano di viaggio è la fonte unica: luoghi, giorni, prenotazioni, file, spese e liste vivono lì.
2. Web e app mostrano gli stessi dati. Una modifica su un canale compare sull’altro.
3. Si può iniziare a pianificare prima di registrarsi. Il salvataggio duraturo richiede un account.
4. Le funzioni di pianificazione di base restano gratuite e senza limite di viaggi o di compagni.
5. Pro sblocca automazioni e uso in viaggio (offline, ottimizzazione, import automatico, AI senza quota stretta, allegati ampi).
6. L’interfaccia è in italiano e in inglese fin dalla prima versione pubblica.
7. I contenuti delle guide sono originali o concessi in licenza. Non si copiano testi, foto o itinerari di Wanderlog.

### 4.2 Canali

| Canale | Ruolo | Fase |
| --- | --- | --- |
| Sito Django | Marketing, guide, ricerca alloggi, editor del viaggio, impostazioni, area admin | 1 |
| API REST | Contratto unico per web e app | 1 |
| App iOS e Android | Stesso editor, ottimizzata per l’uso in viaggio e offline | 2 |
| Estensione browser | Salvare un luogo da una pagina web nel viaggio aperto | 3 |

L’app della fase 2 è un client nativo o multipiattaforma (Flutter o React Native) sull’API Django. La scelta del framework mobile è tecnica e non cambia queste specifiche.

### 4.3 Attori

| Attore | Può |
| --- | --- |
| Visitatore | Leggere guide, cercare destinazioni, iniziare una bozza di viaggio |
| Utente registrato | Possedere viaggi, invitare, gestire account e abbonamento |
| Compagno con modifica | Modificare il piano condiviso |
| Compagno in sola lettura | Vedere il piano, senza modificarlo |
| Autore di guide | Pubblicare una guida o un itinerario |
| Staff editoriale | Curare destinazioni, luoghi in evidenza, guide ufficiali |
| Amministratore | Moderare, gestire utenti, piani, segnalazioni |
| Sistema | Import email, calcolo percorsi, suggerimenti, notifiche, stato voli |

## 5. Mappa delle aree

```text
Account e profilo
    └── Viaggi
            ├── Sezioni e note
            ├── Luoghi e liste
            ├── Itinerario giornaliero
            ├── Mappa e percorsi
            ├── Prenotazioni e allegati
            ├── Budget e spese
            ├── Checklist
            ├── Collaboratori
            └── Assistente AI
Guide, destinazioni, road trip, domande
Ricerca e confronto alloggi
Abbonamento Pro
Notifiche
Offline (app)
```

## 6. Modello informativo

Entità principali e relazioni. I nomi sono concettuali, non lo schema Django definitivo.

**Utente**  
email, nome, foto, lingua, unità di misura (km o miglia), valuta predefinita, fuso orario, piano (free o pro), caselle email collegate.

**Viaggio**  
titolo, copertina, data inizio, data fine, fuso del viaggio, visibilità (`privato`, `link`, `follower`, `pubblico`), valuta di riferimento, budget totale opzionale, proprietario, archiviato sì/no.

**Destinazione del viaggio**  
una o più località (città, regione, parco) con ordine. Un road trip è un viaggio con molte destinazioni in sequenza.

**Giorno**  
data, titolo opzionale, nota, ordine. I giorni si generano dalle date e si possono aggiungere fuori intervallo.

**Sezione**  
appartenente al viaggio, tipo (`lista luoghi`, `nota`, `checklist`, `itinerario`, `prenotazioni`, `budget`), titolo, colore, icona, ordine, visibile sì/no. L’itinerario e le prenotazioni di sistema non si cancellano; le liste utente sì.

**Luogo**  
identificativo esterno (provider mappe), nome, coordinate, indirizzo, categorie, descrizione breve, foto, sito, telefono, orari, voto medio, numero recensioni, fonte. Il luogo è un’anagrafica condivisa; il collegamento al viaggio è a parte.

**Voce luogo**  
luogo + viaggio, nota personale, foto sostitutiva, orario inizio, orario fine, costo opzionale, stato (`idea`, `previsto`, `fatto`). Può stare in più liste e in un giorno.

**Tratta**  
tra due voci consecutive di un giorno: mezzo (`auto`, `a piedi`, `trasporto pubblico`, `bici`, `taxi`, `volo`, `treno`, `traghetto`), durata, distanza, geometria opzionale.

**Prenotazione**  
tipo (`volo`, `alloggio`, `auto`, `treno`, `bus`, `traghetto`, `evento`, `altro`), stato, date, conferma, fornitore, prezzo, valuta, email sorgente, campi specifici (numero volo, aeroporti, gate; check-in e check-out; ritiro e riconsegna).

**Allegato**  
file o link, collegato a viaggio o prenotazione, nome, tipo MIME, dimensione.

**Spesa**  
importo, valuta, data, categoria, pagatore, partecipanti, regola di divisione (`parti uguali`, `quote`, `importo fisso`), collegamento opzionale a luogo o prenotazione, esclusa dal budget sì/no (per evitare i doppioni dei pacchetti).

**Voce checklist**  
testo, spuntata, assegnatario opzionale, quantità opzionale.

**Guida**  
titolo, autore, destinazione, testo, copertina, stato editoriale, luoghi citati, giorni se è un itinerario, contatori di salvataggi.

**Invito**  
viaggio, email o link, ruolo (`modifica`, `lettura`), stato.

**Abbonamento**  
provider di pagamento, piano, rinnovo, stato.

## 7. Requisiti funzionali

Gli identificatori servono per backlog e test. Priorità: **P1** indispensabile per il primo rilascio web, **P2** completo per la parità utile, **P3** estensione.

### 7.1 Account

| ID | Priorità | Requisito |
| --- | --- | --- |
| ACC-01 | P1 | Registrazione con email e password, e accesso con Google. |
| ACC-02 | P1 | Recupero password via email. |
| ACC-03 | P1 | Modifica nome, email, password, foto, lingua, unità, valuta. |
| ACC-04 | P1 | Cancellazione account: i viaggi di cui si è unici proprietari vengono eliminati o trasferiti; i viaggi condivisi restano agli altri collaboratori. |
| ACC-05 | P1 | Un visitatore crea una bozza locale. Alla registrazione la bozza viene salvata sull’account. |
| ACC-06 | P2 | Collegamento e scollegamento di una casella Gmail, con consenso esplicito e revoca immediata. |
| ACC-07 | P2 | Pagina profilo pubblico con viaggi e guide visibili secondo la privacy scelta. |

### 7.2 Viaggio

| ID | Priorità | Requisito |
| --- | --- | --- |
| TRP-01 | P1 | Creare un viaggio con titolo, una o più destinazioni, data inizio e fine. Le date possono restare vuote. |
| TRP-02 | P1 | Cambiare date: i giorni esistenti si spostano o si estendono senza perdere le voci; i giorni in eccesso chiedono conferma prima della cancellazione. |
| TRP-03 | P1 | Elenco “I miei viaggi” con filtri in programma, in corso, passati, archiviati. |
| TRP-04 | P1 | Duplicare, archiviare, eliminare un viaggio. L’eliminazione è consentita al proprietario. |
| TRP-05 | P1 | Visibilità: privato, chiunque abbia il link, follower, pubblico. |
| TRP-06 | P1 | Copertina automatica dalla destinazione, sostituibile. |
| TRP-07 | P2 | Riordinare le sezioni utente. Nascondere una sezione senza cancellarla. |
| TRP-08 | P2 | Stampa e esportazione PDF dell’itinerario dal web. |
| TRP-09 | P2 | Indice del viaggio per saltare a un giorno o a una sezione. In viaggio, il giorno corrente è evidenziato. |
| TRP-10 | P3 | Vista calendario del viaggio, oltre alla lista dei giorni. |
| TRP-11 | P1 | Ogni viaggio ha due viste: piano (itinerario e budget) e diario (fermate visitate, foto, note, percorso). |
| TRP-12 | P1 | Salvataggio automatico, con annulla e ripeti sull’editor. |
| TRP-13 | P2 | Trasporto predefinito del viaggio: auto oppure trasporto pubblico, entrambi con camminata sulle tratte brevi. |
| TRP-14 | P2 | Convertire il viaggio in una guida copiando luoghi e note. Il viaggio non viene cancellato. |

**Criterio TRP-01.** Dato un utente sulla home, quando inserisce “Roma”, dal 12 al 16 giugno, e conferma, il sistema crea un viaggio con cinque giorni vuoti e la destinazione Roma sulla mappa.

### 7.3 Luoghi e liste

| ID | Priorità | Requisito |
| --- | --- | --- |
| PLC-01 | P1 | Cercare un luogo per nome e zona e aggiungerlo a una lista o a un giorno. |
| PLC-02 | P1 | All’aggiunta, compilare coordinate, indirizzo, foto, categoria, voto, orari e sito se il provider li fornisce. |
| PLC-03 | P1 | Creare liste (“Da vedere”, “Ristoranti”, “Se avanza tempo”) con nome, colore e icona. |
| PLC-04 | P1 | Nota libera su ogni voce. Foto personalizzata al posto di quella predefinita. |
| PLC-05 | P1 | Trascinare per riordinare. Spostare o eliminare più voci insieme. |
| PLC-06 | P1 | Spostare una voce da una lista a un giorno e viceversa. Una voce può restare in una lista e comparire anche in un giorno. |
| PLC-07 | P2 | Suggerimenti in fondo a una lista, in base alla destinazione e ai luoghi già presenti. |
| PLC-08 | P2 | Aggiungere un luogo personalizzato non presente nel provider (nome, coordinate o indirizzo, nota). |
| PLC-09 | P3 | Attività senza luogo fisico (“giro in barca”), con durata e costo, posizionabile nel giorno. |

### 7.4 Itinerario

| ID | Priorità | Requisito |
| --- | --- | --- |
| ITN-01 | P1 | Ogni giorno mostra le tappe in ordine, gli orari, e sotto l’intestazione il tempo di spostamento totale e la distanza totale. |
| ITN-02 | P1 | Impostare ora di inizio e di fine su una tappa. |
| ITN-03 | P1 | Tra due tappe mostrare mezzo, durata e distanza. Il mezzo si cambia per quella tratta. |
| ITN-04 | P1 | Mezzi: auto, a piedi, trasporto pubblico, bici. |
| ITN-05 | P1 | Aprire le indicazioni della tratta successiva in Google Maps o Apple Maps. |
| ITN-06 | P1 | Vista compatta (solo nome, orario, tratta) e vista estesa (foto e nota). |
| ITN-07 | P2 | Ottimizzare l’ordine di un giorno, al massimo 15 tappe. L’utente fissa inizio e fine, oppure accetta il suggerimento. Un avviso mostra cosa è cambiato e consente il ripristino. |
| ITN-08 | P2 | Opzione per inserire l’alloggio del giorno come prima e ultima tappa, così le distanze partono e tornano lì. |
| ITN-09 | P3 | Ottimizzazione che rispetta gli orari già fissati e le finestre di apertura. |
| ITN-10 | P3 | Mezzi aggiuntivi: treno, traghetto, taxi, volo interno. |

**Criterio ITN-07.** Dato un giorno con sei luoghi e l’alloggio fissato come inizio e fine, quando l’utente chiede l’ottimizzazione, l’ordine delle tappe intermedie cambia per ridurre il tempo di viaggio e l’utente può tornare all’ordine precedente con un’azione.

### 7.5 Mappa

| ID | Priorità | Requisito |
| --- | --- | --- |
| MAP-01 | P1 | Sul piano, itinerario e mappa sono visibili insieme su schermi larghi. Su mobile si passa da una scheda all’altra. |
| MAP-02 | P1 | Ogni voce ha un pin. Il colore segue la lista o il giorno. |
| MAP-03 | P1 | Filtrare i pin per giorno e per lista. |
| MAP-04 | P1 | Le tappe dello stesso giorno sono collegate da una linea nell’ordine dell’itinerario. |
| MAP-05 | P2 | Export dei luoghi di un giorno o del viaggio verso Google Maps. |
| MAP-06 | P2 | Mappa incorporabile in una pagina esterna, in sola lettura, per una guida pubblica. |
| MAP-07 | P3 | Layer meteo sul giorno selezionato. |

### 7.6 Prenotazioni e documenti

| ID | Priorità | Requisito |
| --- | --- | --- |
| RES-01 | P1 | Tipi: volo, alloggio, auto, ristorante, treno, autobus, traghetto, crociera, allegato. Il volo parte da compagnia aerea, con alternativa “inserisci a mano”. |
| RES-02 | P1 | Il volo è modificabile nei campi numero, scali, aeroporti, data e ora. |
| RES-03 | P1 | Ogni viaggio ha un indirizzo del tipo `trip+<id>@dominio`. L’utente lo copia dal tipo di prenotazione. Un’azione mostra lo stato delle email importate. |
| RES-04 | P1 | Se il parser non riconosce l’email, la conserva come documento allegato e avvisa l’utente. |
| RES-05 | P2 | Con Pro, la casella collegata viene letta solo per conferme di viaggio. L’utente vede l’elenco e può scollegare. |
| RES-06 | P2 | Allegare file (PDF, immagini) a viaggio o prenotazione. Il piano gratuito ha un tetto di spazio; Pro lo alza. |
| RES-07 | P2 | Spostare una prenotazione importata da un viaggio a un altro. |
| RES-08 | P2 | Stato volo: ritardo, gate, cancellazione, con notifica ai collaboratori. |
| RES-09 | P1 | Treno, autobus, traghetto e crociera sono tipi di prenotazione, allo stesso livello di volo e alloggio. |
| RES-10 | P3 | Un pacchetto volo+hotel conta una sola volta nel budget. L’utente marca una prenotazione come “già inclusa”. |

**Criterio RES-03.** Data un’email di conferma con numero di volo, aeroporti e orari, quando viene inoltrata all’indirizzo del viaggio, entro un minuto il volo compare nel giorno corretto con quei campi compilati.

### 7.7 Budget

| ID | Priorità | Requisito |
| --- | --- | --- |
| BDG-01 | P1 | Impostare un budget totale nella valuta del viaggio. |
| BDG-02 | P1 | Aggiungere una spesa con importo, valuta, data opzionale e pagatore. Il pagatore iniziale è l’utente corrente. |
| BDG-03 | P1 | Collegare la spesa a un elemento del piano (luogo o prenotazione), senza obbligo. |
| BDG-04 | P1 | Divisione: non dividere, tutti, oppure individui. Il saldo di gruppo dice chi deve a chi. |
| BDG-05 | P1 | Totali: speso, restante rispetto al budget, ripartizione per categoria. |
| BDG-06 | P2 | Convertire ogni spesa nella valuta del viaggio con un tasso del giorno della spesa, modificabile. Il totale è nella valuta di riferimento e mostra anche l’originale. |
| BDG-07 | P2 | Le prenotazioni con prezzo possono generare una spesa proposta. L’utente conferma, così un importo non entra due volte. |
| BDG-08 | P3 | Esportazione CSV delle spese. |

### 7.8 Checklist

| ID | Priorità | Requisito |
| --- | --- | --- |
| CHK-01 | P1 | Creare liste di bagaglio, cose da fare e acquisti, con voci spuntabili. |
| CHK-02 | P1 | Modelli di bagaglio per tipologia di viaggio (città, mare, montagna, bambino). L’utente li copia e li modifica. |
| CHK-03 | P2 | Assegnare una voce a un compagno. |
| CHK-04 | P2 | L’assistente propone una lista a partire da destinazione, durata e mezzi, e l’utente la accetta in blocco o voce per voce. |

### 7.9 Collaborazione

| ID | Priorità | Requisito |
| --- | --- | --- |
| COL-01 | P1 | Il proprietario invita via email o link con ruolo modifica o lettura. Può revocare o cambiare ruolo. |
| COL-02 | P1 | Le modifiche di testo, ordine e nuove voci si propagano agli altri aperti sul piano entro pochi secondi. |
| COL-03 | P1 | Due modifiche concorrenti sulla stessa nota non si perdono in silenzio: vince l’ultima salvata e l’altra resta recuperabile nell’attività recente. |
| COL-04 | P1 | Registro attività: chi ha aggiunto, spostato o cancellato, con orario. |
| COL-05 | P2 | Commento su una voce, visibile ai collaboratori. |
| COL-06 | P3 | Voto sui luoghi in una lista (“sì”, “forse”, “no”) per decidere in gruppo. |

### 7.10 Guide e scoperta

| ID | Priorità | Requisito |
| --- | --- | --- |
| GDE-01 | P1 | Pagine destinazione: descrizione, attrazioni, categorie, itinerari collegati, road trip in partenza, luoghi vicini. |
| GDE-02 | P1 | Scheda luogo pubblica: foto, categoria, descrizione, voto, estratti recensioni attribuite alla fonte, link al sito. |
| GDE-03 | P1 | Guida o itinerario editoriale con giorni, testo e luoghi. Da ogni luogo, “Aggiungi al viaggio” sulla lista o sul giorno scelto. |
| GDE-04 | P1 | Ricerca guide per destinazione, durata e tema. |
| GDE-05 | P2 | Un utente pubblica una guida. Resta in bozza finché lo staff non la approva, oppure è subito visibile come contenuto della community con segnalazione. |
| GDE-06 | P2 | Domande e risposte sulla destinazione, con moderazione. |
| GDE-07 | P2 | Medie climatiche mensili sulla destinazione. |
| GDE-08 | P2 | Diario di viaggio collegato al piano: riepilogo, copertina, fermate con foto e note, linee del percorso, mappa anche satellitare, condivisione propria. La privacy del diario è indipendente nel pannello, con le stesse tre scelte del piano. |
| GDE-09 | P3 | Guide scaricabili per la lettura offline nell’app. |

**Criterio GDE-03.** Dato un itinerario pubblico e un viaggio aperto dell’utente, quando preme “Aggiungi” su un luogo e sceglie il giorno 2, quel luogo compare nel giorno 2 e sulla mappa del suo viaggio.

### 7.11 Alloggi

| ID | Priorità | Requisito |
| --- | --- | --- |
| HOT-01 | P2 | Ricerca alloggi per destinazione, date e ospiti, con risultati di più fornitori. |
| HOT-02 | P2 | Ordinamento per prezzo totale o per voto, dichiarato in interfaccia. La posizione in elenco non dipende da una commissione di visibilità. |
| HOT-03 | P2 | Il prezzo mostrato include le tasse note. Se un fornitore non le espone, l’interfaccia lo dice. |
| HOT-04 | P2 | Salvare un alloggio nel viaggio come candidato o come prenotazione. |
| HOT-05 | P2 | Avviso se il prezzo di un alloggio osservato scende, entro una soglia scelta. |
| HOT-06 | P2 | Il clic verso il fornitore è tracciato per l’eventuale affiliazione. La prenotazione avviene sul sito del fornitore. |
| HOT-07 | P3 | Confronto voli e noleggi, con gli stessi principi di trasparenza. |

### 7.12 Assistente

| ID | Priorità | Requisito |
| --- | --- | --- |
| AI-01 | P2 | Nel viaggio, una chat risponde su destinazione, spostamenti e vincoli del piano già inserito. |
| AI-02 | P2 | Proporre luoghi e ristoranti. L’utente aggiunge un suggerimento a un giorno con un’azione esplicita. Nessun suggerimento entra nel piano da solo. |
| AI-03 | P2 | Compilare una bozza di giorni vuoti con luoghi noti della destinazione. L’utente conferma prima della scrittura. |
| AI-04 | P2 | Tradurre una nota o una descrizione nella lingua dell’interfaccia. |
| AI-05 | P2 | Il piano gratuito include una quota (su Wanderlog: 10 messaggi). Pro toglie il tetto. Ogni risposta mostra che il testo può essere inaccurato. Si può allegare un file e aprire una chat nuova. |
| AI-06 | P3 | Rispettare profilo del gruppo: ritmo, budget, accesso disabili, presenza di bambini, interessi. |

### 7.13 Offline, notifiche, estensione

| ID | Priorità | Requisito |
| --- | --- | --- |
| OFF-01 | P2 | L’app scarica un viaggio scelto: giorni, note, prenotazioni, allegati già scaricati, pin. Resta leggibile senza rete. |
| OFF-02 | P2 | Le modifiche offline si accodano e si sincronizzano al ritorno della rete. I conflitti seguono COL-03. |
| OFF-03 | P2 | Notifiche: invito, commento, stato volo, calo prezzo, promemoria di una tappa con orario. |
| EXT-01 | P3 | L’estensione, su una pagina web, cerca il testo selezionato come luogo e lo salva nel viaggio scelto. |
| EXT-02 | P3 | Su una pagina di annunci di alloggi supportata, mostra il totale con costi obbligatori quando la pagina lo consente. |

### 7.14 Abbonamento

| ID | Priorità | Requisito |
| --- | --- | --- |
| SUB-01 | P2 | Piani: gratuito e Pro. Riferimento osservato sul web: prova 7 giorni, poi 39,99 USD/anno, rinnovo automatico, disdetta almeno 24 ore prima dalla pagina account. Il mensile resta un’estensione nostra. |
| SUB-02 | P2 | Gestione da account: stato, rinnovo, disdetta. La disdetta mantiene Pro fino a fine periodo pagato. |
| SUB-03 | P2 | Il server applica i limiti. Nascondere un pulsante non è il controllo. |

Funzioni gratuite, senza tetto sul numero di viaggi o di compagni:

- luoghi, liste, itinerario, mappa, note;
- collaborazione live;
- prenotazioni manuali e inoltro email;
- budget e divisione spese;
- checklist;
- suggerimenti di base;
- guide pubbliche.

Funzioni Pro, come nel paywall aperto dall’ottimizzazione:

- ottimizzazione del percorso;
- accesso offline;
- aggiornamenti di stato volo;
- offerte voli e auto a noleggio;
- allegati oltre la quota gratuita;
- scansione automatica di Gmail;
- export verso Google Maps, aggiornato se il piano cambia;
- assistente senza quota;
- tema scuro nell’app;
- salvataggio luoghi dall’estensione, evidenziando il testo su un sito.

### 7.15 Estensioni rispetto a Wanderlog

Queste funzioni non sono una copia: rispondono ai vuoti osservati e al contesto d’uso italiano.

| ID | Requisito |
| --- | --- |
| NEW-01 | Scelta esplicita di km o miglia nel profilo. L’interfaccia italiana e inglese è parità, non un’aggiunta: Wanderlog ha già il cambio lingua. |
| NEW-02 | Totale di budget che converte spese in valute diverse (BDG-06). La valuta per singola spesa c’è già. |
| NEW-03 | Bici come mezzo di tratta (ITN-04), oltre ai preset auto e trasporto pubblico. |
| NEW-04 | Campi treno, bus e traghetto utili in Italia e in Europa: codice prenotazione, stazione, carrozza, posto. Il tipo di prenotazione è già parità (RES-09). |
| NEW-05 | Nessun doppio conteggio dei pacchetti (RES-10, BDG-07). |
| NEW-06 | Meteo previsto sui giorni del viaggio, non solo la media storica della destinazione. |
| NEW-07 | Vista calendario (TRP-10). |
| NEW-08 | Attività senza POI (PLC-09). |
| NEW-09 | Piano Pro mensile oltre all’annuale (SUB-01). |
| NEW-10 | Voto di gruppo sui luoghi (COL-06). |
| NEW-11 | Giorno corrente in evidenza durante il viaggio (TRP-09). |
| NEW-12 | Accessibilità: contrasto, tastiera sull’editor web, etichette per lettori di schermo sulle azioni di riordino. |

## 8. Flussi principali

### 8.1 Iniziare un viaggio

1. Il visitatore indica destinazione e date, oppure apre “Pianifica” e li inserisce dopo.
2. Il sistema crea la bozza con i giorni e una mappa centrata.
3. Mostra attrazioni della destinazione e, se esiste, un itinerario editoriale.
4. L’utente aggiunge luoghi, poi registra l’account per non perdere la bozza.

### 8.2 Costruire la giornata

1. L’utente sposta i luoghi dalle liste ai giorni.
2. Imposta orari e mezzo tra le tappe.
3. La mappa ridisegna pin e linee. Il giorno mostra tempo e km totali.
4. Se il percorso è scomodo, chiede l’ottimizzazione e accetta o annulla.

### 8.3 Importare una conferma

1. Dal blocco prenotazioni l’utente copia l’indirizzo del viaggio oppure collega la casella.
2. Il parser classifica l’email.
3. Crea la prenotazione sul giorno giusto e propone una spesa.
4. L’utente corregge i campi se il parser ha sbagliato.

### 8.4 Pianificare in gruppo

1. Il proprietario invita con diritto di modifica.
2. I compagni vedono lo stesso piano.
3. Uno aggiunge ristoranti, un altro le spese, un terzo spunta il bagaglio.
4. Il saldo dice chi ha pagato e chi deve rimborsare.

### 8.5 Durante il viaggio

1. L’app apre il giorno corrente.
2. Senza rete, il piano scaricato resta consultabile.
3. Una notifica avvisa di un ritardo del volo.
4. Le spese inserite offline si sincronizzano dopo.

## 9. Schermate

### Sito

- Home: proposta di valore, avvio viaggio, guide in evidenza.
- Destinazione, luogo, guida, itinerario, road trip.
- Ricerca alloggi.
- Editor viaggio: colonna sezioni e itinerario, colonna mappa, pannelli prenotazioni, budget, checklist, condivisione, assistente.
- Autenticazione, profilo, impostazioni, abbonamento.
- Pagine legali: termini, privacy, cookie.

### App

- Elenco viaggi.
- Editor con schede Itinerario, Mappa, Prenotazioni, Spese, Liste.
- Scaricamento offline.
- Notifiche.
- Profilo e abbonamento.

L’editor web su desktop tiene lista e mappa affiancate. Sotto una larghezza da telefono, le stesse aree diventano schede, come sull’app.

## 10. Architettura funzionale Django

Applicazioni previste, tutte dietro un’API usata anche dall’app:

| App Django | Responsabilità |
| --- | --- |
| `accounts` | Utenti, profilo, lingue, cancellazione |
| `trips` | Viaggi, giorni, sezioni, visibilità, collaboratori, attività |
| `places` | Anagrafica luoghi, voci, liste |
| `itinerary` | Ordine giornaliero, orari, tratte |
| `maps` | Adattatore del provider mappe e percorsi |
| `reservations` | Prenotazioni, parser email, allegati, stato voli |
| `budget` | Budget, spese, saldi, cambi |
| `checklists` | Liste e modelli |
| `guides` | Destinazioni, guide, Q&A, racconti |
| `lodging` | Ricerca alloggi e affiliazione |
| `assistant` | Chat e bozze, con quota |
| `billing` | Abbonamenti e limiti di piano |
| `notifications` | Email e push |

Tempo reale: canale per viaggio (Django Channels o servizio equivalente) per COL-02.

Regole di autorizzazione:

- lettura del viaggio secondo visibilità e ruolo;
- scrittura solo per proprietario e ruolo modifica;
- cambio visibilità, cancellazione e gestione inviti solo per il proprietario;
- file e prenotazioni seguono il permesso del viaggio.

## 11. Integrazioni

| Servizio | Uso | Note |
| --- | --- | --- |
| Mappe e luoghi | Ricerca, schede, pin, tempi e distanze | Un provider con licenza adatta all’archiviazione dei luoghi. Va scelto prima dello schema, perché i termini limitano cosa si può salvare. |
| Indicazioni | Apertura in Google Maps o Apple Maps | Non sostituisce l’app di navigazione. |
| Email in entrata | Indirizzo per viaggio | Parsing proprio. Non si inoltrano le email a terzi non dichiarati. |
| Gmail | Lettura conferme, solo Pro, revocabile | Scope minimo. Log di cosa è stato letto. |
| Meteo | Previsioni sui giorni e medie sulla destinazione | |
| Voli | Stato in tempo reale | |
| Alloggi | Deep link e, se previsto, API affiliati | La prenotazione resta sul fornitore. |
| Pagamenti | Abbonamento | Stripe o store mobile, con ricevuta. |
| LLM | Assistente | Il piano inviato al modello è ridotto al necessario. L’utente va informato. |
| Cambi | Tassi per BDG-06 | Tasso storicizzato sulla spesa. |
| Push | Notifiche app | |

Se un provider non copre una zona (caso noto: trasporto pubblico in Giappone su alcune API Google), la tratta resta senza stima e l’interfaccia lo dice, senza inventare un tempo.

## 12. Requisiti non funzionali

| Tema | Requisito |
| --- | --- |
| Lingue | Italiano e inglese completi sull’interfaccia. I contenuti editoriali possono nascere in una lingua e indicare la lingua. |
| Prestazioni | Apertura di un viaggio con 200 luoghi sotto i 3 secondi su rete normale. La mappa non blocca l’editing del testo. |
| Salvataggio | Le modifiche dell’editor si salvano da sole. Nessun pulsante “Salva” obbligatorio sul piano. |
| Sync | Propagazione ai collaboratori connessi entro 5 secondi in condizioni normali. |
| Privacy | Account cancellabile. Casella scollegabile. Email di conferma trattate come dati del viaggio, visibili solo ai collaboratori. |
| Sicurezza | Password con hash adeguato, sessioni revocabili, inviti con token a scadenza, file privati non indovinabili, limiti di upload. |
| Accessibilità | Form etichettati, focus visibile, alternative testuali sulle azioni icona dell’editor. |
| Offline | Un piano marcato scaricato si apre in modalità aereo con ultime dati sincronizzati. |
| Disponibilità | Cancellazione e export dei propri viaggi in un archivio scaricabile (JSON più allegati). |
| Modello | I suggerimenti dell’assistente sono proposte. L’orario di un volo importato resta modificabile. |

## 13. Vincoli legali e di prodotto

- Nome, logo, testi e guide di Wanderlog non si riutilizzano.
- Recensioni di Google o di altri siti si mostrano solo se la licenza del provider lo permette, con attribuzione.
- Le foto dei luoghi arrivano dal provider o da caricamenti dell’utente, con diritto d’uso.
- L’embed di mappe e il salvataggio di POI devono rispettare i termini del fornitore scelto.
- L’analisi della posta è opt-in, limitata alle conferme, e documentata nella privacy policy.
- Questo documento descrive un prodotto concorrente per funzioni. Non autorizza scraping del sito Wanderlog per popolare il catalogo.

## 14. Roadmap

**Fase 1 — Web utilizzabile**  
Account, viaggi, luoghi, itinerario, mappa, note, collaborazione, checklist, budget con conversione, prenotazioni manuali e via email, guide in sola lettura scritte dallo staff, italiano e inglese.

**Fase 2 — Parità da viaggio**  
App, offline, Pro, ottimizzazione, allegati, export mappe, stato voli, assistente, ricerca alloggi.

**Fase 3 — Estensioni**  
Calendario, treni e traghetti strutturati, voti di gruppo, meteo sul giorno, attività senza POI, estensione browser, racconti, piano mensile.

## 15. Criteri di accettazione del primo rilascio

Il primo rilascio web è accettato quando un utente riesce, senza assistenza, a:

1. creare un viaggio di cinque giorni su una città;
2. aggiungere almeno dieci luoghi da ricerca e da una guida;
3. distribuirli sui giorni, mettere orari e vedere tempi e distanze;
4. vedere gli stessi luoghi sulla mappa, filtrati per giorno;
5. invitare un’altra persona che modifica il piano e vede l’aggiornamento;
6. inserire un volo a mano e un volo da email di prova;
7. impostare un budget, registrare spese in due valute e leggere il totale convertito e il saldo tra i due;
8. spuntare una checklist di bagaglio;
9. riaprire il viaggio da un altro browser con lo stesso account e trovare i dati integri.

## 16. Fuori ambito della prima versione

- prenotazione diretta di voli e hotel sul nostro dominio;
- navigazione turn-by-turn dentro l’app;
- rete sociale ampia (feed, messaggi privati, follower suggeriti);
- biglietteria treni integrata con acquisto;
- white label per agenzie.

## 17. Glossario

| Termine | Significato |
| --- | --- |
| Piano / viaggio | Il contenitore di date, luoghi, prenotazioni, spese e liste |
| Lista | Raccolta di luoghi non ancora messi in un giorno, o tenuti come riferimento |
| Tappa | Un luogo posizionato in un giorno, in ordine |
| Tratta | Lo spostamento tra due tappe |
| Compagno | Utente invitato sul viaggio |
| Guida | Contenuto pubblico, editoriale o di community, da cui si copiano luoghi nel proprio piano |
| Pro | Abbonamento che sblocca le funzioni della sezione 7.14 |

## 18. Decisioni ancora aperte

1. Nome del prodotto e dominio.
2. Provider mappe (costi, cache dei luoghi, qualità in Italia).
3. Flutter o React Native per l’app.
4. Se le guide della community sono visibili subito o dopo moderazione.
5. Listino Pro nostro. Il riferimento web di Wanderlog, il 7 ottobre 2026, è 39,99 USD/anno dopo 7 giorni di prova, solo annuale.
6. Quali affiliati alloggi attivare al lancio.
