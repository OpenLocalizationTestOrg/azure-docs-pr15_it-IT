<properties
   pageTitle="Aggiornamento dell'applicazione: serializzazione dei dati | Microsoft Azure"
   description="Procedure consigliate per la serializzazione dei dati e del relativo impatto aggiornamenti dell'applicazione."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/19/2016"
   ms.author="vturecek"/>


# <a name="how-data-serialization-affects-an-application-upgrade"></a>Effetti di un aggiornamento dell'applicazione serializzazione dei dati

[Aggiornamento dell'applicazione in sequenza](service-fabric-application-upgrade.md), l'aggiornamento viene applicata a un sottoinsieme dei nodi, un aggiornamento del dominio alla volta. Durante questo processo, alcuni domini aggiornamento verrà visualizzato nella versione più recente dell'applicazione in uso e alcuni domini aggiornamento verrà visualizzato in versione precedente dell'applicazione. Durante la distribuzione, la nuova versione dell'applicazione in uso deve essere in grado di leggere la versione precedente dei dati e la versione precedente dell'applicazione in uso deve essere in grado di leggere la nuova versione dei dati. Se il formato dei dati non è compatibile con avanti e indietro, l'aggiornamento potrebbe non riuscire o peggiore, potrebbero essere perditi o danneggiamento di dati. In questo articolo viene illustrato cosa costituisce il formato dati e offre procedure consigliate per garantire che i dati sono avanti e indietro compatibili.


## <a name="what-makes-up-your-data-format"></a>Quali sono i il formato dati?

Nella struttura di servizio Azure, dati e su cui vengono mantenuti replicati provengono dalle classi c#. Per le applicazioni che utilizzano [Raccolte affidabile](service-fabric-reliable-services-reliable-collections.md), che gli oggetti in dizionari affidabili di code. Per le applicazioni che utilizzano [Gli operatori affidabile](service-fabric-reliable-actors-introduction.md), che rappresenta lo stato di supporto per l'attore. Queste classi c# devono essere serializzabile da mantenere e replicato. Di conseguenza, il formato dei dati viene definito dai campi e proprietà serializzati, nonché il modo in cui vengono serializzati. Ad esempio in un `IReliableDictionary<int, MyClass>` i dati sono un serializzato `int` e un serializzato `MyClass`.

### <a name="code-changes-that-result-in-a-data-format-change"></a>Codice viene modificato il risultato di una modifica formato dati

Poiché il formato dei dati dipende dalla classi c#, le modifiche alle classi possono causare una modifica formato dati. È necessario prestare attenzione per garantire che un aggiornamento consente di gestire la modifica del formato dati. Esempi che potrebbero causare modifiche ai formati di dati:

- Aggiungendo o rimuovendo campi o proprietà
- La ridenominazione dei campi o proprietà
- Modificare i tipi di campi o proprietà
- Modifica del nome della classe o dello spazio dei nomi

### <a name="data-contract-as-the-default-serializer"></a>Contratto di dati come serializzatore predefinito

Il serializzatore è in genere responsabile per la lettura dei dati e la deserializzazione nella versione corrente, anche se i dati in una versione precedente o *successiva* . Il serializzatore predefinito è [serializzatore dei contratti dati](https://msdn.microsoft.com/library/ms733127.aspx), quali sono le regole di controllo delle versioni ben definito. Insiemi affidabili consentono serializzatore l'override, ma gli operatori affidabile attualmente non. Serializzatore dati svolge un ruolo importante all'attivazione di aggiornamenti. Serializzatore dei contratti dati è il serializzatore è consigliabile per le applicazioni di servizio tessuti.


## <a name="how-the-data-format-affects-a-rolling-upgrade"></a>Effetti di un aggiornamento in sequenza il formato dei dati

Durante l'aggiornamento, esistono due scenari principali in cui il serializzatore può verificarsi una versione precedente o *successiva* dei dati:

1. Dopo un nodo viene aggiornato e viene avviato il backup, il nuovo serializzatore verrà caricato i dati che è stati mantenuti su disco nella versione precedente.
2. Durante l'aggiornamento in sequenza, il cluster conterrà una combinazione delle versioni precedenti e nuove del codice. Poiché repliche possono essere posizionate in diversi domini aggiornamento e repliche inviare i dati tra loro, la versione nuova o precedente dei dati può verificarsi con la versione nuova o precedente del serializzatore.

> [AZURE.NOTE] "La nuova versione" e "precedente" qui fare riferimento alla versione del codice che è in esecuzione. "Nuovo serializzatore" fa riferimento al codice serializzatore che è in esecuzione nella nuova versione dell'applicazione. "Nuovi dati" fa riferimento alla classe c# serializzata rispetto alla nuova versione dell'applicazione.

Due versioni del formato di codice e i dati devono essere avanti e indietro compatibili. Se non sono compatibili, potrebbe non riuscire l'aggiornamento in sequenza oppure dati potrebbero andare persi. L'aggiornamento potrebbe non riuscire perché il codice o serializzatore potrebbe generare un'eccezione eccezioni o un errore quando si verifica l'altra versione. Dati potrebbero essere persi se è stata aggiunta una nuova proprietà, ad esempio, ma il vecchio serializzatore ignora durante la deserializzazione.

Contratto di dati è la soluzione consigliata per garantire che i dati sono compatibili. Dispone di regole di controllo delle versioni ben definiti per l'aggiunta, rimozione e modifica di campi. Offre il supporto per la gestione dei campi sconosciuti, collegare al processo di serializzazione e deserializzazione e sulla gestione della classe. Per ulteriori informazioni, vedere [Uso contratto dati](https://msdn.microsoft.com/library/ms733127.aspx).


## <a name="next-steps"></a>Passaggi successivi

[Uprading l'applicazione mediante Visual Studio](service-fabric-application-upgrade-tutorial.md) fornisce un aggiornamento dell'applicazione utilizzando Visual Studio.

[Uprading l'applicazione di uso di Powershell](service-fabric-application-upgrade-tutorial-powershell.md) fornisce un aggiornamento dell'applicazione tramite PowerShell.

È possibile controllare come l'applicazione viene aggiornata utilizzando i [Parametri di aggiornamento](service-fabric-application-upgrade-parameters.md).

Informazioni su come usare la funzionalità avanzate durante l'aggiornamento dell'applicazione facendo riferimento a [Argomenti avanzati](service-fabric-application-upgrade-advanced.md).

Risolvere i problemi comuni in aggiornamenti dell'applicazione facendo riferimento alla procedura di [Risoluzione dei problemi di aggiornamento delle applicazioni ](service-fabric-application-upgrade-troubleshooting.md).
