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

---
Algoritmi E Processi:
---------------------

### Sincronizzazione Tra Processi:

Volendo noi sincronizzare più processi dobbiamo renderci conto che esistono determinate sezioni di codice, per quanto riguarda l’accesso a una risorsa (ad esempio memoria condivisa), che devono essere eseguite in modo mutuamente esclusivo.  
In ogni istante infatti, solo un processo può trovarsi nella sezione di codice critica, che quindi va protetta da sezioni di codice d'ingresso e di uscita da parte di entrambi i processi.  
Vediamo ora alcune soluzioni a questo problema.  

### L'algoritmo del fornaio:

I vari processi sono affiliati ad un numero in un array, quando termina una processo pone a 0 il proprio numero. Ogni processo viene servito a partire dal numero minore.  
N processi e un vettore int numeri[N]={0,0,...,0};  
Come la fila che si fa al fornaio del supermercato.  
Un processo nuovo si assegnerà il numero maggiore tra tutti i processi già presenti + 1.  
L'algoritmo alla nascita del processo i-esimo sarà quindi:  
```c++
ripeti:
  numero[i] <- max(numero[]) + 1;
  for j = [0,...,N-1]:
    while(numero[j] != 0 && numero[j] < numero[i]); // Aspetto
  SEZIONE CRITICA
  numero[i] <- 0;
  SEZIONE NON CRITICA
```
Anche qui c'è però una situazione non deterministica, ossia se due processi eseguono l'istruzione di assegnazione del numero nello stesso istante, otterranno lo stesso numero, per cui entreranno entrambe assieme, questo problema è risolvibile semplicemente eseguendo il processo con indice minore a parità di numero di attesa.  

### Soluzioni Hardware:  

Una soluzione abbastanza semplice è disattivare direttamente gli interrupt nel momento in cui si entra nella sezione critica, in tal modo il processo non può essere fermato, ma ciò porta con se un grande problema. Se la sezione critica contiene codice lento rischia di rallentare l'intera esecuzione della macchina.  

### Test And Set:  

Una possibile istruzione offerta da alcune architetture è la test-and-set, che opera su una cella di memoria booleana.  
```c++
bool test_and_set(bool &var) {
  bool tmp = var;
  var = TRUE;
  return tmp;
}
```
L’accesso a una sezione critica verrebbe così regolato da una variabile booleana condivisa lock:  
```c++
bool lock = FALSE;
while(1) {
  while(test_and_set(lock)); // solo il primo processo che trova lock==FALSE passa,
                             // gli altri la ritrovano subito TRUE.
  // SEZIONE CRITICA
  lock = FALSE;
  // sezione non critica
}
```

### Compare And Swap:  

L’istruzione test_and_set è un caso particolare di un’istruzione più completa:  
```c++
int compare_and_swap(int &var, int obiettivo, int nuovo_valore) {
  int tmp = var;
  if (var == obiettivo) var = nuovo_valore;
  return tmp;
}
```
Un processo $P_i$ potrebbe, in linea di principio, vedersi sempre passare avanti qualche altro processo, rimanendo bloccato indefinitamente ***(starvation)***.  

### Semafori:  

Le soluzioni presentate prima, per quanto funzionali, presentano dei problemi fondamentali:  
- non sono banali da realizzare e necessitano codice ad hoc.  
- sono basate su busy waiting, quindi sprecano CPU  

Un semaforo (intero) è una variabile intera S a cui si accede attraverso due primitive atomiche fornite dal sistema:  
- signal(S) ne incrementa il valore di 1;
- wait(S) tenta di decrementare il valore di S, ma finchè S = 0 tiene bloccato il processo.  

Un'implementazione più semplice è quella del semaforo binario, che ha lo stesso potere rappresentativo di un semaforo intero, ed è detto ***mutex***:  
```c++
void signal(bool &S) {
  S = TRUE;
}
void wait(bool &S) {
  while (!S);
  S = FALSE;
}
  .
  .
  .
semaforo_binario S = TRUE;
while (1) {
  wait(S);
  // SEZIONE CRITICA
  signal(S);
  // sezione non critica
}
```
