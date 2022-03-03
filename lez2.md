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

Sistemi Operativi - Lezione 2
=============================

Una CPU è normalmente connessa a varie telescriventi, ossia dispositivi che permettono a delle persone di interfacciarcisi per inviarci dei programmi da eseguire.  
Normalmente la CPU legge ogni telescrivente ed esegue un programma alla volta ma ciò implica che la CPU dovrà terminare completamente l'esecuzione di un programma prima di poter passare al successivo.  
Qui nasce il concetto di **Processo** diviso in:  
- CPU Burst  
- I/O Burst  

Se un programma in un certo momento utilizza la CPU, gli altri programmi sono in attesa di qualche input, ora il programma che era in esecuzione visto che deve chiedere un input passa in modalità I/O, quando ciò succede uno dei programmi precedentemente in attesa, in modalità I/O, si sposterà in modalità CPU.  
I diversi programmi quindi si alternano nell'uso della CPU ottimizzando i tempi, facendo si che la CPU sia utilizzata in modo efficiente.  

L'alternativa a questo tipo di sistema è, modificando a livello hardware il processore, far alternare alla CPU i programmi, mettendoli in pausa, senza che questi se ne rendano neppure conto, questo tipo di sistemi è detto **Time-Sharing**.  

**Unix:**
- Nasce negli anni '60  
- Nasce come soluzione monoutente a MULTICS  
- Presto diventa multiutente  
- Standard IEEE POSIX
- Sviluppa vari dialetti: BSD, Linux, ecc...  
- Mac OS X si basa su BSD  
- Android si basa su Linux  

---
Quarta Generazione:  
-------------------  

Negli anni '80 nascono i **microprocessori**.  
Si diffondono i dischi magnetici, i **floppy disk**.  
Inoltre nascono vari **sistemi operativi**:  
- **CP/M**, sviluppato da Digital Research nel '77 basato sul famoso 8080 di Intel  
- **MS-DOS**, sviluppato da Microsoft nel '82 per IBM PC basato su processori Intel 8086 e loro successori, poi diventerà la base per Windows 98.  
- **Apple Macintosh System Software**, in seguito **Mac OS**, sviluppato da Apple nel '84 su processore Motorola 68000.  

---
Quinta Generazione:
-------------------  

Dal 1990 nascono i **dispositivi mobili**:  
- Symbian OS  
- BlackBerry OS  
- Vari sistemi operativi in stile Windows per mobile  
- **Android**, sviluppato da Google nel '08  

---
La Struttura Hardware del Computer:
-----------------------------------  

La struttura base di un computer è composta da:  
- **CPU**: componente che svolge i calcoli  
- **BUS di Memoria**: fa da collegamento tra CPU ed altre componenti  
- **Memoria RAM**: memorizza dati molto velocemente in modo volatile  
- **Memoria ROM**: memorizza dati più lentamente ma in modo persistente  
- **I/O**: dispositivi di input e output generici  
- **DMA**: Direct Memory Access, si occupa del trasferimento di dati tra i diversi dispositivi. La CPU istruisce la DMA di leggere un particolare byte di I/O e trasferirlo in memoria per esempio. Si scarica la CPU da questo lavoro spostandolo al DMA.  
Chiaramente la DMA utilizzerà il BUS di Memoria, quindi non risolve possibili problemi di bottleneck o di traffico sul BUS.  

All'interno della CPU inoltre troveremo delle **Cache** che sono sostanzialmente delle memorie molto piccole in cui l'hardware tenta di precaricare i dati utili, per esempio, se la CPU chiede i dati ad un particolare indirizzo, la Cache precarica i dati adiacenti, che statisticamente parlando serviranno poco dopo alla CPU, in questo modo si velocizza l'accesso ai dati tentando di prevedere di quali dati si avrà bisogno, allo stesso tempo si distribuisce anche il carico sul BUS.  

L'I/O è suddiviso in varie porte numerate. Ogni dispositivo ha a sua disposizione un certo intervallo di indirizzi.  

La CPU al suo interno ha una serie di **Registri** che sono delle memorie velocissime ma allo stesso tempo piccolissime, utilizzate per effettuare operazioni di calcolo da parte della **ALU**.  
Non tutti i registri sono di uso generale, ce ne sono alcuni di speciali:  
- **Program Counter**: salva l'indirizzo di memoria del prossimo **OPCODE**, ossia la prossima istruzione da eseguire.  
- **Stack Pointer**: salva l'indirizzo di memoria del top della **Stack**, ossia l'indirizzo di memoria del primo elemento della **Stack**, per permettere le operazioni di **PUSH** e **POP**.  
- **Flags**: registro da 16 - 32 bit per cui ogni singolo bit ha un significato particolare, come per esempio il **carry flag** che indica se l'ultima operazione aritmetica ha avuto un riporto, ecc...  
Alcuni bit determinano anche il livello di ***'privilegio'*** delle istruzioni che si stanno eseguendo, che determinano la possibilità di eseguire alcune istruzioni magari vietate all'utente che permetterebbero di manipolare zone di memoria dedicate al sistema.  

Può succedere che durante l'esecuzione di un'istruzione il programma tenti di accedere a zone di memoria invalide o per esempio viene eseguita una divisione per $0$, in tali situazioni entra in gioco un interrupt a livello hardware chiamato **TRAP**.  
Il **Trap Handler** lancia una **TRAP** a privilegio elevato, che si occuperà di gestire l'eccezione concludendo l'esecuzione del programma.  

Gli **Interrupt** sono delle linee in ingresso al processore specifiche, per cui un dispositivo può *'avvertire'* il processore della presenza di dati pronti a venir elaborati, per cui il processore metterà in pausa ciò che stava facendo e avvierà una routine di gestione dell'interrupt, intervenendo quindi immediatamente nell'elaborazione di quei dati.  
Una CPU avrà più linee di interrupt che codificheranno un numero di priorità ove 0 è priorità massima e 7 è priorità minima.  

**Memory Management Unit** o **MMU**: è un sistema hardware che serve per schermare gli indirizzi interni alla CPU da quelli esterni, facendo da traduttore per tradurre gli indirizzi fisici della memoria in indirizzi logici e viceversa.  
Ad ogni processo avremo indirizzi di memoria logici diversi, in modo tale che sia impossibile per un programma accedere ad indirizzi di memoria diversi da quelli previsti per esso.  

---
Gestione Dei Processi:  
----------------------  

Un **processo** è un programma in esecuzione.  
Qui parleremo di come si gestiscono i processi ed in particolare i possibili problemi di **deadlock** causati dall'accesso alla stessa memoria da parte di due processi, contemporaneamente.  

---
Gestione Della Memoria:  
-----------------------  

La memoria principale è veloce ma volatile, costosa e limitata.  
Il codice dei processi viene caricato in memoria centrale prima di essere eseguito, proprio in virtù della sua velocità.  
Il sistema operativo ha alcune responsabilità:  
- Allocazione e rilascio dello spazio di memoria secondo le necessità dei processi.  
- Spostamento verso il disco di aree di memoria raramente usate per liberare spzio per altri processi (memoria virtuale).  
- Gestione della frammentazione dello spazio libero in presenza di continue allocazioni e rilasci.  
