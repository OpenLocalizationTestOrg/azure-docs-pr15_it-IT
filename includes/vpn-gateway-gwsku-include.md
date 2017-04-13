Quando si crea un gateway virtuali, è necessario specificare il gateway SKU che si desidera utilizzare. Quando si seleziona un gateway superiore SKU, ulteriori CPU e della larghezza di banda di rete vengono assegnati a gateway e di conseguenza, il gateway supportino maggiore velocità di rete per la rete virtuale.

Gateway VPN è possibile utilizzare i seguenti SKU:

- Base
- Standard
- HighPerformance

Quando si selezionano uno SKU, tenere presente quanto segue:

- Se si desidera utilizzare un tipo di PolicyBased VPN, è necessario utilizzare lo SKU di base. VPN PolicyBased (già noto come Routing statico) non sono supportate in altri SKU.
- BGP non è supportato in base SKU.
- Gateway VPN ExpressRoute coesistere configurazioni non sono supportate in base SKU.
- Connessioni Gateway VPN S2S attivo attivo possono essere configurate su solo lo SKU HighPerformance.
