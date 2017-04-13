<properties
    pageTitle="Panoramica delle autoscale in macchine virtuali di Microsoft Azure, servizi Cloud e Web Apps | Microsoft Azure"
    description="Panoramica delle autoscale in Microsoft Azure. Si applica a macchine virtuali, servizi Cloud e Web App."
    authors="rboucher"
    manager="carolz"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="robb"/>

# <a name="overview-of-autoscale-in-microsoft-azure-virtual-machines-cloud-services-and-web-apps"></a>Panoramica delle autoscale in macchine virtuali di Microsoft Azure, servizi Cloud e Web Apps

In questo articolo vengono illustrati è quali autoscale Microsoft Azure, i vantaggi e su come iniziare a usarlo.  

Azure autoscale Monitor si applica solo a [Macchina virtuale scala set](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [Servizi Cloud](https://azure.microsoft.com/services/cloud-services/)e [Servizio App - Web Apps](https://azure.microsoft.com/services/app-service/web/).

>[AZURE.NOTE] Azure include due metodi autoscale. Una versione precedente di autoscale si applica a macchine virtuali (disponibilità set). Questa caratteristica garantisce un supporto limitato e si consiglia di migrazione a macchine Virtuali scala set per il supporto autoscale più veloce e affidabile. In questo articolo è incluso un collegamento come usare la tecnologia precedente.  


## <a name="what-is-autoscale"></a>Che cos'è autoscale?

AutoScale consente di disporre la giusta quantità di risorse in esecuzione per gestire il carico sull'applicazione. È possibile aggiungere risorse per gestire gli aumenti di carico di risparmiare anche mediante la rimozione di risorse che ci siano inattive. Specificare un numero minimo e massimo di istanze di esecuzione e aggiungere o rimuovere macchine virtuali automaticamente in base a un set di regole. Con un minimo rende che viene eseguita l'applicazione sempre anche in alcun carico. Massima limita le possibili ogni ora il costo totale. Ridimensionate automaticamente tra i due estremi utilizzando le regole create.

 ![Spiegazione delle AutoScale. Aggiungere e rimuovere macchine virtuali](./media/monitoring-autoscale-overview/AutoscaleConcept.png)

Quando vengono soddisfatte le condizioni regola viene attivata una o più azioni autoscale. È possibile aggiungere e rimuovere macchine virtuali o eseguire altre azioni. Il diagramma concettuale seguente illustra il processo.  

 ![Diagramma di flusso Autoscale concettuale](./media/monitoring-autoscale-overview/AutoscaleOverview3.png)


## <a name="autoscale-process-explained"></a>SPIEGAZIONE AutoScale processo
Spiegazione seguente si applica a parti del diagramma precedente.   

### <a name="resource-metrics"></a>Metriche di risorse
Risorse generano metriche, in un secondo momento elaborate da regole. Metrica accompagnato tramite vari metodi.
Set di scala macchine Virtuali utilizza dati di telemetria di agenti diagnostica Windows Azure mentre telemetria per le applicazioni Web e servizi Cloud proviene direttamente dall'infrastruttura di Azure. Alcune statistiche comune includono l'utilizzo della CPU, l'utilizzo di memoria, thread conteggi, lunghezza coda e utilizzo del disco. Per un elenco dei quali è possibile utilizzare i dati di telemetria, vedere [Autoscale metriche comuni](insights-autoscale-common-metrics.md).

### <a name="time"></a>Ora
Regole basate su pianificazione seguono si basano su UTC. È necessario impostare il fuso orario correttamente durante l'impostazione delle regole.  

### <a name="rules"></a>Regole
Il diagramma mostra solo una regola autoscale, ma è possibile avere molti di essi. È possibile creare regole che si sovrappongono complesse in base alle esigenze per la propria situazione.  Includono i tipi di regole  

 - **In base a unità di misura metriche** - operazione da eseguire, ad esempio, quando l'utilizzo della CPU è superiore al 50%.
 - **Basate sul tempo** - ad esempio trigger un webhook ogni 8 am sabato in un determinato fuso orario.

Regole basate su unità di misura metriche misurano carico dell'applicazione e aggiungere o rimuovere macchine virtuali in base a tale carico. Regole basate su pianificazione consentono di scalare quando viene visualizzata dello schema di ora il carico di e si desidera ridimensionare prima di un aumento di carico possibili o Riduci si verifica.  


### <a name="actions-and-automation"></a>Automazione e azioni

Le regole possono attivare uno o più tipi di azioni.

- **Scala** - macchine virtuali di scala avanti o indietro
- **Messaggio di posta elettronica** - Invia messaggio di posta elettronica per gli amministratori di abbonamento, gli amministratori di co e/o indirizzo di posta elettronica aggiuntivi specificato
- **Automatizzare tramite webhooks** - webhooks di chiamata, che può attivare più complesse azioni interne o esterne Azure. All'interno di Azure, è possibile avviare un runbook automazione Azure, funzione Azure o Azure logica App. Esempio 3 ° URL di terze parti esterne Azure include servizi come Twilio e margine di flessibilità.


## <a name="autoscale-settings"></a>Impostazioni AutoScale
AutoScale utilizza la seguente terminologia e struttura.

- Un' **impostazione autoscale** viene letto dal motore autoscale per determinare se le dimensioni verso l'alto o verso il basso. Contiene uno o più profili, informazioni sulla risorsa di destinazione e le impostazioni di notifica.
    - Un **profilo autoscale** è una combinazione di capacità impostazione, un set di regole di trigger e azioni scala per il profilo e una ricorrenza. È possibile impostare più profili, che consentono di occuparsi di requisiti differenti sovrapposte.
        - **Impostazione di capacità** indica il minimo, massimo e valori predefiniti per numero di istanze. [posizione appropriata da utilizzare figura 1]
        - Una **regola** include un trigger, ovvero un trigger metrico o un trigger di tempo e un'azione scala, che indica se autoscale deve scalare verso l'alto o verso il basso quando viene soddisfatta tale regola.
        - Una **ricorrenza** indica quando autoscale necessario inserire il profilo in vigore. È possibile impostare profili autoscale diversi per le diverse ore del giorno o i giorni della settimana, ad esempio.
- Un' **impostazione di notifica** consente di definire quali notifiche dovrebbe essere eseguita quando si verifica in base alle che soddisfa i criteri di uno dei profili dell'impostazione autoscale un evento di autoscale. AutoScale può inviare comunicazioni uno o più indirizzi di posta elettronica o effettuare chiamate a uno o più webhooks.

![Impostazione di Azure autoscale, profilo e struttura di regole](./media/monitoring-autoscale-overview/AzureResourceManagerRuleStructure3.png)

L'elenco completo dei campi configurabili e delle descrizioni è disponibile in [API REST Autoscale](https://msdn.microsoft.com/library/dn931928.aspx).

Per esempi di codice, vedere

* [Configurazione avanzata di Autoscale utilizzare i modelli di Manager delle risorse per i set di scala macchine Virtuali](insights-advanced-autoscale-virtual-machine-scale-sets.md)  
* [API REST AutoScale](https://msdn.microsoft.com/library/dn931953.aspx)



## <a name="horizontal-vs-vertical-scaling"></a>Ridimensionamento verticale e orizzontale

AutoScale aumenta risorse in scale sola orizzontalmente, quale un aumento ("Indietro") o ridurre ("in") il numero di istanze di macchine Virtuali.  Scalabilità orizzontale, che risulta più flessibile in una situazione cloud, in quanto consente di eseguire potenzialmente migliaia di macchine virtuali di gestire un carico. Scalabilità verticale è diversa. Mantiene lo stesso numero di macchine virtuali, ma rende la macchina virtuale più ("su") o meno ("giù") potente. Power viene misurato in memoria, la velocità della CPU, spazio su disco e così via.  Ridimensionamento verticale presenta altre limitazioni. È dipende dalla disponibilità di hardware più grande, che può variare per regione e rapidamente visitate e limite superiore. Ridimensionamento verticale in genere anche richiede una tabulazione macchine Virtuali e iniziare a. Per ulteriori informazioni, vedere [ridimensionare verticalmente Azure macchina virtuale con Azure automazione](../virtual-machines/virtual-machines-linux-vertical-scaling-automation.md).


## <a name="methods-of-access"></a>Metodi di accesso
È possibile impostare autoscale tramite

- [Portale di Azure](insights-how-to-scale.md)
- [PowerShell](insights-powershell-samples.md#create-and-manage-autoscale-settings)
- [Multipiattaforma comando interfaccia della riga)](insights-cli-samples.md#autoscale )
- [API REST Monitor Azure](https://msdn.microsoft.com/library/azure/dn931953.aspx )

## <a name="supported-services-for-autoscale"></a>Servizi supportati da per autoscale


| Servizio                              | Schema e documenti                                       |
|--------------------------------------|-----------------------------------------------------|
| Web App                             | [Ridimensionamento delle applicazioni Web](insights-how-to-scale.md)              |
| Servizi cloud                       | [AutoScale un servizio Cloud](../cloud-services/cloud-services-how-to-scale.md) |
| Macchine virtuali: classica           | [Set di disponibilità scala classica macchina virtuale](https://blogs.msdn.microsoft.com/kaevans/2015/02/20/autoscaling-azurevirtual-machines/) |
| Macchine virtuali: Set di scala di Windows| [Ridimensionamento delle macchine Virtuali scala set in Windows](../virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md)  |
| Macchine virtuali di: Imposta scala Linux  | [Ridimensionamento delle macchine Virtuali scala set in Linux](../virtual-machine-scale-sets/virtual-machine-scale-sets-linux-autoscale.md) |
| Macchine virtuali: Esempio di Windows   | [Configurazione avanzata di Autoscale utilizzare i modelli di Manager delle risorse per i set di scala macchine Virtuali](insights-advanced-autoscale-virtual-machine-scale-sets.md) |

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulle autoscale, usare Autoscale Walkthroughs elencati in precedenza o consultare le risorse seguenti:

- [Azure metriche di comuni autoscale Monitor](insights-autoscale-common-metrics.md)
- [Procedure consigliate per autoscale Monitor Azure](insights-autoscale-best-practices.md)
- [Utilizzare azioni autoscale per inviare posta elettronica e webhook le notifiche di avviso](insights-autoscale-to-webhook-email.md)
- [API REST AutoScale](https://msdn.microsoft.com/library/dn931953.aspx)
- [Risoluzione dei problemi macchina virtuale scala set Autoscale](../virtual-machine-scale-sets/virtual-machine-scale-sets-troubleshoot.md)
