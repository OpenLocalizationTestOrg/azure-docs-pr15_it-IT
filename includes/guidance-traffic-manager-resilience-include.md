##<a name="highly-available-solutions-with-azure-traffic-manager"></a>Soluzioni disponibile con gestore del traffico Azure

È necessario determinare se i requisiti di disponibilità del carico di lavoro possono essere soddisfatti utilizzando il traffico Azure manager da solo o se è necessario unire gestore del traffico con altre soluzioni DNS o processi. A seconda delle esigenze, è possibile usare:

- **Gestore del traffico da soli**. Se un 99,99% tempo è sufficiente per il carico di lavoro, è possibile utilizzare gestore del traffico autonomamente. In caso di errore nel servizio di gestione di traffico, gli utenti non saranno potrà accedere il carico di lavoro finché non viene ripristinato il servizio di gestione del traffico.

- **Utilizzare un'altra soluzione di gestione di traffico insieme gestore del traffico Azure**. In caso di errore nel servizio di gestione di traffico, è possibile modificare il record CNAME in modo che puntino a di altro servizio di gestione di traffico. Accesso a carico di lavoro è ancora disponibile e distribuito a tutti i percorsi hosting il carico di lavoro. Questa è la soluzione più costosa, ma potrebbe essere necessaria per carichi di lavoro che richiedono un contratto di servizio superiore.
