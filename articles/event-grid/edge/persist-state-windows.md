---
title: Windows-Azure Event Grid IoT Edge durumu devam ettir | Microsoft Docs
description: Windows 'da durumu kalıcı yap
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: f38e23a3af1e2c81ee012a4f3c268cbff3fc1bee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96002826"
---
# <a name="persist-state-in-windows"></a>Windows 'da durumu kalıcı yap

Event Grid modülünde oluşturulan konular ve abonelikler varsayılan olarak kapsayıcı dosya sisteminde saklanır. Kalıcı olmadan, modül yeniden dağıtılırsa oluşturulan tüm meta veriler kaybedilir. Dağıtımlar ve yeniden başlatmalar genelinde verileri korumak için verileri kapsayıcı dosya sisteminin dışında kalıcı hale getirmeniz gerekir. 

Varsayılan olarak yalnızca meta veriler kalıcıdır ve olaylar, daha iyi performans için bellekte depolanır. Olay kalıcılığını da sağlamak için kalıcı olaylar bölümünü izleyin.

Bu makalede, Windows dağıtımlarında kalıcılığı olan Event Grid modülünü dağıtmak için gereken adımlar sağlanmaktadır.

> [!NOTE]
>Event Grid modülü Windows 'da düşük ayrıcalıklı bir Kullanıcı **containeruser** olarak çalışır.

## <a name="persistence-via-volume-mount"></a>Birim bağlama aracılığıyla Kalıcılık

[Docker birimleri](https://docs.docker.com/storage/volumes/) , dağıtımları genelinde verileri korumak için kullanılır. Bir birimi bağlamak için Docker komutlarını kullanarak oluşturmanız gerekir, kapsayıcının bu komutları okuyabilmesi, yazabilmesi ve sonra modülün dağıtılması için izin vermeniz gerekir.

1. Aşağıdaki komutu çalıştırarak bir birim oluşturun:

    ```sh
    docker -H npipe:////./pipe/iotedge_moby_engine volume create <your-volume-name-here>
    ```

    Örneğin,

   ```sh
   docker -H npipe:////./pipe/iotedge_moby_engine volume create myeventgridvol
   ```
1. Aşağıdaki komutu çalıştırarak birimin eşlendiği konak dizinini alın

    ```sh
    docker -H npipe:////./pipe/iotedge_moby_engine volume inspect <your-volume-name-here>
    ```

    Örneğin,

   ```sh
   docker -H npipe:////./pipe/iotedge_moby_engine volume inspect myeventgridvol
   ```

   Örnek çıkış:-

   ```json
   [
          {
            "CreatedAt": "2019-07-30T21:20:59Z",
            "Driver": "local",
            "Labels": {},
            "Mountpoint": "C:\\ProgramData\\iotedge-moby\u000bolumes\\myeventgridvol\\_data",
            "Name": "myeventgridvol",
            "Options": {},
            "Scope": "local"
          }
   ]
   ```
1. **Bağlama noktası** tarafından işaret edilen değere **Kullanıcı** grubunu aşağıdaki gibi ekleyin:
    1. Dosya gezginini başlatın.
    1. **Bağlama noktası** tarafından işaret edilen klasöre gidin.
    1. Sağ tıklayın ve ardından **Özellikler**' i seçin.
    1. **Güvenlik**’i seçin.
    1. Grup veya Kullanıcı adları altında **Düzenle**' yi seçin.
    1. **Ekle**' yi seçin, `Users` **adları denetle**' yi seçin ve **Tamam**' ı seçin.
    1. *Kullanıcılar Için izinler* altında **Değiştir**' i seçin ve **Tamam**' ı seçin.
1. Bu birimi bağlamak ve Azure portal Event Grid modülünü yeniden dağıtmak için **bağlamalar** kullanın

   Örneğin,

    ```json
        {
              "Env": [
                "inbound__serverAuth__tlsPolicy=strict",
                "inbound__serverAuth__serverCert__source=IoTEdge",
                "inbound__clientAuth__sasKeys__enabled=false",
                "inbound__clientAuth__clientCert__enabled=true",
                "inbound__clientAuth__clientCert__source=IoTEdge",
                "inbound__clientAuth__clientCert__allowUnknownCA=true",
                "outbound__clientAuth__clientCert__enabled=true",
                "outbound__clientAuth__clientCert__source=IoTEdge",
                "outbound__webhook__httpsOnly=true",
                "outbound__webhook__skipServerCertValidation=false",
                "outbound__webhook__allowUnknownCA=true"
              ],
              "HostConfig": {
                "Binds": [
                  "<your-volume-name-here>:C:\\app\\metadataDb"
                ],
                "PortBindings": {
                  "4438/tcp": [
                     {
                        "HostPort": "4438"
                     }
                  ]
                }
              }
        }
    ```

   >[!IMPORTANT]
   >Bağlama değerinin ikinci kısmını değiştirmeyin. Modüldeki belirli bir konuma işaret eder. Windows üzerinde Event Grid modülü için, **C: \\ App \\ metadatadb** olmalıdır.


    Örneğin,

    ```json
    {
        "Env": [
            "inbound__serverAuth__tlsPolicy=strict",
            "inbound__serverAuth__serverCert__source=IoTEdge",
            "inbound__clientAuth__sasKeys__enabled=false",
            "inbound__clientAuth__clientCert__enabled=true",
            "inbound__clientAuth__clientCert__source=IoTEdge",
            "inbound__clientAuth__clientCert__allowUnknownCA=true",
            "outbound__clientAuth__clientCert__enabled=true",
            "outbound__clientAuth__clientCert__source=IoTEdge",
            "outbound__webhook__httpsOnly=true",
            "outbound__webhook__skipServerCertValidation=false",
            "outbound__webhook__allowUnknownCA=true"
         ],
         "HostConfig": {
            "Binds": [
                "myeventgridvol:C:\\app\\metadataDb",
                "C:\\myhostdir2:C:\\app\\eventsDb"
             ],
             "PortBindings": {
                    "4438/tcp": [
                         {
                            "HostPort": "4438"
                          }
                    ]
              }
         }
    }
    ```

## <a name="persistence-via-host-directory-mount"></a>Konak Dizin bağlama aracılığıyla Kalıcılık

Bir birim bağlamak yerine, ana bilgisayar sisteminde bir dizin oluşturabilir ve bu dizini bağlayabilirsiniz.

1. Aşağıdaki komutu çalıştırarak konak dosya sisteminde bir dizin oluşturun.

   ```sh
   mkdir <your-directory-name-here>
   ```

   Örneğin,

   ```sh
   mkdir C:\myhostdir
   ```
1. Dizininizi bağlamak ve Event Grid modülünü Azure portal yeniden dağıtmak için **bağlamalar** ' i kullanın.

    ```json
    {
         "HostConfig": {
            "Binds": [
                "<your-directory-name-here>:C:\\app\\metadataDb"
             ]
         }
    }
    ```

    >[!IMPORTANT]
    >Bağlama değerinin ikinci kısmını değiştirmeyin. Modüldeki belirli bir konuma işaret eder. Windows üzerinde Event Grid modülü için, **C: \\ App \\ metadatadb** olmalıdır.

    Örneğin,

    ```json
    {
        "Env": [
            "inbound__serverAuth__tlsPolicy=strict",
            "inbound__serverAuth__serverCert__source=IoTEdge",
            "inbound__clientAuth__sasKeys__enabled=false",
            "inbound__clientAuth__clientCert__enabled=true",
            "inbound__clientAuth__clientCert__source=IoTEdge",
            "inbound__clientAuth__clientCert__allowUnknownCA=true",
            "outbound__clientAuth__clientCert__enabled=true",
            "outbound__clientAuth__clientCert__source=IoTEdge",
            "outbound__webhook__httpsOnly=true",
            "outbound__webhook__skipServerCertValidation=false",
            "outbound__webhook__allowUnknownCA=true"
         ],
         "HostConfig": {
            "Binds": [
                "C:\\myhostdir:C:\\app\\metadataDb",
                "C:\\myhostdir2:C:\\app\\eventsDb"
             ],
             "PortBindings": {
                    "4438/tcp": [
                         {
                            "HostPort": "4438"
                          }
                    ]
              }
         }
    }
    ```
## <a name="persist-events"></a>Etkinlikleri kalıcı yap

Olay kalıcılığını etkinleştirmek için, önce yukarıdaki bölümleri kullanarak birim bağlama veya ana bilgisayar Dizin bağlama aracılığıyla olay kalıcılığını etkinleştirmeniz gerekir.

Kalıcı olaylar hakkında dikkat etmeniz gereken önemli noktalar:

* Kalıcı olaylar, olay başına abonelik temelinde etkinleştirilir ve bir birim veya dizin bağlandıktan sonra kabul edilir.
* Olay kalıcılığı, oluşturma zamanında bir olay aboneliğinde yapılandırılır ve olay aboneliği oluşturulduktan sonra değiştirilemez. Olay kalıcılığını değiştirmek için olay aboneliğini silip yeniden oluşturmanız gerekir.
* Kalıcı olaylar, bellek işlemlerinden neredeyse her zaman daha yavaştır, ancak hız farkı, sürücünün özelliklerine oldukça bağlıdır. Hız ve güvenilirlik arasındaki zorunluluğunu getirir tüm mesajlaşma sistemlerine sahiptir ancak büyük ölçekte yalnızca fark edilebilir hale gelir.

Olay aboneliğindeki olay kalıcılığını etkinleştirmek için şu `persistencePolicy` şekilde ayarlayın `true` :

 ```json
        {
          "properties": {
            "persistencePolicy": {
              "isPersisted": "true"
            },
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "<your-webhook-url>"
              }
            }
          }
        }
 ```