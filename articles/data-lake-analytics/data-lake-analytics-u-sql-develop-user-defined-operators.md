<properties 
   pageTitle="Sviluppare operatori definite dall'utente U SQL per i processi di Azure dati Lake Analitica | Azure" 
   description="Informazioni su come sviluppare operatori definiti dall'utente per usare e riutilizzare in processi dati Lake Analitica. " 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="edmacauley" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="05/16/2016"
   ms.author="edmaca"/>


# <a name="develop-u-sql-user-defined-operators-for-azure-data-lake-analytics-jobs"></a>Sviluppare operatori definite dall'utente U SQL per i processi di Azure dati Lake Analitica

Informazioni su come sviluppare operatori definiti dall'utente per usare e riutilizzare in processi dati Lake Analitica. Sviluppare un operatore personalizzato per convertire i nomi di paese.

##<a name="prerequisites"></a>Prerequisiti

- Visual Studio 2015, Visual Studio 2013 aggiornare 4 o Visual Studio 2012 con installato Visual C++ 
- Microsoft Azure SDK per .NET versione 2.5 o versione successiva.  Installare Office tramite il programma di installazione di piattaforma Web.
- Un account di dati Lake Analitica.  Vedere [Guida introduttiva di Azure dati Lake Analitica nel portale di Azure](data-lake-analytics-get-started-portal.md).
- Vedere l'esercitazione [Introduzione Azure dati Lake Analitica U SQL Studio](data-lake-analytics-u-sql-get-started.md) .
- Connettersi a Azure, vedere [Guida introduttiva di Azure dati Lake Analitica U SQL Studio](data-lake-analytics-u-sql-get-started.md#connect-to-azure). 
- Caricare i dati di origine, vedere [Guida introduttiva di Azure dati Lake Analitica U SQL Studio](data-lake-analytics-u-sql-get-started.md#upload-source-data-files). 

## <a name="define-and-use-user-defined-operator-in-u-sql"></a>Definire e utilizzare l'operatore definito dall'utente in SQL U

**Per creare e inviare un processo U-SQL** 

1. Dal menu **File** fare clic su **Nuovo**e quindi fare clic su **progetto**.
2. Selezionare il tipo di **Progetto U-SQL** .

    ![nuovo progetto di Visual Studio U SQL](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-analytics-data-lake-tools-new-project.png)

3. Fare clic su **OK**. Visual studio crea una soluzione con un file Script.usql.
4. Da **Esplora soluzioni**, espandere Script.usql e quindi fare doppio clic su **Script.usql.cs**.
5. Incollare il codice seguente nel file:

        using Microsoft.Analytics.Interfaces;
        using System.Collections.Generic;
        
        namespace USQL_UDO
        {
            public class CountryName : IProcessor
            {
                private static IDictionary<string, string> CountryTranslation = new Dictionary<string, string>
                {
                    {
                        "Deutschland", "Germany"
                    },
                    {
                        "Schwiiz", "Switzerland"
                    },
                    {
                        "UK", "United Kingdom"
                    },
                    {
                        "USA", "United States of America"
                    },
                    {
                        "中国", "PR China"
                    }
                };
        
                public override IRow Process(IRow input, IUpdatableRow output)
                {
        
                    string UserID = input.Get<string>("UserID");
                    string Name = input.Get<string>("Name");
                    string Address = input.Get<string>("Address");
                    string City = input.Get<string>("City");
                    string State = input.Get<string>("State");
                    string PostalCode = input.Get<string>("PostalCode");
                    string Country = input.Get<string>("Country");
                    string Phone = input.Get<string>("Phone");
        
                    if (CountryTranslation.Keys.Contains(Country))
                    {
                        Country = CountryTranslation[Country];
                    }
                    output.Set<string>(0, UserID);
                    output.Set<string>(1, Name);
                    output.Set<string>(2, Address);
                    output.Set<string>(3, City);
                    output.Set<string>(4, State);
                    output.Set<string>(5, PostalCode);
                    output.Set<string>(6, Country);
                    output.Set<string>(7, Phone);
        
                    return output.AsReadOnly();
                }
            }
        }

5. Aprire Script.usql e incollare lo script U SQL seguente:

        @drivers =
            EXTRACT UserID      string,
                    Name        string,
                    Address     string,
                    City        string,
                    State       string,
                    PostalCode  string,
                    Country     string,
                    Phone       string
            FROM "/Samples/Data/AmbulanceData/Drivers.txt"
            USING Extractors.Tsv(Encoding.Unicode);
        
        @drivers_CountryName =
            PROCESS @drivers
            PRODUCE UserID string,
                    Name string,
                    Address string,
                    City string,
                    State string,
                    PostalCode string,
                    Country string,
                    Phone string
            USING new USQL_UDO.CountryName();    
        
        OUTPUT @drivers_CountryName
            TO "/Samples/Outputs/Drivers.csv"
            USING Outputters.Csv(Encoding.Unicode);

6. Da **Esplora soluzioni**fare clic con il pulsante destro **Script.usql**e quindi fare clic su **Creazione di Script**.
6. Da **Esplora soluzioni**fare clic con il pulsante destro **Script.usql**e quindi fare clic su **Invia Script**.
7. Se non è ancora stato connettersi al proprio abbonamento Azure, sarà visualizzato immettere le credenziali dell'account Azure.
7. Fare clic su **Invia**. Risultati invio e collegamento processo sono disponibili nella finestra risultati al termine della presentazione.
8. È necessario fare clic sul pulsante Aggiorna per visualizzare lo stato del processo più recente e aggiornare la schermata.

**Per visualizzare l'output di processo**

1. Da **Esplora Server**, espandere **Azure**, espandere **Dati Lake Analitica**, espandere account dati Lake Analitica, espandere **Gli account di archiviazione**, rapida dello spazio di archiviazione predefinito e quindi fare clic su **Esplora risorse**. 
2. Espandere esempi, espandere output e quindi fare doppio clic su **CSV**.


##<a name="see-also"></a>Vedere anche

- [Guida introduttiva a Analitica Lake dati tramite PowerShell](data-lake-analytics-get-started-powershell.md)
- [Guida introduttiva a Analitica Lake dati tramite il portale di Azure](data-lake-analytics-get-started-portal.md)
- [Usare dati Lake Tools per Visual Studio per lo sviluppo di applicazioni U SQL](data-lake-analytics-data-lake-tools-get-started.md)
