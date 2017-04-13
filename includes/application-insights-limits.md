Esistono alcuni limiti al numero di eventi per ogni applicazione e metriche (ovvero, per una chiave strumentazione). 

Limiti dipendono i [prezzi livello](https://azure.microsoft.com/pricing/details/application-insights/) che si è scelto.

**Risorsa** | **Limite predefinito** | **Limite massimo**
-------- | ------------- | -------------
Punti di dati di sessione<sup>1, 2</sup> al mese | illimitato | 
Coordinate totale al mese per la richiesta, eventi, dipendenza, traccia, eccezione e visualizzazione pagina | 5 milioni | 50 milioni<sup>3</sup>
Tasso di dati di [traccia e registro](../articles/application-insights/app-insights-search-diagnostic-logs.md) | 200 dp/s | 500 dp/s
Tasso di dati di [eccezione](../articles/application-insights/app-insights-asp-net-exceptions.md) | dp 50/s | dp 50/s
Tasso di totale dei dati per la richiesta, eventi, dipendenza e telemetria visualizzazione pagina | 200 dp/s | 500 dp/s
Conservazione dei dati non elaborati per [la ricerca](../articles/application-insights/app-insights-diagnostic-search.md) e [Analitica](../articles/application-insights/app-insights-analytics.md) | 7 giorni
Conservazione dei dati aggregati per [explorer metriche](../articles/application-insights/app-insights-metrics-explorer.md) | 90 giorni
[Proprietà](../articles/application-insights/app-insights-api-custom-events-metrics.md#properties) nome conteggio | 100 |
Lunghezza del nome proprietà | 150 | 
Lunghezza del valore proprietà | 8192 | 
Lunghezza del messaggio di analisi e l'eccezione | 10000 |
[Unità di misura metriche](../articles/application-insights/app-insights-api-custom-events-metrics.md#properties) nome conteggio | 100 |
Lunghezza del nome metriche |  150 | 
[Verifica disponibilità](../articles/application-insights/app-insights-monitor-web-app-availability.md) | 10 | 

<sup>1</sup> un punto dati è un singolo valore metriche o un evento, con le proprietà associate e unità di misura.

<sup>2</sup> A sessione punto dati accede all'inizio o alla fine di una sessione e registra identità dell'utente.

<sup>3</sup> è possibile acquistare ulteriore capacità oltre 50 milioni.
 
[Sui prezzi e le quote di applicazione approfondimenti](../articles/application-insights/app-insights-pricing.md)
