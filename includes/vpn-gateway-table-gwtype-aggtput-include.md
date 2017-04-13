|    | **Produttività Gateway VPN (1)** | **Gateway VPN IPsec max tunnel (2)** | **Velocità ExpressRoute Gateway** | **Gateway VPN ed ExpressRoute coesistere**|
|--- |----------------------------|-----------------------------------|-------------------------------------|-----------------------------------------|
| **SKU di base (3)(5)**              |  100 Mbps | 10                         |  500 Mbps                           | No   |
| **SKU standard (4)(5)**           |  100 Mbps | 10                         | 1000 Mbps                           | Sì  |
| **Prestazioni elevate SKU (4)**   | 200 Mbps  | 30                         | 2000 Mbps                           | Sì  |

- (1) la velocità VPN è una stima approssimativa in base alle misure tra VNets nella stessa regione Azure. Non è una velocità garantito per le connessioni tra locale in Internet. È la misura massima produttività possibili.
- (2) il numero di tunnel fare riferimento alla RouteBased VPN. PolicyBased VPN supportino solo un tunnel VPN da sito.
- (3) BGP non è supportato per lo SKU di base.
- (4) le reti private virtuali PolicyBased non sono supportati per la SKU. Sono supportati per codice SKU base.
- (5) le connessioni attivo S2S VPN Gateway non sono supportate per la SKU. Attivo attivo è supportato in solo lo SKU HighPerformance.