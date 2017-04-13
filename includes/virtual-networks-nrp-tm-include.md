## <a name="traffic-manager-profile"></a>Profilo il traffico Manager

Gestore del traffico e la relativa risorsa endpoint figlio consentono di routing DNS per i punti finali in Azure e all'esterno di Azure. All'interno di tale distribuzione il traffico è disciplinata dai routing metodi di criteri. Gestore del traffico consente inoltre integrità endpoint da monitorare e il traffico deviato in modo appropriato in base allo stato di un endpoint. 

| Proprietà | Descrizione |
|---|---|
|**trafficRoutingMethod**| i valori possibili sono *le prestazioni*, *Weighted*e la *priorità* | 
| **dnsConfig** | Nome di dominio completo per il profilo | 
| **Protocollo** | monitoraggio protocollo, i valori possibili sono *HTTP* e *HTTPS*|
| **Porta** | monitoraggio porta |  
| **Percorso** | monitoraggio percorso |
| **Punti finali** |  contenitore per le risorse endpoint | 

### <a name="endpoint"></a>Punto finale 

Un endpoint rappresenta una risorsa figlio di un profilo il traffico Manager. Rappresenta un servizio o endpoint web a cui l'utente è distribuito il traffico in base a criteri configurati in risorsa profilo il traffico Manager. 

| Proprietà | Descrizione | 
|---|---| 
| **Tipo** |  il tipo dell'endpoint, i valori possibili sono *punto finale di Azure*, *Endpoint esterno*ed *Endpoint annidate* | 
| **targetResourceId** |  indirizzo IP pubblico di un endpoint del servizio o web. Può trattarsi di un estremo Azure o esterno. | 
| **Peso** | peso endpoint utilizzato nella gestione del traffico. | 
| **Priorità** | priorità dell'endpoint utilizzato per definire un'azione failover |

Esempio di gestione del traffico nel formato Json: 


        {
            "apiVersion": "[variables('tmApiVersion')]",
            "type": "Microsoft.Network/trafficManagerProfiles",
            "name": "VMEndpointExample",
            "location": "global",
            "dependsOn": [
                "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'), '0')]",
                "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'), '1')]",
                "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'), '2')]",
            ],
            "properties": {
                "profileStatus": "Enabled",
                "trafficRoutingMethod": "Weighted",
                "dnsConfig": {
                    "relativeName": "[parameters('dnsname')]",
                    "ttl": 30
                },
                "monitorConfig": {
                    "protocol": "http",
                    "port": 80,
                    "path": "/"
                },
                "endpoints": [
                    {
                        "name": "endpoint0",
                        "type": "Microsoft.Network/trafficManagerProfiles/azureEndpoints",
                        "properties": {
                            "targetResourceId": "[resourceId('Microsoft.Network/publicIPAddresses',concat(variables('publicIPAddressName'), 0))]",
                            "endpointStatus": "Enabled",
                            "weight": 1
                        }
                    },
                    {
                        "name": "endpoint1",
                        "type": "Microsoft.Network/trafficManagerProfiles/azureEndpoints",
                        "properties": {
                            "targetResourceId": "[resourceId('Microsoft.Network/publicIPAddresses',concat(variables('publicIPAddressName'), 1))]",
                            "endpointStatus": "Enabled",
                            "weight": 1
                        }
                    },
                    {
                        "name": "endpoint2",
                        "type": "Microsoft.Network/trafficManagerProfiles/azureEndpoints",
                        "properties": {
                            "targetResourceId": "[resourceId('Microsoft.Network/publicIPAddresses',concat(variables('publicIPAddressName'), 2))]",
                            "endpointStatus": "Enabled",
                            "weight": 1
                        }
                    }
                ]
            }
        }

 
## <a name="additional-resources"></a>Risorse aggiuntive

Per ulteriori informazioni, vedere [la documentazione API REST per il traffico Manager](https://msdn.microsoft.com/library/azure/mt163664.aspx) .
