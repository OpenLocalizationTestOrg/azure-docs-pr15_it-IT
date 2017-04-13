<properties 
    pageTitle="Risoluzione dei problemi RemoteApp Azure - errori di connessione e avvio dell'applicazione | Microsoft Azure" 
    description="Informazioni su come risolvere i problemi di avvio e la connessione alle applicazioni di Azure RemoteApp." 
    services="remoteapp" 
    documentationCenter="" 
    authors="ericorman" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="elizapo" />



#<a name="troubleshoot-azure-remoteapp---application-launch-and-connection-failures"></a>Risolvere i problemi di Azure RemoteApp - errori di connessione e avvio dell'applicazione 

> [AZURE.IMPORTANT]
> Più RemoteApp Azure utilizzato. Leggere l' [annuncio](https://go.microsoft.com/fwlink/?linkid=821148) per informazioni dettagliate.

Le applicazioni ospitate in Azure RemoteApp possono avere esito negativo avviare per diversi motivi. In questo articolo vengono illustrati i diversi motivi e messaggi di errore gli utenti potrebbero essere visualizzati quando si tenta di avviare le applicazioni. Parla anche gli errori delle connessioni. (Ma non è in questo articolo descrive problemi quando si accede al client RemoteApp di Azure).  

Continuare a leggere per informazioni su messaggi di errore comuni problemi relativi all'avvio e una connessione di app.

##<a name="were-getting-you-set-up-try-again-in-10-minutes"></a>Si ottiene configurazione di... Riprovare 10 minuti.

Questo errore indica che Azure RemoteApp è scalabilità per soddisfare le capacità necessarie degli utenti. In background vengono create più istanze di macchine Virtuali di Azure RemoteApp per gestire le esigenze di capacità degli utenti. In genere questo necessari circa 5 minuti ma può richiedere fino a 10 minuti. In alcuni casi, questo non accade abbastanza rapidamente e le risorse sono necessari immediatamente. Ad esempio 9: 00 scenario in cui è necessario usare l'app in Azure RemoteAppn allo stesso tempo molti utenti. In tal caso è possibile attivare la **modalità capacità** back-end. Per aprire un ticket di supporto di Azure e o inviare tramite posta elettronica al [remoteappforum@microsoft.com](mailto:remoteappforum@microsoft.com). Assicurarsi di includere il proprio ID sottoscrizione nella richiesta.  

![È in corso Introduzione configurare](./media/remoteapp-apptrouble/ra-apptrouble1.png)

## <a name="could-not-auto-reconnect-to-your-applications-please-re-launch-your-application"></a>Impossibile non riconnessione automatica per le applicazioni, eseguire di nuovo l'applicazione  

Questo messaggio di errore viene visualizzato spesso se vi accedessero RemoteApp di Azure e quindi si inserisce in sospensione più di 4 ore il PC e quindi si è attivato il PC e il client di Azure RemoteApp tentare di auto riconnettere e timeout superato.  Chiedere agli utenti di accedere all'applicazione e tenta di aprire dal client di Azure RemoteApp.

![Impossibile non riconnessione automatica per le applicazioni](./media/remoteapp-apptrouble/ra-apptrouble2.png) 

## <a name="problems-with-the-temp-profile"></a>Problemi con il profilo temp 

Questo errore si verifica quando il profilo utente (su disco del profilo utente) non è riuscito a installare e l'utente riceve un profilo temporaneo.  Gli amministratori devono passare alla raccolta nel portale di Azure e quindi fare clic sulla scheda **sessioni** e tentativo **Disconnetti** l'utente. Questa operazione verrà forzare un file di log completo di fuori del sessione utente - quindi che l'utente tenta di avviare un'app di nuovo. Se il problema persiste contattare il supporto di Azure e o inviare tramite posta elettronica al [remoteappforum@microsoft.com](mailto:remoteappforum@microsoft.com).

## <a name="azure-remoteapp-has-stopped-working"></a>Azure RemoteApp ha smesso di funzionare

Questo messaggio di errore indica che il client di Azure RemoteApp è verificato un errore e deve essere riavviato. Chiedere agli utenti di chiudere: selezionare **Chiudi programma** e quindi avviare di nuovo il client di Azure RemoteApp.  Se il problema persiste Apri e ticket di supporto di Azure e o inviare tramite posta elettronica al [remoteappforum@microsoft.com](mailto:remoteappforum@microsoft.com).

![Azure RemoteApp ha smesso di funzionare](./media/remoteapp-apptrouble/ra-apptrouble3.png)  

## <a name="an-error-occurred-while-remote-desktop-connection-was-accessing-this-resource-retry-the-connection-or-contact-your-system-administrator"></a>Errore durante la connessione Desktop remoto è stata accesso a questa risorsa. Provare la connessione o contattare l'amministratore di sistema

Si tratta di un messaggio di errore generico - contattare il supporto di Azure e o inviare tramite posta elettronica al [remoteappforum@microsoft.com](mailto:remoteappforum@microsoft.com) in modo sarà possibile esaminare. 

![Messaggio di Azure RemoteApp generico](./media/remoteapp-apptrouble/ra-apptrouble4.png) 