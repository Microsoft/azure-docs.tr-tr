---
title: Azure Red Hat OpenShift 4 için DNS Iletmeyi yapılandırma
description: Azure Red Hat OpenShift 4 için DNS Iletmeyi yapılandırma
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: 68cc5dbee5b96472d11bdfb3a8bf35d118638dcd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100633878"
---
# <a name="configure-dns-forwarding-on-an-azure-red-hat-openshift-4-cluster"></a>Azure Red Hat OpenShift 4 kümesinde DNS iletmeyi yapılandırma

Azure Red Hat OpenShift kümesinde DNS Iletmeyi yapılandırmak için DNS işlecini değiştirmeniz gerekir. Bu değişiklik, küme içinde çalışan uygulama yığınlarınızın, küme dışındaki özel bir DNS sunucusunda barındırılan adları çözümlemesine izin verir. Bu adımlar, OpenShift 4,6 için [burada](https://docs.openshift.com/container-platform/4.6/networking/dns-operator.html)belgelenmiştir.

Örneğin, *. example.com için tüm DNS isteklerini bir DNS sunucusu 192.168.100.10 tarafından çözümlenecek şekilde iletmek istiyorsanız, şunu çalıştırarak operatör yapılandırmasını düzenleyebilirsiniz:
 
```bash
oc edit dns.operator/default
```
 
Bu, bir düzenleyici başlatacaktır ve şu şekilde değiştirebilirsiniz `spec: {}` :
 
 ```yaml
spec:
  servers:
  - forwardPlugin:
      upstreams:
      - 192.168.100.10
    name: example-dns
    zones:
    - example.com
```

Dosyayı kaydedin ve düzenleyiciden çıkın.

## <a name="next-steps"></a>Sonraki adımlar
Openshıft 4,6 için DNS iletimi hakkında daha fazla bilgi için [buraya](https://docs.openshift.com/container-platform/4.6/networking/dns-operator.html)bakın.