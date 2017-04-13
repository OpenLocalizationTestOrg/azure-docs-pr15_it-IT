<properties
    pageTitle="Risoluzione dei problemi SSH dettagliate per una macchina virtuale Azure | Microsoft Azure"
    description="Ulteriori SSH risoluzione di problemi durante la connessione a un computer virtuale Azure"
    keywords="SSH connessione rifiutata, ssh errore, azure ssh, SSH connessione non è riuscita"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="top-support-issue,azure-service-management,azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="support-article"
    ms.date="09/01/2016"
    ms.author="iainfou"/>

# <a name="detailed-ssh-troubleshooting-steps"></a>Procedura di risoluzione dei problemi SSH dettagliata

Sono disponibili molti perché è possibile che il client SSH potrebbe non essere possibile raggiungere il servizio SSH nella macchina virtuale. Se è stata eseguita tramite altre [procedure di risoluzione dei problemi SSH generale](virtual-machines-linux-troubleshoot-ssh-connection.md), è necessario per ulteriori informazioni sulla risoluzione del problema di connessione. In questo articolo illustra la procedura dettagliata sulla risoluzione dei problemi per stabilire nel punto in cui la connessione SSH ha esito negativo e su come risolvere il problema.

## <a name="take-preliminary-steps"></a>Operazioni preliminari

Il diagramma seguente illustra i componenti coinvolti.

![Diagramma che mostra i componenti del servizio SSH](./media/virtual-machines-linux-detailed-troubleshoot-ssh-connection/ssh-tshoot1.png)

I passaggi seguenti consentono di isolare l'origine dell'errore e scoprire le soluzioni.

Prima di tutto, controllare lo stato di macchine Virtuali nel portale.

Nel [portale di Azure](https://portal.azure.com):

1. Per le macchine virtuali create usando il modello di distribuzione classica, selezionare **Sfoglia** > **(classica) macchine virtuali di** > *nome macchina virtuale*.

    -OPPURE-

    Per le macchine virtuali create utilizzando il modello di Manager delle risorse, selezionare **Sfoglia** > **macchine virtuali** > *nome macchina virtuale*.

    Nel riquadro di stato per la macchina virtuale deve essere visualizzata **in esecuzione**. Scorrere fino a Mostra le attività più recenti per elaborazione, lo spazio di archiviazione e le risorse di rete.

2. Selezionare **le impostazioni** per esaminare i punti finali, ma gli indirizzi IP e altre impostazioni.

    Per identificare i punti finali in macchine virtuali che sono stati creati tramite Gestione risorse, verificare che sia stato definito un [gruppo di sicurezza di rete](../virtual-network/virtual-networks-nsg.md) . Verificare anche che siano state applicate le regole per il gruppo di sicurezza di rete e che sta viene fatto riferimento nella subnet.

Nel [portale classica Azure](https://manage.windowsazure.com), per macchine virtuali che sono stati creati usando il modello di distribuzione classica:

1. Selezionare **macchine virtuali** > *nome macchina virtuale*.
2. Selezionare di Virtual Machine **Dashboard** per verificare lo stato.
3. Selezionare **Monitor** per visualizzare le attività più recenti per elaborazione, lo spazio di archiviazione e le risorse di rete.
4. Selezionare **i punti finali** per verificare che sia presente un endpoint per il traffico SSH.

Per verificare la connettività di rete, verificare l'endpoint configurati e se è possibile raggiungere la macchina virtuale tramite un altro protocollo, ad esempio HTTP o un altro servizio.

Dopo questa procedura, provare la connessione SSH.


## <a name="find-the-source-of-the-issue"></a>Trovare l'origine del problema

Il client SSH nel computer in uso potrebbe non riuscire a raggiungere il servizio SSH nella macchina virtuale Azure a causa di problemi o errori di configurazione le informazioni seguenti:

- [Computer client SSH](#source-1-ssh-client-computer)
- [Dispositivo di bordo dell'organizzazione](#source-2-organization-edge-device)
- [Endpoint del servizio cloud e accedere a elenco di controllo)](#source-3-cloud-service-endpoint-and-acl)
- [Gruppi di sicurezza di rete](#source-4-network-security-groups)
- [Basati su Linux Azure macchine Virtuali](#source-5-linux-based-azure-virtual-machine)

## <a name="source-1-ssh-client-computer"></a>Origine 1: SSH computer client

Per eliminare il computer come origine dell'errore, verificare che rende SSH connessioni a un'altra in locale, computer basato su Linux.

![Diagramma che evidenzia componenti del computer client SSH](./media/virtual-machines-linux-detailed-troubleshoot-ssh-connection/ssh-tshoot2.png)

Se la connessione non riesce, verificare quanto segue nel computer in uso:

- Un'impostazione firewall locale blocca il traffico SSH in ingresso o in uscita (TCP 22)
- Il software client proxy che impedisce connessioni SSH installato nel computer locale
- Monitoraggio di software che impedisce SSH connessioni di rete installato nel computer locale
- Altri tipi di software di sicurezza che monitorare il traffico o abilitare o disabilitare tipi specifici di traffico

Se una di queste condizioni da applicare temporaneamente disattivare il software e provare una connessione SSH a un computer locale per scoprire il motivo per che la connessione è stata bloccata nel computer in uso. Quindi utilizzare l'amministratore di rete per correggere le impostazioni di software per consentire connessioni SSH.

Se si utilizza l'autenticazione certificato, verificare di avere le autorizzazioni per la cartella .ssh nella home directory:

- Chmod 700 ~/.ssh
- Chmod 644 ~/.ssh/\*. pub
- ~/.Ssh/id_rsa chmod 600 (o altri file contenenti le chiavi private in esse memorizzate)
- ~/.Ssh/known_hosts 644 chmod (contiene host che si è connessi a tramite SSH)

## <a name="source-2-organization-edge-device"></a>Origine 2: Dispositivo di bordo organizzazione

Per eliminare il dispositivo di bordo dell'organizzazione come origine dell'errore, verificare che un computer in cui è connesso a Internet applicabili SSH connessioni alle macchine Virtuali di Azure. Se si accede a macchina virtuale su una rete VPN da sito o una connessione di Azure ExpressRoute, andare al [origine 4: gruppi di sicurezza di rete](#nsg).

![Diagramma che evidenzia il dispositivo di bordo dell'organizzazione](./media/virtual-machines-linux-detailed-troubleshoot-ssh-connection/ssh-tshoot3.png)

Se non si dispone di un computer in cui è direttamente connessi a Internet, creare una nuova macchina virtuale Azure nel proprio gruppo di risorse o servizio cloud e usarla. Per ulteriori informazioni, vedere [creare una macchina virtuale che eseguono Linux in Azure](virtual-machines-linux-quick-create-cli.md). Eliminare il gruppo di risorse o il servizio macchine Virtuali e cloud al termine con i test.

Se è possibile creare un collegamento SSH con un computer in cui è connesso a Internet, controllare il dispositivo di bordo dell'organizzazione per:

- Un firewall interno che blocca il traffico SSH con Internet
- Un server proxy che impedisce connessioni SSH
- Intrusione o monitoraggio software in esecuzione in dispositivi di rete bordo che impedisce SSH connessioni di rete

Collaborare con l'amministratore di rete per correggere le impostazioni dei dispositivi di bordo dell'organizzazione per consentire il traffico SSH con Internet.

## <a name="source-3-cloud-service-endpoint-and-acl"></a>Origine 3: Endpoint del servizio Cloud e ACL

> [AZURE.NOTE] Questa origine si applica solo a macchine virtuali che sono state create usando il modello di distribuzione classica. Per le macchine virtuali che sono state create tramite Gestione risorse, andare al [origine 4: gruppi di sicurezza di rete](#nsg).

Per eliminare l'endpoint del servizio cloud e ACL come origine dell'errore, verificare che un altro macchine Virtuali di Azure nella stessa rete virtuale applicabili SSH connessioni a di una macchina virtuale.

![Diagramma che evidenzia endpoint del servizio cloud e ACL](./media/virtual-machines-linux-detailed-troubleshoot-ssh-connection/ssh-tshoot4.png)

Se non si dispone di un altro macchine Virtuali nella stessa rete virtuale, è possibile creare con facilità uno nuovo. Per ulteriori informazioni, vedere [creare una macchina virtuale Linux su Azure tramite CLI](virtual-machines-linux-quick-create-cli.md). Eliminare la macchina virtuale aggiuntiva dopo aver con i test.

Se è possibile creare un collegamento SSH con una macchina virtuale nella stessa rete virtuale, verificare quanto segue:

- **Configurazione dell'endpoint per il traffico SSH in macchine Virtuali di destinazione.** La porta TCP privata dell'endpoint deve corrispondere la porta TCP in cui il servizio SSH nella macchina virtuale è in attesa. (La porta predefinita è 22). Per le macchine virtuali create usando il modello di distribuzione di Manager delle risorse, verificare il numero di porta SSH TCP nel portale di Azure selezionando **Sfoglia** > **macchine virtuali (v2)** > *nome macchine Virtuali* > **Impostazioni** > **i punti finali**.

- **ACL per l'endpoint di traffico SSH sul computer virtuale di destinazione.** Un ACL consente di specificare consentito o negato il traffico in ingresso da Internet, in base all'indirizzo IP di origine. ACL non configurati correttamente è possibile impedire il traffico in ingresso SSH all'endpoint. Selezionare l'ACL per verificare che il traffico in ingresso da indirizzi IP pubblici del proxy o altri server perimetrale consentito. Per ulteriori informazioni, vedere [informazioni sull'accesso rete elenchi di controllo ()](../virtual-network/virtual-networks-acl.md).

Per eliminare l'endpoint come origine del problema, rimuovere l'endpoint corrente, creare un nuovo endpoint e specificare il nome SSH (porta TCP 22 per il numero di porta pubblici e privati). Per ulteriori informazioni, vedere [configurare i punti finali in una macchina virtuale in Azure](virtual-machines-windows-classic-setup-endpoints.md).

<a id="nsg"></a>
## <a name="source-4-network-security-groups"></a>Origine 4: Gruppi di sicurezza di rete

Gruppi di sicurezza di rete consentono di disporre di un controllo più dettagliato del traffico consentito in ingresso e in uscita. È possibile creare regole che comprendere subnet e cloud services in una rete virtuale Azure. Controllare le regole di gruppo di sicurezza rete per assicurarsi che sia consentito SSH il traffico da e verso Internet.
Per ulteriori informazioni, vedere [informazioni sui gruppi di sicurezza di rete](../virtual-network/virtual-networks-nsg.md).

## <a name="source-5-linux-based-azure-virtual-machine"></a>: 5 basate su Linux Azure macchina virtuale di origine

L'ultima origine possibili problemi è Azure macchina virtuale stessa.

![Diagramma che evidenzia basati su Linux Azure macchina virtuale](./media/virtual-machines-linux-detailed-troubleshoot-ssh-connection/ssh-tshoot5.png)

Se già fatto, seguire le istruzioni [per reimpostare una password o SSH per macchine virtuali basate su Linux](virtual-machines-linux-classic-reset-access.md).

Riprovare a connettersi dal proprio computer. Se il problema persiste, di seguito sono riportati alcuni dei problemi:

- Il servizio SSH non è in esecuzione nel computer virtuale di destinazione.
- Il servizio SSH non è in ascolto sulla porta TCP 22. Per verificarlo, installare un client telnet nel computer locale ed eseguire "telnet *cloudServiceName*. cloudapp.net 22". Consente di determinare se la macchina virtuale consente la comunicazione in ingresso e in uscita all'endpoint SSH.
- Il firewall locale del computer virtuale dispone di regole che impediscono il traffico SSH in ingresso o in uscita.
- Intrusione o in rete monitoraggio software in esecuzione nel computer virtuale Azure impedisce SSH connessioni.


## <a name="additional-resources"></a>Risorse aggiuntive
Per ulteriori informazioni sulla risoluzione dei problemi di accesso alle applicazioni, vedere [risoluzione dei problemi di accesso a un'applicazione in esecuzione in una macchina virtuale Azure](virtual-machines-linux-troubleshoot-app-connection.md)