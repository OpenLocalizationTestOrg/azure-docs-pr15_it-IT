<properties
    pageTitle="Introduzione Stack chiave archivio Azure | Microsoft Azure"
    description="Informazioni su come Azure Stack chiave archivio vengono gestiti chiavi e informazioni riservate"
    services="azure-stack"
    documentationCenter=""
    authors="rlfmendes"
    manager="natmack"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/26/2016"
    ms.author="ricardom"/>

# <a name="introduction-to-key-vault-in-azure-stack"></a>Introduzione all'archivio chiave in pila Azure #

## <a name="before-you-start"></a>Prima di iniziare

In questo articolo si presuppone che le operazioni seguenti:

- Il lettore ha accesso a un abbonamento con Azure chiave archivio abilitato.
- Azure PowerShell SDK è configurato e disponibile.
- Versione TP2 di tutte le operazioni di tenant esposto possono essere eseguite solo da PowerShell.

## <a name="key-vault-basics"></a>Nozioni fondamentali di archivio di chiave

Archivio di chiave in pila Azure consente di proteggere le chiavi di crittografia e utilizzano informazioni riservate che applicazioni e servizi cloud. Utilizzando chiave archivio, è possibile crittografare chiavi e informazioni riservate (ad esempio chiavi di autenticazione, lo spazio di archiviazione account chiavi, chiavi di crittografia, file PFX e password).

Archivio chiave semplifica il processo di gestione delle chiavi e consente di mantenere il controllo delle chiavi per l'accesso e crittografare i dati. Gli sviluppatori possono creare chiavi per sviluppo e la verifica in minuti e quindi migrare loro ai tasti di produzione. Gli amministratori di sicurezza possono concedere e revocare autorizzazione in base alle esigenze dei tasti.

Chiunque disponga di un abbonamento di Azure pila possa creare e utilizzare archivi chiavi. Sebbene chiave archivio vantaggi gli sviluppatori e per gli amministratori di sicurezza, possono essere implementata e gestito dall'amministratore che gestisce altri servizi di Azure Stack per un'organizzazione. Ad esempio, l'amministratore può accedere con un abbonamento dello Stack di Azure, creare un archivio per l'organizzazione in cui memorizzare le chiavi e la responsabilità di queste attività operative:

- Creare o importare una chiave o segreto
- Revocare o eliminare una chiave o segreto
- Autorizzare utenti o alle applicazioni di accedere archivio chiave, in modo che può gestire o utilizzare le chiavi e informazioni riservate
- Configurare l'utilizzo della chiave (ad esempio, eseguire l'accesso o crittografare)

L'amministratore può offrire agli sviluppatori URI chiamare dalle applicazioni quindi fornire un amministratore della sicurezza con le informazioni di registrazione di utilizzo della chiave.

Gli sviluppatori inoltre possono gestire i tasti direttamente tramite API. Per ulteriori informazioni, vedere Guida per gli sviluppatori di archivio di chiave.

## <a name="scenarios"></a>Scenari

Nella tabella seguente sono illustrati alcuni scenari in cui archivio chiave consente di soddisfare le esigenze di sviluppatori e amministratori di sicurezza:


### <a name="developer-for-an-azure-stack-application"></a>Sviluppo di un'applicazione di Stack di Azure

**Problema**: scrivere un'applicazione per Stack di Azure che utilizza chiavi per la firma e crittografia, ma si desidera questi file essere esterno dall'applicazione in modo che la soluzione ideale per un'applicazione che limitazioni geografiche distribuita.

**Istruzione**: tasti sono archiviati in un archivio e richiamati dal URI quando necessario.


### <a name="developer-for-software-as-a-service-saas"></a>Per sviluppatori di software come un servizio

**Problema:** Non si desidera la responsabilità o potenziali responsabilità per chiavi tenant i clienti e informazioni riservate.

**Istruzione:** I clienti possono importare le rispettive chiavi in pila Azure e gestirli. Desidero clienti proprietari e la gestione delle chiavi in modo che è possibile concentrarsi su eseguire operazioni ottimale, che fornisce le principali funzionalità del software.


### <a name="chief-security-officer-cso"></a>Protezione direttore (compagnia)

**Problema:** Si desidera verificare che la propria organizzazione nel controllo del ciclo di vita chiave che è possibile monitorare l'utilizzo della chiave.

**Istruzione** Archivio di chiave è progettata in modo che Microsoft non visualizzare o estrarre le chiavi.  Quando un'applicazione deve eseguire le operazioni di crittografia utilizzando i tasti dei clienti, chiave archivio responsabile per conto dell'applicazione. L'applicazione non vedrà le chiavi dei clienti.  Anche se si utilizza più servizi dello Stack di Azure e risorse, si desidera gestire i tasti da una posizione centralizzata in pila Azure. L'archivio offre un'unica interfaccia, indipendentemente dalla archivi quanti avere in pila di Azure, quali aree sono supporto e utilizzano delle applicazioni.

## <a name="next-steps"></a>Passaggi successivi

[Guida introduttiva a archivio chiave](azure-stack-kv-getting-started.md)
