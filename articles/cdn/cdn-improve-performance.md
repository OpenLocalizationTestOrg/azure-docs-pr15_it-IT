<properties
    pageTitle="Migliorare le prestazioni comprimendo i file in rete CDN di Azure | Microsoft Azure"
    description="Informazioni su come migliorare la velocità di trasferimento file e aumentare le prestazioni di caricamento delle pagine comprimendo i file in rete CDN di Azure."
    services="cdn"
    documentationCenter=""
    authors="camsoper"
    manager="erikre"
    editor=""/>

<tags
    ms.service="cdn"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/28/2016"
    ms.author="casoper"/>

# <a name="improve-performance-by-compressing-files"></a>Migliorare le prestazioni, la compressione dei file

La compressione è un metodo semplice ed efficace per migliorare la velocità di trasferimento file e migliorare le prestazioni di caricamento delle pagine mediante la riduzione delle dimensioni del file prima che venga inviato dal server. Larghezza di banda i costi e offre un'esperienza più efficiente per gli utenti.

Esistono due modi per attivare la compressione:

- È possibile abilitare la compressione sul server di origine, nel qual caso il CDN verrà attraversano i file compressi e i client di richiedono loro di inviare file compressi.
- È possibile abilitare la compressione direttamente nel server perimetrali CDN, nel qual caso il CDN verrà comprimere i file e fornire agli utenti finali, anche se non compresse dal server di origine.

> [AZURE.IMPORTANT] Modifiche alla configurazione CDN richiedere del tempo di propagare tramite la rete.  Per i profili di <b>Azure CDN da Akamai</b> , in genere completamento, in un minuto.  Per i profili di <b>Azure CDN da Verizon</b> , in genere è presente le modifiche applicate nel 90 minuti.  Se si tratta la prima volta compressione è stato configurato per l'endpoint CDN, è necessario prendere in considerazione in attesa 1-2 ore per essere certi che la compressione impostazioni distribuite ai POP prima di risoluzione dei problemi

## <a name="enabling-compression"></a>Abilitazione della compressione

> [AZURE.NOTE] I livelli Standard e Premium CDN forniscono le stesse funzionalità di compressione, ma l'interfaccia utente diversa.  Per ulteriori informazioni sulle differenze tra i livelli Standard e CDN Premium, vedere [Panoramica CDN Azure](cdn-overview.md).

### <a name="standard-tier"></a>Livello standard

> [AZURE.NOTE] Questa sezione viene applicata ai profili di ** **Azure CDN da Verizon Azure CDN Standard** e da Akamai** .

1. Selezionare l'endpoint di rete CDN che si desidera gestire e il profilo CDN.

    ![Punti finali blade profilo CDN](./media/cdn-file-compression/cdn-endpoints.png)

    Verrà visualizzata e il punto finale CDN.

2. Fare clic sul pulsante **Configura** .

    ![Pulsante Gestisci blade profilo CDN](./media/cdn-file-compression/cdn-config-btn.png)

    Verrà visualizzata e il CDN configurazione.

3. Attivare **la compressione**.

    ![Opzioni di compressione CDN](./media/cdn-file-compression/cdn-compress-standard.png)

4. Utilizzare i tipi di predefiniti o modificare l'elenco mediante la rimozione o aggiunta di tipi di file.
    
    > [AZURE.TIP] Possibili, mentre non si consiglia di applicare la compressione a formati compressi, ad esempio CAP, MP3, MP4, JPG e così via.
    
5. Dopo aver apportato le modifiche, fare clic sul pulsante **Salva** .

### <a name="premium-tier"></a>Livello Premium

> [AZURE.NOTE] Questa sezione viene applicata ai profili di **Azure CDN Premium da Verizon** .

1. Scegliere il pulsante **Gestisci** e il profilo CDN.

    ![Pulsante Gestisci blade profilo CDN](./media/cdn-file-compression/cdn-manage-btn.png)

    Portale di gestione di rete CDN apre.

2. Al passaggio del mouse sulla scheda **Grandi HTTP** , quindi al passaggio del mouse sopra il riquadro a comparsa **Impostazioni della Cache** .  Fare clic su **compressione**.

    Vengono visualizzate le opzioni di compressione.

    ![Compressione dei file](./media/cdn-file-compression/cdn-compress-files.png)

3. Abilitare la compressione facendo clic sul pulsante di opzione **Attivata la compressione** .  Immettere i tipi MIME che si desidera comprimere come un elenco delimitato da virgola (senza spazi) nella casella **Tipi di File** di testo.
        
    > [AZURE.TIP] Possibili, mentre non si consiglia di applicare la compressione a formati compressi, ad esempio CAP, MP3, MP4, JPG e così via. 

4. Dopo aver apportato le modifiche, fare clic sul pulsante di **aggiornamento** .


## <a name="compression-rules"></a>Regole di compressione

Le tabelle seguenti includono descrivono il comportamento di compressione Azure CDN per ogni scenario.

> [AZURE.IMPORTANT] Per **CDN Azure da Verizon** (Standard e Premium), solo idoneo sono compressi.  Per essere idoneo per la compressione, un file necessario:
>
> - Essere maggiore di 128 byte.
> - Essere minore di 1 MB.
> 
> **Rete CDN di Azure da Akamai**, tutti i file sono idonei per la compressione.
>
> Per tutti i prodotti di rete CDN di Azure, il file deve essere un tipo MIME che è stato [configurato per la compressione](#enabling-compression).
>
> I profili di **Azure CDN da Verizon** (Standard e Premium) supportano **gzip**, **decompressione**o **bzip2** codifica.  I profili di **Azure CDN da Akamai** supportano solo la codifica **gzip** .
>
> I punti finali **CDN Azure da Akamai** richiedono sempre **gzip** codificato file dell'origine, indipendentemente dalla richiesta del client.

### <a name="compression-disabled-or-file-is-ineligible-for-compression"></a>Compressione disabilitata o un file non è idoneo per la compressione

|Formato richiesto client (tramite la codifica accetta intestazione)|Formato di file memorizzati nella cache|Risposta CDN al client|Note|
|----------------|-----------|------------|-----|
|Compresso|Compresso|Compresso|   |
|Compresso|Non compressa|Non compressa|    | 
|Compresso|Non memorizzati nella cache|Compresso o non compresso|Dipende dalla risposta di origine|
|Non compressa|Compresso|Non compressa|    |
|Non compressa|Non compressa|Non compressa|    |   
|Non compressa|Non memorizzati nella cache|Non compressa|     |

### <a name="compression-enabled-and-file-is-eligible-for-compression"></a>Attivata la compressione e file sia idoneo per la compressione

|Formato richiesto client (tramite la codifica accetta intestazione)|Formato di file memorizzati nella cache|Risposta CDN al client|Note|
|----------------|-----------|------------|-----|
|Compresso|Compresso|Compresso|Rete CDN transcodifica tra i formati supportati|
|Compresso|Non compressa|Compresso|Compressione dei CDN|
|Compresso|Non memorizzati nella cache|Compresso|Rete CDN esegue la compressione se origin restituisce non compressa.  **Rete CDN di Azure da Verizon** passare al file non compresso alla prima richiesta e quindi Comprimi e dei file per le successive richieste nella cache.  I file con `Cache-Control: no-cache` intestazione mai essere compresso. 
|Non compressa|Compresso|Non compressa|Rete CDN esegue decompressione|
|Non compressa|Non compressa|Non compressa|     |  
|Non compressa|Non memorizzati nella cache|Non compressa|     |

## <a name="media-services-cdn-compression"></a>Servizi multimediali di Microsoft CDN compressione

Per CDN servizi multimediali abilitato i punti finali flussi, la compressione è attivata per impostazione predefinita per i tipi di contenuto seguenti: applicazione/vnd.ms-sstr + xml, application/dash+xml,application/vnd.apple.mpegurl, applicazione/f4m + xml. Sarà possibile attivare o disattivare la compressione per i tipi di menzionata tramite il portale di Azure.  

## <a name="see-also"></a>Vedere anche
- [Risoluzione dei problemi la compressione dei file di rete CDN](cdn-troubleshoot-compression.md)    
