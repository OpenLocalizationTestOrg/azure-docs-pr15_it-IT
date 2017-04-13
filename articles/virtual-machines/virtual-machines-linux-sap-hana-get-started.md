<properties
   pageTitle="Guida rapida per l'installazione manuale di HANA SAP in macchine virtuali di Azure | Microsoft Azure"
   description="Guida rapida per l'installazione manuale di HANA SAP in macchine virtuali di Azure"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="hermanndms"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"
   keywords=""/>
<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="09/15/2016"
   ms.author="hermannd"/>

# <a name="quickstart-guide-for-manual-installation-of-single-instance-sap-hana-on-azure-vms"></a>Guida rapida per l'installazione manuale della singola istanza SAP HANA in macchine virtuali di Azure

## <a name="introduction"></a>Introduzione

Questa Guida introduttiva Guida configurare un singola istanza SAP HANA prototipo/demo sistema in macchine virtuali di Azure da un'installazione manuale di SAP NetWeaver 7.5 e SAP HANA SP12.
La guida presuppone che il lettore sia familiarità con concetti di base di Azure IaaS come come distribuire macchine virtuali o reti virtuali tramite il portale di Azure o Powershell/CLI, inclusi l'opzione per usare i modelli di json. Inoltre è probabile che il lettore conoscano HANA SAP, SAP NetWeaver e come installare locale.

È previsto l'utilità per la lettura è a conoscenza della documentazione di Azure SAP generale indicata nella sezione informazioni generali alla fine dell'articolo.

A causa di limitazioni ai sistemi di produzione non questa Guida non tratta argomenti come elevata, eseguire il backup, DR, prestazioni o considerazioni sulla protezione speciale.

La configurazione di esempio venisse eseguita utilizzando due macchine virtuali per eseguire un'installazione SAP NetWeaver distribuita tramite il modello di gestione di risorse Azure come SAP-Linux-Azure è supportato solo in Gestione risorse di Azure e non dal modello classico. Nella sezione informazioni generali alla fine di questo articolo, è possibile trovare i collegamenti a ulteriori informazioni sulla gestione di risorse di Azure.

Queste sono le macchine virtuali due test utilizzate per l'installazione di esempio:

* Hana-appsrv (tipo DS3) per ospitare l'istanza di ricevere 7.5 ASCS + PAS
* Hana dbsrv (tipo GS4) all'host HANA SP12
* entrambi macchine virtuali appartenevano a una rete virtuale Azure (azure hana-test vnet)
* Sistema operativo in entrambi i casi è stata SLES 12 SP1


Prestare particolare attenzione del fatto attraverso prima del 2016 luglio HANA SAP è solo interamente supportata per sistemi di produzione OLAP (bianco e nero) tipo macchine Virtuali di Azure GS5. Nel punto in cui una non è previsto il supporto SAP ufficiale a scopo di testing è possibile utilizzare un nome più piccole come ad esempio GS4.
Che cosa deve sempre essere utilizzato per lo spazio di archiviazione di Azure premium per i file di dati e log HANA - HANA SAP in Azure vedere "Disco di installazione" sezione ulteriormente verso il basso. Relative alle ulteriori dettagli sui quali SAP prodotti supportati in Azure, controllare la sezione informazioni generali alla fine di questo articolo.


La Guida vengono descritti due diversi modi per installare manualmente HANA SAP in macchine virtuali di Azure:

* installare SAP HANA tramite SAP Software Provisioning Manager (SWPM) come parte di un'installazione NetWeaver distribuita nel passaggio "istanza di database"
* installare HANA SAP usando il hdblcm strumento di gestione del ciclo di vita HANA e quindi NetWeaver in un secondo momento

È inoltre possibile utilizzare SWPM e installare tutti i componenti (SAP HANA, server di applicazioni SAP, istanza ASCS, grafica SAP) in una macchina virtuale singola. Questa opzione non è descritto in questo articolo, ma gli elementi che devono essere considerate sono le stesse.

Prima di avviare un'installazione sezione successiva gli elenchi di controllo sotto sull'impostazione di macchine virtuali test Azure deve essere letti per evitare errori di base diverse che verranno eseguita quando si utilizza solo una configurazione di macchine Virtuali di Azure predefinita.


## <a name="checklist-sap-hana-installation-via-sap-swpm"></a>Elenco di controllo installazione SAP HANA tramite SWPM SAP

Si tratta di un semplice elenco di controllo della chiave installare gli elementi relativi a un'installazione di SAP HANA singola istanza manuale per demo o la creazione di prototipi pursposes tramite SWPM SAP eseguendo una distribuito 7.5 ricevere SAP. I singoli elementi sono stati spiegati e visualizzati nel modulo di schermate in modo più dettagliato resto di questo articolo:

* creare una rete virtuale Azure ad macchine virtuali due test 
* distribuire due macchine virtuali di Azure con sistema operativo SLES 12 SP1 tramite Gestione risorse Azure modello 
* collegare due dischi standard dello spazio di archiviazione sul server app macchine Virtuali (ad esempio 75GB e 500GB)
* allegare quattro dischi al server di database HANA macchine Virtuali, ad esempio per il server di app macchine Virtuali di archiviazione standard 2 dischi + 2 dischi di archiviazione premium (ad esempio 2x512GB)
* in base ai requisiti di dimensione e/o velocità allegare più dischi e creare volumi a strisce con sia con lvm o mdadm sul livello del sistema operativo all'interno della macchina virtuale
* creare sistemi di file XFS dischi collegati / logico volumi
* installare i sistemi di file XFS nuovi nel livello del sistema operativo. Utilizzare un file System per mantenere tutto il software SAP e quello ad esempio, per la directory sapmnt e forse backup. Nel database HANA SAP montaggio server la XFS file System dischi archiviazione premium come /hana e /usr/sap si tratta in tutte le necessarie per evitare che il file System radice che non è troppo grande in macchine virtuali di Azure Linux Riempie verso l'alto
* Immettere gli indirizzi ip locale del test macchine virtuali nel/ecc/host
* Immettere il parametro nofail in /etc/fstab.
* impostare i parametri kernel in base alla nota SAP HANA-SLES-12 (vedere i dettagli più in basso nella sezione parametri kernel)
* aggiungere spazio scambiare
* Se si desidera - installare un desktop con interfaccia grafico in macchine virtuali di test. In caso contrario utilizzare un'installazione remota sapinst
* scaricare il software SAP dal marketplace di servizio SAP
* installare l'istanza di SAP ASCS nel server app macchine Virtuali
* condividere la directory sapmnt tramite NFS tra il test macchine virtuali (server app macchine Virtuali è il server NFS)
* installare l'istanza del database inclusi HANA tramite SWPM nel server di database macchine Virtuali
* installare il PAS nel server app macchine Virtuali
* avviare MC SAP e connettersi ad esempio tramite interfaccia grafica SAP / HANA Studio 



## <a name="checklist-sap-hana-installation-via-hdblcm"></a>Elenco di controllo installazione SAP HANA tramite hdblcm

Si tratta di un semplice elenco di controllo della chiave installare gli elementi relativi a un'installazione di SAP HANA singola istanza manuale per demo o la creazione di prototipi pursposes tramite SWPM SAP eseguendo una distribuito 7.5 ricevere SAP. I singoli elementi sono stati spiegati e visualizzati nel modulo di schermate in modo più dettagliato resto di questo articolo:

* creare una rete virtuale Azure ad macchine virtuali due test 
* distribuire due macchine virtuali di Azure con sistema operativo SLES 12 SP1 tramite Gestione risorse Azure modello 
* collegare due dischi standard dello spazio di archiviazione sul server app macchine Virtuali (ad esempio 75GB e 500GB)
* allegare quattro dischi al server di database HANA macchine Virtuali - 2 standard spazio di archiviazione ad esempio per il server di app macchine Virtuali + 2 dischi di archiviazione premium (ad esempio 2x512GB)
* in base ai requisiti di dimensione e/o velocità allegare più dischi e creare volumi a strisce con sia con lvm o mdadm sul livello del sistema operativo all'interno della macchina virtuale
* creare sistemi di file XFS dischi collegati / logico volumi
* installare i sistemi di file XFS nuovi nel livello del sistema operativo. Utilizzare un file System per mantenere tutto il software SAP e quello ad esempio, per la directory sapmnt e forse backup. Nel database HANA SAP montaggio server la XFS file System dischi archiviazione premium come /hana e /usr/sap si tratta in tutte le necessarie per evitare che il file System radice che non è troppo grande in macchine virtuali di Azure Linux Riempie verso l'alto
* Immettere gli indirizzi ip locale del test macchine virtuali nel/ecc/host
* Immettere il parametro nofail in /etc/fstab.
* impostare i parametri kernel in base alla nota SAP HANA-SLES-12 (vedere i dettagli più in basso nella sezione parametri kernel)
* aggiungere spazio scambiare
* Se si desidera - installare un desktop con interfaccia grafico in macchine virtuali di test. In caso contrario utilizzare un'installazione remota sapinst
* scaricare il software SAP dal marketplace di servizio SAP
* Crea gruppo "sapsys" con id di gruppo 1001 nella macchina HANA DB Server virtuale
* installare HANA SAP nel server di database macchine Virtuali mediante hdblcm
* installare l'istanza di SAP ASCS nel server app macchine Virtuali
* condividere la directory sapmnt tramite NFS tra il test macchine virtuali (server app macchine Virtuali è il server NFS)
* installare l'istanza del database inclusi HANA tramite SWPM nel server di database macchine Virtuali
* installare il PAS nel server app macchine Virtuali
* avviare MC SAP e connettersi ad esempio tramite interfaccia grafica SAP / HANA Studio 




## <a name="prepare-azure-vms-for-manual-installation-of-sap-hana"></a>Preparare macchine virtuali di Azure per l'installazione manuale di HANA SAP

In questo capitolo sulla preparazione macchine virtuali di Azure per l'installazione manuale di SAP HANA è costituito da cinque sezioni che riguardano i seguenti argomenti:

* Imposta su disco
* Parametri del kernel
* Quelli
* via/host
* via/fstab


### <a name="disk-setup"></a>Imposta su disco

Il file System radice in una VM Linux su Azure è di dimensioni limitate. Pertanto è necessario aggiungere ulteriore spazio su disco una macchina virtuale per l'esecuzione di SAP. In caso di un server di app SAP che macchine Virtuali utilizzato in un ambiente pure prototipo/demo è possibile utilizzare l'archiviazione standard Azure dischi. Mentre per i file di dati e log SAP HANA DB - dischi di archiviazione di Azure Premium devono essere utilizzati anche in orizzontale non di produzione.

Vedere alcune informazioni dettagliate su come allegare dischi a una VM Linux [qui](virtual-machines-linux-add-disk.md)

Quando si tratta di la cache del disco Azure - devono usare "Nessuno" per dischi che verranno utilizzati per archiviare i registri delle transazioni HANA. Per HANA file di dati che è ok usare leggere la memorizzazione nella cache. Come HANA è un database in memoria che dipende il modello di utilizzo complessivo quantità cache di lettura a livello di Azure disco miglioreranno (ad esempio avvio HANA e si leggono dati dal disco in memoria).

Visualizzare i dettagli dello spazio di archiviazione di Azure Premium [qui](../storage/storage-premium-storage.md)

[Di seguito](https://github.com/Azure/azure-quickstart-templates) si trovano esempio json modelli per creare macchine virtuali.
La "101-macchine virtuali-semplici-linux" viene illustrato l'aspetto di un modello di base come compresa la sezione dello spazio di archiviazione che aggiunge un disco dati 100GB.

[In questo articolo](virtual-machines-linux-sap-on-suse-quickstart.md) include alcune informazioni su come individuare un'immagine SUSE tramite Powershell o CLI e la priorità per allegare un disco tramite UUID.


A seconda delle dimensioni dei requisiti di sistema e la velocità effettiva potrebbe essere necessario collegare più dischi anziché una e versioni successive su Crea una striscia impostata su tali dischi a livello del sistema operativo. Di seguito sono due aspetti in perché viene creato un strisce impostata su più dischi Azure:

* aumentare la velocità
* un singolo file System > 1TB è necessario che il limite di dimensioni disco Azure corrente è 1TB (dal 2016 luglio)


Ulteriori informazioni relative ai due strumenti principale per configurare striping sono disponibili qui:

[Articolo sull'utilizzo mdadm per configurare raid software Linux su una macchina virtuale Azure](virtual-machines-linux-configure-raid.md)

[L'articolo sulla configurazione di gestione dei volumi logici in una macchina virtuale Azure Linux](virtual-machines-linux-configure-lvm.md)



![](./media/virtual-machines-linux-sap-hana-get-started/image003.jpg)

Nel test ambiente due archiviazione standard Azure dischi sono stati collegati al server di app SAP macchine Virtuali. Ne è stato utilizzato per archiviare tutto il software SAP per l'installazione (ad esempio NetWeaver 7.5, grafica, SAP SAP HANA... ) e quella di spazio sufficiente per tutti i valori potrebbero essere necessari (ad esempio, eseguire il backup, dati di test) e la directory sapmnt (ad esempio profili SAP) per essere condivisi tra tutte le macchine virtuali che appartengono allo stesso orizzontale SAP.

![](./media/virtual-machines-linux-sap-hana-get-started/image004.jpg)

Contrasto con il server app dischi macchine Virtuali quattro sono stati collegati al server SAP HANA macchine Virtuali. Come prima due dischi sono stati utilizzati per mantenere il software SAP (uno inoltre possibile condividere il disco di software SAP tramite NFS) e con spazio sufficiente ad esempio per il backup. Altri due dischi sono dischi di archiviazione di Azure Premium per mantenere i file di dati e log HANA SAP, nonché la directory /usr/sap.


### <a name="kernel-parameters"></a>Parametri del kernel


SAP HANA richiede specifiche impostazioni Linux che non fanno parte delle immagini raccolta Azure standard ed sono necessario impostare manualmente. Esiste una nota specifica di SAP che descrive le impostazioni. 


Nota SAP SAP HANA DB: OS impostazioni consigliate per SLES 12 / SLES per SAP applicazioni 12: [2205917 nota SAP](https://launchpad.support.sap.com/#/notes/2205917)

Possibile trovarne uno argomento aggiuntive reagrding cache della pagina correlate all'esecuzione HANA SAP in SLES [qui](https://www.suse.com/documentation/sles_for_sap/singlehtml/sles_for_sap_guide/sles_for_sap_guide.html#sec.s4s.configure.page-cache) capitolo 6.1 Kernel: il limite della Cache di pagina

È inoltre disponibile una nota SAP relativa al limite di cache delle pagine [1557506 nota SAP](https://service.sap.com/sap/support/notes/1557506)

12 SLES dispone di un nuovo strumento che sostituisce l'utilità sapconf precedente. È "adm ottimizzati" e un profilo SAP HANA speciale è disponibile. Una possibile trovare informazioni dettagliate su questo strumento collegamento due riportato di seguito.

Sono disponibili documentazione SLES sulle adm ottimizzati profilo sap-hana [qui](https://www.suse.com/documentation/sles-for-sap-12/book_s4s/data/sec_s4s_configure_sapconf.html)

Documentazione SLES sulle adm ottimizzati profilo sap hana - capitolo 6.2 ottimizzazione sistemi per carichi di lavoro SAP con adm ottimizzati - sono disponibili [qui](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/book_s4s/book_s4s.pdf)


![](./media/virtual-machines-linux-sap-hana-get-started/image005.jpg)

Qui si può osservare come "adm ottimizzati" modificato il transparent_hugepage, nonché i valori di numa_balancing in base alle impostazioni SAP HANA necessarie.


Per rendere le impostazioni di SAP HANA permanenti è necessario utilizzare grub2 su SLES 12. Sono disponibili ulteriori informazioni sulla grub2 [qui](https://www.suse.com/documentation/sled-12/book_sle_admin/data/sec_grub2_file_structure.html)


![](./media/virtual-machines-linux-sap-hana-get-started/image006.jpg)

Questa schermata mostra come le impostazioni sono state modificate in file di configurazione e quindi "compilate" tramite grub2 mkconfig


![](./media/virtual-machines-linux-sap-hana-get-started/image007.jpg)

In alternativa, è possibile modificare le impostazioni tramite Yast e le impostazioni di parametro caricatore di avvio.


### <a name="filesystems"></a>Quelli 

![](./media/virtual-machines-linux-sap-hana-get-started/image008.jpg)

Uno qui possibile visualizzare i sistemi di due file che sono stati creati nel server di app SAP macchine Virtuali nella parte superiore di due dischi di archiviazione Azure standard. Sia quelli di tipo XFS e installato /sapdata e /sapsoftware.

Non è necessario gestirlo in questo modo. Sono disponibili diverse opzioni come sturcture lo spazio su disco.
L'aspetto più importante consiste nell'evitare che il file System radice ha più spazio disponibile. 


![](./media/virtual-machines-linux-sap-hana-get-started/image009.jpg)

Per quanto riguarda la macchina virtuale DB HANA SAP è importante sapere che durante l'installazione un database tramite sapinst (swpm) e usando l'opzione di installazione "tipica" semplice verrà installato oggetti per impostazione predefinita in /hana e /usr/sap. L'impostazione predefinita per il backup del Registro di SAP HANA è ad esempio in /usr/sap.
Come prima della chiave per evitare che il file System radice ha più spazio disponibile. Pertanto uno deve verificare che sia spazio sufficiente in /hana e /usr/sap prima di installare HANA SAP tramite swpm.

[In questo articolo](http://help.sap.com/saphelp_hanaplatform/helpdata/en/4c/24d332a37b4a3caad3e634f9900a45/frameset.htm) da SAP descrive il layout standard file System di HANA SAP 


![](./media/virtual-machines-linux-sap-hana-get-started/image010.jpg)

Quando si installa SAP NetWeaver su un'immagine di raccolta SLES 12 Azure standard si verificherà un messaggio che non è disponibile spazio Scambia. Per rimuovere il messaggio uno potrebbe ad esempio aggiungere manualmente un file di scambio come descritto in questo documento tramite gg, mkswap e swapon. Una ricerca di "Aggiunta di un scambiare File manualmente" in [questo articolo](https://www.suse.com/documentation/sled-12/book_sle_deployment/data/sec_yast2_i_y2_part_expert.html)

È anche possibile configurare lo spazio di scambio tramite l'agente Linux VM. Altre informazioni sono disponibili [qui](virtual-machines-linux-agent-user-guide.md)


### <a name="etchosts"></a>via/host

![](./media/virtual-machines-linux-sap-hana-get-started/image011.jpg)

Un altro aspetto importante prima di iniziare a installare SAP è da includere nel file /etc/hosts host nomi e indirizzi IP di macchine virtuali SAP. Uno deve distribuirle tutti i SAP all'interno di una rete virtuale Azure e quindi utilizzare gli indirizzi IP interni.

### <a name="etcfstab"></a>via/fstab

![](./media/virtual-machines-linux-sap-hana-get-started/image000c.jpg)

Durante la fase di test è stato eseguito da una buona idea aggiungere il parametro nofail fstab. Se si verifichino problemi con i dischi macchina virtuale sarebbe ancora brevi e non si blocca nel processo di avvio. Ma è necessario prestare particolare come in questo caso l'ulteriore spazio su disco potrebbero non essere disponibile e processi Impossibile riempire quello principale. Nel caso in cui /hana sarebbe mancanti HANA SAP non iniziare se tutto.


## <a name="install-graphical-gnome-desktop-on-sles-12"></a>Installare desktop Gnome grafica in SLES 12

In questo capitolo è costituito da due secitions che riguardano i seguenti argomenti:

* Installazione del desktop Gnome e xrdp SLES 12
* Esecuzione basato su Java SAP MC in Firefox 12 SLES

Può anche usare alternative come Xterminal, VNC ma prima del separatore 2016 in questo articolo vengono illustrati solo xrdp.

### <a name="installation-of-gnome-desktop-and-xrdp-on-sles-12"></a>Installazione del desktop Gnome e xrdp SLES 12

In particolare per coloro che dispongono di sfondo di Microsoft Windows e si desidera utilizzare un desktop con interfaccia grafico direttamente in macchine virtuali Linux SAP per eseguire Firefox, Sapinst, grafica SAP, SAP MC o HANA Studio e forse connettersi al supporto virtuale tramite RDP da un computer Microsoft Windows esiste un modo semplice per eseguire questa operazione. Anche se questo potrebbe non essere appropriato, ad esempio per un server di database di produzione è ok per un ambiente pure prototipo/demo. Ecco i passaggi per installare il desktop Gnome in una macchina virtuale di Azure SLES 12:

installare il desktop gnome tramite il comando seguente (ad esempio in una finestra putty):

   zypper nel modello -t gnome basic

quindi installare xrdp per consentire la connessione al supporto virtuale tramite RDP:

   zypper in xrdp

modificare /etc/sysconfig/windowmanager e impostare Gnome lo strumento di gestione di windows predefinito:

   DEFAULT_WM = "gnome"

eseguire chkconfig per assicurarsi che xrdp viene avviato automaticamente dopo il riavvio: 

  chkconfig-xrdp di livello 3 in

nel caso in cui dovrebbe essere presente un problema con la connessione RDP provare a riavviare (forse dalla finestra putty):

  riavvio /etc/xrdp/xrdp.sh

nel caso in cui il riavvio di xrdp come indicati in precedenza non funzionano controllo se esiste un file .pid e rimuoverlo:

  Controllare /var/run e cercare xrdp.pid   
  rimuovere e quindi riprovare a eseguire il riavvio



### <a name="sap-mc"></a>MC SAP


Per avviare la grafica MC SAP basato su Java fuori Firefox in esecuzione in una macchina virtuale 12 SLES Azure dopo l'installazione di Gnome dekstop come descritto nella sezione precedente uno verrà visualizzato un errore a causa di mancanti plug-in linguaggio browser.

L'URL per avviare MC SAP <server>: 5 < instance_number > 13

Sono disponibili ulteriori dettagli [qui](https://help.sap.com/saphelp_nwce10/helpdata/en/48/6b7c6178dc4f93e10000000a42189d/frameset.htm)


![](./media/virtual-machines-linux-sap-hana-get-started/image013.jpg)

Nella schermata precedente una possibile visualizzare il messaggio di errore Impossibile ad esempio quando non è presente il plug-in linguaggio Browser. 

![](./media/virtual-machines-linux-sap-hana-get-started/image014.jpg)

Un'opzione per risolvere il problema è sufficiente per installare il plug-in mancanti tramite Yast.

![](./media/virtual-machines-linux-sap-hana-get-started/image015.jpg)

Ripetizione URL SAP MC verrà visualizzato un po' di tempo la prima finestra di dialogo in cui viene richiesto di attivare il plug-in.


Un problema aggiuntivo che potrebbe appare è un messaggio di errore relativo a un file mancante: javafx.properties è molto probabile correlati all'installazione di linguaggio 1.8 richiesto per 7.4 grafica SAP

La versione di IBM Java visualizzata tramite Yast non include il file. La soluzione è un download di linguaggio da Oracle.
È disponibile un articolo che parla questo problema specifico [qui](https://scn.sap.com/thread/3908306)



## <a name="manual-sap-hana-installation-via-swpm-as-part-of-a-netweaver-75-installation"></a>Installazione manuale HANA SAP tramite SWPM come parte di un'installazione NetWeaver 7.5


L'elenco di screenshot seguente mostra i passaggi principali per l'installazione SAP NetWeaver 7.5 e SAP HANA SP12 tramite SWPM (sapinst). Come parte di un 7.5 ricevere installazione SWPM include le funzionalità per installare anche il database HANA come una singola istanza.


![](./media/virtual-machines-linux-sap-hana-get-started/image012.jpg)

Per il test di esempio è stato installato server ambiente sola ABAP app. L'opzione "Distributed System" utilizzato per installare il ASCS e l'istanza di server primario App in "uno" macchine Virtuali di Azure e SAP HANA come il sistema di database in un altro macchine Virtuali Azure.


![](./media/virtual-machines-linux-sap-hana-get-started/image016.jpg)

Dopo che l'istanza ASCS installato sul server app macchine Virtuali ed è impostata su "verde" in SAP MC la directory sapmnt che include ad esempio la directory di profilo SAP deve essere condiviso con il server SAP HANA DB macchine Virtuali.
Schermata di installazione DB richiede l'accesso a queste informazioni. Il modo migliore consiste nell'utilizzare NFS che possono essere configurate utilizzando Yast.


![](./media/virtual-machines-linux-sap-hana-get-started/image017b.jpg)

App di server macchine Virtuali nella directory sapmnt devono essere condivise tramite NFS utilizzando opzioni "rw" e "no_root_squash". Valore predefinito è "sola lettura" e "root_squash", che possono causare problemi durante l'installazione l'istanza del database.


![](./media/virtual-machines-linux-sap-hana-get-started/image018b.jpg)

Nel server di database di HANA SAP macchine Virtuali di sapmnt condividere dal server app macchine Virtuali deve essere configurata tramite "client NFS" (ad esempio, con l'aiuto di Yast)


![](./media/virtual-machines-linux-sap-hana-get-started/image019.jpg)

Quindi è necessario accedere al server di database di HANA SAP macchine Virtuali e iniziare a SWPM per completare il passaggio successivo di un'installazione di ricevere 7.5 distribuita - "Istanza di Database".


![](./media/virtual-machines-linux-sap-hana-get-started/image035b.jpg)

Relativo all'installazione di SAP HANA c'è infatti troppo immettere dopo aver selezionato un'installazione "tipica". Inoltre è il percorso per il supporto di installatiom uno immettere un SID DB, il nome host, il numero di istanza e la password di amministratore di sistema DB.

 
![](./media/virtual-machines-linux-sap-hana-get-started/image036b.jpg)

Passaggio successivo consiste nell'immettere la password per lo schema DBACOCKPIT.



![](./media/virtual-machines-linux-sap-hana-get-started/image037b.jpg)

Quindi viene fornita la domanda per la password di schema SAPABAP1.


![](./media/virtual-machines-linux-sap-hana-get-started/image023.jpg)

Alla fine dovrebbero essere solo segni di spunta verdi che precede tutte le fasi del processo di installazione DB e la finestra di messaggio piccola che appare dovrebbe essere indicato "esecuzione di... Database che istanza è stata completata".

 
![](./media/virtual-machines-linux-sap-hana-get-started/image024.jpg)

Al termine dell'installazione MC SAP deve essere visualizzata anche l'istanza DB come "verde" e l'elenco completo dei processi SAP HANA (ad esempio hdbindexserver, hdbcompileserver)


![](./media/virtual-machines-linux-sap-hana-get-started/image025.jpg)

Questa schermata mostra parti della struttura di file in /hana/shared che SWPM creato durante l'installazione di HANA. Si è non verificato la possibilità di specificare un percorso diverso. Pertanto è importante installare ulteriore spazio su disco in /hana prima dell'installazione di SAP HANA tramite SWPM per evitare che venga eseguito il file System radice spazio disponibile.


![](./media/virtual-machines-linux-sap-hana-get-started/image026.jpg)

Di seguito si può osservare la stessa operazione come descritto in precedenza per la directory /usr/sap.


![](./media/virtual-machines-linux-sap-hana-get-started/image027b.jpg)

Al termine dell'installazione ABAP distribuita è "Primaria istanza dell'applicazione Server"


![](./media/virtual-machines-linux-sap-hana-get-started/image028b.jpg)

Dopo aver installato PAS, nonché grafica SAP uno controllare tramite transazione "dbacockpit" che tutto sembra destra con l'installazione di HANA SAP.

 
![](./media/virtual-machines-linux-sap-hana-get-started/image038b.jpg)

Un ultimo passaggio uno potrebbe installazione SAP HANA Studio nel server di app macchine Virtuali di SAP e connettersi all'istanza HANA SAP in esecuzione nel server di database macchine Virtuali.




## <a name="manual-sap-hana-installation-via-hana-life-cycle-manager-tool-hdblcm"></a>Installazione manuale di SAP HANA tramite HANA ciclo di vita manager strumento hdblcm


Oltre l'installazione di SAP HANA come parte di un'installazione distribuita tramite SWPM è inoltre più realista innanzitutto installare autonoma HANA utilizzando hdblcm e quindi installare ad esempio SAP NetWeaver 7.5 in un secondo momento. L'elenco di screenshot seguente viene illustrato il funzionamento.

Ecco tre fonti di informazioni sullo strumento hdblcm HANA:

[Scelta di HDBLCM HANA SAP corretto per l'attività](https://help.sap.com/saphelp_hanaplatform/helpdata/en/68/5cff570bb745d48c0ab6d50123ca60/content.htm)

[Strumenti di gestione del ciclo di vita HANA SAP](http://saphanatutorial.com/sap-hana-lifecycle-management-tools/)

[SAP HANA Server Guida all'installazione e aggiornamento](http://help.sap.com/hana/SAP_HANA_Server_Installation_Guide_en.pdf)



![](./media/virtual-machines-linux-sap-hana-get-started/image030.jpg)

Per evitare l'esecuzione dei problemi in un secondo momento con un'impostazione di id di gruppo predefinito per il \<HANA SID\>utente adm, creata mediante lo strumento hdblcm, uno necessario definire un nuovo gruppo denominato "sapsys" con id gruppo 1001 prima di installare HANA SAP utilizzando hdblcm.




![](./media/virtual-machines-linux-sap-hana-get-started/image031.jpg)

Avvio hdblcm il primo si verificherà un menu di avvio semplice in cui è necessario selezionare elemento 1 "installare nuovo sistema"



![](./media/virtual-machines-linux-sap-hana-get-started/image032.jpg)

In questa schermata una possibile vedere tutte le opzioni chiave che sono state immesse prima. Importante - directory che sono stati denominati per i volumi log e dati HANA, nonché il percorso di installazione (hana/condiviso in questo esempio) e usr/sap non devono far parte del file System radice ma appartengono a dischi Azure dati che sono stati collegati a macchina virtuale come descritto nella sezione Configurazione macchine Virtuali di Azure. Evitare il rischio che il file System radice potrebbe esaurirsi spazio.
Uno anche possibile visualizzare che l'utente di amministratore HANA id utente 1005 sono previsti e fa parte del gruppo sapsys è stato definito prima dell'installazione (id 1001).



![](./media/virtual-machines-linux-sap-hana-get-started/image033.jpg)

Una possibile controllare la HANA \<HANA SID\>dettagli utente adm (azdadm in questo esempio) in/ecc/password



![](./media/virtual-machines-linux-sap-hana-get-started/image034.jpg)

Dopo l'installazione HANA SAP usando hdblcm può essere visualizzato in SAP HANA Studio. Lo schema SAPABAP1 che include ad esempio tutte le SAP NetWeaver tabelle non è ancora disponibile.



![](./media/virtual-machines-linux-sap-hana-get-started/image035b.jpg)

Dopo l'installazione di SAP HANA una possibile installare SAP NetWeaver su di essa. In questo esempio che è stata effettuata utilizzando "installazione distribuita" tramite SWPM come descritto nella sezione corrispondente precedente.
Durante l'installazione l'istanza del database tramite SWPM uno solo deve immettere gli stessi dati come prima con hdblcm (ad esempio nome host, HANA SID, numero di istanza). SWPM verranno quindi utilizzare l'installazione di HANA esistente e aggiungere altri schemi.



![](./media/virtual-machines-linux-sap-hana-get-started/image036b.jpg)

Questo è l'immagine della schermata di installazione SWPM nel punto in cui è necessario immettere i dati relativi lo schema DBACOCKPIT.


![](./media/virtual-machines-linux-sap-hana-get-started/image037b.jpg)

Quindi SWPM prevede di immettere dati informazioni sullo schema di SAPABAP1.


![](./media/virtual-machines-linux-sap-hana-get-started/image038b.jpg)

Al termine dell'installazione di istanza del database di SWPM uno possa vedere lo schema SAPABAP1 HANA Studio.



![](./media/virtual-machines-linux-sap-hana-get-started/image039b.jpg)

E infine dopo l'installazione del server di applicazioni SAP e grafica SAP uno dovrebbe essere possibile verificare l'istanza HANA DB con transazione "dbacockpit".




## <a name="general-information-related-to-sap-azure-certifications-running-sap-hana-on-azure-and-sap-software-download"></a>Informazioni generali relative alle certificazioni SAP Azure, esecuzione HANA SAP in scarica software Azure e SAP

* Utilizzare SAP Azure generali sull'esecuzione di SAP su Azure con sistema operativo Windows in modalità classica: [Utilizzando SAP in macchine virtuali di Windows Azure](virtual-machines-windows-classic-sap-get-started.md)

* informazioni sui modelli SAP esistente per l'uso dei clienti: [Modelli di Guida introduttiva di Azure per SAP](https://blogs.msdn.microsoft.com/saponsqlserver/2016/05/16/azure-quickstart-templates-for-sap/)

* Utilizzare SAP Azure generali sull'esecuzione SAP in Azure con sistema operativo Linux nel modello di gestione di risorse Azure: [Utilizzando SAP su Linux macchine ()](virtual-machines-linux-sap-get-started.md)

* directory di hardware SAP HANA certificata in cui sono elencati i tipi di macchine Virtuali di Azure supportati per produzione: [Certified HANA SAP® Hardware Directory](https://global.sap.com/community/ebook/2014-09-02-hana-hardware/enEN/iaas.html)

* informazioni sulle dimensioni macchina virtuale in particolare per carichi di lavoro Linux: [dimensioni per macchine virtuali di Azure](virtual-machines-linux-sizes.md)

* Nota SAP in cui sono elencati tutti SAP prodotti supportati in Azure e tipi di macchine Virtuali di Azure supportati per SAP: [1928533 nota SAP](https://launchpad.support.sap.com/#/notes/1928533/E)

* Nota SAP sul SAP "enhanced monitoraggio" con macchine virtuali Linux su Azure: [2191498 nota SAP](https://launchpad.support.sap.com/#/notes/2191498/E)

* SAP HANA offerta su Azure "Istanze di grandi dimensioni". È importante tenere presente che questa operazione sull'esecuzione HANA SAP in macchine virtuali di Azure ma non in una distribuzione ibrida ambiente in cui il server di app SAP eseguito in macchine virtuali di Azure ma HANA SAP viene eseguita nel server privo: [2316233 nota SAP](https://launchpad.support.sap.com/#/notes/2316233/E)

* SAP nota con informazioni SAPOSCOL su Linux: [1102124 nota SAP](https://launchpad.support.sap.com/#/notes/1102124/E)

* Tasto monitoraggio metriche per SAP in Microsoft Azure: [SAP nota 2178632](https://launchpad.support.sap.com/#/notes/2178632/E)

* Informazioni su Gestione risorse di Azure: [Panoramica di gestione risorse di Azure](../azure-resource-manager/resource-group-overview.md)

* Informazioni sulla distribuzione macchine virtuali Linux tramite modelli: [Distribuisci e gestire macchine virtuali utilizzando i modelli di gestione di risorse Azure e CLI Azure](virtual-machines-linux-cli-deploy-templates.md)

* Confronto di distribuzione di modelli tra Gestione risorse di Azure e classica: [Manager delle risorse di Azure e distribuzione classica: informazioni su modelli di distribuzione e lo stato delle risorse](../resource-manager-deployment-model.md)

* Scaricare NetWeaver 7.5 per Linux/HANA di Marketplace di servizio SAP:![](./media/virtual-machines-linux-sap-hana-get-started/image001.jpg)

* Scaricare HANA SP12 piattaforma Edition dal Marketplace servizio SAP:![](./media/virtual-machines-linux-sap-hana-get-started/image002.jpg)

