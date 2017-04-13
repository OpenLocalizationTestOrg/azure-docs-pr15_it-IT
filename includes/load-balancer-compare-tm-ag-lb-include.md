## <a name="load-balancer-differences"></a>Differenze di bilanciamento carico

Sono disponibili diverse opzioni per distribuire il traffico di rete utilizzando Microsoft Azure. Queste opzioni funzionano in modo leggermente diverso da altri, con una funzionalità diversa impostata e supporta diversi scenari. Ogni possono essere usate in isolamento oppure riunire.

- **Servizio di bilanciamento del carico Azure** funziona dal livello di trasporto (livello 4 dello stack di riferimento OSI rete). Fornisce distribuzione a livello di rete del traffico attraverso le istanze di un'applicazione in esecuzione nell'interfaccia di dati di Azure stesso.

- **Gateway di applicazioni** funziona livello di applicazione (livello 7 dello stack di riferimento OSI rete). Funge da un servizio proxy inverso, terminare la connessione client e le richieste di back-end endpoint di inoltro.

- **Gestore del traffico** funziona a livello di DNS.  Utilizza le risposte DNS per indirizzare il traffico degli utenti finali agli endpoint distribuito a livello globale. Client quindi connettersi a tali endpoint direttamente.

Nella tabella seguente sono riepilogate le funzionalità offerte da ogni servizio:

| Servizio | Bilanciamento del carico Azure | Gateway di applicazioni | Gestore del traffico |
|---|---|---|---|
|Tecnologia| Livello di trasporto (livello 4) | Livello di applicazione (livello 7) | Livello di DNS |
| Protocolli di applicazioni supportati | Qualsiasi | HTTP e HTTPS |  Qualsiasi (un endpoint HTTP è necessario per il monitoraggio endpoint) |
| Punti finali | Azure istanze di ruolo macchine virtuali e servizi Cloud | Qualsiasi indirizzo IP interno Azure o internet pubblica indirizzo IP | Macchine virtuali di Azure, servizi Cloud, Azure Web Apps e gli endpoint esterni |
| Supporto Vnet | Può essere usata per entrambe le applicazioni Internet esposto e (Vnet) | Può essere usata per entrambe le applicazioni Internet esposto e (Vnet) |    Supporta solo le applicazioni di esposto a Internet |
Endpoint monitoraggio | È supportata tramite le ricerche | È supportata tramite le ricerche | È supportata tramite HTTP/HTTPS GET | 

Il traffico Azure di bilanciamento del carico e di rete la distribuzione di Gateway di applicazioni per gli endpoint ma hanno differenti scenari di utilizzo per il tipo di traffico per gestire. Nella tabella seguente consente di comprendere la differenza tra i servizi di bilanciamento del due carico:

| Tipo | Bilanciamento del carico Azure | Gateway di applicazioni |
|---|---|---|
| Protocolli | TCP/UDP | HTTP / HTTPS |
| Prenotazione IP | Supportati | Non è supportata | 
| Modalità di bilanciamento del carico | 5-tuple(source IP, source port, destination IP, destination port, protocol type) | Circolari<br>Il routing basato su URL | 
| Modalità di bilanciamento del carico (IP di origine / permanenti sessioni) |  2-tupla (indirizzo IP di origine e destinazione IP), 3-tupla (indirizzo IP di origine, IP di destinazione e porta). Ridimensionare verso l'alto o verso il basso in base al numero di macchine virtuali | Basata su cookie affinità<br>Il routing basato su URL |
| Ricerche integrità | Predefinito: intervallo di ricerca - 15 secondi. Escluso dalla rotazione: 2 ripetuti errori. Supporta definite dall'utente esegue la ricerca | Intervallo di inattività verifica 30 secondi. Sottoscritti dopo 5 errori consecutivi traffico live o un errore di verifica solo in modalità di inattività. Supporta definite dall'utente esegue la ricerca | 
| Possibilità di scaricare SSL | Non è supportata | Supportati | 
  