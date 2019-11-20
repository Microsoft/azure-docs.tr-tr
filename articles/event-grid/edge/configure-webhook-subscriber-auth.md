---
title: Web kancası abonesi kimlik doğrulamasını Yapılandırma-Azure Event Grid IoT Edge | Microsoft Docs
description: Web kancası abone kimlik doğrulamasını yapılandırma
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 97ed1e2ad84d895e9da0d96cd070e14acb46385d
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992489"
---
# <a name="configure-webhook-subscriber-authentication"></a>Web kancası abone kimlik doğrulamasını yapılandırma

Bu kılavuzda, bir Event Grid modülü için olası Web kancası abone yapılandırmalarının örnekleri verilmiştir. Varsayılan olarak, Web kancası aboneleri için yalnızca HTTPS uç noktaları kabul edilir. Abone, kendinden imzalı bir sertifika sunduğunda Event Grid modülü reddeder.

## <a name="allow-only-https-subscriber"></a>Yalnızca HTTPS abonesi izin ver

```json
 {
  "Env": [
    "outbound:webhook:httpsOnly=true",
    "outbound:webhook:skipServerCertValidation=false",
    "outbound:webhook:allowUnknownCA=false"
  ]
}
 ```

## <a name="allow-https-subscriber-with-self-signed-certificate"></a>Otomatik olarak imzalanan sertifika ile HTTPS abonesi izin ver

```json
 {
  "Env": [
    "outbound:webhook:httpsOnly=true",
    "outbound:webhook:skipServerCertValidation=false",
    "outbound:webhook:allowUnknownCA=true"
  ]
}
 ```

>[!NOTE]
>Genellikle otomatik olarak imzalanan sertifikaları kullanırken, özelliği `outbound:webhook:allowUnknownCA` yalnızca test ortamlarında `true` olarak ayarlayın. Üretim iş yükleri için, bunların **false**olarak ayarlanması önerilir.

## <a name="allow-https-subscriber-but-skip-certificate-validation"></a>HTTPS abonesi izin ver ancak sertifika doğrulamayı atla

```json
 {
  "Env": [
    "outbound:webhook:httpsOnly=true",
    "outbound:webhook:skipServerCertValidation=true",
    "outbound:webhook:allowUnknownCA=false"
  ]
}
 ```

>[!NOTE]
>Kimliği doğrulanabilmesi gereken bir sertifika sunmadığından, özellik `outbound:webhook:skipServerCertValidation` yalnızca test ortamlarında `true` olarak ayarlayın. Üretim iş yükleri için, bunların **false** olarak ayarlanması önerilir

## <a name="allow-both-http-and-https-with-self-signed-certificates"></a>Otomatik olarak imzalanan sertifikalarla hem HTTP hem de HTTPS 'ye izin ver

```json
 {
  "Env": [
    "outbound:webhook:httpsOnly=false",
    "outbound:webhook:skipServerCertValidation=false",
    "outbound:webhook:allowUnknownCA=true"
  ]
}
 ```

>[!NOTE]
>Önce bir HTTP abonesi getirmek isteyebileceğiniz gibi, özelliği `outbound:webhook:httpsOnly` yalnızca test ortamlarında `false` olarak ayarlayın. Üretim iş yükleri için, bunların **true** olarak ayarlanması önerilir
