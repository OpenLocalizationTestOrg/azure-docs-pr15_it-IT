<properties
    pageTitle="Configurare i metodi di routing gestore del traffico | Microsoft Azure"
    description="In questo articolo viene spiegato come configura in gestore del traffico diversi metodi di routing"
    services="traffic-manager"
    documentationCenter=""
    authors="sdwheeler"
    manager="carmonm"
    editor=""
/>
<tags
    ms.service="traffic-manager"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
   ms.date="10/18/2016"
   ms.author="sewhee" />
<!-- repub for nofollow -->

# <a name="configure-traffic-manager-routing-methods"></a>Configurare il traffico Manager metodi di routing

Gestore del traffico Azure include tre metodi di routing che consentono di controllare la modalità del traffico verso endpoint del servizio disponibili. Il metodo di routing del traffico viene applicato a ogni query DNS ricevuto per determinare quali endpoint da restituire nella risposta DNS.

Sono disponibili tre metodi di routing il traffico in gestore del traffico:

- **Priorità:** Selezionare 'Priority' quando si desidera utilizzare un endpoint del servizio principale e fornire backup nel caso in cui il principale non è disponibile.
- **Ponderato:** Selezionare 'ponderato"quando si desidera distribuire il traffico su un set di punti finali, in modo uniforme o in base a fattori, quali si definire.
- **Prestazioni:** Selezionare 'Prestazioni' dopo avere endpoint in diverse aree geografiche e si desidera agli utenti finali di utilizzare l'endpoint "più vicina" in termini della latenza di rete più bassa.

## <a name="configure-priority-routing-method"></a>Configurare metodo routing priorità

Indipendentemente dalla modalità di sito Web, siti Web di Azure già funzionalità failover per i siti Web all'interno di un Data Center (noto anche come area). Gestore del traffico fornisce failover per i siti Web nei data center diverso.

Un modello comune per failover del servizio è per inviare il traffico a un servizio principale e forniscono un set di servizi di backup identici per failover. La procedura seguente viene illustrato come configurare questa failover priorità con siti Web e servizi cloud Azure:

1. Nel portale di classica Azure, nel riquadro sinistro fare clic sull'icona di **Gestore del traffico** per aprire il riquadro di gestore del traffico.
2. Nel riquadro di gestione di traffico nel portale di classica Azure, individuare il profilo di gestore del traffico che contiene le impostazioni che si desidera modificare. Per aprire la pagina di impostazioni del profilo, fare clic sulla freccia a destra del nome del profilo.
3. Nella pagina del profilo, fare clic su **endpoint** nella parte superiore della pagina. Verificare che siano presenti servizi cloud e siti Web che si desidera includere nella configurazione.
4. Nella parte superiore per aprire la pagina di configurazione, fare clic su **Configura** .
5. Per **le impostazioni del metodo instradare il traffico**, verificare che il metodo di routing il traffico **Failover**. In caso contrario, fare clic su **Failover** nell'elenco a discesa.
6. Per **Nell'elenco priorità Failover**, modificare l'ordine di failover per gli endpoint. Quando si seleziona il metodo di routing il traffico **Failover** , è importante l'ordine di endpoint selezionato. L'endpoint principale è in primo piano. Utilizzare la freccia su e freccia giù per cambiare l'ordine in base alle esigenze. Per informazioni su come impostare la priorità di failover mediante Windows PowerShell, vedere [Set-AzureTrafficManagerProfile](http://go.microsoft.com/fwlink/p/?LinkId=400880).
7. Verificare che le **Impostazioni di monitoraggio** sono configurate in modo appropriato. Monitoraggio garantisce che gli endpoint attualmente offline non vengono inviati il traffico. Per controllare i punti finali, è necessario specificare un percorso e il nome. Segno di divisione "/" è un valore valido per il relativo percorso e implica che il file sia nella directory principale (impostazione predefinita).
8. Dopo aver completato le modifiche di configurazione, fare clic su **Salva** nella parte inferiore della pagina.
9. Verificare le modifiche nella configurazione.
10. Dopo il profilo di gestore del traffico funziona correttamente, modificare i record DNS per il server DNS autorevole in modo che puntino il nome di dominio della società al nome di dominio gestore del traffico.

## <a name="configure-weighted-routing-method"></a>Configurare il metodo di routing Media ponderato

Un modello di metodo instradare il traffico comune consiste nel fornire un insieme di endpoint identici, che includono servizi cloud e siti Web, e inviare il traffico a ognuna in una circolare. La procedura seguente viene illustrato come configurare questo tipo di metodo instradare il traffico.

>[AZURE.NOTE] Siti Web Azure già bilanciamento circolari funzionalità per i siti Web all'interno di un Data Center (noto anche come area). Gestore del traffico consente di specificare il traffico circolari metodo routing per i siti Web nei data center diversi.

1. Nel portale di classica Azure, nel riquadro sinistro fare clic sull'icona di **Gestore del traffico** per aprire il riquadro di gestore del traffico.
2. Nel riquadro di traffico Manager, individuare il profilo di gestore del traffico che contiene le impostazioni che si desidera modificare. Per aprire la pagina di impostazioni del profilo, fare clic sulla freccia a destra del nome del profilo.
3. Nella pagina per il proprio profilo, fare clic su **endpoint** nella parte superiore della pagina e verificare che siano presenti gli endpoint del servizio che si desidera includere nella configurazione.
4. Nella pagina del profilo, fare clic su **Configura** nella parte superiore per aprire la pagina di configurazione.
5. Per **il traffico routing metodo impostazioni**, verificare che il metodo di routing il traffico **circolari**. In caso contrario, fare clic su **circolari** nell'elenco a discesa.
6. Verificare che le **Impostazioni di monitoraggio** sono configurate in modo appropriato. Monitoraggio garantisce che gli endpoint attualmente offline non vengono inviati il traffico. Per controllare i punti finali, è necessario specificare un percorso e il nome. Segno di divisione "/" è un valore valido per il relativo percorso e implica che il file sia nella directory principale (impostazione predefinita).
7. Dopo aver completato le modifiche di configurazione, fare clic su **Salva** nella parte inferiore della pagina.
8. Verificare le modifiche nella configurazione.
9. Dopo il profilo di gestore del traffico funziona correttamente, modificare i record DNS per il server DNS autorevole in modo che puntino il nome di dominio della società al nome di dominio gestore del traffico.

## <a name="configure-performance-traffic-routing-method"></a>Configurare le prestazioni del traffico routing metodo

Il metodo di routing il traffico prestazioni consente di indirizzare il traffico all'endpoint con latenza minima dalla rete del cliente. Data Center con latenza minima è in genere, la più vicina distanza geografica. Questo metodo di routing il traffico non è possibile includere le modifiche in tempo reale in configurazione di rete o si carica.

1. Nel portale di classica Azure, nel riquadro sinistro fare clic sull'icona di **Gestore del traffico** per aprire il riquadro di gestore del traffico.
2. Nel riquadro di traffico Manager, individuare il profilo di gestore del traffico che contiene le impostazioni che si desidera modificare. Per aprire la pagina di impostazioni del profilo, fare clic sulla freccia a destra del nome del profilo.
3. Nella pagina per il proprio profilo, fare clic su **endpoint** nella parte superiore della pagina e verificare che siano presenti gli endpoint del servizio che si desidera includere nella configurazione.
4. Nella pagina per il proprio profilo, fare clic su **Configura** nella parte superiore per aprire la pagina di configurazione.
5. Per **le impostazioni del metodo instradare il traffico**, verificare che il metodo di routing il traffico * *prestazioni*. In caso contrario, fare clic su * *prestazioni** nell'elenco a discesa.
6. Verificare che le **Impostazioni di monitoraggio** sono configurate in modo appropriato. Monitoraggio garantisce che gli endpoint attualmente offline non vengono inviati il traffico. Per controllare i punti finali, è necessario specificare un percorso e il nome. Segno di divisione "/" è un valore valido per il relativo percorso e implica che il file sia nella directory principale (impostazione predefinita).
7. Dopo aver completato le modifiche di configurazione, fare clic su **Salva** nella parte inferiore della pagina.
8. Verificare le modifiche nella configurazione.
9. Dopo il profilo di gestore del traffico funziona correttamente, modificare i record DNS per il server DNS autorevole in modo che puntino il nome di dominio della società al nome di dominio gestore del traffico.

## <a name="next-steps"></a>Passaggi successivi

* [Gestire i profili di gestore del traffico](traffic-manager-manage-profiles.md)
* [Metodi di routing il traffico Manager](traffic-manager-routing-methods.md)
* [Verifica impostazioni di gestione del traffico](traffic-manager-testing-settings.md)
* [Scegliere un dominio Internet della società a un dominio il traffico Manager](traffic-manager-point-internet-domain.md)
* [Gestire i punti finali gestore del traffico](traffic-manager-manage-endpoints.md)
* [Risoluzione dei problemi gestore del traffico ridotto di stato](traffic-manager-troubleshooting-degraded.md)
