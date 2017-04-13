<properties
   pageTitle="Infrastruttura di aggiornamento Hat rosso (RHUI) | Microsoft Azure"
   description="Informazioni sulle rosso Hat aggiornamento dell'infrastruttura (RHUI) per le istanze di Red Hat Enterprise Linux su richiesta in Microsoft Azure"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="BorisB2015"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="10/14/2016"
   ms.author="borisb"/>

# <a name="red-hat-update-infrastructure-rhui-for-on-demand-red-hat-enterprise-linux-vms-in-azure"></a>Infrastruttura di aggiornamento Hat rosso (RHUI) per le macchine virtuali Linux Enterprise Hat rosso su richiesta in Azure

Per accedere a Red Hat aggiornamento dell'infrastruttura (RHUI) distribuita in Azure sono registrate macchine virtuali create da immagini di Red Hat Enterprise Linux (RHEL) su richiesta disponibile in Azure Marketplace.  Le istanze RHEL su richiesta hanno accesso a un archivio locale slurp e consente di ricevere aggiornamenti incrementali.

L'elenco di repository slurp, viene gestita da RHUI, è configurato l'istanza RHEL durante il provisioning. Non è necessario apportare le modifiche alla configurazione - eseguire `yum update` dopo l'istanza RHEL è pronta per ottenere gli aggiornamenti più recenti.

> [AZURE.NOTE] Infrastruttura di Azure RHUI aggiornata recente (settembre 2016) e richiede modifiche alla configurazione delle istanze di RHEL esistente per l'accesso a RHUI Azure senza interruzione. Fare riferimento alla sezione aggiornamento dell'infrastruttura di Azure RHUI per informazioni dettagliate.


## <a name="rhui-azure-infrastructure-update"></a>Aggiornamento dell'infrastruttura di Azure RHUI
In settembre 2016, quest'ultimo presenta un nuovo set di server Red Hat aggiornamento dell'infrastruttura (RHUI). Questi server vengono distribuiti con [Azure il traffico Manager]( https://azure.microsoft.com/services/traffic-manager/) in modo che un unico endpoint (rhui 1.micrsoft.com) può essere utilizzato da qualsiasi macchine Virtuali indipendentemente dall'area. È inoltre possibile utilizzare un certificato SSL appartenente a un'autorità di certificazione noto (radice Baltimora). Effettuare l'aggiornamento automatico sarebbe pericoloso per alcuni clienti che hanno ACL o tabelle di routing personalizzate per il server di aggiornamento RHUI, in modo che l'aggiornamento è "acconsentire aggiuntivo." Le procedure di valutazione a questi nuovi server sono disponibili in questa pagina e uno script completo per onboarding in modo automatico (dopo la verifica dei singoli passaggi). Le nuove immagini RHEL PAYG in Azure Marketplace (versioni maturate settembre 2016 o versioni successive) verranno automaticamente scegliere i nuovi server Azure RHUI e non richiede alcuna azione aggiuntiva.

### <a name="the-new-azure-rhui-infrastructure-onboarding-timeline"></a>La sequenza temporale nuova onboarding dell'infrastruttura di Azure RHUI

| Data | Nota |
| --- | --- |
|22 settembre 2016 | I server RHUI e istruzioni di installazione disponibili per l'utilizzo. Macchine virtuali distribuite mediante la visualizzazione della nuova (2016 settembre datata) immagini marketplace RHEL PAYG utilizzerà automaticamente i nuovi server RHUI, ma sono macchine virtuali esistenti "consenso esplicito in"
|1 novembre 2016 | Immagini di macchine Virtuali PAYG RHEL legacy (che utilizzano i server Azure RHUI precedenti) verranno rimossi dalla raccolta di Azure Marketplace
|16 gennaio 2017 | I server Azure RHUI precedenti verranno disattivati. Aggiornare tutti delle proprie macchine virtuali RHEL PAYG interessato da questo momento per garantire l'accesso a RHUI Azure

### <a name="the-ips-for-the-new-rhui-content-delivery-servers-are"></a>Sono gli indirizzi IP per il server di distribuzione di contenuti RHUI nuovo

```
# Azure Global
13.91.47.76
40.85.190.91
52.187.75.218
52.174.163.213

# Azure US Government
13.72.186.193
```

### <a name="manual-update-procedure-to-use-the-new-azure-rhui-servers"></a>Procedura di aggiornamento manuale da utilizzare i nuovi server Azure RHUI

Scaricare (tramite curvatura) la firma della chiave pubblica

```
curl -o RPM-GPG-KEY-microsoft-azure-release https://download.microsoft.com/download/9/D/9/9d945f05-541d-494f-9977-289b3ce8e774/microsoft-sign-public.asc 
```

Verificare il tasto scaricato

```
gpg --list-packets --verbose < RPM-GPG-KEY-microsoft-azure-release
```

Individuare l'output, verificare `keyid` e `user ID packet`:

```
Version: GnuPG v1.4.7 (GNU/Linux)
:public key packet:
        version 4, algo 1, created 1446074508, expires 0
        pkey[0]: [2048 bits]
        pkey[1]: [17 bits]
        keyid: EB3E94ADBE1229CF
:user ID packet: "Microsoft (Release signing) <gpgsecurity@microsoft.com>"
:signature packet: algo 1, keyid EB3E94ADBE1229CF
        version 4, created 1446074508, md5len 0, sigclass 0x13
        digest algo 2, begin of digest 1a 9b
        hashed subpkt 2 len 4 (sig created 2015-10-28)
        hashed subpkt 27 len 1 (key flags: 03)
        hashed subpkt 11 len 5 (pref-sym-algos: 9 8 7 3 2)
        hashed subpkt 21 len 3 (pref-hash-algos: 2 8 3)
        hashed subpkt 22 len 2 (pref-zip-algos: 2 1)
        hashed subpkt 30 len 1 (features: 01)
        hashed subpkt 23 len 1 (key server preferences: 80)
        subpkt 16 len 8 (issuer key ID EB3E94ADBE1229CF)
        data: [2047 bits]
```

Installare la chiave pubblica

```
sudo install -o root -g root -m 644 RPM-GPG-KEY-microsoft-azure-release /etc/pki/rpm-gpg
sudo rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-microsoft-azure-release
```

Scaricare, verificare e installare il Client RPM

Download: Per RHEL 6

```
curl -o azureclient.rpm https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel6/rhui-azure-rhel6-2.0-2.noarch.rpm 
```

Per RHEL 7

```
curl -o azureclient.rpm https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel7/rhui-azure-rhel7-2.0-2.noarch.rpm  
```

Verificare:

```
rpm -Kv azureclient.rpm
```

Selezionare nell'output tale firma del pacchetto è corretta

```
azureclient.rpm:
    Header V3 RSA/SHA256 Signature, key ID be1229cf: OK
    Header SHA1 digest: OK (927a3b548146c95a3f6c1a5d5ae52258a8859ab3)
    V3 RSA/SHA256 Signature, key ID be1229cf: OK
    MD5 digest: OK (c04ff605f82f4be8c96020bf5c23b86c)
```

Installare il sistema RPM

```
sudo rpm -U azureclient.rpm
```

Al termine, verificare che sia possibile accedere modulo di Azure RHUI la macchina virtuale

### <a name="all-in-one-script-for-automating-the-above-task"></a>In un'unica script per automatizzare l'attività precedente
Usare il seguente script in base alle esigenze per automatizzare l'operazione di aggiornamento macchine virtuali interessate ai nuovi server Azure RHUI.

```
# Download key
curl -o RPM-GPG-KEY-microsoft-azure-release https://download.microsoft.com/download/9/D/9/9d945f05-541d-494f-9977-289b3ce8e774/microsoft-sign-public.asc 

# Validate key
if ! gpg --list-packets --verbose < RPM-GPG-KEY-microsoft-azure-release | grep -q "keyid: EB3E94ADBE1229CF"; then
    echo "Keyfile azure.asc NOT valid. Exiting."
    exit 1
fi

# Install Key
sudo install -o root -g root -m 644 RPM-GPG-KEY-microsoft-azure-release /etc/pki/rpm-gpg
sudo rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-microsoft-azure-release

# Download RPM package
if grep -q "release 7" /etc/redhat-release; then
    ver=7
elif  grep -q "release 6" /etc/redhat-release; then
    ver=6
else
    echo "Version not supported, exiting"
    exit 1
fi

url=https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel$ver/rhui-azure-rhel$ver-2.0-2.noarch.rpm
curl -o azureclient.rpm "$url"

# Verify package
if ! rpm -Kv azureclient.rpm | grep -q "key ID be1229cf: OK"; then
    echo "RPM failed validation ($url)"
    exit 1
fi

# Install package
sudo rpm -U azureclient.rpm
```

## <a name="rhui-overview"></a>Panoramica RHUI
[Infrastruttura di aggiornamento Hat rosso](https://access.redhat.com/products/red-hat-update-infrastructure) offre una soluzione scalabilità per gestire il contenuto di archivio slurp per le istanze del cloud Red Hat Enterprise Linux ospitate dal provider di servizi cloud Red Hat tramite certificazione incrociata. Basato sul progetto Paste padre, RHUI consente di provider di servizi cloud localmente fedelmente ospitato Red Hat archivio contenuto, creare archivi personalizzati con il proprio contenuto, e apportare tali archivi disponibili per un grande numero di utenti finali tramite un sistema di recapito bilanciamento del carico al contenuto.

## <a name="regions-where-rhui-is-available"></a>Aree in cui RHUI è disponibile
RHUI è disponibile in tutti i paesi in cui sono disponibili immagini su richiesta RHEL. Includa attualmente pubbliche tutte le aree geografiche elencate nella pagina [dashboard stato Azure](https://azure.microsoft.com/status/) e aree di Azure IT per la pubblica amministrazione. Accesso RHUI per macchine virtuali viene completato il provisioning di immagini su richiesta RHEL è incluso nel loro prezzo. Disponibilità di altre internazionali/nazionale cloud verrà aggiornata di disponibilità su richiesta RHEL in futuro.

> [AZURE.NOTE] Accesso a RHUI ospitato Azure è limitata ai macchine virtuali all'interno di [intervalli di indirizzi IP di Microsoft Azure Data Center](https://www.microsoft.com/download/details.aspx?id=41653).

## <a name="get-updates-from-another-update-repository"></a>Ottenere gli aggiornamenti da un altro aggiornamento repository

Se è necessario ottenere gli aggiornamenti da un archivio di aggiornamento diverso (invece di Azure ospitato RHUI) è necessario annullare la registrazione le istanze di RHUI e registrare nuovamente con l'infrastruttura di aggiornamento desiderato (ad esempio un accento circonflesso rosso o Red Hat cliente portale CDN). È necessario sottoscrizioni Red Hat appropriate per tali servizi e la registrazione per [L'accesso Cloud Hat rosso in Azure](https://access.redhat.com/ecosystem/partners/ccsp/microsoft-azure).

Per annullare la registrazione RHUI e ripetere la registrazione per il completamento dell'infrastruttura di aggiornamento la seguente procedura.

1.  Modificare /etc/yum.repos.d/rh-cloud.repo e modificare tutti `enabled=1` a `enabled=0`. Per esempio:

        # sed -i 's/enabled=1/enabled=0/g' /etc/yum.repos.d/rh-cloud.repo

2.  Modificare /etc/yum/pluginconf.d/rhnplugin.conf e `enabled=0` a `enabled=1`.
3.  Quindi eseguire la registrazione con l'infrastruttura desiderato, ad esempio portale clienti Hat rosso. Seguire Red Hat soluzione Guida su [come eseguire la registrazione e sottoscrivere un sistema per il portale di clienti Hat rosso](https://access.redhat.com/solutions/253273).

> [AZURE.NOTE] Accesso a RHUI ospitato Azure è incluso nel prezzo di immagine RHEL uso prepagato (PAYG). Annullamento della registrazione di una macchina virtuale RHEL PAYG da RHUI ospitato Azure non converte la macchina virtuale in tipo di porta Your-proprietari-licenza (BYOL) macchine Virtuali e quindi si possono essere costi doppia se si registra la macchina virtuale stessa con un'altra origine degli aggiornamenti. 
> 
> Se è necessario utilizzare diverso da un'infrastruttura di aggiornamento in modo coerente ospitato Azure RHUI valutare la possibilità di creare e distribuire le proprie immagini (tipo di BYOL) come descritto nel [creare e caricare Red Hat base virtual machine per Azure](virtual-machines-linux-redhat-create-upload-vhd.md) .

## <a name="next-steps"></a>Passaggi successivi
Per creare una macchina virtuale di Red Hat Enterprise Linux da utilizzare e l'uso prepagato di Azure Marketplace immagine ospitato Azure RHUI passare a [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/redhat/). Sarà possibile usare `yum update` nell'istanza RHEL senza eventuali impostazioni aggiuntive.