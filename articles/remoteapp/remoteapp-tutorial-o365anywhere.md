<properties
   pageTitle="Ottenere la stessa esperienza di Office 365 in qualsiasi dispositivo con RemoteApp di Azure | Microsoft Azure"
   description="Informazioni su come condividere qualsiasi applicazione di Office 365 con altri utenti tramite RemoteApp di Azure."
   services="remoteapp"
   documentationCenter=""
   authors="guscatalano"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="remoteapp"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="compute"
   ms.date="08/15/2016"
   ms.author="guscatal;elizapo"/>


# <a name="get-the-same-office-365-experience-on-any-device-with-azure-remoteapp"></a>Ottenere la stessa esperienza di Office 365 in qualsiasi dispositivo con RemoteApp di Azure

> [AZURE.IMPORTANT]
> Più RemoteApp Azure utilizzato. Leggere l' [annuncio](https://go.microsoft.com/fwlink/?linkid=821148) per informazioni dettagliate.

In questo articolo verrà illustrate le caratteristiche come distribuire Office 365 in qualsiasi dispositivo della società. Gli utenti possono ottenere le stesse funzionalità e interfaccia utente Android, Apple e Windows.

Si verrà eseguire questa operazione utilizzando Azure RemoteApp eseguendo l'hosting di Office 365 su scala grado macchine virtuali in Azure che gli utenti possono connettersi. Il valore impostato macchine virtuali si chiama "insieme di cloud".

## <a name="create-a-cloud-collection"></a>Creare una raccolta di cloud

Prima di tutto dopo aver creato un account Azure, passare a **RemoteApp** facendo clic sul collegamento sul lato sinistro.
![Con RemoteApp Azure nel portale di Azure](./media/remoteapp-tutorial-o365anywhere/1-menu.png)

Quindi continuare facendo clic su **Nuovo** nella parte inferiore e "creazione veloce" una raccolta. Specificare un nome, l'area geografica, l'abbonamento, il piano e l'immagine "Proffesional Office 2013" in cui sono disponibili.
![Finestra di dialogo Crea](./media/remoteapp-tutorial-o365anywhere/2-quickcreate.png)

Una volta completata la maschera che deve iniziare il processo di creazione della raccolta. Questo potrebbe richiedere un'ora o meno.

![In attesa](./media/remoteapp-tutorial-o365anywhere/3-waiting.png)

Una volta completata la procedura, appaia simile al seguente. Se si fa clic su **pubblicazione** è possibile vedere che la maggior parte delle applicazioni di Office sono state pubblicate per noi già.
![Insieme creato](./media/remoteapp-tutorial-o365anywhere/4-done.png)

![App pubblicati](./media/remoteapp-tutorial-o365anywhere/5-publish.png)

A questo punto è possibile aggiungere anche altri utenti che hanno accesso alla raccolta, fare clic su **Accesso degli utenti**.
![Configurare l'accesso degli utenti](./media/remoteapp-tutorial-o365anywhere/6-user.png)

A questo punto si provare la connessione a Office 365!

## <a name="connect-to-office-365"></a>Connettersi a Office 365

Si verrà visitare su anche [https://www.remoteapp.windowsazure.com/](https://www.remoteapp.windowsazure.com/), scorrere verso il basso e fare clic su **Scarica client** per installare il client di Azure RemoteApp sul dispositivo che ci si trova nella. Screenshot seguente disponibili per Windows.

Dopo l'avvio dell'applicazione verrà chiesto di accedere con l'account Microsoft (noto in precedenza come un "Live ID"), utilizzare lo stesso come account Azure per ora. Dopo avere effettuato in deve visualizzata una notifica che informa sugli inviti nuovi, fare clic su sono e verrà visualizzato un elenco come riportata di seguito. Accettare l'invito che corrisponde alla posta elettronica proprietario dell'account Azure.

![Nuovo invito](./media/remoteapp-tutorial-o365anywhere/7-araclient.png)

L'aspetto quando sono presenti nuovi inviti.

![Accettare una domanda](./media/remoteapp-tutorial-o365anywhere/8-invitation.png)

Una volta accettare l'invito verrà visualizzato tutte le applicazioni di Office nel client di Azure RemoteApp.

![Elenco delle App](./media/remoteapp-tutorial-o365anywhere/9-work.png)

Quando si fa clic su una delle operazioni che dell'applicazione dovrebbe iniziare la macchina virtuale Azure e si dovrebbe essere tutte impostate! Buona!

![avvio](./media/remoteapp-tutorial-o365anywhere/10-arastart.png)

![PowerPoint](./media/remoteapp-tutorial-o365anywhere/11-pp.png)
