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

# Sistemi Operativi - Lezione 5  

Come abbiamo visto nell'ultima lezione ogni processo può trovarsi in uno tra molti stati, la rappresentazione di ciò nella realtà è una lista.  
Per esempio troveremo una lista(queue) di Waiting, in cui gli elementi saranno i PCB dei processi in attesa di essere svolti.  
Ogni PCB(Process Control Block) conterrà tutti i valori dei registri della CPU al momento di pausa, ossia conterrà lo stato del processo.  

---
Lo Scheduler:
-------------

Le code dei processi non sono strutture FIFO rigide, infatti, la scelta di quale processo eseguire in un determinato istante può dipendere da vari fattori, inclusa la sua criticità, il tempo già trascorso in coda, il possesso di risorse che tengono occupati altri processi, e così via.  
La selezione dei processi da eseguire è operata da un componente del SO detto **scheduler**.  
I suoi obbiettivi sono:  
- massimizzare l'uso della CPU evitando tempi morti  
- passare rapidamente da un processo all'altro in modo che tutti si vedano eseguiti senza soluzione di continuità  
- privilegiare processi critici per il sistema  

Distinguiamo due tipi di scheduler:  
- A breve termine: sceglie, tra i processi in Waiting, il prossimo da portare in stato Running.  
- A lungo termine: scegli quali processi avviare e portare in memoria principale, invocato meno di frequente, determina il grado di multiprogrammazione del sistema (numero di processi in memoria).  

---
Operazioni Sui Processi:
------------------------

Processo ***'init'*** all'avvio ha come compito avviare altri processi ciascuno dei quali compie dei compiti specifici.  
Su qualsiasi macchina unix avremo quindi un albero di processi, tutti risalenti ad una root comune che è init.  
Un processo come init, per chiamarne un altro, chiama una syscall chiamata ***'fork()'***.  
La fork() a partire da un processo, ne fa un clone, clonando pure tutte le informazioni della PCB.  
Nel PCB del processo appena creato avremo un nuovo valore di PID e PPID, per cui il PID sarà un numero arbitrario determinato dallo stato del sistema al momento della creazione e PPID sarà il PID del padre, ossia del processo che ha chiamato la fork().  
Da ciò deriva il nome fork, stiamo biforcando il processo.  

Per distinguere processo padre da processo figlio la syscall di fork() ritorna un valore CPID, che è il PID del processo figlio.  
Spesso poniamo il padre in uno stato di Blocked fino alla fine dell'esecuzione del figlio.  

I processi possono terminare in vari modi:  
- Dal padre, che ha il diritto di chiudere il processo figlio.  
- Attraverso invocazione di una syscall chiamata ***'exit()'***, dall'interno del processo.  
- Attraverso una syscall chiamata ***'kill()'***, dall'esterno del processo, invocato da un processo con sufficienti privilegi.  
- Se il SO ritiene che il processo sta usando troppe risorse, o a causa di errori di protezione (istruzioni privilegiate), o a causa di accesso ad aree di memoria non consentite.  
