- Le reti virtuali possono essere nelle stesse o in altre Azure regioni (posizioni).

- Un servizio cloud o endpoint di bilanciamento del carico non interessano le reti virtuali, anche se sono collegati tra loro.

- Connessione tra loro più reti virtuali Azure non richiede tutti i gateway VPN locale a meno che non è necessaria una connessione tra locale.

- VNet-VNet supporta la connessione di reti virtuale. Non supporta la connessione macchine virtuali o cloud services non in una rete virtuale.

- VNet-VNet richiede gateway VPN Azure con RouteBased (già noto come Routing dinamico) tipi di VPN. 

- La connettività di rete virtuale può essere utilizzata contemporaneamente con più siti VPN, con un massimo di 10 (gateway predefinito/Standard) o 30 (elevato prestazioni gateway) VPN tunnel per un gateway VPN virtuali la connessione a uno dei due altre reti virtuali o siti locale.

- Gli spazi di indirizzi dei siti di rete locale virtuali reti e locale devono non si sovrappongono. Spazi degli indirizzi sovrapposti impedirà la creazione di connessioni VNet-VNet l'esito negativo.

- Tunnel ridondanti tra una coppia di reti virtuali non sono supportati.

- Tutti i tunnel VPN della rete virtuale condividono la larghezza di banda disponibile su gateway VPN Azure e la stessa VPN gateway tempi di attività contratto di servizio in Azure.

- Il traffico VNet-VNet attraversano Microsoft Network, non a Internet.

- Il traffico VNet a VNet all'interno dell'area stessa è gratuito per entrambe le direzioni; Cross area VNet-VNet uscita il traffico viene addebitato con la velocità di trasferimento in uscita tra VNet in base alle aree di origine. I [prezzi pagina](https://azure.microsoft.com/pricing/details/vpn-gateway/) per informazioni dettagliate, vedere.