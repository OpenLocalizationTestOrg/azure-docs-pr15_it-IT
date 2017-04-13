<properties
   pageTitle="Aggiungere l'automazione Azure runbook ai piani di ripristino | Microsoft Azure"
   description="In questo articolo descrive come il ripristino del sito di Azure consente ora di estendere piani di ripristino utilizzando l'automazione di Azure per completare le attività complesse durante il ripristino di Azure"
   services="site-recovery"
   documentationCenter=""
   authors="ruturaj"
   manager="gauravd"
   editor=""/>

<tags
   ms.service="site-recovery"
   ms.devlang="powershell"
   ms.tgt_pltfrm="na"
   ms.topic="article"
   ms.workload="required"
   ms.date="10/23/2016"
   ms.author="ruturajd@microsoft.com"/>


# <a name="add-azure-automation-runbooks-to-recovery-plans"></a>Aggiungere l'automazione Azure runbook ai piani di ripristino


In questa esercitazione viene illustrato il ripristino del sito di Azure si integra con automazione Azure per fornire estensibilità ai piani di ripristino. Piani di ripristino possano progettare il ripristino di macchine virtuali è protetto con il ripristino del sito di Azure per replica cloud secondario e replica di Azure scenari. Possono inoltre aiutare ripristino **costanti e precise**, **ripetibile**e **automatica**. Se non riescono su macchine virtuali di Azure, integrazione con Azure automazione estende i piani di ripristino e consente l'esecuzione delle runbook, in modo da consentire attività di automazione potenti.

Se non ascoltati sull'automazione di Azure ancora, iscriversi [qui](https://azure.microsoft.com/services/automation/) e scaricare gli script di esempio [qui](https://azure.microsoft.com/documentation/scripts/). Leggere altre informazioni sul [Ripristino del sito di Azure](https://azure.microsoft.com/services/site-recovery/) e su come gestire il ripristino Azure utilizzando piani di ripristino [di seguito](https://azure.microsoft.com/blog/?p=166264).

In questa esercitazione, esamineremo come è possibile integrare Azure automazione runbook piani di ripristino. Verrà automatizzare attività semplici che in precedenza necessari interventi manuali e procedura convertire un ripristino più passaggio in un'operazione di ripristino fare clic su singola. È inoltre esaminare le modalità di risoluzione un semplice script in caso di errato.

## <a name="customize-the-recovery-plan"></a>Personalizzare il piano di ripristino

1. Automatica innanzitutto operning e il risorse del piano di ripristino. È possibile visualizzare il piano di ripristino di due macchine virtuali aggiunte per il ripristino. 

    ![](media/site-recovery-runbook-automation-new/essentials-rp.PNG)
---------------------

2. Fare clic sul pulsante Personalizza per iniziare ad aggiungere un runbook. Verrà aperto il piano di ripristino personalizzare blade.


    ![](media/site-recovery-runbook-automation-new/customize-rp.PNG)


3. Fare clic con il pulsante destro sul gruppo di inizio 1 e selezionare questa opzione per aggiungere un 'azione Aggiungi post'.

4. Selezionare questa opzione per scegliere uno script in e il nuovo.

5. Assegnare un nome lo script 'Hello World'.

6. Scegliere il nome di un Account di automazione. Questo è l'account Azure automazione. Si noti che l'account può essere in qualsiasi geography Azure ma deve essere nello stesso abbonamento come archivio il ripristino del sito.

7. Selezionare un runbook dall'Account di automazione. Si tratta di uno script che verrà eseguita durante l'esecuzione del piano di ripristino dopo il ripristino del primo gruppo.

    ![](media/site-recovery-runbook-automation-new/update-rp.PNG)


8. Fare clic su OK per salvare lo script. Lo script verrà aggiunto al gruppo di azione post del gruppo 1: avviare.


    ![](media/site-recovery-runbook-automation-new/addedscript-rp.PNG)


## <a name="salient-points-of-adding-a-script"></a>Punti salienti dell'aggiunta di uno script

1. È possibile fare clic con il pulsante destro sullo script e scegliere 'delete passaggio' o 'aggiornare script'.

2. Uno script possono essere eseguiti sui Azure durante failover locali in Azure e possono essere eseguiti sui Azure come uno script sul lato principale prima dell'arresto durante failback da Azure nella distribuzione locale.

3. Quando viene eseguito uno script, inserirà un contesto di piano di ripristino.

Di seguito è illustrato un esempio dell'aspetto che avrà la variabile di contesto.

        {"RecoveryPlanName":"hrweb-recovery",

        "FailoverType":"Test",

        "FailoverDirection":"PrimaryToSecondary",

        "GroupId":"1",

        "VmMap":{"7a1069c6-c1d6-49c5-8c5d-33bfce8dd183":

                {"CloudServiceName":"pod02hrweb-Chicago-test",

                "RoleName":"Fabrikam-Hrweb-frontend-test"}

                }

        }


Nella tabella seguente contiene nome e una descrizione per ogni variabile nel contesto.

**Nome della variabile** | **Descrizione**
---|---
RecoveryPlanName | Nome del piano in esecuzione. Consente di eseguire azioni in base al nome con lo stesso script
FailoverType | Specifica se è testare il failover, pianificato o non pianificato.
FailoverDirection | Specificare se il ripristino su principale o secondario
ID del gruppo | Individuare il numero del gruppo all'interno del piano di ripristino quando il piano è in esecuzione
VmMap | Matrice di tutte le macchine virtuali di gruppo
Chiave VMMap | Chiave univoca (GUID) per ogni macchina virtuale. È diverso da quello di ID VMM della macchina virtuale eventualmente.
RoleName | Nome della macchina virtuale Azure quale viene recuperata
CloudServiceName | Nome servizio Cloud Azure in cui viene creata la macchina virtuale.
CloudServiceName (nel modello di distribuzione Manager delle risorse) | Nome del gruppo di risorse Azure in cui viene creata la macchina virtuale.


## <a name="using-complex-variables-per-recovery-plan"></a>Utilizzo di variabili complesso al piano di ripristino

In alcuni casi, un runbook richiede più informazioni solo il RecoveryPlanContext. Non esiste alcun modo prima classe per passare un parametro a una runbook. Tuttavia, se si desidera utilizzare lo stesso script tramite più piani di ripristino è possibile utilizzare la variabile di contesto di piano di ripristino 'RecoveryPlanName' e usare il sotto sperimentazione tecnica di utilizzare una variabile di complesso Azure automazione in un runbook. Nell'esempio seguente mostra come è possibile creare tre diversi complesso variabile beni e usarli in runbook in base al nome del piano di ripristino.

Tenere presente che si desidera utilizzare 3 parametri aggiuntivi in un runbook. Automatica li codifica in una maschera JSON {"Var1": "testautomation", "Var2": "Non pianificato", "Var3": "PrimaryToSecondary"}

Consente di creare una nuova attività di automazione [AA complesso variabile](../automation/automation-variables.md#variable-types Complex variable) .
Denominare la variabile come <RecoveryPlanName>- parametri.
È possibile utilizzare il riferimento di seguito per creare una [variabile complesso](https://msdn.microsoft.com/library/dn913767.aspx?f=255&MSPPError=-2147217396).

Per i piani di ripristino diverso, la variabile come assegnare un nome

1. recoveryPlanName1 >-parametri
2. recoveryPlanName2 >-parametri
3. recoveryPlanName3 >-parametri

A questo punto, nello script fanno riferimento a parametri come

1. È possibile ottenere il nome RP dalla $rpname = $Recoveryplancontext variabile
2. Ottenere risorse di $paramValue = "$($rpname) parametri"
3. Utilizzarla come una variabile di tipo complesso per il piano di ripristino chiamando Get-AzureAutomationVariable [-AutomationAccountName] <String> -nome $paramValue.

Ad esempio, per ottenere il complesso variabile/parametro per il piano di ripristino SharepointApp, creare una variabile di complesso Azure automazione denominata 'SharepointApp parametri'.

Utilizzare nel piano del ripristino tramite l'estrazione la variabile da risorse utilizzando l'istruzione Get-AzureAutomationVariable [-AutomationAccountName] <String> [-nome] $paramValue. [Questo riferimento per altri dettagli](https://msdn.microsoft.com/library/dn913772.aspx)

In questo modo lo stesso script può essere usato per piano di ripristino diverso dal archiviare la variabile complesso specifico piano attività.

## <a name="sample-scripts"></a>Script di esempio

Per un archivio di script, è possibile importarli direttamente in un account di automazione, vedere [repository OMS di Kristian Nese gli script](https://github.com/krnese/AzureDeploy/tree/master/OMS/MSOMS/Solutions/asrautomation)

Lo script qui è un modello di gestione di risorse Azure che consente di distribuire tutte le sotto script

* NSG

Runbook NSG verranno assegnare indirizzi IP pubblico a ogni macchina virtuale all'interno del piano di ripristino e collegare le schede di rete virtuale a un gruppo di sicurezza di rete che consente la comunicazione predefinito

* PublicIP

Runbook IP pubblico assegna indirizzi IP pubblico ogni macchine Virtuali all'interno del piano di ripristino. Accesso al computer e applicazioni dipenderà le impostazioni del firewall all'interno di ogni guest


* CustomScript

Runbook CustomScript verranno assegnare indirizzi IP pubblico a ogni macchina virtuale all'interno del piano di ripristino e installare un'estensione di script personalizzati che effettuerà il pull lo script che viene fatto riferimento a durante la distribuzione del modello

* NSGwithCustomScript

NSGwithCustomScript runbook assegna indirizzi IP pubblico per ogni macchina virtuale all'interno di pianificazione di ripristino, installazione personalizzata utilizzando l'estensione di script e connettere le schede di rete virtuale per consentire un NSG predefinito comunicazioni in ingresso e in uscita per l'accesso remoto

## <a name="additional-resources"></a>Risorse aggiuntive

[Servizio di automazione Azure eseguito come Account](../automation/automation-sec-configure-azure-runas-account.md)

[Panoramica di automazione Azure] (http://msdn.microsoft.com/library/azure/dn643629.aspx "Panoramica di automazione Azure")

[Script di automazione Azure di esempio] (http://gallery.technet.microsoft.com/scriptcenter/site/search?f[0].Type=User&f[0].Value=SC%20Automation%20Product%20Team&f[0].Text=SC%20Automation%20Product%20Team "Script di automazione Azure di esempio")
