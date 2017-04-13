## <a name="scenario"></a>Scenario:

In questo documento consentono a una distribuzione che utilizza più schede di rete in macchine virtuali in uno scenario specifico. In questo scenario, è necessario un a due livelli IaaS carico di lavoro ospitato in Azure. Ogni livello viene distribuito nella propria subnet in una rete virtuale (VNet). Il livello di front-end è costituito da diversi server web, raggruppati in un bilanciamento del carico impostata per la disponibilità elevata. Il livello di back-end è costituito da diversi server di database. Questi server di database verranno distribuiti con due schede di rete, uno per l'accesso al database, altri per la gestione. Lo scenario include anche i gruppi di sicurezza di rete (NSGs) per controllare il traffico consentito per ogni subnet e NIC nella distribuzione. La figura seguente mostra l'architettura di base di questo scenario.  

![Scenario MultiNIC](./media/virtual-network-deploy-multinic-scenario-include/Figure1.png)

