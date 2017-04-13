## <a name="virtual-network"></a>Rete virtuale
Risorse virtuali reti (VNET) e subnet consentono di definire un limite di protezione per carichi di lavoro in esecuzione in Azure. Un VNet è caratterizzato da una raccolta di spazi indirizzo, definito come blocchi CIDR. 

>[AZURE.NOTE] Gli amministratori di rete hanno familiari con la notazione CIDR. Se non conoscono CIDR, [informazioni su ulteriori informazioni](http://whatismyipaddress.com/cidr).

![VNet con più subnet](./media/resource-groups-networking/Figure4.png)

VNets contengono le proprietà seguenti.

|Proprietà|Descrizione|Valori di esempio|
|---|---|---|
|**addressSpace**|Insieme di prefissi di indirizzi che compongono la VNet in notazione CIDR|192.168.0.0/16|
|**subnet**|Insieme di subnet che costituiscono il VNet|vedere [subnet](#Subnets) .|
|**indirizzo IP**|Indirizzo IP assegnata a un oggetto. Si tratta di una proprietà di sola lettura.|104.42.233.77|

### <a name="subnets"></a>Subnet
Una subnet è una risorsa figlio di un VNet e consente di definiscono segmenti di spazi di indirizzi in un blocco CIDR utilizzando prefissi degli indirizzi IP. NIC possono essere aggiunto alla subnet e connessi a macchine virtuali, fornire la connettività per diversi carichi di lavoro.

Subnet contengono le proprietà seguenti. 

|Proprietà|Descrizione|Valori di esempio|
|---|---|---|
|**addressPrefix**|Prefisso singolo indirizzo che costituiscono la subnet in notazione CIDR|192.168.1.0/24|
|**networkSecurityGroup**|NSG applicati alla subnet|vedere [NSGs](#Network-Security-Group)|
|**routeTable**|Tabella di routing applicata alla subnet|vedere [UDR](#Route-table)|
|**ipConfigurations**|Insieme di oggetti di configurazione IP usati da NIC connessa alla subnet|vedere [UDR](#Route-table)|


VNet di esempio in formato JSON:

    {
        "name": "TestVNet",
        "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet",
        "etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
        "type": "Microsoft.Network/virtualNetworks",
        "location": "westus",
        "tags": {
            "displayName": "VNet"
        },
        "properties": {
            "provisioningState": "Succeeded",
            "resourceGuid": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
            "addressSpace": {
                "addressPrefixes": [
                    "192.168.0.0/16"
                ]
            },
            "subnets": [
                {
                    "name": "FrontEnd",
                    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                    "etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
                    "properties": {
                        "provisioningState": "Succeeded",
                        "addressPrefix": "192.168.1.0/24",
                        "networkSecurityGroup": {
                            "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-BackEnd"
                        },
                        "routeTable": {
                            "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/routeTables/UDR-FrontEnd"
                        },
                        "ipConfigurations": [
                            {
                                "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICWEB1/ipConfigurations/ipconfig1"
                            },
                            ...]
                    }
                },
                ...]
        }
    }

### <a name="additional-resources"></a>Risorse aggiuntive

- È possibile ottenere ulteriori informazioni su [VNet](../articles/virtual-network/virtual-networks-overview.md).
- Leggere la [documentazione di riferimento all'API REST](https://msdn.microsoft.com/library/azure/mt163650.aspx) per VNets.
- Leggere la [documentazione di riferimento all'API REST](https://msdn.microsoft.com/library/azure/mt163618.aspx) per subnet.