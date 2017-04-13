<properties
    pageTitle="B2C Azure Active Directory: Autenticazione a più fattori | Microsoft Azure"
    description="Come abilitare l'autenticazione a più fattori nelle applicazioni consumer esposto protette da Azure Active Directory B2C"
    services="active-directory-b2c"
    documentationCenter=""
    authors="swkrish"
    manager="msmbaldwin"
    editor="bryanla"/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/24/2016"
    ms.author="swkrish"/>

# <a name="azure-active-directory-b2c-enable-multi-factor-authentication-in-your-consumer-facing-applications"></a>Azure B2C Directory attiva: Abilitare l'autenticazione a più fattori nelle applicazioni consumer esposto

Azure Active Directory (Azure Active Directory) B2C integrazione diretta con [Autenticazione a più fattori Azure](../multi-factor-authentication/multi-factor-authentication.md) in modo che è possibile aggiungere un secondo livello di sicurezza per l'abbonamento e di accesso di esperienze nelle applicazioni consumer esposto. E farlo senza scrivere una sola riga di codice. Attualmente è supportato verifica messaggio telefonata e testo. Se già stato creato criteri per l'abbonamento e l'accesso, è comunque possibile attivare l'autenticazione a più fattori.

> [AZURE.NOTE]
Autenticazione a più fattori può anche essere attivato quando si creano criteri di abbonamento e l'accesso, non solo modificando i criteri esistenti.

Questa caratteristica consente alle applicazioni situazioni, ad esempio le operazioni seguenti:

- Non richiedono l'autenticazione a più fattori accedere a un'applicazione, ma sia necessario per accedere a un altro. Ad esempio consumer possa accedere a una richiesta di assicurazione automatica con un account di social networking o locale, ma è necessario verificare il numero di telefono prima che accedono all'applicazione di assicurazione home registrato nella stessa directory.
- Non è richiesta l'autenticazione a più fattori accedere a un'applicazione in generale, ma sia necessario per accedere a parti riservate all'interno di essa. Ad esempio, consumer possa accedere a un'applicazione bancaria con un account di social networking o locale e controllo saldo, ma è necessario verificare il numero di telefono prima di cercare un trasferimento.

## <a name="modify-your-sign-up-policy-to-enable-multi-factor-authentication"></a>Modificare i criteri per l'abbonamento per abilitare l'autenticazione a più fattori

1. [Seguire questa procedura per passare a e il caratteristiche B2C nel portale di Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Fare clic su **criteri per l'abbonamento**.
3. Fare clic sui criteri per l'abbonamento (ad esempio, "B2C_1_SiUp") per aprirlo.
4. Fare clic su **autenticazione a più fattori** e disattivare lo **stato** su **ON**. Fare clic su **OK**.
5. Fare clic su **Salva** nella parte superiore e il.

È possibile utilizzare la funzionalità "Esegui" sul criterio per verificare l'esperienza utente. Verificare quanto segue:

Un account utente viene creato nella directory prima che si verifica il passaggio di autenticazione a più fattori. Durante il passaggio consumer viene chiesto di specificare il numero di telefono e la verifica. Se la verifica riesce, il numero di telefono è collegato all'account consumer per successivi utilizzi. Anche se il consumer Annulla o Elimina, è possibile chiedere lui per verificare un numero di telefono nuovo durante il successivo accesso aggiuntivo (con attivata l'autenticazione a più fattori).

## <a name="modify-your-sign-in-policy-to-enable-multi-factor-authentication"></a>Modificare il criterio di accesso per abilitare l'autenticazione a più fattori

1. [Seguire questa procedura per passare a e il caratteristiche B2C nel portale di Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Fare clic su **criteri di accesso**.
3. Fare clic sui criteri di iscrizione (ad esempio, "B2C_1_SiIn") per aprirlo. Fare clic su **Modifica** nella parte superiore e il.
4. Fare clic su **autenticazione a più fattori** e disattivare lo **stato** su **ON**. Fare clic su **OK**.
5. Fare clic su **Salva** nella parte superiore e il.

È possibile utilizzare la funzionalità "Esegui" sul criterio per verificare l'esperienza utente. Verificare quanto segue:

Quando l'utente si registra (usando un account di social networking o locale), se un numero di telefono verificato è collegato all'account consumer, lui viene richiesto di verifica. Se non è collegato Nessun numero di telefono, consumer viene chiesto di fornire una e verificare. Una volta verificato, il numero di telefono è collegato all'account consumer per successivi utilizzi.

## <a name="multi-factor-authentication-on-other-policies"></a>Autenticazione a più fattori in altri criteri

Come descritto per i criteri per l'abbonamento e accesso sopra, è anche possibile attivare l'autenticazione a più fattori su abbonamento o criteri di reimpostare la password e criteri di accesso. Sarà presto disponibile nel profilo Modifica criteri.
