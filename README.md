Applicazione web per raccogliere, organizzare e visualizzare in forma
aggregata le risposte degli studenti sull'utilizzo dell'Intelligenza
Artificiale a scuola e nella vita quotidiana.

Il progetto è pensato per essere utilizzato da più classi e da
dispositivi differenti. Le risposte vengono inviate a un archivio
centrale su **Firebase Cloud Firestore**, così da poter essere
consultate successivamente attraverso una dashboard.

------------------------------------------------------------------------

## Indice

1.  [Obiettivi del progetto](#obiettivi-del-progetto)
2.  [Funzionamento per gli studenti](#funzionamento-per-gli-studenti)
3.  [Domande del sondaggio](#domande-del-sondaggio)
4.  [Raccolta e sincronizzazione dei
    dati](#raccolta-e-sincronizzazione-dei-dati)
5.  [Dashboard aggregata](#dashboard-aggregata)
6.  [Modalità proiezione e
    responsive](#modalità-proiezione-e-responsive)
7.  [Esportazione dei dati](#esportazione-dei-dati)
8.  [Architettura tecnica](#architettura-tecnica)
9.  [Struttura dei dati in Firestore](#struttura-dei-dati-in-firestore)
10. [Pubblicazione con GitHub Pages](#pubblicazione-con-github-pages)
11. [Configurazione Firebase](#configurazione-firebase)
12. [Sicurezza e privacy](#sicurezza-e-privacy)
13. [Manutenzione e risoluzione dei
    problemi](#manutenzione-e-risoluzione-dei-problemi)
14. [Possibili sviluppi futuri](#possibili-sviluppi-futuri)

------------------------------------------------------------------------

## Obiettivi del progetto

AI School Quest consente di realizzare un'indagine sull'utilizzo
dell'Intelligenza Artificiale da parte degli studenti.

Gli obiettivi principali sono:

-   raccogliere le risposte di singoli studenti o piccoli gruppi;
-   associare ogni raccolta a una scuola, a un grado scolastico, a una
    classe e a un codice condiviso;
-   permettere a più classi e dispositivi di contribuire allo stesso
    sondaggio;
-   centralizzare le risposte in un unico archivio online;
-   visualizzare i risultati attraverso grafici e indicatori;
-   confrontare i risultati di un singolo sondaggio oppure consultare
    l'intero archivio;
-   esportare i dati raccolti per ulteriori analisi.

L'applicazione non richiede agli studenti di creare un account o di
inserire nome e cognome.

## Funzionamento per gli studenti

Il percorso di compilazione è suddiviso in tre schermate principali.

### 1. Configurazione del gruppo

Prima di iniziare, vengono richieste le informazioni necessarie a
identificare il gruppo:

  -----------------------------------------------------------------------
  Campo                               Descrizione
  ----------------------------------- -----------------------------------
  Nome istituto / scuola              Nome della scuola partecipante

  Grado scolastico                    Scuola secondaria di primo grado,
                                      biennio o triennio della secondaria
                                      di secondo grado

  Classe e sezione                    Per esempio 3A, 4BS o 1C

  Codice sessione                     Codice condiviso che identifica il
                                      sondaggio

  Componenti del gruppo               Numero di studenti che rispondono
                                      da quel dispositivo
  -----------------------------------------------------------------------

Il numero dei partecipanti può essere impostato da 1 a 10.

**Il codice sessione è particolarmente importante:** tutte le risposte
associate allo stesso codice possono essere riunite nella dashboard,
anche quando provengono da scuole, classi, dispositivi o giornate
differenti.

Il codice viene normalizzato automaticamente: viene eliminato lo spazio
iniziale e finale, vengono rimossi gli spazi interni e il testo viene
convertito in maiuscolo.

Esempio: `AI 2026` diventa `AI2026`.

### 2. Compilazione delle risposte

Il dispositivo passa da uno studente al successivo, mostrando un
indicatore di avanzamento.

Per ogni partecipante vengono raccolte le risposte alle domande del
sondaggio. Le selezioni vengono azzerate prima di passare al
partecipante successivo.

Le risposte obbligatorie sono: - utilizzo dell'Intelligenza
Artificiale; - frequenza di utilizzo.

Gli altri campi sono a scelta multipla o facoltativi, secondo quanto
indicato nella schermata.

### 3. Conferma e invio

Dopo l'ultima risposta del gruppo, l'applicazione:

1.  raccoglie tutte le risposte compilate sul dispositivo;
2.  crea un record relativo alla sessione;
3.  invia il record a Cloud Firestore;
4.  mostra una schermata di conferma dell'invio.

Se l'invio non riesce, viene mostrato un messaggio che invita ad
avvisare l'insegnante e a controllare la connessione e la configurazione
Firebase.

**Importante:** in caso di errore di rete, non chiudere la pagina prima
di aver verificato con l'insegnante se i dati siano stati effettivamente
salvati. Il codice attuale non implementa una coda offline o un sistema
automatico di reinvio.

------------------------------------------------------------------------

## Domande del sondaggio

Le domande presenti nel codice sono le seguenti.

### 1. Utilizzo dell'Intelligenza Artificiale

Domanda: **«Usa l'Intelligenza Artificiale?»**

Risposte: - Sì - A volte - No

### 2. Frequenza di utilizzo

Domanda: **«Con che frequenza lo usi?»**

Risposte: - Tutti i giorni - Ogni settimana - Qualche volta al mese -
Rara o mai

### 3. Strumenti utilizzati

Domanda: **«Quali strumenti usi di più?»**

È possibile selezionare più risposte: - ChatGPT - Gemini / Copilot -
Canva AI - Claude / Perplexity - Altro tool - Nessuno

Se viene selezionata l'opzione «Altro tool», è possibile specificare il
nome dello strumento.

### 4. Finalità di utilizzo

Domanda: **«Per cosa lo usi prevalentemente?»**

È possibile selezionare più risposte: - Studio e spiegazioni - Scrivere
/ riassumere - Coding / informatica - Generare immagini / arte - Svago e
curiosità - Altra finalità

Anche in questo caso è possibile specificare una finalità diversa.

### 5. Note e commenti personali

È presente un campo facoltativo nel quale lo studente può descrivere
brevemente il proprio utilizzo dell'Intelligenza Artificiale.

------------------------------------------------------------------------

## Raccolta e sincronizzazione dei dati

L'applicazione utilizza Firebase come archivio centrale.

Ogni gruppo completa il proprio sondaggio sul dispositivo disponibile.
Al termine, le risposte vengono inviate a Firestore.

Non è necessario che tutti gli studenti utilizzino lo stesso computer o
la stessa rete locale: i dati vengono raccolti nel progetto Firebase
configurato nell'applicazione.

### Esempio di utilizzo con più classi

Si immagini di organizzare un'indagine con il codice condiviso `AI2026`.

  Gruppo     Classe   Dispositivo   Codice
  ---------- -------- ------------- --------
  Gruppo 1   3A       Computer 1    AI2026
  Gruppo 2   3B       Tablet 1      AI2026
  Gruppo 3   4A       Computer 2    AI2026
  Gruppo 4   2C       Smartphone    AI2026

Le sessioni vengono salvate separatamente, ma la dashboard può riunire
tutte le risposte che condividono il codice `AI2026`.

Questo consente di costruire una vista complessiva dell'indagine senza
dover raccogliere manualmente i risultati delle singole classi.

### Percorso dei dati

Il percorso utilizzato nel codice è:

``` text
artifacts/
└── ai-school-quest-app/
    └── public/
        └── data/
            └── sessioni_classe/
                ├── documento-sessione-1
                ├── documento-sessione-2
                └── documento-sessione-3
```

Ogni documento rappresenta una sessione completata da un gruppo su un
dispositivo.

------------------------------------------------------------------------

## Dashboard aggregata

La dashboard permette di consultare i dati raccolti nel database
Firestore.

Nel codice attuale sono presenti tre indicatori principali.

### Indicatori sintetici

-   **Studenti votanti:** numero delle risposte presenti nella selezione
    visualizzata.
-   **Utilizzano l'AI:** percentuale di risposte «Sì» o «A volte».
-   **Tool preferito:** strumento con il maggior numero di selezioni.

### Grafici

La dashboard presenta tre grafici:

1.  **Frequenza di utilizzo:** grafico ad anello con la distribuzione
    delle frequenze.
2.  **Strumenti più popolari:** grafico a barre con il numero di
    selezioni per ciascuno strumento.
3.  **Principali ambiti di utilizzo:** grafico a barre con la
    distribuzione delle finalità.

Poiché strumenti e finalità sono domande a scelta multipla, il numero
delle selezioni può essere superiore al numero degli studenti.

### Note e usi specifici

La dashboard raccoglie anche: - le specifiche inserite nei campi
«Altro»; - le note e i commenti facoltativi; - le informazioni di scuola
e classe associate alle note.

### Filtro per codice sessione

È possibile inserire un codice per visualizzare esclusivamente le
risposte appartenenti a quel sondaggio.

Il filtro: - normalizza il codice inserito; - seleziona le risposte con
lo stesso codice; - aggiorna gli indicatori; - aggiorna i grafici; -
mostra il numero di sessioni e risposte associate.

Il pulsante **Tutti i sondaggi** ripristina la visualizzazione
dell'intero archivio.

### Aggiornamento in tempo reale

La dashboard utilizza `onSnapshot()` di Firestore per ascoltare le
modifiche alla raccolta.

Quando arrivano nuove sessioni, i dati ricevuti vengono rielaborati e
gli indicatori e i grafici vengono aggiornati.

------------------------------------------------------------------------

## Modalità proiezione e responsive

L'interfaccia è progettata per adattarsi a diversi dispositivi.

### Visualizzazione ordinaria

La dashboard utilizza la larghezza disponibile, con un layout a più
colonne sui monitor più ampi e una disposizione verticale sugli schermi
più piccoli.

### Modalità proiezione

Il pulsante **Modalità proiezione** attiva una visualizzazione pensata
per monitor, LIM e videoproiettori.

Sui monitor desktop sufficientemente ampi e alti: - la dashboard occupa
l'intera area disponibile; - l'intestazione dell'applicazione viene
nascosta; - i filtri vengono rimossi dalla vista; - gli indicatori
sintetici rimangono nella parte superiore; - i grafici vengono disposti
in colonne; - le note vengono collocate nella fascia inferiore; - lo
scorrimento viene disabilitato per mostrare la dashboard in una sola
schermata.

Sui tablet, sulle finestre più basse e sugli schermi piccoli, la
modalità si adatta privilegiando la leggibilità e consentendo lo
scorrimento quando necessario.

Per tornare alla visualizzazione normale, utilizzare **Esci da
proiezione**.

------------------------------------------------------------------------

## Esportazione dei dati

La dashboard include un pulsante per scaricare un report aggregato in
formato CSV.

Il file contiene le seguenti colonne:

-   Scuola
-   Grado
-   Classe
-   Codice sessione
-   Studente
-   Usa AI
-   Frequenza
-   Strumenti
-   Scopi
-   Note

Il contenuto esportato dipende dalla vista selezionata: - se è attivo un
codice, viene esportato il relativo sondaggio; - se è selezionato
l'archivio completo, vengono esportati tutti i sondaggi disponibili.

Il file utilizza il separatore `;` e la codifica UTF-8 con BOM, per
favorire la corretta apertura con programmi di foglio di calcolo,
incluso Microsoft Excel.

**Nota:** il pulsante è etichettato nel codice come «Scarica report
aggregato CSV». La funzione JavaScript si chiama
`inviaDatiAGoogleSheets()`, ma non effettua un collegamento o un invio a
Google Sheets: genera e scarica un file CSV direttamente dal browser.

------------------------------------------------------------------------

## Architettura tecnica

Il progetto è realizzato come applicazione web client-side.

Non è necessario un server applicativo dedicato per eseguire il
sondaggio: l'interfaccia viene distribuita come pagina web statica e
comunica direttamente con Firebase.

### Tecnologie utilizzate

  -----------------------------------------------------------------------
  Tecnologia                          Utilizzo
  ----------------------------------- -----------------------------------
  HTML5                               Struttura delle schermate

  CSS                                 Stili personalizzati e responsive

  Tailwind CSS                        Layout e classi di utilità

  JavaScript                          Logica del sondaggio e gestione
                                      delle schermate

  Firebase Authentication             Autenticazione anonima prevista per
                                      i partecipanti nel codice allegato

  Cloud Firestore                     Salvataggio e lettura dei dati

  Chart.js                            Creazione dei grafici

  Font Awesome                        Icone

  Google Fonts                        Tipografia
  -----------------------------------------------------------------------

### Librerie esterne

Le librerie vengono caricate tramite CDN:

-   Tailwind CSS: `https://cdn.tailwindcss.com`
-   Chart.js: `https://cdn.jsdelivr.net/npm/chart.js`
-   Font Awesome 6.5.1
-   Google Fonts: Outfit e Plus Jakarta Sans
-   Firebase JavaScript SDK 11.6.1

È quindi necessaria una connessione Internet per caricare correttamente
le risorse esterne e comunicare con Firebase.

### Organizzazione del codice

Nel file HTML principale sono presenti: - markup delle schermate; -
stili CSS e regole responsive; - modulo JavaScript per Firebase; -
script JavaScript per la gestione del sondaggio, della dashboard e
dell'esportazione.

------------------------------------------------------------------------

## Struttura dei dati in Firestore

Il codice salva un documento per ogni sessione completata.

Un documento contiene i seguenti campi:

  Campo                    Tipo          Significato
  ------------------------ ------------- --------------------------------------
  `scuola`                 Stringa       Nome dell'istituto
  `grado`                  Stringa       Grado scolastico
  `classe`                 Stringa       Classe e sezione
  `codiceSessione`         Stringa       Codice normalizzato del sondaggio
  `totalePartecipanti`     Numero        Partecipanti previsti nella sessione
  `studenteAttualeIndex`   Numero        Indice dell'ultimo partecipante
  `risposte`               Array         Risposte raccolte dal gruppo
  `timestamp`              Stringa ISO   Data e ora di salvataggio

Ogni elemento dell'array `risposte` contiene:

  -----------------------------------------------------------------------
  Campo                   Tipo                    Significato
  ----------------------- ----------------------- -----------------------
  `studenteId`            Numero                  Progressivo interno del
                                                  partecipante nella
                                                  sessione

  `usaAi`                 Stringa                 Sì, A volte oppure No

  `frequenza`             Stringa                 Frequenza di utilizzo

  `strumenti`             Array                   Strumenti selezionati

  `scopi`                 Array                   Finalità selezionate

  `rispostaLibera`        Stringa                 Nota facoltativa
  -----------------------------------------------------------------------

Il campo `studenteId` è un progressivo interno alla sessione e non è un
identificativo personale.

------------------------------------------------------------------------

## Pubblicazione con GitHub Pages

Il progetto può essere pubblicato come sito statico attraverso GitHub
Pages.

### Repository

Repository GitHub:

<https://github.com/pp-fdeducation/sondaggio_ai>

### Procedura di pubblicazione

1.  Aprire il repository GitHub.
2.  Verificare che il file HTML principale sia presente nella cartella
    pubblicata.
3.  Aprire **Settings**.
4.  Selezionare **Pages**.
5.  Nella sezione relativa alla pubblicazione, scegliere il branch
    utilizzato dal progetto.
6.  Selezionare la cartella corretta, normalmente `/ (root)` se il file
    si trova nella radice.
7.  Salvare la configurazione.
8.  Attendere il completamento della pubblicazione.

GitHub mostrerà l'indirizzo pubblico del sito nella sezione Pages.

### Aggiornamento dell'applicazione

Per aggiornare il progetto:

1.  modificare il file HTML nel repository;
2.  salvare le modifiche;
3.  eseguire il commit;
4.  attendere la nuova pubblicazione di GitHub Pages;
5.  ricaricare il sito nel browser.

Se il browser continua a mostrare una versione precedente, provare un
aggiornamento forzato della pagina o svuotare la cache.

### Ruolo di GitHub Pages e Firebase

I due servizi svolgono funzioni differenti:

-   **GitHub Pages** ospita e distribuisce l'interfaccia web.
-   **Firebase Authentication** gestisce l'identità utilizzata dalle
    richieste.
-   **Cloud Firestore** conserva i dati e applica le regole di accesso.

La pubblicazione su GitHub Pages non sostituisce la configurazione di
Firebase.

------------------------------------------------------------------------

## Configurazione Firebase

Il codice contiene una configurazione Firebase associata al progetto:

-   Project ID: `sondaggio-ai`
-   Auth domain: `sondaggio-ai.firebaseapp.com`
-   App ID: configurato nel file HTML
-   Raccolta Firestore:
    `artifacts/ai-school-quest-app/public/data/sessioni_classe`

La configurazione viene utilizzata per inizializzare Firebase App,
Authentication e Firestore.

### Servizi da verificare

Nel progetto Firebase devono essere configurati:

1.  **Authentication**
    -   il metodo di autenticazione anonima, utilizzato dal flusso
        studenti presente nel file allegato;
2.  **Cloud Firestore**
    -   il database;
    -   la raccolta prevista dal codice;
    -   le regole di sicurezza coerenti con i ruoli e le operazioni
        consentite.

Le regole Firestore devono essere considerate parte integrante della
sicurezza dell'applicazione: non è sufficiente nascondere una schermata
o un pulsante nell'interfaccia.

------------------------------------------------------------------------

## Sicurezza e privacy

Il progetto è pensato per raccogliere informazioni sulle abitudini di
utilizzo dell'Intelligenza Artificiale, senza richiedere nome e cognome
degli studenti.

Alcune buone pratiche:

-   non inserire dati anagrafici nei campi di risposta;
-   non inserire informazioni personali nelle note libere;
-   non condividere pubblicamente eventuali credenziali riservate;
-   verificare sempre le regole di sicurezza Firestore;
-   limitare la lettura dei dati aggregati agli utenti autorizzati;
-   verificare che le autorizzazioni siano applicate anche alle
    richieste dirette al database.

### Attenzione: autenticazione della dashboard

**La versione del file HTML documentata in questo README non contiene
ancora un flusso di login insegnante con email e password.**

Nel codice allegato: - Firebase viene inizializzato con autenticazione
anonima; - la funzione `apriAccessoInsegnante()` apre direttamente la
dashboard; - la lettura della raccolta viene avviata con l'utente
Firebase corrente; - non è presente una chiamata a
`signInWithEmailAndPassword()`.

Di conseguenza, non bisogna considerare la dashboard protetta da
password sulla sola base di questo file HTML.

Se il progetto Firebase è configurato con regole Firestore che
consentono la lettura soltanto agli insegnanti autenticati, la dashboard
di questo file, utilizzando un utente anonimo, non potrà leggere i dati.
Per ottenere l'accesso riservato desiderato, il codice di autenticazione
dell'area insegnanti e le regole Firestore devono essere coerenti.

Questa verifica è necessaria prima di descrivere o distribuire
l'applicazione come dotata di dashboard riservata.

------------------------------------------------------------------------

## Manutenzione e risoluzione dei problemi

### Le risposte non vengono salvate

Verificare: - che il dispositivo sia connesso a Internet; - che Firebase
Authentication sia attivo; - che l'autenticazione anonima sia abilitata
per il flusso studenti; - che Firestore sia disponibile; - che le regole
consentano la creazione dei documenti da parte dell'utente previsto; -
che il percorso della raccolta nel codice corrisponda a quello previsto
dalle regole.

Consultare anche la console degli strumenti per sviluppatori del browser
per individuare eventuali errori JavaScript o Firebase.

### La dashboard non mostra i dati

Verificare: - che esistano documenti nella raccolta corretta; - che il
codice sessione inserito sia corretto; - che le regole Firestore
consentano la lettura all'utente autenticato; - che l'autenticazione
utilizzata dalla dashboard sia compatibile con le regole.

### Il codice non restituisce risultati

Il codice viene normalizzato eliminando gli spazi e convertendo le
lettere in maiuscolo. Controllare che tutte le classi abbiano utilizzato
lo stesso codice.

### I grafici non vengono visualizzati

Verificare: - la connessione Internet; - il caricamento della libreria
Chart.js; - la presenza di errori nella console del browser; - che il
database contenga risposte nel formato previsto.

### La modalità proiezione non occupa una sola schermata

La modalità full-screen è prevista per monitor desktop con larghezza di
almeno 1024 pixel e altezza di almeno 600 pixel. Su schermi più piccoli
o finestre basse viene privilegiato un layout scorrevole per evitare di
tagliare i grafici.

------------------------------------------------------------------------

## Possibili sviluppi futuri

Le seguenti sono idee di evoluzione, non funzionalità da considerare già
implementate:

-   autenticazione insegnanti con accesso riservato e password;
-   gestione degli insegnanti autorizzati;
-   esportazione CSV con ulteriori filtri;
-   confronto tra scuole, gradi e classi;
-   visualizzazione dell'andamento delle risposte nel tempo;
-   stampa della dashboard;
-   gestione di più indagini con codici differenti;
-   sistema di recupero o reinvio delle risposte in caso di interruzione
    della connessione;
-   controlli amministrativi per la gestione dell'archivio.

------------------------------------------------------------------------

## Licenza e utilizzo

Il progetto è destinato ad attività didattiche e di raccolta dati
sull'utilizzo dell'Intelligenza Artificiale nel contesto scolastico.

Per modalità di riutilizzo, distribuzione o modifica del codice, fare
riferimento alla licenza eventualmente presente nel repository. Questo
README non attribuisce al progetto una licenza specifica.

------------------------------------------------------------------------

## Riepilogo

**AI School Quest** è un'applicazione web per realizzare un'indagine
condivisa sull'utilizzo dell'Intelligenza Artificiale a scuola.

La combinazione di GitHub Pages e Firebase permette di distribuire
l'applicazione online e raccogliere le risposte in un archivio
centralizzato, consultabile attraverso una dashboard con indicatori,
grafici, filtri e modalità proiezione.

Il codice è organizzato per consentire l'utilizzo da parte di più classi
e dispositivi, mantenendo le sessioni distinte e permettendo di
aggregare i risultati tramite un codice condiviso.
