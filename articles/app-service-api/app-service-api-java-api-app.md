<properties
    pageTitle="Compilare e distribuire un'app API Java nel servizio App Azure"
    description="Informazioni su come creare un pacchetto dell'app API Java e distribuire al servizio App Azure."
    services="app-service\api"
    documentationCenter="java"
    authors="bradygaster"
    manager="mohisri"
    editor="tdykstra"/>

<tags
    ms.service="app-service-api"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="java"
    ms.topic="get-started-article"
    ms.date="08/31/2016"
    ms.author="rachelap"/>

# <a name="build-and-deploy-a-java-api-app-in-azure-app-service"></a>Compilare e distribuire un'app API Java nel servizio App Azure

[AZURE.INCLUDE [app-service-api-get-started-selector](../../includes/app-service-api-get-started-selector.md)]

In questa esercitazione viene illustrato come creare un'applicazione Java e distribuire in Azure App servizio API App utilizzando [fra]. Le istruzioni disponibili in questa esercitazione possono essere seguite dal sistema operativo che è in grado di eseguire linguaggio. Il codice in questa esercitazione viene compilato mediante [Maven]. [JAX-r] viene generato in base alla specifica di metadati [Swagger] utilizzando l' [Editor Swagger]viene utilizzata per creare il servizio REST.

## <a name="prerequisites"></a>Prerequisiti

1. [Kit per gli sviluppatori di linguaggio 8] \(o versioni successive)
1. [Maven] installato nel computer di sviluppo
1. [Operazioni] installato nel computer di sviluppo
1. Una sottoscrizione a pagamento o [versione di valutazione gratuita] di [Microsoft Azure]
1. Un'applicazione di test HTTP come [Postman]

## <a name="scaffold-the-api-using-swaggerio"></a>Scaffold API utilizzando Swagger.IO

Nell'editor swagger.io online, è possibile immettere codice JSON Swagger o YAML che rappresenta la struttura dell'API. Dopo avere creato l'area della superficie API progettato, è possibile esportare codice per un'ampia varietà di piattaforme e Framework. Nella sezione successiva verrà modificato il codice di supporto temporaneo per includere funzionalità fittizi. 

In questa dimostrazione inizia con un corpo Swagger JSON cui si desidera incollare nell'editor di swagger.io, quindi essere usata per generare il codice che utilizza JAX RS per accedere a un endpoint API REST. Quindi, viene modificato il codice di supporto temporaneo per restituire dati fittizi, simulare una API REST integrato nella parte superiore di un meccanismo di persistenza dei dati.  

1. Copiare il codice seguente Swagger JSON agli Appunti:

        {
            "swagger": "2.0",
            "info": {
                "version": "v1",
                "title": "Contact List",
                "description": "A Contact list API based on Swagger and built using Java"
            },
            "host": "localhost",
            "schemes": [
                "http",
                "https"
            ],
            "basePath": "/api",
            "paths": {
                "/contacts": {
                    "get": {
                        "tags": [
                            "Contact"
                        ],
                        "operationId": "contacts_get",
                        "consumes": [],
                        "produces": [
                            "application/json",
                            "text/json"
                        ],
                        "responses": {
                            "200": {
                                "description": "OK",
                                "schema": {
                                    "type": "array",
                                    "items": {
                                        "$ref": "#/definitions/Contact"
                                    }
                                }
                            }
                        },
                        "deprecated": false
                    }
                },
                "/contacts/{id}": {
                    "get": {
                        "tags": [
                            "Contact"
                        ],
                        "operationId": "contacts_getById",
                        "consumes": [],
                        "produces": [
                            "application/json",
                            "text/json"
                        ],
                        "parameters": [
                            {
                                "name": "id",
                                "in": "path",
                                "required": true,
                                "type": "integer",
                                "format": "int32"
                            }
                        ],
                        "responses": {
                            "200": {
                                "description": "OK",
                                "schema": {
                                    "type": "array",
                                    "items": {
                                        "$ref": "#/definitions/Contact"
                                    }
                                }
                            }
                        },
                        "deprecated": false
                    }
                }
            },
            "definitions": {
                "Contact": {
                    "type": "object",
                    "properties": {
                        "Id": {
                            "format": "int32",
                            "type": "integer"
                        },
                        "Name": {
                            "type": "string"
                        },
                        "EmailAddress": {
                            "type": "string"
                        }
                    }
                }
            }
        }

1. Passare al [Online Swagger Editor]. Una volta, fare clic sulla voce di menu **File -> Incolla JSON** .

    ![Voce di menu Incolla JSON][paste-json]

1. Incollare i contatti elenco API Swagger JSON copiato in precedenza. 

    ![Incollare il codice JSON Swagger][pasted-swagger]

1. Visualizzare le pagine della documentazione e riepilogo API rendering nell'editor. 

    ![Visualizzazione Swagger generato documenti][view-swagger-generated-docs]

1. Selezionare l'opzione di menu **generare Server-> JAX RS** a scaffold codice sul lato server viene modificato in un secondo momento per aggiungere implementazione fittizia. 

    ![Generare voce di Menu codice][generate-code-menu-item]

    Dopo il codice viene generato, si viene fornito un file ZIP da scaricare. Il file contiene il codice scaffolding dal generatore di codice Swagger e tutti associati compilazione script. Decomprimere intera raccolta in una directory nella workstation di sviluppo. 

## <a name="edit-the-code-to-add-api-implementation"></a>Modificare il codice per aggiungere implementazione dell'API

In questa sezione, è necessario sostituire implementazione sul lato server del codice generato Swagger con il codice personalizzato. Il nuovo codice restituirà un'entità elenco matrice di contatto al client chiamante. 

1. Aprire il file di modello *Contact.java* , che si trova nella cartella *src/gen/java/io/swagger/modello* utilizzando [Visual Studio codice] o un editor di testo. 

    ![File di modello contatti aperto][open-contact-model-file]

1. Aggiungere il seguente costruttore alla classe **contatto** . 

        public Contact(Integer id, String name, String email) 
        {
            this.id = id;
            this.name = name;
            this.emailAddress = email;
        }

1. Aprire il file di implementazione del servizio di *ContactsApiServiceImpl.java* , si trova nella cartella *src/principali/java/io/swagger/api / l'implementazione del* utilizzando [Visual Studio codice] o un editor di testo.

    ![File di codice servizio contatto aperto][open-contact-service-code-file]

1. Sovrascrivere il codice nel file con il nuovo codice per aggiungere un'implementazione fittizia al codice servizio. 

        package io.swagger.api.impl;

        import io.swagger.api.*;
        import io.swagger.model.*;
        import com.sun.jersey.multipart.FormDataParam;
        import io.swagger.model.Contact;
        import java.util.*;
        import io.swagger.api.NotFoundException;
        import java.io.InputStream;
        import com.sun.jersey.core.header.FormDataContentDisposition;
        import com.sun.jersey.multipart.FormDataParam;
        import javax.ws.rs.core.Response;
        import javax.ws.rs.core.SecurityContext;

        @javax.annotation.Generated(value = "class io.swagger.codegen.languages.JaxRSServerCodegen", date = "2015-11-24T21:54:11.648Z")
        public class ContactsApiServiceImpl extends ContactsApiService {
  
            private ArrayList<Contact> loadContacts()
            {
                ArrayList<Contact> list = new ArrayList<Contact>();
                list.add(new Contact(1, "Barney Poland", "barney@contoso.com"));
                list.add(new Contact(2, "Lacy Barrera", "lacy@contoso.com"));
                list.add(new Contact(3, "Lora Riggs", "lora@contoso.com"));
                return list;
            }
  
            @Override
            public Response contactsGet(SecurityContext securityContext)
            throws NotFoundException {
                ArrayList<Contact> list = loadContacts();
                return Response.ok().entity(list).build();
                }
  
            @Override
            public Response contactsGetById(Integer id, SecurityContext securityContext)
            throws NotFoundException {
                ArrayList<Contact> list = loadContacts();
                Contact ret = null;
            
                for(int i=0; i<list.size(); i++)
                {
                    if(list.get(i).getId() == id)
                        {
                            ret = list.get(i);
                        }
                }
                return Response.ok().entity(ret).build();
            }
        }

1. Aprire un prompt dei comandi e passare alla cartella radice dell'applicazione.

1. Eseguire il seguente comando Maven per generare il codice ed eseguirlo tramite il server di app alla banchina in locale. 

        mvn package jetty:run

1. È necessario visualizzare la finestra di comando riflettere che alla banchina ha avviato il codice su porta 8080. 

    ![File di codice servizio contatto aperto][run-jetty-war]

1. Utilizzare [Postman] per effettuare una richiesta al "Recupera tutti i contatti" metodo API all'api/http://localhost:8080/contatti.

    ![Chiamare i contatti dell'API][calling-contacts-api]

1. Utilizzare [Postman] per effettuare una richiesta al metodo API "Recupera contatto specifico" che si trova in http://localhost:8080/api/contatti/2.

    ![Chiamare i contatti dell'API][calling-specific-contact-api]

1. Infine, compilare il file WAR Java (archivio Web) eseguendo il seguente comando Maven nella console. 

        mvn package war:war

1. Una volta creato il file WAR, verrà inserito nella cartella di **destinazione** . Esplorare la cartella di **destinazione** e rinominare il file WAR in **ROOT.war**. (Assicurarsi che le lettere maiuscole corrispondano a questo formato).

         rename swagger-jaxrs-server-1.0.0.war ROOT.war

1. Infine, eseguire i comandi seguenti dalla cartella principale dell'applicazione per creare una cartella di **distribuzione** da utilizzare per distribuire il file WAR Azure. 

         mkdir deploy
         mkdir deploy\webapps
         copy target\ROOT.war deploy\webapps
         cd deploy

## <a name="publish-the-output-to-azure-app-service"></a>Pubblicare l'output al servizio App Azure

In questa sezione verranno informazioni su come creare una nuova App API tramite il portale di Azure, preparare che App API per l'hosting di applicazioni Java e distribuire il file WAR appena creato al servizio di App Azure per eseguire la nuova API App. 

1. Creare una nuova app API nel [portale di Azure], facendo clic sulla voce di menu **Nuovo -> Web + Mobile -> app API** , immettere i dettagli dell'app e quindi fare clic su **Crea**.

    ![Creare una nuova API App][create-api-app]

1. Dopo aver creato l'app API, aprire blade **Impostazioni** dell'applicazione e quindi fare clic sulla voce di menu **Impostazioni applicazione** . Selezionare le versioni più recenti di linguaggio tra le opzioni disponibili, quindi selezionare Tomcat più recenti dal menu **contenitore Web** e quindi fare clic su **Salva**.

    ![Configurare la Java e il API App][set-up-java]

1. Fare clic sulla voce di menu impostazioni **delle credenziali di distribuzione** e specificare un nome utente e password che si desidera utilizzare per la pubblicazione di file all'API App. 

    ![Impostare le credenziali di distribuzione][deployment-credentials]

1. Selezionare la voce di menu impostazioni **origine di distribuzione** . Una volta, fare clic sul pulsante **Scegli origine** , selezionare l'opzione **Archivio fra locale** e quindi fare clic su **OK**. Verrà creato un archivio di operazioni in esecuzione in Azure, che contiene un'associazione con l'API di App. Ogni volta che si esegue il commit codice ramo *master* il repository fra il codice viene pubblicato nell'istanza di API App in esecuzione attiva. 

    ![Configurare un nuovo repository fra locale][select-git-repo]

1. Copia URL dell'archivio fra nuovo negli Appunti. Salva come sarà importante in un momento. 

    ![Configurare un nuovo repository fra per l'app][copy-git-repo-url]

1. Operazioni push il file WAR all'archivio online. A tale scopo, passare alla cartella **distribuire** creata in precedenza in modo che è possibile applicare facilmente il codice per l'archivio in esecuzione nel servizio di App. Quando è nella finestra della console e ci si sposta nella cartella in cui si trova nella cartella WebApp, eseguire i comandi di operazioni seguenti per avviare il processo e avvio di una distribuzione. 

         git init
         git add .
         git commit -m "initial commit"
         git remote add azure [YOUR GIT URL]
         git push azure master

    Quando si utilizza la richiesta di **push** , verrà chiesto per la password creata in precedenza per le credenziali di distribuzione. Dopo avere immesso le credenziali, verrà visualizzato il portale di visualizzare che l'aggiornamento è stato distribuito.

1. Se si utilizza nuovamente Postman prima l'App API appena distribuito in esecuzione in Azure App servizio, si noterà che il comportamento sia coerenza e che ora restituisce i dati dei contatti come previsto con le modifiche al codice semplice per la Swagger.io scaffolding codice Java. 

    ![Usare l'API REST di linguaggio contatti live in Azure][postman-calling-azure-contacts]

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato possibile iniziare con un file Swagger JSON e alcune scaffolding codice Java ottenuto dall'editor Swagger.io. A questo punto, le modifiche semplici e un fra distribuire processo condotto alla visualizzazione in un'app API funziona scritta in linguaggio. Nell'esercitazione successiva viene illustrato come [utilizzare App API dai client JavaScript, utilizzando CORS][App Service API CORS]. Le esercitazioni successive della serie viene illustrato come implementare l'autenticazione e l'autorizzazione.

Per essere incrementali in questo esempio, si acquisiscono informazioni sull' [Archiviazione SDK per Java] in modo permanente i BLOB JSON. In alternativa, è possibile utilizzare il [Documento DB linguaggio SDK] per salvare i dati dei contatti in Azure documento DB. 

Per ulteriori informazioni sull'uso di linguaggio in Azure, vedere il [Centro per sviluppatori di linguaggio].

<!-- URL List -->

[App Service API CORS]: app-service-api-cors-consume-javascript.md
[Portale di Azure]: https://portal.azure.com/
[Documento linguaggio DB SDK]: ../documentdb/documentdb-java-application.md
[versione di valutazione gratuita]: https://azure.microsoft.com/pricing/free-trial/
[Operazioni]: http://www.git-scm.com/
[Centro per sviluppatori Java]: /develop/java/
[Kit per gli sviluppatori di linguaggio 8]: http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html
[JAX-r]: https://jax-rs-spec.java.net/
[Maven]: https://maven.apache.org/
[Microsoft Azure]: https://azure.microsoft.com/
[Editor Swagger online]: http://editor.swagger.io/
[Postman]: https://www.getpostman.com/
[Spazio di archiviazione SDK per Java]: ../storage/storage-java-how-to-use-blob-storage.md
[Swagger]: http://swagger.io/
[Editor swagger]: http://editor.swagger.io/
[Codice Visual Studio]: https://code.visualstudio.com

<!-- IMG List -->

[paste-json]: ./media/app-service-api-java-api-app/paste-json.png
[pasted-swagger]: ./media/app-service-api-java-api-app/pasted-swagger.png
[view-swagger-generated-docs]: ./media/app-service-api-java-api-app/view-swagger-generated-docs.png
[generate-code-menu-item]: ./media/app-service-api-java-api-app/generate-code-menu-item.png
[open-contact-model-file]: ./media/app-service-api-java-api-app/open-contact-model-file.png
[open-contact-service-code-file]: ./media/app-service-api-java-api-app/open-contact-service-code-file.png
[run-jetty-war]: ./media/app-service-api-java-api-app/run-jetty-war.png
[calling-contacts-api]: ./media/app-service-api-java-api-app/calling-contacts-api.png
[calling-specific-contact-api]: ./media/app-service-api-java-api-app/calling-specific-contact-api.png
[create-api-app]: ./media/app-service-api-java-api-app/create-api-app.png
[set-up-java]: ./media/app-service-api-java-api-app/set-up-java.png
[deployment-credentials]: ./media/app-service-api-java-api-app/deployment-credentials.png
[select-git-repo]: ./media/app-service-api-java-api-app/select-git-repo.png
[copy-git-repo-url]: ./media/app-service-api-java-api-app/copy-git-repo-url.png
[postman-calling-azure-contacts]: ./media/app-service-api-java-api-app/postman-calling-azure-contacts.png
