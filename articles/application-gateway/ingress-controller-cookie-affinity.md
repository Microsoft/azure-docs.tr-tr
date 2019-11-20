---
title: Application Gateway tanımlama bilgisine dayalı benzeşimi etkinleştir
description: Bu makalede, tanımlama bilgisi tabanlı Benzeşimin bir Application Gateway nasıl etkinleştirileceği hakkında bilgi sağlanır.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: adacd805e736524fd7956c4bbc0ad402980b4cd0
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73795988"
---
# <a name="enable-cookie-based-affinity-with-an-application-gateway"></a>Application Gateway tanımlama bilgisi tabanlı benzeşimi etkinleştirme
[Azure Application Gateway belgelerinde](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#http-settings)özetlenen Application Gateway, tanımlama bilgisi tabanlı benzeşimi destekler, yani sonraki trafiği işlenmek üzere bir kullanıcı oturumundan aynı sunucuya yönlendirebilir.

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
