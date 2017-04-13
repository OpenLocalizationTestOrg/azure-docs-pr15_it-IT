<properties
    pageTitle="Usare il reindirizzamento in Azure RemoteApp | Microsoft Azure"
    description="Informazioni su come configurare e usare il reindirizzamento in RemoteApp"
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

# <a name="using-redirection-in-azure-remoteapp"></a>Usare il reindirizzamento in RemoteApp di Azure

> [AZURE.IMPORTANT]
> Più RemoteApp Azure utilizzato. Leggere l' [annuncio](https://go.microsoft.com/fwlink/?linkid=821148) per informazioni dettagliate.

Il reindirizzamento del dispositivo consente agli utenti di interagire con le applicazioni remote con i dispositivi collegati al loro computer locale, un telefono o tablet. Ad esempio, se Skype tramite RemoteApp di Azure, l'utente deve fotocamera installata nel PC per l'uso con Skype. Questo vale anche per le stampanti, gli altoparlanti, monitor e periferiche un intervallo di USB connessi.

RemoteApp utilizza il protocollo RDP (Remote Desktop) e RemoteFX per il reindirizzamento.

## <a name="what-redirection-is-enabled-by-default"></a>Quali il reindirizzamento è attivato per impostazione predefinita?
Quando si usa RemoteApp, reindirizzamenti seguenti vengono attivati per impostazione predefinita. Le informazioni tra parentesi Mostra l'impostazione RDP.

- Riprodurre i suoni del computer locale (**riprodurre su questo computer**). (audiomode:i:0)
- Acquisire audio dal computer locale e inviare al computer remoto (**Record dal computer in uso**). (audiocapturemode:i:1)
- Stampare su stampanti locali (redirectprinters:i:1)
- Porte COM (redirectcomports:i:1)
- Dispositivo di smart card (redirectsmartcards:i:1)
- Appunti (possibilità di copiare e incollare) (redirectclipboard:i:1)
- Deselezionare tipo smussatura (Consenti smussatura: i:1)
- Reindirizzare i dispositivi Plug and Play tutti supportati. (devicestoredirect:s: *)

## <a name="what-other-redirection-is-available"></a>Quali altre reindirizzamento è disponibile?
Due opzioni di reindirizzamento sono disabilitate per impostazione predefinita:

- Reindirizzamento unità (unità): unità del computer locale diventano mappate nella sessione remota. In questo modo si salva o file aperti da unità locali mentre si lavora nella sessione remota.
- Reindirizzamento USB: È possibile utilizzare i dispositivi USB collegati al computer locale all'interno della sessione remota.

## <a name="change-your-redirection-settings-in-remoteapp"></a>Modificare le impostazioni di reindirizzamento in RemoteApp
È possibile modificare le impostazioni di reindirizzamento dispositivo per una raccolta utilizzando Microsoft Azure PowerShell con SDK. Dopo avere installato la nuova PowerShell SDK, configurarlo prima di tutto per gestire l'abbonamento, come descritto in [come installare e configurare Azure PowerShell](../powershell-install-configure.md).

Per impostare le proprietà personalizzate RDP quindi utilizzare un comando simile al seguente:

    Set-AzureRemoteAppCollection -CollectionName <collection name>  -CustomRdpProperty "drivestoredirect:s:*`nusbdevicestoredirect:s:*"

Si noti che *' n* viene utilizzato come delimitatore tra le singole proprietà.

Per ottenere un elenco di quali proprietà RDP personalizzate sono configurati, eseguire il cmdlet seguente. Si noti che le proprietà personalizzate solo vengono visualizzate come output risultati e non le proprietà predefinite:  

    Get-AzureRemoteAppCollection -CollectionName <collection name>

Quando si impostano le proprietà personalizzate è necessario specificare tutte le proprietà personalizzate ogni volta. in caso contrario l'impostazione viene ripristinata come disabilitato.   

### <a name="common-examples"></a>Esempi comuni
Utilizzare il cmdlet seguente per attivare il reindirizzamento dell'unità:  

    Set-AzureRemoteAppCollection -CollectionName <collection name>  -CustomRdpProperty "drivestoredirect:s:*”

Utilizzare questo cmdlet per attivare USB e unità reindirizzamento:

    Set-AzureRemoteAppCollection -CollectionName <collection name>  -CustomRdpProperty "drivestoredirect:s:*`nusbdevicestoredirect:s:*"

Utilizzare questo cmdlet per disabilitare la condivisione Appunti:  

    Set-AzureRemoteAppCollection -CollectionName <collection name>  -CustomRdpProperty "redirectclipboard:i:0”

> [AZURE.IMPORTANT] Assicurarsi di disconnettersi completamente tutti gli utenti della raccolta (e non solo disconnessi) prima di testare la modifica. Per assicurarsi che gli utenti siano completamente disconnessi, fare clic sulla scheda **sessioni** nella raccolta nel portale di Azure e disconnettersi da tutti gli utenti disconnessa o effettuato l'accesso. In alcuni casi è possibile richiedere alcuni secondi per le unità locale per mostrare in Esplora risorse all'interno della sessione.

## <a name="change-usb-redirection-settings-on-your-windows-client"></a>Modificare le impostazioni di reindirizzamento USB nel client di Windows

Se si desidera utilizzare il reindirizzamento USB in un computer che si connette a RemoteApp, esistono 2 azioni che devono essere eseguite. 1 - l'amministratore abbia abilitato il reindirizzamento USB a livello di raccolta tramite PowerShell Azure. 2 - su ogni dispositivo in cui si desidera utilizzare il reindirizzamento USB, è necessario attivare un criterio di gruppo è consentito. Questo passaggio sarà necessario eseguire per ogni utente che utilizza il reindirizzamento USB.

> [AZURE.NOTE] Reindirizzamento USB con Azure RemoteApp è supportato solo per i computer Windows.

### <a name="enable-usb-redirection-for-the-remoteapp-collection"></a>Attivare il reindirizzamento USB per una raccolta RemoteApp
Per abilitare il reindirizzamento USB a livello di raccolta, utilizzare il cmdlet seguente:

    Set-AzureRemoteAppCollection -CollectionName <collection_name> -CustomRdpProperty "nusbdevicestoredirect:s:*"

### <a name="enable-usb-redirection-for-the-client-computer"></a>Attivare il reindirizzamento USB per il computer client

Per configurare le impostazioni di reindirizzamento USB nel computer in uso:

1. Aprire l'Editor criteri di gruppo (GPEDIT. MSC). (Esecuzione gpedit da un prompt dei comandi).
2. Aprire **Computer configurazione amministrativi\Componenti Windows\Servizi Desktop remoto\Host connessione Desktop Client\RemoteFX USB il reindirizzamento del dispositivo**.
3. Fare doppio clic su **reindirizzamento consentire RD di altri dispositivi USB RemoteFX supportati dal computer in uso**.
4. Selezionare **attivato**e quindi selezionare **gli amministratori e utenti i diritti di accesso RemoteFX USB il reindirizzamento**.
5. Aprire un prompt dei comandi con autorizzazioni amministrative ed eseguire il comando seguente:

        gpupdate /force
6. Riavviare il computer.

È anche possibile utilizzare lo strumento di gestione dei criteri di gruppo per creare e applicare i criteri di reindirizzamento USB per tutti i computer del proprio dominio:

1. Accedere come amministratore del dominio controller di dominio.
2. Aprire la Console di gestione dei criteri di gruppo. (Fare clic su **Start > Strumenti di amministrazione > Gestione criteri di gruppo**.)
3. Passare al dominio o unità organizzativa per il quale si desidera creare il criterio.
4. Pulsante destro del mouse **Dominio predefinito**e quindi fare clic su **Modifica**.
5. Aprire **Computer configurazione amministrativi\Componenti Windows\Servizi Desktop remoto\Host connessione Desktop Client\RemoteFX USB il reindirizzamento del dispositivo**.
6. Fare doppio clic su **reindirizzamento consentire RD di altri dispositivi USB RemoteFX supportati dal computer in uso**.
7. Selezionare **attivato**e quindi selezionare **gli amministratori e utenti i diritti di accesso RemoteFX USB il reindirizzamento**.
8. Fare clic su **OK**.  
