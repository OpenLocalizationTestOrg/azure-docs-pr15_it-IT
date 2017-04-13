<properties 
    pageTitle="Dati Factory - regole di denominazione | Microsoft Azure" 
    description="Descrive le regole di denominazione per le entità Factory dati." 
    services="data-factory" 
    documentationCenter="" 
    authors="sharonlo101" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/12/2016" 
    ms.author="shlo"/>

# <a name="azure-data-factory---naming-rules"></a>Dati di Azure Factory - regole di denominazione 
La tabella seguente contiene le regole di denominazione per gli elementi di dati Factory.



Nome | Univocità dei nomi | Controlli di convalida
:--- | :-------------- | :----------------
Dati Factory | Univoca in Microsoft Azure. Nomi sono senza maiuscole/minuscole, vale a dire MyDF e mydf fare riferimento alla stessa factory di dati. |<ul><li>Ogni factory dati è collegata a un solo abbonamento Azure.</li><li>Nomi di oggetti devono iniziare con una lettera o un numero e possono contenere solo lettere, numeri e il carattere trattino (-).</li><li>Ogni carattere trattino (-) deve essere immediatamente preceduto e seguito da una lettera o un numero. Linee tratteggiate consecutivi non sono consentiti nei nomi di contenitore.</li><li>Nome può contenere caratteri 3 63.</li></ul>
Servizi/tabelle/tubazioni collegate | Univoco con una factory di dati. I nomi sono senza maiuscole/minuscole. | <ul><li>Numero massimo di caratteri in un nome di tabella: 260.</li><li>I nomi degli oggetti devono iniziare con un numero lettera di presentazione o un carattere di sottolineatura (_).</li><li>Le seguenti caratteri non consentiti: ".", "+", "?", "/", "<", ">","*", "%", "&", ":","\\"</li></ul>
Gruppo di risorse | Univoca in Microsoft Azure. I nomi sono senza maiuscole/minuscole. | <ul><li>Numero massimo di caratteri: 1000.</li><li>Nome può contenere lettere, cifre e i caratteri seguenti: "-", "_",""e".".</li></ul>