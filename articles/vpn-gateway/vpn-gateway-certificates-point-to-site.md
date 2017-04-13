<properties 
   pageTitle="Creare certificati per virtuali punto al sito cross-locale connessioni utilizzando makecert | Microsoft Azure"
   description="In questo articolo contiene i passaggi per utilizzare makecert per creare certificati in Windows 10."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/22/2016"
   ms.author="cherylmc" />

# <a name="working-with-self-signed-certificates-for-point-to-site-connections"></a>Utilizzo di certificati per le connessioni al sito

In questo articolo consente di creare un certificato autofirmato utilizzando **makecert**e generare i certificati client del file. I passaggi sono scritti per makecert in Windows 10. Makecert convalidato per creare i certificati compatibili con le connessioni a P2S. 

Per le connessioni P2S, il metodo consigliato per i certificati consiste nell'utilizzare della soluzione di certificato dell'organizzazione, assicurandosi di rilasciare il client certificati con il formato del valore nome comuni 'name@yourdomain.com', piuttosto che il formato 'Nome dominio\nomeutente. NetBIOS'.

Se non si dispone di una soluzione aziendale, un certificato autofirmato è necessario per consentire ai client di P2S per connettersi a una rete. Siamo consapevoli makecert deprecato, ma è comunque un metodo valido per la creazione di certificati compatibili con le connessioni a P2S. Ci stiamo lavorando un'altra soluzione per la creazione di certificati, ma al momento makecert è il metodo preferito.

## <a name="create-a-self-signed-certificate"></a>Creare un certificato autofirmato

Makecert è un modo per la creazione di un certificato autofirmato. La procedura seguente guidano l'utente nella creazione di un certificato autofirmato utilizzando makecert. Questi passaggi non sono specifiche del modello di distribuzione. Sono valide per la gestione risorse e classica.

### <a name="to-create-a-self-signed-certificate"></a>Per creare un certificato autofirmato

1. Da un computer che esegue Windows 10, scaricare e installare [Windows Software Development Kit (SDK) per Windows 10](https://dev.windows.com/en-us/downloads/windows-10-sdk).

2. Dopo l'installazione, è possibile trovare l'utilità di makecert.exe in questo percorso: c:\Programmi\Microsoft file (x86) \Windows Kits\10\bin\<arco >. 
        
    Esempio:`C:\Program Files (x86)\Windows Kits\10\bin\x64`

3. Successivamente, creare e installare un certificato nell'archivio certificati personali nel computer in uso. Nell'esempio seguente crea un file *. cer* corrispondente da caricare in Azure durante la configurazione P2S. Eseguire il comando seguente come amministratore. Sostituire *ARMP2SRootCert* e *ARMP2SRootCert.cer* con il nome che si desidera utilizzare per il certificato.<br><br>Il certificato si troverà nei certificati - User\Personal\Certificates corrente.

        makecert -sky exchange -r -n "CN=ARMP2SRootCert" -pe -a sha1 -len 2048 -ss My "ARMP2SRootCert.cer"


###  <a name="rootpublickey"></a>Per ottenere la chiave pubblica

Come parte della configurazione del Gateway VPN per le connessioni al sito, la chiave pubblica per il certificato viene caricata Azure.

1. Per ottenere un file CER del certificato, aprire **certmgr**. Pulsante destro del mouse il certificato autofirmato, fare clic su **tutte le attività**e quindi fare clic su **Esporta**. Verrà visualizzata l' **Esportazione guidata certificati**.

2. Nella procedura guidata, fare clic su **Avanti**, selezionare **No, esportare la chiave privata**e quindi fare clic su **Avanti**.

3. Nella pagina **Formato File di esportazione** , selezionare **Base 64 codificato x. 509 (. CER).** Scegliere **Avanti**. 

4. Sul **File da esportare**, **passare** al percorso in cui si vuole esportare il certificato. Per **Nome File**, assegnare un nome di file di certificato. Fare clic su **Avanti**.

5. Fare clic su **Fine** per esportare il certificato.

 
### <a name="export-the-self-signed-certificate-optional"></a>Esportare il certificato autofirmato (facoltativo)

È consigliabile esportare il certificato autofirmato e archiviarlo in modo sicuro. Se necessario, è possibile in seguito installarlo in un altro computer e generare più certificati client o esportare un altro file. cer. Tutti i computer con installato un certificato client e che viene configurato anche con il client VPN iniz impostazioni è possono connettersi alla rete virtuale tramite P2S. Per questo motivo, si desidera verificare che i certificati client vengono generati e installati solo quando necessario e che il certificato autofirmato è archiviato in modo sicuro.

Per esportare il certificato autofirmato come un file pfx, selezionare il certificato e utilizzare gli stessi passaggi come descritto in [esportare un certificato client](#clientkey) da esportare.

## <a name="create-and-install-client-certificates"></a>Creare e installare certificati client

Installare il certificato autofirmato non direttamente nel computer client. È necessario generare un certificato client dal certificato autofirmato. Quindi esportare e installare il certificato client nel computer client. I passaggi seguenti non sono specifiche del modello di distribuzione. Sono valide per la gestione risorse e classica.

### <a name="part-1---generate-a-client-certificate-from-a-self-signed-certificate"></a>Parte 1 - generare un certificato client da un certificato autofirmato

La procedura seguente scorrere un modo per generare un certificato client da un certificato autofirmato. È possibile generare più certificati client dallo stesso certificato. Ogni certificato client può essere esportato e installato nel computer client. 

1. Nello stesso computer usato per creare il certificato autofirmato, aprire un prompt dei comandi come amministratore.

2. In questo esempio, "ARMP2SRootCert" si intende il certificato autofirmato creato. 
    - Modificare il nome della radice autofirmata che si desidera generare il certificato client da *"ARMP2SRootCert"* . 
    - Modificare il nome che si desidera generare un certificato client per essere *ClientCertificateName* . 


    Modificare ed eseguire l'esempio per generare un certificato client. Se si esegue l'esempio seguente senza modificarlo, il risultato è un certificato client denominato ClientCertificateName nell'archivio certificati personali generati da autorità di certificazione ARMP2SRootCert.

        makecert.exe -n "CN=ClientCertificateName" -pe -sky exchange -m 96 -ss My -in "ARMP2SRootCert" -is my -a sha1

4. Tutti i certificati sono archiviati nei 'Certificati - User\Personal\Certificates corrente' vengono archiviate nel computer. È possibile generare come molti certificati client in base alle esigenze in base a questa procedura.

### <a name="clientkey"></a>Parte 2: esportare un certificato client

1. Per esportare un certificato client, aprire **certmgr**. Pulsante destro del mouse il certificato client che si desidera esportare, fare clic su **tutte le attività**e quindi fare clic su **Esporta**. Verrà visualizzata l' **Esportazione guidata certificati**.

2. Nella procedura guidata, fare clic su **Avanti**, quindi selezionare **Esporta la chiave privata**e quindi fare clic su **Avanti**.

3. Nella pagina **Formato File di esportazione** , è possibile lasciare selezionate le impostazioni predefinite. Fare clic su **Avanti**. 
 
4. Nella pagina **sicurezza** , è necessario proteggere la chiave privata. Se si sceglie di utilizzare una password, assicurarsi che consente di registrare o ricordare la password impostata per il certificato. Fare clic su **Avanti**.

5. Sul **File da esportare**, **passare** al percorso in cui si vuole esportare il certificato. Per **Nome File**, assegnare un nome di file di certificato. Fare clic su **Avanti**.

6. Fare clic su **Fine** per esportare il certificato.  

### <a name="part-3---install-a-client-certificate"></a>Parte 3 - installare un certificato client

Ogni client che si desidera connettersi alla rete virtuale tramite una connessione punto a sito deve essere installato un certificato client. Il certificato è oltre il pacchetto di configurazione VPN necessario. La procedura seguente scorrere l'installazione del certificato client manualmente.

1. Individuare e copiare il file *. pfx* nel computer client. Nel computer client fare doppio clic su file *. pfx* da installare. Lasciare la **Posizione di archiviazione** come **Utente corrente**e quindi fare clic su **Avanti**.

2. In **File** da importare pagina, non apportare le modifiche. Fare clic su **Avanti**.

3. Nella pagina **protezione della chiave privata** immettere la password per il certificato se è stato utilizzato uno, o verificare che l'identità di protezione che installa il certificato sia corretto, quindi fare clic su **Avanti**.

4. Nella pagina **Archivio certificati** lasciare il percorso predefinito e quindi fare clic su **Avanti**.

5. Fare clic su **Fine**. In **Avviso di sicurezza** per l'installazione del certificato, fare clic su **Sì**. Il certificato è importato.

## <a name="next-steps"></a>Passaggi successivi

Continuare con la configurazione del punto al sito. 

- Per passaggi del modello di **Gestione risorse di** distribuzione, vedere [configurare una connessione punto a sito a un VNet tramite PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md). 
- Per **classica** distribuzione passaggi per il modello, vedere [connessione VPN configura un punto al sito per un VNet tramite il portale classico](vpn-gateway-point-to-site-create.md).