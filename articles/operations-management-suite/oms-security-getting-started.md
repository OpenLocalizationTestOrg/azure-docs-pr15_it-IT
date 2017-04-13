<properties
   pageTitle="Introduzione alle operazioni di gestione Suite sicurezza e controllo soluzione | Microsoft Azure"
   description="In questo documento consente di imparare a usare le operazioni di gestione Suite sicurezza e funzionalità per soluzioni di controllo per monitorare il cloud ibrida."
   services="operations-management-suite"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="operations-management-suite"
   ms.topic="get-started-article" 
   ms.devlang="na"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/20/2016"
   ms.author="yurid"/>
 
# <a name="getting-started-with-operations-management-suite-security-and-audit-solution"></a>Guida introduttiva di protezione della famiglia di prodotti di gestione di operazioni e soluzione di controllo
In questo documento contribuisce di iniziare rapidamente operazioni gestione famiglia di prodotti (OMS) sicurezza e controllo funzionalità per soluzioni mostrando ogni opzione.

## <a name="what-is-oms"></a>Che cos'è OMS?
Famiglia di prodotti (OMS, le operazioni di gestione Order) è basato sul cloud IT soluzione Microsoft che consente di gestire e proteggere le in locale e infrastruttura cloud. Per ulteriori informazioni sulle OMS, leggere l'articolo [Operazioni Management Suite](https://technet.microsoft.com/library/mt484091.aspx).

## <a name="oms-security-and-audit-dashboard"></a>Dashboard di sicurezza OMS e controllo

Soluzione OMS sicurezza e controllo fornisce una panoramica completa dell'organizzazione condizioni di protezione IT con le query di ricerca incorporata per problemi rilevanti che richiedono l'attenzione dell'utente. Dashboard di **sicurezza e controllo** è la schermata iniziale per tutti i dati correlati alla sicurezza in OMS. Consente di alto livello approfondita lo stato di protezione del computer. Include inoltre la possibilità di visualizzare tutti gli eventi nelle ultime 24 ore, 7 giorni, o qualsiasi altro intervallo di tempo personalizzato. Per accedere al dashboard di **sicurezza e controllo** , procedere come segue:

1. Nella **Famiglia di prodotti Microsoft operazioni di gestione dei** dashboard principale fare clic su riquadro **Impostazioni** nella parte sinistra.
2. Fare clic su **sicurezza e controllo** opzione in **soluzioni** e **l'Impostazioni** .
3. Dashboard di **sicurezza e controllo** verranno visualizzate:

    ![Dashboard di sicurezza OMS e controllo](./media/oms-security-getting-started/oms-getting-started-fig1-ga.png)

Se si accede a questo dashboard per la prima volta e non si dispone di dispositivi monitorati da OMS, i riquadri non vengano inseriti dati ottenuti dall'agente. Dopo avere installato l'agente, può richiedere alcuni istanti per popolare, pertanto situazione inizialmente potrebbe mancare alcuni dati come sono ancora in corso il caricamento nel cloud.  In questo caso è normale per vedere alcune sezioni senza informazioni materiali. Leggere [i computer Windows connettersi direttamente a OMS](https://technet.microsoft.com/library/mt484108.aspx) per ulteriori informazioni su come installare agente OMS in un sistema di Windows e [computer di connettersi Linux a OMS](https://technet.microsoft.com/library/mt622052.aspx) per ulteriori informazioni su come eseguire questa operazione in un sistema Linux.

> [AZURE.NOTE] L'agente raccoglie le informazioni in base agli eventi correnti siano abilitati, ad esempio nome del computer, nome utente e all'indirizzo IP. Tuttavia non documenti e file, il nome del database o dati riservati sono stati raccolti.   

Le soluzioni sono un insieme di regole di acquisizione di logica, visualizzazione e i dati che consente di risolvere i problemi legati alla clienti chiave. Sicurezza e controllo è una soluzione, gli altri utenti possono essere aggiunti separatamente. Leggere l'articolo [aggiungere soluzioni](https://technet.microsoft.com/library/mt674635.aspx) per ulteriori informazioni su come aggiungere una nuova soluzione.

Dashboard di sicurezza OMS e controllo sono organizzate in quattro categorie principali:

- **I domini di protezione**: in quest ' area è sarà possibile ulteriormente esplorare i record di protezione nel tempo, accesso valutazione di software dannoso, aggiornare valutazione, la sicurezza della rete, identità e accedere a informazioni, computer con gli eventi di protezione e rapidamente hanno accesso al dashboard di Azure Security Center.
- **Problemi rilevanti**: questa opzione consente di identificare rapidamente il numero di problemi attivi e la gravità di questi problemi.
- **Rilevamenti (Preview)**: consente di identificare schemi di attacco la visualizzazione degli avvisi di sicurezza come che si verificano contro le risorse.
- **Business Intelligence di rischio**: consente di identificare schemi di attacco per visualizzare il numero totale di server con il traffico IP dannoso in uscita, il tipo di rischio dannoso e una mappa che mostri provenienza questi indirizzi IP. 
- **Query comuni di sicurezza**: questa opzione comprende un elenco delle query di sicurezza più comuni che è possibile utilizzare per monitorare l'ambiente. Quando si fa clic in una query, viene aperto e il **ricerca** con i risultati per la query.

> [AZURE.NOTE] Per ulteriori informazioni su come OMS i dati rimangono sicura, leggere che come OMS protegge i dati.

## <a name="security-domains"></a>Domini di protezione

Quando si controllano le risorse, è importante essere in grado di accedere rapidamente lo stato corrente dell'ambiente. Tuttavia è importante tenere traccia degli eventi nascosti che si sono verificati in passato che può comportare una migliore comprensione delle azioni eseguite nel proprio ambiente certo punto nel tempo. 

> [AZURE.NOTE] conservazione dei dati viene eseguito in base a OMS prezzi piano. Per ulteriori informazioni, visitare la [Famiglia di prodotti Microsoft operazioni di gestione dei](https://www.microsoft.com/server-cloud/operations-management-suite/pricing.aspx) prezzi pagina.

Operazioni non consentite risposta legali indagini scenari di utilizzo e verrà direttamente vantaggi offerti dai risultati disponibili nella sezione **Sicurezza record nel tempo** .

![Record di protezione nel tempo](./media/oms-security-getting-started/oms-getting-started-fig2.JPG)

Quando si fa clic sul riquadro, verrà aperto e il **ricerca** , che mostra il risultato di una query per **Gli eventi di protezione** (tipo = SecurityEvents) con i dati in base a ultimi sette giorni, come illustrato di seguito:

![Record di protezione nel tempo](./media/oms-security-getting-started/oms-getting-started-fig3.JPG)

Il risultato della ricerca è suddiviso in due riquadri: riquadro a sinistra fornisce una descrizione dettagliata del numero di eventi di sicurezza che sono stati rilevati i computer in cui sono stati trovati questi eventi, il numero di account che sono stati rilevati in questi computer e i tipi di attività. Riquadro di destra fornisce risultati totali e cronologico visualizzare gli eventi di protezione con attività di eventi e nome del computer. È anche possibile fare clic su **Mostra altro** per visualizzare ulteriori dettagli sull'evento, ad esempio i dati dell'evento, l'ID di eventi e origine dell'evento.

> [AZURE.NOTE] Per ulteriori informazioni sulle query di ricerca OMS, leggere [OMS ricerca riferimento](https://technet.microsoft.com/library/mt450427.aspx).

### <a name="antimalware-assessment"></a>Valutazione antimalware

Questa opzione consente di identificare rapidamente i computer con protezione insufficiente e computer compromesso da una parte di software dannoso. Lo stato di valutazione di software dannoso e minacce rilevate nei server che monitorate vengono letti e quindi i dati vengono inviati al servizio Outlook Mobile nel cloud per elaborazione. Rischi e server con protezione insufficiente vengono visualizzati nel dashboard di valutazione di software dannoso, è accessibile dopo aver fatto clic nel riquadro **Dei rischi Antimalware** rilevati server con. 

![valutazione malware](./media/oms-security-getting-started/oms-getting-started-fig4-ga.png)

Come per qualsiasi altro animato disponibili nel Dashboard OMS, quando si fa clic su di esso, e il **ricerca** verrà aperto con il risultato della query. Per questa opzione se si fa clic nella casella **Non Reporting** in **Stato di protezione**, si avrà il risultato della query che mostra questo immissione singola che contiene il nome del computer e il rango, come illustrato di seguito:

![risultato della ricerca](./media/oms-security-getting-started/oms-getting-started-fig5.png)

> [AZURE.NOTE] *rango* è un voto dato in modo da rispecchiare lo stato della protezione (on, off, aggiornato, e così via) e minacce che si trovano. Visto che un numero consente di verificare le aggregazioni.

Se si fa clic nella casella nome del computer, sarà necessario cronologica visualizzazione dello stato di protezione per il computer. Questo è molto utile per gli scenari in cui è necessario capire se è stato una volta installato il antimalware e un certo punto in cui che è stata rimossa.   

### <a name="update-assessment"></a>Valutazione di aggiornamento 

Questa opzione consente di determinare rapidamente l'esposizione a potenziali problemi di sicurezza e se o come critiche questi aggiornamenti per il proprio ambiente. Soluzione di controllo e protezione OMS forniscono solo la visualizzazione degli aggiornamenti, i dati reali provengono da [Soluzioni di aggiornamenti di sistema](https://technet.microsoft.com/library/mt484096.aspx), ossia un modulo diverso all'interno di OMS. Ecco un esempio degli aggiornamenti:

![aggiornamenti del sistema](./media/oms-security-getting-started/oms-getting-started-fig6.png)

> [AZURE.NOTE] Per ulteriori informazioni sulla soluzione gli aggiornamenti, leggere [aggiornare server con la soluzione gli aggiornamenti del sistema](https://technet.microsoft.com/library/mt484096.aspx).

### <a name="identity-and-access"></a>Identità e accesso

Identità deve essere il piano di controllo per la propria azienda, protezione dell'identità dovrebbe essere la massima priorità. Mentre in passato si sono verificati perimetro attorno alle organizzazioni e tali perimetro utilizzato uno dei limiti di difesa principali, oggi con altri dati e altre App passare al cloud l'identità diventa perimetrale nuovo. 

> [AZURE.NOTE] attualmente i dati sono basati solo sui dati login gli eventi di protezione (evento ID 4624) gli accessi futuri di Office 365 e dati di Azure Active Directory anche verranno inclusi.

Per controllare le attività di identità sarà possibile prima di eseguire azioni preventive un caso di posizione o riattivare azioni per interrompere un tentativo di attacco. Dashboard di **identità e accessi** viene fornita una panoramica dello stato di identità, ad esempio il numero di tentativi per l'accesso, l'account dell'utente che sono stati utilizzati durante tali tentativi, l'account che siano stati bloccati, gli account con modifica o Reimposta la password e attualmente il numero di account connessi. 

Quando si fa clic nella sezione **identità e Access** verrà visualizzato nel dashboard seguente:

![identità e accesso](./media/oms-security-getting-started/oms-getting-started-fig7-ga.png)

Le informazioni disponibili in questo dashboard immediatamente possono essere utili all'utente di identificare una potenziale attività sospetta. Ad esempio, sono disponibili 338 tenta di accedere come **amministratore** e 100% di questi tentativi non è riuscita. Può essere causato da attacchi rispetto a questo account. Se si fa clic su questo account che è possibile ottenere altre informazioni che consentono di determinare la risorsa di destinazione per questo tipo di attacco potenziale:

![risultati della ricerca](./media/oms-security-getting-started/oms-getting-started-fig8.JPG)

Il rapporto dettagliato vengono fornite informazioni importanti sull'evento, tra cui: computer di destinazione, il tipo di accesso (in questo caso accesso alla rete), l'attività (in questo caso evento 4625) e la cronologia completa di ogni tentativo. 

### <a name="computers"></a>Computer

Riquadro può essere utilizzato per accedere tutti i computer che hanno attivamente eventi di sicurezza. Quando si fa clic in questa sezione verrà visualizzato l'elenco di computer con gli eventi di protezione e il numero di eventi in ogni computer:

![Computer](./media/oms-security-getting-started/oms-getting-started-fig9.JPG)

È possibile continuare l'analisi facendo clic su ciascun computer ed esaminare gli eventi di sicurezza che sono stati contrassegnati.

### <a name="azure-security-center"></a>Centro protezione di Azure

Questa sezione è una scelta rapida per accedere a Centro protezione di Azure dashboard. Per ulteriori informazioni su questa soluzione, vedere [Introduzione al Centro protezione di Azure](../security-center/security-center-get-started.md) .

## <a name="notable-issues"></a>Problemi rilevanti

Lo scopo principale di questo gruppo di opzioni è fornire una breve descrizione dei problemi che potrebbero nel proprio ambiente da categorizzazione critico, avviso e informativo. Il riquadro di tipo di problema attivo è una visualizzazione di questi problemi, ma non consente di esplorare altri dettagli, per che è necessario utilizzare la parte inferiore del riquadro che contiene il nome del problema (nome), il numero di oggetti ha questo accade (conteggio) e come critiche è (GRAVITÀ).

![Problemi rilevanti](./media/oms-security-getting-started/oms-getting-started-fig10.JPG)

È possibile vedere che questi problemi già analizzati in aree diverse del gruppo di **Sicurezza domini** , consolida lo scopo di questa visualizzazione: visualizzare i problemi più importanti nel proprio ambiente da un'unica posizione.

## <a name="detections-preview"></a>Rilevamenti (Preview)

Lo scopo principale di questa opzione è consentire IT per identificare rapidamente i rischi per il proprio ambiente tramite e la gravità questo rischio.

![Rischio Intel](./media/oms-security-getting-started/oms-getting-started-fig12.png)

Questa opzione può essere utilizzata anche durante indagini intervento per eseguire la valutazione e ottenere ulteriori informazioni sull'attacco.

> [AZURE.NOTE] Per ulteriori informazioni su come utilizzare OMS per risposta evento imprevisto, guardare [come sfruttare il Centro protezione di Azure & Microsoft operazioni Management Suite per una risposta evento imprevisto](https://channel9.msdn.com/Blogs/Taste-of-Premier/ToP1703).

## <a name="threat-intelligence"></a>Business Intelligence di rischio

La nuova sezione di business intelligence di rischio della soluzione di sicurezza e di controllo vengono visualizzati i modelli di attacco in diversi modi: il numero totale di server con il traffico IP dannoso in uscita, il tipo di rischio dannoso e una mappa che mostri provenienza questi indirizzi IP. È possibile interagire con la mappa e fare clic sugli indirizzi IP per altre informazioni.

Gialli puntine da disegno sulla mappa indicano il traffico in ingresso da indirizzi IP dannoso. Non è insolito per i server esposto su internet per visualizzare il traffico dannoso in ingresso, ma è consigliabile esaminare questi tentativi per assicurarsi che nessuna di esse è stata eseguita correttamente. Questi indicatori sono basati su registri di IIS, WireData e registri di Windows Firewall.  

![Rischio Intel](./media/oms-security-getting-started/oms-getting-started-fig11-ga.png)

## <a name="common-security-queries"></a>Query comuni di sicurezza

L'elenco delle query comuni di sicurezza disponibili può essere utile per accedere rapidamente alle informazioni della risorsa e personalizzarlo in base alle esigenze dell'ambiente. Queste query comuni sono:

- Tutte le attività di sicurezza
- Attività di protezione del computer "computer01.contoso.com" (Sostituisci con il proprio nome del computer)
- Attività di protezione del computer "computer01.contoso.com" account "Administrator" (Sostituisci con i proprio nome computer e di account)
- Eseguire l'accesso attività dal Computer
- Account che terminate antimalware Microsoft qualsiasi computer
- Computer in cui è stato terminato il processo di antimalware Microsoft
- Computer in cui "hash.exe" è stata eseguita (Sostituisci con nome processo diverso)
- Tutti i nomi di processo che sono stati eseguiti
- Eseguire l'accesso attività per Account
- Account connesso in modalità remota del computer "computer01.contoso.com" (Sostituisci con il proprio nome del computer)

## <a name="see-also"></a>Vedere anche

In questo documento sono stati introdotti alla soluzione OMS sicurezza e di controllo. Per ulteriori informazioni sulla protezione OMS, vedere gli articoli seguenti:

- [Panoramica di gestione famiglia di prodotti (OMS) operazioni](operations-management-suite-overview.md)
- [Risposta agli avvisi di sicurezza in protezione della famiglia di prodotti di gestione di operazioni e soluzione di controllo e il monitoraggio](oms-security-responding-alerts.md)
- [Monitoraggio delle risorse operazioni Management Suite sicurezza e la soluzione di controllo](oms-security-monitoring-resources.md)
