<properties
   pageTitle="Gestire le capacità di calcolo in SQL Azure Data Warehouse (portal Azure) | Microsoft Azure"
   description="Azure attività portale per la gestione calcolare power. Risorse di elaborazione scala regolando DWUs. In alternativa, sospendere e riprendere calcolare le risorse per risparmiare sui costi."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/22/2016"
   ms.author="barbkess;sonyama"/>

# <a name="manage-compute-power-in-azure-sql-data-warehouse-azure-portal"></a>Gestire le capacità di calcolo in SQL Azure Data Warehouse (portal Azure)

> [AZURE.SELECTOR]
- [Panoramica](sql-data-warehouse-manage-compute-overview.md)
- [Portale](sql-data-warehouse-manage-compute-portal.md)
- [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
- [RESTO](sql-data-warehouse-manage-compute-rest-api.md)
- [TSQL](sql-data-warehouse-manage-compute-tsql.md)


Scala da scalabilità per calcolare le risorse e la memoria per soddisfare le esigenze del carico di lavoro. Risparmiare sui costi risorse posteriore proporzioni durante l'elaborazione sospensione o non periodi completamente. 

Insieme di attività utilizza il portale Azure per:

- Elaborazione di scala
- Calcolo pausa
- Calcolo curriculum

Per ulteriori informazioni, vedere [gestire calcolare Panoramica][].

<a name="scale-performance-bk"></a>
<a name="scale-compute-bk"></a>

## <a name="scale-compute-power"></a>Scala grande potenza

[AZURE.INCLUDE [SQL Data Warehouse scale DWUs description](../../includes/sql-data-warehouse-scale-dwus-description.md)]

Per modificare le risorse di elaborazione:

1. Aprire il [portale di Azure][], aprire il database e fare clic su **scala**.

    ![Fare clic su scala][1]

1. In e il ridimensionamento, spostare il dispositivo di scorrimento verso sinistra o destra per modificare l'impostazione DWU.

    ![Spostare il dispositivo di scorrimento][2]

1. Fare clic su **Salva**. Viene visualizzato un messaggio di conferma. Fare clic su **Sì** per confermare o **Nessun** per annullare.

    ![Fare clic su Salva][3]

<a name="pause-compute-bk"></a>

## <a name="pause-compute"></a>Calcolo pausa

[AZURE.INCLUDE [SQL Data Warehouse pause description](../../includes/sql-data-warehouse-pause-description.md)]

Per sospendere un database:

1. Aprire il [portale di Azure][] e aprire il database. Si noti che lo stato **Online**. 

    ![Stato online][6]

1. Per sospendere l'elaborazione e della memoria dalle risorse, fare clic su **Pausa**e un messaggio di conferma visualizzato. Fare clic su **Sì** per confermare o **Nessun** per annullare.

    ![Confermare pausa][7]

1. Durante l'avvio del database di SQL Data Warehouse, stato è impostato su **sospensione**.
2. Quando lo stato è **sospeso**, viene eseguita l'operazione di pausa e non siano state addebitate per DWUs.

    ![Stato di pausa][4]

<a name="resume-compute-bk"></a>

## <a name="resume-compute"></a>Calcolo curriculum

[AZURE.INCLUDE [SQL Data Warehouse resume description](../../includes/sql-data-warehouse-resume-description.md)]Per riprendere a un database:

1. Aprire il [portale di Azure][] e aprire il database. Si noti che lo stato **in pausa**. 

    ![Database pausa][4]

1. Per riprendere il database, fare clic su **Start**e quindi viene visualizzato un messaggio di conferma. Fare clic su **Sì** per confermare o **Nessun** per annullare.

    ![Confermare curriculum][5]

1. Durante l'avvio del database di SQL Data Warehouse, lo stato è "Ripresa dell'esecuzione".
2. Quando lo stato è **in linea**, il database è pronto.

    ![Stato online][6]

<a name="next-steps-bk"></a>

## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni, vedere [Panoramica di gestione][].

<!--Image references-->
[1]: ./media/sql-data-warehouse-manage-compute-portal/click-scale.png
[2]: ./media/sql-data-warehouse-manage-compute-portal/move-slider.png
[3]: ./media/sql-data-warehouse-manage-compute-portal/click-save.png
[4]: ./media/sql-data-warehouse-manage-compute-portal/resume-database.png
[5]: ./media/sql-data-warehouse-manage-compute-portal/resume-confirm.png
[6]: ./media/sql-data-warehouse-manage-compute-portal/pause-database.png
[7]: ./media/sql-data-warehouse-manage-compute-portal/pause-confirm.png

<!--Article references-->
[Panoramica della gestione]: ./sql-data-warehouse-overview-manage.md
[Gestire le informazioni generali di calcolo]: ./sql-data-warehouse-manage-compute-overview.md

<!--MSDN references-->


<!--Other Web references-->

[Portale di Azure]: http://portal.azure.com/
