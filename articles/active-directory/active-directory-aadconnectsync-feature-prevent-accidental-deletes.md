<properties
   pageTitle="Sincronizzazione di Azure AD Connect: impedisce eliminazioni accidentali | Microsoft Azure"
   description="In questo argomento viene descritta la funzionalità di eliminazioni accidentali (impedendo eliminazioni accidentali) Impedisci di Azure AD Connect."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/01/2016"
   ms.author="billmath"/>

# <a name="azure-ad-connect-sync-prevent-accidental-deletes"></a>Sincronizzazione di Azure AD Connect: impedisce eliminazioni accidentali
In questo argomento viene descritta la funzionalità di eliminazioni accidentali (impedendo eliminazioni accidentali) Impedisci di Azure AD Connect.

Durante l'installazione di Azure AD Connect impedire accidentali eliminazioni è attivata per impostazione predefinita e non configurato per consentire l'esportazione con più di 500 Elimina. Questa caratteristica è progettata per protezione da modifiche accidentali configurazione e sulle modifiche apportate alla directory locale che possa modificare molti utenti e altri oggetti.

Scenari comuni quando viene visualizzato Elimina molti includono:

- Modifiche apportate ai [filtri](active-directory-aadconnectsync-configure-filtering.md) in cui non è selezionata un' intera [unità Organizzativa](active-directory-aadconnectsync-configure-filtering.md#organizational-unitbased-filtering) o [dominio](active-directory-aadconnectsync-configure-filtering.md#domain-based-filtering) .
- Tutti gli oggetti in un'unità Organizzativa vengono eliminati.
- Un'unità Organizzativa è stata rinominata in modo che tutti i relativi oggetti sono considerati fuori ambito per la sincronizzazione.

Il valore predefinito di 500 oggetti può essere modificato con PowerShell utilizzando `Enable-ADSyncExportDeletionThreshold`. È necessario configurare questo valore per adattarsi alle dimensioni della propria organizzazione. Poiché l'utilità di pianificazione sincronizzazione viene eseguita ogni 30 minuti, il valore è il numero di eliminazioni visualizzate entro 30 minuti.

Se sono presenti troppi eliminazioni in prova per essere esportati in Azure Active Directory, si interrompe l'esportazione e si riceve un messaggio di posta elettronica come segue:

![Evitare di posta elettronica eliminazioni accidentali](./media/active-directory-aadconnectsync-feature-prevent-accidental-deletes/email.png)

> *Hello (contatto tecnico). (Orario) il servizio di sincronizzazione di identità rileva che il numero di eliminazioni superato il limite di eliminazione configurato per (nome organizzazione). Un totale di (numero) oggetti sono stati inviati per l'eliminazione di eseguire la sincronizzazione di identità. Questa soddisfatto o superato il valore di soglia configurato l'eliminazione di oggetti (numero). È necessario per confermare che le eliminazioni devono essere elaborati prima verranno. Vedere le eliminazioni accidentali che impedisce per ulteriori informazioni sull'errore elencati in questo messaggio di posta elettronica.*

È anche possibile visualizzare lo stato `stopped-deletion-threshold-exceeded` quando Cerca in **Gestione servizio di sincronizzazione** dell'interfaccia utente per il profilo di esportazione.
![Evitare che le eliminazioni accidentali UI gestore del servizio di sincronizzazione](./media/active-directory-aadconnectsync-feature-prevent-accidental-deletes/syncservicemanager.png)

Se si tratta di imprevisto, provare a utilizzare e intraprendere azioni correttive. Per visualizzare gli oggetti che devono essere eliminate, procedere come segue:

1. Avviare il **servizio di sincronizzazione** dal Menu Start.
2. Passare ai **connettori**.
3. Selezionare il connettore con il tipo di **Azure Active Directory**.
4. In **Azioni** a destra, selezionare **Ricerca connettore spazio**.
5. Nella finestra popup nella sezione **ambito**selezionare **Disconnessa dal momento che** e scegliere un orario in passato. Fare clic su **Cerca**. Questa pagina fornisce una visualizzazione di tutti gli oggetti per essere eliminato. Facendo clic su ogni elemento, è possibile ottenere ulteriori informazioni sull'oggetto. È anche possibile fare clic su **Delle colonne** per aggiungere altri attributi per essere visibile nella griglia.

![Spazio di connettore di ricerca](./media/active-directory-aadconnectsync-feature-prevent-accidental-deletes/searchcs.png)

Volendo tutte le eliminazioni, eseguire le operazioni seguenti:

1. Per disattivare questa protezione temporaneamente e consentire a tali eliminazioni elaborata, eseguire il cmdlet di PowerShell: `Disable-ADSyncExportDeletionThreshold`. Specificare un account di amministratore globale di Azure Active Directory e una password.
![Credenziali](./media/active-directory-aadconnectsync-feature-prevent-accidental-deletes/credentials.png)
2. Con Azure Active Directory Connector ancora selezionato, selezionare l'azione **Esegui** e quindi **esportare**.
3. Per riattivare la protezione, eseguire il cmdlet di PowerShell: `Enable-ADSyncExportDeletionThreshold`.

## <a name="next-steps"></a>Passaggi successivi

**Argomenti della panoramica**

- [Sincronizzazione di Azure AD Connect: comprendere e personalizzare la sincronizzazione](active-directory-aadconnectsync-whatis.md)
- [Integrazione le identità locali con Azure Active Directory](active-directory-aadconnect.md)
