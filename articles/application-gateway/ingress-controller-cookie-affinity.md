---
title: Application Gateway tanımlama bilgisine dayalı benzeşimi etkinleştir
description: Bu makalede, tanımlama bilgisi tabanlı Benzeşimin bir Application Gateway nasıl etkinleştirileceği hakkında bilgi sağlanır.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: a7806cf9518090539ba540a9a522af1aae2691f0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93397425"
---
# <a name="enable-cookie-based-affinity-with-an-application-gateway"></a>Application Gateway tanımlama bilgisi tabanlı benzeşimi etkinleştirme
[Azure Application Gateway belgelerinde](./application-gateway-components.md#http-settings)özetlenen Application Gateway, tanımlama bilgisi tabanlı benzeşimi destekler, yani sonraki trafiği işlenmek üzere bir kullanıcı oturumundan aynı sunucuya yönlendirebilir.

## <a name="example"></a>Örnek
```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: guestbook
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/cookie-based-affinity: "true"
spec:
  rules:
  - http:
      paths:
      - backend:
          serviceName: frontend
          servicePort: 80
```