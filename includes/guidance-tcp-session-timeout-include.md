##<a name="tcp-settings-for-azure-vms"></a>Impostazioni di TCP per macchine virtuali di Azure

Azure macchine virtuali comunicano con Internet pubblica utilizzando [NAT] [ nat] (NAT). Dispositivi NAT assegnare un indirizzo IP pubblico e la porta a una macchina virtuale Azure, consentendo che macchine Virtuali di stabilire un socket per la comunicazione con altri dispositivi. Se i pacchetti interrompere scorrono che socket dopo un'ora specifica, il dispositivo NAT interrompe il mapping e il socket è gratuito che verrà utilizzato da altre macchine virtuali.

Si tratta di un comportamento NAT comune, che può causare problemi di comunicazione sulle applicazioni basate sul protocollo TCP che prevede un socket ad anello esploso oltre un periodo di timeout. Sono disponibili due impostazioni di tempo di inattività da prendere in considerazione per le sessioni in uno stato *stabilito connessione* :

- **in ingresso** tramite il [bilanciamento del carico Azure][azure-lb-timeout]. Il timeout per 4 minuti impostazione predefinita e può essere modificato la a 30 minuti.
- **in uscita** utilizzando [SNAT] [ snat] (origine NAT). Il timeout è impostato su 4 minuti e non può essere modificato.

Per assicurarsi che le connessioni vengono persi non oltre il limite di timeout, assicurarsi che l'applicazione mantiene la sessione attivo, oppure è possibile configurare il sistema operativo a questo scopo. Le impostazioni da utilizzare sono diverse per i sistemi Linux e Windows, come illustrato di seguito.

Per [Linux][linux], è consigliabile modificare variabili kernel riportate di seguito.
NET.IPv4.tcp_keepalive_time = net.ipv4.tcp_keepalive_intvl 120 = 30 net.ipv4.tcp_keepalive_probes = 8
 
Per [Windows][windows], è necessario modificare i valori del Registro di sistema riportata di seguito.
KeepAliveInterval = 30 KeepAliveTime = 120 TcpMaxDataRetransmissions = 8


Le impostazioni precedenti assicurano un pacchetto attivo viene inviato dopo 2 minuti (120 secondi) di inattività e quindi inviato ogni 30 secondi. E se 8 di tali pacchetti non riesce, viene eliminata la sessione.

<!-- links -->
[nat]: http://computer.howstuffworks.com/nat.htm
[snat]: ../load-balancer/load-balancer-overview.md/#source-nat
[linux]: http://tldp.org/HOWTO/TCP-Keepalive-HOWTO/usingkeepalive.html
[windows]: http://blogs.technet.com/b/nettracer/archive/2010/06/03/things-that-you-may-want-to-know-about-tcp-keepalives.aspx
[azure-lb-timeout]: ../load-balancer/load-balancer-tcp-idle-timeout.md