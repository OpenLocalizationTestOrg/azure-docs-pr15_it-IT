<properties
    pageTitle="Disattivare le password SSH sulle VM Linux configurando SSHD | Microsoft Azure"
    description="Proteggere le VM Linux su Azure disabilitando gli account di accesso di password per SSH."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="vlivech"
    manager="timlt"
    editor=""
    tags="" />

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/26/2016"
    ms.author="v-livech"/>

# <a name="disable-ssh-passwords-on-your-linux-vm-by-configuring-sshd"></a>Disattivare le password SSH sulle VM Linux configurando SSHD

In questo articolo è incentrato su come bloccare la sicurezza di accesso delle VM Linux.  Non appena viene aperta la porta SSH 22 all'inizio di Bot mondo si tenta di accedere da indovinare una password.  Che cos'è produrrà in questo articolo è disabilitare gli account di accesso di password su SSH.  Per rimuovere completamente la possibilità di usare le password vengono protetti VM Linux da questo tipo di attacchi.  Il vantaggio aggiuntivo è che verranno configurate Linux SSHD per consentire solo gli account di accesso tramite SSH chiavi pubbliche e private, di gran lunga il modo più sicuro per l'accesso a Linux.  Le possibili combinazioni di richiede di indovinare la chiave privata è svariata e pertanto sconsiglia Bot da richiedere anche l'intervento per tentare di forzare SSH tasti.


## <a name="goals"></a>Obiettivi

- Configurare SSHD per impedire che:
  - Account di accesso di password
  - Accesso utente radice
  - Autenticazione in attesa di risposta
- Configurare SSHD consentire o meno:
  - solo SSH chiave account di accesso
- Riavviare SSHD mentre si è ancora connessi
- Verificare la nuova configurazione SSHD

## <a name="introduction"></a>Introduzione

[SSH definito](https://en.wikipedia.org/wiki/Secure_Shell)

SSHD è SSH Server che esegue sui VM Linux.  SSH è un client che viene eseguita da una shell workstation MacBook o Linux.  SSH è anche il protocollo utilizzato per proteggere e crittografare le comunicazioni tra workstation e VM Linux.

In questo articolo è molto importante mantenere un accesso per la VM Linux apre per l'intera panoramica sulle procedure.  Per questo motivo si aprirà due terminali e SSH VM Linux da entrambi gli elementi.  Il primo terminale verrà utilizzato per apportare le modifiche al file di configurazione SSHDs e riavviare il servizio SSHD.  Il secondo terminale verrà usata per testare le modifiche dopo il riavvio del servizio.  Perché è in corso la disattivazione di SSH password e basarsi esclusivamente sulle chiavi SSH, se le chiavi SSH non sono corrette e si chiude la connessione per la macchina virtuale, la macchina virtuale andranno definitivamente bloccato e nessuno sarà in grado di accedere a tale richiesta venga eliminato e ricreato.

## <a name="prerequisites"></a>Prerequisiti

- [Creare le chiavi SSH su Linux e Mac per macchine virtuali Linux in Azure](virtual-machines-linux-mac-create-ssh-keys.md)
- Account Azure
  - [abbonamento di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/)
  - [Portale di Azure](http://portal.azure.com)
- Linux VM in esecuzione in azure
- SSH pubblici e privati coppia`~/.ssh/`
- Chiave pubblica SSH in `~/.ssh/authorized_keys` su VM Linux
- Diritti Sudo nella macchina virtuale
- Porta 22 aperto

## <a name="quick-commands"></a>Comandi rapidi

_Esperto Linux Admins che si vuole semplicemente la versione TLDR iniziare da qui.  Per tutti gli utenti che richiede la spiegazione dettagliata e indicazioni ignorare questa sezione._

```
username@macbook$ sudo vim /etc/ssh/sshd_config

# Change PasswordAuthentication to this:
PasswordAuthentication no

# Change PubkeyAuthentication to this:
PubkeyAuthentication yes

# Change PermitRootLogin to this:
PermitRootLogin no

# Change ChallengeResponseAuthentication to this:
ChallengeResponseAuthentication no

# On the Debian Family
username@macbook$ sudo service ssh restart

# On the RedHat Family
username@macbook$ sudo service sshd restart
```

## <a name="detailed-walk-through"></a>Panoramica sulle procedure dettagliata

Accesso a VM Linux in terminale 1 (T1).  Accesso a VM Linux su 2 terminale (T2).

Nella tabella T2 che verranno modificare il file di configurazione SSHD.  

```
username@macbook$ sudo vim /etc/ssh/sshd_config
```

Da qui è necessario modificare solo le impostazioni per disattivare la password e attivare gli accessi chiavi SSH.  Sono disponibili numerose impostazioni in questo file che è necessario eseguire una ricerca e modifica per Linux & SSH come proteggere in base alle esigenze.

#### <a name="disable-password-logins"></a>Disabilitare gli account di accesso di Password

```
username@macbook$ sudo vim /etc/ssh/sshd_config

# Change PasswordAuthentication to this:
PasswordAuthentication no
```

#### <a name="enable-public-key-authentication"></a>Abilitare l'autenticazione di chiave pubblica

```
username@macbook$ sudo vim /etc/ssh/sshd_config

# Change PubkeyAuthentication to this:
PubkeyAuthentication yes
```

#### <a name="disable-root-login"></a>Disattivare l'accesso principale

```
username@macbook$ sudo vim /etc/ssh/sshd_config

# Change PermitRootLogin to this:
PermitRootLogin no
```

#### <a name="disable-challenge-response-authentication"></a>Disabilitare l'autenticazione in attesa di risposta

```
# Change ChallengeResponseAuthentication to this:
ChallengeResponseAuthentication no
```

### <a name="restart-sshd"></a>Riavviare SSHD

Verificare che si è ancora connessi da shell T1.  Si tratta di critica non ottenere bloccate fuori di una macchina virtuale se le chiavi SSH non sono corrette poiché password ora sono disabilitate.  Se tutte le impostazioni non sono corretti nelle VM Linux T1 consente di correggere sshd_config come comunque essere eseguito l'accesso e SSH consente di mantenere la connessione attivo durante il servizio SSHD riavviare.

Da T2 eseguire:

##### <a name="on-the-debian-family"></a>Per la famiglia Debian

```
username@macbook$ sudo service ssh restart
```

##### <a name="on-the-redhat-family"></a>Per la famiglia Red Hat

```
username@macbook$ sudo service sshd restart
```

Le password sono ora disabilitate per la macchina virtuale la protezione dai tentativi di accesso di forzare la password.  Con solo SSH chiavi consentito che sarà possibile effettuare l'accesso più veloce e più sicuro.
