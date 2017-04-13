<properties
    pageTitle="Cosa contiene le immagini dei modelli RemoteApp di Azure? | Microsoft Azure"
    description="Informazioni sulle immagini dei modelli incluse in Azure RemoteApp."
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/15/2016"
    ms.author="elizapo" />

# <a name="what-is-in-the-azure-remoteapp-template-images"></a>Cosa contiene le immagini dei modelli RemoteApp di Azure?

> [AZURE.IMPORTANT]
> Più RemoteApp Azure utilizzato. Leggere l' [annuncio](https://go.microsoft.com/fwlink/?linkid=821148) per informazioni dettagliate.

L'abbonamento di Azure RemoteApp include tre le immagini dei modelli:


- Windows Server 2012
- Microsoft Office 365 ProPlus (abbonamento a Office 365 obbligatorio)
- Microsoft Office 2013 Professional Plus (versione di valutazione solo)

> [AZURE.IMPORTANT]L'abbonamento RemoteApp di Azure consente di accedere al programma di immagini, ad eccezione di Office 365 ProPlus, che richiede una sottoscrizione separata e Office 2013, non può essere utilizzato in fase di produzione. Ciò significa che è possibile condividere i programmi o applicazioni per le immagini dei modelli con altri utenti. Ad esempio, se si crea una raccolta che utilizza l'immagine di Windows Server 2012 R2, è possibile pubblicare System Center Endpoint Protection per gli utenti di accedere tramite RemoteApp.
>
> Controllare i [dettagli di gestione delle licenze RemoteApp](remoteapp-licensing.md) per ulteriori informazioni. E [Usare Office con Azure RemoteApp](remoteapp-o365.md) per l'ufficio licenze info.

Continuare a leggere per informazioni dettagliate su ogni immagine contiene.

## <a name="windows-server-2012-r2--the-vanilla-image"></a>Windows Server 2012 R2 ("il vaniglia immagine")
Questa immagine è basata sul sistema operativo Data Center R2 di Microsoft Windows Server 2012 e presenta ruoli e le funzionalità per soddisfare i requisiti per le immagini dei modelli Azure RemoteApp seguenti:


- .NET framework 4.5, 3.5.1, 3.5
- Esperienza desktop
- Input penna e grafia servizi
- Media Foundation
- Host sessione Desktop remoto
- Windows PowerShell 4.0
- Windows PowerShell ISE
- Supporto per WoW64

In questa figura è installato anche installate le applicazioni seguenti:

- Adobe Flash Player
- Microsoft Silverlight
- Microsoft System Center 2012 Endpoint Protection
- Microsoft Windows Media Player


## <a name="microsoft-office-365-proplus-subscription-required"></a>Microsoft Office 365 ProPlus (è necessaria la sottoscrizione)
Office 365 è l'applicazione più richiesto, abbiamo creato un'immagine "personalizzata" per l'utilizzo.

Questa immagine è un'estensione dell'immagine vaniglia e è i seguenti componenti di Microsoft Office 365 ProPlus installato oltre ai componenti descritti nell'immagine Windows Server 2012 R2:


- Accesso
- Excel
- Lync
- OneNote
- OneDrive for Business (si noti che l'agente di sincronizzazione non è supportata per l'utilizzo con RemoteApp di Azure)
- Outlook
- PowerPoint
- Word
- Strumenti di correzione di Microsoft Office

L'immagine include anche Pro di Visio e Project Pro.

E anche le applicazioni seguenti:

- SQL Native client
- Driver ODBC
- Client Data Mining di SQL Server
- Client MasterDataServices
- Microsoft Publisher
- PowerQuery
- Power Map


Tutte le funzionalità delle applicazioni di Office 365 ProPlus sono disponibile solo per gli utenti che dispongono di un piano di Office 365 ProPlus. Per ulteriori informazioni su Office 365 piani di abbonamento a vedere [piani di servizio di Office 365](http://technet.microsoft.com/library/office-365-plan-options.aspx). Hai altre domande? Vedere le informazioni di [Office 365 + RemoteApp](remoteapp-o365.md) . Consultare anche l'articolo nuovo [come usare l'abbonamento a Office 365 con RemoteApp di Azure](remoteapp-officesubscription.md).

Si noti che è necessario licenza di Office 365 ProPlus, Visio Pro e Project Pro separatamente, hanno la propria licenza.

## <a name="microsoft-office-2013-professional-plus-trial-only"></a>Microsoft Office 2013 Professional Plus (versione di valutazione solo)
Durante il periodo di valutazione gratuito, è possibile testare il servizio con l'immagine di Office 2013.

Questa immagine è un'estensione dell'immagine vaniglia e è i seguenti componenti di Microsoft Office 2013 Professional Plus installato oltre ai componenti descritti nell'immagine Windows Server 2012 R2:


- Accesso
- Excel
- Lync
- OneNote
- OneDrive for Business (si noti che l'agente di sincronizzazione non è supportata per l'utilizzo con RemoteApp di Azure)
- Outlook
- PowerPoint
- Progetto
- Visio
- Word
- Strumenti di correzione di Microsoft Office

> [AZURE.IMPORTANT]**Informazioni legali:** Questa immagine non include una licenza di Microsoft Office e *non può essere usata per produzione*. L'immagine è destinato solo uso versione di valutazione di Office 2013 Professional Plus. Se si desidera usare le app di Office in Azure RemoteApp per produzione, è necessario utilizzare l'immagine di Office 365 ProPlus. Per ulteriori informazioni sulla gestione delle licenze di Office, vedere [uso di Office 365 con RemoteApp di Azure](remoteapp-o365.md)
