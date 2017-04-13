<properties
   pageTitle="Aggiornare un cluster di Azure servizio tessuti | Microsoft Azure"
   description="Aggiornare il codice servizio tessuti e/o una configurazione che viene eseguito un cluster di servizio tessuti, inclusa l'impostazione delle modalità di aggiornamento cluster, aggiornamento dei certificati, aggiunta di applicazione porte, procedere come patch del sistema operativo, e così via. Che cosa è possibile aspettarsi quando vengono eseguiti gli aggiornamenti?"
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/10/2016"
   ms.author="chackdan"/>


# <a name="upgrade-an-azure-service-fabric-cluster"></a>Aggiornare un cluster di Azure servizio tessuti

> [AZURE.SELECTOR]
- [Cluster Azure](service-fabric-cluster-upgrade.md)
- [Cluster autonomo](service-fabric-cluster-upgrade-windows-server.md)

Per i sistemi moderno, progettazione per l'aggiornamento è fondamentale per ottenere il successo a lungo termine del prodotto. Un cluster di Azure servizio tessuti rappresenta una risorsa che il proprietario, ma in parte gestita da Microsoft. In questo articolo vengono illustrati cosa viene gestito automaticamente e quali è possibile configurare personalmente.

## <a name="controlling-the-fabric-version-that-runs-on-your-cluster"></a>Controllare la versione di tessuti che viene eseguito il cluster

È possibile impostare il cluster per ricevere gli aggiornamenti automatici tessuti, quando Microsoft rilascia una nuova versione o selezionare una versione supportata tessuti si desidera che il cluster essere acceso.

A tale scopo, impostare la configurazione di cluster "upgradeMode" nel portale di o Gestione risorse al momento della creazione o versioni successive su un cluster di live 

>[AZURE.NOTE] Assicurarsi di mantenere i cluster che esegue una versione supportata tessuti sempre. Come e quando le ultime del rilascio di una nuova versione di tessuti servizio, la versione precedente è contrassegnata per fine del supporto tecnico dopo un minimo di 60 giorni che vanno dalla data. le nuove versioni siano annunciata [nel blog del team servizio tessuti](https://blogs.msdn.microsoft.com/azureservicefabric/ ). La nuova versione è disponibile per scegliere quindi. 

14 giorni prima della scadenza della versione che il cluster è in esecuzione, un evento di stato viene generato che inserisce il cluster in uno stato di integrità di avviso. Il cluster rimane in stato di avviso fino a quando non si esegue l'aggiornamento a una versione supportata tessuti.


### <a name="setting-the-upgrade-mode-via-portal"></a>Impostazione della modalità di aggiornamento tramite portale 

Quando si desidera creare il cluster, è possibile impostare il cluster su automatico o manuale.

![Create_Manualmode][Create_Manualmode]

È possibile impostare il cluster su automatico o manuale quando in un cluster live, usando l'esperienza di gestione. 

#### <a name="upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-portal"></a>L'aggiornamento a una nuova versione in un cluster che è impostata sulla modalità manuale tramite portale.
 
Per eseguire l'aggiornamento a una nuova versione, è sufficiente è selezionare la versione disponibile nell'elenco a discesa e salvare. L'aggiornamento di tessuti viene avviata automaticamente. Criteri di integrità cluster (una combinazione di nodo salute e l'integrità tutte le applicazioni in esecuzione nel cluster) rispettati durante l'aggiornamento.

Se non vengono soddisfatti i criteri di integrità cluster, viene eseguito l'aggiornamento. Scorrere verso il basso in questo documento per ulteriori informazioni su come impostare i criteri di integrità personalizzato. 

Dopo aver risolto i problemi che hanno generato il ripristino, è necessario avviare di nuovo l'aggiornamento seguendo la stessa procedura descritta in precedenza.

![Manage_Automaticmode][Manage_Automaticmode]

### <a name="setting-the-upgrade-mode-via-a-resource-manager-template"></a>Impostazione della modalità di aggiornamento tramite un modello di Manager delle risorse 

Aggiungere la configurazione di "upgradeMode" per la definizione di risorse Microsoft.ServiceFabric/clusters e impostare "clusterCodeVersion" su una delle versioni tessuti supportati, come illustrato di seguito e distribuire il modello. I valori validi per "upgradeMode" sono "Manuale" o "Automatico"
 
![ARMUpgradeMode][ARMUpgradeMode]

#### <a name="upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-a-resource-manager-template"></a>L'aggiornamento a una nuova versione in un cluster che è impostata sulla modalità manuale tramite un modello di Manager delle risorse.
 
Quando il cluster è in modalità manuale, per eseguire l'aggiornamento a una nuova versione, modificare "clusterCodeVersion" a una versione supportata e distribuirlo. La distribuzione del modello, viene attivato dell'aggiornamento tessuti viene avviata automaticamente. Criteri di integrità cluster (una combinazione di nodo salute e l'integrità tutte le applicazioni in esecuzione nel cluster) rispettati durante l'aggiornamento.

Se non vengono soddisfatti i criteri di integrità cluster, viene eseguito l'aggiornamento. Scorrere verso il basso in questo documento per ulteriori informazioni su come impostare i criteri di integrità personalizzato. 

Dopo aver risolto i problemi che hanno generato il ripristino, è necessario avviare di nuovo l'aggiornamento seguendo la stessa procedura descritta in precedenza.

### <a name="get-list-of-all-available-version-for-all-environments-for-a-given-subscription"></a>Ottenere l'elenco di tutte le versioni disponibili per tutti gli ambienti per una sottoscrizione specificato

Eseguire il comando seguente e viene visualizzato un output simile alla seguente.

"supportExpiryUtc" indica quando una determinata release in scadenza o è scaduto. La versione più recente non dispone di una data valida: è un valore di "9999-12-31T23:59:59.9999999", che significa semplicemente che la data di scadenza non è ancora impostata.

```REST
GET https://<endpoint>/subscriptions/{{subscriptionId}}/providers/Microsoft.ServiceFabric/clusterVersions?api-version= 2016-09-01

Output:
{
                  "value": [
                    {
                      "id": "subscriptions/35349203-a0b3-405e-8a23-9f1450984307/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/5.0.1427.9490",
                      "name": "5.0.1427.9490",
                      "type": "Microsoft.ServiceFabric/environments/clusterVersions",
                      "properties": {
                        "codeVersion": "5.0.1427.9490",
                        "supportExpiryUtc": "2016-11-26T23:59:59.9999999",
                        "environment": "Windows"
                      }
                    },
                    {
                      "id": "subscriptions/35349203-a0b3-405e-8a23-9f1450984307/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/4.0.1427.9490",
                      "name": "5.1.1427.9490",
                      "type": " Microsoft.ServiceFabric/environments/clusterVersions",
                      "properties": {
                        "codeVersion": "5.1.1427.9490",
                        "supportExpiryUtc": "9999-12-31T23:59:59.9999999",
                        "environment": "Windows"
                      }
                    },
                    {
                      "id": "subscriptions/35349203-a0b3-405e-8a23-9f1450984307/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/4.4.1427.9490",
                      "name": "4.4.1427.9490",
                      "type": " Microsoft.ServiceFabric/environments/clusterVersions",
                      "properties": {
                        "codeVersion": "4.4.1427.9490",
                        "supportExpiryUtc": "9999-12-31T23:59:59.9999999",
                        "environment": "Linux"
                      }
                    }
                  ]
                }


```

## <a name="fabric-upgrade-behavior-when-the-cluster-upgrade-mode-is-automatic"></a>Comportamento di aggiornamento tessuti quando cluster modalità di aggiornamento automatico

Microsoft mantiene il codice di tessuti e configurazione che viene eseguita in un cluster di Azure. Abbiamo eseguire aggiornamenti automatici del monitorate al software in base alle esigenze. Questi aggiornamenti potrebbero essere codice, configurazione o entrambe. Per assicurarsi che l'applicazione non presenta Nessun impatto o un impatto minimo a causa di questi aggiornamenti, si eseguono gli aggiornamenti in fasi seguenti:

### <a name="phase-1-an-upgrade-is-performed-by-using-all-cluster-health-policies"></a>Fase 1: Con tutti i criteri di integrità cluster viene eseguito un aggiornamento

In questa fase gli aggiornamenti procedere un dominio di eseguire l'aggiornamento alla volta e le applicazioni che sono state eseguite nel cluster continuano per l'esecuzione senza alcuna interruzione. Criteri di integrità cluster (una combinazione di nodo salute e l'integrità tutte le applicazioni in esecuzione nel cluster) rispettati durante l'aggiornamento.

Se non vengono soddisfatti i criteri di integrità cluster, viene eseguito l'aggiornamento. Non viene inviato un messaggio di posta elettronica al proprietario della sottoscrizione. Messaggio di posta elettronica contiene le informazioni seguenti:

- Notifica di avere ripristinare un aggiornamento cluster.
- Suggeriti correttive, se presenti.
- Il numero di giorni che vanno (n) fino a quando non viene eseguita la fase 2.

Si tenta di eseguire l'aggiornamento stesso alcune volte nel caso in cui gli eventuali aggiornamenti non riuscito per motivi di infrastruttura. Dopo n giorni dalla data di che messaggio di posta elettronica è stato inviato, passare alla fase 2.

Se vengono soddisfatti i criteri di integrità cluster, l'aggiornamento è considerata come eseguita e contrassegnato come completato. Questo problema può verificarsi durante l'aggiornamento iniziale o una qualsiasi delle repliche aggiornamento in questa fase. Da alcun messaggio di posta elettronica di esecuzione. In modo da evitare l'invio di si troppi messaggi di posta elettronica, ossia la ricezione di un messaggio di posta elettronica deve essere considerato di un'eccezione normale. Si prevede che la maggior parte degli aggiornamenti cluster venga eseguita correttamente senza influenzare la disponibilità delle applicazioni.

### <a name="phase-2-an-upgrade-is-performed-by-using-default-health-policies-only"></a>Fase 2: Viene eseguito un aggiornamento utilizzando solo i criteri di integrità predefinito

I criteri di integrità in questa fase sono impostati in modo che il numero di applicazioni che erano integro all'inizio dell'aggiornamento rimane inalterato per la durata del processo di aggiornamento. Come fase 1, gli aggiornamenti fase 2 procedere un dominio di eseguire l'aggiornamento alla volta e le applicazioni che sono state eseguite nel cluster continuano per l'esecuzione senza alcuna interruzione. Criteri di integrità cluster (una combinazione di nodo salute e l'integrità tutte le applicazioni in esecuzione nel cluster) vengano rispettati i per la durata dell'aggiornamento.

Se i criteri di integrità cluster valide non vengono soddisfatti, viene eseguito l'aggiornamento. Non viene inviato un messaggio di posta elettronica al proprietario della sottoscrizione. Messaggio di posta elettronica contiene le informazioni seguenti:

- Notifica di avere ripristinare un aggiornamento cluster.
- Suggeriti correttive, se presenti.
- Il numero di giorni che vanno (n) fino a quando non viene eseguita fase 3.

Si tenta di eseguire l'aggiornamento stesso alcune volte nel caso in cui gli eventuali aggiornamenti non riuscito per motivi di infrastruttura. Un messaggio di posta elettronica promemoria viene inviato un paio di giorni che vanno prima siano n giorni. Dopo n giorni dalla data di che messaggio di posta elettronica è stato inviato, passare alla fase 3. I messaggi di posta elettronica che verrà inviata nella fase 2 devono essere prese gravemente e correttive devono essere prese.

Se vengono soddisfatti i criteri di integrità cluster, l'aggiornamento è considerata come eseguita e contrassegnato come completato. Questo problema può verificarsi durante l'aggiornamento iniziale o una qualsiasi delle repliche aggiornamento in questa fase. Da alcun messaggio di posta elettronica di esecuzione.

### <a name="phase-3-an-upgrade-is-performed-by-using-aggressive-health-policies"></a>Fase 3: Tramite criteri di integrità aggressive viene eseguito un aggiornamento

Questi criteri di integrità in questa fase sono rivolto completamento dell'aggiornamento piuttosto che lo stato delle applicazioni. Solo alcuni aggiornamenti cluster finiscano in questa fase. Se il cluster ottiene in questa fase, è probabile che l'applicazione assume la forma non corretti e/o perdere disponibilità.

Simile a due fasi, fase 3 aggiornamenti procedere con un unico dominio aggiornamento contemporaneamente.

Se non vengono soddisfatti i criteri di integrità cluster, viene eseguito l'aggiornamento. Si tenta di eseguire l'aggiornamento stesso alcune volte nel caso in cui gli eventuali aggiornamenti non riuscito per motivi di infrastruttura. Successivamente, il cluster è bloccato, in modo che possa non è più ricevere supporto e/o aggiornamenti.

Un messaggio di posta elettronica con queste informazioni verrà inviata al proprietario della sottoscrizione, insieme a operazioni correttive. Non si prevede qualsiasi cluster per ottenere in uno stato in cui non è riuscita fase 3.

Se vengono soddisfatti i criteri di integrità cluster, l'aggiornamento è considerata come eseguita e contrassegnato come completato. Questo problema può verificarsi durante l'aggiornamento iniziale o una qualsiasi delle repliche aggiornamento in questa fase. Da alcun messaggio di posta elettronica di esecuzione.

## <a name="cluster-configurations-that-you-control"></a>Configurazioni cluster che è possibile controllare

Oltre la possibilità di impostare il cluster modalità di aggiornamento, di seguito sono riportate le configurazioni che è possibile modificare in un cluster di live.

### <a name="certificates"></a>Certificati

È possibile aggiungere nuovi o eliminare con facilità i certificati per il cluster e il client tramite il portale. Fare riferimento al [documento per informazioni dettagliate](service-fabric-cluster-security-update-certs-azure.md)

![Schermata che mostra identificazioni personali del certificato nel portale di Azure.][CertificateUpgrade]


### <a name="application-ports"></a>Porte applicazione

È possibile modificare le porte applicazione modificando le proprietà delle risorse di bilanciamento del carico associate al tipo di nodo. È possibile utilizzare il portale oppure è possibile utilizzare Gestione risorse PowerShell direttamente.

Per aprire una nuova porta in macchine virtuali tutti in un tipo di nodo, eseguire le operazioni seguenti:

1. Aggiungere un nuovo sondaggio al servizio di bilanciamento del carico appropriato.

    Se è stato distribuito il cluster tramite il portale, servizi di bilanciamento del carico sono denominate "Kg-nome di risorsa gruppo-NodeTypename", uno per ogni tipo di nodo. Poiché i nomi di bilanciamento del carico sono univoci solo all'interno di un gruppo di risorse, è consigliabile se la ricerca in un determinato gruppo di risorse.

    ![Schermata che illustra l'aggiunta di una ricerca per un bilanciamento del carico nel portale.][AddingProbes]

2. Aggiungere una nuova regola per il servizio di bilanciamento del carico.

    Aggiungere una nuova regola di bilanciamento stesso utilizzando la ricerca creato nel passaggio precedente.

    ![Aggiunta di una nuova regola per un bilanciamento del carico nel portale.][AddingLBRules]


### <a name="placement-properties"></a>Proprietà della posizione

Per ogni tipo di nodo, è possibile aggiungere proprietà personalizzata della posizione che si desidera utilizzare nelle applicazioni. Tipo di nodo è una proprietà predefinita che è possibile utilizzare senza aggiungerlo in modo esplicito.

>[AZURE.NOTE] Per informazioni dettagliate sull'uso dei vincoli di posizionamento, le proprietà del nodo e su come essere definiti dall'utente, vedere la sezione "Posizionamento vincoli e proprietà del nodo" nel documento Manager delle risorse Cluster tessuti di servizio in [Cui vengono descritti il Cluster](service-fabric-cluster-resource-manager-cluster-description.md).

### <a name="capacity-metrics"></a>Metrica capacità

Per ogni tipo di nodo, è possibile aggiungere metriche capacità personalizzato che si desidera utilizzare nelle applicazioni per caricare report. Per informazioni dettagliate sull'utilizzo di metriche capacità a report caricare, consultare la gestione di risorse Cluster tessuti servizio [Che descrive il Cluster](service-fabric-cluster-resource-manager-cluster-description.md) e [metriche e carica](service-fabric-cluster-resource-manager-metrics.md).

### <a name="fabric-upgrade-settings---health-polices"></a>Impostazioni di aggiornamento tessuti - integrità dei criteri

È possibile specificare criteri dello stato personalizzato per l'aggiornamento di tessuti. Se si impostano i cluster agli aggiornamenti automatici tessuti, questi criteri venga applicati alla fase 1 degli aggiornamenti automatici tessuti.
Se si impostano i cluster per tessuti manuali degli aggiornamenti, questi criteri venga applicati ogni volta che si seleziona una nuova versione attivare il sistema per avviare l'aggiornamento di tessuti del cluster. Se non si esegue l'override di criteri, vengono utilizzate le impostazioni predefinite.

È possibile specificare i criteri di stato personalizzati o rivedere le impostazioni correnti in e il "aggiornamento tessuti", selezionando le impostazioni di aggiornamento avanzate. Esaminare come nella figura seguente. 

![Gestire i criteri di stato personalizzati][HealthPolices]

### <a name="customize-fabric-settings-for-your-cluster"></a>Personalizzare le impostazioni di tessuti per il cluster

Fare riferimento alle [impostazioni del servizio tessuti cluster tessuti](service-fabric-cluster-fabric-settings.md) su cosa e come è possibile personalizzare.

### <a name="os-patches-on-the-vms-that-make-up-the-cluster"></a>Patch del sistema operativo in macchine virtuali che costituiscono il cluster

Questa funzionalità è pianificata per il futuro come una funzione automatica. Ma al momento è responsabile per patch nelle macchine virtuali. È necessario ripetere questa macchina uno virtuale alla volta, in modo che non si effettua verso il basso più di uno alla volta.

### <a name="os-upgrades-on-the-vms-that-make-up-the-cluster"></a>Aggiornamenti del sistema operativo in macchine virtuali che costituiscono il cluster

Se è necessario aggiornare l'immagine del sistema operativo in macchine virtuali del cluster, è necessario farlo contemporaneamente una macchina virtuale. Sei responsabile per l'aggiornamento, non è al momento alcun tipo di automazione per l'oggetto.

## <a name="next-steps"></a>Passaggi successivi
- Informazioni su come personalizzare alcune delle [impostazioni del servizio tessuti cluster tessuti](service-fabric-cluster-fabric-settings.md)
- Informazioni su come [ridurre i cluster avanti e indietro](service-fabric-cluster-scale-up-down.md)
- Informazioni sugli [Aggiornamenti applicazione](service-fabric-application-upgrade.md)

<!--Image references-->
[CertificateUpgrade]: ./media/service-fabric-cluster-upgrade/CertificateUpgrade2.png
[AddingProbes]: ./media/service-fabric-cluster-upgrade/addingProbes2.PNG
[AddingLBRules]: ./media/service-fabric-cluster-upgrade/addingLBRules.png
[HealthPolices]: ./media/service-fabric-cluster-upgrade/Manage_AutomodeWadvSettings.PNG
[ARMUpgradeMode]: ./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG
[Create_Manualmode]: ./media/service-fabric-cluster-upgrade/Create_Manualmode.PNG
[Manage_Automaticmode]: ./media/service-fabric-cluster-upgrade/Manage_Automaticmode.PNG