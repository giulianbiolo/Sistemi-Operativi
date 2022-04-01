<script type="text/javascript"
  src="https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.0/MathJax.js?config=TeX-AMS_CHTML">
</script>
<script type="text/x-mathjax-config">
  MathJax.Hub.Config({
    tex2jax: {
      inlineMath: [['$','$'], ['\\(','\\)']],
      processEscapes: true},
      jax: ["input/TeX","input/MathML","input/AsciiMath","output/CommonHTML"],
      extensions: ["tex2jax.js","mml2jax.js","asciimath2jax.js","MathMenu.js","MathZoom.js","AssistiveMML.js", "[Contrib]/a11y/accessibility-menu.js"],
      TeX: {
      extensions: ["AMSmath.js","AMSsymbols.js","noErrors.js","noUndefined.js"],
      equationNumbers: {
      autoNumber: "AMS"
      }
    }
  });
</script>

# Sistemi Operativi - Lezione 6  

Come abbiamo visto nell'ultima lezione, un webserver resterà in attesa fino a che non riceve una richiesta.  
A quel punto viene eseguita una funzione che gestisce la richiesta.  
Un webserver sarà di default in stato Blocked, dopodichè all'arrivo di una richiesta entrerà in stato Waiting fino a che non entrerà in Running eseguendo la richiesta.  
C'è un problema, in questo lasso di tempo di esecuzione non stiamo più ascoltando per nuove richieste, l'idea è di creare un nuovo processo per ogni richiesta.  
Avremo un processo padre che è sempre in Waiting, all'arrivo di una richiesta viene creato un processo figlio, con una fork(), che esegue la richiesta.  

```
richiesta = attendi();
if (fork() == 0) {
  gestisci(richiesta);
}
```

Nonostante questo miglioramento, la fork() è pesante, si tratta di una syscall che copia completamente il processo padre, al giorno d'oggi quello che si fa è una via di mezzo tra le due soluzioni, la via monolitica e la via ad albero.  
Si sfruttano infatti i thread.  

---
I Thread:  
---------  

Fino ad ora abbiamo associato nello stesso processo sia il possesso delle risorse che l'uso della cpu, le due caratteristiche sono però indipendenti.  
Possiamo infatti immaginare più unità di esecuzione che condividono il codice, la memoria e le altre risorse, ma che operano in modo concorrente fra loro.  
Distinguiamo dunque:  
- I processi sono le unità di possesso delle risorse.  
  In particolare un processo è associato a:  
  - Uno spazio di indirizzamento
  - Un elenco di risorse

- i **thread** sono le unità di utilizzo della cpu, uno stesso processo può essere associato a più thread.  
  In particolare, un thread è associato a:  
  - Uno stato di esecuzione [Running, Blocked, Waiting]]  
  - Un insieme di registri CPU [in particolare Program Counter (PC)]  
  - Uno stack [tra i registri anche lo Stack Pointer (SP)]]  

In uno stesso processo (identificato dal PCB) possiamo avere più flussi di esecuzione concorrenti, ciascuno con i propri registri e il proprio stack, che condividono la stessa pagina di memoria.  
Il contesto specifico di ciascun thread è conservato in un ***"thread control block"***.  
Inoltre ogni thread avrà bisogno del proprio stack separato in quanto eseguendo linee di esecuzione diverse, possono esserci chiamate a subroutine che necessitano di un pointer di ritorno, quindi bisogna assicurarsi che i due thread non condividano gli stessi indirizzi di ritorno.  

```
richiesta = attendi();
crea_thread(gestisci, richiesta);
```

I Vantaggi di utilizzare i thread sono:  
- La creazione di un nuovo thread è molto più veloce dell'esecuzione di una fork().  
- Il context switch tra thread è più veloce che non tra processi.  
  Il motivo è che thread diversi fanno riferimento alle stesse pagine di memoria, quindi la MMU non deve caricare più pagine di memoria.  
  Banalmente si può accedere da due thread diversi alle stesse variabili globali in un codice C.  

Ci possono essere varie implementazioni di thread su un sistema operativo:  
- User Level Threads: Il kernel non sa cosa siano i thread quindi vengono gestiti e definiti ad user-level ed il kernel vede solo il singolo processo.  
- Kernel Level Threads: Il kernel gestisce i thread ed i thread a livello utente sono speculari a quelli realmente esistenti a livello kernel che poi sono figli dello stesso processo.  
- Il kernel può dare un limite di thread control block per ogni processo, per cui se vuoi avere più di x-thread dovrà pensarci una libreria a livello utente a simulare ulteriori thread a livello utente.  
  Abbiamo quindi un sistema di threading a due livelli.  

Per quanto ogni processo costituisca un’unità separata, può essere necessario far collaborare più processi fra loro.  
La cooperazione richiede comunicazione, e un sistema operativo mette a disposizione molte forme di comunicazione fra processi (**interprocess communications, IPC**) che si possono suddividere in due categorie principali:  
- Condivisione di memoria (**shared memory**)  
- Passaggio di messaggi (**message passing**)  

La memoria condivisa è utile per mettere in comune grandi quantità di dati e strutture dati complesse.  
Porta con se un problema però, ossia l'introduzione di situazioni di ***nondeterminismo***.  
Se due processi eseguono un'operazione sulla stessa memoria condivisa, concorrentemente, il risultato è indeterminato. Banalmente incrementare una variabile può portare al risultato di un solo incremento, se i due processi caricano nei registri della cpu la variabile nello stesso istante.  

I processi che condividono memoria devono porre particolare attenzione quando effettuano accessi in scrittura: se due processi devono scrivere nello stesso momento, in generale non è possibile prevedere quale processo prevarrà se la scrittura riguarda una parola di più byte, potrebbe avvenire a cavallo di un cambio di contesto, oppure su processori diversi, e il risultato finale potrebbe addirittura essere un miscuglio delle due parole.  

Un problema tipico nel caso della condivisione di memoria è il problema **Produttore - Consumatore**:  
Produttore:  

```
condividi
  record dato
  bool valido
valido = false;
ripeti
  Produci info...
  finchè valido
    Aspetta...
  dato = informazione;
  valido = true;
```

Consumatore:

```
ripeti
  finchè non valido
    Aspetta...
  informazione = dato;
  valido = false;
  Utilizza l'informazione...
```

Però questo sistema è abbastanza problematico, il produttore può produrre solo un elemento alla volta, il che non è per nulla efficiente.  
Possiamo implementare un buffer circolare:  

Produttore:  

```
condividi
  record buffer[DIMENSIONE BUFFER]
  int in, out;
  in = 0; out = 0;
  ripeti
    Produci l’informazione
    finchè (in+1) % DIMENSIONE BUFFER = out
      Buffer pieno, non fare nulla
      buffer[in] ← informazione
      in = (in+1) % DIMENSIONE BUFFER
```

Consumatore:  

```
ripeti
  finchè in = out  
    Aspetta...  
  informazione = buffer[out]
  out = (out+1) % DIMENSIONE BUFFER
  Utilizza l’informazione...
```
