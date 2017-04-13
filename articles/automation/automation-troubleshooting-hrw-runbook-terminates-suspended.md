<properties
   pageTitle="Lavoro Runbook ibrido: Un processo runbook termina con lo stato sospesi | Microsoft Azure"
   description="Cause sintomi e le risoluzioni ibrido Runbook lavoro processo terminazione errore."
   services="automation"
   documentationCenter=""
   authors="mgoedtel"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/17/2016"
   ms.author="magoedte" />

# <a name="hybrid-runbook-worker-a-runbook-job-terminates-with-a-status-of-suspended"></a>Lavoro Runbook ibrido: Un processo runbook termina con lo stato sospesi

## <a name="summary"></a>Riepilogo

I runbook è sospesa subito dopo il tentativo di eseguire tre volte. Sono disponibili le condizioni che possono interrompere runbook completamento e il messaggio di errore correlati non include eventuali informazioni aggiuntive per indicare il motivo. In questo articolo vengono fornite procedure per problemi relativi agli errori di esecuzione runbook lavoro Runbook ibrida.

Se il problema Azure non è indirizzato in questo articolo, visitare i forum Azure su [MSDN e Overflow dello Stack](https://azure.microsoft.com/support/forums/). È possibile registrare il problema questi forum o a [ @AzureSupport su Twitter](https://twitter.com/AzureSupport). Inoltre, è possibile archiviare una richiesta di supporto Azure selezionando **ottenere supporto** nel sito di [supporto di Azure](https://azure.microsoft.com/support/options/) .

## <a name="symptom"></a>Sintomo

Si verifica un errore di esecuzione runbook e l'errore restituito è, "l'azione del processo non è possibile eseguire 'Attiva', perché il processo di arresto anomalo. L'azione del processo tentato di 3 volte."


## <a name="cause"></a>Causa

Esistono diverse cause dell'errore: 

  1. Lavoro ibrido viene utilizzato un proxy o firewall
  2. Il computer di lavoro ibrido in è inferiore a [requisiti](automation-hybrid-runbook-worker.md#hybrid-runbook-worker-requirements) hardware minimi 
  3. Non è possibile eseguire l'autenticazione di runbook con le risorse locali


## <a name="cause-1-hybrid-runbook-worker-is-behind-proxy-or-firewall"></a>Causa 1: Ibrido Runbook lavoro è protetto da proxy o firewall

Il computer che è in esecuzione il lavoro Runbook ibrido dietro un firewall o il server proxy e accesso alla rete in uscita non può essere consentito o configurato in modo corretto.

### <a name="solution"></a>Soluzione

Verificare il computer abbia accesso in uscita a *. cloudapp.net sulle porte 443, 9354 e 30199 30000. 

## <a name="cause-2-computer-has-less-than-minimum-hardware-requirements"></a>Motivo 2: Computer in uso disponga requisiti hardware inferiore al minimo

Computer che eseguono il lavoro Runbook ibrido deve soddisfare i requisiti hardware minimi prima di designare per ospitare questa caratteristica. In caso contrario, a seconda di utilizzo delle risorse di altri processi in background e conflitto dovuti runbook durante l'esecuzione dal computer verranno diventano più utilizzate e causare ritardi processo runbook o timeout. 

### <a name="solution"></a>Soluzione 

Verificare prima di tutto il computer designato per eseguire la funzione di distribuzione ibrida Runbook lavoro soddisfi i requisiti hardware minimi.  In caso affermativo, monitorare l'utilizzo della CPU e memoria per determinare le correlazione tra le prestazioni dei processi di lavoro Runbook ibrida e Windows.  In caso di memoria o pressione CPU, è possibile che sia necessario aggiornare o aggiungere ulteriori processori o aumentare la memoria per risolvere bottiglia delle risorse e risolvere l'errore. In alternativa, selezionare una risorsa di elaborazione diversi in grado di supportare le proporzioni e i requisiti minimi quando carichi indicano che un aumento è necessario.         

## <a name="cause-3-runbooks-cannot-authenticate-with-local-resources"></a>Motivo 3: Runbook non è possibile eseguire l'autenticazione con le risorse locali

### <a name="solution"></a>Soluzione

Controllare il registro eventi di **Microsoft SMA** per un evento corrispondente con descrizione *Win32 processo terminato con codice [4294967295]*.  La causa dell'errore è non configurato l'autenticazione nel runbook o specifica delle credenziali Esegui come per il gruppo di lavoro ibrida.  Esaminare [le autorizzazioni Runbook](automation-hybrid-runbook-worker.md#runbook-permissions) per confermare che l'autenticazione sia stato configurato correttamente per il runbook.  


 

