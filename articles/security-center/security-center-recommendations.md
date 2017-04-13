<properties
   pageTitle="Gestione dei suggerimenti relativi alla protezione in Centro protezione di Azure | Microsoft Azure"
   description="In questo documento illustra come raccomandazioni in Centro protezione di Azure consentono di proteggere le risorse Azure e rimanere in conformità con criteri di sicurezza."
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="MBaldwin"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/25/2016"
   ms.author="terrylan"/>

# <a name="managing-security-recommendations-in-azure-security-center"></a>Gestione dei suggerimenti relativi alla protezione in Centro protezione di Azure

In questo documento illustra come usare consigli in Centro protezione di Azure per proteggere le risorse Azure.

> [AZURE.NOTE] In questo documento introduce il servizio usando una distribuzione di esempio.  Questa non è una Guida dettagliata.

## <a name="what-are-security-recommendations"></a>Quali sono i suggerimenti per la sicurezza?
Nel Centro sicurezza analizza periodicamente lo stato di protezione delle risorse di Azure. Quando nel Centro sicurezza identifica potenziali vulnerabilità, crea consigli. I suggerimenti guidano il processo di configurazione controlli necessari.

## <a name="implementing-security-recommendations"></a>Implementazione suggerimenti per la sicurezza

### <a name="set-recommendations"></a>Consigli set

In [impostazione di criteri di sicurezza in Centro protezione di Azure](security-center-policies.md), descritta la procedura per:

- Configurare i criteri di sicurezza.
- Attivare la raccolta di dati.
- Scegliere quali raccomandazioni per vedere come parte dei criteri di sicurezza.

Criteri consigli centro corrente all'interno degli aggiornamenti del sistema, le regole di base, applicazioni antimalware, [gruppi di sicurezza di rete](../virtual-network/virtual-networks-nsg.md) subnet e interfacce di rete, il controllo dei database SQL, crittografia dati trasparente di database SQL e i firewall dell'applicazione web.  [L'impostazione di criteri di sicurezza](security-center-policies.md) fornisce una descrizione delle singole opzioni recommendation.

### <a name="monitor-recommendations"></a>Consigli monitor
Dopo aver impostato un criterio di protezione, Security Center analizza lo stato di protezione delle risorse per identificare i potenziali vulnerabilità. Riquadro **suggerimenti** su e il **Centro protezione** consente di conoscere il numero totale di raccomandazioni identificato dal Centro protezione.

![Riquadro suggerimenti][1]

Per visualizzare i dettagli di ogni recommendation:

1. Fare clic su **riquadro consigli** su e il **Centro protezione** . Verrà visualizzata e il **consigli** .

I suggerimenti vengono visualizzati in un formato di tabella in cui ogni riga rappresenta un suggerimento particolare. Le colonne di questa tabella sono:

- **Descrizione**: viene illustrato il suggerimento e cosa è necessario eseguire per risolverlo.
- **Risorsa**: Elenca le risorse a cui si applica questo suggerimento.
- **Stato**: descrive lo stato corrente della recommendation:
    - **Apri**: il suggerimento non è stato risolto ancora.
    - **In corso**: il suggerimento attualmente applicato alle risorse e dall'utente è richiesta alcuna azione.
    - **Risolto**: il suggerimento è già stato completato (in questo caso, la riga verrà visualizzato in grigio).
- **GRAVITÀ**: descrive la gravità di tale recommendation specifico:
    - **Altezza**: una vulnerabilità è presente una risorsa significativo (ad esempio un'applicazione, una macchina virtuale o un gruppo di sicurezza di rete) e richiede l'attenzione.
    - **Supporto**: una vulnerabilità e non critici o altri passaggi necessari per eliminarla o completare un processo.
    - **Bassa**: esiste una vulnerabilità che deve essere corretto, ma non richiede attenzione immediata. (Per impostazione predefinita, non vengono presentati consigli basse, ma è possibile filtrare bassa suggerimenti Se si desidera visualizzarle)

Utilizzare la tabella seguente come riferimento per comprendere le indicazioni disponibili e ciascuno di essi verrà cosa se si applica.

> [AZURE.NOTE] Si desidera comprendere la [classica e modelli di distribuzione di Manager delle risorse](../azure-classic-rm.md) per le risorse Azure.

|Suggerimento|Descrizione|
|-----|-----|
|[Abilitare la raccolta di dati per le sottoscrizioni](security-center-enable-data-collection.md)|Si consiglia di attivare la raccolta di dati nei criteri di sicurezza per ciascuna delle sottoscrizioni e tutte le macchine () le sottoscrizioni.|
|[Correggere le vulnerabilità del sistema operativo](security-center-remediate-os-vulnerabilities.md)|Si consiglia di allineare le configurazioni OS con le regole di configurazione consigliata, ad esempio non salvataggio password.|
|[Applicare gli aggiornamenti di sistema](security-center-apply-system-updates.md)|Si consiglia di distribuire aggiornamenti critici e protezione di sistema mancanti in macchine virtuali.|
|[Riavviare il computer dopo l'aggiornamento del sistema](security-center-apply-system-updates.md#reboot-after-system-updates)|Si consiglia riavviare una macchina virtuale per completare il processo di applicazione degli aggiornamenti del sistema.|
|[Aggiungere un firewall di applicazione web](security-center-add-web-application-firewall.md)|Si consiglia di distribuire un firewall di applicazione web (WAF) per gli endpoint web. È possibile proteggere più applicazioni web nel Centro protezione mediante l'aggiunta di queste applicazioni per le distribuzioni WAF esistenti. Accessori WAF (creati tramite il modello di distribuzione di Manager delle risorse) devono essere distribuiti a una rete separata. Accessori WAF (creati tramite il modello di distribuzione classica) sono limitati all'uso di un gruppo di sicurezza di rete. Il supporto verrà esteso a una distribuzione completamente personalizzata di un dispositivo WAF (classica) in futuro. Nel Centro sicurezza indicazioni provisioning WAF per la difesa da attacchi diretti applicazioni web in macchine virtuali e in ambiente servizio App (base). Per altre informazioni di base, vedere la [Documentazione ambiente servizio App](../app-service/app-service-app-service-environments-readme.md). |
|[Finalizzare protezione dell'applicazione](security-center-add-web-application-firewall.md#finalize-application-protection)|Per completare la configurazione di un WAF, il traffico deve essere reindirizzato al accessorio WAF. Seguendo queste indicazioni completeranno le modifiche di installazione necessari.|
|[Aggiungere un Firewall di generazione successivo](security-center-add-next-generation-firewall.md)|Si consiglia di aggiungere un Firewall di generazione successivo (NGFW) da un partner Microsoft per aumentare le protezioni.|
|[Instradare il traffico attraverso NGFW solo](security-center-add-next-generation-firewall.md#route-traffic-through-ngfw-only)|Si consiglia di configurare regole di gruppo (NSG) sicurezza di rete che forzare il traffico in ingresso per la macchina virtuale tramite il NGFW.|
|[Installare Endpoint Protection](security-center-install-endpoint-protection.md)|Si consiglia di effettuare il provisioning programmi antimalware macchine virtuali (solo macchine virtuali Windows).|
|[Risolvere gli avvisi di integrità Endpoint Protection](security-center-resolve-endpoint-protection-health-alerts.md)|Si consiglia di risolvere gli errori di protezione endpoint.|
|[Abilitare i gruppi di sicurezza di rete in subnet o macchine virtuali](security-center-enable-network-security-groups.md)|Si consiglia di abilitare NSGs in subnet o macchine virtuali.|
|[Limitare l'accesso tramite Internet affiancate endpoint](security-center-restrict-access-through-internet-facing-endpoints.md)|Si consiglia di configurare le regole di traffico in ingresso per NSGs.|
|[Abilitare il controllo di SQL server](security-center-enable-auditing-on-sql-servers.md)|Si consiglia l'attivazione del controllo per i server di SQL Azure (servizio di SQL Azure solo; non include SQL in esecuzione in macchine virtuali).|
|[Abilitare il database SQL di controllo](security-center-enable-auditing-on-sql-databases.md)|Si consiglia l'attivazione del controllo per i database di SQL Azure (servizio di SQL Azure solo; non include SQL in esecuzione in macchine virtuali).|
|[Abilitare trasparente la crittografia dei dati nel database SQL](security-center-enable-transparent-data-encryption.md)|Si consiglia di abilitare la crittografia per i database SQL (solo servizio di SQL Azure).|
|[Attivare l'agente di macchine Virtuali](security-center-enable-vm-agent.md)|Consente di visualizzare che richiedono macchine virtuali agente di macchine Virtuali. Per eseguire questa procedura patch analisi previsione analisi e programmi antimalware, è necessario installare l'agente di macchine Virtuali in macchine virtuali. Agente di macchine Virtuali è installato per impostazione predefinita per le macchine virtuali distribuiti da Azure Marketplace. L'articolo [agente macchine Virtuali ed estensioni-parte 2](http://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/) fornisce informazioni su come installare l'agente di macchine Virtuali.|
| [Applicare la crittografia del disco](security-center-apply-disk-encryption.md) |Si consiglia di crittografare il disco di macchine Virtuali utilizzando la crittografia del Azure (Windows e macchine virtuali Linux). La crittografia è consigliata per i volumi sistema operativo e dati di una macchina virtuale.|
|[Fornire informazioni di contatto di sicurezza](security-center-provide-security-contact-details.md) | Si consiglia di fornire protezione informazioni di contatto per ognuna delle sottoscrizioni. Informazioni di contatto sono un numero di telefono e indirizzo di posta elettronica. Le informazioni verranno usate per contattare l'utente se il nostro team sicurezza rileva che vengano compromesse le risorse. |
| [Aggiornare una versione del sistema operativo](security-center-update-os-version.md) | Si consiglia di aggiornare la versione del sistema operativo (SO) per il servizio Cloud per la versione più recente disponibile per la famiglia di sistemi.  Per ulteriori informazioni sui servizi Cloud, vedere [Panoramica di servizi Cloud](../cloud-services/cloud-services-choose-me.md). |
| [Valutazione delle vulnerabilità non è installata](security-center-vulnerability-assessment-recommendations.md) | Consiglia di installare una soluzione di valutazione di una vulnerabilità in di una macchina virtuale. |
| [Correggere vulnerabilità](security-center-vulnerability-assessment-recommendations.md#review-recommendation) | Consente di vedere rilevata dalla soluzione di valutazione di una vulnerabilità installata le macchine Virtuali vulnerabilità delle applicazioni e sistema. |

È possibile filtrare e ignorare i suggerimenti.

1. Scegliere **filtro** e il **consigli** . Viene aperto e il **filtro** e si selezionano i valori di gravità e stato che si desidera visualizzare.

    ![Filtrare i consigli][2]

2. Se si determina che un suggerimento non è applicabile, è possibile ignorare il suggerimento e quindi filtrare fuori la visualizzazione. Esistono due modi per ignorare un suggerimento. Un modo consiste nel fare clic con il pulsante destro un elemento e quindi selezionare **Elimina**. L'altro consiste nel passare il mouse su un elemento, scegliere i puntini di sospensione a destra e quindi selezionare **Elimina**. È possibile visualizzare suggerimenti licenziati facendo clic su **filtro**e quindi selezionando **Dismissed**.

    ![Ignorare recommendation][3]

### <a name="apply-recommendations"></a>Applicare consigli
Dopo aver esaminato tutti i suggerimenti, decidere quale una è necessario applicare prima di tutto. È consigliabile utilizzare la gravità come il parametro principale per valutare quale consigli deve essere applicato prima di tutto.

Nella tabella dei suggerimenti indicati, selezionare un suggerimento e scoprirà che come un esempio su come applicare un suggerimento.

## <a name="see-also"></a>Vedere anche
In questo documento sono stati introdotti a consigli sulla protezione in Centro protezione. Per ulteriori informazioni sul Centro protezione, vedere gli articoli seguenti:

- [Impostazione di criteri di sicurezza in Centro protezione di Azure](security-center-policies.md) , informazioni su come configurare i criteri di sicurezza per gli abbonamenti Azure e i gruppi di risorse.
- [Monitoraggio di protezione in Centro protezione di Azure](security-center-monitoring.md) , informazioni su come controllare lo stato delle risorse di Azure.
- [Gestione e risposta agli avvisi di protezione in Centro protezione di Azure](security-center-managing-and-responding-alerts.md) , informazioni su come gestire e risposta agli avvisi di sicurezza.
- [Monitoraggio delle soluzioni dei partner con Azure Security Center](security-center-partner-solutions.md) -informazioni su come controllare lo stato di integrità delle soluzioni partner.
- [Domande frequenti su Centro protezione di Azure](security-center-faq.md) : domande frequenti sull'utilizzo del servizio di ricerca.
- [Blog di sicurezza di Windows Azure](http://blogs.msdn.com/b/azuresecurity/) , trovare post di blog sulla protezione di Azure e conformità.

<!--Image references-->
[1]: ./media/security-center-recommendations/recommendations-tile.png
[2]: ./media/security-center-recommendations/filter-recommendations.png
[3]: ./media/security-center-recommendations/dismiss-recommendations.png
