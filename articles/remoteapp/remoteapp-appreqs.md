
<properties
    pageTitle="Requisiti dell'App per Azure RemoteApp | Microsoft Azure"
    description="Informazioni sui requisiti per l'App che si desidera utilizzare RemoteApp di Azure"
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



# <a name="app-requirements"></a>Requisiti dell'App

> [AZURE.IMPORTANT]
> Più RemoteApp Azure utilizzato. Leggere l' [annuncio](https://go.microsoft.com/fwlink/?linkid=821148) per informazioni dettagliate.

Azure RemoteApp supporta flusso versione a 32 bit o 64 bit basato su Windows applicazioni da un'immagine di Windows Server 2012 R2. La maggior parte delle esistenti a 32 o 64 bit basato su Windows applicazioni vengono eseguite "così com'è" in Azure RemoteApp (Servizi Desktop remoto o in precedenza noto come servizi Terminal) ambiente. C'è una differenza tra l'esecuzione e in esecuzione anche - alcune applicazioni di funzionano correttamente e le prestazioni migliori, mentre altri non. Di seguito vengono fornite indicazioni per lo sviluppo di applicazioni in un ambiente di Servizi Desktop remoto e il test per garantire la compatibilità.

Suggerimento: Stiamo lavorando alla creazione di alcuni esempi di utilizzo delle App dell'utente. Si noterà nuovi argomenti che illustrano l'utilizzo di Microsoft Access, QuickBooks e App-V in RemoteApp.

## <a name="requirements"></a>Requisiti
Questi tre requisiti seguito, rivelarsi utili applicazione eseguire correttamente in RemoteApp:

1.  Le applicazioni di soddisfano tutti i [requisiti di certificazione per le applicazioni desktop di Windows](https://msdn.microsoft.com/library/windows/desktop/hh749939.aspx) e rispettare [le istruzioni di programmazione di Servizi Desktop remoto](https://msdn.microsoft.com/library/aa383490.aspx) avrà la compatibilità completa con RemoteApp.
2.  Le applicazioni non devono memorizzare mai dati localmente sull'immagine o istanze RemoteApp che possono essere perse.  Dopo aver creato un insieme di RemoteApp, le istanze vengono duplicate e sono prive di stato e può contenere solo le applicazioni. Archiviare dati in un'origine esterna o all'interno del profilo utente.
3.  Immagini personalizzate non devono contenere dati che possono essere persi.  

## <a name="testing-your-apps"></a>Verifica delle applicazioni
Utilizzare questa procedura per il test di applicazioni:

1.  Installare Windows Server 2012 R2 e l'applicazione
2.  Attivare Desktop remoto
3.  Creare due account utente, potrebbero verificarsi, aggiunta di entrambi gli account utente al gruppo di sicurezza Desktop remoto e successivamente.
4.  Verificare la compatibilità con più di sessione definendo due licenze sessioni simultanee al PC durante l'avvio dell'applicazione.
5.  Convalidare il comportamento di app

## <a name="application-development-guidelines"></a>Istruzioni per lo sviluppo dell'applicazione
Utilizzare le seguenti linee guida per lo sviluppo di applicazioni per RemoteApp.

### <a name="multiple-users"></a>Più utenti

- Installazione di un' [applicazione per un singolo utente ](https://msdn.microsoft.com/library/aa380661.aspx)può creare problemi in un ambiente multiutente.
- Le applicazioni devono [memorizzare informazioni specifiche dell'utente](https://msdn.microsoft.com/library/aa383452.aspx) in posizioni specifiche dell'utente, separatamente dalla globale informazioni che si applicano a tutti gli utenti.
- RemoteApp utilizza più [spazi dei nomi per gli oggetti kernel](https://msdn.microsoft.com/library/aa382954.aspx); uno spazio dei nomi globale viene utilizzato principalmente dai servizi nelle applicazioni client/server.
- Non è consigliabile presuppongono che il nome del computer o l' [indirizzo IP](https://msdn.microsoft.com/library/aa382942.aspx) assegnato al computer sono associate a un singolo utente perché più utenti possono essere connessi contemporaneamente a un server Host sessione Desktop remoto (Host sessione Desktop remoto).

### <a name="performance"></a>Prestazioni
- Disabilitare [gli effetti grafici](https://msdn.microsoft.com/library/aa380822.aspx) prima di aggiungere l'app a RemoteApp.
- Per ottimizzare la disponibilità di CPU per tutti gli utenti, disattivare [le attività in background](https://msdn.microsoft.com/library/aa380665.aspx) o creare attività in background efficienti che non sono risorse.
- Occorre ottimizzare e saldo totale delle applicazioni di [utilizzo dei thread](https://msdn.microsoft.com/library/aa383520.aspx) per un ambiente multiutente, più processori.
- Per ottimizzare le prestazioni, è consigliabile per le applicazioni per [rilevare](https://msdn.microsoft.com/library/aa380798.aspx) se sono in esecuzione in una sessione client.
