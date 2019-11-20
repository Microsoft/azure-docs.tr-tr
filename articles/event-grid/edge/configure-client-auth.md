---
title: Gelen çağrılar için istemci kimlik doğrulamasını Yapılandırma-Azure Event Grid IoT Edge | Microsoft Docs
description: IoT Edge Event Grid tarafından açığa çıkarılan API protokollerini yapılandırın.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: b5456130e89bf77e2c2ba41880323e38f6b27f4c
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992515"
---
# <a name="configure-client-authentication-of-incoming-calls"></a>Gelen çağrıların istemci kimlik doğrulamasını yapılandırma

Bu kılavuzda, Event Grid modülü için olası istemci kimlik doğrulama yapılandırmalarının örnekleri verilmiştir. Event Grid modülü iki tür istemci kimlik doğrulamasını destekler:-

* Paylaşılan erişim imzası (SAS) anahtar tabanlı
* Sertifika tabanlı

Tüm olası yapılandırmalara yönelik [güvenlik ve kimlik doğrulama Kılavuzu '](security-authentication.md) na bakın.

## <a name="enable-certificate-based-client-authentication-no-self-signed-certificates"></a>Sertifika tabanlı istemci kimlik doğrulamasını etkinleştirme, otomatik olarak imzalanan sertifika yok

```json
 {
  "Env": [
    "inbound:clientAuth:sasKeys:enabled=false",
    "inbound:clientAuth:clientCert:enabled=true",
    "inbound:clientAuth:clientCert:source=IoTEdge",
    "inbound:clientAuth:clientCert:allowUnknownCA=false"
  ]
}
 ```

## <a name="enable-certificate-based-client-authentication-allow-self-signed-certificates"></a>Sertifika tabanlı istemci kimlik doğrulamasını etkinleştirme, otomatik olarak imzalanan sertifikalara izin verme

```json
 {
  "Env": [
    "inbound:clientAuth:sasKeys:enabled=false",
    "inbound:clientAuth:clientCert:enabled=true",
    "inbound:clientAuth:clientCert:source=IoTEdge",
    "inbound:clientAuth:clientCert:allowUnknownCA=true"
  ]
}
```

>[!NOTE]
>Genellikle otomatik olarak imzalanan sertifikalar kullanıyorsanız, **gelen: clientAuth: clientCert: allowUnknownCA** özelliğini yalnızca test ortamlarında **true** değerine ayarlayın. Üretim iş yükleri için, bu özelliği **yanlış** ve bir sertifika YETKILISINDEN (CA) sertifikalar olarak ayarlamanızı öneririz.

## <a name="enable-certificate-based-and-sas-key-based-client-authentication"></a>Sertifika tabanlı ve SAS anahtar tabanlı istemci kimlik doğrulamasını etkinleştir

```json
 {
  "Env": [
    "inbound:clientAuth:sasKeys:enabled=true",
    "inbound:clientAuth:sasKeys:key1=<some-secret1-here>",
    "inbound:clientAuth:sasKeys:key2=<some-secret2-here>",
    "inbound:clientAuth:clientCert:enabled=true",
    "inbound:clientAuth:clientCert:source=IoTEdge",
    "inbound:clientAuth:clientCert:allowUnknownCA=true"
  ]
}
 ```

>[!NOTE]
>SAS anahtar tabanlı istemci kimlik doğrulaması, IoT olmayan bir uç modülün yönetim ve çalışma zamanı işlemlerine izin verir ve bu da API bağlantı noktalarına IoT Edge ağı dışından erişilebilir.
