<properties
   pageTitle="Pubblicare le applicazioni ai singoli utenti in un insieme di Azure RemoteApp (Preview) | Microsoft Azure"
   description="Informazioni su come è possibile pubblicare App per singoli utenti, invece che a seconda di gruppi in Azure RemoteApp."
   services="remoteapp-preview"
   documentationCenter=""
   authors="piotrci"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="remoteapp"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="compute"
   ms.date="08/15/2016"
   ms.author="piotrci"/>

# <a name="publish-applications-to-individual-users-in-an-azure-remoteapp-collection-preview"></a>Pubblicare le applicazioni ai singoli utenti in un insieme di Azure RemoteApp (Preview)

> [AZURE.IMPORTANT]
> Più RemoteApp Azure utilizzato. Leggere l' [annuncio](https://go.microsoft.com/fwlink/?linkid=821148) per informazioni dettagliate.

In questo articolo viene spiegato come pubblicare le applicazioni ai singoli utenti in un insieme di Azure RemoteApp. Si tratta di nuove funzionalità di Azure RemoteApp, attualmente in "preview privato" e disponibile solo per la selezione adottato per la valutazione.

Azure RemoteApp abilitati in origine solo un modo per applicazioni "pubblicazione": l'amministratore pubblicare App dall'immagine e saranno visibili a tutti gli utenti della raccolta.

Uno scenario comune consiste nel includere molte applicazioni in una singola immagine e distribuire un insieme per ridurre i costi di gestione. Spesso non tutte le applicazioni sono i contenuti pertinenti per tutti gli utenti, ovvero gli amministratori preferisce pubblicare App per singoli utenti in modo che non vedono le applicazioni non necessarie nell'applicazione feed.

Questo è ora possibile in Azure RemoteApp – attualmente come una caratteristica di anteprima limitato. Ecco un breve riepilogo delle nuove funzionalità:

1. È possibile impostare una raccolta in due modi:
 
  - originale "insieme modalità", in cui è possono visualizzare tutti gli utenti in una raccolta tutte pubblicati applicazioni. Questa è la modalità predefinita.
  - la nuova "applicazione modalità", in cui gli utenti vedono solo le applicazioni di cui sono stata assegnata in modo esplicito

2. Al momento la modalità di applicazione può essere attivata solo uso dei cmdlet di RemoteApp PowerShell Azure.

  - Se è impostata sulla modalità di applicazione, assegnazione utente nella raccolta non può essere gestita tramite il portale di Azure. Assegnazione utente deve essere gestita mediante i cmdlet di PowerShell.

3. Gli utenti vedranno solo le applicazioni pubblicate direttamente alla loro. Tuttavia, potrebbe essere comunque possibile che un utente avviare le applicazioni disponibili nell'immagine accedendovi direttamente nel sistema operativo.
  - Questa caratteristica non è disponibile un blocco di protezione delle applicazioni. Limita solo visibilità nell'applicazione di feed.
  - Se è necessario isolare agli utenti di applicazioni, sarà necessario utilizzare raccolte separate per tale.

## <a name="how-to-get-azure-remoteapp-powershell-cmdlets"></a>Come ottenere i cmdlet di RemoteApp PowerShell Azure

Per provare la nuova funzionalità di anteprima, sarà necessario utilizzare i cmdlet di PowerShell Azure. Attualmente non è possibile utilizzare il portale di gestione di Azure per attivare la modalità di pubblicazione applicazione nuova.

Verificare prima di tutto, verificare di che avere installato il [modulo di Azure PowerShell](../powershell-install-configure.md) .

Quindi avviare la console di PowerShell in modalità di amministratore ed eseguire il cmdlet seguente:

        Add-AzureAccount

Verrà chiesto il nome utente Azure e la password. Dopo aver effettuato l'accesso, sarà possibile eseguire i cmdlet di Azure RemoteApp le proprie sottoscrizioni Azure.

## <a name="how-to-check-which-mode-a-collection-is-in"></a>Come si verifica la modalità di una raccolta

Eseguire il cmdlet seguente:

        Get-AzureRemoteAppCollection <collectionName>

![Controllare la modalità di raccolta](./media/remoteapp-perapp/araacllelvel.png)

La proprietà AclLevel può avere i valori seguenti:

- Raccolta: pubblicazione modalità originale. Tutti gli utenti vedranno che tutte pubblicati app.
- Applicazione: la nuova pubblicazione modalità. Gli utenti vedono solo le app pubblicate direttamente alla loro.

## <a name="how-to-switch-to-application-publishing-mode"></a>Come passare alla modalità di pubblicazione delle applicazioni

Eseguire il cmdlet seguente:

        Set-AzureRemoteAppCollection -CollectionName -AclLevel Application

Stato pubblicazione applicazione verrà mantenuto: inizialmente tutti gli utenti vedranno tutte le app pubblicate originale.

## <a name="how-to-list-users-who-can-see-a-specific-application"></a>Come visualizzare un elenco di utenti che possono vedere un'applicazione specifica

Eseguire il cmdlet seguente:

        Get-AzureRemoteAppUser -CollectionName <collectionName> -Alias <appAlias>

Elenca tutti gli utenti possono vedere dell'applicazione.

Nota: È possibile visualizzare gli alias dell'applicazione (denominati "alias app" nella sintassi sopra riportata) eseguendo AzureRemoteAppProgram Get - NomeInsieme <collectionName>.

## <a name="how-to-assign-an-application-to-a-user"></a>Come assegnare un'applicazione di un utente

Eseguire il cmdlet seguente:

        Add-AzureRemoteAppUser -CollectionName <collectionName> -UserUpn <user@domain.com> -Type <OrgId|MicrosoftAccount> -Alias <appAlias>

L'utente verrà visualizzato l'applicazione nel client RemoteApp di Azure e sarà in grado di connettersi.

## <a name="how-to-remove-an-application-from-a-user"></a>Come rimuovere un'applicazione di un utente

Eseguire il cmdlet seguente:

        Remove-AzureRemoteAppUser -CollectionName <collectionName> -UserUpn <user@domain.com> -Type <OrgId|MicrosoftAccount> -Alias <appAlias>

## <a name="providing-feedback"></a>Fornire commenti e suggerimenti
Grazie per i commenti e suggerimenti su questa caratteristica di anteprima. Compilare il [sondaggio](http://www.instant.ly/s/FDdrb) per far sapere a cosa si ritiene che.

## <a name="havent-had-a-chance-to-try-the-preview-feature"></a>Non hanno avuto la possibilità di provare la caratteristica Anteprima?
Se l'utente non ha partecipato nel riquadro di anteprima ancora, utilizzare il [sondaggio](http://www.instant.ly/s/AY83p) per richiedere l'accesso.
