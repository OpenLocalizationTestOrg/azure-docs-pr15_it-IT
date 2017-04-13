<properties
   pageTitle="Usare Power BI con SQL Data Warehouse | Microsoft Azure"
   description="Suggerimenti per l'uso di Power BI con Azure SQL Data Warehouse per lo sviluppo di soluzioni."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="05/31/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# <a name="use-power-bi-with-sql-data-warehouse"></a>Usare Power BI con SQL Data Warehouse
Come con Database di SQL Azure, SQL dati Warehouse diretto connettersi consente di sfruttare le potente impostare la logica insieme a funzioni analitiche di Power BI.  Con connessione diretta query vengono inviate al Data Warehouse SQL Azure in tempo reale mentre si esplorare i dati.  Questa operazione, combinati con la scala del SQL Data Warehouse, consente agli utenti di creare report dinamici in minuti per terabyte di dati.  Inoltre, l'introduzione del pulsante Apri in Power BI consente agli utenti di connettersi direttamente Power BI per SQL Data Warehouse senza raccogliere informazioni da altre parti di Azure.

Quando si Usa connessione diretta, Nota:

+ Specificare il nome completo del server per la connessione (vedere di seguito per altri dettagli)
+ Verificare le regole firewall per il database siano configurate su "Consenti l'accesso ai servizi Azure".
+ Per ogni azione, ad esempio selezionando una colonna o l'aggiunta di un filtro direttamente richiederà data warehouse
+ Riquadri vengono aggiornati circa 15 minuti (aggiornamento non devono essere pianificate)
+ Domande e risposte non è disponibile per la connessione diretta set di dati
+ Le modifiche allo schema non sono selezionate automaticamente
+ Tutte le query di connessione diretta saranno dopo 2 minuti

Queste limitazioni e le note possono cambiare per continuare a migliorare l'esperienza. Procedura per la connessione è riportata di seguito.  

## <a name="using-the-open-in-power-bi-button"></a>Utilizzo del pulsante 'Apri ripiegato'
Il modo più semplice per spostarsi tra i SQL Data Warehouse e Power BI è con il pulsante Apri in Power BI. Questo pulsante consente di facile iniziare a creare nuovi dashboard in Power BI.  

1.  Iniziare a passare all'istanza di SQL Data Warehouse nel portale classica di Azure.
2.  Fare clic sul pulsante 'Apri ripiegato'.
3.  Se non si riesce più ad accedere direttamente oppure se non si dispone di un account di Power BI, è necessario effettuare l'accesso.  
4.  Si verrà indirizzati a della pagina di connessione SQL Data Warehouse con le informazioni da SQL Data Warehouse prepopolata.
5.  Dopo aver immesso le credenziali verrà completamente connesso il Data Warehouse SQL.

## <a name="connecting-through-the-power-bi-portal"></a>La connessione tramite il portale di Power BI
Oltre a utilizzare il pulsante Apri in Power BI, gli utenti possono inoltre connettersi SQL Data Warehouse tramite il portale di Power BI.

1.  Fare clic su 'Dati' nella parte inferiore del riquadro di spostamento.
2.  Selezionare 'Databases'.
3.  Una volta nella pagina database selezionare "Warehouse dati di SQL Azure" e quindi fare clic su "Connetti".
4.  Immettere le informazioni di connessione necessarie.  Il nome del server e database sono disponibili nel portale di Azure.
5.  Si accede alla pagina principale di Power BI e dopo la connessione è una chiamata effettuata una nuova voce in 'set di dati con il nome dell'istanza.  
6.   È possibile fare clic sul nuovo set di dati per esplorare tutte le tabelle e viste nel database. Selezionando una colonna invierà una query all'origine, in modo dinamico la creazione della visualizzazione. Questi elementi visivi possono essere salvati in un nuovo report e aggiunte tornare al dashboard.

<!--Image references-->

<!--Article references-->
[SQL Data Warehouse development overview]:  ./sql-data-warehouse-overview-develop/
[SQL Data Warehouse integration overview]:  ./sql-data-warehouse-overview-integration/

<!--MSDN references-->

<!--Other Web references-->
