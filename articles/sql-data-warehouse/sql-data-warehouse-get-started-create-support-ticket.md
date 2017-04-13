<properties
   pageTitle="Come creare un ticket di supporto per SQL Data Warehouse | Microsoft Azure"
   description="Come creare un ticket di supporto in Warehouse di dati di SQL Azure."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/01/2016"
   ms.author="sonyama;barbkess"/>

# <a name="how-to-create-a-support-ticket-for-sql-data-warehouse"></a>Come creare un ticket di supporto per SQL Data Warehouse
 
Se si verificano problemi con la Data Warehouse SQL, creare un supporto ticket in modo che il nostro team engineering possono aiutare a.

## <a name="create-a-support-ticket"></a>Creare un ticket di supporto

1. Aprire il [portale di Azure][].

2. Nella schermata iniziale fare clic sul riquadro **della Guida + supporto** .

    ![Guida + supporto](./media/sql-data-warehouse-get-started-create-support-ticket/help-support.png)

3. Nella Guida di + blade di supporto, fare clic su **Crea richiesta di assistenza**.

    ![Nuova richiesta di assistenza](./media/sql-data-warehouse-get-started-create-support-ticket/create-support-request.png)
    
    <a name="request-quota-change"></a> 

4. Selezionare il **tipo di richiesta**.

    ![Tipo di richiesta](./media/sql-data-warehouse-get-started-create-support-ticket/request-type.png)
    
    >[AZURE.NOTE]  Per impostazione predefinita, ogni SQL server (ad esempio myserver.database.windows.net) dispone di una **Quota DTU** 45,000. La quota è semplicemente un limite di sicurezza. È possibile aumentare la quota di creazione di un ticket di supporto e selezionando *la Quota* come tipo di richiesta. Per calcolare le DTU esigenze, moltiplicare il 7.5 totale [che DWU][] necessari. Ad esempio, si vuole ospitare due DW6000s su un server SQL server, quindi è consigliabile richiedere una quota DTU 90.000.  È possibile visualizzare il consumo DTU corrente da e il server SQL nel portale. Database in pausa e riavviate contare verso la quota DTU. 

5. Selezionare l' **abbonamento** che ospita i database con il problema che si desidera segnalare.

    ![Abbonamento](./media/sql-data-warehouse-get-started-create-support-ticket/subscription.png)

6. Selezionare **SQL Data Warehouse** come risorsa.

    ![Risorsa](./media/sql-data-warehouse-get-started-create-support-ticket/resource.png)

7. Selezionare il [piano di supporto di Azure][].

    - Supporto per **la fatturazione, la gestione della quota e sottoscrizione** è disponibile in tutti i livelli di supporto.
    - **Supporto per** viene fornito tramite [sviluppo][], [Standard][], [Professional diretta][] o supporto [Premier][] . Interruzione Correggi i problemi sono i problemi riscontrati dai clienti durante l'uso di Azure dove non c'è prevede che Microsoft ha causato il problema.
    - **Servizi di consulenza** e **formazione di sviluppo** sono disponibili i livelli di supporto [Premier][] e [Professional diretto][] . 
    
    Se si dispone di un Premier supportare piano, è inoltre possibile segnalare SQL Data Warehouse problemi nel [portale online Microsoft Premier][].  Per ulteriori informazioni sui diversi piani di supporto, inclusi ambito, i tempi di risposta, prezzi e così via, vedere [Azure supporta i piani di][Azure supportare piano] .  Per le domande frequenti su Azure supporto, vedere [Azure supportano domande frequenti][].  

    ![Piano di supporto](./media/sql-data-warehouse-get-started-create-support-ticket/support-plan.png)

8. Selezionare il **tipo di problema** e la **categoria**.

    ![Categoria tipo di problema](./media/sql-data-warehouse-get-started-create-support-ticket/problem-type-category.png)

9. Descrivere il problema e scegliere il livello di impatto per l'azienda.

    ![Descrizione del problema](./media/sql-data-warehouse-get-started-create-support-ticket/problem-description.png)

10. Le **informazioni di contatto** per il ticket di supporto saranno precompilate. Aggiornare questa operazione, se necessario.

    ![Informazioni di contatto](./media/sql-data-warehouse-get-started-create-support-ticket/contact-info.png)

11. Fare clic su **Crea** per inviare la richiesta di assistenza.


## <a name="monitor-a-support-ticket"></a>Monitorare i ticket di supporto

Dopo che è stata inviata la richiesta di assistenza, si verrà contattati il team di supporto Azure. Per verificare lo stato di richiesta e le informazioni dettagliate, fare clic su **Gestisci le richieste di assistenza** nel dashboard.

![Controllare lo stato](./media/sql-data-warehouse-get-started-create-support-ticket/check-status.png)

## <a name="other-resources"></a>Altre risorse

Inoltre, è possibile connettersi con la community di SQL Data Warehouse [Overflow dello Stack][] o nel [forum su MSDN Warehouse dati di SQL Azure][].

<!--Image references--> 

<!--Article references--> 
[DWU]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units

<!--MSDN references--> 

<!--Other web references--> 
[Portale di Azure]: https://portal.azure.com/
[Piano di supporto Azure]: https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/  
[Per sviluppatori]: https://azure.microsoft.com/support/plans/developer/  
[Standard]: https://azure.microsoft.com/support/plans/standard/  
[Pubblicitario professionale]: https://azure.microsoft.com/support/plans/prodirect/  
[Premier]: https://azure.microsoft.com/support/plans/premier/  
[Domande frequenti supporto Azure]: https://azure.microsoft.com/support/faq/
[Portale online Microsoft Premier]: https://premier.microsoft.com/
[Overflow dello stack]: https://stackoverflow.com/questions/tagged/azure-sqldw/
[Forum MSDN Warehouse dati di SQL Azure]: https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse/

