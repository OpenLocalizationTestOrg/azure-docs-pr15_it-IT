<properties
   pageTitle="Configurare l'autenticazione con i servizi Web di Amazon | Microsoft Azure"
   description="In questo articolo viene descritto come creare e convalidare una credenziale AWS per runbook in automazione Azure la gestione delle risorse AWS."
   services="automation"
   documentationCenter=""
   authors="mgoedtel"
   manager="jwhit"
   editor="tysonn"
   keywords="autenticazione AWS, configurare aws"/>
<tags
   ms.service="automation"
   ms.workload="tbd"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.date="09/12/2016"
   ms.author="magoedte"/>

# <a name="authenticate-runbooks-with-amazon-web-services"></a>Eseguire l'autenticazione runbook con i servizi Web di Amazon
Automazione di attività comuni con le risorse in servizi Web di Amazon (AWS) può essere eseguita con runbook automazione in Azure.  È possibile automatizzare molte attività di AWS utilizzando l'automazione runbook nello stesso modo in cui è possibile con le risorse di Azure.  È sufficiente sono due fattori:

* Un abbonamento a AWS e un insieme di credenziali.  In particolare il tasto di scelta rapida AWS e chiave privata.  Per ulteriori informazioni, consultare l'articolo [Con le credenziali AWS](http://docs.aws.amazon.com/powershell/latest/userguide/specifying-your-aws-credentials.html).
* Un account di automazione e Azure abbonamento.  Per ulteriori informazioni sull'impostazione di un account Azure automazione, consultare l'articolo [Configurare esecuzione come Account Azure](../automation/automation-sec-configure-azure-runas-account.md).  

Per eseguire l'autenticazione con AWS, è necessario specificare un insieme di credenziali AWS autenticare i runbook esecuzione da Azure automazione. Se si dispone già di un account di automazione creato e si vuole che consente di eseguire l'autenticazione con AWS, è possibile eseguire i passaggi indicati nella sezione seguente.  Se si vuole dedicato un account per le risorse AWS destinata runbook, è necessario prima di tutto creare un nuovo [account di automazione Esegui come](../automation/automation-sec-configure-azure-runas-account.md) (salta l'opzione per creare un'entità servizio) e seguire la procedura descritta di seguito.

## <a name="configure-automation-account"></a>Configurare l'account di automazione
Per l'automazione di Azure comunicare con AWS, è necessario innanzitutto recuperare le credenziali AWS e archiviarli come attività di automazione di Azure.  Effettuare le seguenti operazioni descritte nel documento AWS [Gestione dei tasti di scelta per il proprio Account AWS](http://docs.aws.amazon.com/general/latest/gr/managing-aws-access-keys.html) per creare un tasto e copiare le **ID chiave di accesso** e un **Tasto di scelta segreto** (se si desidera scaricare il file di chiave per archiviarlo in un luogo sicuro).

Dopo aver creato e copiare le chiavi di sicurezza AWS, sarà necessario creare una risorsa di credenziali con un account Azure automazione in modo sicuro archiviarli e farvi riferimento con il runbook.  Seguire i passaggi indicati nella sezione **per creare nuove credenziali** nell'articolo [risorse credenziali in Azure automazione](../automation/automation-certificates.md/###To create a new credential with the Azure portal) e immettere le informazioni seguenti:

1. Nella casella **nome** immettere **AWScred** o un valore appropriato seguendo il denominazione standard.  
2. Nella casella **nome utente** digitare l' **ID di accesso** e il **Tasto segreto** nella casella **Password** e **Conferma password** .   

## <a name="next-steps"></a>Passaggi successivi

- Reivew l'articolo soluzione [automatizzare la distribuzione di una macchina virtuale nei servizi Web di Amazon](../automation/automation-scenario-aws-deployment.md) per imparare a creare runbook per automatizzare le attività in AWS.
