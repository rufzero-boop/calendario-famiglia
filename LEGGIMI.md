# Calendario di famiglia — guida all'installazione

Il pacchetto contiene:

| File | A cosa serve |
|---|---|
| `index.html` | L'app vera e propria |
| `manifest.webmanifest`, `icon-*.png` | Nome e icona per installarla su Android |
| `sw.js` | Permette di aprirla anche senza connessione |
| `firestore.rules` | Regole di sicurezza del database (da incollare in Firebase) |

**Senza configurare nulla** l'app funziona in *modalità prova*: aprendo `index.html` i dati restano solo su quel telefono. È utile per provarla. Per condividerla seguite i passi qui sotto: si fanno una volta sola, in circa 30–45 minuti, da PC.

Costo: zero. Il piano gratuito di Firebase (Spark) ha limiti molto più alti di quelli che userà una famiglia.

---

## 1. Creare il progetto Firebase

1. Vai su https://console.firebase.google.com e accedi con un account Google.
2. **Crea un progetto**, ad esempio `calendario-famiglia`. Google Analytics non serve: puoi disattivarlo.

## 2. Attivare l'accesso con email e password

1. Nel menu a sinistra: **Build → Authentication → Inizia**.
2. Nella scheda *Metodo di accesso* abilita **Email/password**, solo la prima opzione, e salva.

## 3. Creare il database

1. **Build → Firestore Database → Crea database**.
2. Scegli una località europea (es. `eur3` o `europe-west`) e la **modalità di produzione**.
3. Quando il database è pronto, apri la scheda **Regole**. Cancella il contenuto, incolla tutto il file `firestore.rules` e premi **Pubblica**.

## 4. Collegare l'app a Firebase

1. Clicca l'ingranaggio ⚙️ → **Impostazioni progetto** → in basso *Le tue app* → icona **Web `</>`**.
2. Dai un nome (es. `calendario`). Firebase Hosting non serve. Registra.
3. Firebase mostra un blocco `const firebaseConfig = { apiKey: ..., ... }`. Copia solo la parte tra le graffe `{ ... }`.
4. Apri `index.html` con un editor di testo (Blocco note va bene, meglio ancora VS Code). In cima allo script cerca la riga:
   ```js
   const FIREBASE_CONFIG = null;
   ```
   e sostituisci `null` con quello che hai copiato:
   ```js
   const FIREBASE_CONFIG = {
     apiKey: "AIza...",
     authDomain: "calendario-famiglia.firebaseapp.com",
     projectId: "calendario-famiglia",
     storageBucket: "...",
     messagingSenderId: "...",
     appId: "..."
   };
   ```
   Questi valori **non sono segreti**: sono fatti per stare in una pagina web. La protezione dei dati la fanno l'accesso con password e le regole del punto 3.

## 5. Pubblicare l'app (GitHub Pages)

1. Crea un account gratuito su https://github.com, se non ce l'hai.
2. **New repository** → nome `calendario-famiglia`, visibilità **Public**. Crealo.
3. Nella pagina del repository: **Add file → Upload files**. Trascina tutti i file del pacchetto (index.html, sw.js, manifest, icone; LEGGIMI e rules sono facoltativi). Poi **Commit changes**.
4. **Settings → Pages**. In *Build and deployment* scegli *Deploy from a branch*, branch `main`, cartella `/ (root)`, e salva.
5. Dopo un minuto l'app è online su `https://TUONOME.github.io/calendario-famiglia/`.
6. Torna in Firebase: **Authentication → Impostazioni → Domini autorizzati → Aggiungi dominio** e inserisci `TUONOME.github.io`. Serve per le email di recupero password.

## 6. Installare sui telefoni

**Il primo di voi (crea la famiglia):**
1. Apri l'indirizzo in **Chrome** → menu ⋮ → **Installa app** (o *Aggiungi a schermata Home*).
2. Apri l'app → inserisci email e password → **Crea account**.
3. **Crea una nuova famiglia**.
4. Menu ⋯ → *Persone*: rinomina "Lei", "Figlio 1", "Figlio 2" con i nomi veri, scegli i colori e **Salva impostazioni**.
5. Sempre nel menu, in *Famiglia condivisa*, premi **Condividi codice** e mandalo all'altro genitore (es. su WhatsApp).

**L'altro genitore:**
1. Installa l'app allo stesso modo e crea il proprio account con la propria email.
2. Scegli **Unisciti con il codice** e incolla il codice ricevuto.

Da questo momento vedete e modificate entrambi lo stesso calendario. Le modifiche arrivano in pochi secondi. Se si è senza rete si continua a usare l'app, e i dati si sincronizzano appena torna la connessione.

## 7. Recuperare i turni dalla prima versione

Nella vecchia app turni: menu ⋯ → **Esporta backup**. Nella nuova: menu ⋯ → **Importa backup** → scegli quel file. I turni vengono assegnati alla persona indicata in *Chi fa i turni*.

---

## Come funziona il controllo di copertura

L'app segna con ⚠️ e bordo rosso i giorni in cui, in un certo momento, **nessun adulto è libero**:

- **Fasce da coprire**: si impostano dal menu. Di default c'è *Notte 22:00–06:00* tutti i giorni. Se ne aggiungono altre a piacere, ad esempio *Uscita scuola 16:00–16:30 lun–ven*.
- **Attività dei figli**: ogni impegno con orario di una persona di tipo *Figlio/a* richiede un adulto libero. Si può togliere la spunta "Serve un adulto" quando non serve.

Un adulto è considerato occupato durante:
- i turni con orario (Mattina, Pomeriggio, Notte, Corsi), secondo gli orari impostati nel menu. Riposo, Smonto, Ferie e Altro non lo rendono occupato;
- i suoi impegni con orario. Quelli "tutto il giorno" non contano. Se l'ora di fine è prima dell'inizio, l'impegno prosegue nel giorno dopo, come una notte in cantiere 22–06.

Toccando un giorno segnalato si vede quale fascia è scoperta e chi è impegnato in cosa.

## Aggiornare l'app in futuro

Carica i file modificati su GitHub con *Add file → Upload files*, sovrascrivendo i vecchi. In `sw.js` aumenta il numero di versione (`famcal-v1` → `famcal-v2`): così i telefoni scaricano la versione nuova al successivo avvio. I dati non si toccano, perché stanno su Firebase.
