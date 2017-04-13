<properties
    pageTitle="Prima di distribuire Azure Stack prova | Microsoft Azure"
    description="Visualizzare i requisiti hardware e ambiente di Azure Stack prova (amministratore del servizio)."
    services="azure-stack"
    documentationCenter=""
    authors="ErikjeMS"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/12/2016"
    ms.author="erikje"/>

# <a name="azure-stack-deployment-prerequisites"></a>Prerequisiti per la distribuzione di Azure Stack

Prima di distribuire Azure Stack prova ([Di prova](azure-stack-poc.md)), verificare che il computer soddisfi i requisiti seguenti.
I requisiti di distribuzione Technical Preview 2 per la prova pratica sono equivalenti a quelli necessari per Technical Preview 1. Pertanto, è possibile utilizzare lo stesso hardware utilizzato per l'anteprima singola casella precedente.

## <a name="hardware"></a>Hardware

| Componente | Valore minimo  | Consigliati |
|---|---|---|
| Unità disco: sistema operativo | 1 disco OS con minimo di 200 GB di spazio disponibile per partizione di sistema (SSD o sul disco rigido) | 1 disco OS con minimo di 200 GB di spazio disponibile per partizione di sistema (SSD o sul disco rigido) |
| Unità disco: generale dati prova dello Stack di Azure | 4 dischi. Ogni disco fornisce almeno 140 GB di capacità (SSD o sul disco rigido). Verranno utilizzati tutti i dischi disponibili. | 4 dischi. Ogni disco fornisce un minimo di 250 GB della capacità (SSD o sul disco rigido). Verranno utilizzati tutti i dischi disponibili.|
| Calcolare: CPU | Due Socket: 12 core fisici (totale)  | Due Socket: 16 core fisici (totale) |
| Calcolare: memoria | 96 GB DI RAM  | 128 GB DI RAM |
| Calcolare: BIOS | Hyper-V abilitato (con supporto SLAT)  | Hyper-V abilitato (con supporto SLAT) |
| Rete: NIC | Certificazione di Windows Server 2012 R2 necessari per NIC; Nessuna funzionalità speciali obbligatoria | Certificazione di Windows Server 2012 R2 necessari per NIC; Nessuna funzionalità speciali obbligatoria |
| Certificazione logo hardware | [Certificati per Windows Server 2012 R2](http://windowsservercatalog.com/results.aspx?&chtext=&cstext=&csttext=&chbtext=&bCatID=1333&cpID=0&avc=79&ava=0&avq=0&OR=1&PGS=25&ready=0) |[Certificati per Windows Server 2012 R2](http://windowsservercatalog.com/results.aspx?&chtext=&cstext=&csttext=&chbtext=&bCatID=1333&cpID=0&avc=79&ava=0&avq=0&OR=1&PGS=25&ready=0)|

**Configurazione delle unità disco dati:** Tutte le unità di dati devono avere lo stesso tipo (tutte le SA o SATA tutti) e capacità. Se si utilizzano unità disco SA, le unità disco deve essere collegate tramite un unico percorso (non MPIO supporto multi-path fornito).

**Opzioni di configurazione HBA**
 
- (Consigliato) HBA semplice
- RAID HBA-scheda deve essere configurato in modalità "attraversano"
- HBA RAID – dischi devono essere configurati come singolo disco, RAID 0

**Elementi multimediali e bus supportati digitare combinazioni**

-   DISCO RIGIDO SATA

-   DISCO RIGIDO SA

-   DISCO RIGIDO RAID

-   RAID SSD (se il tipo di supporto non viene specificato/sconosciuto\*)

-   SSD SATA + DISCO RIGIDO SATA

-   SA SSD + SA SUL DISCO RIGIDO

\*Opzionale senza la funzionalità di pass-through non riconoscono il tipo di supporto. Ad esempio controller verrà contrassegnato disco rigido e SSD come non specificato. In questo caso, il SSD da utilizzare come archivio permanente anziché la memorizzazione nella cache dispositivi. Di conseguenza, è possibile distribuire la prova pratica dello Stack di Microsoft Azure su tali SSDs.

**Esempio di schede bus host**: LSI 9207 8i, 8i di 9300 LSI o LSI 9265 8i in modalità pass-through

Configurazioni OEM di esempio sono disponibili.

## <a name="operating-system"></a>Sistema operativo

| | **Requisiti**  |
|---|---|
| **Versione del sistema operativo** | Windows Server 2012 R2 o versione successiva. Versione del sistema operativo non è critica prima di avvia la distribuzione, come verrà avviato il computer host nel disco rigido virtuale incluso in zip installazione dello Stack di Azure. Il sistema operativo e tutte le patch sono già integrate nell'immagine. Non usare i tasti di attivazione di tutte le istanze di Windows Server utilizzate la prova pratica.|

## <a name="deployment-requirements-check-tool"></a>Strumento di verifica dei requisiti di distribuzione

Dopo aver installato il sistema operativo, è possibile utilizzare il [Controllo di distribuzione per Azure Stack Technical Preview 2](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b) per verificare che l'hardware soddisfi i requisiti.



## <a name="microsoft-azure-active-directory-accounts"></a>Account di Microsoft Azure Active Directory

La distribuzione di Microsoft Azure Stack prova deve essere connesso a Azure. Di conseguenza, è necessario preparare un account di Microsoft Azure Active Directory prima di eseguire la distribuzione script di PowerShell. Questo account diventa l'amministratore globale per il tenant di Azure Active Directory. Da utilizzare per eseguire il provisioning e delegare applicazioni e identità di servizio per tutti i servizi di Azure Stack che interagiscono con Azure Active Directory e API grafici. Verrà inoltre utilizzato come il proprietario della sottoscrizione provider predefinito (che è possibile modificare). È possibile accedere al portale di amministrazione del sistema dello Stack di Azure tramite questo account.

1. Creare un account Azure Active Directory che è l'amministratore della directory di almeno un Azure Active Directory. Se si è già presente, è possibile utilizzare che. In caso contrario, è possibile crearne uno gratuitamente su [http://azure.microsoft.com/en-us/pricing/free-trial/](http://azure.microsoft.com/pricing/free-trial/) (in Cina, visitare <http://go.microsoft.com/fwlink/?LinkID=717821> invece.)

    Salvare le credenziali per l'utilizzo nel passaggio 6 di [eseguire lo script di distribuzione di PowerShell](azure-stack-run-powershell-script.md#run-the-powershell-deployment-script). L'account di *amministratore del servizio* è possibile configurare e gestire cloud delle risorse, gli account utente, piani tenant, le quote e prezzi. Nel portale di vengano creare cloud sito Web, cloud privati macchina virtuale, creare piani e gestire le sottoscrizioni utente.

2. [Creare](azure-stack-add-new-user-aad.md) almeno un account in modo che è possibile accedere a prova di Stack Azure come un tenant.

  	| **Account Azure Active Directory**  | **Supportati?** |
  	|---|---| 
  	| Account aziendale o dell'istituto di istruzione con abbonamento valido a Azure pubblico  | Sì |
  	| Account Microsoft con abbonamento valido a Azure pubblico  | No |
  	| Account aziendale o dell'istituto di istruzione con abbonamento valido a Azure Cina  | Sì |
  	| Account aziendale o dell'istituto di istruzione con abbonamento valido a Contattaci per enti pubblici Azure  | Sì |


## <a name="network"></a>Rete

### <a name="switch"></a>Opzione

Una porta disponibile un'opzione per il computer di prova.  

Il computer di prova Stack Azure supporta la connessione a un parametro accesso porta o trunk. Non sono necessario alcun caratteristiche speciali sul parametro. Se si utilizza una porta trunk o se è necessario configurare un ID VLAN, è necessario fornire l'ID VLAN come un parametro di distribuzione. È possibile vedere esempi nell' [elenco dei parametri di distribuzione](azure-stack-run-powershell-script.md).

### <a name="subnet"></a>Subnet

Non connettersi al computer di prova subnet seguenti:
- 192.168.200.0/24
- 192.168.100.0/27
- 192.168.101.0/26
- 192.168.102.0/24
- 192.168.103.0/25
- 192.168.104.0/25

Queste subnet sono riservate per le reti interne all'interno dell'ambiente di Microsoft Azure Stack prova.

### <a name="ipv4ipv6"></a>IPv4/IPv6

È supportata solo IPv4. Non è possibile creare le reti IPv6.

### <a name="dhcp"></a>DHCP

Verificare che nella rete NIC si connette a è disponibile un server DHCP. Se DHCP non è disponibile, è necessario preparare una rete IPv4 statica aggiuntiva oltre a quella utilizzata dall'host. È necessario specificare l'indirizzo IP e gateway come un parametro di distribuzione. È possibile vedere esempi nell' [elenco dei parametri di distribuzione](azure-stack-run-powershell-script.md).

### <a name="internet-access"></a>Accesso a Internet

Pila di Azure richiede l'accesso a Internet, direttamente o tramite un proxy trasparente. Pila di Azure non supporta la configurazione di un proxy web per consentire l'accesso a Internet. IP host e il nuovo PI assegnata a MAS-BGPNAT01 (da DHCP o indirizzo IP statico) deve essere in grado di accedere a Internet. Porte 80 e 443 vengono utilizzate in domini graph.windows.net e login.windows.net.

### <a name="telemetry"></a>Telemetria

Per supportare il flusso di dati di telemetria, porta 443 (HTTPS) deve essere aperta in rete. L'endpoint client è https://vortex-win.data.microsoft.com.


## <a name="next-steps"></a>Passaggi successivi

[Scaricare il pacchetto di distribuzione di Azure Stack prova](https://azure.microsoft.com/overview/azure-stack/try/?v=try)

[Distribuire Stack Azure prova](azure-stack-run-powershell-script.md)
