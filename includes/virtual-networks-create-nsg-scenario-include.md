## <a name="scenario"></a>Scenario:

Per illustrare come creare NSGs, questo documento utilizzerà scenario descritto di seguito.

![Scenario VNet](./media/virtual-networks-create-nsg-scenario-include/figure1.png)

In questo scenario si creerà un NSG per ogni subnet nella rete virtuale **TestVNet** , come descritto di seguito: 

- **Front-end NSG**. Front-end NSG verrà applicato alla subnet *front-end* e contenere due regole:  
    - **regola di rdp**. Questa regola consentirà il traffico RDP alla subnet *front-end* .
    - **regola di web**. Questa regola consentirà il traffico HTTP alla subnet *front-end* .
- **NSG-back-end**. Back-end NSG verrà applicato alla subnet *back-end* e contenere due regole: 
    - **regola sql**. Questa regola consente il traffico SQL solo da subnet *front-end* .
    - **regola di web**. Questa regola Nega che tutti internet associato il traffico dalla subnet *back-end* .

La combinazione di queste regole creare uno scenario di rete Perimetrale simile, in cui possono ricevere solo il traffico in ingresso per SQL dalla subnet front-end subnet back-end e non ha accesso a Internet, mentre la subnet front-end di comunicare con Internet e ricevere solo le richieste HTTP in arrivo.
 
