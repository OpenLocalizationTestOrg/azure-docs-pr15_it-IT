<properties
   pageTitle="Le cause comuni dei ruoli di servizio Cloud riciclaggio | Microsoft Azure"
   description="Un ruolo di servizio cloud che improvvisamente Ricicla può causare tempi di inattività significativi. Ecco alcuni problemi comuni che causano ruoli essere riutilizzata, che consentono di ridurre i tempi di inattività."
   services="cloud-services"
   documentationCenter=""
   authors="simonxjx"
   manager="felixwu"
   editor=""
   tags="top-support-issue"/>
<tags
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd"
   ms.date="09/02/2016"
   ms.author="v-six" />

# <a name="common-issues-that-cause-roles-to-recycle"></a>Problemi che causano ruoli al Cestino

In questo articolo vengono illustrate alcune delle cause comuni dei problemi di distribuzione e suggerimenti sulla risoluzione dei problemi per risolvere questi problemi. L'indicazione che si verifica un problema con un'applicazione è quando non è possibile avviare l'istanza di ruoli o per passare alternativamente l'inizializzazione, occupato e di arresto.

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="missing-runtime-dependencies"></a>Dipendenze runtime mancanti

Se un ruolo nell'applicazione si basa su una libreria gestita assembly che non fa parte di .NET Framework o di Azure, è necessario includere in modo esplicito quell'assembly nel pacchetto dell'applicazione. Tenere presente che altri modelli di Microsoft non sono disponibili in Azure per impostazione predefinita. Se il proprio ruolo si basa su tale quadro, è necessario aggiungere tali assembly per il pacchetto dell'applicazione.

Prima di creare e assemblare l'applicazione, verificare quanto segue:

- Se utilizzando Visual studio, assicurarsi che la proprietà **Copia locale** è impostata su **True** per ogni assembly del progetto che non fa parte di Azure SDK o .NET Framework.

- Verificare che il file config non fa riferimento a qualsiasi assembly inutilizzato nell'elemento compilazione.

- L' **Operazione di compilazione** di ogni file cshtml è impostata su **contenuto**. In questo modo che i file verranno visualizzate correttamente nel pacchetto e attiva altri file di cui si fa riferimento all'interno del pacchetto.

## <a name="assembly-targets-wrong-platform"></a>Piattaforma non corretta destinazioni assembly

Azure è un ambiente a 64 bit. Di conseguenza, assembly .NET compilato per una versione a 32 bit di destinazione non funzionano in Azure.

## <a name="role-throws-unhandled-exceptions-while-initializing-or-stopping"></a>Ruolo eccezioni non gestita durante l'inizializzazione o arresto

Eventuali eccezioni generate dai metodi della classe [RoleEntryPoint] , che include le [OnStart], [OnStop]e i metodi di [esecuzione] , costituiscono un'eccezione non gestita. Se si verifica un'eccezione non gestita in uno di questi metodi, verrà Cestino il ruolo. Se il ruolo Ricicla ripetutamente, potrebbe essere generare un'eccezione non gestita ogni volta che si tenta di avviarlo.

## <a name="role-returns-from-run-method"></a>Ruolo restituisce dal metodo Esegui

Il metodo [Run] deve essere eseguito in modo indefinito. Se il codice sostituisce il metodo [Run] , periodo di inattività tempo indefinito. Se il metodo [Run] restituisce, ricicla il ruolo.

## <a name="incorrect-diagnosticsconnectionstring-setting"></a>Impostazione di DiagnosticsConnectionString non corretta

Se l'applicazione utilizza diagnostica Azure, il file di configurazione del servizio necessario specificare il `DiagnosticsConnectionString` impostazione di configurazione. Questa impostazione è necessario specificare una connessione HTTPS al proprio account di archiviazione in Azure.

Per assicurarsi che il `DiagnosticsConnectionString` impostazione sia corretta prima di distribuire il pacchetto dell'applicazione in Azure, verificare quanto segue:  

- Il `DiagnosticsConnectionString` impostazione punti a un account valido dello spazio di archiviazione in Azure.  
  Per impostazione predefinita, questa impostazione punti importanti per l'account di memoria emulata, in modo che è necessario modificare questa impostazione in modo esplicito prima di distribuire il pacchetto dell'applicazione. Se non si modifica questa impostazione, viene generata un'eccezione quando l'istanza di ruolo tenta di avviare il monitor diagnostico. Questa operazione può causare l'istanza di ruolo a tempo indefinito Cestino.

- La stringa di connessione specificata nel [formato](../storage/storage-configure-connection-string.md)seguente. (Il protocollo deve essere specificato come HTTPS). Sostituire *MyAccountName* con il nome di account di archiviazione e *MyAccountKey* con il tasto di scelta:    

        DefaultEndpointsProtocol=https;AccountName=MyAccountName;AccountKey=MyAccountKey

  Se si sviluppa l'applicazione tramite Azure Tools per Microsoft Visual Studio, è possibile utilizzare le [pagine delle proprietà](https://msdn.microsoft.com/library/ee405486) per impostare questo valore.

## <a name="exported-certificate-does-not-include-private-key"></a>Certificato esportato non include la chiave privata

Per eseguire un ruolo web in SSL, è necessario assicurarsi che il certificato di gestione esportato include la chiave privata. Se si utilizza *Gestione certificati di Windows* per esportare il certificato, assicurarsi di selezionare **Sì** per l'opzione **Esporta la chiave privata** . Il certificato deve essere esportato in formato PFX, che è l'unico formato attualmente supportato.

## <a name="next-steps"></a>Passaggi successivi

Visualizzare altri [articoli di risoluzione dei problemi](https://azure.microsoft.com/documentation/articles/?tag=top-support-issue&product=cloud-services) per i servizi cloud.

Consente di visualizzare altre ruolo riciclaggio scenari nella [serie di blog di Kevin Williamson](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).

[RoleEntryPoint]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.aspx
[OnStart]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx
[OnStop]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstop.aspx
[Correre]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx
