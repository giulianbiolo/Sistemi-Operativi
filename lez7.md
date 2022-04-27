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

# Sistemi Operativi - Lezione 7  

## Passaggio di Messaggi Tra Processi:

Un sistema a passaggio di messaggi consiste in un canale messo a disposizione dal sistema operativo per cui attraverso alcune direttive si riesce a far comunicare due processi.  
Le direttive saranno del tipo:    
- send() - invio di messaggi
- receive() - ricezione di messaggi

A differenza della memoria condivisa qui il sistema operativo si prende in carico il compito di sincronizzare i processi che intendono comunicare.  
Le implementazioni possono differire per molti aspetti tra cui il modo di identificare il canale, comunicazione per via sincrona o asincrona, buffering automatico o esplicito, dimensione dei messaggi, comunicazione uni o bilaterale, ecc...  

La comunicazione può avvenire in maniera diretta, per cui un processo assieme al messaggio fornisce anche l'ID del processo destinatario.  
Oppure può avvenire in maniera indiretta, per cui il processo invia un messaggio assieme all'ID di un canale, dopodichè qualunque altro processo potrà leggere il contenuto del canale a patto di conoscere l'ID del canale.  

Lo standard POSIX prevede varie implementazioni di canali di comunicazione tra processi, tra cui:  
- Pipes:
  - Code FiFo
  - Sincrone
  - Unidirezionali
  - Create solo fra genitore e figlio
- Named Pipes:
  - Code FiFo
  - Sincrone
  - Bidirezionali
  - Istanziabili fra processi arbitrari
- Message Queues:
  - I Messaggi sono blocchi di dati accompagnati dalle loro dimensioni
  - Hanno una priorità, il primo ad essere estratto è il più vecchio con priorità più elevata
  - Un processo può informarsi sullo stato della coda(quanti messaggi contiene, la dimensione massima, ecc...)
- Socket:
  - Comunicazione fra processi sulla stessa macchina o fra macchine in rete
  - Orientati al flusso di byte o allo scambio di messaggi
  - Bidirezionali
  - Asincroni

