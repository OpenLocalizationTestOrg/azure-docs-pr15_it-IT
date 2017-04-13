<properties 
    pageTitle="Come reindirizzare dispositivi USB in RemoteApp di Azure | Microsoft Azure" 
    description="Informazioni su come utilizzare il reindirizzamento per i dispositivi USB in Azure RemoteApp." 
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



# <a name="how-do-you-redirect-usb-devices-in-azure-remoteapp"></a>Come reindirizzare dispositivi USB in RemoteApp di Azure

> [AZURE.IMPORTANT]
> Più RemoteApp Azure utilizzato. Leggere l' [annuncio](https://go.microsoft.com/fwlink/?linkid=821148) per informazioni dettagliate.

Il reindirizzamento dei dispositivi consente agli utenti di utilizzare i dispositivi USB collegati al computer o tablet con l'App in Azure RemoteApp. Ad esempio, se è stata condivisa Skype tramite RemoteApp di Azure, gli utenti necessario essere in grado di utilizzare le fotocamere dispositivo.

Prima di proseguire, assicurarsi che leggere le informazioni di reindirizzamento USB in [uso il reindirizzamento in Azure RemoteApp](remoteapp-redirection.md). Tuttavia la consigliati nusbdevicestoredirect:s: * non è possibile usare le webcam USB e potrebbe non funzionare per alcune stampanti USB o dispositivi multifunzionali USB. In base alla progettazione e per motivi di sicurezza, l'amministratore di RemoteApp di Azure deve attivare il reindirizzamento per GUID classe dispositivo o per ID istanza periferica prima che gli utenti possono utilizzare i dispositivi.

Anche se in questo articolo parla il reindirizzamento fotocamera web, è possibile utilizzare un approccio simile per reindirizzare le stampanti USB e altri dispositivi multifunzionale USB che non vengono reindirizzati tramite il **nusbdevicestoredirect:s:*** comando.

## <a name="redirection-options-for-usb-devices"></a>Opzioni di reindirizzamento per i dispositivi USB
Azure RemoteApp utilizza meccanismi di molto simile per il reindirizzamento di dispositivi USB come quelli disponibili per Servizi Desktop remoto. La tecnologia sottostante consente di scegliere il metodo di reindirizzamento corretto per un determinato dispositivo, per ottenere il massimo di entrambi alto livello e un dispositivo USB RemoteFX reindirizzamento utilizzando il **usbdevicestoredirect:s:** comando. Esistono quattro elementi a questo comando:

| Ordine di elaborazione | Parametro           | Descrizione                                                                                                                |
|------------------|---------------------|----------------------------------------------------------------------------------------------------------------------------|
| 1                | *                   | Seleziona tutti i dispositivi che non vengono rilevati dal reindirizzamento di alto livello. Nota: per impostazione predefinita, * non funziona per webcam USB.  |
|                  | {GUID classe dispositivo} | Seleziona tutti i dispositivi che soddisfano la classe di installazione di dispositivo specificato.                                                           |
|                  | USB\InstanceID      | Selezionare un dispositivo USB specificato per l'ID istanza specificata.                                                                  |
| 2                | -ID USB\Instance    | Rimuove le impostazioni di reindirizzamento per il dispositivo specificato.                                                                 |

## <a name="redirecting-a-usb-device-by-using-the-device-class-guid"></a>Reindirizzare un dispositivo USB utilizzando la classe di dispositivo GUID
Esistono due modi per trovare la classe di dispositivi GUID che possono essere usate per il reindirizzamento. 

La prima opzione consiste nell'utilizzare [Definite dal sistema dispositivo configurazione classi disponibili ai fornitori](https://msdn.microsoft.com/library/windows/hardware/ff553426.aspx). Selezionare la classe che maggiormente corrisponde al dispositivo collegato al computer locale. Fotocamere può trattarsi di una classe di dispositivi di acquisizione immagini o classe di dispositivi di acquisizione Video. È necessario eseguire alcuni esperimenti con le classi di dispositivo per individuare la classe corretta GUID che interagisce con il dispositivo USB collegato localmente (in questo caso la webcam).

Un modo migliore o la seconda opzione, consiste nell'eseguire la procedura seguente per trovare la classe di dispositivo specifico GUID:

1. Aprire Gestione dispositivi, individuare il dispositivo che sarà reindirizzato e pulsante destro del mouse e quindi aprire la finestra Proprietà.
![Aprire Gestione dispositivi](./media/remoteapp-usbredir/ra-devicemanager.png)
2. Nella scheda **Dettagli** , scegliere proprietà **Guid classe**. Il valore visualizzato è il GUID della classe per tale tipo di dispositivo.
![Proprietà della fotocamera](./media/remoteapp-usbredir/ra-classguid.png)
3. Usare il valore di classe Guid per il reindirizzamento dispositivi che corrispondono a essa.

Per esempio:

        Set-AzureRemoteAppCollection -CollectionName <collection name> -CustomRdpProperty "nusbdevicestoredirect:s:<Class Guid value>"

È possibile combinare più reindirizzamenti dispositivo cmdlet stesso. Ad esempio: per reindirizzare l'archiviazione locale e una webcam USB, cmdlet è simile alla seguente:

        Set-AzureRemoteAppCollection -CollectionName <collection name> -CustomRdpProperty "drivestoredirect:s:*`nusbdevicestoredirect:s:<Class Guid value>"

Quando si imposta il reindirizzamento del dispositivo mediante GUID classe vengono reindirizzati tutti i dispositivi che corrispondono a tale classe GUID dell'insieme specificato. Ad esempio, se sono presenti più computer collegati alla rete locale che hanno la stessa webcam USB, è possibile eseguire un cmdlet singolo per reindirizzare tutte le webcam.

## <a name="redirecting-a-usb-device-by-using-the-device-instance-id"></a>Reindirizzare un dispositivo USB mediante l'ID dell'istanza di dispositivo

Se si desidera un controllo più preciso e si vuole controllare il reindirizzamento per dispositivo, è possibile utilizzare il parametro di reindirizzamento **USB\InstanceID** .

La parte più difficile di questo metodo è possibile trovare l'ID istanza di dispositivo USB. È necessario accedere al computer e il dispositivo USB specifico. Attenersi alla seguente procedura:

1. Attivare il reindirizzamento del dispositivo in sessione Desktop remoto, come descritto in [come è possibile utilizzare i dispositivi e le risorse in una sessione Desktop remoto?](http://windows.microsoft.com/en-us/windows7/How-can-I-use-my-devices-and-resources-in-a-Remote-Desktop-session)
2. Aprire una connessione Desktop remoto e fare clic su **Mostra opzioni**.
3. Fare clic su **Salva con nome** per salvare le impostazioni di connessione corrente in un file RDP.  
    ![Salvare le impostazioni come un file RDP](./media/remoteapp-usbredir/ra-saveasrdp.png)
4. Scegliere un nome file e un percorso, ad esempio "MyConnection.rdp" e "Questa PC\Documents" e salvare il file.
5. Aprire il file MyConnection.rdp utilizzando un editor di testo e trovare l'ID dell'istanza del dispositivo che si desidera reindirizzare.

A questo punto, usare l'ID dell'istanza nel seguente cmdlet:

    Set-AzureRemoteAppCollection -CollectionName <collection name> -CustomRdpProperty "nusbdevicestoredirect:s: USB\<Device InstanceID value>"



### <a name="help-us-help-you"></a>Aiutaci a consentono di 
Non tutti sanno che oltre alle valutazione in questo articolo e aggiunta di commenti verso il basso sotto, è possibile apportare modifiche all'articolo stesso. Qualcosa mancanti? Un problema? È stato possibile scrivere qualcosa che solo confusione? Scorrere verso l'alto e fare clic su **Modifica nella GitHub** per apportare modifiche - quelli venga recapitata a Microsoft per la revisione e quindi una volta abbiamo disconnettere su di essi, verranno visualizzate le modifiche e i miglioramenti a destra.