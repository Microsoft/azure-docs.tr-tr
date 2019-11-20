---
title: 'Hızlı başlangıç: C-Azure Event Hubs kullanarak olay gönderme'
description: 'Hızlı başlangıç: Bu makale, Azure Event Hubs olayları gönderen bir C uygulaması oluşturmaya yönelik bir yol sağlar.'
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: c
ms.devlang: csharp
ms.topic: quickstart
ms.custom: seodec18
ms.date: 11/05/2019
ms.author: shvija
ms.openlocfilehash: 5bd4bb66b7e3c3ec37724f8684105befbc9132ff
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73720667"
---
# <a name="quickstart-send-events-to-azure-event-hubs-using-c"></a>Hızlı başlangıç: C kullanarak Azure Event Hubs olayları gönderme

## <a name="introduction"></a>Giriş
Azure Event Hubs saniyede milyonlarca olay alıp işleme kapasitesine sahip olan bir Büyük Veri akış platformu ve olay alma hizmetidir. Event Hubs dağıtılan yazılımlar ve cihazlar tarafından oluşturulan olayları, verileri ve telemetrileri işleyebilir ve depolayabilir. Bir olay hub’ına gönderilen veriler, herhangi bir gerçek zamanlı analiz sağlayıcısı ve işlem grubu oluşturma/depolama bağdaştırıcıları kullanılarak dönüştürülüp depolanabilir. Olay Hub’larının ayrıntılı genel bakışı için bkz. [Olay Hub’larına genel bakış](event-hubs-about.md) ve [Olay Hub’ları özellikleri](event-hubs-features.md).

Bu öğreticide, C 'deki bir konsol uygulaması kullanılarak Olay Hub 'ına olayların nasıl gönderileceği açıklanmaktadır. 

## <a name="prerequisites"></a>Ön koşullar
Bu öğreticiyi tamamlamak için aşağıdakiler gerekir:

* C geliştirme ortamı. Bu öğreticide, Ubuntu 14,04 ile bir Azure Linux sanal makinesinde GCC yığını varsayılmaktadır.
* [Microsoft Visual Studio](https://www.visualstudio.com/).
* **Event Hubs bir ad alanı ve bir olay hub 'ı oluşturun**. Event Hubs türünde bir ad alanı oluşturmak ve uygulamanızın Olay Hub 'ı ile iletişim kurması için gereken yönetim kimlik bilgilerini almak için [Azure Portal](https://portal.azure.com) kullanın. Bir ad alanı ve Olay Hub 'ı oluşturmak için [Bu makaledeki](event-hubs-create.md)yordamı izleyin. Makaledeki yönergeleri izleyerek Olay Hub 'ı için erişim anahtarı değerini alın: [bağlantı dizesi al](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Bu öğreticide daha sonra yazdığınız kodda erişim anahtarını kullanın. Varsayılan anahtar adı: **RootManageSharedAccessKey**.

## <a name="write-code-to-send-messages-to-event-hubs"></a>Event Hubs ileti göndermek için kod yazma
Bu bölümde, Olay Hub 'ınıza olayları göndermek için bir C uygulamasının nasıl yazılacağı gösterilmektedir. Kod, [Apache Qpid projesinden](https://qpid.apache.org/)proton AMQP kitaplığını kullanır. Bu [örnek, bu örnekte](https://code.msdn.microsoft.com/Using-Apache-Qpid-Proton-C-afd76504)gösterildiği gibi C 'den AMQP ile Service Bus kuyrukları ve konuları kullanmakla benzerdir. Daha fazla bilgi için bkz. [Qpid proton belgeleri](https://qpid.apache.org/proton/index.html).

1. [QPID AMQP Messenger sayfasından](https://qpid.apache.org/proton/messenger.html)ortamınıza bağlı olarak Qpid proton 'yi yüklemek için yönergeleri izleyin.
2. Proton kitaplığını derlemek için aşağıdaki paketleri yüklemelisiniz:
   
    ```shell
    sudo apt-get install build-essential cmake uuid-dev openssl libssl-dev
    ```
3. [Qpid proton kitaplığını](https://qpid.apache.org/proton/index.html)indirip ayıklayın, örneğin:
   
    ```shell
    wget https://archive.apache.org/dist/qpid/proton/0.7/qpid-proton-0.7.tar.gz
    tar xvfz qpid-proton-0.7.tar.gz
    ```
4. Derleme dizini oluşturun, derleyin ve yükler:
   
    ```shell
    cd qpid-proton-0.7
    mkdir build
    cd build
    cmake -DCMAKE_INSTALL_PREFIX=/usr ..
    sudo make install
    ```
5. İş dizininizde, aşağıdaki kodla **Sender. c** adlı yeni bir dosya oluşturun. SAS anahtarınız/adınız, Olay Hub 'ınızın adı ve ad alanınız için değerleri değiştirmeyi unutmayın. Ayrıca, daha önce oluşturulan **Sendrule** IÇIN anahtarın URL kodlu bir sürümünü de yerine kullanmalısınız. [Burada](https://www.w3schools.com/tags/ref_urlencode.asp)URL 'yi kodlayabilirsiniz.
   
    ```c
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
        char * address = (char *) "amqps://{SAS Key Name}:{SAS key}@{namespace name}.servicebus.windows.net/{event hub name}";
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
6. Dosyayı Derle, **GCC**varsayılıyor:
   
    ```
    gcc sender.c -o sender -lqpid-proton
    ```

    > [!NOTE]
    > Bu kod, iletileri mümkün olan en kısa sürede zorlamak için 1 giden bir pencere kullanır. Uygulamanızın aktarım hızını artırmak için Batch iletilerini denemesini öneririz. Bu ve diğer ortamlarda Qpid proton kitaplığını kullanma ve bağlamaların sağlandığı platformlar (Şu anda Perl, PHP, Python ve Ruby) hakkında bilgi edinmek için [QPID AMQP Messenger sayfasına](https://qpid.apache.org/proton/messenger.html) bakın.

Olay Hub 'ına ileti göndermek için uygulamayı çalıştırın. 

Tebrikler! Bir olay hub'ına ileti gönderdiniz.

## <a name="next-steps"></a>Sonraki adımlar
Aşağıdaki makaleleri okuyun:

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Azure Event Hubs Özellikler ve terminoloji](event-hubs-features.md).


<!-- Images. -->
[21]: ./media/event-hubs-c-ephcs-getstarted/run-csharp-ephcs1.png
[24]: ./media/event-hubs-c-ephcs-getstarted/receive-eph-c.png
