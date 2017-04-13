<properties
   pageTitle="Utilizzando il DNS dinamico per registrare i nomi host"
   description="Questa pagina offre informazioni dettagliate su come configurare il DNS dinamico per registrare nomi host nel server DNS."
   services="dns"
   documentationCenter="na"
   authors="GarethBradshawMSFT"
   manager="carmonm"
   editor="" />
<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/31/2016"
   ms.author="sewhee" />

# <a name="using-dynamic-dns-to-register-hostnames-in-your-own-dns-server"></a>Utilizzando il DNS dinamico per registrare i nomi host nel server DNS

[Azure offre la risoluzione dei nomi](virtual-networks-name-resolution-for-vms-and-role-instances.md) per macchine () e istanze del ruolo. Tuttavia, quando la risoluzione dei nomi deve andare oltre a quelli forniti da Azure, è possibile fornire server DNS. In questo modo la possibilità di personalizzare la soluzione DNS in base alle esigenze specifiche. Ad esempio, potrebbe essere necessario accedere alle risorse locali mediante il controller di dominio Active Directory.

Quando i server DNS personalizzati sono ospitati come macchine virtuali di Azure consente di inoltrare query hostname per vnet stesso a Azure risolvere nome host. Se non si desidera utilizzare questo percorso, è possibile registrare il nome host macchine Virtuali nel server DNS utilizzando il DNS dinamico.  Azure non è in grado (ad esempio credenziali) per creare record direttamente nel server DNS, disposizioni alternativi spesso sono necessari. Ecco alcuni scenari comuni con alternative.

## <a name="windows-clients"></a>Client di Windows

Client di Windows non dominio tentare non protetta gli aggiornamenti DNS dinamici (DNS dinamico) quando si avvia o quando si modifica il proprio indirizzo IP. Il nome DNS è il nome host più il suffisso DNS. Azure lasciati vuoti suffisso DNS, ma è possibile impostare in macchine Virtuali, tramite [l'interfaccia utente](https://technet.microsoft.com/library/cc794784.aspx) o [utilizzando l'automazione](https://social.technet.microsoft.com/forums/windowsserver/3720415a-6a9a-4bca-aa2a-6df58a1a47d7/change-primary-dns-suffix).

Dominio client Windows registrare gli indirizzi IP di controller di dominio tramite DNS dinamici protetti. Il processo di connessione al dominio imposta il suffisso DNS sul client e crea e mantiene la relazione di attendibilità.

## <a name="linux-clients"></a>Client Linux

Client Linux in genere non registrano con il server DNS all'avvio, si presume che indica il server DHCP. Server DHCP di Azure non ha la possibilità o le credenziali per registrare i record del server DNS.  È possibile usare uno strumento denominato *nsupdate*, incluso nel pacchetto di associazione, per inviare gli aggiornamenti DNS dinamici. Poiché il protocollo DNS dinamico è standardizzato, è possibile utilizzare *nsupdate* anche quando non si usa associazione sul server DNS.

È possibile utilizzare i collegamenti vengono forniti dal client DHCP per creare e gestire la voce hostname nel server DNS. Durante il ciclo DHCP, il client esegue gli script */etc/dhcp/dhclient-exit-hooks.d/*. Può essere utilizzato per registrare il nuovo indirizzo IP utilizzando *nsupdate*. Per esempio:

        #!/bin/sh
        requireddomain=mydomain.local

        # only execute on the primary nic
        if [ "$interface" != "eth0" ]
        then
            return
        fi

        # when we have a new IP, perform nsupdate
        if [ "$reason" = BOUND ] || [ "$reason" = RENEW ] ||
           [ "$reason" = REBIND ] || [ "$reason" = REBOOT ]
        then
            host=`hostname`
            nsupdatecmds=/var/tmp/nsupdatecmds
              echo "update delete $host.$requireddomain a" > $nsupdatecmds
              echo "update add $host.$requireddomain 3600 a $new_ip_address" >> $nsupdatecmds
              echo "send" >> $nsupdatecmds

              nsupdate $nsupdatecmds
        fi

        #done
        exit 0;

È anche possibile usare il comando *nsupdate* per eseguire aggiornamenti DNS dinamici protetti. Ad esempio, quando si usa un server DNS associare, una coppia di chiave pubblica privata viene [generato](http://linux.yyz.us/nsupdate/).  Il server DNS è [configurato](http://linux.yyz.us/dns/ddns-server.html) con la parte della chiave pubblica in modo da poter verificare la firma nella richiesta. È necessario utilizzare l'opzione *-k* per fornire che la coppia di chiave da *nsupdate* affinché il DNS dinamico Aggiorna richiesta da firmare.

Quando si usa un server DNS Windows, è possibile utilizzare l'autenticazione Kerberos con il parametro *-g* in *nsupdate* (non disponibile nella versione di Windows di *nsupdate*). Per eseguire questa operazione, utilizzare *kinit* per caricare le credenziali (ad esempio da un [file keytab](http://www.itadmintools.com/2011/07/creating-kerberos-keytab-files.html)). Quindi *nsupdate g* preleverà le credenziali dalla cache.

Se necessario, è possibile aggiungere un suffisso di ricerca DNS nelle macchine virtuali. Il suffisso DNS specificato nel file */etc/resolv.conf* . La maggior parte dei distros Linux per gestire automaticamente il contenuto del file, quindi in genere non è possibile modificarlo. Tuttavia, è possibile ignorare il suffisso utilizzando *sostituisce il* comando del client. A tale scopo, in */etc/dhcp/dhclient.conf*, aggiungere:

        supersede domain-name <required-dns-suffix>;

