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

# Sistemi Operativi - Lezione 3

## Tipi Di Sistemi Operativi:

### **MainFrame:**
Orientati all'I/O, trasferimento dati, elaborazione di grosse moli di dati.
- **Batch Processing:**  
  elaborazione di job di routine senza supervisione.
- **Transaction Processing:**  
  brevi operazioni da gestire rapidamente.
- **Timesharing:**  
  job richiesti dagli utenti da eseguire in _"parallelo"_.

### **Server:**
Servono più utenti/richieste via rete. Perettono la condivisione di hardware e software.
- **Periferiche:**  
  stampa, disco.
- **GPU / HW Acceleration:**  
  tempi di calcolo ristretti.
- **Servizi Web.**

### **Multiprocessore:**
Orientati a bilanciare carico fra CPU.

### **Personal Computer:**
Enfasi sull'interfaccia utente.

### **Sistemi Mobili:**
Bassa potenza di calcolo, memoria limitata, restrizioni nel consumo energetico, enfasi sull'interfaccia utente.

### **Embedded:**
Fatti per macchine ancora meno potenti, con la garanzia che l'hardware e soprattutto il sdoftware che devono eseguire non cambierà ed è fidato.  
Spesso completamente in ROM (_"firmware"_), a volte semplicemente un bootloader.

### **Real-Time:**
Per applicazioni critiche. Offrono garanzie sui tempi di utilizzo delle risorse.

---

## Architettura dei Sistemi Operativi:

### **Requisiti:**

- **Lato Utente:**  
  UI facile da imparare e da usare, affidabile, sicuro, veloce.
- **Lato Programmatore:**  
  Facile da progettare, realizzare e mantenere, efficiente e privo di errori.

### **Realizzazione Pratica:**

I SO sono collezioni di numerosi programmi, generalmente scritti lungo archi di tempo estesi da molte persone.  
Ora tipicamente si utilizzano linguaggi di _"alto"_ livello come C, C++, ecc... per rendere più leggibile e manutenibile il codice che se venisse scritto in assembly sarebbe si veloce ma completamente illeggibile.  
Le ragioni per l'uso di codice ad alto livello sono:

- Facilità di scrittura, debug, manutenibilità
- Portabilità attraverso diverse architetture
- I Compilatori moderni sono molto efficienti nell'ottimizzare il codice, quindi le prestazioni nello scrivere un SO con un linguaggio ad alto livello non ne soffriranno.

### **Stratificazione:**

Al Livello più basso abbiamo l'hardware, poi al primo livello c'è il kernel ed infine al di sopra ci sono le applicazioni, supportate a loro volta da GUI e Shell, compilatori ed interpreti e librerie di sistema.

Allo stesso modo c'è una divisione in termini di privilegi:  
| Ring Level |   Privilegi    |
| :--------: | :------------: |
|  Ring -1   |    Hardware    |
|   Ring 0   |     Kernel     |
|   Ring 1   | Device Drivers |
|   Ring 2   | Device Drivers |
|   Ring 3   |  Applications  |

Ciò permette di eseguire varie verifiche sulle operazioni che si intende eseguire per guadagnare un livello di privilegio, per cui si minimizzano i danni nel caso di operazioni non permesse.  
Si segue il principio del minimo privilegio: **PoLA, Principle of Least Authority**.

### **Sistemi A MicroKernel:**  

La maggior parte del codice all'interno di una routine di gestione di un componente, ad esempio, il filesystem, non richiede direttamente operazioni privilegiate. Ad esempio se devo manipolare strutture dati, e può essere fatto in userspace, in obbedienza al PoLA. Solo quando queste strutture dati sono pronte a essere trasferite all'I/O diventa necessario passare a un livello privilegiato.   
Questo porta a una filosofia apparentemente opposta al livello a strati: tutto il codice possibile è piazzato a livello utente, il kernel stesso viene ridotto a una piccola collezione di componenti essenziali, riducendosi per l'appunto a un microkernel.  
Il precursore dei sistemi a microkernel è **Mach** (CMU, $\approx 1985$) ed i principi sono utilizzati anche da **GNU/Hurd** (mai completato perchè soppiantato da Linux) e da **MINIX**.  
Anche il kernel di Mac OS X, **Darwin**, utilizza parti di **Mach**.  
Sotto questa nuova struttura diventa fondamentale la componente che coordina i vari componenti a livello utente attraverso l'astrazione del **message passing**, ogni operazione che richiede il ricorso a moduli di sistema comporta la comunicazione fra moduli attraverso quest'interfaccia.  
