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

# Sistemi Operativi - Lezione 4  

Un processo in memoria è diviso in:  
| |
|:--:|
| Stack |
| \| |
| \| |
| Heap |
| .data |
| .text |

Le zone di memoria .data e .text vengono caricate dalla memoria di massa.  
In .data troveremo le variabili globali del programma.

Un processo può essere in esecuzione, in attesa di input, eccetera.  
Notare che i nomi in figura non sono
standardizzati.  
- Created — processo in corso di istanziazione
- Waiting — in attesa di essere preso in carico da una CPU
- Running — in corso di esecuzione
- Blocked — in attesa di un evento esterno (I/O)
- Terminated — completato (o interrotto).

In un sistema in multiprogrammazione, un processo si alterna molto rapidamente fra gli stati Waiting
e Running, finendo occasionalmente nello stato Blocked se richiede un input.  

Il periodo di tempo in cui un processo rimane nello stato Running è detto **quanto** ed è compito del
sistema operativo suddividere il tempo CPU in quanti e assegnare ciascun quanto a un processo.  

Gli stati di un processo si possono modellare con un flow chart di questo tipo:  

![Image](/lez4/stati_processo.PNG)  

Un processo, dopo essere creato, va in attesa di essere preso in carico da una CPU, dopodichè se durante la sua esecuzione viene richiesto un input, il processo va in attesa di un evento esterno (I/O), stato chiamato *"blocked"*.  
Se un processo finisce il tempo a lui assegnato, ritorna in *"attesa"*.  

Il **PCB: Process Control Block** è la struttura che contiene le informazioni di contesto dei processi, in modo da poterne sospendere e riprendere l'esecuzione a piacimento.  
Queste informazioni fondamentali sono per esempio tutti i valori dei registri della CPU, ecc...  

Nel momento in cui (per una richiesta di
I/O o alla scadenza del suo quanto di tempo) la CPU, che entra in modalità supervisore, deve smettere
di eseguire un processo ed eseguire il **dispatch** di un nuovo processo.  
In dettaglio:  
- Un interrupt segnala il termine del quanto di tempo allocato al processo attualmente in stato
Running, oppure il processo avvia una richiesta di I/O; in entrambi i casi la CPU passa in
modalità privilegiata.
- Parte l’operazione di cambio di contesto (context switch):  
    – Il contesto del processo in esecuzione viene salvato nel relativo PCB.  
    – Il PCB viene accodato alla lista dei processi pertinente.  
    – Un altro PCB viene recuperato dalla lista dei processi in stato Waiting.  
    – Il contesto (registri CPU, program counter. . . ) del nuovo processo viene ripristinato sulla
base delle informazioni contenute nel nuovo PCB.
- La CPU si riporta in modalit`a utente e continua ad eseguire il nuovo processo.  
