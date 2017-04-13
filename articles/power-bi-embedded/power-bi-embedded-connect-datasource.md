<properties
   pageTitle="Incorporati di Microsoft Power BI - connessione a un'origine dati"
   description="Power BI incorporato, connettersi a origini dati"
   services="power-bi-embedded"
   documentationCenter=""
   authors="guyinacube"
   manager="erikre"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="10/04/2016"
   ms.author="asaxton"/>

# <a name="connect-to-a-data-source"></a>Connettersi a un'origine dati

Con **Power BI incorporato**, è possibile incorporare i report nell'applicazione. Quando si incorpora un report di Power BI in un'applicazione, il report si connette ai dati sottostanti **importando** una copia dei dati o una **connessione direttamente** alla fonte dati utilizzando **DirectQuery**.

Ecco le differenze nell'utilizzo di **importazione** / **DirectQuery**.

|Importazione | DirectQuery
|---|---
|Importare o copiate nel set di dati del report di tabelle, colonne *e i dati* . Per visualizzare le modifiche apportate ai dati sottostanti, aggiornare o importare un completa, corrente set di dati nuovamente.|Solo *le tabelle e colonne* sono importati o copiato nel set di dati del report. È sempre possibile visualizzare i dati più recenti.
Con Power BI incorporato, è possibile utilizzare DirectQuery con origini dati cloud ma non in origini dati locali al momento.

## <a name="benefits-of-using-directquery"></a>Vantaggi dell'utilizzo DirectQuery

Esistono due principali vantaggi quando si utilizza **DirectQuery**:

   -    **DirectQuery** consente di creare visualizzazioni sul set di dati molto grandi, in cui in caso contrario sarebbe Verbania da importare prima tutti i dati.
   -    Sottostante modifiche ai dati può richiedere un aggiornamento dei dati e per alcuni report, la necessità di visualizzare dati recenti può richiedere trasferimenti di dati di grandi dimensioni, rendere i dati reimportare Verbania. Se invece **DirectQuery** report sempre utilizzare dati corrente.

## <a name="limitations-of-directquery"></a>Limitazioni della DirectQuery

   Esistono alcune limitazioni all'uso di **DirectQuery**:

   -    Tutte le tabelle devono provenire da un singolo database.
   -    Se la query è troppo complessa, si verificherà un errore. Per risolvere l'errore è necessario refactoring la query in modo meno complesso. Se il quuery deve essere complesse, è necessario importare i dati invece di usare **DirectQuery**.
   -    Relazione tra il filtro è limitato a un'unica direzione, piuttosto che entrambe le direzioni.
   -    Non è possibile modificare il tipo di dati di una colonna.
   -    Per impostazione predefinita, vengono applicate le espressioni DAX consentite misure limitazioni. Vedere [DirectQuery e misure](#measures).

<a name="measures"/>
## <a name="directquery-and-measures"></a>Misure e DirectQuery

Per garantire query inviate all'origine dati sottostante prestazioni accettabili, sono limitazioni misure. Uso di **Power BI Desktop**, utenti avanzati quando possibile scegliere di ignorare questa limitazione scegliendo **File > Opzioni e le impostazioni > Opzioni**. Nella finestra di dialogo **Opzioni** , scegliere **DirectQuery**e selezionare l'opzione **Consenti misure senza restrizioni in modalità DirectQuery**. Quando l'opzione è selezionata, è possibile utilizzare qualsiasi espressione DAX valida per una misura. Gli utenti devono essere a conoscenza; Tuttavia, che alcune espressioni che eseguono ben quando i dati importati potrebbe causare nelle query molto lenta l'origine di back-end quando in modalità **DirectQuery** . 

## <a name="see-also"></a>Vedere anche
- [Introduzione a Microsoft Power BI incorporata](power-bi-embedded-get-started.md)
- [Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)
