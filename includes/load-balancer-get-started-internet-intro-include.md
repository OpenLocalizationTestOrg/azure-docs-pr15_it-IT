Un servizio di bilanciamento del carico Azure è un servizio di bilanciamento carico di livello 4 (TCP, UDP). Servizio di bilanciamento del carico garantisce una disponibilità elevata mediante la distribuzione del traffico in ingresso tra istanze di integrità del servizio in servizi cloud o macchine virtuali in un set di servizio di bilanciamento del carico. Servizio di bilanciamento del carico Azure potrebbe costituire anche i servizi in più porte, più indirizzi IP o entrambi.

È possibile configurare un servizio di bilanciamento del carico per:

* Carico bilanciare il traffico Internet in ingresso per le macchine virtuali (VM). Si fa riferimento a un servizio di bilanciamento del carico in questo scenario come un [servizio di bilanciamento del carico con connessione Internet](../articles/load-balancer/load-balancer-internet-overview.md).
* Carico bilanciare il traffico tra le macchine virtuali in una rete virtuale (VNet), tra le macchine virtuali in servizi cloud o tra i computer locale e macchine virtuali in una rete virtuale cross-premise. Si fa riferimento a un dispositivo di bilanciamento del carico in questo scenario come un [sistema di bilanciamento del carico interno (ILB)](../articles/load-balancer/load-balancer-internal-overview.md).
* Inoltra il traffico esterno a una specifica istanza di macchina virtuale.
