<properties
   pageTitle="Fornire informazioni di contatto di protezione in Centro protezione di Azure | Microsoft Azure"
   description="In questo documento viene illustrato come fornire informazioni di contatto di protezione in Centro protezione di Azure."
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="MBaldwin"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/17/2016"
   ms.author="terrylan"/>

# <a name="provide-security-contact-details-in-azure-security-center"></a>Fornire informazioni di contatto di protezione in Centro protezione di Azure

Centro protezione di Azure indicazioni fornire informazioni di contatto di sicurezza per l'abbonamento Azure se non è già. Queste informazioni vengono usate da Microsoft di contattare l'utente se Microsoft Security Response Center (MSRC) rileva che i dati dei clienti sono stato effettuato l'accesso da una parte atti illecita o non autorizzata. MSRC esegue il monitoraggio della protezione seleziona della rete Azure e infrastruttura e riceve reclami di business intelligence e abuse rischio da terze parti.

Viene inviata una notifica di messaggio di posta elettronica con la prima occorrenza giornaliera di un avviso e solo per gli avvisi di gravità elevato. Preferenze di posta elettronica possono essere configurate solo per i criteri di sottoscrizione. Gruppi di risorse all'interno di un abbonamento verranno ereditano queste impostazioni.

> [AZURE.NOTE] In questo documento introduce il servizio usando una distribuzione di esempio.  Questa non è una Guida dettagliata.

## <a name="implement-the-recommendation"></a>Implementare la recommendation

1. In e il **raccomandazioni** , selezionare **protezione i dettagli sui contatti**.
![Fornire contatto di sicurezza][1]

2. Verrà aperta e il **protezione i dettagli sui contatti**. Selezionare l'abbonamento Azure per fornire informazioni sul contatti.
![Fornire informazioni di contatto di sicurezza][2]

3. Verrà visualizzata una seconda pala **fornire dettagli contatto di sicurezza** .

  - Immettere l'indirizzo di posta elettronica del contatto di sicurezza o indirizzi separati da virgole. Non è un limite per il numero di indirizzi di posta elettronica che è possibile immettere.
  - Immettere un numero di telefono internazionale di sicurezza.
  - Per ricevere messaggi di posta elettronica relativi ad avvisi gravità elevata, attivare l'opzione **Invia messaggi di posta elettronica relativi ad avvisi**.
  - In futuro, si avrà l'opzione per l'invio di notifiche tramite posta elettronica ai proprietari di sottoscrizione. Questa opzione è disattivata.
  - Selezionare **OK** per applicare le informazioni di contatto di sicurezza all'abbonamento.

## <a name="see-also"></a>Vedere anche

Per ulteriori informazioni sul Centro protezione, vedere gli articoli seguenti:

- [Impostazione di criteri di sicurezza in Centro protezione di Azure](security-center-policies.md) - informazioni su come configurare i criteri di sicurezza per gli abbonamenti Azure e i gruppi di risorse.
- [Gestione dei suggerimenti relativi alla protezione in Centro protezione di Azure](security-center-recommendations.md) - informazioni su come raccomandazioni consentono di proteggere le risorse Azure.
- [Il monitoraggio dell'integrità di protezione in Centro protezione di Azure](security-center-monitoring.md) informazioni su come controllare lo stato delle risorse di Azure.
- [Gestione e risposta agli avvisi di protezione in Centro protezione di Azure](security-center-managing-and-responding-alerts.md) -- informazioni su come gestire e risposta agli avvisi di sicurezza.
- [Monitoraggio di soluzioni dei partner con Azure Security Center](security-center-partner-solutions.md) - informazioni su come controllare lo stato di integrità delle soluzioni partner.
- [Domande frequenti su Centro protezione di azure](security-center-faq.md) - trova domande frequenti sull'utilizzo del servizio.
- [Blog di sicurezza di Windows azure](http://blogs.msdn.com/b/azuresecurity/) - ottenere le ultime notizie di sicurezza di Azure e informazioni.

<!--Image references-->
[1]: ./media/security-center-provide-security-contacts/provide-contacts.png
[2]:./media/security-center-provide-security-contacts/provide-contact-details.png
