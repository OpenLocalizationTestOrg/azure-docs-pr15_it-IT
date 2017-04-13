<properties
    pageTitle="Configurazione di DHCPv6 per macchine virtuali Linux | Microsoft Azure"
    description="Informazioni su come configurare DHCPv6 per macchine virtuali Linux."
    services="load-balancer"
    documentationCenter="na"
    authors="sdwheeler"
    manager="carmonm"
    editor=""
    keywords="IPv6, bilanciamento del carico azure, stack doppio, ip pubblico, ipv6 nativo, mobile, iot"
/>
<tags
    ms.service="load-balancer"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="09/14/2016"
    ms.author="sewhee"
/>

# <a name="configuring-dhcpv6-for-linux-vms"></a>Configurazione di DHCPv6 per macchine virtuali Linux

In alcune immagini macchina virtuale Linux in Azure Marketplace non è DHCPv6 configurati per impostazione predefinita. Per il supporto di IPv6 DHCPv6 deve essere configurato all'interno della distribuzione del sistema operativo Linux che si sta utilizzando. Diverse distribuzioni Linux sono diversi modi di configurazione DHCPv6 perché utilizzano pacchetti diversi.

>[AZURE.NOTE] Immagini di SUSE Linux e CoreOS recenti in Azure Marketplace state preconfigurate con DHCPv6. Nessuna modifica aggiuntiva necessari quando si utilizzano tali immagini.

In questo documento viene descritto come abilitare DHCPv6 in modo che il computer virtuale Linux ottenga un indirizzo IPv6.

>[AZURE.WARNING] In modo non corretto modifica file di configurazione di rete può causare la perdita di accesso alla rete della macchina virtuale. È consigliabile verificare le modifiche di configurazione dei sistemi di prova. Le istruzioni fornite in questo articolo sono state testate le versioni più recenti delle immagini Linux in Azure Marketplace. Consultare la documentazione per la versione di Linux per informazioni più dettagliate.

## <a name="ubuntu"></a>Ubuntu

1. Modificare il file `/etc/dhcp/dhclient6.conf` e aggiungere la riga seguente:

        timeout 10;

2. Modificare la configurazione di rete per l'interfaccia eth0 con le seguenti operazioni:

    * **Ubuntu 12.04 e 14.04**, modificare il file`/etc/network/interfaces.d/eth0.cfg`
    * Modificare il file **Ubuntu 16.04**`/etc/network/interfaces.d/50-cloud-init.cfg`

    ```bash
    iface eth0 inet6 auto
        up sleep 5
        up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.eth0.leases -v eth0 || true
    ```

3. Rinnovare indirizzo IPv6:

    ```bash
    # sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="debian"></a>Debian

1. Modificare il file `/etc/dhcp/dhclient6.conf` e aggiungere la riga seguente:

        timeout 10;

2. Modificare il file `/etc/network/interfaces` e aggiungere le seguenti operazioni:

        iface eth0 inet6 auto
            up sleep 5
            up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.eth0.leases -v eth0 || true

3. Rinnovare indirizzo IPv6:

    ```bash
    # sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="rhel--centos--oracle-linux"></a>RHEL / CentOS / Linux Oracle

1. Modificare il file `/etc/sysconfig/network` e aggiungere il parametro seguente:

        NETWORKING_IPV6=yes

2. Modificare il file `/etc/sysconfig/network-scripts/ifcfg-eth0` e aggiungere due parametri seguenti:

        IPV6INIT=yes
        DHCPV6C=yes

3. Rinnovare indirizzo IPv6:

    ```bash
    # sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="sles-11--opensuse-13"></a>SLES 11 & openSUSE 13

Immagini SLES e openSUSE recenti in Azure sono state preconfigurate con DHCPv6. Nessuna modifica aggiuntiva necessari quando si utilizzano tali immagini. Se si dispone di una macchina virtuale in base a un'immagine SUSE meno recente o personalizzata, procedere come segue:

1. Installare il `dhcp-client` creare un pacchetto, se necessario:

    ```bash
    # sudo zypper install dhcp-client
    ```

2. Modificare il file `/etc/sysconfig/network/ifcfg-eth0` e aggiungere il parametro seguente:

        DHCLIENT6_MODE='managed'

3. Rinnovare l'indirizzo IPv6:

    ```bash
    # sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="sles-12-and-opensuse-leap"></a>12 SLES e openSUSE bisestile

Immagini SLES e openSUSE recenti in Azure sono state preconfigurate con DHCPv6. Nessuna modifica aggiuntiva necessari quando si utilizzano tali immagini. Se si dispone di una macchina virtuale in base a un'immagine SUSE meno recente o personalizzata, procedere come segue:

1. Modificare il file `/etc/sysconfig/network/ifcfg-eth0` e sostituire questo parametro

        #BOOTPROTO='dhcp4'

    con il valore seguente:

        BOOTPROTO='dhcp'

2. Aggiungere il parametro seguente a `/etc/sysconfig/network/ifcfg-eth0`:

        DHCLIENT6_MODE='managed'

3. Rinnovare l'indirizzo IPv6:

    ```bash
    # sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="coreos"></a>CoreOS

Immagini CoreOS recenti in Azure sono state preconfigurate con DHCPv6. Nessuna modifica aggiuntiva necessari quando si utilizzano tali immagini. Se si dispone di una macchina virtuale in base a un'immagine di CoreOS meno recente o personalizzata, procedere come segue:

1. Modificare il file`/etc/systemd/network/10_dhcp.network`

        [Match]
        eth0

        [Network]
        DHCP=ipv6

2. Rinnovare l'indirizzo IPv6:

    ```bash
    # sudo systemctl restart systemd-networkd
    ```
