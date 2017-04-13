<properties
    pageTitle="Ambiente servizio App e il ridimensionamento automatico | Microsoft Azure"
    description="Ambiente servizio App e il ridimensionamento automatico"
    services="app-service"
    documentationCenter=""
    authors="btardif"
    manager="wpickett"
    editor=""
/>

<tags
    ms.service="app-service"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/24/2016"
    ms.author="byvinyal"
/>

# <a name="autoscaling-and-app-service-environment"></a>Ambiente servizio App e il ridimensionamento automatico

Azure ambienti servizio App supportano *il ridimensionamento automatico*. È possibile pool di lavoro singoli autoscale in base a metriche o pianificazione.

![Opzioni di AutoScale per un pool di lavoro.][intro]

Il ridimensionamento automatico consente di ottimizzare l'utilizzo delle risorse automaticamente ingrandimento o riduzione di un ambiente App per adattare il budget e o caricare il profilo.

## <a name="configure-worker-pool-autoscale"></a>Configurare autoscale pool di lavoro

È possibile accedere alla funzionalità di autoscale dalla scheda **Impostazioni** del pool di lavoro.

![Scheda Impostazioni del pool di lavoro.][settings-scale]

A questo punto, che l'interfaccia deve essere abbastanza familiare perché è la stessa esperienza viene visualizzato quando si modifica la scala di un piano di servizio di App. 

![Impostazioni di scala manuale.][scale-manual]

È inoltre possibile configurare un profilo autoscale.

![Impostazioni AutoScale.][scale-profile]

I profili AutoScale sono utili per impostare limiti di dimensioni dell'azienda. In questo modo, è possibile impostare un prestazioni coerenti esperienza utente impostando un valore di scala limite inferiore (1) e un'estremità di spesa prevedibili impostando un limite superiore (2).

![Impostazioni di scala nel profilo.][scale-profile2]

Dopo avere definito un profilo, è possibile aggiungere regole autoscale scalare verso l'alto o verso il basso il numero di istanze nel pool di lavoro entro i limiti definiti dal profilo. Regole AutoScale sono basate sui dati statistici relativi.

![Regola di scala.][scale-rule]

 Per definire regole autoscale è possono utilizzare qualsiasi pool di lavoro o metriche front-end. Queste sono le stesse metriche è possibile monitorare nei grafici blade delle risorse o impostare avvisi per.

## <a name="autoscale-example"></a>Esempio di AutoScale

AutoScale di un ambiente servizio App può essere illustrato meglio esaminando uno scenario.

Questo articolo illustra tutte le necessarie considerazioni quando si configura autoscale. L'articolo illustra le interazioni che entrano in gioco quando si analizzano il ridimensionamento automatico ambienti servizio App ospitate in ambiente App.

### <a name="scenario-introduction"></a>Introduzione di uno scenario

Franco è sysadmin per un'organizzazione che è stata eseguita la migrazione di una parte di carichi di lavoro che gestisce in un ambiente servizio App.

Ambiente servizio App è configurato in scala manuale come segue:

* **Anteriore estremità:** 3
* **Pool di lavoro 1**: 10
* **Pool di lavoro 2**: 5
* **Pool di lavoro 3**: 5

Pool di lavoro 1 viene usato per carichi di lavoro di produzione, mentre il pool di lavoro 2 e 3 pool di lavoro vengono usati per qualità (domande e risposte) e carichi di lavoro di sviluppo.

I piani di servizio App per domande e risposte e dev sono configurati in scala manuale. Il piano di servizio App di produzione è impostata per scalare automaticamente di occuparsi di variazioni relative a carico e il traffico.

Franco è molto familiarità con l'applicazione. Saprà che gli orari di punta per carico siano compresi tra 9:00:00 e 6:00 PM, poiché si tratta di un'applicazione (Line) line-of-business utilizzate dipendenti mentre si trovano in office. L'uso inserisce in seguito quando gli utenti vengono eseguiti per tale giorno. All'esterno di orari, è ancora presente alcune carico perché gli utenti possono accedere in remoto l'app con i dispositivi mobili o un PC di casa. Il piano di servizio App di produzione è già configurata per scalare automaticamente in base all'utilizzo della CPU con le regole seguenti:

![Impostazioni specifiche per l'applicazione line.][asp-scale]

|   **Profilo AutoScale: i giorni lavorativi: piano di servizio di App**     |   **Profilo AutoScale: i fine settimana: piano di servizio di App**     |
|   ----------------------------------------------------    |   ----------------------------------------------------    |
|   **Nome:** Profilo del giorno della settimana                               |   **Nome:** Profilo festivi                               |
|   **Scalare da:** Regole pianificazione e le prestazioni            |   **Scalare da:** Regole pianificazione e le prestazioni            |
|   **Profilo:** Giorni lavorativi                                   |   **Profilo:** Fine settimana                                    |
|   **Tipo:** Ricorrenza                                    |   **Tipo:** Ricorrenza                                    |
|   **Intervallo di destinazione:** le istanze di 5 a 20                     |   **Intervallo di destinazione:** 3 a 10 istanze                     |
|   **Giorni:** Lunedì, martedì, mercoledì, giovedì, venerdì  |   **Giorni:** Sabato, domenica                              |
|   **Ora di inizio:** 9:00:00                                 |   **Ora di inizio:** 9:00:00                                 |
|   **Fuso orario:** ORA UTC-08                                   |   **Fuso orario:** ORA UTC-08                                   |
|                                                           |                                                           |
|   **Regola AutoScale (scala su)**                           |   **Regola AutoScale (scala su)**                           |
|   **Risorsa:** Produzione (ambiente servizio App)      |   **Risorsa:** Produzione (ambiente servizio App)      |
|   **Unità di misura metriche:** % CPU                                       |   **Unità di misura metriche:** % CPU                                       |
|   **Operazione:** Maggiore di 60%                         |   **Operazione:** Superiore all'80%                         |
|   **Durata:** 5 minuti                                 |   **Durata:** 10 minuti                                |
|   **Ora aggregazione:** Media                           |   **Ora aggregazione:** Media                           |
|   **Azione:** Aumentare il numero da 2                         |   **Azione:** Aumentare il numero da 1                         |
|   **Interessanti verso il basso (minuti):** 15                             |   **Interessanti verso il basso (minuti):** 20                             |
|                                                           |                                                           |
  	|   **Regola AutoScale (scala verso il basso)**                     |   **Regola AutoScale (scala verso il basso)**                         |
|   **Risorsa:** Produzione (ambiente servizio App)      |   **Risorsa:** Produzione (ambiente servizio App)      |
|   **Unità di misura metriche:** % CPU                                       |   **Unità di misura metriche:** % CPU                                       |
|   **Operazione:** Meno di 30%                            |   **Operazione:** Meno di 20%                            |
|   **Durata:** 10 minuti                                |   **Durata:** 15 minuti                                |
|   **Ora aggregazione:** Media                           |   **Ora aggregazione:** Media                           |
|   **Azione:** Ridurre il numero da 1                         |   **Azione:** Ridurre il numero da 1                         |
|   **Interessanti verso il basso (minuti):** 20                             |   **Interessanti verso il basso (minuti):** 10                             |

### <a name="app-service-plan-inflation-rate"></a>Tasso di inflazione piano di servizio di App

Piani di servizio App che vengono configurati per scalare automaticamente farlo una velocità massima orarie. Il tasso può essere calcolato in base ai valori della regola autoscale.

Comprendere e calcolare il *tasso di inflazione piano di servizio App* è importante per servizio App ambiente autoscale perché scala apportate a un pool di lavoro non vengono istantanee.

Il tasso di inflazione piano di servizio App viene calcolato nel modo seguente:

![Servizio di App tasso di inflazione il calcolo del piano.][ASP-Inflation]

In base a Autoscale-scala la regola per il profilo del giorno della settimana della produzione piano di servizio App:

![Tasso di inflazione servizio App piano per i giorni lavorativi in base a Autoscale-scala la regola.][Equation1]

Nel caso dei Autoscale-scala la regola per il profilo festivi di produzione piano di servizio App, la formula viene risolto in:

![Tasso di inflazione servizio App piano per i fine settimana in base a Autoscale-scala la regola.][Equation2]

Questo valore può essere calcolato anche per le operazioni di ridimensionamento verso il basso.

In base a Autoscale – regola scala verso il basso per il profilo del giorno della settimana della produzione piano di servizio App, questo sarebbe come indicato di seguito:

![Tasso di inflazione servizio App piano per i giorni lavorativi in base a Autoscale – regola scala verso il basso.][Equation3]

Nel caso dei Autoscale – regola scala verso il basso per il profilo festivi di produzione piano di servizio App, la formula viene risolto in:  

![Tasso di inflazione servizio App piano per i fine settimana in base a Autoscale – regola scala verso il basso.][Equation4]

Il piano di servizio App produzione espandibile in una velocità massima di otto istanze all'ora nel corso della settimana e quattro le istanze all'ora durante il fine settimana. È possibile rilasciare istanze una velocità massima di quattro istanze/ora nel corso della settimana e sei istanze all'ora nei fine settimana.

Se più piani di servizio di App sono ospitati in un pool di lavoro, è necessario calcolare il *tasso di inflazione totale* dato dalla somma del tasso inflazione per tutti i piani di servizio App che vengono hosting del pool di lavoro.

![Calcolo del totale tasso di inflazione per più piani di servizio App ospitato in un pool di lavoro.][ASP-Total-Inflation]

### <a name="use-the-app-service-plan-inflation-rate-to-define-worker-pool-autoscale-rules"></a>Utilizzare il tasso di inflazione piano di servizio di App per definire regole di autoscale pool di lavoro

Lavoro pool che ospitano i piani di servizio App che vengono configurati per scalare automaticamente necessario allocare un buffer della capacità. Il buffer consente di operazioni autoscale di ingrandimento e riduzione del piano di servizio App in base alle esigenze. Il buffer minimo sarebbe il totale App servizio pianificare inflazione tasso calcolato.

Poiché le operazioni di ridimensionamento ambiente servizio App richiedono del tempo da applicare, qualsiasi modifica apportata deve tenere conto eventuali altre modifiche di richiesta che potrebbero verificarsi durante l'esecuzione di un'operazione di ridimensionamento. Per adattare la latenza, è consigliabile utilizzare il totale App servizio pianificare inflazione tasso calcolato come il numero minimo di istanze che vengono aggiunti per ogni operazione autoscale.

Con queste informazioni, è possibile definire franco le seguenti autoscale profilo e le regole:

![Regole di profilo AutoScale ad esempio Line.][Worker-Pool-Scale]

|   **Profilo AutoScale: i giorni lavorativi**                        |   **Profilo AutoScale: i fine settimana**                |
|   ----------------------------------------------------    |   --------------------------------------------    |
|   **Nome:** Profilo del giorno della settimana                               |   **Nome:** Profilo festivi                       |
|   **Scalare da:** Regole pianificazione e le prestazioni            |   **Scalare da:** Regole pianificazione e le prestazioni    |
|   **Profilo:** Giorni lavorativi                                   |   **Profilo:** Fine settimana                            |
|   **Tipo:** Ricorrenza                                    |   **Tipo:** Ricorrenza                            |
|   **Intervallo di destinazione:** 13 e 25 istanze                    |   **Intervallo di destinazione:** 6-15 istanze             |
|   **Giorni:** Lunedì, martedì, mercoledì, giovedì, venerdì  |   **Giorni:** Sabato, domenica                      |
|   **Ora di inizio:** 7:00 AM                                 |   **Ora di inizio:** 9:00:00                         |
|   **Fuso orario:** ORA UTC-08                                   |   **Fuso orario:** ORA UTC-08                           |
|                                                           |                                                   |
|   **Regola AutoScale (scala su)**                           |   **Regola AutoScale (scala su)**                   |
|   **Risorsa:** Pool di lavoro 1                             |   **Risorsa:** Pool di lavoro 1                     |
|   **Unità di misura metriche:** WorkersAvailable                            |   **Unità di misura metriche:** WorkersAvailable                    |
|   **Operazione:** Minore di 8                              |   **Operazione:** Minore di 3                      |
|   **Durata:** 20 minuti                                |   **Durata:** 30 minuti                        |
|   **Ora aggregazione:** Media                           |   **Ora aggregazione:** Media                   |
|   **Azione:** Aumentare conteggio di 8                         |   **Azione:** Aumentare conteggio di 3                 |
|   **Interessanti verso il basso (minuti):** 180                            |   **Interessanti verso il basso (minuti):** 180                    |
|                                                           |                                                   |
|   **Regola AutoScale (scala verso il basso)**                         |   **Regola AutoScale (scala verso il basso)**                 |
|   **Risorsa:** Pool di lavoro 1                             |   **Risorsa:** Pool di lavoro 1                     |
|   **Unità di misura metriche:** WorkersAvailable                            |   **Unità di misura metriche:** WorkersAvailable                    |
|   **Operazione:** Maggiori di 8                           |   **Operazione:** Maggiore di 3                   |
|   **Durata:** 20 minuti                                |   **Durata:** 15 minuti                        |
|   **Ora aggregazione:** Media                           |   **Ora aggregazione:** Media                   |
|   **Azione:** Ridurre il numero da 2                         |   **Azione:** Ridurre conteggio di 3                 |
|   **Interessanti verso il basso (minuti):** 120                            |   **Interessanti verso il basso (minuti):** 120                    |

L'intervallo di destinazione definito nel profilo viene calcolato dalle istanze minime definite nel profilo per il piano di servizio App + buffer.

L'intervallo massimo è la somma di tutti gli intervalli massimo per tutti i piani di servizio App ospitati nel pool di lavoro.

Il numero di aumento per la scala le regole da impostare per il tasso di inflazione App piano di servizio per scala almeno 1x.

Riduci conteggio può essere modificato su un valore tra 1/2 X o 1x il tasso di inflazione App piano di servizio per scala verso il basso.

### <a name="autoscale-for-front-end-pool"></a>AutoScale per pool front-end

Regole per autoscale front-end sono più semplice per i pool di lavoro. Principalmente, è necessario  
Assicurarsi che la durata della misurazione e timer raffreddamento considera che le operazioni di ridimensionamento in un piano di servizio App non sono istantanee.

Per questo scenario, franco sappia che la frequenza degli errori aumenta dopo front-end raggiunga 80% della CPU e imposta la regola autoscale per aumentare le istanze come indicato di seguito:

![Specificare le impostazioni AutoScale pool front-end.][Front-End-Scale]

|   **Profilo AutoScale: Front end**              |
|   --------------------------------------------    |
|   **Nome:** AutoScale – Front end                |
|   **Scalare da:** Regole pianificazione e le prestazioni    |
|   **Profilo:** Ogni giorno                           |
|   **Tipo:** Ricorrenza                            |
|   **Intervallo di destinazione:** 3 a 10 istanze             |
|   **Giorni:** Ogni giorno                              |
|   **Ora di inizio:** 9:00:00                         |
|   **Fuso orario:** ORA UTC-08                           |
|                                                   |
|   **Regola AutoScale (scala su)**                   |
|   **Risorsa:** Pool front-end                    |
|   **Unità di misura metriche:** % CPU                               |
|   **Operazione:** Maggiore di 60%                 |
|   **Durata:** 20 minuti                        |
|   **Ora aggregazione:** Media                   |
|   **Azione:** Aumentare conteggio di 3                 |
|   **Interessanti verso il basso (minuti):** 120                    |
|                                                   |
|   **Regola AutoScale (scala verso il basso)**                 |
|   **Risorsa:** Pool di lavoro 1                     |
|   **Unità di misura metriche:** % CPU                               |
|   **Operazione:** Meno di 30%                    |
|   **Durata:** 20 minuti                        |
|   **Ora aggregazione:** Media                   |
|   **Azione:** Ridurre conteggio di 3                 |
|   **Interessanti verso il basso (minuti):** 120                    |

<!-- IMAGES -->
[intro]: ./media/app-service-environment-auto-scale/introduction.png
[settings-scale]: ./media/app-service-environment-auto-scale/settings-scale.png
[scale-manual]: ./media/app-service-environment-auto-scale/scale-manual.png
[scale-profile]: ./media/app-service-environment-auto-scale/scale-profile.png
[scale-profile2]: ./media/app-service-environment-auto-scale/scale-profile-2.png
[scale-rule]: ./media/app-service-environment-auto-scale/scale-rule.png
[asp-scale]: ./media/app-service-environment-auto-scale/asp-scale.png
[ASP-Inflation]: ./media/app-service-environment-auto-scale/asp-inflation-rate.png
[Equation1]: ./media/app-service-environment-auto-scale/equation1.png
[Equation2]: ./media/app-service-environment-auto-scale/equation2.png
[Equation3]: ./media/app-service-environment-auto-scale/equation3.png
[Equation4]: ./media/app-service-environment-auto-scale/equation4.png
[ASP-Total-Inflation]: ./media/app-service-environment-auto-scale/asp-total-inflation-rate.png
[Worker-Pool-Scale]: ./media/app-service-environment-auto-scale/wp-scale.png
[Front-End-Scale]: ./media/app-service-environment-auto-scale/fe-scale.png
