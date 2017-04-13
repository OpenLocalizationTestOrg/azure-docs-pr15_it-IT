<properties
   pageTitle="Funzionalità di rilevamento in Centro protezione di Azure | Microsoft Azure"
   description="In questo documento consente di informazioni sul funzionamento delle funzionalità di rilevamento di Azure Security Center."
   services="security-center"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security-center"
   ms.topic="hero-article"
   ms.devlang="na"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/22/2016"
   ms.author="yurid"/>

# <a name="azure-security-center-detection-capabilities"></a>Funzionalità di rilevamento di Centro protezione di Azure
In questo documento vengono illustrate le funzionalità avanzate di rilevamento del Centro protezione di Azure, che consente di identificare i rischi active assegnazione delle risorse di Microsoft Azure e vengono fornite informazioni dettagliate sui necessaria per rispondere rapidamente.

> [AZURE.NOTE] Rilevamenti avanzati sono disponibili in Standard livello di Azure Centro sicurezza. È disponibile una versione di valutazione gratuita di 90 giorni. È possibile passare da una selezione livello prezzi nei [Criteri di sicurezza](security-center-policies.md). Visitare la [pagina Centro protezione](https://azure.microsoft.com/pricing/details/security-center/) per altre informazioni sui prezzi. 


## <a name="responding-to-todays-threats"></a>Rispondere a minacce attuali
Sono state apportate modifiche significative in orizzontale rischio negli ultimi 20 anni. In passato, in genere solo era preoccuparsi di cancellazione del sito web da utenti malintenzionati singoli utenti interessati prevalentemente vedere "operazioni che può eseguire". Gli utenti malintenzionati odierna sono molto più sofisticati e organizzati. Hanno spesso specifici obiettivi finanziari e strategici. Hanno anche altre risorse disponibili, come possono essere finanziati dagli Stati paese o criminali.

Questo approccio ha portato a un livello di aspetto professionale in classifica pirata informatico senza precedenti. Non è sono interessati alterazione web. Ora sono interessati furto informazioni conti finanziari e dati riservati, ognuno dei quali può usino per generare cassa sul mercato aperto o per sfruttare una particolare business, posizione politica o militare. Ancora più in materia di tali utenti malintenzionati con un obiettivo finanziario sono malintenzionati violare reti per danneggiare infrastruttura e persone.

In risposta, le organizzazioni spesso distribuire varie soluzioni di punto, focalizzare l'attenzione su difendere perimetro aziendale o i punti finali riconoscibili firme attacchi noti. Queste soluzioni comandi sono particolarmente utili per generare un numero elevato di avvisi fedeltà bassa, che richiedono un esperto di sicurezza valutare e provare a utilizzare. La maggior parte delle organizzazioni mancano la competenze necessarie per rispondere a questi avvisi: Vai così tanti giornalismo e l'ora.  Nel frattempo, gli utenti malintenzionati hanno derivato i metodi per rendere inefficace molti difesa basate su firma e [adattarsi agli ambienti cloud](https://azure.microsoft.com/blog/detecting-threats-with-azure-security-center/). Nuovi approcci sono necessari per identificare nuove minacce e accelerare il rilevamento e le risposte più rapidamente. 

## <a name="how-azure-security-center-detects-and-responds-to-threats"></a>Modalità di rilevamento e risponde a rischi di Centro protezione di Azure

Ricercatori sulla protezione di Microsoft vengono costantemente attenzione ai rischi. Devono avere accesso a un ampio insieme di telemetria ottenuta presenza globale Microsoft nel cloud e locale. Questa raccolta estesa e diverse di set di dati consente a Microsoft di individuare nuovo attacco motivi e tendenze tra i prodotti consumer e dell'organizzazione locale, nonché i servizi online. Di conseguenza, Security Center aggiornarne rapidamente gli algoritmi di rilevamento come utenti malintenzionati rilasciare attacchi nuovi e sempre più complessi. Questo approccio consente di tenere il passo con un ambiente di rischio spostamento veloce. 

Individuazione Security Center funziona raccogliendo automaticamente le informazioni sulla protezione da risorse di Azure, la rete e le soluzioni partner connessi. In presenza di queste informazioni, spesso correlare le informazioni provenienti da più origini, per identificare i rischi. Gli avvisi di sicurezza vengono priorità nel Centro protezione insieme a suggerimenti su come risolvere il rischio.

![Presentazione e raccolta di dati di Centro protezione](./media/security-center-detection-capabilities/security-center-detection-capabilities-fig1.png)

Nel Centro sicurezza impiega analitica protezione avanzata, che vanno oltre la approcci basati su firma. Successi nei dati e nelle tecnologie [di apprendimento computer](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/) vengono utilizzati per valutare gli eventi dell'infrastruttura cloud intera – rilevamento minacce Impossibile identificare gli approcci manuale e la previsione evoluzione di attacchi. Questi analitica sicurezza include: 

- **Business intelligence di rischio integrato**: consente di cercare gli operatori errato noti sfruttando intelligence rischio globale da prodotti Microsoft e servizi, Microsoft digitale crimini unità (DCU), Microsoft Security Response Center (MSRC) e feed esterni.
- **Comportamento analitica**: applica modelli noti per individuare comportamenti dannosi. 
- **Rilevamento delle anomalie in**: Usa analisi statistiche per creare una previsione nella cronologia. Avvisa sulle deviazioni dalle linee di base Fondazione conformi a un potenziale attacchi.


### <a name="threat-intelligence"></a>Business intelligence di rischio
Microsoft ha un importo svariato di business intelligence di rischio globale. Flussi di telemetria da più origini, ad esempio Azure, Office 365, Microsoft CRM online, Microsoft Dynamics AX, outlook.com, MSN.com Microsoft digitale crimini unità (DCU) e Microsoft Security Response Center (MSRC). Ricercatori riceveranno anche le informazioni di business intelligence rischio condividono tra i provider di servizi cloud principali e sottoscrivono rischio intelligence feed da terze parti. Azure Security Center è possibile utilizzare queste informazioni per un avviso di minacce dagli operatori errato noti. Seguono alcuni esempi:

- **Le comunicazioni in uscita a un indirizzo IP dannoso**: il traffico in uscita a un noti indesiderati o darknet probabile che indica che la risorsa è stata compromessa e un pirata informatico che si tenta di eseguire comandi su tali dati sistema o exfiltrate. Centro protezione di Azure confronta il traffico di rete al database di Microsoft rischio globale e notifica l'esistenza di se vengono rilevati comunicazioni a un indirizzo IP dannoso.

## <a name="behavioral-analytics"></a>Comportamento analitica

Comportamento analitica è una tecnica che consente di analizzare e confronta i dati a un insieme di schemi noti. Tuttavia, questi modelli non sono semplici firme. Vengono determinati tramite complessa apprendimento algoritmi applicati a un set di dati rilevanti. Vengono inoltre determinati tramite analisi accurata della comportamenti dannosi da analisti esperti. Centro protezione di Azure consente analitica visive per identificare le risorse compromesse in base alle analisi dei registri macchina virtuale, i registri dispositivo virtuali, tessuti registri, anomalo e da altre origini. 

Inoltre, non esiste correlazione con altri segnali per verificare la presenza di altri elementi di una campagna diffusa. Questa correlazione consente di identificare gli eventi che sono coerenti con gli indicatori di fondazione di violazione. Seguono alcuni esempi:

- **Esecuzione dei processi suspicious**: gli utenti malintenzionati utilizzano diverse tecniche per l'esecuzione di software dannoso senza il rilevamento. Ad esempio, un pirata informatico potrebbe assegnare gli stessi nomi dei file di sistema legittimi malware ma posizionare questi file in un percorso alternativo, usare un nome che è molto simile a un file consentito o maschera l'estensione del file true. Nel Centro sicurezza modelli processi comportamenti e monitor elaborare le esecuzioni per rilevare valori erratici, ad esempio questi.  
- **Malware nascosta e sfruttamento tenterà**: malware più sofisticati è in grado di eludere prodotti antimalware tradizionale mai la scrittura su disco o la crittografia componenti software memorizzati su disco.  Tuttavia, ad esempio malware può essere rilevato usando l'analisi di memoria, come il malware deve lasciare tracce in memoria per funzionare. Quando si blocca software, un arresto anomalo acquisisce una porzione di memoria al momento dell'arresto anomalo.  L'analisi della memoria nell'arresto anomalo, Azure Security Center consente di rilevare le tecniche utilizzate per sfruttare le vulnerabilità nel software, accedere ai dati riservati e sulle mantenere con-in un computer manomesso senza influire sulle prestazioni del computer.
- **Laterali movimento ed esplorazione interno**: per mantenere un compromesso di rete e individuare/raccolto efficace dei dati, gli utenti malintenzionati spesso tentano di spostarsi lateralmente dal computer compromesso ad altri utenti all'interno della stessa rete. Centro protezione esegue il monitoraggio dell'attività di processo e accesso per rilevare tentativi di espandere di mercato dannoso all'interno della rete, ad esempio comando remoto esecuzione rete sondaggio ed enumerazione account.
- **Script di PowerShell dannosi**: PowerShell è utilizzato da utenti malintenzionati per eseguire il codice dannoso in macchine virtuali di destinazione per diversi scopi. Centro protezione esamina attività PowerShell per prove di attività sospetto. 
- **In uscita attacchi**: gli utenti malintenzionati spesso risorse cloud con lo scopo dell'utilizzo di tali risorse per organizzare ulteriori attacchi di destinazione. Macchine virtuali compromesse, ad esempio, potrebbe essere utilizzate per forzare attacchi altre macchine virtuali di avvio, inviare la posta indesiderata o analizzare le porte aperte e altri dispositivi su internet. Applicando l'apprendimento per il traffico di rete, è possibile rilevare Security Center quando comunicazioni di rete in uscita superano la norma. In caso di posta indesiderata, Centro sicurezza correlato anche il traffico di posta elettronica insolito con intelligence da Office 365 per determinare se il messaggio è probabile che nefandi o il risultato di una campagna di posta elettronica legittimi.  

### <a name="anomaly-detection"></a>Rilevamento delle anomalie in

Azure Security Center Usa anche il rilevamento di anomalie per identificare i rischi. Invece di comportamento analitica (che dipende schemi noti derivati da grandi quantità di dati), rilevamento di anomalie in più "personalizzato" e analizza le previsioni specifiche per le distribuzioni. Apprendimento viene applicato per determinare normale attività per le distribuzioni e quindi le regole vengono generate per definire condizioni esterni che potrebbero indicare un evento di protezione. Ecco un esempio:

- **In ingresso RDP/SSH brute forzare attacchi**: le distribuzioni potrebbero essere occupate macchine virtuali con moltissimi accessi ogni giorno e altre le macchine virtuali con pochi o account di accesso. Centro protezione di Azure può determinare dell'attività di accesso previsto per queste macchine virtuali e utilizzare apprendimento definire all'esterno dell'attività di accesso normale. Se il numero di account di accesso o l'ora del giorno degli account di accesso o la posizione da cui vengono richiesti gli account di accesso o altre caratteristiche di accesso è molto diversa da quella, potrebbe generato un avviso. Di nuovo l'apprendimento determina gli elementi significativi.

## <a name="continuous-threat-intelligence-monitoring"></a>Rischio continuo monitoraggio di business intelligence

Centro protezione di Azure funziona sicurezza ricerche e ai dati scienza team che controllano costantemente le modifiche dei rischi di protezione. Sono incluse le seguenti iniziative:

- **Monitoraggio di business intelligence rischio**: rischio intelligence include meccanismi, indicatori, implicazioni e suggerimenti con funzioni specifiche sull'esistenti o nuove minacce. Queste informazioni sono condiviso nella community di sicurezza e Microsoft monitoraggio continuo dei rischi intelligence feed da origini interne ed esterne.
- **Condivisione di segnale**: approfondimenti dal team sicurezza portfolio generali Microsoft del cloud e locale dispositivi endpoint servizi, server e client vengono condivisi e analizzati. 
- **Specialisti della protezione di Microsoft**: coinvolgimento in corso con team Microsoft che funzionano in campi di protezione specializzate, ad esempio amministrativi e web rilevamento degli attacchi.
- **Rilevamento ottimizzazione**: algoritmi vengono eseguiti su set di dati reali dei clienti e ricercatori lavorare con i clienti per convalidare i risultati. True e false positivi vengono utilizzati per perfezionare algoritmi risorse computer.

Nessuna azione utile eseguire queste attività combinata concludere rilevamenti nuovi e migliorati, è possibile trarre vantaggio da immediatamente.

## <a name="see-also"></a>Vedere anche
In questo documento è stato al rilevamento di Centro protezione di Azure funzionalità funzionano. Per ulteriori informazioni sul Centro protezione, vedere gli articoli seguenti:

- [Centro protezione di Azure Guida alla pianificazione e operazioni](security-center-planning-and-operations-guide.md)
- [Gestire e rispondere agli avvisi di protezione in Centro protezione di Azure](security-center-managing-and-responding-alerts.md)
- [Avvisi di sicurezza in base al tipo di Centro protezione di Azure](security-center-alerts-type.md)
- [Monitoraggio di protezione in Centro protezione di Azure](security-center-monitoring.md) , informazioni su come controllare lo stato delle risorse di Azure.
- [Monitoraggio delle soluzioni dei partner con Azure Security Center](security-center-partner-solutions.md) -informazioni su come controllare lo stato di integrità delle soluzioni partner.
- [Domande frequenti su Centro protezione di Azure](security-center-faq.md) : domande frequenti sull'utilizzo del servizio di ricerca.
- [Blog di sicurezza di Windows Azure](http://blogs.msdn.com/b/azuresecurity/) , trovare post di blog sulla protezione di Azure e conformità.
