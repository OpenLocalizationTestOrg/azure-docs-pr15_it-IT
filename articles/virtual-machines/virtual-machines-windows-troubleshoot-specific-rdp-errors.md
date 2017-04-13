<properties
    pageTitle="Messaggi di errore RDP specifici per macchine virtuali di Azure | Microsoft Azure"
    description="Comprendere i messaggi di errore specifici che potrebbero essere visualizzati quando si tenta utilizzano connessione Desktop remoto a una macchina virtuale di Windows Azure"
    keywords="Errore desktop remoto, errore di connessione desktop remoto, non è possibile connettersi a macchine Virtuali, remote desktop Risoluzione dei problemi"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="top-support-issue,azure-service-management,azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="support-article"
    ms.date="10/14/2016"
    ms.author="iainfou"/>

# <a name="troubleshooting-specific-rdp-error-messages-to-a-windows-vm-in-azure"></a>Risoluzione dei problemi relativi a messaggi di errore RDP specifici per una macchina virtuale di Windows in Azure
Si potrebbe ricevere un messaggio di errore specifico quando si Usa connessione Desktop remoto a una Windows macchine () in Azure. In questo articolo vengono riportati alcuni dei messaggi di errore più comuni rilevati insieme risoluzione dei problemi per risolverli. Se si verificano problemi durante la connessione per la macchina virtuale utilizzando RDP ma non si verifichino un messaggio di errore specifico, vedere [risoluzione dei problemi di Guida per il Desktop remoto](virtual-machines-windows-troubleshoot-rdp-connection.md).

Per informazioni sui messaggi di errore specifico, vedere le operazioni seguenti:

- [La sessione remota è stata disconnessa perché non sono presenti server di licenze Desktop remoto disponibile per fornire una licenza](#rdplicense).
- [Desktop remoto non è possibile trovare il computer "nome"](#rdpname).
- Si è verificato un errore [un tipo di autenticazione. Autorità di protezione locale non può essere contattato](#rdpauth).
- [Errore di protezione di Windows: le credenziali non risolvono](#wincred).
- [In questo computer non riesce a connettersi al computer remoto](#rdpconnect).

<a id="rdplicense"></a>
## <a name="the-remote-session-was-disconnected-because-there-are-no-remote-desktop-license-servers-available-to-provide-a-license"></a>La sessione remota disconnessa perché non sono presenti server di licenze Desktop remoto disponibile per fornire una licenza.

Causa: Il periodo di tolleranza licenze 120 giorni per il ruolo di Server Desktop remoto è scaduto ed è necessario installare licenze.

In alternativa, salvare una copia locale del file RDP dal portale di e il seguente comando al prompt dei comandi di PowerShell per la connessione. Questo passaggio consente di disattivare le licenze di solo la connessione:

        mstsc <File name>.RDP /admin

Se non è effettivamente necessario più di due connessioni Desktop remoto per la macchina virtuale, è possibile utilizzare Server Manager per rimuovere il ruolo di Server Desktop remoto.

Per ulteriori informazioni, vedere il blog post [macchine Virtuali di Azure viene interrotta con "Nessun server licenze Desktop remoto disponibile"](https://blogs.msdn.microsoft.com/mast/2014/01/21/rdp-to-azure-vm-fails-with-no-remote-desktop-license-servers-available/).

<a id="rdpname"></a>
## <a name="remote-desktop-cant-find-the-computer-name"></a>Desktop remoto non è possibile trovare il computer "nome".

Causa: Il client Desktop remoto nel computer in uso non è possibile risolvere il nome del computer le impostazioni del file RDP.

Possibili soluzioni:

- Se si è nella rete intranet dell'organizzazione, assicurarsi che il computer abbia accesso al server proxy e possa inviare il traffico HTTPS.

- Se si usa un file con estensione RDP archiviato in locale, provare a utilizzare quello che viene generato dal portale. Questo passaggio consente di disporre di nome DNS corretto per macchina virtuale o il servizio cloud e la porta dell'endpoint della macchina virtuale. Ecco un file RDP esempio generato dal portale:

        full address:s:tailspin-azdatatier.cloudapp.net:55919
        prompt for credentials:i:1

La parte del file RDP previsti:
- Nome di dominio completo del servizio cloud che contiene la macchina virtuale ("tailspin-azdatatier.cloudapp.net" in questo esempio).

- Porte TCP esterna dell'endpoint per il traffico Desktop remoto (55919).

<a id="rdpauth"></a>
## <a name="an-authentication-error-has-occurred-the-local-security-authority-cannot-be-contacted"></a>Si è verificato un errore di autenticazione. Non è possibile contattare autorità di protezione locale.

Causa: La destinazione macchine Virtuali non è possibile individuare autorità di protezione nella parte di nome utente delle credenziali.

Quando il proprio nome utente è nel modulo *SecurityAuthority*\\*UserName* (esempio: CORP\User1), la parte *SecurityAuthority* è nome del computer di Virtual Machine (per l'ufficio di sicurezza locale) o un nome di dominio Active Directory.

Possibili soluzioni:

- Se l'account è locale per la macchina virtuale, assicurarsi che il nome di macchine Virtuali è stato digitato correttamente.

- Se l'account si trova in un dominio Active Directory, controllare l'ortografia del nome di dominio.

- Se si tratta di un account di dominio Active Directory e il nome di dominio è stato digitato correttamente, verificare che un controller di dominio sia disponibile in tale dominio. È un problema comune in Azure reti virtuali che contengono controller di dominio un controller di dominio non è disponibile perché non è stato iniziato. In alternativa, è possibile usare un account di amministratore locale invece di un account di dominio.

<a id="wincred"></a>
## <a name="windows-security-error-your-credentials-did-not-work"></a>Errore di protezione di Windows: le credenziali non risolvono.

Causa: La destinazione macchine Virtuali Impossibile convalidare il nome dell'account e la password.

Un computer basato su Windows è possibile convalidare le credenziali dell'account locale o un account di dominio.

- Per gli account locali, utilizzare *nomecomputer*\\sintassi del*nome utente* (esempio: SQL1\Admin4798).
- Per gli account di dominio, usare *NomeDominio*\\sintassi del*nome utente* (esempio: CONTOSO\peterodman).

Se si dispone di alzati di livello di una macchina virtuale controller di dominio in una nuova foresta di Active Directory, l'account di amministratore locale che è stato effettuato l'accesso con viene convertita in un account equivalente con la stessa password nella nuova foresta e nel dominio. L'account locale quindi viene eliminato.

Ad esempio, se è stato effettuato l'accesso con l'account locale DC1\DCAdmin e quindi alzati di livello la macchina virtuale come controller di dominio in un nuovo insieme di strutture per il dominio corp.contoso.com, l'account locale DC1\DCAdmin viene eliminato e viene creato un nuovo account di dominio (CORP\DCAdmin) con la stessa password.

Verificare che il nome dell'account è un nome che è possibile verificare la macchina virtuale come un account valido e che la password sia corretta.

Se è necessario modificare la password dell'account dell'amministratore locale, vedere [come reimpostare la password o il servizio Desktop remoto per macchine virtuali di Windows](virtual-machines-windows-reset-rdp.md).

<a id="rdpconnect"></a>
## <a name="this-computer-cant-connect-to-the-remote-computer"></a>In questo computer non riesce a connettersi al computer remoto.

Causa: L'account usato per la connessione non ha diritti di accesso Desktop remoto.

Ogni computer Windows dispone di un gruppo locale di utenti Desktop remoto, che contiene l'account e i gruppi che possono accedere al suo interno in remoto. I membri del gruppo administrators locale hanno anche accesso, anche se tali account non sono elencati nel gruppo locale utenti Desktop remoto. Per i computer di dominio, gruppo administrators locale contiene anche gli amministratori del dominio per il dominio.

Assicurarsi che l'account in uso per connettersi a abbia diritti di accesso Desktop remoto. In alternativa, utilizzare un dominio o account dell'amministratore locale per la connessione desktop remoto. Per aggiungere l'account desiderato per il gruppo locale utenti Desktop remoto, utilizzare lo snap-in Microsoft Management Console (**utilità di sistema > utenti e gruppi locali > gruppi > utenti Desktop remoto**).


## <a name="next-steps"></a>Passaggi successivi
Se nessuno di questi errori si sono verificati e si verifica un problema sconosciuto con connessione mediante RDP, vedere [risoluzione dei problemi di Guida per il Desktop remoto](virtual-machines-windows-troubleshoot-rdp-connection.md).

- [Pacchetto di diagnostica Azure IaaS (Windows)](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864)
- I problemi di accesso alle applicazioni in esecuzione in una macchina virtuale, vedere [risoluzione dei problemi di accesso a un'applicazione in esecuzione in una macchina virtuale Azure](virtual-machines-linux-troubleshoot-app-connection.md).
- Se si verificano problemi di utilizzo Secure Shell (SSH) per connettersi a un VM Linux in Azure, vedere [risolvere i problemi di SSH connessioni a una macchina virtuale Linux in Azure](virtual-machines-linux-troubleshoot-ssh-connection.md).