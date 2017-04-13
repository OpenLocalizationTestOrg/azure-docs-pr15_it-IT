## <a name="scenario"></a>Scenario:

Per illustrare come creare UDRs, questo documento utilizzerà scenario descritto di seguito.

![DESCRIZIONE IMMAGINE](./media/virtual-network-create-udr-scenario-include/figure1.png)

In questo scenario si creerà un UDR per *subnet fine anteriore* e un altro UDR per *subnet fine indietro* , come descritto di seguito: 

- **Front-end UDR**. Front-end UDR verrà applicato alla subnet *front-end* e contengono una route:  
    - **RouteToBackend**. Questo percorso invierà tutto il traffico alla subnet back-end per la macchina virtuale **FW1** .
- **Back-end UDR**. Back-end UDR verrà applicato alla subnet *back-end* e contengono una route: 
    - **RouteToFrontend**. Questo percorso invierà tutto il traffico alla subnet front-end per la macchina virtuale **FW1** .

La combinazione di questi percorsi garantisce che tutto il traffico destinato da una subnet a un'altra instradati nel computer di virtuale **FW1** , viene utilizzato come dispositivo virtuale. È necessario attivare l'inoltro di indirizzi IP di quella macchina virtuale, per garantire che la ricezione di traffico verso altre macchine virtuali.
