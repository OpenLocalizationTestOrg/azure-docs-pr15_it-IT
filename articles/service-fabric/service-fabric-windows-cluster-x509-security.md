<properties
   pageTitle="Connettersi a un cluster privato sicuro | Microsoft Azure"
   description="In questo articolo viene descritto come proteggere la comunicazione all'interno del prodotto autonomo o cluster private, nonché tra i client e il cluster."
   services="service-fabric"
   documentationCenter=".net"
   authors="dsk-2015"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/08/2016"
   ms.author="dkshir"/>

# <a name="secure-a-standalone-cluster-on-windows-using-x509-certificates"></a>Proteggere un cluster autonomo in Windows certificati x. 509

In questo articolo viene descritto come proteggere le comunicazioni tra i vari nodi del cluster Windows autonoma, nonché come eseguire l'autenticazione client che si connettono a questo cluster utilizza x. 509 certificati. In questo modo che solo gli utenti autorizzati possono accedere al cluster di applicazioni ed eseguire attività di gestione.  Certificato attivata la protezione deve essere cluster quando viene creato il cluster.  

Per ulteriori informazioni sulla protezione di cluster, ad esempio nodi di sicurezza, sicurezza a nodo client e il controllo dell'accesso basato sui ruoli, vedere [scenari di protezione Cluster](service-fabric-cluster-security.md).

## <a name="which-certificates-will-you-need"></a>I certificati saranno necessarie?

Per iniziare a [scaricare il pacchetto di cluster autonomo](service-fabric-cluster-creation-for-windows-server.md#downloadpackage) su uno dei nodi del cluster. Nel pacchetto scaricato, si noterà un file **ClusterConfig.X509.MultiMachine.json** . Aprire il file e consultare la sezione di **protezione** della sezione **proprietà** :

    "security": {
        "metadata": "The Credential type X509 indicates this is cluster is secured using X509 Certificates. The thumbprint format is - d5 ec 42 3b 79 cb e5 07 fd 83 59 3c 56 b9 d5 31 24 25 42 64.",
        "ClusterCredentialType": "X509",
        "ServerCredentialType": "X509",
        "CertificateInformation": {
            "ClusterCertificate": {
                "Thumbprint": "[Thumbprint]",
                "ThumbprintSecondary": "[Thumbprint]",
                "X509StoreName": "My"
            },
            "ServerCertificate": {
                "Thumbprint": "[Thumbprint]",
                "ThumbprintSecondary": "[Thumbprint]",
                "X509StoreName": "My"
            },
            "ClientCertificateThumbprints": [{
                "CertificateThumbprint": "[Thumbprint]",
                "IsAdmin": false
            }, {
                "CertificateThumbprint": "[Thumbprint]",
                "IsAdmin": true
            }],
            "ClientCertificateCommonNames": [{
                "CertificateCommonName": "[CertificateCommonName]",
                "CertificateIssuerThumbprint" : "[Thumbprint]",
                "IsAdmin": true
            }]
            "HttpApplicationGatewayCertificate":{
                "Thumbprint": "[Thumbprint]",
                "X509StoreName": "My"
            }
        }
    }

In questa sezione descrive i certificati che è necessario per proteggere il cluster di Windows autonomo. Impostare i valori di **ClusterCredentialType** e **ServerCredentialType** per attivare la sicurezza basata su certificato *X509*.

>[AZURE.NOTE] Un' [identificazione personale](https://en.wikipedia.org/wiki/Public_key_fingerprint) è l'identità principale di un certificato. Informazioni [su come recuperarla identificazione personale di un certificato](https://msdn.microsoft.com/library/ms734695.aspx) per individuare l'identificazione personale dei certificati che si creano.

Nella tabella seguente elenca i certificati che sarà necessario nella configurazione del cluster:

|**Impostazione di CertificateInformation**|**Descrizione**|
|-----------------------|--------------------------|
|ClusterCertificate|Sono richieste per proteggere le comunicazioni tra i nodi in un cluster. È possibile utilizzare due diversi certificati, un principale e secondaria per l'aggiornamento. Impostare l'identificazione personale del certificato principale della sezione di **identificazione personale** e che quello secondario in variabili **ThumbprintSecondary** .|
|Dei certificati|Questo certificato viene presentato al client durante il tentativo di connettersi a questo cluster. Per semplicità di utilizzo, è possibile scegliere di utilizzare lo stesso certificato per *ClusterCertificate* e *dei certificati*. È possibile usare due certificati server diverso, un principale e secondaria per l'aggiornamento. Impostare l'identificazione personale del certificato principale della sezione di **identificazione personale** e che quello secondario in variabili **ThumbprintSecondary** . |
|ClientCertificateThumbprints|Si tratta di un set di attestati e certificati che si desidera installare nel client autenticati. È un numero di certificati client diverso installato nel computer in cui si desidera consentire l'accesso al cluster. Impostare l'identificazione personale di ogni certificato nella variabile **CertificateThumbprint** . Se si imposta **IsAdmin** su *true*, quindi il client con il certificato in cui è installato possibili amministratore attività di gestione nel cluster. Se **IsAdmin** è *Falso*, il client con questo certificato eseguire solo le azioni consentite per i diritti di accesso utente, in genere di sola lettura. Per ulteriori informazioni sui ruoli leggere [(RBAC) il controllo dell'accesso basato sui ruoli](service-fabric-cluster-security.md/#role-based-access-control-rbac)  |
|ClientCertificateCommonNames|Impostare il nome del certificato client prima comune per **CertificateCommonName**. **CertificateIssuerThumbprint** è l'identificazione personale per il richiedente del certificato. Leggere [utilizzano i certificati](https://msdn.microsoft.com/library/ms731899.aspx) per saperne di più nomi comuni e autorità.|
|HttpApplicationGatewayCertificate|Si tratta di un certificato facoltativo che è possibile specificare se si desidera proteggere il Gateway di applicazione Http. Assicurarsi che reverseProxyEndpointPort sia impostata in nodeTypes se si utilizza il certificato.|

Ecco configurazione cluster di esempio in cui sono disponibili i certificati Cluster, Server e Client.

 ```
 {
    "name": "SampleCluster",
    "clusterConfigurationVersion": "1.0.0",
    "apiVersion": "2016-09-26",
    "nodes": [{
        "nodeName": "vm0",
        "metadata": "Replace the localhost below with valid IP address or FQDN",
        "iPAddress": "10.7.0.5",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r0",
        "upgradeDomain": "UD0"
    }, {
      "nodeName": "vm1",
            "metadata": "Replace the localhost with valid IP address or FQDN",
        "iPAddress": "10.7.0.4",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r1",
        "upgradeDomain": "UD1"
    }, {
        "nodeName": "vm2",
      "iPAddress": "10.7.0.6",
            "metadata": "Replace the localhost with valid IP address or FQDN",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r2",
        "upgradeDomain": "UD2"
    }],
    "properties": {
        "diagnosticsStore": {
        "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
        "dataDeletionAgeInDays": "7",
        "storeType": "FileShare",
        "IsEncrypted": "false",
        "connectionstring": "c:\\ProgramData\\SF\\DiagnosticsStore"
        }
        "security": {
            "metadata": "The Credential type X509 indicates this is cluster is secured using X509 Certificates. The thumbprint format is - d5 ec 42 3b 79 cb e5 07 fd 83 59 3c 56 b9 d5 31 24 25 42 64.",
            "ClusterCredentialType": "X509",
            "ServerCredentialType": "X509",
            "CertificateInformation": {
                "ClusterCertificate": {
                    "Thumbprint": "a8 13 67 58 f4 ab 89 62 af 2b f3 f2 79 21 be 1d f6 7f 43 26",
                    "X509StoreName": "My"
                },
                "ServerCertificate": {
                    "Thumbprint": "a8 13 67 58 f4 ab 89 62 af 2b f3 f2 79 21 be 1d f6 7f 43 26",
                    "X509StoreName": "My"
                },
                "ClientCertificateThumbprints": [{
                    "CertificateThumbprint": "c4 c18 8e aa a8 58 77 98 65 f8 61 4a 0d da 4c 13 c5 a1 37 6e",
                    "IsAdmin": false
                }, {
                    "CertificateThumbprint": "71 de 04 46 7c 9e d0 54 4d 02 10 98 bc d4 4c 71 e1 83 41 4e",
                    "IsAdmin": true
                }]
            }
        },
        "reliabilityLevel": "Bronze",
        "nodeTypes": [{
            "name": "NodeType0",
            "clientConnectionEndpointPort": "19000",
            "clusterConnectionEndpoint": "19001",
            "httpGatewayEndpointPort": "19080",
            "applicationPorts": {
                "startPort": "20001",
                "endPort": "20031"
            },
            "ephemeralPorts": {
                "startPort": "20032",
                "endPort": "20062"
            },
            "isPrimary": true
        }
         ],
        "fabricSettings": [{
            "name": "Setup",
            "parameters": [{
                "name": "FabricDataRoot",
                "value": "C:\\ProgramData\\SF"
            }, {
                "name": "FabricLogRoot",
                "value": "C:\\ProgramData\\SF\\Log"
            }]
        }]
    }
}
 ```

## <a name="aquire-the-x509-certificates"></a>Acquisire x. 509 i certificati
Per proteggere le comunicazioni all'interno del cluster, è necessario innanzitutto ottenere i certificati x. 509 per i nodi cluster. Inoltre, per limitare connessione al cluster a macchine/utenti autorizzati, sarà necessario scaricare e installare certificati per i computer client.

Per i cluster che eseguono carichi di lavoro di produzione, è necessario utilizzare un' [Autorità di certificazione (CA)](https://en.wikipedia.org/wiki/Certificate_authority) firma x. 509 per proteggere il cluster. Per informazioni dettagliate su come ottenere i certificati, passare a [come: ottenere un certificato](http://msdn.microsoft.com/library/aa702761.aspx).

Per i cluster che utilizzano a scopo di test, è possibile scegliere di utilizzare un certificato autofirmato.

## <a name="optional-create-a-self-signed-certificate"></a>Facoltativo: Creare un certificato autofirmato
Un modo per creare un certificato autofirmato che può essere protetto in modo corretto consiste nell'utilizzare lo script *CertSetup.ps1* nella cartella Service tessuti SDK nella directory *C:\Programmi\Microsoft c:\Programmi\Microsoft SDKs\Service Fabric\ClusterSetup\Secure*. Modificare il file e consente di creare un certificato con un nome appropriato.

A questo punto è possibile esportare il certificato in un file PFX con una password protetta. Prima di tutto è necessario ottenere l'identificazione del certificato digitale. Eseguire l'applicazione certmgr.exe. Passare alla cartella **Locale\Personale.** e trovare il certificato che appena creato. Fare doppio clic sul certificato per aprirlo, selezionare la scheda *Dettagli* e scorrere fino a campo di *identificazione personale* . Copiare il valore di identificazione personale nel comando PowerShell seguente, rimuovere gli spazi.  Modificare il valore di *$pswd* in una password di protezione di idoneità per proteggerlo ed eseguire di PowerShell:

```   
$pswd = ConvertTo-SecureString -String "1234" -Force –AsPlainText
Get-ChildItem -Path cert:\localMachine\my\<Thumbprint> | Export-PfxCertificate -FilePath C:\mypfx.pfx -Password $pswd
```

Per visualizzare i dettagli di un certificato installato nel computer è possibile eseguire il comando PowerShell seguente:

```
$cert = Get-Item Cert:\LocalMachine\My\<Thumbprint>
Write-Host $cert.ToString($true)
```

In alternativa, se si ha un abbonamento Azure, consultare la sezione [Aggiungi certificati all'archivio di chiave](service-fabric-cluster-creation-via-arm.md#add-certificate-to-key-vault).

## <a name="install-the-certificates"></a>Installare i certificati
Dopo avere creato i certificati, è possibile installare nei nodi del cluster. I nodi devono avere la più recente di Windows PowerShell 3. x installato su di essi. Sarà necessario ripetere questi passaggi per ogni nodo per i certificati secondari e certificati Cluster e Server.

1. Copiare i file. pfx al livello di nodo.
2. Aprire una finestra di PowerShell come amministratore e immettere i comandi seguenti. Sostituire *$pswd* con la password utilizzati per creare il certificato. Sostituire *$PfxFilePath* con il percorso completo del PFX copiato a questo nodo.

    ```
    $pswd = "1234"
    $PfcFilePath ="C:\mypfx.pfx"
    Import-PfxCertificate -Exportable -CertStoreLocation Cert:\LocalMachine\My -FilePath $PfxFilePath -Password (ConvertTo-SecureString -String $pswd -AsPlainText -Force)
    ```

3. Successivamente è necessario impostare il controllo di accesso del certificato in modo che il processo di servizio tessuti, viene eseguito con l'account del servizio di rete, usarla eseguendo il seguente script. Fornire l'identificazione personale del certificato e "Servizio di rete" per l'account di servizio. È possibile verificare che siano corrette utilizzando lo strumento certmgr.exe ed esamina Gestisci chiavi Private nel certificato ACL sul certificato.

    ```
    param
    (
        [Parameter(Position=1, Mandatory=$true)]
        [ValidateNotNullOrEmpty()]
        [string]$pfxThumbPrint,

        [Parameter(Position=2, Mandatory=$true)]
        [ValidateNotNullOrEmpty()]
        [string]$serviceAccount
        )

        $cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object -FilterScript { $PSItem.ThumbPrint -eq $pfxThumbPrint; };

        # Specify the user, the permissions and the permission type
        $permission = "$($serviceAccount)","FullControl","Allow"
        $accessRule = New-Object -TypeName System.Security.AccessControl.FileSystemAccessRule -ArgumentList $permission;

        # Location of the machine related keys
        $keyPath = $env:ProgramData + "\Microsoft\Crypto\RSA\MachineKeys\";
        $keyName = $cert.PrivateKey.CspKeyContainerInfo.UniqueKeyContainerName;
        $keyFullPath = $keyPath + $keyName;

        # Get the current acl of the private key
        $acl = (Get-Item $keyFullPath).GetAccessControl('Access')

        # Add the new ace to the acl of the private key
        $acl.SetAccessRule($accessRule);

        # Write back the new acl
        Set-Acl -Path $keyFullPath -AclObject $acl -ErrorAction Stop

        #Observe the access rights currently assigned to this certificate.
        get-acl $keyFullPath| fl
        ```

4. Repeat the steps above for each server certificate. You can also use these steps to install the client certificates on the machines that you want to allow access to the cluster.

## Create the secure cluster
After configuring the **security** section of the **ClusterConfig.X509.MultiMachine.json** file, you can proceed to [Create your cluster](service-fabric-cluster-creation-for-windows-server.md#createcluster) section to configure the nodes and create the standalone cluster. Remember to use the **ClusterConfig.X509.MultiMachine.json** file while creating the cluster. For example, your command might look like the following:

```
.\CreateServiceFabricCluster.ps1 - ClusterConfigFilePath.\ClusterConfig.X509.MultiMachine.json - MicrosoftServiceFabricCabFilePath.\MicrosoftAzureServiceFabric.cab - AcceptEULA $true
```

Once you have the secure standalone Windows cluster successfully running, and have setup the authenticated clients to connect to it, follow the section [Connect to a secure cluster using PowerShell](service-fabric-connect-to-secure-cluster.md#connectsecurecluster) to connect to it. For example:

```
Connettere ServiceFabricCluster - ConnectionEndpoint 10.7.0.4:19000 - KeepAliveIntervalInSec 10 - X509Credential - ServerCertThumbprint 057b9544a6f2733e0c8d3a60013a58948213f551 - FindType FindByThumbprint - FindValue 057b9544a6f2733e0c8d3a60013a58948213f551 - StoreLocation CurrentUser - StoreName personale
```

If you are logged on to one of the machines in the cluster, since this already has the certificate installed locally you can simply run the Powershell command to connect to the cluster and show a list of nodes:

```
Connettere ServiceFabricCluster Get-ServiceFabricNode
```
To remove the cluster call the following command:

```
.\RemoveServiceFabricCluster.ps1 - ClusterConfigFilePath.\ClusterConfig.X509.MultiMachine.json - MicrosoftServiceFabricCabFilePath.\MicrosoftAzureServiceFabric.cab
```
