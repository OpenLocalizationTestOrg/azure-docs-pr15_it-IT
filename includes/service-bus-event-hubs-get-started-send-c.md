## <a name="send-messages-to-event-hubs"></a>Inviare messaggi a un hub di evento

In questa sezione verrà scritto un'app C per inviare gli eventi per l'Hub di evento. Verrà usata la raccolta di Proton AMQP dal [progetto Apache Qpid](http://qpid.apache.org/). Questo è analogo all'utilizzo di code Bus di servizio e argomenti con AMQP da C come illustrato [di seguito](https://code.msdn.microsoft.com/Using-Apache-Qpid-Proton-C-afd76504). Per ulteriori informazioni, vedere [la documentazione Qpid Proton](http://qpid.apache.org/proton/index.html).

1. Nella [pagina Qpid AMQP Messenger](http://qpid.apache.org/components/messenger/index.html), fare clic sul collegamento **Qpid Proton l'installazione** e seguire le istruzioni a seconda dell'ambiente. Si supponga di un ambiente Linux; ad esempio una [Macchina virtuale Linux Azure](../articles/virtual-machines/virtual-machines-linux-quick-create-cli.md) con Ubuntu 14.04.

2. Per compilare la libreria Proton, installare i pacchetti seguenti:

    ```
    sudo apt-get install build-essential cmake uuid-dev openssl libssl-dev
    ```

3. Scaricare la [Qpid Proton](http://qpid.apache.org/proton/index.html) library ed estrarre, ad esempio:

    ```
    wget http://archive.apache.org/dist/qpid/proton/0.7/qpid-proton-0.7.tar.gz
    tar xvfz qpid-proton-0.7.tar.gz
    ```

4. Creare una directory di generazione, compilazione e installare:

    ```
    cd qpid-proton-0.7
    mkdir build
    cd build
    cmake -DCMAKE_INSTALL_PREFIX=/usr ..
    sudo make install
    ```

5. Creare un nuovo file denominato **sender.c** con il seguente contenuto nella cartella di lavoro. Ricordarsi di sostituire il valore per il nome dell'evento Hub e dello spazio dei nomi (la seconda è in genere `{event hub name}-ns`). È anche necessario sostituire una versione con codifica URL della chiave per **SendRule** creata in precedenza. È possibile codificare URL è [qui](http://www.w3schools.com/tags/ref_urlencode.asp).

    ```
    #include "proton/message.h"
    #include "proton/messenger.h"

    #include <getopt.h>
    #include <proton/util.h>
    #include <sys/time.h>
    #include <stddef.h>
    #include <stdio.h>
    #include <string.h>
    #include <unistd.h>
    #include <stdlib.h>

    #define check(messenger)                                                     \
      {                                                                          \
        if(pn_messenger_errno(messenger))                                        \
        {                                                                        \
          printf("check\n");                                                     \
          die(__FILE__, __LINE__, pn_error_text(pn_messenger_error(messenger))); \
        }                                                                        \
      }  

    pn_timestamp_t time_now(void)
    {
      struct timeval now;
      if (gettimeofday(&now, NULL)) pn_fatal("gettimeofday failed\n");
      return ((pn_timestamp_t)now.tv_sec) * 1000 + (now.tv_usec / 1000);
    }  

    void die(const char *file, int line, const char *message)
    {
      printf("Dead\n");
      fprintf(stderr, "%s:%i: %s\n", file, line, message);
      exit(1);
    }

    int sendMessage(pn_messenger_t * messenger) {
        char * address = (char *) "amqps://SendRule:{Send Rule key}@{namespace name}.servicebus.windows.net/{event hub name}";
        char * msgtext = (char *) "Hello from C!";

        pn_message_t * message;
        pn_data_t * body;
        message = pn_message();

        pn_message_set_address(message, address);
        pn_message_set_content_type(message, (char*) "application/octect-stream");
        pn_message_set_inferred(message, true);

        body = pn_message_body(message);
        pn_data_put_binary(body, pn_bytes(strlen(msgtext), msgtext));

        pn_messenger_put(messenger, message);
        check(messenger);
        pn_messenger_send(messenger, 1);
        check(messenger);

        pn_message_free(message);
    }

    int main(int argc, char** argv) {
        printf("Press Ctrl-C to stop the sender process\n");

        pn_messenger_t *messenger = pn_messenger(NULL);
        pn_messenger_set_outgoing_window(messenger, 1);
        pn_messenger_start(messenger);

        while(true) {
            sendMessage(messenger);
            printf("Sent message\n");
            sleep(1);
        }

        // release messenger resources
        pn_messenger_stop(messenger);
        pn_messenger_free(messenger);

        return 0;
    }
    ```

6. Compilare il file, presupponendo che **gcc**:

    ```
    gcc sender.c -o sender -lqpid-proton
    ```

> [AZURE.NOTE] In questo codice, una finestra in uscita di 1 vengono utilizzati per forzare presto messaggi in uscita. In generale, l'applicazione tentare di messaggi batch per aumentare la velocità. Per altre informazioni su come utilizzare la libreria Qpid Proton in questo e altri ambienti e da piattaforme per il quale vengono fornite le associazioni, vedere [pagina Qpid AMQP Messenger](http://qpid.apache.org/components/messenger/index.html) (attualmente Perl, PHP, Python e trascrizione).
