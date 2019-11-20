---
title: Kimlik Yapılandırma-Azure Event Grid IoT Edge | Microsoft Docs
description: Event Grid modülünün kimliğini yapılandırma
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/05/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 336b6157128468169264d6ffa9564da4d9338aae
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992450"
---
# <a name="configure-identity-for-the-event-grid-module"></a>Event Grid modülü için kimlik yapılandırma

Bu makalede, bir Event Grid modülü için olası kimlik yapılandırmalarına örnekler verilmektedir. Varsayılan olarak Event Grid modülü, kendi kimlik sertifikasını IoT güvenlik arka plan programı tarafından yapılandırılmış olarak sunar. Kimlik sertifikası, olaylar teslim edildiğinde, giden çağrılarında Event Grid modülü tarafından sunulur. Bir Event Grid olayına abone olmak, olayı kabul etmeden önce olayı gönderen Event Grid modülünün gerçekten olduğunu doğrulamayı seçebilir.

Tüm olası yapılandırmalara yönelik [güvenlik ve kimlik doğrulama Kılavuzu '](security-authentication.md) na bakın.

## <a name="always-present-identity-certificate"></a>Her zaman kimlik sertifikası sunun
Giden çağrılar üzerinde her zaman bir kimlik sertifikası sunmaya yönelik örnek bir yapılandırma aşağıda verilmiştir. 

```json
 {
  "Env": [
    "outbound:clientAuth:clientCert:enabled=true",
    "outbound:clientAuth:clientCert:source=IoTEdge"
  ]
}
 ```

## <a name="dont-present-identity-certificate"></a>Kimlik sertifikası sunma
Giden çağrılar üzerinde kimlik sertifikası sunulmayan bir yapılandırma aşağıda verilmiştir. 

```json
 {
  "Env": [
    "outbound:clientAuth:clientCert:enabled=false"
  ]
}
 ```
