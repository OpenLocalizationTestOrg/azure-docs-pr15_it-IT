<properties
    pageTitle="Servizi pubblici Azure | Microsoft Azure"
    description="Informazioni sulla gestione dell'abbonamento per enti pubblici Azure"
    services="Azure-Government"
    cloud="gov" 
    documentationCenter=""
    authors="zakramer"
    manager="liki"
    editor="" />

<tags
    ms.service="multiple"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="azure-government"
    ms.date="10/21/2016"
    ms.author="zakramer" />


#  <a name="managing-and-connecting-to-your-subscription-in-azure-government"></a>Gestione e la connessione al proprio abbonamento in Azure per la pubblica amministrazione

Per enti pubblici Azure sono univoci URL e gli endpoint per gestire l'ambiente. È importante utilizzare connessioni appropriate per gestire l'ambiente tramite il portale oppure PowerShell. Una volta che si è connessi all'ambiente di Azure per enti pubblici, il normale funzionamento per la gestione di un servizio funziona se il componente è stato distribuito.

## <a name="connecting-via-the-portal"></a>La connessione tramite il portale
Il portale è il modo principale che si connettono maggior parte delle persone a Government Azure.  Per connettersi, esplorare il portale [all'https://portal.azure.us](https://portal.azure.us).  È possibile accedere alla versione precedente del portale di Azure tramite [https://manage.windowsazure.us](https://manage.windowsazure.us).

Connettendosi a [https://account.windowsazure.us](https://account.windowsazure.us), è possibile creare sottoscrizioni per l'account.

## <a name="connecting-via-powershell"></a>La connessione tramite PowerShell

Se si usa Azure PowerShell per gestire grande abbonamento tramite script o access caratteristiche che non sono attualmente disponibili nel portale di Azure che è necessario connettersi per enti pubblici Azure invece di Azure pubblico.  Se è stata utilizzata PowerShell in Azure pubblico, è prevalentemente lo stesso.  Le differenze per enti pubblici Azure sono:

+ Connette l'account
+ Nomi di aree

>[AZURE.NOTE] Se non è stato utilizzato ancora PowerShell, vedere [Introduzione a PowerShell Azure](../powershell-install-configure.md).

Quando si avvia PowerShell, è necessario indicare PowerShell Azure a cui connettersi per enti pubblici Azure specificando un parametro di ambiente.  Il parametro assicura che PowerShell si connette a endpoint corretto.  L'insieme di endpoint viene determinato quando ci si connette accesso al proprio account.  API diversi richiedono diverse versioni dell'opzione ambiente:

Tipo di connessione | Comando
---|----
Comandi di [Gestione dei servizi](https://msdn.microsoft.com/library/dn708504.aspx) | `Add-AzureAccount -Environment AzureUSGovernment`
Comandi di [Gestione delle risorse](https://msdn.microsoft.com/library/mt125356.aspx) | `Add-AzureRmAccount -EnvironmentName AzureUSGovernment`
Comandi di [Azure Active Directory](https://msdn.microsoft.com/library/azure/jj151815.aspx) | `Connect-MsolService -AzureEnvironment UsGovernment`
[Azure Active Directory comando v2](https://msdn.microsoft.com/library/azure/mt757189.aspx) | `Connect-AzureAD -AzureEnvironmentName AzureUSGovernment`

È possibile anche utilizzare l'opzione di ambiente quando ci si connette a un account di archiviazione utilizzando nuovo AzureStorageContext e specificare AzureUSGovernment.

### <a name="determining-region"></a>Determinare l'area geografica

Una volta connessi, c'è una differenza aggiuntiva: aree utilizzate per un servizio di destinazione.  Ogni cloud Azure sono diverse aree geografiche.  È possibile vederle elencati nella pagina di disponibilità dei servizi.  Si utilizza generalmente l'area nel parametro posizione per un comando.

C'è un problema.  Le aree di Azure Government necessario formattato in modo diverso rispetto a loro nomi comuni:

Nome comune | Comando
---|----
Stati Uniti Gov Virginia | USGov Virginia
Stati Uniti Gov Iowa | USGov Iowa

>[AZURE.NOTE] È non presente alcun spazio tra gli Stati Uniti e Gov quando si utilizza il parametro del percorso.

Se si desidera convalidare le aree disponibili per la pubblica amministrazione Azure, è possibile eseguire i comandi seguenti e stampare un elenco di corrente:

    Get-AzureLocation

Se si desidera conoscere gli ambienti disponibili tra Azure, è possibile eseguire:

    Get-AzureEnvironment

## <a name="next-steps"></a>Passaggi successivi

Se si sta cercando ulteriori informazioni, è possibile estrarre:

+ [Documenti di PowerShell su GitHub](https://github.com/Azure/azure-powershell)
+ [Istruzioni dettagliate sulla connessione per la gestione delle risorse](https://blogs.msdn.microsoft.com/azuregov/2015/10/08/configuring-arm-on-azure-gc/)
+ [Documenti di PowerShell Azure su MSDN](https://msdn.microsoft.com/library/mt619274.aspx)

Per ulteriori informazioni e aggiornamenti sottoscrivere [Microsoft Azure Government Blog] (https://blogs.msdn.microsoft.com/azuregov/)
