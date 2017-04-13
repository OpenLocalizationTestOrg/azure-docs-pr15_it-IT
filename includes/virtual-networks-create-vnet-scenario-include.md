## <a name="scenario"></a>Scenario:

Per illustrare come creare un VNet e subnet, in questo documento utilizzerà scenario descritto di seguito.

![Scenario VNet](./media/virtual-networks-create-vnet-scenario-include/vnet-scenario.png)

In questo scenario si creerà un VNet denominato **TestVNet** con un blocco CIDR riservato di **192.168.0.0./16**. Il VNet conterrà subnet seguenti: 

- **Front-end**, utilizzando **192.168.1.0/24** come il blocco CIDR.
- **Back-end**, utilizzando **192.168.2.0/24** come il blocco CIDR.

 