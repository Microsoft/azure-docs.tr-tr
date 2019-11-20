---
title: Linux 'ta kalıcı durum Azure Event Grid IoT Edge | Microsoft Docs
description: Linux 'ta meta verileri kalıcı hale getirme
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 3506399537fe2cb16014ceb3429bce5aeee8cb69
ms.sourcegitcommit: b45ee7acf4f26ef2c09300ff2dba2eaa90e09bc7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73100328"
---
# <a name="persist-state-in-linux"></a>Linux 'ta durumu kalıcı yap

Event Grid modülünde oluşturulan konular ve abonelikler, varsayılan olarak kapsayıcı dosya sisteminde saklanır. Kalıcı olmadan, modül yeniden dağıtılırsa oluşturulan tüm meta veriler kaybedilir. Şu anda yalnızca meta veriler kalıcıdır. Olaylar bellek içinde depolanır. Event Grid modülü yeniden dağıtılırsa veya yeniden başlatılırsa, teslim edilmemiş tüm olaylar kaybedilir.

Bu makalede, Event Grid modülünü Linux dağıtımlarında kalıcı hale getirme adımları sağlanmaktadır.

> [!NOTE]
>Event Grid modülü UID `2000` ve ad `eventgriduser`olan düşük ayrıcalıklı bir kullanıcı olarak çalışır.

## <a name="persistence-via-volume-mount"></a>Birim bağlama aracılığıyla Kalıcılık

 [Docker birimleri](https://docs.docker.com/storage/volumes/) , dağıtımları genelinde verileri korumak için kullanılır. Event Grid modülünü dağıtmanın bir parçası olarak Docker 'ın adlandırılmış bir birimi otomatik olarak oluşturmasını sağlayabilirsiniz. Bu seçenek en basit seçenektir. **Bağlar** bölümünde oluşturulacak birim adını aşağıdaki şekilde belirtebilirsiniz:

```json
  {
     "HostConfig": {
          "Binds": [
                 "<your-volume-name-here>:/app/metadataDb"
           ]
      }
   }
```

>[!IMPORTANT]
>Bağlama değerinin ikinci kısmını değiştirmeyin. Modül içindeki belirli bir konuma işaret eder. Linux üzerinde Event Grid modülü için, **/App/metadatadb**olmalıdır.

Örneğin, aşağıdaki yapılandırma, meta verilerin kalıcı olacağı, **Yumurmetadatadbvol** birimi oluşturulmasına neden olur.

```json
 {
  "Env": [
    "inbound:serverAuth:tlsPolicy=strict",
    "inbound:serverAuth:serverCert:source=IoTEdge",
    "inbound:clientAuth:sasKeys:enabled=false",
    "inbound:clientAuth:clientCert:enabled=true",
    "inbound:clientAuth:clientCert:source=IoTEdge",
    "inbound:clientAuth:clientCert:allowUnknownCA=true",
    "outbound:clientAuth:clientCert:enabled=true",
    "outbound:clientAuth:clientCert:source=IoTEdge",
    "outbound:webhook:httpsOnly=true",
    "outbound:webhook:skipServerCertValidation=false",
    "outbound:webhook:allowUnknownCA=true"
  ],
  "HostConfig": {
    "Binds": [
      "egmetadataDbVol:/app/metadataDb"
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

Alternatif olarak, Docker istemci komutlarını kullanarak bir Docker birimi de oluşturabilirsiniz. 

## <a name="persistence-via-host-directory-mount"></a>Konak Dizin bağlama aracılığıyla Kalıcılık

Bir Docker birimi yerine, bir ana bilgisayar klasörü bağlama seçeneğiniz de vardır.

1. İlk olarak, aşağıdaki komutu çalıştırarak **eventgriduser** ve ID **2000** adlı bir kullanıcı oluşturun:

    ```sh
    sudo useradd -u 2000 eventgriduser
    ```
1. Aşağıdaki komutu çalıştırarak konak dosyası sisteminde bir dizin oluşturun.

   ```sh
   md <your-directory-name-here>
   ```

    Örneğin, aşağıdaki komutu çalıştırmak **myhostdir**adlı bir dizin oluşturur.

    ```sh
    md /myhostdir
    ```
1. Sonra, aşağıdaki komutu çalıştırarak **eventgriduser** sahibini bu klasörün sahibi yapın.

   ```sh
   sudo chown eventgriduser:eventgriduser -hR <your-directory-name-here>
   ```

    Örneğin,

    ```sh
    sudo chown eventgriduser:eventgriduser -hR /myhostdir
    ```
1. Dizini bağlamak ve Event Grid modülünü Azure portal yeniden dağıtmak için **bağlamalar** ' i kullanın.

    ```json
    {
         "HostConfig": {
            "Binds": [
                "<your-directory-name-here>:/app/metadataDb"
             ]
         }
    }
    ```

    Örneğin,

    ```json
    {
          "Env": [
            "inbound:serverAuth:tlsPolicy=strict",
            "inbound:serverAuth:serverCert:source=IoTEdge",
            "inbound:clientAuth:sasKeys:enabled=false",
            "inbound:clientAuth:clientCert:enabled=true",
            "inbound:clientAuth:clientCert:source=IoTEdge",
            "inbound:clientAuth:clientCert:allowUnknownCA=true",
            "outbound:clientAuth:clientCert:enabled=true",
            "outbound:clientAuth:clientCert:source=IoTEdge",
            "outbound:webhook:httpsOnly=true",
            "outbound:webhook:skipServerCertValidation=false",
            "outbound:webhook:allowUnknownCA=true"
          ],
          "HostConfig": {
                "Binds": [
                  "/myhostdir:/app/metadataDb"
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
    >Bağlama değerinin ikinci kısmını değiştirmeyin. Modül içindeki belirli bir konuma işaret eder. Linux üzerinde Event Grid modülü için, **/App/Metadata**olmalıdır.
