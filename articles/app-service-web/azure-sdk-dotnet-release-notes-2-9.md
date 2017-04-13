<properties 
   pageTitle="Note sulla versione di Azure SDK per .NET 2.9" 
   description="Note sulla versione di Azure SDK per .NET 2.9" 
   services="app-service\web" 
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

# <a name="azure-sdk-for-net-29-release-notes"></a>Note sulla versione di Azure SDK per .NET 2.9

##<a name="overview"></a>Panoramica

Questo documento contiene le note sulla versione di Azure SDK versione 2.9 .NET. 

Per informazioni dettagliate sugli aggiornamenti in questa versione, vedere l' [annuncio di Azure SDK 2.9 post](https://azure.microsoft.com/blog/announcing-visual-studio-azure-tools-and-sdk-2-9/).

## <a name="azure-sdk-29-for-visual-studio-2015-update-2-and-visual-studio-15-preview"></a>Visualizzare l'anteprima di Azure SDK 2.9 per Visual Studio 2015 Update 2 e Visual Studio "15"
 
Questo aggiornamento include le correzioni di bug seguenti:

- Problema relativo al resto API Client generazione in apparirà come il nome della cartella di generazione di codice e/o il nome dello spazio dei nomi in cui la stringa "Tipo sconosciuto" eliminati nel codice generato.
- Problema relativo alla WebJobs pianificato in cui le informazioni di autenticazione non era possibile passare al processo di provisioning l'utilità di pianificazione.

Questo aggiornamento include la nuova funzionalità seguenti:

- Supporto per i servizi di App secondario nella scheda "Servizi" della finestra di dialogo provisioning del servizio di App. 

##<a name="azure-data-lake-tools-for-visual-studio-2015-update-2"></a>Dati di Azure Lake Tools per Visual Studio 2015 Update 2
 
Gli aggiornamenti sono incluse le operazioni seguenti:

- **Azure Data Lake Tools** per Visual Studio viene quindi unita Azure SDK versione .NET. Lo strumento viene installato automaticamente durante l'installazione di Azure SDK. 

    Lo strumento viene aggiornato frequentemente, passare [qui](http://aka.ms/datalaketool) per ottenere gli aggiornamenti.

- **Esplora server** ora consente di visualizzare tutti e creare alcune entità di metadati U-SQL. Per ulteriori informazioni, vedere [questo](https://azure.microsoft.com/documentation/services/data-lake-analytics/) blog.


##<a name="hdinsight-tools"></a>Strumenti HDInsight 

**HDInsight Tools** per Visual Studio supporta ora HDInsight versione 3.3, inclusi con Tez grafici e le correzioni altra lingua.


##<a name="azure-resource-manager"></a>Gestione risorse di Azure 

In questa versione aggiunge il supporto di [KeyVault](../resource-manager-keyvault-parameter.md) per i modelli di ARM.

##<a name="see-also"></a>Vedere anche

[Post di annuncio SDK 2.9 Azure](https://azure.microsoft.com/blog/announcing-visual-studio-azure-tools-and-sdk-2-9/)
