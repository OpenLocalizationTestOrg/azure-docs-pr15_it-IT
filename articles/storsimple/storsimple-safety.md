<properties 
   pageTitle="Sicurezza del dispositivo StorSimple | Microsoft Azure"
   description="Vengono descritti considerazioni, linee guida e convenzioni di sicurezza e come installare e utilizzare un dispositivo StorSimple in modo sicuro."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/18/2016"
   ms.author="alkohli" />

# <a name="safely-install-and-operate-your-storsimple-device"></a>In modo sicuro installare e utilizzare un dispositivo StorSimple

![Icona di avviso](./media/storsimple-safety/IC740879.png)
![leggere sull'icona di avviso di sicurezza](./media/storsimple-safety/IC740885.png) **leggere sicurezza e informazioni di stato**

Leggere tutte le informazioni sulla sicurezza e integrità in questo articolo si applica al dispositivo Microsoft Azure StorSimple. Mantenere tutte le guide stampate fornite con il dispositivo StorSimple per riferimento futuro. Impossibilità di seguire le istruzioni correttamente configurare, utilizzare e assistenza per questo prodotto può aumentare il rischio di gravemente o morte o danni dispositivi. È anche disponibile una [versione scaricabile di questa guida](http://www.microsoft.com/download/details.aspx?id=44233) .

## <a name="safety-icon-conventions"></a>Convenzioni sull'icona di sicurezza

Di seguito sono riportate le icone che si trova nell'analisi le misure di sicurezza devono essere rispettate durante la configurazione e l'esecuzione del dispositivo Microsoft Azure StorSimple.

| Icona  | Descrizione  |
|:------|:-------------| 
|![Icona di rischio](./media/storsimple-safety/IC740879.png) **rischio!**|Indica una situazione pericolosa che, se non è evitato, darà come risultato in morte o gravemente. La parola segnale è limitato a situazioni estreme.| 
|![Icona di avviso](./media/storsimple-safety/IC740879.png) **avviso!**|Indica una situazione pericolosa da cui, se non è evitato, potrebbe risultare decesso o gravemente.|
|![Icona di avviso](./media/storsimple-safety/IC740879.png) **attenzione!**|Indica una situazione pericolosa da cui, se non è evitato, potrebbe risultare pregiudizio secondario o moderato.|
|![Un'icona a forma](./media/storsimple-safety/IC740881.png) **Segnalatore:**|Indica le informazioni considerate importante, ma non correlati rischio.|
|![Icona di impianti elettrici elettriche](./media/storsimple-safety/IC740882.png) **rischio elettriche impianti elettrici** |Alta tensione|
|![Icona approfondita](./media/storsimple-safety/IC740883.png) **approfondita**| |
|![Nessun utente manutenzione parti icona](./media/storsimple-safety/IC740879.png) **parti di manutenzione nessun utente**|Non hanno accesso a meno che non sia stato formato adeguatamente.|
|![Leggere la sicurezza icona avviso](./media/storsimple-safety/IC740885.png)**leggere per primo tutte le istruzioni**| |
|![Suggerimento rischio icona](./media/storsimple-safety/IC740886.png) **Suggerimento rischio**| |


## <a name="handling-precautions"></a>Gestione precauzioni

![Icona di avviso](./media/storsimple-safety/IC740879.png) ![icona approfondita](./media/storsimple-safety/IC740883.png) **avviso!** 


Per ridurre il rischio di lesioni:

- Un simbolo completamente configurato di valutare fino a 32 kg (70 kg); non provare a sollevare personalmente.
- Prima di spostare il simbolo, assicurarsi sempre che due persone siano disponibili per gestire il peso. Tenere presente che una persona che sta tentando di sollevare il peso è in grado di sostenere alle persone.
- Non sollevare il simbolo da punti di manipolazione Power e moduli raffreddamento (PCMs) nella parte posteriore dell'unità. Questi non sono progettati per il peso.

## <a name="connection-precautions"></a>Precauzioni di connessione

![Icona di avviso](./media/storsimple-safety/IC740879.png) ![sull'icona di impianti elettrici elettriche](./media/storsimple-safety/IC740882.png) **avviso!**

Per ridurre la probabilità di pregiudizio, elettriche impianti elettrici o morte:

- Con la tecnologia di più origini AC, disconnettere tutta la potenza di fornitura per isolamento completo.

- Scollegare l'unità in modo permanente prima di spostarlo o se si ritiene che è danneggiato in alcun modo.

- Fornire una connessione di terra impianti elettrici attendibili per i cavi di fornitura di alimentazione. Verificare che terra del locale soddisfi i requisiti nazionali e locali prima dell'applicazione power.

- Assicurarsi che la connessione power viene sempre interrotta prima della rimozione del PCM dal simbolo.

- Dato che la presa in alimentazione fornitura principale disconnettere dispositivo, accertarsi che prese socket si trovino vicina alle apparecchiature e facilmente accessibili.

![Icona di avviso](./media/storsimple-safety/IC740879.png) ![sull'icona di impianti elettrici elettriche](./media/storsimple-safety/IC740882.png) **avviso!**

Per ridurre il rischio di surriscaldamento o fire da impianti elettriche connessioni:

- Fornire una fonte di alimentazione adatto con protezione overload impianti elettrici per soddisfare i requisiti di dettaglio nelle specifiche tecniche.

- Non utilizzare cavi di alimentazione biforcato (lead "Y").

- Per rispettare requisiti termici, emissione e sicurezza applicabili, non deve essere rimosso Nessuna copertina e alloggiamenti tutto è necessario inserire i moduli plug-in o la macchina valori vuoti.

- Assicurarsi che le apparecchiature venga utilizzata nel modo specificato dal produttore. Se il dispositivo viene utilizzato in modo non specificato dal produttore, la protezione fornita dall'apparecchiatura può essere vista.

![Un'icona a forma](./media/storsimple-safety/IC740881.png) **Segnalatore:**

Per il corretto funzionamento delle apparecchiature e per impedire danni prodotti:

- Le porte RJ45 in background il dispositivo sono per solo una connessione Ethernet. Questi non deve essere connesso a una rete di telecomunicazioni.

- Assicurarsi di installare il dispositivo in un rack in grado di soddisfare un design raffreddamento fronte-retro.

- Moduli tutti plug-in e lastre vuote fanno parte del simbolo di sistema. È necessario rimuoverli solo quando si sostituisce può essere aggiunti immediatamente. Il sistema non deve essere eseguito senza tutti i moduli o spazi vuoti in posizione.

## <a name="rack-system-precautions"></a>Rack sistema precauzioni

Quando si installazione il dispositivo in un rack archivio, è necessario considerare i seguenti requisiti di sicurezza.

![Icona di avviso](./media/storsimple-safety/IC740879.png) ![suggerimento rischio icona](./media/storsimple-safety/IC740886.png) **avviso!**
 
Per ridurre il rischio di pregiudizio da parte di un suggerimento su:

- La struttura rack deve supportare il peso totale degli allegati installati e dovrebbe integrare funzionalità stabilizzazione adatta per impedire rack bilancia o vengano applicate su durante l'installazione o uso normale.

- Quando si carica un rack, riempimento rack dal basso verso l'e svuotare dall'alto verso il basso.

- Diapositiva non più di un simbolo dal rack contemporaneamente per evitare il rischio di rack toppling.

![Icona di avviso](./media/storsimple-safety/IC740879.png) ![icona elettriche impianti elettrici](./media/storsimple-safety/IC740882.png) **avviso!**

Per ridurre la probabilità di pregiudizio, elettriche impianti elettrici o morte:

- Rack dovrebbe essere disponibile un sistema di distribuzione di impianti elettrici attendibili. È necessario fornire di protezione in eccesso corrente per il simbolo e non deve essere eseguito per il numero totale di allegati installato. La classificazione di consumo di energia elettrica mostrata sulla tensione riportate sul dispositivo dovrebbe essere esaminata.

- Il sistema di distribuzione elettrica deve fornire una terra affidabile per ogni simbolo nel rack.

- La struttura del sistema di distribuzione elettrica deve considerare la perdita di massa totale corrente da tutte alimentazione elementi tutti. Si noti che ciascun alimentatore in ogni simbolo è una corrente di perdita terra di 1.0 massimo mA 60 Hz volt 264. Rack può richiedere l'assegnazione di etichette con "corrente perdita elevato. Connessione di terra (terra) è essenziale prima della connessione forniture."

- Rack, quando è configurato con allegati, deve soddisfare i requisiti di sicurezza di UL 60950-1 e IEC 60950-1/EN 60950-1.

![Un'icona a forma](./media/storsimple-safety/IC740881.png) **Segnalatore:**

Per il corretto raffreddamento rack del sistema di posta:

- Assicurarsi che la struttura rack prende in considerazione simbolo massimo operativo temperatura di 35 gradi Celsius (95 gradi Fahrenheit).

- Il sistema viene gestito con l'installazione di bassa pressione, posteriore scarico (la pressione creata da rack porte e ostacoli non deve superare 5 Pascal [0,5 mm di colonna d'acqua]).

## <a name="power-cooling-module-pcm-precautions"></a>Precauzioni raffreddamento modulo (PCM) Power

Il dispositivo è progettato per funzionare con due PCMs. Ognuna del PCMs un alimentatore e una ventola con due assi. Durante una condizione critica, il sistema consente di un errore di alimentazione uno continuando il normale funzionamento. Due PCMs (e pertanto power forniture) deve sempre essere installato. Una singola PCM non fornisce alimentazione. La mancata PCM anche una sola possa pertanto i tempi di inattività o possibile perdita di dati.

![Icona di avviso](./media/storsimple-safety/IC740879.png) ![sull'icona di impianti elettrici elettriche](./media/storsimple-safety/IC740882.png) **avviso!**

Per ridurre la probabilità di pregiudizio, elettriche impianti elettrici o morte:

- Non rimuovere le quinte PCM. Esiste un rischio di impianti elettrici elettriche all'interno. Per restituire il PCM e ottenere un CD sostitutivo, [contattare il supporto tecnico Microsoft](https://msdn.microsoft.com/library/azure/dn757750.aspx).

![Un'icona a forma](./media/storsimple-safety/IC740881.png) **Segnalatore:**

Per il corretto funzionamento delle apparecchiature e per impedire danni prodotti:

- È necessario sostituire PCM non riuscito entro 24 ore. Dopo la rimozione di un PCM per la sostituzione, la sostituzione deve essere completata entro 10 minuti dopo la rimozione.

- Rimuovere un PCM, a meno che non sostituzione può essere installata immediatamente. Il simbolo deve non possano funzionare senza tutti i moduli di posizione.

## <a name="electrostatic-discharge-esd-precautions"></a>Precauzioni elettrostatica (ESD)

![Un'icona a forma](./media/storsimple-safety/IC740881.png) **Segnalatore:**

Osservare le seguenti precauzioni ESD.

- Assicurarsi che è stato installato e selezionato un nastro polso o caviglia antistatiche appropriata.

- Osservare tutti precauzioni ESD convenzionale durante la gestione di moduli e i componenti.

- Evitare di contatto con i componenti di base e connettori modulo.

- Danni ESD non è coperto dalla garanzia.

## <a name="battery-disposal-precautions"></a>Precauzioni disposizione batteria

L'alimentazione utilizza una batteria speciale per proteggere il contenuto della memoria durante guasti temporaneo a breve termine. La pila viene inserita nel PCM. Considerare le seguenti informazioni in relazione alla pila.

![Icona di avviso](./media/storsimple-safety/IC740879.png) **avviso!**

Per ridurre il rischio di brevi, fire, esplosione, ferite o morte:

- Eliminare pile usati conformemente alle normative nazionali/internazionali.

- Non disassemblare, macinare, o termica sopra 60 gradi Celsius (140 gradi Fahrenheit) o incenerire. Sostituire la pila PCM con solo una batteria specificata. Utilizzo di un'altra carica può presentare il rischio di fire o esplosione.

- Usare le pile delimitatori protezione se questi vengono rimossi dall'alimentazione.

![Un'icona a forma](./media/storsimple-safety/IC740881.png) **Segnalatore:**

In caso contrario trasporto le pile aereo di spedizione o seguire il documento IATA agli batteria indicazioni disponibile in [http://www.iata.org/whatwedo/cargo/dgr/Pages/lithium-batteries.aspx](http://www.iata.org/whatwedo/cargo/dgr/Pages/lithium-batteries.aspx)

Dopo aver esaminato le comunicazioni di sicurezza, i passaggi successivi sono decomprimere, rack e il dispositivo di cablaggio.

## <a name="next-steps"></a>Passaggi successivi

- Per un dispositivo 8100, passare a [installare il dispositivo StorSimple 8100](storsimple-8100-hardware-installation.md).

- Per un dispositivo 8600, passare a [installare il dispositivo 8600 StorSimple](storsimple-8600-hardware-installation.md).

