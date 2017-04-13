<properties
    pageTitle="Distribuire modelli con Visual Studio in pila Azure | Microsoft Azure"
    description="Informazioni su come distribuire modelli con Visual Studio in pila Azure."
    services="azure-stack"
    documentationCenter=""
    authors="HeathL17"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="helaw"/>

# <a name="deploy-templates-in-azure-stack-using-visual-studio"></a>Distribuire modelli di Stack di Azure utilizzando Visual Studio

Utilizzare Visual Studio per distribuire modelli di gestione di risorse Azure la prova pratica dello Stack di Azure.

Modelli di gestione risorse distribuire ed effettuare il provisioning di tutte le risorse per l'applicazione in un'operazione su una singola e coordinata.

1.  Aprire Visual Studio 2015 Update 1.

2.  Fare clic su **File**, fare clic su **Nuovo**e nella finestra di dialogo **Nuovo progetto** fare clic su **Gruppo di risorse Azure**.

3.  Immettere un **nome** per il nuovo progetto e quindi fare clic su **OK**.

4.  Nella finestra di dialogo **Seleziona modello di Azure** , fare clic su **macchina virtuale di Windows**e quindi fare clic su **OK**.

  Nel nuovo progetto, è possibile visualizzare un elenco dei modelli disponibili, espandere il nodo **modelli** nel riquadro **Esplora soluzioni** .

5.  Nel riquadro **Esplora soluzioni** fare clic con il nome del progetto, fare clic su **Distribuisci**e fare clic su **Nuova distribuzione**.

6.  Nell'elenco a discesa **abbonamento** della finestra di dialogo **Distribuisci al gruppo di risorse** selezionare l'abbonamento a Microsoft Azure Stack.

7.  Nell'elenco **Gruppo di risorse** , scegliere un gruppo di risorse esistente o crearne uno nuovo.

8.  Nell'elenco **delle risorse gruppo percorso** , scegliere un percorso e quindi fare clic su **Distribuisci**.

9.  Nella finestra di dialogo **Modifica parametri** immettere valori per i parametri (che possono variare dal modello) e quindi fare clic su **Salva**.

## <a name="next-steps"></a>Passaggi successivi

[Distribuire modelli con la riga di comando](azure-stack-deploy-template-command-line.md)
