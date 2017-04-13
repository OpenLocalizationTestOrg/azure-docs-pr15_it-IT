<properties
    pageTitle="Replica locale macchine virtuali VMware o server fisici per un sito secondario | Microsoft Azure"
    description="Usare questo articolo replicare macchine virtuali VMware o Windows/Linux server fisici in un sito secondario con il ripristino del sito di Azure."
    services="site-recovery"
    documentationCenter=""
    authors="nsoneji"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.workload="backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/20/2016"
    ms.author="nisoneji"/>


# <a name="replicate-on-premises-vmware-virtual-machines-or-physical-servers-to-a-secondary-site"></a>Replicare macchine virtuali VMware locale o server fisici in un sito secondario


## <a name="overview"></a>Panoramica

Esploratore InMage nel ripristino del sito di Azure fornisce la replica in tempo reale tra siti VMware locale. InMage esploratore è incluso nelle sottoscrizioni di assistenza il ripristino del sito di Azure.


## <a name="prerequisites"></a>Prerequisiti

**Account Azure**: È necessario un account di [Microsoft Azure](https://azure.microsoft.com/) . È possibile iniziare con una [versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/). [Altre informazioni](https://azure.microsoft.com/pricing/details/site-recovery/) sui prezzi il ripristino del sito.


## <a name="step-1-create-a-vault"></a>Passaggio 1: Creare un archivio
1. Accedere al [portale di Azure](https://portal.azure.com).
2. Fare clic su Nuovo > Gestione > eseguire il Backup e il ripristino del sito (OMS). In alternativa, è possibile fare clic su Sfoglia > archivio di servizi di recupero > Aggiungi.
3. Nella **casella Nome** specificare un nome descrittivo per identificare l'archivio. Se si dispone di più di una sottoscrizione, selezionare una di esse.
4. Nel **gruppo risorse** creare un nuovo gruppo di risorse o selezionarne uno esistente. Specificare un'area Azure per completare i campi obbligatori. 
5.  In **posizione**, selezionare la località geografica per l'archivio. Per verificare se le aree supportate, vedere [Azure sito ripristino prezzi](https://azure.microsoft.com/pricing/details/site-recovery/).
5. Se si desidera accedere rapidamente l'archivio nel dashboard fare clic su Aggiungi al dashboard e quindi fare clic su Crea.
6. Nuovo archivio verrà visualizzato nel Dashboard > tutte le risorse e sui servizi di recupero principale archivi blade.

## <a name="step-2-configure-the-vault-and-download-inmage-scout-components"></a>Passaggio 2: Configurare l'archivio e scaricare componenti esploratore InMage
7. In e archivi il ripristino servizi selezionare l'insieme di credenziali e fare clic su impostazioni.
8. In **Impostazioni** > **Introduzione** fare clic sul **Ripristino del sito** > passaggio 1: **Preparare infrastruttura** > **obiettivo di protezione**.
9. In **obiettivo di protezione** selezionare al sito di ripristino e selezionare Sì, con VMware vSphere Hypervisor. Fare clic su OK.
10. In **Imposta Scout**, fare clic su download per scaricare GA InMage esploratore 8.0.1 software e registrazione la chiave. File di installazione di tutti i componenti necessari siano presenti nel file ZIP scaricati.


## <a name="step-3-install-component-updates"></a>Passaggio 3: Installare gli aggiornamenti dei componenti

Informazioni sugli [aggiornamenti](#updates)più recenti. Installare i file di aggiornamento sul server nell'ordine seguente:

1. Server di ricezione se presente
2. Server di configurazione
3. Server di processo
3. Server di destinazione principale
4. vContinuum server
5. Server di origine (Windows e Linux Server)

Installare gli aggiornamenti come indicato di seguito:

1. Scaricare il file con estensione zip [aggiornare](https://aka.ms/asr-scout-update4) . In questo file con estensione zip contiene i file seguenti:

    - RX_8.0.4.0_GA_Update_4_8725872_16Sep16.tar.gz
    - CX_Windows_8.0.4.0_GA_Update_4_8725865_14Sep16.exe
    - UA_Windows_8.0.4.0_GA_Update_4_9035261_27Sep16.exe
    - UA_RHEL6 64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz
    - vCon_Windows_8.0.4.0_GA_Update_4_8921562_16Sep16.exe
    - Bit update4 UA per RHEL5, OL5, OL6, SUSE 10, 11 SUSE: UA_<Linux OS>_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz 
    
2. Estrarre i file con estensione zip.<br>
3. **Server di ricezione**: copia **RX_8.0.4.0_GA_Update_4_8725872_16Sep16.tar.gz** al server di ricezione ed estrarre i file. Nella cartella estratta eseguire **Installa**.<br>
4. **Per il server di server/processo di configurazione**: copia **CX_Windows_8.0.4.0_GA_Update_4_8725865_14Sep16.exe** per il server di configurazione processo. Fare doppio clic per eseguirlo.<br>
5. **Per Windows server di destinazione master**: per aggiornare l'agente unificato, copiare **UA_Windows_8.0.4.0_GA_Update_4_9035261_27Sep16.exe** al server di destinazione master. Fare doppio clic per eseguirlo. Si noti che l'agente unificato è applicabile al server di origine. È necessario installare Office nel server di origine nonché, come descritto più avanti in questo elenco.<br>
7. **Per il server vContinuum**: copiare **vCon_Windows_8.0.4.0_GA_Update_4_8921562_16Sep16.exe** al server vContinuum.  Assicurarsi che è stato chiuso la procedura guidata vContinuum. Fare doppio clic sul file per eseguirlo.<br>
8. **Server di destinazione master per Linux**: per aggiornare l'agente unificato, copiare **UA_RHEL6 64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz** al server di destinazione master ed estrarre i file. Nella cartella estratta eseguire **Installa**.<br>
9. **Server di origine per Windows**: per aggiornare l'agente unificato, copiare **UA_Windows_8.0.4.0_GA_Update_4_9035261_27Sep16.exe** al server di origine. Fare doppio clic per eseguirlo.<br>
10. **Server di origine per Linux**: per aggiornare l'agente unificato, copiare versione corrispondente del file UA al server Linux ed estrarre i file. Nella cartella estratta eseguire **Installa**.  Esempio: Per server a 6,7 64 RHEL, copiare **UA_RHEL6 64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz** sul server ed estrarlo. Nella cartella estratta eseguire **Installa**.

## <a name="step-4-set-up-replication"></a>Passaggio 4: Impostare la replica
1. Impostare la replica tra l'origine e destinazione VMware siti.
2. Per ulteriori informazioni, utilizzare la documentazione InMage esploratore scaricati con il prodotto. In alternativa, è possibile accedere alla documentazione come indicato di seguito:

    - [Note sulla versione](https://aka.ms/asr-scout-release-notes)
    - [Matrice di compatibilità](https://aka.ms/asr-scout-cm)
    - [Guida dell'utente](https://aka.ms/asr-scout-user-guide)
    - [Guida di ricezione degli utenti](https://aka.ms/asr-scout-rx-user-guide)
    - [Guida all'installazione rapida](https://aka.ms/asr-scout-quick-install-guide)


## <a name="updates"></a>Aggiornamenti

### <a name="azure-site-recovery-scout-801-update-4"></a>Sito Azure ripristino esploratore 8.0.1 aggiornamento 4
Aggiornamento esploratore 4 è un aggiornamento cumulativo. Include tutte le correzioni di update1 fino a update3 e miglioramenti e correzioni di bug nuove seguenti.

**Nuovo supporto piattaforma** 

- È stato aggiunto il supporto per vCenter/vSphere 6.0, 6.1 e 6.2
- Supporto è stato aggiunto per sistemi operativi Linux seguenti
    - Red Hat Enterprise Linux (RHEL) 7.0, 7.1 e 7.2 
    - CentOS 7.0, 7.1 e 7.2
    - Red Hat Enterprise Linux (RHEL) 6,8
    - CentOS 6,8

>[AZURE.NOTE]
>
> RHEL/CentOS 7 a 64 bit **InMage_UA_8.0.1.0_RHEL7-64_GA_06Oct2016_release.tar.gz** fornito con il pacchetto esploratore GA base **InMage_Scout_Standard_8.0.1 GA.zip**. Scaricare il pacchetto di Scout GA dal portale come descritto nel [passaggio 1](site-recovery-vmware-to-vmware.md#Step 1: Create a vault).

**Miglioramenti e correzioni di bug** 

- Arresto migliorato gestione per seguenti Linux OSes e duplicati evitare problemi indesiderati ripetere la sincronizzazione.
    - Red Hat Enterprise Linux (RHEL) 6. x
    - Linux Oracle (OL) 6. x
- Per Linux, eseguire l'accesso alle cartelle autorizzazioni nella directory di installazione agente unificato ora sono limitate solo per l'utente locale.
- In Windows timeout problema durante l'invio comuni segnalibro coerenza distribuito in modo intensivo caricato distribuito applicazioni, ad esempio cluster SQL e punto di condivisione.
- Registro aggiunte correlati lo strumento fix nel programma di installazione di base CX.
- VMware vCLI 6.0 download collegamento viene aggiunta al programma di installazione di base destinazione schema di Windows.
- Aggiungere altri controlli e registri delle modifiche configurazioni di rete durante failover ed esercitazioni DR.
- Informazioni di conservazione talvolta non viene segnalate per il CX.  
- Per cluster fisici, volume ridimensionare operazione vContinuum guidata non riesce quando relative Riduci volume di origine.
- Protezione cluster non riuscita con errore "Impossibile trovare la firma del disco" quando cluster disco è PRDM.
- cxps trasporto arresto anomalo di server a causa di un'eccezione all'intervallo. 
- Nome del server e le colonne di indirizzi IP sono ora ridimensionabile nella pagina di installazione push della procedura guidata vContinuum.
- Miglioramenti dell'API di ricezione
    - Fornisce cinque ultima versione disponibile comuni coerenza punti (garantisce solo tag).
    - Fornisce capacità e i dettagli di spazio disponibile per tutti i dispositivi protetti.
    - Fornisce esploratore driver stato nel server di origine. 
    
>[AZURE.NOTE] 
>
>- Pacchetto di base **InMage_Scout_Standard_8.0.1_GA.zip** è aggiornati CX installer base **InMage_CX_8.0.1.0_Windows_GA_26Feb2015_release.exe** e destinazione Windows schema base installer **InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_26Feb2015_release.exe**. Per l'installazione di nuovo tutti utilizzare nuovi bit GA CX e destinazione schema di Windows.
>- Aggiornamento 4 può essere applicato direttamente su 8.0.1 GA.
>- Non è possibile annullare gli aggiornamenti di ricezione e il server di configurazione dopo vengono applicati nel sistema.

### <a name="azure-site-recovery-scout-801-update-3"></a>Aggiornamento di Scout 8.0.1 ripristino di Azure del sito 3
Aggiornamento 3 include le correzioni di bug e i miglioramenti seguenti:

- Il server di configurazione e la ricezione esito negativo registrare all'archivio il ripristino del sito quando sono dietro il proxy.
- Il numero di ore che non è stato raggiunto l'obiettivo di punto di ripristino (rilasciato) non viene visualizzato aggiornato nel rapporto di stato.
- Il server di configurazione non è sincronizzato con ricezione quando i dettagli hardware ESX o i dettagli di rete contienecontengono caratteri UTF-8.
- Controller di dominio Windows Server 2008 R2 esito negativo per l'avvio dopo il ripristino.
- Sincronizzazione offline non funziona come previsto.
- Dopo aver failover macchine (), l'eliminazione di replica coppia rimane bloccato nella UI CX una lunga esperienza, e non è possibile completare il failback o riprendere il funzionamento.
- In generale snapshot operazioni vengono eseguite dal processo coerenza sono state ottimizzate per ridurre l'applicazione disconnette come SQL client.
- Le prestazioni dello strumento coerenza (VACP.exe) sono stata migliorata riducendo l'utilizzo della memoria che è necessario per la creazione di istantanee in Windows.
- Il comando installare arresto anomalo del servizio quando la password è maggiore di 16 caratteri.
- vContinuum non controllo ed è richiesta di conferma per le nuove credenziali vCenter quando vengono modificate le credenziali.
- Su Linux, il responsabile della cache di destinazione principale (cachemgr) non scarica file dal server di processo, creando così limitazione coppia di replica.
- Quando l'ordine di disco fisico failover cluster (MSCS) non è la stessa per tutti i nodi, replica non è impostata per alcuni dei volumi cluster.
<br/>Si noti che il cluster deve essere reprotected per sfruttare questa correzione.  
- La funzionalità SMTP non funziona come previsto dopo ricezione viene aggiornata esploratore 7.1 a esploratore 8.0.1.
- Altre statistiche sono state aggiunte nel log per l'operazione di ripristino tenere traccia del tempo che ha preso per completare l'operazione.
- Supporto è stato aggiunto per sistemi operativi Linux nel server di origine:
    - Aggiornamento di Red Hat Enterprise Linux (RHEL) 6 7
    - CentOS 6 aggiornare 7
- CX interfaccia di e ricezione ora possibile visualizzare la notifica per la coppia, entra in modalità di bitmap.
- In ricezione sono state aggiunte le correzioni di protezione seguenti:

**Descrizione del problema**|**Procedure di implementazione**
---|---
Ignorare le autorizzazioni tramite l'alterazione dei parametri|Accesso limitato a utenti non è applicabile.
FALSO richiesta intersito|Implementata il concetto di token di pagina, viene generato in modo casuale per ogni pagina. <br/>Con questo valore, verrà visualizzato: <li> Esiste una sola accesso istanza per l'utente stesso.</li><li>Aggiornare la pagina non funziona, si viene reindirizzati a dashboard.</li>
Caricamento di file dannoso|File con restrizioni per determinate estensioni. Estensioni consentiti: 7z, aiff, asf, avi, bmp, csv, doc, docx, fla, flv, gif, gz, gzip, jpeg, jpg, log, mid, mov, mp3, mp4, mpc, mpeg, mpg, ods, odt, pdf, png, ppt, pptx, pxd, qt, ram, rar, rm, rmi, rmvb, rtf, sdc, sitd, swf, sxc, sxw, tar, tgz, tif, tiff, txt, vsd, wav, wma, wmv, xls, xlsx, xml e CAP.
Persistent CSS | Aggiungere la convalida di input.


>[AZURE.NOTE]
>
>-  Tutti gli aggiornamenti il ripristino del sito sono cumulativi. Aggiornamento 3 include tutte le correzioni di aggiornamento 1 e 2 di aggiornamento. Aggiornamento 3 può essere applicato direttamente su 8.0.1 GA.
>-  Non è possibile annullare gli aggiornamenti di ricezione e il server di configurazione dopo vengono applicati nel sistema.

### <a name="azure-site-recovery-scout-801-update-2-update-03dec15"></a>Sito Azure ripristino esploratore 8.0.1 aggiornamento 2 (aggiornamento 03 15 dic)

Risoluzioni nell'aggiornamento 2 includono:

- **Server di configurazione**: risoluzione per un problema che impedisce la caratteristica di misurazione gratuita di 31 giorni lavorativi come previsto quando il server di configurazione è stato registrato il ripristino del sito.
- **Agente unificato**: risoluzione per un problema di aggiornamento 1 che ha causato l'aggiornamento non viene installato sul server di destinazione master quando è stato aggiornato dalla versione 8.0 a 8.0.1.


### <a name="azure-site-recovery-scout-801-update-1"></a>Sito Azure ripristino esploratore 8.0.1 aggiornamento 1

Aggiornamento 1 include le seguenti correzioni di bug e nuove caratteristiche:

- 31 giorni di protezione gratuita per ogni istanza server. In questo modo è possibile verificare la funzionalità o configurare la verifica dei concetti.
    - Tutte le operazioni sul server, inclusi failover e opzione, sono gratuite per 31 giorni prima, partire dal momento in cui un server prima di tutto è protetto con esploratore ripristino del sito.
    - In poi del giorno 32nd, ogni server protetto verrà addebitato alla tariffa standard istanza per la protezione il ripristino del sito di Azure a un sito il cliente è proprietario.
    - In qualsiasi momento, il numero di server protetti che attualmente addebitate è disponibile nella pagina Dashboard dell'archivio il ripristino del sito di Azure.
- Aggiunto il supporto per vSphere interfaccia riga di comando (vCLI) di aggiornamento 5,5 2.
- Supporto aggiunto per sistemi operativi Linux nel server di origine:
    - RHEL 6 aggiornare 6
    - RHEL 5 aggiornare 11
    - Aggiornamento di centOS 6 6
    - Aggiornamento centOS 5 11
- Correzioni degli errori risolvere i problemi seguenti:
    - Archivio registrazione avrà esito negativo per il server di configurazione o ricezione.
    - Volumi cluster non vengono visualizzati come previsto quando macchine sono reprotected quando si riattiva.
    - Failback non riuscirà se il server di destinazione principale si trova in un server ESXi diverso da macchine virtuali di produzione locale.
    - Autorizzazioni del file di configurazione vengono modificate quando si esegue l'aggiornamento a 8.0.1, che ha effetto sulla protezione e le operazioni.
    - Soglia risincronizzazione non viene applicata come previsto, che genera il comportamento di replica non coerente.
    - Le impostazioni di rilasciato non vengono visualizzati correttamente nell'interfaccia di configurazione server. Il valore di dati non compressi in modo non corretto Mostra il valore compresso.
    -  Non elimina l'operazione di rimozione come previsto nella creazione guidata vContinuum e replica non vengono eliminata dall'interfaccia configurazione server.
    -  Nella procedura guidata vContinuum il disco è deselezionato automaticamente quando si fa clic su **Dettagli** nella visualizzazione disco durante la protezione delle macchine virtuali MSCS.
    - Durante lo scenario fisica a virtuale (P2V), Servizi HP necessari, ad esempio CIMnotify e CqMgHost, non vengono spostati manuale di ripristino macchina virtuale. Il risultato avvio aggiuntive.
    - Protezione macchina virtuale Linux non riuscirà se sono presenti più di 26 dischi nel server di destinazione master.

## <a name="next-steps"></a>Passaggi successivi

Inviare eventuali domande installata nel [forum di servizi di ripristino di Windows Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).
