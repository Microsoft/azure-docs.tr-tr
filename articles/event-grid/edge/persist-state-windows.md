---
title: Windows-Azure Event Grid IoT Edge durumu devam ettir | Microsoft Docs
description: Windows 'da durumu kalıcı yap
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 485c6d4a92539a2ba67aece319c68d31649e8045
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992268"
---
# <a name="persist-state-in-windows"></a>Windows 'da durumu kalıcı yap

Event Grid modülünde oluşturulan konular ve abonelikler, varsayılan olarak kapsayıcı dosya sisteminde saklanır. Kalıcı olmadan, modül yeniden dağıtılırsa oluşturulan tüm meta veriler kaybedilir. Dağıtımlar genelinde verileri korumak için verileri kapsayıcı dosya sisteminin dışında kalıcı hale getirmeniz gerekecektir. Şu anda yalnızca meta veriler kalıcıdır. Olaylar bellek içinde depolanır. Event Grid modülü yeniden dağıtılırsa veya yeniden başlatılırsa, teslim edilmemiş tüm olaylar kaybedilir.

Bu makalede, Windows dağıtımlarında kalıcılığı olan Event Grid modülünü dağıtmak için gereken adımlar sağlanmaktadır.

> [!NOTE]
>Event Grid modülü Windows 'da düşük ayrıcalıklı bir Kullanıcı **containeruser** olarak çalışır.

## <a name="persistence-via-volume-mount"></a>Birim bağlama aracılığıyla Kalıcılık

[Docker birimleri](https://docs.docker.com/storage/volumes/) , dağıtımları genelinde verileri korumak için kullanılır. Bir birimi bağlamak için Docker komutlarını kullanarak oluşturmanız gerekir, kapsayıcının bu komutları okuyabilmesi, yazabilmesi ve sonra modülün dağıtılması için izin vermeniz gerekir. Windows üzerinde gerekli izinlere sahip bir birimi otomatik olarak oluşturmak için bir sağlama yoktur. Dağıtılmadan önce oluşturulması gerekir.

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
    1. **Bağlama noktası**tarafından işaret edilen klasöre gidin.
    1. Sağ tıklayın ve ardından **Özellikler**' i seçin.
    1. **Güvenlik**' i seçin.
    1. Grup veya Kullanıcı adları altında **Düzenle**' yi seçin.
    1. **Ekle**' yi seçin, `Users`girin, **adları denetle**öğesini seçin ve **Tamam**' ı seçin.
    1. *Kullanıcılar Için izinler*altında **Değiştir**' i seçin ve **Tamam**' ı seçin.
1. Bu birimi bağlamak ve Azure portal Event Grid modülünü yeniden dağıtmak için **bağlamalar** kullanın

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
   >Bağlama değerinin ikinci kısmını değiştirmeyin. Modüldeki belirli bir konuma işaret eder. Windows üzerinde Event Grid modülü için, **C:\\app\\metadataDb**olmalıdır.


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
                "myeventgridvol:C:\\app\\metadataDb"
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

Alternatif olarak, konak sisteminde bir dizin oluşturmayı ve bu dizini bağlamaya da seçim yapabilirsiniz.

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
    >Bağlama değerinin ikinci kısmını değiştirmeyin. Modüldeki belirli bir konuma işaret eder. Windows üzerinde Event Grid modülü için, **C:\\app\\metadataDb**olmalıdır.

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
                "C:\\myhostdir:C:\\app\\metadataDb"
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
