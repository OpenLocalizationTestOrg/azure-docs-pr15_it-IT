<properties 
   pageTitle="Note sulla versione di Azure SDK per .NET 2.6" 
   description="Note sulla versione di Azure SDK per .NET 2.6" 
   services="app-service/web" 
   documentationCenter=".net" 
   authors="Juliako" 
   manager="erikre" 
   editor=""/>

<tags
   ms.service="app-service"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="10/17/2016"
   ms.author="juliako"/>

 
# <a name="azure-sdk-for-net-26-release-notes"></a>Note sulla versione di Azure SDK per .NET 2.6

Questo documento contiene le note sulla versione di Azure SDK .NET 2.6 versione. 

Con Azure SDK 2.6 è possibile sviluppare applicazioni di servizio cloud (PaaS), a condizione che si installa manualmente .NET Framework di destinazione nel ruolo di servizio Cloud di destinazione .NET 4.5.2 o 4.6 .NET. Vedere [installare .NET su un ruolo di servizio Cloud](http://go.microsoft.com/fwlink/?LinkID=309796).


##<a name="service-bus-updates"></a>Aggiornamenti di Bus di servizio

- Hub di evento: 

    - Quando si inviano eventi tramite l'esposizione endpoint publisher aggiuntive per gli hub di evento ora consente il controllo dell'accesso di destinazione.
    - Maggiore stabilità e il miglioramento aggiunto alla caratteristica hub evento.
    - Aggiunta di supporto del protocollo Amqp WebSocket per la messaggistica e hub di evento.

##<a name="hdinsight-tools-for-visual-studio-updates"></a>Aggiorna HDInsight Tools per Visual Studio

- **Miglioramento IntelliSense**: suggerimento metadati remoto

    HDInsight Tools per Visual Studio supporta il recupero dei metadati remoto durante la modifica di uno script Hive. Ad esempio, è possibile digitare * *Selezionare* FROM** e verranno visualizzati tutti i nomi di tabella. Inoltre, i nomi delle colonne verranno visualizzati dopo aver specificato una tabella.

- **Supporto emulatore HDInsight**

    HDInsight Tools per Visual Studio supporta la connessione all'emulatore HDInsight, in modo che è possibile sviluppare script Hive localmente senza introdurre i costi, quindi eseguire gli script con i cluster HDInsight. 

    Per ulteriori informazioni, fare riferimento al [manuale](http://go.microsoft.com/fwlink/?LinkID=529540&clcid=0x409).

- **HDInsight Tools per Visual Studio di supporto per cluster Hadoop generico** (Anteprima)

    HDInsight Tools per Visual Studio supporta cluster Hadoop generico, è possibile utilizzare HDInsight Tools per Visual Studio per eseguire le operazioni seguenti:

    - connettersi al cluster 
    - scrivere query Hive supporto avanzato di completamento automatico IntelliSense, 
    - Visualizza tutti i processi del cluster con un'interfaccia utente intuitiva. 

    Per ulteriori informazioni, fare riferimento al [manuale](http://go.microsoft.com/fwlink/?LinkID=529540&clcid=0x409).

##<a name="in-role-cache-updates"></a>Aggiornamenti della Cache in ruolo

- **Cache in ruolo** è stato aggiornato per l'utilizzo di **Microsoft Azure archiviazione SDK** versione 4.3. Fino a questo momento, la **Cache di ruolo** utilizzava Azure archiviazione SDK versione 1.7.

    I clienti con Azure SDK 2,5 o sotto aggiornare a Azure SDK 2.6 e passare alla nuova versione di Azure archiviazione SDK. 

    In questa fase di archiviazione Azure versione 2011-08-18 è programmata da rimuovere 1 agosto 2016. Le migrazioni della Cache nella ruolo da Azure SDK 2.5 o di sotto di 2.6 devono essere completate da questo momento. Per ulteriori informazioni sul pensionistico di spazio di archiviazione di Azure versione 2011-08-18, vedere [Microsoft Azure lo spazio di archiviazione servizio versione rimozione aggiornamento: estensione a 2016](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/10/19/microsoft-azure-storage-service-version-removal-update-extension-to-2016.aspx).

>[AZURE.IMPORTANT]Abbiamo stiamo annuncio pensionistico 30 novembre 2016, per servizio Cache gestite Azure e Azure In ruolo Cache. È consigliabile migrare Azure Redis Cache in preparazione per questo pensionistico. Per ulteriori informazioni su data e indicazioni per la migrazione, vedere [offerta della Cache Azure a cui è appropriata?](../redis-cache/cache-faq.md#which-azure-cache-offering-is-right-for-me)

##<a name="azure-app-service-tools"></a>Strumenti di Azure App servizio

Gli elementi seguenti sono stati aggiornati nella versione Azure SDK 2.6.

- Pubblicazione Azure migliorata per includere dati di Azure API App come destinazione di distribuzione.
- API App per il provisioning di funzionalità per consentire agli utenti con le funzionalità di creazione e il provisioning di API App.
- Esplora server cambia per riflettere nuovo nodo servizio App, con le applicazioni Web, mobili e API raggruppate per gruppo di risorse.
- Aggiungere gesto di Azure API App Client aggiunto alla maggior parte dei progetti c# consentono la generazione automatica di attivazione Swagger API App in esecuzione in Azure abbonamento di un utente.
- Utensili API App e servizio App nodi in Esplora Server sono disponibili in Visual Studio 2013 solo. 

##<a name="azure-resource-manager-tools-updates"></a>Aggiornamenti degli strumenti di gestione risorse di Azure

Gli strumenti di gestione delle risorse Azure sono stati aggiornati per includere i modelli di macchine virtuali, rete e archiviazione. JSON esperienza di modifica è stata aggiornata per includere una nuova visualizzazione struttura per i modelli e la possibilità di modificare i modelli di utilizzo di frammenti JSON. Modelli distribuiti da Visual Studio utilizzano uno script di PowerShell disponibili in project, in modo che le modifiche apportate allo script verranno utilizzate da Visual Studio.

##<a name="diagnostics-improvements-for-cloud-services"></a>Miglioramenti di diagnostica per i servizi Cloud

Azure SDK 2.6 riporta il supporto per la raccolta dei registri di diagnostica nell'emulatore calcolo Azure e il loro trasferimento allo spazio di archiviazione di sviluppo. Qualsiasi diagnostica log (inclusi applicazione analisi dei registri, l'analisi per i registri di Windows (ETW), contatori, infrastruttura i registri eventi di windows e i registri degli eventi) generate quando l'applicazione è in esecuzione nell'emulatore possono essere trasferiti allo spazio di archiviazione di sviluppo per verificare che la registrazione diagnostica effettui sul computer locale. 

A questo punto specificare l'account di archiviazione diagnostica nel file di configurazione (. cscfg) servizio rendendo più semplice da utilizzare gli account di archiviazione di diagnostica diversi per gli ambienti diversi. Esistono alcune differenze importanti tra il modo di lavorare in Azure SDK 2,4 e Azure SDK 2.6 la stringa di connessione. Per ulteriori informazioni su come utilizzare la connessione di spazio di archiviazione di diagnostica stringa e impatto che i progetti vedere [Configurazione di diagnostica per servizi Cloud Windows Azure](http://go.microsoft.com/fwlink/?LinkID=532784).

##<a name="breaking-changes"></a>Ultime modifiche

###<a name="azure-resource-manager-tools"></a>Strumenti di gestione risorse di Azure 

- Il tipo di progetto **I progetti di distribuzione Cloud** in Azure SDK 2.5 è stato rinominato al **Gruppo di risorse Azure**.
- Tipo di **Progetti di distribuzione cloud** dei progetti creati in Azure SDK 2.5 può essere utilizzato in 2.6 ma distribuire il modello da Visual Studio avrà esito negativo. Tuttavia, la distribuzione con lo script di PowerShell ancora funzionano come in precedenza.  Per informazioni su come utilizzare **I progetti di distribuzione Cloud** 2.6 leggere questo [post](http://go.microsoft.com/fwlink/?LinkID=534086).
 
##<a name="known-issues"></a>Problemi noti

- Raccogliere i log di diagnostica nell'emulatore richiede un sistema operativo a 64 bit. Registri di diagnostica non verranno raccolte durante l'esecuzione in un sistema operativo a 32 bit. Questa operazione non ha alcun effetto altre funzionalità di emulatore. 

- Azure 2.6 SDK rilasciati 29/4/2015 ha due aspetti: 

    - Impossibile caricare l'App universale in Visual Studio 2015 quando Azure SDK 2.6 è stato installato nel computer.
    - Debug di un progetto di servizio Cloud avrà esito negativo in Visual Studio 2013 e Visual Studio 2015, in Visual Studio smette di rispondere e determina l'arresto anomalo durante la visualizzazione di una finestra di dialogo con il messaggio "Diagnostica configurazione per emulatore".
    
    L'aggiornamento di Azure SDK 2.6 è stato rilasciato il 5/18/2015. La versione aggiornata è 2.6.30508.1601; la presentazione contiene le correzioni per due problemi descritti sopra. È possibile identificare la compilazione di SDK dal Pannello di controllo -> programmi e funzionalità -> Strumenti di Microsoft Azure per Microsoft Visual Studio 2013-v 2.6. Nella colonna versione verrà visualizzato il numero di build.

    Se sono comunque affiancate questi problemi, installare l'ultima versione di Azure 2.6 SDK per [Visual Studio 2012](http://go.microsoft.com/fwlink/p/?linkid=323511&clcid=0x409), [VS 2013](http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409) o [VS 2015](http://go.microsoft.com/fwlink/?linkid=518003&clcid=0x409).
 
##<a name="see-also"></a>Vedere anche

[Supporto e informazioni pensionistico per Azure SDK per .NET e le API](https://msdn.microsoft.com/library/azure/dn479282.aspx/)
