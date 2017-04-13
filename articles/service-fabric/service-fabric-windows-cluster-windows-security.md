<properties
   pageTitle="Proteggere un cluster che esegue Windows la sicurezza di Windows | Microsoft Azure"
   description="Informazioni su come configurare la protezione a nodi e a nodo client in un cluster di autonoma in esecuzione su Windows la sicurezza di Windows."
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/25/2016"
   ms.author="ryanwi"/>


# <a name="secure-a-standalone-cluster-on-windows-using-windows-security"></a>Proteggere un cluster autonomo in Windows la sicurezza di Windows

Per impedire l'accesso non autorizzato a un cluster di servizio tessuti è necessario proteggere, soprattutto se dispone di carichi di lavoro di produzione in esecuzione su di esso. In questo articolo viene descritto come configurare la protezione a nodi e nodo client per la sicurezza di Windows nel file *ClusterConfig.JSON* e corrisponde al passaggio Configura sicurezza della [Crea un cluster autonoma in esecuzione su Windows](service-fabric-cluster-creation-for-windows-server.md). Per ulteriori informazioni su come servizio tessuti utilizza la sicurezza di Windows, vedere [scenari di protezione Cluster](service-fabric-cluster-security.md).

>[AZURE.NOTE]
Considerare la selezione di sicurezza per la protezione a nodi con attenzione, poiché esiste un aggiornamento cluster dalla scelta di un titolo a un altro. Modificare la selezione di protezione sarà necessario ricostruire cluster completo.

## <a name="configure-windows-security"></a>Configurare la sicurezza di Windows
File di configurazione di esempio *ClusterConfig.Windows.JSON* vengono scaricati con [Microsoft.Azure.ServiceFabric.WindowsServer.<version>. CAP](http://go.microsoft.com/fwlink/?LinkId=730690) autonoma cluster pacchetto contiene un modello per la configurazione di sicurezza di Windows.  Sicurezza di Windows è configurata nella sezione **proprietà** :

```
"security": {
            "ClusterCredentialType": "Windows",
            "ServerCredentialType": "Windows",
            "WindowsIdentities": {
        "ClusterIdentity" : "[domain\machinegroup]",
                "ClientIdentities": [{
                    "Identity": "[domain\username]",
                    "IsAdmin": true
                }]
            }
        }
```

|**Impostazione di configurazione**|**Descrizione**|
|-----------------------|--------------------------|
|ClusterCredentialType|Sicurezza di Windows è abilitata impostando il parametro **ClusterCredentialType** su *Windows*.|
|ServerCredentialType|Sicurezza di Windows per i client viene attivata impostando il parametro **ServerCredentialType** per *Windows*. Indica che il client e cluster, cluster sono in esecuzione all'interno di un dominio Active Directory.|
|WindowsIdentities|Contiene le identità cluster e client.|
|ClusterIdentity|Configura la protezione a nodi. Un elenco delimitato da virgole del gruppo gestite account del servizio o i nomi dei computer.|
|ClientIdentities|Configura nodo client per la protezione. Matrice di account utente client.|
|Identità|L'identità del client, un utente del dominio.|
|IsAdmin|True specifica che l'utente del dominio è disponibile l'accesso come amministratore client false per l'accesso degli utenti client.|

[Nodo sicurezza nodo](service-fabric-cluster-security.md#node-to-node-security) è configurato con impostazione utilizzando **ClusterIdentity**. Per creare relazioni tra i nodi, è necessario comunicare tra loro. Questa operazione può essere eseguita in due modi diversi: specificare il gruppo gestite Account di servizio che include tutti i nodi del cluster o specificare le identità del nodo di dominio di tutti i nodi del cluster. È consigliabile utilizzare l'approccio [Gruppo gestite degli Account del servizio (gMSA)](https://technet.microsoft.com/library/hh831782.aspx) , in particolare per i cluster di grandi dimensioni (più di 10 nodi) o per i cluster che potrebbero Ingrandisci o Riduci.
Questo approccio consente di aggiungere o rimuovere da gMSA, senza apportare modifiche al manifesto cluster nodi. Questo approccio non richiede la creazione di un gruppo di dominio per cui sono state concesse amministratori del cluster diritti di accesso per aggiungere e rimuovere membri. Per ulteriori informazioni, vedere [Guida introduttiva di gruppo gestiti gli account di servizio](http://technet.microsoft.com/library/jj128431.aspx).

[Client di protezione nodo](service-fabric-cluster-security.md#client-to-node-security) viene configurato tramite **ClientIdentities**. Per stabilire l'attendibilità tra un client e il cluster, è necessario configurare il cluster per sapere quale client identità sono attendibili. Questa operazione può essere eseguita in due modi diversi: specificare gli utenti di gruppo di dominio che è possono connettersi o specificare gli utenti di nodo di dominio in grado di connettersi. Servizio tessuti supporta due tipi di controllo di accesso diverso per i client connessi a un cluster di servizio tessuti: degli amministratori e utenti. Controllo dell'accesso offre la possibilità per l'amministratore cluster limitare l'accesso a determinati tipi di operazioni cluster per diversi gruppi di utenti, rendere più sicuri cluster.  Gli amministratori hanno accesso completo alle funzionalità di gestione (incluse funzionalità di lettura/scrittura). Gli utenti, per impostazione predefinita, hanno solo accesso in lettura alla funzionalità di gestione (ad esempio le funzionalità di query) e la possibilità di risolvere applicazioni e servizi.

La sezione di **protezione** di esempio seguente consente di configurare la sicurezza di Windows e specifica che il computer in *ServiceFabric/clusterA.contoso.com* fanno parte del cluster e che *CONTOSO\usera* abbia accesso client di amministratore:

```
"security": {
    "ClusterCredentialType": "Windows",
    "ServerCredentialType": "Windows",
    "WindowsIdentities": {
        "ClusterIdentity" : "ServiceFabric/clusterA.contoso.com",
        "ClientIdentities": [{
            "Identity": "CONTOSO\\usera",
        "IsAdmin": true
        }]
    }
},
```

## <a name="next-steps"></a>Passaggi successivi

Dopo aver configurato la sicurezza di Windows nel file *ClusterConfig.JSON* , riprendere il processo di creazione cluster in [Crea un cluster autonoma in esecuzione su Windows](service-fabric-cluster-creation-for-windows-server.md).

Per ulteriori informazioni su come nodi di sicurezza, sicurezza a nodo client e controllo dell'accesso basato sui ruoli, vedere [scenari di protezione Cluster](service-fabric-cluster-security.md).

Per esempi di connessione tramite PowerShell o FabricClient, vedere [connettersi a un cluster sicuro](service-fabric-connect-to-secure-cluster.md) .
