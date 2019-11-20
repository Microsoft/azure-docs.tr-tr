---
title: AKS yığınlarınız için sistem durumu araştırmaları ekleyin
description: Bu makalede, aks Pod 'ye bir Application Gateway ile sistem durumu araştırmalarının (hazır olma ve/veya öğrenimlilik) nasıl ekleneceği hakkında bilgi verilmektedir.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 5d0543a3a43d53e462a6406312faddf37d2653c6
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73795589"
---
# <a name="add-health-probes-to-your-service"></a>Hizmetinize sistem durumu araştırmaları ekleyin
Varsayılan olarak, giriş denetleyicisi, sunulan pods 'ler için bir HTTP GET araştırması sağlayacak.
Araştırma özellikleri, `deployment`/`pod` spec 'e bir [hazır olma veya bir araştırma](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) eklenerek özelleştirilebilir.

## <a name="with-readinessprobe-or-livenessprobe"></a>`readinessProbe` veya `livenessProbe` ile
```yaml
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: aspnetapp
spec:
  replicas: 3
  template:
    metadata:
      labels:
        service: site
    spec:
      containers:
      - name: aspnetapp
        image: mcr.microsoft.com/dotnet/core/samples:aspnetapp
        imagePullPolicy: IfNotPresent
        ports:
        - containerPort: 80
        readinessProbe:
          httpGet:
            path: /
            port: 80
          periodSeconds: 3
          timeoutSeconds: 1
```

Kubernetes API başvurusu:
* [Kapsayıcı araştırmaları](https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle/#container-probes)
* [HttpGet eylemi](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.14/#httpgetaction-v1-core)

> [!NOTE]
> * `readinessProbe` ve `livenessProbe` `httpGet`ile yapılandırıldığında desteklenir.
> * Pod üzerinde açığa çıkarılan bir bağlantı noktasında araştırma Şu anda desteklenmiyor.
> * `HttpHeaders`, `InitialDelaySeconds``SuccessThreshold` desteklenmez.

##  <a name="without-readinessprobe-or-livenessprobe"></a>`readinessProbe` veya `livenessProbe` olmadan
Yukarıdaki yoklamalar sağlanmamışsa, giriş denetleyicisi, hizmetin `backend-path-prefix` ek açıklaması için belirtilen `Path` veya hizmetin `ingress` tanımında belirtilen `path` için ulaşılabildiğini bir varsayım yapar.

## <a name="default-values-for-health-probe"></a>Sistem durumu araştırması için varsayılan değerler
Hazırlık/lizlilik araştırması tarafından çıkarsanmayan tüm özellikler için varsayılan değerler ayarlanır.

| Application Gateway araştırma özelliği | Varsayılan değer |
|-|-|
| `Path` | / |
| `Host` | e |
| `Protocol` | HTTP |
| `Timeout` | 30 |
| `Interval` | 30 |
| `UnhealthyThreshold` | 3 |