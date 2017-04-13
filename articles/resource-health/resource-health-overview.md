<properties
   pageTitle="Panoramica di integrità risorsa Azure | Microsoft Azure"
   description="Panoramica dell'integrità della risorsa Azure"
   services="Resource health"
   documentationCenter="dev-center-name"
   authors="BernardoAMunoz"
   manager=""
   editor=""/>

<tags
   ms.service="resource-health"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="Supportability"
   ms.date="06/01/2016"
   ms.author="BernardoAMunoz"/>

# <a name="azure-resource-health-overview"></a>Panoramica di integrità Azure delle risorse

Azure dello stato delle risorse è un servizio che espone lo stato delle singole risorse Azure e vengono fornite indicazioni utilizzabili per risolvere i problemi. In un ambiente cloud in cui non è possibile accedere direttamente a server o gli elementi dell'infrastruttura lo scopo dell'integrità di risorse è ridurre il tempo clienti sulla risoluzione dei problemi, in particolare ridurre il tempo impiegato per determinare se definisce la radice del problema all'interno dell'applicazione o se è dovuto a un evento all'interno della piattaforma Azure.

## <a name="what-is-considered-a-resource-and-how-does-resource-health-decides-if-the-resource-is-healthy-or-not"></a>Che cos'è considerato una risorsa e funzionamento dell'integrità della risorsa decide se la risorsa è integro? 
Una risorsa è un'istanza di creata dall'utente di un tipo di risorsa fornito da un servizio, ad esempio: una macchina virtuale, un'app Web o un database SQL. 

Integrità risorsa si basa su segnali emessi dalla risorsa e/o il servizio per determinare se una risorsa è integro o meno. È importante notare che attualmente integrità solo gli account delle risorse per lo stato di una risorsa specifica per digitare e non considera altri elementi che possono contribuire alle condizioni generali. Ad esempio, per il report che viene considerato lo stato di una macchina virtuale, solo la parte del calcolo dell'infrastruttura, ad esempio problemi di rete non verranno visualizzati dell'integrità della risorsa, a meno che non è presente un'interruzione del servizio dichiarato, nel qual caso, essa viene rilevato tramite l'intestazione nella parte superiore e il. Ulteriori informazioni sull'interruzione del servizio sono disponibile più avanti in questo articolo. 

## <a name="how-is-resource-health-different-from-service-health-dashboard"></a>Come è diverso dal Dashboard di integrità dei servizi dell'integrità della risorsa?

Le informazioni fornite dello stato delle risorse sono maggiore rispetto a quello fornito dal Dashboard di integrità dei servizi. Mentre SHD comunica eventi che influiscono sulla disponibilità di un servizio in un'area, dell'integrità della risorsa espone informazioni relative a una risorsa specifica, ad esempio, verranno esposto eventi che influiscono sulla disponibilità di una macchina virtuale, un'app web o un database SQL. Ad esempio, se un nodo si riavvia in modo imprevisto, clienti cui macchine virtuali in esecuzione su tale nodo saranno possibile ottenere il motivo per cui le macchine Virtuali non è disponibile per un periodo di tempo.   

## <a name="how-to-access-resource-health"></a>Modalità di accesso dell'integrità della risorsa
Per i servizi disponibili tramite dell'integrità della risorsa, sono disponibili 2 modi per accedere dell'integrità della risorsa.

### <a name="azure-portal"></a>Portale di Azure
E dell'integrità della risorsa nel portale di Azure, vengono fornite informazioni dettagliate sullo stato della risorsa, nonché consigliati azioni che variano a seconda di integrità corrente della risorsa. Questo blade offre un'esperienza utente ottimale durante la ricerca dell'integrità della risorsa, come facilita accesso ad altre risorse all'interno del portale. Come detto in precedenza, l'insieme di azioni consigliate in e dell'integrità della risorsa variano in base allo stato corrente:

* Risorse integro: dal momento che non è stato rilevato alcun problema che potrebbe interferire con lo stato della risorsa, le azioni riguardano di aiutare il processo di risoluzione dei problemi. Ad esempio, fornisce accesso diretto ai e risoluzione dei problemi, che offre indicazioni su come risolvere faccia clienti problemi più comune.
* Risorse non corretti: presenza di problemi causati dalla Azure, e il visualizzerà azioni Microsoft sta impiegando (o ha avuto) per recuperare la risorsa. Per problemi causati dalla utente azioni avviate, verranno blade può richiedere un elenco di clienti di azioni in modo risolvere il problema e ripristino la risorsa.  

Dopo aver eseguito l'accesso al portale di Azure, esistono due modi per accedere e dell'integrità della risorsa: 

###<a name="open-the-resource-blade"></a>Aprire e delle risorse
Aprire e delle risorse per una determinata risorsa. In e l'impostazioni che viene visualizzata accanto a e delle risorse, fare clic su integrità delle risorse per aprire e dell'integrità della risorsa. 

![Blade dell'integrità della risorsa](./media/resource-health-overview/resourceBladeAndResourceHealth.png)

### <a name="help-and-support-blade"></a>Guida e supporto blade
Aprire e il supporto tecnico facendo clic sul punto interrogativo nell'angolo superiore destro, quindi selezionando Guida + supporto. 

**Sulla barra di spostamento superiore**

![Guida + supporto](./media/resource-health-overview/HelpAndSupport.png)

Fare clic sul riquadro, verrà visualizzata e l'abbonamento integrità delle risorse che verrà elencati tutte le risorse nell'abbonamento. Accanto a ogni risorsa è presente un'icona che indica la propria integrità. Fare clic su ogni risorsa verrà aperto e dell'integrità della risorsa.

**Riquadro dell'integrità della risorsa**

![Riquadro dell'integrità della risorsa](./media/resource-health-overview/resourceHealthTile.png)

## <a name="what-does-my-resource-health-status-mean"></a>Che cosa significa lo stato dell'integrità della risorsa
Sono disponibili 4 stati integrità diversa che potrebbe essere visualizzato per la risorsa.

### <a name="available"></a>Disponibile
Il servizio non ha rilevato i problemi di piattaforma che potrebbe incidere sulla disponibilità della risorsa. Questo è contrassegnato da un'icona di segno di spunta verde. 

![Risorsa è disponibile](./media/resource-health-overview/Available.png)

### <a name="unavailable"></a>Non è disponibile

In questo caso il servizio ha rilevato un problema in corso la piattaforma che impatto la disponibilità della risorsa, ad esempio, il nodo in cui è stata eseguita la macchina virtuale in modo imprevisto riavviato. Questo è indicato da un'icona di avviso rosso. Ulteriori informazioni sul problema sono illustrate nella sezione centrale della stessa e, tra cui: 

1.  Quali azioni su Microsoft sta impiegando per recuperare la risorsa 
2.  Una sequenza temporale dettagliata del problema, incluso il tempo di risoluzione prevista
3.  Un elenco di azioni per gli utenti consigliato 

![Risorsa non è disponibile](./media/resource-health-overview/Unavailable.png)

### <a name="unavailable--customer-initiated"></a>Non disponibile-cliente avviata
La risorsa è disponibile a causa di una richiesta di assistenza clienti, ad esempio l'arresto di una risorsa o che richiede il riavvio. Questo è contrassegnato da un'icona informativa blu. 

![Risorsa non è disponibile a causa di un'azione avviato da un utente](./media/resource-health-overview/userInitiated.png)

### <a name="unknown"></a>Sconosciuto
Il servizio non ha ricevuto informazioni su questa risorsa per più di 5 minuti. Questo è contrassegnato da un'icona grigio punto interrogativo. 

È importante tenere presente che non si tratta di un'indicazione definitiva che esiste un problema con una risorsa, in modo che i clienti devono seguire questi suggerimenti:

* Se la risorsa è in esecuzione come previsto, ma il relativo stato è impostato su sconosciuto salute delle risorse, è probabile che lo stato della risorsa eseguire l'aggiornamento a integro dopo alcuni minuti non siano presenti problemi.
* Se sono presenti problemi di accesso alla risorsa e il relativo stato sono impostati su sconosciuto salute delle risorse, questo errore può essere indicazione tempestiva possono essere un problema e indagini aggiuntive dovrebbero avvenire fino a quando non verrà aggiornato sull'integrità integro o non corretti

![Integrità risorsa è sconosciuto](./media/resource-health-overview/unknown.png)

## <a name="service-impacting-events"></a>Eventi di impatto del servizio
Se la risorsa può subisca un evento di impatto del servizio in corso, un'intestazione verrà visualizzata nella parte superiore e dell'integrità della risorsa. Fare clic su intestazione verrà aperto e il controllo degli eventi, che verrà visualizzate informazioni aggiuntive sull'interruzione.

![Integrità risorsa possono interferire con un articolo spiega](./media/resource-health-overview/serviceImpactingEvent.png)

## <a name="what-else-do-i-need-to-know-about-resource-health"></a>Altre informazioni utili dell'integrità della risorsa?

### <a name="signal-latency"></a>Latenza segnale
Segnala che feed dell'integrità della risorsa, potrebbe essere fino a 15 minuti di ritardo, che può causare le differenze tra lo stato di integrità corrente della risorsa e il relativo effettiva disponibilità. È importante tenere presente quanto segue come sarà più semplice eliminare tempi istruttoria possibili problemi. 

### <a name="special-case-for-sql"></a>Speciale per SQL 
Integrità risorsa indica lo stato del database SQL, non di SQL server. Passando questo percorso offre un'immagine di integrità più realistica, è necessario che più componenti e servizi da prendere in considerazione per determinare lo stato del database. Il segnale corrente si basa sull'account di accesso a un database, il che significa che per i database accessi regolari (che include ad esempio la ricezione delle richieste di esecuzione query) di ricevere lo stato stato verrà regolarmente visualizzato. Se il database non è disponibile per un periodo di 10 minuti o più, verrà spostato allo stato sconosciuto. Ciò significa che il database sia disponibile, solo che non è stato generato alcun segnale perché nessun account di accesso sono state eseguite. Connessione al database e l'esecuzione di una query genererà i segnali necessari per individuare e aggiornare lo stato di integrità del database.

## <a name="feedback"></a>Commenti e suggerimenti
È sempre sono aperte a commenti e suggerimenti! Inviare i [suggerimenti](https://feedback.azure.com/forums/266794-support-feedback). Inoltre, è possibile utilizzare con noi tramite i [forum MSDN](https://social.msdn.microsoft.com/Forums/azure)o [Twitter](https://twitter.com/azuresupport) .
