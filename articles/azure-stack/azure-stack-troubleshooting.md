<properties
    pageTitle="Risoluzione dei problemi di Microsoft Azure Stack | Microsoft Azure"
    description="Azure Stack risoluzione dei problemi."
    services="azure-stack"
    documentationCenter=""
    authors="heathl17"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/13/2016"
    ms.author="helaw"/>

# <a name="microsoft-azure-stack-troubleshooting"></a>Risoluzione dei problemi di Microsoft Azure Stack

Questo documento offre informazioni sulla risoluzione dei problemi comuni per Stack di Azure.

Per risultati ottimali, assicurarsi che l'ambiente di distribuzione soddisfi tutti i [requisiti](azure-stack-deploy.md) e [preparati](azure-stack-run-powershell-script.md) prima di installare. 

Suggerimenti per la risoluzione dei problemi descritte in questa sezione provengono da varie origini e potrebbero essere o potrebbero non consente di risolvere il problema specifico. Se si verifica un problema non descritte, assicurarsi di verificare il [Forum MSDN di Azure Stack](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack) per ulteriore assistenza e ulteriori informazioni.

Esempi di codice vengono forniti come tali e non è possibile garantire i risultati previsti. In questa sezione è soggetto a modifiche frequenti e aggiornamenti implementato di miglioramenti al prodotto.

  

## <a name="known-issues"></a>Problemi noti

 - È possibile riscontrare i seguenti non definitive errori durante la distribuzione, che incidono sull'esito di distribuzione:
     - "Il termine 'C:\WinRM\Start-Logging.ps1' non è riconosciuto"
     - "Richiamare EceAction: non è possibile indicizzare una matrice null" 
     - "InvokeEceAction: non è possibile associare il argomento al parametro 'Messaggio' perché è una stringa vuota."
 - Si può vedere distribuzione esito negativo al passaggio 60.61.93 con un messaggio di errore "Applicazione con identificatore"URI"non trovato". Questo comportamento risulta perché le applicazioni vengono registrate in Azure Active Directory.  Se si riceve questo errore, continuare a [eseguire di nuovo lo script di installazione](azure-stack-rerun-deploy.md) dal passaggio 60.61.93 fino al completamento di distribuzione.
 - Si noterà che la **Disponibilità** risorsa sul mercato viene visualizzata nella categoria **Macchinavirtuale ARM** -questo aspetto è un problema di cosmetico.
 - Quando si crea una nuova macchina virtuale nel portale di nel passaggio **Nozioni di base** di SSD impostazione predefinita l'opzione di spazio di archiviazione.  È necessario modificare questa impostazione per disco rigido o al passaggio **dimensioni** della distribuzione di macchine Virtuali, non verranno visualizzate le dimensioni in macchine Virtuali disponibili per selezionare e continuare la distribuzione. 
 - Verrà visualizzato AzureRM PowerShell moduli non vengono più installati per impostazione predefinita nella macchina virtuale MAS CON01 (in TP1 questo stato denominato ClientVM). Questo comportamento risulta per impostazione predefinita, poiché non esiste un metodo alternativo per [installare questi moduli e la connessione](azure-stack-connect-powershell.md).  
 - Si noterà che il provider di risorse **Microsoft.Insights** non viene registrato automaticamente per gli abbonamenti tenant. Se si desidera visualizzare il monitoraggio dei dati per una macchina virtuale distribuita come un tenant, eseguire il comando seguente da PowerShell (dopo aver si [installazione e la connessione](azure-stack-connect-powershell.md) come un tenant): 
       
        Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Insights 

 - Verrà visualizzata la funzionalità nel portale di esportazione per i gruppi di risorse, ma il testo non è visualizzata e disponibile per l'esportazione.      
 - È possibile iniziare una distribuzione delle risorse di archiviazione maggiore di quota disponibile.  Si verifica un errore di distribuzione e le risorse account verranno sospeso.  Sono disponibili due opzioni di monitoraggio e aggiornamento:
     - Amministratore del servizio è possibile aumentare la quota, anche se le modifiche non vengono applicate immediatamente e simboli richiedere fino a un'ora propagazione delle modifiche apportate.
     - Amministratore del servizio è possibile creare un piano di componente aggiuntivo con ulteriore quota che tenant quindi possibile aggiungere all'abbonamento.
 - Quando si usa il portale di creare macchine virtuali in ambienti dello Stack di Azure con identità in "Azure - Cina", non vedrà le dimensioni in macchine Virtuali disponibili selezionare il passaggio di **dimensioni** della distribuzione e non saranno in grado di continuare la distribuzione.
 - Potrebbe essere visualizzato un errore di distribuzione nel portale, quando la macchina virtuale è effettivamente distribuite correttamente.
 - Quando si elimina un piano, offerta o l'abbonamento, macchine virtuali non possono essere eliminate.
 - Si vedranno le estensioni macchine Virtuali di marketplace.
 - Non è possibile distribuire una macchina virtuale da un'immagine salvata macchine Virtuali.
 - Tenant che venga visualizzata servizi che non sono inclusi nell'abbonamento.  Quando i tenant tentano di distribuire queste risorse, ricevono un messaggio di errore.  Esempio: Abbonamento Tenant include solo le risorse di archiviazione.  Tenant verrà visualizzata l'opzione per creare altre risorse come macchine virtuali.  In questo scenario, quando un tenant tenta di distribuire una macchina virtuale, ricevono un messaggio che indica che non è possibile creare la macchina virtuale. 
 - Quando si installa TP2, è necessario attivare non l'host del sistema operativo del disco rigido virtuale specificato nel punto in cui si esegue lo script di installazione dello Stack di Azure o venga visualizzato un messaggio di errore indicante Windows di messaggistica sta per scadere.


## <a name="deployment"></a>Distribuzione

### <a name="deployment-failure"></a>Errore di distribuzione
Se si verifica un errore durante l'installazione, il programma di installazione dello Stack di Azure consente di continuare a un'installazione non riuscita seguendo [Ripetere i passaggi di distribuzione](azure-stack-rerun-deploy.md).

### <a name="at-the-end-of-the-deployment-the-powershell-session-is-still-open-and-doesnt-show-any-output"></a>Al termine della distribuzione, la sessione di PowerShell è ancora aperta e non viene visualizzato alcun output

Questo comportamento risulta probabilmente solo il risultato del comportamento predefinito di una finestra di comando di PowerShell, quando è stato selezionato. La distribuzione di prova effettivamente ha avuto esito positivo, ma lo script interrotta quando si seleziona la finestra. È possibile verificare che questo è il caso, osservare la parola "select" sulla barra del titolo della finestra di comando.  Premere ESC per deselezionarlo e il messaggio di completamento deve essere visualizzato dopo tale voce.

## <a name="templates"></a>Modelli

### <a name="azure-template-wont-deploy-to-azure-stack"></a>Modelli di Azure non è possibile distribuire in pila di Azure

Verificare quanto segue:

- Il modello è necessario utilizzare un servizio di Microsoft Azure che è già disponibile o in anteprima in pila Azure.
- API utilizzate per una risorsa specifica sono supportate dall'istanza di Azure Stack locale e di destinazione di un percorso valido ("locale" in Azure Stack Technical Preview (RS) 2, vs "Stati Uniti orientali" o "Sud India" in Azure).
- Leggere [questo articolo](https://github.com/Azure/AzureStack-QuickStart-Templates/blob/master/README.md) sui cmdlet di Test AzureRmResourceGroupDeployment, che rilevare piccole differenze di gestione di risorse Azure sintassi.

Utilizzare i modelli di Azure Stack già specificati nel [repository GitHub](http://aka.ms/AzureStackGitHub/) consentono di iniziare.

## <a name="virtual-machines"></a>Macchine virtuali

### <a name="after-starting-my-microsoft-azure-stack-poc-host-all-my-tenants-vms-are-gone-from-hyper-v-manager-and-come-back-automatically-after-waiting-a-bit"></a>Dopo l'avvio dell'host di Microsoft Azure Stack prova, tutti i tenant macchine virtuali eliminato dal Manager Hyper-V e torni automaticamente dopo un po'?

Mentre il sistema viene restituita la il sottosistema di archiviazione e l'esigenza di RPs per determinare la coerenza. Il tempo necessario dipende dall'hardware e specifiche in uso, ma può essere po' di tempo dopo il riavvio dell'host per tenant macchine virtuali torni e sarà riconosciuto.

### <a name="i-have-deleted-some-virtual-machines-but-still-see-the-vhd-files-on-disk-is-this-behavior-expected"></a>Eliminati alcune macchine virtuali, ma continuare comunque a vedere i file disco rigido virtuale sul disco rigido. Questo comportamento è previsto?

Sì, questo comportamento è previsto. È stata progettata in questo modo perché:

- Quando si elimina una macchina virtuale, dischi rigidi virtuali non vengono eliminati. Dischi sono risorse distinte nel gruppo di risorse.
- Quando viene eliminato un account di archiviazione, l'eliminazione è visibile immediatamente tramite Azure Manager delle risorse (portal, PowerShell) ma dischi che potrebbe contenere ancora archiviati in archiviazione solo durante l'esecuzione simultanea.

Se viene visualizzato "orfano" dischi rigidi virtuali, è importante sapere se fanno parte della cartella per un account di archiviazione che è stato eliminato. Se l'account di archiviazione non è stato eliminato, è normale che sono ancora disponibili.

È possibile leggere altre informazioni sulla configurazione la soglia di conservazione in [gestire gli account di archiviazione](azure-stack-manage-storage-accounts.md).

## <a name="installation-steps"></a>Procedura di installazione
Le seguenti informazioni sui passaggi di installazione dello Stack di Azure possono risultare utili per la risoluzione dei problemi:  

| Indice | Nome | Descrizione|
| ----- | ----- | -----|
|0.11 | (PROTEZIONE ESECUZIONE PROGRAMMI) Convalidare macchine fisiche | Convalida della configurazione hardware e del sistema operativo in nodi fisici. |
| 0,12 | (PROTEZIONE ESECUZIONE PROGRAMMI) Configurare la rete di computer di prova | Configurazione di rete virtuale parametri e interfacce. |
| 0,14 | (PROTEZIONE ESECUZIONE PROGRAMMI) Distribuire dominio | Distribuire Active Directory sul computer virtuale. |
| 0,15 | (PROTEZIONE ESECUZIONE PROGRAMMI) Configurare il server di dominio | Configurare il server di dominio con i gruppi di sicurezza e così via. |
| 0,16 | (PROTEZIONE ESECUZIONE PROGRAMMI) Configurare i computer fisico | Configurare la rete, aggiungersi a un dominio e il programma di installazione locale amministratori. |
| 0,18 | (SERIE) Configurare lo spazio di archiviazione Cluster | Creare lo spazio di archiviazione cluster, creare un server di archiviazione pool e file. |
| 0.19 | CPI) Infrastruttura di tessuti di installazione | Configurare i prerequisiti per la distribuzione di tessuti. |
| 0,21 | (NETTO) Configurazione BGP e NAT | Installa BGP e NAT - necessario solo per un nodo. |
| 0,22 | (NETTO) Configurare NAT e ora Server | Sincronizza il server di tempo e configura le voci NAT. |
| 40.41 | CPI) Creare macchine virtuali guest | Creare macchine virtuali di gestione. |
| 40.42 | (FBI) Configurare PowerShell JEA | Configurare PowerShell JEA per tutti i ruoli. |
| 40.43 | (FBI) Configurare un'autorità di certificazione Stack Azure | Installa un'autorità di certificazione Stack Azure. |
| 40.44 | (FBI) Configurare l'autorità di certificazione Stack Azure | Consente di configurare autorità di certificazione Stack Azure. |
| 40.45 | (NETTO) Configurare la NC in macchine virtuali | Installa NC in macchine virtuali guest |
| 40.46 | (NETTO) Configurare NC in macchine virtuali | Configurare NC in macchine virtuali guest |
| 40.47 | (NETTO) Configurare le macchine virtuali guest | Configurare la gestione macchine virtuali con ACL NC. |
| 60.61.81 | (FBI) Distribuire i servizi di squillo tessuti Stack Azure - prerequisito FabricRing | Crea VIP per FabricRing |
| 60.61.82 | (FBI) Distribuire i servizi di Azure Stack tessuti squillo - distribuzione tessuti squillo Cluster | Installare e configurare Azure Stack tessuti squillo Cluster. |
| 60.61.83 | (FBI) Distribuire le estensioni di amministrazione per i provider di risorse | Installazione delle estensioni di amministrazione per i provider di risorse |
| 60.61.84 | (ACS) Impostare lo spazio di archiviazione Azure coerenti in livello di nodo. | Installare e configurare lo spazio di archiviazione Azure coerenti in livello di nodo. |
| 60.61.85 | (ACS) Impostare lo spazio di archiviazione Azure coerente nel livello cluster. | Installare e configurare lo spazio di archiviazione Azure coerenti in livello cluster. |
| 60.61.86 | (FBI) Distribuire Stack Azure tessuti squillo Controller servizi - prerequisito | Prerequisiti per InfraServiceController |
| 60.61.87 | (FBI) Distribuire Stack Azure tessuti squillo Controller servizi - prerequisito | Prerequisiti per CPI |
| 60.61.88 | (FBI) Distribuire Stack Azure tessuti squillo Controller servizi - prerequisito | Prerequisiti per ASAppGateway |
| 60.61.89 | (FBI) Distribuire Stack Azure tessuti squillo Controller servizi - prerequisito | Prerequisiti per Controller di archiviazione |
| 60.61.90 | (FBI) Distribuire Stack Azure tessuti squillo Controller servizi - prerequisito | Prerequisiti per HealthMonitoring |
| 60.61.91 | (FBI) Distribuire Stack Azure tessuti squillo Controller servizi - prerequisito | Prerequisiti per ECE |
| 60.61.92 | (FBI) Distribuire Stack Azure tessuti squillo Controller servizi - prerequisito | Prerequisiti per PMM |
| 60.61.93 | (Katal) Creazione di identità di servizio AzureStack | Creare applicazioni grafico Azure e identità di servizio in AAD. |
| 60.61.94 | (NETTO) Configurazione GW macchine virtuali | Installa GW in macchine virtuali guest. |
| 60.61.95 | (NETTO) Configurare macchine virtuali GW | Configura GW macchine virtuali guest. |
| 60.61.96 | (NETTO) Distribuire IDN su host | Distribuire IDN negli host dell'infrastruttura |
| 60.61.97 | (NETTO) Configurare IDN | Configurare IDN ruolo |
| 60.61.98 | (FBI) Configurazione macchine virtuali di Windows Server Update Services | Installa Windows Server Update Services server in macchine virtuali guest. |
| 60.61.99 | (FBI) Configurare macchine virtuali di Windows Server Update Services | Configura Windows Server Update Services server in macchine virtuali guest. |
| 60.61.100 | (FBI) Configurazione macchine virtuali SQL Azure | Installazioni Azure SQL server in macchine virtuali guest |
| 60.61.101 | (Katal) Impostare i prerequisiti per macchine virtuali di stato. | Configura automaticamente i prerequisiti per Microsoft Azure Stack in macchine virtuali guest. |
| 60.61.102 | (Katal) Il programma di installazione è stata macchine virtuali | Installa Microsoft Azure Stack in macchine virtuali guest. |
| 60.120.121 | (FBI) Distribuire provider di risorse e controller | Installa i provider di risorse e controller |
| 60.120.121 | (FBI) Distribuire provider di risorse e controller | Installa i provider di risorse e controller |
| 60.120.121 | (FBI) Distribuire provider di risorse e controller | Installa i provider di risorse e controller |
| 60.120.121 | (FBI) Distribuire provider di risorse e controller | Installa i provider di risorse e controller |
| 60.120.121 | (FBI) Distribuire provider di risorse e controller | Installa i provider di risorse e controller |
| 60.120.121 | (FBI) Distribuire provider di risorse e controller | Installa i provider di risorse e controller |
| 60.120.121 | (FBI) Distribuire provider di risorse e controller | Installa i provider di risorse e controller |
| 60.120.121 | (FBI) Distribuire provider di risorse e controller | Installa i provider di risorse e controller |
| 60.120.122 | (FBI) Configurazione del controller | Configura controller |
| 60.120.123 | (Katal) Configurare è stato macchine virtuali | Configura Microsoft Azure Stack macchine virtuali guest. |
| 60.120.124 | (Katal) Configurazione di AAD dello Stack Azure. | Configura Stack Azure con Azure Active Directory. |
| 60.120.125 | (Katal) Installare ADFS | Consente di installare Active Directory Federation Services (ADFS) |
| 60.120.126 | (Katal) Installare ADFS/grafico | Installa grafico Stack Azure |
| 60.120.127 | (Katal) Configurare ADFS | Configura Active Directory Federation Services (ADFS) |
| 60.140.141 | (FBI) Configurare pacchetto cumulativo di protezione | Configura il Provider di risorse dello spazio di archiviazione |
| 60.140.142 | (ACS) Configurare lo spazio di archiviazione Azure coerenti. | Consente di configurare lo spazio di archiviazione Azure coerenti. |
| 60.140.143 | (FBI) Creare gli account di archiviazione | Creare tutti gli account di archiviazione che verrà utilizzato da diversi provider. |
| 60.140.144 | (FBI) Utilizzo dei registri per pacchetto cumulativo di protezione | Registrare l'utilizzo del provider di archiviazione. |
| 60.140.145 | CPI) Eseguire la migrazione create macchine virtuali, host e Cluster a CPI | Eseguire la migrazione di oggetti delle macchine virtuali, host e Cluster creato in CPI |
| 60.140.146 | (FBI) Configurare Windows Defender | Configurare Windows Defender |
| 60.160.161 | (LUN) Configurare il monitoraggio dell'agente | Configura agente di monitoraggio |
| 60.160.162 | (FBI) Prerequisito NRP | Prerequisiti NRP installazioni |
| 60.160.163 | (FBI) Distribuzione NRP | Installazioni NRP  |
| 60.160.164 | (FBI) Configurazione di NRP | Configura NRP |
| 60.160.165 | (FBI) Prerequisito CRP | Installa i prerequisiti CRP |
| 60.160.166 | (FBI) Distribuzione CRP | Installa CRP |
| 60.160.167 | (FBI) Configurazione di CRP | Configura CRP |
| 60.160.168 | (FBI) In materia plastica rinforzata prerequisito | Prerequisiti in materia plastica rinforzata installazioni |
| 60.160.169 | (FBI) In materia plastica rinforzata distribuzione | Installazioni in materia plastica rinforzata  |
| 60.160.170 | (FBI) Configurazione in materia plastica rinforzata | Configura in materia plastica rinforzata |
| 60.160.174 | (FBI) URP prerequisiti | Prerequisiti di installazione URP |
| 60.160.175 | (FBI) Distribuzione URP | Installa URP  |
| 60.160.176 | (FBI) Configurazione di URP | Configura URP |
| 60.160.171 | (FBI) Prerequisito effettuato | Installa i prerequisiti effettuato |
| 60.160.172 | (FBI) Distribuzione effettuato | Installazioni effettuato  |
| 60.160.173 | (FBI) Configurazione effettuato | Configura effettuato |
| 60.160.177 | (KV) Prerequisito KeyVault | Prerequisiti di installazione KeyVault |
| 60.160.178 | (KV) Distribuzione KeyVault | Installa KeyVault  |
| 60.160.179 | (KV) Configurazione di KeyVault | Configura KeyVault | 
| 60.190.191 | (FBI) Configurare una raccolta | Configurare una raccolta |
| 60.190.192 | (FBI) Configurare i servizi di infrastruttura ad anello | Configurare i servizi di infrastruttura ad anello |
| 60.221 | (FBI) Macchine virtuali Console di configurazione | Installa Console server in macchine virtuali guest. |
| 60.222 | (FBI) Macchine virtuali Console di configurazione | Spostare il contenuto DVM Console macchina virtuale. |
| 251 | Preparare l'ambiente per si riavvia host futuri | Set di criteri di riavviare il computer |


## <a name="next-steps"></a>Passaggi successivi

[Domande frequenti](azure-stack-FAQ.md)
