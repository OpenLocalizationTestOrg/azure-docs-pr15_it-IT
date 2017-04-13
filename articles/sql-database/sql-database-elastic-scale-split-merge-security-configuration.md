<properties 
    pageTitle="Configurazione della protezione divisa unione | Microsoft Azure" 
    description="Configurare x409 i certificati di crittografia" 
    metaKeywords="Elastic Database certificates security" 
    services="sql-database" 
    documentationCenter="" 
    manager="jhubbard" 
    authors="torsteng"/>

<tags 
    ms.service="sql-database" 
    ms.workload="sql-database" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="05/27/2016" 
    ms.author="torsteng" />


# <a name="split-merge-security-configuration"></a>Configurazione della protezione di stampa unione di divisione  

Per utilizzare il servizio di divisione/unione, è necessario configurare correttamente sicurezza. Il servizio fa parte della funzionalità scalabilità flessibile di Database SQL di Microsoft Azure. Per ulteriori informazioni, vedere [flessibile scala divisa e unire esercitazione servizio](sql-database-elastic-scale-configure-deploy-split-and-merge.md).

## <a name="configuring-certificates"></a>Configurazione dei certificati

I certificati sono configurati in due modi. 

1. [Per configurare il certificato SSL](#To-Configure-the-SSL#Certificate)
2. [Per configurare i certificati di Client](#To-Configure-Client-Certificates) 

## <a name="to-obtain-certificates"></a>Per ottenere i certificati

I certificati possono essere ottenuti dal pubblico autorità di certificazione (CA) o dal [Servizio di certificati di Windows](http://msdn.microsoft.com/library/windows/desktop/aa376539.aspx). Questi sono i metodi preferiti per ottenere i certificati.

Se queste opzioni non sono disponibili, è possibile generare **certificati**.
 
## <a name="tools-to-generate-certificates"></a>Strumenti per generare i certificati

* [Makecert.exe](http://msdn.microsoft.com/library/bfsktky3.aspx)
* [Pvk2pfx.exe](http://msdn.microsoft.com/library/windows/hardware/ff550672.aspx)

### <a name="to-run-the-tools"></a>Per eseguire gli strumenti

* Da un sviluppatore prompt dei comandi per Visual Studio, vedere [Prompt dei comandi di Visual Studio](http://msdn.microsoft.com/library/ms229859.aspx) 

    Se è installato, passare a:

        %ProgramFiles(x86)%\Windows Kits\x.y\bin\x86 

* Ottenere WDK da [Windows 8.1: scaricare strumenti di modifica e kit](http://msdn.microsoft.com/windows/hardware/gg454513#drivers)

## <a name="to-configure-the-ssl-certificate"></a>Per configurare il certificato SSL
È necessario un certificato SSL per crittografare le comunicazioni e autenticazione del server. Scegliere più applicabile degli tre scenari seguenti ed eseguire tutti i relativi passaggi:

### <a name="create-a-new-self-signed-certificate"></a>Creare un nuovo certificato autofirmato

1.    [Creare un certificato autofirmato](#Create-a-Self-Signed-Certificate)
2.    [Creare file PFX per il certificato SSL autofirmato](#Create-PFX-file-for-Self-Signed-SSL-Certificate)
3.    [Caricare il certificato SSL per servizio Cloud](#Upload-SSL-Certificate-to-Cloud-Service)
4.    [Aggiornare il certificato SSL in File di configurazione del servizio](#Update-SSL-Certificate-in-Service-Configuration-File)
5.    [Importare un'autorità di certificazione SSL](#Import-SSL-Certification-Authority)

### <a name="to-use-an-existing-certificate-from-the-certificate-store"></a>Usare un certificato esistente dall'archivio certificati
1. [Esportare il certificato SSL da archivio certificati](#Export-SSL-Certificate-From-Certificate-Store)
2. [Caricare il certificato SSL per servizio Cloud](#Upload-SSL-Certificate-to-Cloud-Service)
3. [Aggiornare il certificato SSL in File di configurazione del servizio](#Update-SSL-Certificate-in-Service-Configuration-File)

### <a name="to-use-an-existing-certificate-in-a-pfx-file"></a>Usare un certificato esistente in un file PFX

1. [Caricare il certificato SSL per servizio Cloud](#Upload-SSL-Certificate-to-Cloud-Service)
2. [Aggiornare il certificato SSL in File di configurazione del servizio](#Update-SSL-Certificate-in-Service-Configuration-File)

## <a name="to-configure-client-certificates"></a>Per configurare i certificati di client
I certificati client sono necessarie per l'autenticazione delle richieste al servizio. Scegliere più applicabile degli tre scenari seguenti ed eseguire tutti i relativi passaggi:

### <a name="turn-off-client-certificates"></a>Disattivare i certificati client
1.    [Disattivare l'autenticazione basata su certificato Client](#Turn-Off-Client-Certificate-Based-Authentication)

### <a name="issue-new-self-signed-client-certificates"></a>Rilasciare nuovi certificati client autofirmato
1.    [Creare un'autorità di certificazione autofirmato](#Create-a-Self-Signed-Certification-Authority)
2.    [Caricare autorità di certificazione per servizio Cloud](#Upload-CA-Certificate-to-Cloud-Service)
3.    [Autorità di certificazione aggiornamento file di configurazione del servizio](#Update-CA-Certificate-in-Service-Configuration-File)
4.    [Rilasciare certificati Client](#Issue-Client-Certificates)
5.    [Creare file PFX per i certificati Client](#Create-PFX-files-for-Client-Certificates)
6.    [Certificato Client di importazione](#Import-Client-Certificate)
7.    [Copiare identificazioni personali del certificato Client](#Copy-Client-Certificate-Thumbprints)
8.    [Configurare i client consentiti nel File di configurazione del servizio](#Configure-Allowed-Clients-in-the-Service-Configuration-File)

### <a name="use-existing-client-certificates"></a>Utilizzare i certificati di client esistente
1.    [Trovare chiave pubblica autorità di certificazione](#Find-CA-Public Key)
2.    [Caricare autorità di certificazione per servizio Cloud](#Upload-CA-certificate-to-cloud-service)
3.    [Aggiornare il file di configurazione del servizio Autorità di certificazione](#Update-CA-Certificate-in-Service-Configuration-File)
4.    [Copiare identificazioni personali del certificato Client](#Copy-Client-Certificate-Thumbprints)
5.    [Configurare i client consentiti nel File di configurazione del servizio](#Configure-Allowed-Clients-in-the-Service-Configuration File)
6.    [Configurare il controllo di revoca certificato Client](#Configure-Client-Certificate-Revocation-Check)

## <a name="allowed-ip-addresses"></a>Indirizzi IP consentiti

Accesso per gli endpoint del servizio può essere limitata a determinati intervalli di indirizzi IP.

## <a name="to-configure-encryption-for-the-store"></a>Per configurare la crittografia per l'archivio

È necessario un certificato per crittografare le credenziali memorizzate nell'archivio dei metadati. Scegliere i tre scenari seguenti più appropriato ed eseguire tutti i relativi passaggi:

### <a name="use-a-new-self-signed-certificate"></a>Utilizzare un nuovo certificato autofirmato

1.     [Creare un certificato autofirmato](#Create-a-Self-Signed-Certificate)
2.     [Creare file PFX per il certificato di crittografia autofirmato](#Create-PFX-file-for-Self-Signed-Encryption-Certificate)
3.     [Caricare il certificato di crittografia per servizio Cloud](#Upload-Encryption-Certificate-to-Cloud-Service)
4.     [Aggiornare il certificato di crittografia dei file di configurazione del servizio](#Update-Encryption-Certificate-in-Service-Configuration-File)

### <a name="use-an-existing-certificate-from-the-certificate-store"></a>Usare un certificato esistente dall'archivio certificati

1.     [Esportare il certificato di crittografia dall'archivio certificati](#Export-Encryption-Certificate-From-Certificate-Store)
2.     [Caricare il certificato di crittografia per servizio Cloud](#Upload-Encryption-Certificate-to-Cloud-Service)
3.     [Aggiornare il certificato di crittografia dei file di configurazione del servizio](#Update-Encryption-Certificate-in-Service-Configuration-File)

### <a name="use-an-existing-certificate-in-a-pfx-file"></a>Usare un certificato esistente in un file PFX

1.     [Caricare il certificato di crittografia per servizio Cloud](#Upload-Encryption-Certificate-to-Cloud-Service)
2.     [Aggiornare il certificato di crittografia dei file di configurazione del servizio](#Update-Encryption-Certificate-in-Service-Configuration-File)

## <a name="the-default-configuration"></a>La configurazione predefinita

La configurazione predefinita nega tutti gli accessi all'endpoint HTTP. Verrà visualizzata l'impostazione consigliata, poiché le richieste per gli endpoint possono effettuare informazioni sensibili come le credenziali del database.
La configurazione predefinita consente tutti l'accesso all'endpoint HTTPS. Questa impostazione potrebbe essere ulteriormente limitata.

### <a name="changing-the-configuration"></a>La modifica della configurazione

Gruppo di regole di controllo di accesso che si applicano a ed endpoint è configurato nel **<EndpointAcls>** sezione del **file di configurazione del servizio**.

    <EndpointAcls>
      <EndpointAcl role="SplitMergeWeb" endPoint="HttpIn" accessControl="DenyAll" />
      <EndpointAcl role="SplitMergeWeb" endPoint="HttpsIn" accessControl="AllowAll" />
    </EndpointAcls>

Le regole in un gruppo di controllo di accesso sono configurate un <AccessControl name=""> sezione del file di configurazione del servizio. 

Il formato è descritto nella documentazione di rete elenchi di controllo.
Ad esempio, per consentire solo indirizzi IP nell'intervallo 100.100.0.0 a 100.100.255.255 per accedere all'endpoint HTTPS, le regole da aspetto simile al seguente:

    <AccessControl name="Retricted">
      <Rule action="permit" description="Some" order="1" remoteSubnet="100.100.0.0/16"/>
      <Rule action="deny" description="None" order="2" remoteSubnet="0.0.0.0/0" />
    </AccessControl>
    <EndpointAcls>
    <EndpointAcl role="SplitMergeWeb" endPoint="HttpsIn" accessControl="Restricted" />

## <a name="denial-of-service-prevention"></a>Negazione dei programmi di servizio

Esistono due diversi meccanismi supportati per rilevare ed evitare di attacchi:

*    Limita numero di richieste simultanee per host remoto (deselezionata per impostazione predefinita)
*    Limitare la frequenza di accesso per host remoto (in per impostazione predefinita)

Questi sono in base alle ulteriormente descritte in protezione IP dinamica in IIS. Quando la modifica di questa configurazione prestare attenzione ai fattori seguenti:

* Il comportamento del proxy e dispositivi NAT sulle informazioni host remoto
* Ogni richiesta per tutte le risorse nel ruolo web viene considerato (ad esempio il caricamento di script, immagini e così via)

## <a name="restricting-number-of-concurrent-accesses"></a>Limitazione delle numero di accessi simultanei

Le impostazioni che consentono di configurare questo comportamento sono:

    <Setting name="DynamicIpRestrictionDenyByConcurrentRequests" value="false" />
    <Setting name="DynamicIpRestrictionMaxConcurrentRequests" value="20" />

Modificare DynamicIpRestrictionDenyByConcurrentRequests su true per abilitare la protezione.

## <a name="restricting-rate-of-access"></a>Limitazione delle tariffe di access

Le impostazioni che consentono di configurare questo comportamento sono:

    <Setting name="DynamicIpRestrictionDenyByRequestRate" value="true" />
    <Setting name="DynamicIpRestrictionMaxRequests" value="100" />
    <Setting name="DynamicIpRestrictionRequestIntervalInMilliseconds" value="2000" />

## <a name="configuring-the-response-to-a-denied-request"></a>Configurare la risposta a una richiesta negata

Questa impostazione consente di configurare la risposta a una richiesta di negato:

    <Setting name="DynamicIpRestrictionDenyAction" value="AbortRequest" />
Fare riferimento alla documentazione per la sicurezza IP dinamico in IIS per gli altri valori supportati.

## <a name="operations-for-configuring-service-certificates"></a>Operazioni per la configurazione di certificati di servizio
In questo argomento è solo per riferimento. Seguire i passaggi di configurazione descritti in:

* Configurare il certificato SSL
* Configurare i certificati client

## <a name="create-a-self-signed-certificate"></a>Creare un certificato autofirmato
Eseguire:

    makecert ^
      -n "CN=myservice.cloudapp.net" ^
      -e MM/DD/YYYY ^
      -r -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.1" ^
      -a sha1 -len 2048 ^
      -sv MySSL.pvk MySSL.cer

Per personalizzare i:

*    -n con l'URL del servizio. I caratteri jolly ("CN = * .cloudapp .net") e nomi alternativi ("CN=myservice1.cloudapp.net, CN=myservice2.cloudapp.net") sono supportati.
*    -e con la data di scadenza certificato creare una password complessa e specificare quando richiesto.

## <a name="create-pfx-file-for-self-signed-ssl-certificate"></a>Creare file PFX per un certificato SSL autofirmato

Eseguire:

        pvk2pfx -pvk MySSL.pvk -spc MySSL.cer

Immettere la password e quindi esportare certificato con queste opzioni:
* Esporta la chiave privata
* Esporta tutte le proprietà "Extended"

## <a name="export-ssl-certificate-from-certificate-store"></a>Esportare il certificato SSL da archivio certificati

* Trovare il certificato
* Fare clic su azioni -> tutte le attività -> Esporta...
* Esportare certificato in un. File PFX con queste opzioni:
    * Esporta la chiave privata
    * Includere tutti i certificati nel percorso di certificazione, se possibile * Esporta tutte le proprietà estese

## <a name="upload-ssl-certificate-to-cloud-service"></a>Caricare il certificato SSL in servizio cloud

Carica certificato con esistenti o generato. File PFX con la coppia SSL:

* Immettere la password di protezione delle informazioni sulla chiave private

## <a name="update-ssl-certificate-in-service-configuration-file"></a>Aggiornare il certificato SSL in file di configurazione del servizio

Aggiornare il valore di identificazione personale dell'impostazione seguente nel file di configurazione del servizio con l'identificazione digitale del certificato di caricamento di servizio cloud:

    <Certificate name="SSL" thumbprint="" thumbprintAlgorithm="sha1" />

## <a name="import-ssl-certification-authority"></a>Importare un'autorità di certificazione SSL

Seguire questa procedura in computer account tutti/comunicare con il servizio:

* Fare doppio clic sui. File CER in Esplora risorse
* Nella finestra di dialogo certificato, fare clic su Installa certificato.
* Importare certificato nell'archivio Autorità di certificazione radice attendibili

## <a name="turn-off-client-certificate-based-authentication"></a>Disattivare l'autenticazione basata su certificato client

È supportata solo autenticazione basata su certificato client e disattivarlo consentirà per l'accesso pubblico per gli endpoint del servizio, a meno che non altri meccanismi sono attive (ad esempio Microsoft Azure Virtual Network).

Modificare queste impostazioni su false nel file di configurazione del servizio per disattivare la caratteristica:

    <Setting name="SetupWebAppForClientCertificates" value="false" />
    <Setting name="SetupWebserverForClientCertificates" value="false" />

Impostazione di certificato CA, copiare la stessa identificazione personale del certificato SSL:

    <Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />

## <a name="create-a-self-signed-certification-authority"></a>Creare un'autorità di certificazione autofirmato
Eseguire la procedura seguente per creare un certificato autofirmato funga da un'autorità di certificazione:

    makecert ^
    -n "CN=MyCA" ^
    -e MM/DD/YYYY ^
     -r -cy authority -h 1 ^
     -a sha1 -len 2048 ^
      -sr localmachine -ss my ^
      MyCA.cer

Personalizzare la

*    -e con la data di scadenza di certificazione


## <a name="find-ca-public-key"></a>Trovare chiave pubblica autorità di certificazione

Tutti i certificati client devono essere rilasciati da un'autorità di certificazione attendibile per il servizio. Trovare la chiave pubblica all'autorità di certificazione che certificati il client che verrà utilizzato per l'autenticazione per caricare il servizio cloud.

Se il file con la chiave pubblica non è disponibile, è possibile esportarlo dall'archivio certificati:

* Trovare il certificato
    * Cercare un certificato client emesso dalla stessa autorità di certificazione
* Fare doppio clic sul certificato.
* Selezionare la scheda Percorso certificazione nella finestra di dialogo certificato.
* Fare doppio clic sulla voce CA nel percorso.
* Scrivere note della proprietà del certificato.
* Chiudere la finestra di dialogo **certificato** .
* Trovare il certificato
    * Cercare l'autorità di certificazione indicato in precedenza.
* Fare clic su azioni -> tutte le attività -> Esporta...
* Esportare certificato in un. CER le seguenti opzioni:
    * **No, esportare la chiave privata**
    * Se possibile, includere tutti i certificati nel percorso di certificazione.
    * Esporta tutte le proprietà estese.

## <a name="upload-ca-certificate-to-cloud-service"></a>Caricare autorità di certificazione in servizio cloud

Carica certificato con l'esistente o generato. File CER con la chiave pubblica CA.

## <a name="update-ca-certificate-in-service-configuration-file"></a>Autorità di certificazione aggiornamento file di configurazione del servizio

Aggiornare il valore di identificazione personale dell'impostazione seguente nel file di configurazione del servizio con identificazione personale del certificato di caricamento di servizio cloud:

    <Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />

Aggiornare il valore di questa impostazione con la stessa identificazione personale:

    <Setting name="AdditionalTrustedRootCertificationAuthorities" value="" />

## <a name="issue-client-certificates"></a>Rilasciare certificati client

Ogni utente autorizzato ad accedere al servizio deve avere un certificato client emesso per his/hers esclusivo utilizzare e deve scegliere che HIS/hers proprietari password complesse per proteggere la chiave privata. 

Sullo stesso computer in cui il certificato autofirmato generato e archiviato, è necessario eseguire la procedura seguente:

    makecert ^
      -n "CN=My ID" ^
      -e MM/DD/YYYY ^
      -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.2" ^
      -a sha1 -len 2048 ^
      -in "MyCA" -ir localmachine -is my ^
      -sv MyID.pvk MyID.cer

Personalizzazione:

* n - con un ID per al client che verrà eseguita l'autenticazione con il certificato
* -e con la data di scadenza certificato
* MyID.pvk e MyID.cer con nomi di file univoco per il certificato client

Questo comando chiederà di una password per creare e quindi utilizzare una sola volta. Utilizzare una password complessa.

## <a name="create-pfx-files-for-client-certificates"></a>Creare file PFX per client certificati

Per ogni certificato client generato, eseguire:

    pvk2pfx -pvk MyID.pvk -spc MyID.cer

Personalizzazione:

    MyID.pvk and MyID.cer with the filename for the client certificate

Immettere la password e quindi esportare certificato con queste opzioni:

* Esporta la chiave privata
* Esporta tutte le proprietà "Extended"
* La persona a cui viene rilasciato il certificato deve scegliere la password di esportazione

## <a name="import-client-certificate"></a>Certificato client di importazione

Ogni singolo per il quale è stato rilasciato un certificato client deve importare coppia di computer che e verrà utilizzato per comunicare con il servizio:

* Fare doppio clic sui. File PFX in Esplora risorse
* Importazione in personale archivio certificati con almeno questa opzione:
    * Includere tutte le proprietà estese selezionate

## <a name="copy-client-certificate-thumbprints"></a>Copiare identificazioni personali del certificato client
Ogni singolo per il quale è stato rilasciato un certificato client deve seguire la procedura seguente per ottenere l'identificazione personale del his/hers certificato che verrà aggiunti al file di configurazione del servizio:
* Eseguire certmgr.exe
* Selezionare la scheda personale
* Fare doppio clic sul certificato client da utilizzare per l'autenticazione
* Nella finestra di dialogo certificato che si apre, selezionare la scheda dettagli
* Assicurarsi che trasmette Mostra tutto
* Selezionare il campo denominato identificazione personale nell'elenco
* Copia il valore di identificazione personale **eliminare caratteri Unicode non visibili che precede la prima cifra** eliminare tutti gli spazi

## <a name="configure-allowed-clients-in-the-service-configuration-file"></a>Configurare i client consentiti nel file di configurazione del servizio

Aggiornare il valore dell'impostazione seguente nel file di configurazione del servizio con un elenco delimitato da virgole di identificazioni personali dei certificati del client autorizzati ad accedere al servizio:

    <Setting name="AllowedClientCertificateThumbprints" value="" />

## <a name="configure-client-certificate-revocation-check"></a>Configurare il controllo di revoca certificato client

L'impostazione predefinita non rivolgersi all'autorità di certificazione relativi allo stato di revoca certificato client. Per attivare i controlli, se l'autorità di certificazione che ha rilasciato il client certificati supporta parteciparvi, modificare le impostazioni seguenti con uno dei valori definiti nell'enumerazione X509RevocationMode:

    <Setting name="ClientCertificateRevocationCheck" value="NoCheck" />

## <a name="create-pfx-file-for-self-signed-encryption-certificates"></a>Creare file PFX per i certificati di crittografia autofirmato

Per un certificato di crittografia, eseguire:

    pvk2pfx -pvk MyID.pvk -spc MyID.cer

Personalizzazione:

    MyID.pvk and MyID.cer with the filename for the encryption certificate

Immettere la password e quindi esportare certificato con queste opzioni:
*    Esporta la chiave privata
*    Esporta tutte le proprietà "Extended"
*    Quando si carica il certificato sul servizio cloud, sarà necessario la password.

## <a name="export-encryption-certificate-from-certificate-store"></a>Esportare il certificato di crittografia dall'archivio certificati

*    Trovare il certificato
*    Fare clic su azioni -> tutte le attività -> Esporta...
*    Esportare certificato in un. File PFX con queste opzioni: 
  *    Esporta la chiave privata
  *    Se possibile, Includi tutti i certificati nel percorso di certificazione 
*    Esporta tutte le proprietà "Extended"

## <a name="upload-encryption-certificate-to-cloud-service"></a>Carica certificato di crittografia al servizio cloud

Carica certificato con esistenti o generato. File PFX coppia di crittografia:

* Immettere la password di protezione delle informazioni sulla chiave private

## <a name="update-encryption-certificate-in-service-configuration-file"></a>Aggiornare il certificato di crittografia dei file di configurazione del servizio

Aggiornare il valore di identificazione personale delle seguenti impostazioni nel file di configurazione del servizio con identificazione personale del certificato di caricamento di servizio cloud:

    <Certificate name="DataEncryptionPrimary" thumbprint="" thumbprintAlgorithm="sha1" />

## <a name="common-certificate-operations"></a>Operazioni di certificato

* Configurare il certificato SSL
* Configurare i certificati di client

## <a name="find-certificate"></a>Trovare il certificato

Procedere come segue:

1. Eseguire mmc.exe.
2. File -> Aggiungi/Rimuovi Snap-in...
3. Selezionare **i certificati**.
4. Fare clic su **Aggiungi**.
5. Scegliere la posizione dell'archivio certificati.
6. Fare clic su **Fine**.
7. Fare clic su **OK**.
8. Espandere **certificati**.
9. Espandere il nodo archivio certificati.
10. Espandere il nodo figlio certificato.
11. Selezionare un certificato nell'elenco.

## <a name="export-certificate"></a>Certificato di esportazione
**Esportazione guidata di certificato**:

1. Fare clic su **Avanti**.
2. Selezionare **Sì**, quindi **esportare la chiave privata**.
3. Fare clic su **Avanti**.
4. Selezionare il formato di file di output desiderato.
5. Selezionare le opzioni desiderate.
6. Selezionare **Password**.
7. Immettere una password complessa e confermarla.
8. Fare clic su **Avanti**.
9. Digitare o selezionare un nome di file in cui archiviare il certificato (utilizzare un. Estensione PFX).
10. Fare clic su **Avanti**.
11. Fare clic su **Fine**.
12. Fare clic su **OK**.

## <a name="import-certificate"></a>Certificato di importazione

Nell'Importazione guidata certificato:

1. Selezionare la posizione dell'archivio.

    * Selezionare **l'Utente corrente** se solo i processi in esecuzione in utente corrente saranno accedere al servizio
    * Selezionare **Computer locale** se altri processi in questo computer avrà accesso il servizio
2. Fare clic su **Avanti**.
3. Se si importa da un file, verificare il percorso del file.
4. Se l'importazione di un. File PFX:
    1.     Immettere la password di protezione la chiave privata
    2.     Selezionare le opzioni di importazione
5.     Selezionare "Posizione" certificati nel seguente archivio
6.     Fare clic su **Sfoglia**.
7.     Selezionare l'archivio desiderato.
8.     Fare clic su **Fine**.
       
    * Se è stato scelto di archivio Autorità di certificazione attendibile, fare clic su **Sì**.
9.     Fare clic su **OK** in tutte le finestre di dialogo.

## <a name="upload-certificate"></a>Carica certificato

Nel [portale di Azure](https://portal.azure.com/)

1. Selezionare **servizi Cloud**.
2. Selezionare il servizio cloud.
3. Nel menu superiore fare clic su **certificati**.
4. Nella barra inferiore fare clic su **Carica**.
5. Selezionare il file di certificato.
6. Se si tratta di un. PFX file, immettere la password per la chiave privata.
7. Al termine, copiare l'identificazione di certificato digitale dalla nuova voce nell'elenco.

## <a name="other-security-considerations"></a>Altre considerazioni sulla sicurezza
 
Impostazioni SSL descritte in questo documento crittografare le comunicazioni tra il servizio e i relativi client quando viene utilizzata l'endpoint HTTPS. È importante poiché le credenziali per l'accesso al database e potenzialmente altre informazioni riservate sono contenuti nella comunicazione. Notare che il servizio viene mantenuta stato interno, incluse le credenziali, in tabelle interne nel database SQL di Microsoft Azure fornito per lo spazio di archiviazione di metadati nell'abbonamento a Microsoft Azure. Il database è stato definito come parte dell'impostazione seguente nel file di configurazione del servizio (. File CSCFG): 

    <Setting name="ElasticScaleMetadata" value="Server=…" />

Credenziali archiviate nel database vengono crittografate. Tuttavia, come ottimale, assicurarsi che i ruoli web e lavoro distribuzione servizio del vengono aggiornati e sicura man mano che si sia hanno accesso al database di metadati e il certificato usato per la crittografia e decrittografia delle credenziali archiviate. 

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
