<properties
   pageTitle="Ripristinare un Gateway VPN Azure | Microsoft Azure"
   description="In questo articolo illustra la reimpostazione del Gateway VPN Azure. L'articolo si applica a gateway VPN in visualizzazione classica e i modelli di distribuzione di Manager delle risorse."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager,azure-service-management"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/23/2016"
   ms.author="cherylmc"/>

# <a name="reset-an-azure-vpn-gateway-using-powershell"></a>Ripristinare un Gateway VPN Azure tramite PowerShell


In questo articolo illustra la reimpostazione del Gateway VPN Azure uso dei cmdlet di PowerShell. Queste istruzioni includono il modello di distribuzione classica e il modello di distribuzione di Manager delle risorse.

Reimpostare il gateway VPN Azure è utile se si perde la connettività VPN tra locale in uno o più tunnel S2S VPN. In questo caso, i dispositivi VPN locale tutti funzionano correttamente, ma non sono in grado di stabilire tunnel IPsec con gateway VPN Azure. 

Ogni gateway VPN Azure è composto da due istanze di macchine Virtuali in esecuzione in una configurazione standby attivo. Quando si utilizza il cmdlet di PowerShell per reimpostare il gateway, si riavvia il gateway e quindi applicare nuovamente le configurazioni tra locale a tale. Il gateway mantiene l'indirizzo IP pubblico che ha già. Di conseguenza, che non sarà necessario aggiornare la configurazione di router VPN con un nuovo indirizzo IP pubblico gateway VPN Azure.  

Dopo il comando, l'istanza corrente attiva del gateway VPN Azure riavvio del immediatamente. Si verificherà un breve ritardo durante il failover dall'istanza attivo (in corso il riavvio), per l'istanza standby. La distanza deve essere inferiore a un minuto.

Se la connessione non viene ripristinata dopo il primo riavvio, eseguire lo stesso comando nuovo per riavviare la seconda istanza di macchine Virtuali (nuovo attiva gateway). Se il due riavvio è richieste per tornare indietro, si verificherà un periodo più lungo leggermente nel punto in cui sono riavviate entrambe le istanze di macchine Virtuali (attive e standby). In questo modo un ritardo più in connettività VPN, fino a 2 a 4 minuti per le macchine virtuali completare il riavvio.

Dopo il due riavvio, se si verificano ancora problemi di connettività tra locale, aprire una richiesta di assistenza dal portale di Azure.

## <a name="before-you-begin"></a>Prima di iniziare

Prima di ripristinare il gateway, verificare gli elementi chiavi elencati di seguito per ogni tunnel VPN IPsec a siti (S2S). Qualsiasi mancata corrispondenza nei campi determinerà la disconnessione di tunnel VPN S2S. Verificare e correggere le configurazioni per locale e gateway VPN Azure evita il riavvio non necessarie di interruzioni per le altre connessioni di lavorativo gateway.

Prima di reimpostare il gateway, verificare quanto segue:

- Gli indirizzi IP Internet (VIP) per il gateway VPN Azure e il gateway VPN locale sono configurati correttamente il Azure e i criteri VPN locale.
- La chiave deve essere la stessa gateway VPN Azure e locale.
- Se si applica configurazione IPsec/IKE specifica, ad esempio la crittografia, gli algoritmi e questa opzione (perfetto inoltra segreto), assicurarsi che sia Azure e locale gateway VPN ha le stesse configurazioni.

## <a name="reset-a-vpn-gateway-using-the-resource-management-deployment-model"></a>Ripristinare un Gateway VPN mediante il modello di distribuzione di gestione delle risorse

Il cmdlet Gestione risorse di PowerShell per la reimpostazione gateway è `Reset-AzureRmVirtualNetworkGateway`. Nell'esempio seguente Reimposta gateway VPN Azure "VNet1GW", "TestRG1" gruppo di risorse.

    $gw = Get-AzureRmVirtualNetworkGateway -Name VNet1GW -ResourceGroup TestRG1
    Reset-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw

## <a name="reset-a-vpn-gateway-using-the-classic-deployment-model"></a>Ripristinare un Gateway VPN mediante il modello di distribuzione classica

Cmdlet di PowerShell per la reimpostazione gateway VPN Azure è `Reset-AzureVNetGateway`. Nell'esempio seguente Reimposta il gateway VPN Azure per la rete virtuale denominato "ContosoVNet".
 
    Reset-AzureVNetGateway –VnetName “ContosoVNet” 

Risultato:

    Error          :
    HttpStatusCode : OK
    Id             : f1600632-c819-4b2f-ac0e-f4126bec1ff8
    Status         : Successful
    RequestId      : 9ca273de2c4d01e986480ce1ffa4d6d9
    StatusCode     : OK


## <a name="next-steps"></a>Passaggi successivi
    
Vedere il [riferimento sui cmdlet di gestione dei servizi di PowerShell](https://msdn.microsoft.com/library/azure/mt617104.aspx) e [di riferimento sui cmdlet Gestione risorse di PowerShell](http://go.microsoft.com/fwlink/?LinkId=828732) per altre informazioni.






