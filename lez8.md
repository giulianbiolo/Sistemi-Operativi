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

# Sistemi Operativi - Lezione 8  

---
Gli Scheduler:
--------------

L'obbiettivo è tenere la CPU occupata per il maggior tempo possibile.

Ci sono due tipi di scheduler:
- A lungo termine (Job Scheduler)
- A breve termine (CPU Scheduler)

Lo scheduler a breve termine viene invocato molto spesso, per ciò dev'essere molto veloce, nell'ordine dei millisecondi, per evitare di occupare troppo tempo della CPU.  
Lo scheduler a lungo termine viene chiamato meno spesso e per ciò si può permettere di essere lento, nell'ordine dei secondi.  

Gli scheduler della CPU si divono a loro volta in:  
- Non A Prelazione (Non-Preemptive)  
  Il processo che detiene la CPU non la rilascia fino al termine del burst.
- A Prelazione (Preemption)  
  Il processo che detiene la CPU può essere forzato a rilasciarla prima del termine del burst.  

Vediamo ora alcuni algoritmi di scheduling:  
- **FCFS (First Come First Served)**  
  Il processo che arriva prima viene processato prima.  
  Il concetto è quello di una coda FIFO di processi.  
  Come pro è molto semplice da implementare, come contro succede che processi brevi si accodino ai processi lunghi precedentemente arrivati, che è un problema in contesti interattivi.  
- **SJF (Shortest Job First)**  
  Il processo che ha il burst più corto viene processato prima.  
  Ne esistono due varianti, la variante Preemptive e la variante Non-Preemptive.  
  Nella sua variante Preeemptive, se arriva un nuovo processo con burst più breve del tempo di burst rimanente al processo corrente , quest'ultimo viene rimosso dalla CPU per far spazio a quello appena arrivato, in questo caso l'algoritmo prende il nome di **SRTF (Shortest Remaining Time First)**.  
  SJF di per se è ottimo, si ottiene con esso il minimo tempo medio di attesa.  
  Inoltre in linux SJF è uno scheduler a priorità, $priorità = \frac{1}{burst}$  
  Però rimane un problema, c'è il rischio che processi a bassa priorità non vengano mai chiamati, la soluzione è implementare un ulteriore termine di *"invecchiamento"*, per cui si aumenta la priorità col passare del tempo.  
- **HRRN (Highest Response Ratio Next)**  
  Algoritmo a priorità non-preemptive, $priorità = 1+ \frac{attesa}{burst}$  
  Va effettuato un ricalcolo ad ogni fine processo.  
  Nel complesso risolve il favoritismo a job corti da parte di SJF.  
- **RR (Round Robin)**  
  Scheduling basato su time-out, ad ogni processo viene assegnato un quanto del tempo di CPU, al termine di questo quanto il processo è prelazionato e messo in attesa in una ready queue, una coda circolare.  
  Per $n$ processi con un quanto $q$ avremo che ogni processo ottiene $t=\frac{q}{n}$ millisecondi di CPU di tempo di esecuzione, prima di venir prelazionato, per poi aspettare $(n-1)q$ millisecondi.  
  Si tratta praticamente di un FCFS con prelazione, difatti un $q$ troppo grande si comporta proprio come un FCFS.  
  Allo stesso modo bisogna stare molto attenti a mettere $q$ troppo piccoli per evitare di avere un tempo di context switching troppo grande.  
- **Code Multilivello**  
  L'idea è che si abbiano delle code su vari livelli prefissate, un processo viene allocato definitivamente ad una di queste code, dopodichè se l'implementazione lo permette (code adattive), il processo potrà spostarsi da una coda all'altra a seconda delle sue caratteristiche, ciò viene usato anche per implementare l'invecchiamento.  
  Esempio:  
  - Coda $Q_0$: RR con $q=8$ ms  
  - Coda $Q_1$: RR con $q=16$ ms  
  - Coda $Q_2$: FCFS  
  La Cpu serve nell'ordine $Q_0, Q_1, Q_2$, e si serve la coda successiva solo se tutte le precedenti sono vuote.  

