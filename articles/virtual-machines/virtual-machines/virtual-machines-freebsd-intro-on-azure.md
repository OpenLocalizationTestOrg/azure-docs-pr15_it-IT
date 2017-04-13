<properties
   pageTitle="Introduzione a FreeBSD su Azure | Microsoft Azure"
   description="Informazioni sull'uso di macchine virtuali FreeBSD su Azure"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="KylieLiang"
   manager="timlt"
   editor=""
   tags="azure-service-management"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="08/27/2016"
   ms.author="kyliel"/>

# <a name="introduction-to-freebsd-on-azure"></a>Introduzione a FreeBSD su Azure
In questo argomento viene fornita una panoramica dell'esecuzione di una macchina virtuale FreeBSD in Azure.

## <a name="overview"></a>Panoramica
FreeBSD per Microsoft Azure è un sistema operativo avanzate utilizzato per lo spegnimento moderna dei server, desktop e incorporato piattaforme. Nella figura 10.3 FreeBSD viene fornita da Microsoft Corporation ed è disponibile in Azure. Si basa sulla versione 10.3 FreeBSD e Azure macchine Virtuali Guest agente [2.1.4](https://github.com/Azure/WALinuxAgent/releases/tag/v2.1.4) è installato. L'agente è responsabile delle comunicazioni tra VM FreeBSD e infrastruttura di Azure per le operazioni, ad esempio il provisioning di macchine Virtuali al primo utilizzo (nome utente, password, nome host, ecc.) e abilitando le funzionalità per le estensioni macchine Virtuali selettive.
Per quanto riguarda le versioni future di FreeBSD, la strategia è per essere sempre aggiornati e rendere disponibili le più recenti subito dopo il rilascio dal team di progettazione di rilascio FreeBSD. Prossima versione è [FreeBSD 11](https://www.freebsd.org/releases/11.0R/schedule.html).

## <a name="deploying-a-freebsd-virtual-machine"></a>Distribuzione di una macchina virtuale FreeBSD
Distribuire una macchina virtuale FreeBSD è un processo semplice utilizzo di un'immagine da [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/microsoft/freebsd103/).

## <a name="vm-extensions-for-freebsd"></a>Estensioni macchine Virtuali per FreeBSD
Di seguito sono supportate estensioni macchine Virtuali in FreeBSD.

### <a name="vmaccess"></a>VMAccess

L'estensione [VMAccess](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess) possibile:

- Reimpostare la password dell'utente sudo originale.
- Creare un nuovo utente sudo con la password specificata.
- Impostare la chiave pubblica host insieme al tasto specificato.
- Reimpostare il tasto host pubblico fornito durante il provisioning di macchine Virtuali se il tasto host non è disponibile.
- Aprire la porta SSH (22) e ripristinare la sshd_config se reset_ssh è impostato su true.
- Rimuovere l'utente esistente.
- Controllare dischi.
- Ripristinare un disco aggiunto.

### <a name="customscript"></a>CustomScript

L'estensione [CustomScript](https://github.com/Azure/azure-linux-extensions/tree/master/CustomScript) possibile:

- Se, scaricare gli script personalizzati da archiviazione Azure o esterno pubblico lo spazio di archiviazione (ad esempio GitHub).
- Eseguire lo script di punto di ingresso.
- Supporta i comandi all'interno del testo.
- Convertire stile Windows nuova riga nella shell e script Python automaticamente.
- Per rimuovere DB shell e script Python automaticamente.
- Proteggere i dati sensibili nella CommandToExecute.

## <a name="authentication-user-names-passwords-and-ssh-keys"></a>Autenticazione: i nomi utente, password e chiavi SSH
Quando si crea una macchina virtuale FreeBSD tramite il portale di Azure, è necessario specificare un nome utente, password o chiave pubblica SSH.
I nomi utente per la distribuzione di una macchina virtuale FreeBSD su Azure non devono corrispondere i nomi degli account di sistema (UID < 100) già presenti nel computer virtuale ("radice,", ad esempio).
Attualmente è supportata solo la RSA SSH chiave. Chiave SSH multilinea deve iniziare con "---inizio SSH2 chiave pubblica---" e terminano con "---fine SSH2 chiave pubblica---".

## <a name="obtaining-superuser-privileges"></a>Ottenere privilegi avanzati
L'account utente specificato durante la distribuzione di istanza macchina virtuale in Azure è un account con privilegi. È stato installato il pacchetto di sudo nell'immagine FreeBSD pubblicato.
Dopo che è stato effettuato tramite questo account utente, è possibile eseguire comandi come radice utilizzando la sintassi di comando.

    # sudo <COMMAND>

Facoltativamente, è possibile ottenere una shell radice utilizzando sudo -s.

## <a name="next-steps"></a>Passaggi successivi
- Passare a [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/microsoft/freebsd103/) per creare una VM FreeBSD.
- Se si desidera visualizzare il proprio FreeBSD in Azure, fare riferimento a [creare e caricare un disco rigido virtuale FreeBSD in Azure](../virtual-machines-linux-classic-freebsd-create-upload-vhd.md).
