<properties
   pageTitle="Il monitoraggio dell'integrità nel servizio tessuti | Microsoft Azure"
   description="Introduzione al monitoraggio modello, che fornisce il monitoraggio cluster e le applicazioni e servizi dello stato di Azure servizio tessuti."
   services="service-fabric"
   documentationCenter=".net"
   authors="oanapl"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/28/2016"
   ms.author="oanapl"/>

# <a name="introduction-to-service-fabric-health-monitoring"></a>Introduzione al servizio tessuti monitoraggio dell'integrità
Azure servizio tessuti introduce un modello di stato che consente di valutare l'integrità extensible RTF e flessibile e creazione di report. Grazie al modello vicino tempo reale monitoraggio dello stato del cluster e i servizi in esecuzione al suo interno. È possibile ottenere informazioni sullo stato e correggere facilmente potenziali problemi prima che a catena e causare interruzioni enorme. Nel modello tipico servizi inviare rapporti basati su osservazioni locale e che le informazioni raggruppate per fornire un' generale cluster a livello di visualizzazione.

Componenti di servizio tessuti utilizzano questo modello di integrità RTF per segnalare lo stato corrente. È possibile utilizzare lo stesso meccanismo di integrità dei report dalle applicazioni di posta. Se investire in report di integrità di alta qualità che acquisisce le condizioni personalizzate, è possibile rilevare e risolvere i problemi per l'applicazione in esecuzione molto più facilmente.

> [AZURE.NOTE] Si inizia a sottosistema integrità per soddisfare esigenze per gli aggiornamenti monitorate. Servizio tessuti vengono monitorati Aggiornamenti applicazione e cluster che garantiscono una maggiore disponibilità completa, senza tempi di inattività e il minimo senza intervento dell'utente. Per raggiungere gli obiettivi, l'aggiornamento verifica integrità in base a configurati i criteri di aggiornamento e consente l'aggiornamento a procedere solo quando l'integrità rispetta soglie desiderate. In caso contrario, l'aggiornamento viene automaticamente all'annullamento o sospese per consentire agli amministratori di risolvere i problemi. Per altre informazioni sull'aggiornamento delle applicazioni, vedere [l'articolo](service-fabric-application-upgrade.md).

## <a name="health-store"></a>Archivio di integrità
Archivio integrità mantiene informazioni relative alla salute entità del cluster di recupero semplice e di valutazione. Viene implementata come un'infrastruttura di servizio mantenute informazioni sullo stato del servizio per garantire disponibilità e scalabilità elevate. Archivio integrità fa parte del **tessuti: / sistema** applicazione ed è disponibile quando il cluster è attivo e in esecuzione.

## <a name="health-entities-and-hierarchy"></a>Gerarchia ed entità integrità
Entità integrità sono organizzate in una gerarchia logica che acquisisce interazioni e le dipendenze tra entità diverse. L'entità e la gerarchia vengono create automaticamente dall'archivio di stato in base a report ricevuto dai componenti di servizio tessuti.

Entità integrità fedelmente entità servizio tessuti. (Ad esempio **entità applicazione dell'integrità** corrisponde a un'istanza di applicazione distribuita in cluster, mentre **entità nodo integrità** corrisponde a un nodo tessuti servizio) Gerarchia dell'integrità acquisite le interazioni entità del sistema di ed è la base per la valutazione dell'integrità avanzate. Per informazioni sui concetti servizio tessuti [Panoramica tecnica servizio tessuti](service-fabric-technical-overview.md). Per ulteriori informazioni sull'applicazione, vedere [il modello di applicazione di servizio tessuti](service-fabric-application-model.md).

L'entità dell'integrità e la gerarchia consentire il cluster e alle applicazioni di essere in modo efficace segnalati, eseguire il debug e controllato. Il modello di integrità fornisce una rappresentazione accurata, *granulare* dell'integrità di più mappe di spostamento del cluster.

![Enti di integrità.][1]
Entità integrità organizzati in una gerarchia in base alle relazioni padre-figlio.

[1]: ./media/service-fabric-health-introduction/servicefabric-health-hierarchy.png

L'entità dell'integrità sono:

- **Cluster**. Rappresenta lo stato di un cluster di servizio tessuti. Report di stato cluster descrivere condizioni che interessano l'intero cluster e non è possibile restringere l'ambito di uno o più elementi figlio non corretti. Esempi di cervello del cluster divisione a causa di problemi di rete in partizioni o comunicazioni.

- **Nodo**. Rappresenta lo stato di un nodo servizio tessuti. Report di stato nodo descrivere condizioni che influenzano sull'esecuzione di nodo. Vengono in genere influisce su tutte le entità distribuite in esecuzione su di esso. Ad esempio quando un nodo non è compresa nel spazio su disco (o un'altra proprietà a livello di computer, ad esempio memoria, le connessioni) e quando un nodo verso il basso. L'entità nodo viene identificato dal nome del nodo (stringa).

- **Applicazione**. Rappresenta lo stato di un'istanza dell'applicazione in esecuzione nel cluster. Report di stato applicazione descrivere condizioni che influiscono sulle condizioni generali dell'applicazione. Non è possibile restringere verso il basso per singoli elementi figlio (servizi o applicazioni). Esempi interazione-to-end tra diversi servizi dell'applicazione. L'entità dell'applicazione viene identificato tramite il nome dell'applicazione (URI).

- **Servizio**. Rappresenta lo stato di un servizio in esecuzione nel cluster. Report di stato del servizio descrivere le condizioni che influiscono sull'integrità del servizio e non è possibile restringere l'ambito di una partizione o un'altra replica. Esempi di una configurazione del servizio (ad esempio, porta o condivisione di file esterna) che causa problemi di tutte le partizioni. L'entità servizio viene identificato dal nome del servizio (URI).

- **Partizione**. Indica l'integrità di una partizione di servizio. Report di stato partizione descrivere condizioni che influiscono sull'intera replica insieme. Ad esempio quando il numero di repliche è inferiore al conteggio di destinazione e quando una partizione la perdita di base. L'entità partizione è identificato da partizione ID (GUID).

- **Replica**. Indica l'integrità di una replica di informazioni sullo stato del servizio o un'istanza senza informazioni sullo stato del servizio. L'unità minima che watchdog e componenti di sistema possono creare un report per un'applicazione. Per i servizi di informazioni sullo stato, esempi di replica primaria reporting quando operazioni non è possibile replicare database secondari e quando replica è impostato su non procedere alla previsto velocità. Inoltre, un'istanza senza informazioni sullo stato può inviare una segnalazione quando quasi esaurito risorse o con problemi di connettività. L'entità di replica è identificato da partizione ID (GUID) e l'ID replica o istanza (long).

- **DeployedApplication**. Rappresenta lo stato di un' *applicazione in esecuzione su un nodo*. Report di stato di applicazione distribuita descrivere condizioni specifiche per l'applicazione sul nodo che non è possibile restringere l'ambito per distribuire sullo stesso nodo i pacchetti di servizio. Esempi di quando non è possibile scaricare il pacchetto dell'applicazione su tale nodo e quando c'è un'impostazione di problema identità di protezione applicazione sul nodo. Applicazione distribuita è identificato da nome applicazione (URI) e nodo nome (stringa).

- **DeployedServicePackage**. Rappresenta lo stato di un pacchetto di servizio in esecuzione su un nodo del cluster. Descrive le condizioni specifiche di un pacchetto di servizio che non interessano gli altri pacchetti di servizio sullo stesso nodo per la stessa applicazione. Esempi di un pacchetto di codice in un pacchetto di configurazione che non può essere letti e il pacchetto di servizio non è possibile avviare. Il pacchetto di servizio distribuito è identificato da nome applicazione (URI), nome (stringa) e il nome del manifesto di servizio (stringa).

Granularità del modello di integrità rende più facile rilevare e correggere eventuali problemi. Ad esempio, se un servizio non risponde, è possibile segnalare che l'istanza dell'applicazione non corretti. Creazione di relazioni di livello non è ideale, tuttavia, poiché il problema potrebbe non essere influisce negativamente sulla tutti i servizi all'interno dell'applicazione. Il report da applicare al servizio non corretti o in una partizione figlio specifico se informazioni fa riferimento a tale partizione. I dati automaticamente superfici tramite la gerarchia e una partizione non integro viene visualizzato livelli di servizio e dell'applicazione. Questa aggregazione consente di individuare e risolvere più rapidamente la causa del problema.

È composta da relazioni padre-figlio della gerarchia di integrità. Un cluster è composto da nodi e le applicazioni. Le applicazioni di siano e applicazioni distribuite. Le applicazioni distribuite hanno distribuito pacchetti di assistenza. Servizi hanno partizioni ogni partizione è una o più repliche. Esiste una relazione speciale tra nodi e distribuita entità. Se un nodo è danneggiato riportati dal relativo componente di sistema di certificazione (servizio di gestione di Failover), influisce applicazioni, pacchetti di servizio e repliche distribuite su di esso.

Gerarchia dell'integrità rappresenta lo stato del sistema in base a più recente report di stato, ovvero quasi in tempo reale informazioni più recente.
Watchdog interni ed esterni possono creare un report nelle stesse entità in base a specifiche di un'applicazione logica o condizioni monitorate personalizzate. Report utente coesistere con i report di sistema.

Pianificare un investimento di come segnalare e rispondere a integrità durante la progettazione di un servizio cloud di grandi dimensioni, per rendere più semplice eseguire il debug, monitorare e gestire il servizio.

## <a name="health-states"></a>Stati di integrità
Servizio tessuti utilizza tre stati di integrità per descrivere se un'entità è integro: OK, avviso e errore. I report inviato all'archivio di integrità specificare uno dei seguenti stati. Il risultato della valutazione integrità corrisponde a uno di questi stati.

Possibili [stati di integrità](https://msdn.microsoft.com/library/azure/system.fabric.health.healthstate) sono:

- **OK**. L'entità è integro. Esistono problemi noti segnalati ai relativi oggetti figlio (se applicabile).

- **Avviso**. L'entità di esperienze alcuni problemi, ma non è ancora non corretti (ad esempio, sono disponibili i ritardi dei voli, ma non provocano eventuali problemi funzionali ancora). In alcuni casi, la condizione di avviso potrebbe risolvere stesso senza intervento speciale ed è utile per fornire la visibilità sullo cosa succede. In altri casi, la condizione di avviso può influire negativamente su un problema grave senza intervento dell'utente.

- **Errore**. L'entità è danneggiato. Azione da eseguire per risolvere lo stato dell'entità, perché non funzionerà correttamente.

- **Sconosciuto**. L'entità non esiste nell'archivio di integrità. Questo risultato può essere ottenuto dalla query distribuite che unire i risultati da più componenti. È possibile ottenere, ad esempio, passare a **FailoverManager** e **HealthManager**, get applicazione elenco query consente di passare alla **ClusterManager** e **HealthManager**query di nodo elenco. Queste query unire i risultati più componenti del sistema. Se un altro componente di sistema restituisce un'entità che non ha raggiunto o cancellata dall'archivio di integrità, il risultato unito è sconosciuto lo stato di integrità.

## <a name="health-policies"></a>Criteri di integrità
L'archivio di integrità Applica criteri di integrità per determinare se un'entità è integro sulla base dei report e ai relativi oggetti figlio.

> [AZURE.NOTE] Nel manifesto cluster (per la valutazione dell'integrità di cluster e nodo) o nel manifesto dell'applicazione (per la valutazione dell'applicazione e i relativi elementi figlio), è possono specificare criteri di integrità. Le richieste di valutazione di integrità anche possono passare in Criteri di valutazione personalizzato integrità vengono usate solo per la valutazione.

Per impostazione predefinita, tessuti servizio applica regole restrittive (tutti gli elementi devono essere integro) per la relazione padre-figlio. Se uno degli elementi figlio presenta un evento non corretti, l'elemento padre viene considerato non corretti.

### <a name="cluster-health-policy"></a>Criterio di integrità cluster
Il [criterio di integrità cluster](https://msdn.microsoft.com/library/azure/system.fabric.health.clusterhealthpolicy.aspx) viene utilizzato per valutare l'integrità cluster e nodo stati di integrità. Il criterio può essere definito nel manifesto cluster. Se non è presenta, viene utilizzato il criterio predefinito (zero errori tollerati).
Il criterio di integrità cluster contiene:

- [ConsiderWarningAsError](https://msdn.microsoft.com/library/azure/system.fabric.health.clusterhealthpolicy.considerwarningaserror.aspx). Specifica se trattare integrità avviso segnalare come errori durante la valutazione dell'integrità. Predefinito: false.

- [MaxPercentUnhealthyApplications](https://msdn.microsoft.com/library/azure/system.fabric.health.clusterhealthpolicy.maxpercentunhealthyapplications.aspx). Specifica la percentuale massima tollerata delle applicazioni che possono essere non corretti prima che il cluster risulta errore.

- [MaxPercentUnhealthyNodes](https://msdn.microsoft.com/library/azure/system.fabric.health.clusterhealthpolicy.maxpercentunhealthynodes.aspx). Specifica la percentuale massima tollerata dei nodi che possono essere non corretti prima che il cluster risulta errore. In cluster di grandi dimensioni, alcuni nodi sono sempre verso il basso o indietro per ripristinare il funzionamento, in modo tale percentuale deve essere configurata per tollerare che.

- [ApplicationTypeHealthPolicyMap](https://msdn.microsoft.com/library/azure/system.fabric.health.clusterhealthpolicy.applicationtypehealthpolicymap.aspx). Il mapping di criteri applicazione dell'integrità tipo è utilizzabile durante la valutazione dell'integrità cluster per descrivere i tipi di applicazione speciale. Per impostazione predefinita, tutte le applicazioni vengono inserite in un pool e valutate con MaxPercentUnhealthyApplications. Se alcuni tipi di applicazione devono essere gestiti in modo diverso, possono essere creati dal pool globale. Se, tuttavia, vengono confrontate le percentuali associate con il nome del tipo di applicazione nella mappa. Ad esempio, in un cluster sono disponibili migliaia di applicazioni di tipo diverso e alcuni istanze di applicazione di controllo di un tipo di applicazione speciale. Le applicazioni di controllo non dovrebbero mai essere errore. È possibile specificare MaxPercentUnhealthyApplications globale 20% di tollerare alcuni errori, ma per il tipo di applicazione "ControlApplicationType" impostata la MaxPercentUnhealthyApplications su 0. In questo modo, se alcune delle applicazioni molti non corretti, ma sotto la percentuale non corretti globale, il cluster da restituire avviso. Stato di integrità avviso non influisce sull'aggiornamento cluster o altri tipi di monitoraggio attivate tramite lo stato di integrità di errore. Ma anche un controllo applicazione errore consentirebbe cluster danneggiato, che attiva ripristino o sospende l'aggiornamento del cluster, a seconda della configurazione di aggiornamento.
Per i tipi di applicazione definiti nella mappa, tutte le istanze di applicazione provengono dal pool di applicazioni globale. Vengono valutati in base al numero totale di applicazioni del tipo di applicazione, utilizzando MaxPercentUnhealthyApplications specifici dalla mappa. Tutto il resto delle applicazioni rimangono nel pool di globale e vengono valutati con MaxPercentUnhealthyApplications.

Nell'esempio seguente è un estratto da un manifesto cluster. Per definire le voci nella mappa del tipo di applicazione, prefisso il nome del parametro con "ApplicationTypeMaxPercentUnhealthyApplications-", seguito dal nome del tipo di applicazione.

```xml
<FabricSettings>
  <Section Name="HealthManager/ClusterHealthPolicy">
    <Parameter Name="ConsiderWarningAsError" Value="False" />
    <Parameter Name="MaxPercentUnhealthyApplications" Value="20" />
    <Parameter Name="MaxPercentUnhealthyNodes" Value="20" />
    <Parameter Name="ApplicationTypeMaxPercentUnhealthyApplications-ControlApplicationType" Value="0" />
  </Section>
</FabricSettings>
```

### <a name="application-health-policy"></a>Criteri di applicazione dell'integrità
Il [criterio di integrità applicazione](https://msdn.microsoft.com/library/azure/system.fabric.health.applicationhealthpolicy.aspx) descrive come la valutazione di eventi e le aggregazioni stati figlio viene eseguita per applicazioni e ai relativi oggetti figlio. Può essere definita nel manifesto dell'applicazione **ApplicationManifest.xml**nel pacchetto dell'applicazione. Se non viene specificato alcun criterio, tessuti servizio presuppone che l'entità non corretti se dispone di un rapporto di stato o un elemento figlio lo stato di integrità avviso o errore.
I criteri configurabili sono:

- [ConsiderWarningAsError](https://msdn.microsoft.com/library/azure/system.fabric.health.applicationhealthpolicy.considerwarningaserror.aspx). Specifica se trattare integrità avviso segnalare come errori durante la valutazione dell'integrità. Predefinito: false.

- [MaxPercentUnhealthyDeployedApplications](https://msdn.microsoft.com/library/azure/system.fabric.health.applicationhealthpolicy.maxpercentunhealthydeployedapplications.aspx). Specifica la percentuale massima tollerata delle applicazioni distribuite che possono essere non corretti prima che l'applicazione è considerato errore. Questa percentuale viene calcolata dividendo il numero delle applicazioni distribuite non corretti per il numero dei nodi le applicazioni attualmente installati del cluster. Il calcolo Arrotonda il valore di tollerare un errore in numeri piccoli dei nodi. Percentuale predefinito: zero.

- [DefaultServiceTypeHealthPolicy](https://msdn.microsoft.com/library/azure/system.fabric.health.applicationhealthpolicy.defaultservicetypehealthpolicy.aspx). Specifica il servizio tipo integrità criterio predefinito, che sostituiscono i criteri di integrità predefiniti per tutti i tipi di servizio dell'applicazione.

- [ServiceTypeHealthPolicyMap](https://msdn.microsoft.com/library/azure/system.fabric.health.applicationhealthpolicy.servicetypehealthpolicymap.aspx). Fornisce una mappa dei criteri di integrità dei servizi per tipo di servizio. Questi criteri sostituiscono i criteri di integrità tipo servizio predefiniti per ogni tipo di servizio specificato. Ad esempio, se un'applicazione dispone di un tipo di servizio gateway senza informazioni sullo stato e un tipo di servizio informazioni sullo stato motore, è possibile configurare i criteri di integrità di valutazione in modo leggermente diverso. Quando si imposta la riproduzione dei criteri per ogni tipo di servizio, è possibile ottenere un controllo più dettagliato delle condizioni del servizio.

### <a name="service-type-health-policy"></a>Criterio di integrità di tipo servizio
Il [criterio di integrità di tipo servizio](https://msdn.microsoft.com/library/azure/system.fabric.health.servicetypehealthpolicy.aspx) specifica la modalità di valutare e aggregare i servizi e gli elementi figlio di servizi. Il criterio contiene:

- [MaxPercentUnhealthyPartitionsPerService](https://msdn.microsoft.com/library/azure/system.fabric.health.servicetypehealthpolicy.maxpercentunhealthypartitionsperservice.aspx). Specifica la percentuale massima tollerata delle partizioni non corretti prima di un servizio viene considerato non corretti. Percentuale predefinito: zero.

- [MaxPercentUnhealthyReplicasPerPartition](https://msdn.microsoft.com/library/azure/system.fabric.health.servicetypehealthpolicy.maxpercentunhealthyreplicasperpartition.aspx). Specifica la percentuale massima tollerata di repliche non corretti prima di una partizione viene considerata non corretti. Percentuale predefinito: zero.

- [MaxPercentUnhealthyServices](https://msdn.microsoft.com/library/azure/system.fabric.health.servicetypehealthpolicy.maxpercentunhealthyservices.aspx). Specifica la percentuale massima tollerata dei servizi non corretti prima dell'applicazione viene considerato non corretti. Percentuale predefinito: zero.

Nell'esempio seguente è un estratto da un manifesto dell'applicazione:

```xml
    <Policies>
        <HealthPolicy ConsiderWarningAsError="true" MaxPercentUnhealthyDeployedApplications="20">
            <DefaultServiceTypeHealthPolicy
                   MaxPercentUnhealthyServices="0"
                   MaxPercentUnhealthyPartitionsPerService="10"
                   MaxPercentUnhealthyReplicasPerPartition="0"/>
            <ServiceTypeHealthPolicy ServiceTypeName="FrontEndServiceType"
                   MaxPercentUnhealthyServices="0"
                   MaxPercentUnhealthyPartitionsPerService="20"
                   MaxPercentUnhealthyReplicasPerPartition="0"/>
            <ServiceTypeHealthPolicy ServiceTypeName="BackEndServiceType"
                   MaxPercentUnhealthyServices="20"
                   MaxPercentUnhealthyPartitionsPerService="0"
                   MaxPercentUnhealthyReplicasPerPartition="0">
            </ServiceTypeHealthPolicy>
        </HealthPolicy>
    </Policies>
```

## <a name="health-evaluation"></a>Valutazione dell'integrità
Utenti e servizi automatizzati possono valutare l'integrità per qualsiasi entità in qualsiasi momento. Per valutare integrità un'entità, le aggregazioni di archivio di integrità salute tutti i report sull'entità e restituisce tutti gli elementi figlio (se applicabile). Algoritmo di aggregazione integrità utilizza criteri di integrità che specificano come valutare report di stato e modalità di aggregazione stati di integrità figlio (se applicabile).

### <a name="health-report-aggregation"></a>Aggregazione di report di stato
Un'entità può avere più report di stato provenienti dai diversi reporters (componenti del sistema o watchdog) nella proprietà diverse. L'aggregazione vengono utilizzati i criteri di integrità associato, in particolare il membro ConsiderWarningAsError dell'applicazione o cluster criterio di integrità. ConsiderWarningAsError specifica la modalità di valutare gli avvisi.

Lo stato di integrità aggregato viene attivato da *peggiore* report di stato sull'entità. Se c'è il rapporto di stato di almeno un errore, lo stato di integrità aggregato è un errore.

![Aggregazione report di stato con segnalazione errori.][2]

Viene valutata un'entità di integrità che contiene uno o più report di stato di errore come errore. Lo stesso avviene per un report di stato scaduto, indipendentemente dal relativo stato di integrità.

[2]: ./media/service-fabric-health-introduction/servicefabric-health-report-eval-error.png

Se sono presenti senza segnalazioni errori e uno o più avvisi, lo stato di integrità aggregato è avviso o errore, a seconda di contrassegno di criteri ConsiderWarningAsError.

![Aggregazione di report dello stato con report di avviso e ConsiderWarningAsError false.][3]

L'aggregazione di report dello stato con report di avviso e ConsiderWarningAsError impostato su false (impostazione predefinita).

[3]: ./media/service-fabric-health-introduction/servicefabric-health-report-eval-warning.png

### <a name="child-health-aggregation"></a>Aggregazione integrità figlio
Lo stato di integrità aggregati di un'entità riflette gli stati di integrità figlio (se applicabile). Algoritmo per l'aggregazione di stati di integrità figlio vengono utilizzati i criteri di integrità applicabili in base al tipo di entità.

![Aggregazione di integrità di entità figlio.][4]

Aggregazione figlio in base a criteri di integrità.

[4]: ./media/service-fabric-health-introduction/servicefabric-health-hierarchy-eval.png

Dopo l'archivio di integrità è valutato tutti gli elementi figlio, aggrega gli stati di integrità in base alla percentuale massima configurata figli non corretti. Questa percentuale viene accettata dai criteri in base al tipo di entità e figlio.

- Se tutti gli elementi figlio sono stati OK, lo stato di integrità figlio aggregato è OK.

- Nel caso figli OK e stati di avviso, lo stato di integrità figlio aggregato è avviso.

- Se sono presenti elementi figlio con gli stati di errore che non rispettano la massima consentita percentuale figli danneggiato, lo stato di integrità aggregato è un errore.

- Se gli elementi figlio con errore stati rispettano la massima percentuale figli danneggiato, lo stato di integrità aggregato è avviso.

## <a name="health-reporting"></a>Report di integrità
Componenti del sistema, applicazioni tessuti di sistema e interno/esterno watchdog segnalare con entità servizio tessuti. Il reporter verificare *locale* decisioni dell'integrità di entità controllate, in base a condizioni che sottoposti a monitoraggio. Non è necessario esaminare qualsiasi stato globale o aggregare i dati. Il comportamento desiderato è necessario reporters semplici e non complessi organismi che è necessario esaminare molti aspetti di dedurre le informazioni da inviare.

Per inviare integrità dei dati per l'archivio di integrità, un strumento di creazione report deve identificare l'entità interessato e creare un rapporto di stato. Il report quindi può essere inviato tramite l'API tramite [FabricClient.HealthClient.ReportHealth](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient_members.aspx), PowerShell o resto.

### <a name="health-reports"></a>Report di stato
I [report di stato](https://msdn.microsoft.com/library/azure/system.fabric.health.healthreport.aspx) per ognuna delle entità del cluster contengono le informazioni seguenti:

- **SourceId**. Stringa che identifica in modo univoco chi l'evento di stato.

- **Identificatore entità**. Identifica l'entità in cui è applicato il report. Essa varia in base al [entità digitare](service-fabric-health-introduction.md#health-entities-and-hierarchy):

  - Cluster. Nessuno.

  - Nodo. Nome del nodo (stringa).

  - Applicazione. Nome dell'applicazione (URI). Rappresenta il nome dell'istanza dell'applicazione distribuita in cluster.

  - Servizio. Nome del servizio (URI). Rappresenta il nome dell'istanza del servizio distribuita in cluster.

  - Partizione. ID partizione (GUID). Rappresenta l'identificatore univoco partizione.

  - Replica. L'ID di replica di informazioni sullo stato del servizio o l'ID dell'istanza senza informazioni sullo stato del servizio (INT64).

  - DeployedApplication. Nome dell'applicazione (URI) e il nome del nodo (stringa).

  - DeployedServicePackage. Nome dell'applicazione (URI), il nome del nodo (stringa) e servizio manifesto name (stringa).

- **Proprietà**. *Stringa* (non enumerazione fisso) che consente a chi categorizzare l'evento di stato per una specifica proprietà dell'entità. Ad esempio, strumento di creazione report A segnalare che lo stato della proprietà Node01 "archiviazione" e strumento di creazione report B possa creare report sullo stato della proprietà Node01 "connettività". Nell'archivio integrità questi report sono considerati eventi relativi allo stato separati per l'entità Node01.

- **Descrizione**. Stringa che consente di strumento di creazione report presentare informazioni dettagliate sull'evento integrità. **SourceId**, **proprietà**e **HealthState** deve descrivere il report. La descrizione aggiunge leggibile informazioni sul report. Il testo rende più facile per amministratori e utenti di comprendere il rapporto di stato.

- **HealthState**. Un' [enumerazione](service-fabric-health-introduction.md#health-states) che descrive l'integrità del report. I valori accettati sono OK, avvisi ed errori.

- **TimeToLive**. Un intervallo di tempo che indica il periodo di tempo il rapporto di stato è valido. Oltre a **RemoveWhenExpired**, consente l'archivio di integrità disponibili informazioni su come calcolare eventi scaduti. Per impostazione predefinita, il valore è infinito e il report è valido per sempre.

- **RemoveWhenExpired**. Valore booleano. Se è impostato su true, il rapporto di stato scaduto automaticamente viene rimosso dall'archivio di salute e il report non effetti in termini di valutazione di integrità di entità. Utilizzato quando il report è valido per un determinato periodo di tempo solo e chi non è necessario cancellare in modo esplicito. Viene usato anche per eliminare i rapporti dall'archivio di integrità (ad esempio, viene quindi viene modificato e interrompe l'invio di report con origine precedente e proprietà). È possibile inviare un report con una breve TimeToLive insieme RemoveWhenExpired per eliminare qualsiasi stato precedente dall'archivio di integrità. Se il valore è impostato su false, il report scaduto viene considerato come un errore durante la valutazione dell'integrità. Il valore false segnala all'archivio di integrità che l'origine deve segnalare periodicamente su questa proprietà. In caso contrario, deve essere presente un problema con il controllo. Integrità del controllo vengono acquisite da prendere in considerazione l'evento come un errore.

- **SequenceNumber**. Un intero positivo che deve essere crescente, rappresenta l'ordine dei report. Viene utilizzato dall'archivio di integrità per rilevare obsoleti nei report che vengono ricevuti in ritardo a causa di ritardi di rete o altri problemi. Un report è rifiutato se il numero di sequenza è che minore o uguale al più recente applicati numero per la stessa entità, origine e proprietà. Se non è specificato, viene generato automaticamente il numero di sequenza. È necessario inserire il numero di sequenza solo quando si segnalano su transizioni. In questo caso, l'origine deve ricordare report è inviato e mantenere le informazioni per il ripristino in caso di failover.

Questi quattro tipi di informazione: SourceId, identificatore entità, proprietà e HealthState - sono necessari per ogni rapporto di stato. L'ID di origine non è consentita stringa per iniziare con il prefisso "**sistema**", riservato di sistema di report. Per la stessa entità non esiste un solo report per la proprietà e la stessa origine. Relazioni per la stessa origine e proprietà ignorare ogni altra, sul lato client integrità (se sono batch) o sull'integrità lato dell'archivio. La sostituzione è basata sui numeri di sequenza; i report più recenti (con i valori più alti sequenza) sostituiranno report precedente.

### <a name="health-events"></a>Eventi di stato
Internamente, l'archivio di integrità mantiene aggiornata [agli eventi](https://msdn.microsoft.com/library/azure/system.fabric.health.healthevent.aspx), che contiene tutte le informazioni da report e metadati aggiuntivi. I metadati includono ora che il report è stato assegnato a client integrità e l'ora che ultima modifica sul lato server. Gli eventi di stato vengono restituiti dalle [query di integrità](service-fabric-view-entities-aggregated-health.md#health-queries).

Contengono i metadati aggiunto:

- **SourceUtcTimestamp**. Il tempo il report è stato assegnato al client integrità (Coordinated Universal Time).

- **LastModifiedUtcTimestamp**. Ora che dell'ultima modifica il report sul lato server (Coordinated Universal Time).

- **IsExpired**. Un contrassegno per indicare se il rapporto di stato scaduto quando è stata eseguita la query da parte dell'archivio di integrità. Un evento può essere scaduto solo se RemoveWhenExpired è false. In caso contrario, l'evento non viene restituito dalla query e viene rimosso dall'archivio.

- **LastOkTransitionAt**, **LastWarningTransitionAt** **LastErrorTransitionAt**. L'ultima volta per le transizioni OK/avviso/errore. Questi campi inviare la cronologia di stato di integrità transizioni di stato per l'evento.

I campi di transizione stato possono essere utilizzati per gli avvisi più intelligenti o informazioni sugli eventi di integrità "cronologici". Consentono ad esempio scenari:

- Un avviso quando una proprietà è stata all'avviso/errore per più di X minuti. Controllare la condizione per un periodo di tempo evita avvisi condizioni temporaneo. Ad esempio, un avviso se lo stato di integrità è state avviso per più di cinque minuti può essere convertito in (HealthState = = avviso e ora - LastWarningTransitionTime > 5 minuti).

- Avviso solo alle condizioni che hanno subito modifiche negli ultimi X minuti. Se un report era già all'errore prima di tempo specificato, può essere ignorata perché è già stato segnalato in precedenza.

- Se una proprietà è per alternare tra avviso e di errore, verificare quanto tempo è stato danneggiato (non OK). Ad esempio, un avviso se la proprietà non è stato integro per più di cinque minuti può essere convertito in (HealthState! = Ok e ora - LastOkTransitionTime > 5 minuti).

## <a name="example-report-and-evaluate-application-health"></a>Esempio: Report e valutare dell'integrità
Nell'esempio seguente invia un rapporto di stato tramite PowerShell sull'applicazione **tessuti: / WordCount** dal sito di origine **MyWatchdog**. Il rapporto di stato contiene informazioni sulla proprietà integrità "disponibilità" in uno stato di integrità di errore, con TimeToLive infinito. Quindi, viene eseguita l'integrità dell'applicazione che restituisce aggregato errori dello stato di salute e gli eventi di stato segnalati nell'elenco degli eventi di stato.

```powershell
PS C:\> Send-ServiceFabricApplicationHealthReport –ApplicationName fabric:/WordCount –SourceId "MyWatchdog" –HealthProperty "Availability" –HealthState Error

PS C:\> Get-ServiceFabricApplicationHealth fabric:/WordCount


ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Error
UnhealthyEvaluations            :
                                  Error event: SourceId='MyWatchdog', Property='Availability'.

ServiceHealthStates             :
                                  ServiceName           : fabric:/WordCount/WordCountService
                                  AggregatedHealthState : Error

                                  ServiceName           : fabric:/WordCount/WordCountWebService
                                  AggregatedHealthState : Ok

DeployedApplicationHealthStates :
                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_0
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_2
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_3
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_4
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_1
                                  AggregatedHealthState : Ok

HealthEvents                    :
                                  SourceId              : System.CM
                                  Property              : State
                                  HealthState           : Ok
                                  SequenceNumber        : 360
                                  SentAt                : 3/22/2016 7:56:53 PM
                                  ReceivedAt            : 3/22/2016 7:56:53 PM
                                  TTL                   : Infinite
                                  Description           : Application has been created.
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : Error->Ok = 3/22/2016 7:56:53 PM, LastWarning = 1/1/0001 12:00:00 AM

                                  SourceId              : MyWatchdog
                                  Property              : Availability
                                  HealthState           : Error
                                  SequenceNumber        : 131032204762818013
                                  SentAt                : 3/23/2016 3:27:56 PM
                                  ReceivedAt            : 3/23/2016 3:27:56 PM
                                  TTL                   : Infinite
                                  Description           :
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : Ok->Error = 3/23/2016 3:27:56 PM, LastWarning = 1/1/0001 12:00:00 AM
```

## <a name="health-model-usage"></a>Uso del modello di integrità
Grazie al modello di integrità dei servizi cloud e la piattaforma tessuti servizio sottostante scalare, poiché determinare il monitoraggio e l'integrità sono distribuiti in monitor diversi all'interno del cluster.
Altri sistemi dispone di un servizio singolo, centralizzato livello di cluster che analizza tutte le *potenzialmente* informazioni utili emesse da servizi. Questo approccio ostacola loro scalabilità. Inoltre non consente di raccogliere informazioni specifiche per identificare i potenziali problemi come vicino causa possibile e problemi.

Il modello di integrità viene utilizzato in modo rilevante per la diagnosi e il monitoraggio, per la valutazione dell'integrità cluster e applicazione e per gli aggiornamenti monitorati. Altri servizi di utilizzano dati di integrità per eseguire le operazioni di ripristino automatici, creare cronologia integrità cluster e rilasciare avvisi determinate condizioni.

## <a name="next-steps"></a>Passaggi successivi
[Visualizza report integrità servizio tessuti](service-fabric-view-entities-aggregated-health.md)

[Utilizzare il report di stato di sistema per la risoluzione dei problemi](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Come segnalare e verificare l'integrità del servizio](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[Aggiungere report di stato servizio tessuti personalizzato](service-fabric-report-health.md)

[Monitorare e diagnosticare servizi localmente](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Aggiornamento dell'applicazione di servizio tessuti](service-fabric-application-upgrade.md)
