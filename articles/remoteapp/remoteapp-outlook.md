<properties
    pageTitle="Utilizzo di Outlook in Azure RemoteApp | Microsoft Azure" 
    description="Informazioni su come configurare e utilizzare Outlook in Azure RemoteApp | Microsoft Azure"
    services="remoteapp"
    documentationCenter=""
    authors="pavithir"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="08/15/2016"
    ms.author="elizapo" />

# <a name="using-microsoft-outlook-in-azure-remoteapp"></a>Utilizzo di Microsoft Outlook in RemoteApp Azure

> [AZURE.IMPORTANT]
> Più RemoteApp Azure utilizzato. Leggere l' [annuncio](https://go.microsoft.com/fwlink/?linkid=821148) per informazioni dettagliate.

Azure RemoteApp supporta Office 365 di Microsoft Outlook. Altre informazioni sui come [Office funziona in Azure RemoteApp](remoteapp-officesubscription.md). Esistono alcune impostazioni consigliate per Outlook se usata in Azure RemoteApp.

## <a name="cached-mode"></a>Modalità cache
Modalità cache è una configurazione consigliata quando si utilizza Outlook in Azure RemoteApp. Quando si configura un account di Outlook 2013 per l'utilizzo della modalità cache, Outlook 2013 funziona da una copia locale della cassetta postale di Microsoft Exchange dell'utente memorizzati in un file di dati non in linea (file con estensione ost) nel computer dell'utente, insieme alla Offline indirizzo Rubrica (con estensione OAB). La cassetta postale memorizzati nella cache e della Rubrica fuori rete vengono aggiornati periodicamente dal servizio di Office 365. Altre informazioni sulle [differenze tra la modalità cache e online](https://technet.microsoft.com/library/jj683103.aspx).

L'utente può selezionare **La modalità cache** o **La modalità Online** durante l'installazione di account o modificando le impostazioni dell'account. Utilizzando lo strumento di personalizzazione di Office (OCT) o criteri di gruppo, è possibile distribuire una modalità o l'altro.  

Leggere [le istruzioni dettagliate su come abilitare la modalità cache](https://technet.microsoft.com/library/c6f4cad9-c918-420e-bab3-8b49e1885034#proc).

## <a name="search"></a>Ricerca
In Azure RemoteApp, la ricerca all'interno di Outlook con presenta limitazioni. Azure RemoteApp utilizza macchine virtuali in pool per consentire l'esecuzione sessioni utente. Dall'indicizzazione della ricerca dipende l'ID di computer, è diversa per macchine virtuali diverse. È possibile che ogni volta che un utente accede a RemoteApp di Azure, viene richiesto di una nuova macchina virtuale. Ciò implica, se si abilita ricerca locale, l'indicizzatore verrà eseguita ogni volta che l'ID di computer cambia (quando l'utente si trova in una macchina virtuale diversa). A seconda delle dimensioni del. Con estensione ost, l'indicizzatore potrebbe richiedere molto tempo per il completamento e usare le risorse necessarie per altre applicazioni. Ricerca non solo è lenta, ma potrebbe non produrre risultati. Utilizzando un profilo di account in modalità Online da risolvere il problema, ma le prestazioni complessive danno a causa dell'assenza di una cache locale (fare clic sul collegamento sopra per ulteriori informazioni sulla differenza tra la modalità cache e online). Purtroppo non è possibile disattivare ricerca indicizzato/locale e ricerca online non è attivata per impostazione predefinita in Outlook 2013.
