
<properties
    pageTitle="Proteggere le applicazioni e risorse in Azure RemoteApp | Microsoft Azure"
    description="Informazioni su come bloccare le applicazioni e risorse in RemoteApp di Azure"
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="elizapo" />



# <a name="secure-apps-and-resources-in-azure-remoteapp"></a>Proteggere le applicazioni e risorse in RemoteApp di Azure

> [AZURE.IMPORTANT]
> Più RemoteApp Azure utilizzato. Leggere l' [annuncio](https://go.microsoft.com/fwlink/?linkid=821148) per informazioni dettagliate.

RemoteApp Azure consente agli utenti l'accesso alle app di Windows gestiti centralmente, che consente di controllare i gli utenti che possono e non consentite.  Questa operazione è particolarmente utile quando l'utente si connette da un dispositivo non gestito (ad esempio loro Macbook personali) e si desidera controllare l'accesso degli utenti o esperienza utente.

Ad esempio, se si usa Active Directory per l'autenticazione utente e si vuole impedire agli utenti di copiare dati da un'app, è possibile configurare un criterio di gruppo Desktop remoto per impedire agli utenti di copiare i dati.

Un altro esempio è se si desidera bloccare l'accesso a internet per una determinata app nella raccolta. È possibile creare una regola di Windows Firewall blocca l'accesso quando si crea l'immagine per la raccolta.

## <a name="implementation-options"></a>Opzioni di implementazione

  Ecco le opzioni di implementazione della chiave, che possono essere utilizzate singolarmente o in parallelo, in base alle esigenze:

1.  Se l'insieme di RemoteApp dominio è possibile applicare i [Criteri di gruppo](https://technet.microsoft.com/library/cc725828.aspx) (ad eccezione del Idle e disconnessione timeout criteri descritti [di seguito](../azure-subscription-service-limits.md)).
2.  In alternativa ai criteri di gruppo (se la raccolta non è dominio oppure non si dispone di privilegi destro in Active Directory), è possibile configurare [Criteri locali](https://technet.microsoft.com/library/cc775702.aspx) nell'immagine del modello.  Nota che il gruppo criteri criteri locali sovrascrivono quando si verifica un conflitto.
3.  Alcune impostazioni del sistema operativo/app non possono essere configurati tramite criterio, ma possono essere tramite chiave del Registro di sistema utilizzando lo [strumento RegEdit](./remoteapp-hybridtrouble.md) durante la configurazione l'immagine del modello.
4.  È possibile utilizzare [Windows Firewall](http://windows.microsoft.com/en-US/windows-8/Windows-Firewall-from-start-to-finish) per controllare l'accesso alla rete da e verso il computer in cui viene eseguita l'app. Assicurarsi che non bloccare le porte definite in questa posizione e l'URL.
5.  È possibile utilizzare [AppLocker](https://technet.microsoft.com/library/hh831440.aspx) per controllare i supporti l'esecuzione di applicazioni e gli utenti di file. Ad esempio utenti esperti possono scoprire come eseguire applicazioni che non pubblicate ma che sono disponibili nell'immagine utilizzato per creare la raccolta, AppLocker possibile bloccare il.

## <a name="detailed-information"></a>Informazioni dettagliate

- I seguenti criteri di servizi desktop remoto sono potrebbero essere utili:
    - [Dispositivo e risorse](https://technet.microsoft.com/library/ee791794.aspx)
    - [Reindirizzamento della stampante](https://technet.microsoft.com/library/ee791784.aspx)
    - [I profili](https://technet.microsoft.com/library/ee791865.aspx).
- Si noti che la configurazione reindirizzamenti tramite RemoteApp PowerShell modulo (come visto [qui](./remoteapp-redirection.md)) si basa sul computer client per applicare i criteri, in modo che l'obiettivo principale è la sicurezza è consigliabile applicare i criteri tramite il criterio locale immagine di modello o criteri di gruppo.
- [Criteri di Windows Server 2012 R2](https://technet.microsoft.com/library/hh831791.aspx).
- [Criteri di Office 2013](https://technet.microsoft.com/library/cc178969.aspx) (inclusa [la modalità personalizzare la barra degli strumenti di Office](https://technet.microsoft.com/library/cc179143.aspx)).
