---
title: Inress denetleyicisini Held ile yükseltme
description: Bu makale, Held kullanarak bir Application Gateway girişini yükseltme hakkında bilgi sağlar.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 3903ccd1c15765d06cd1794a40567e2c70062538
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73795898"
---
# <a name="how-to-upgrade-application-gateway-ingress-controller-using-helm"></a>Held kullanarak Application Gateway giriş denetleyicisini yükseltme 

Kubernetes (AGIC) için Azure Application Gateway giriş denetleyicisi, Azure depolama 'da barındırılan bir Held deposu kullanılarak yükseltilebilir.

Yükseltme yordamına başlamadan önce, gerekli depoyu eklemiş olduğunuzdan emin olun:

- Şu anda eklenmiş olan Held depolarınızı görüntüleyin:

    ```bash
    helm repo list
    ```

- AGIC depoyu şu şekilde ekleyin:

    ```bash
    helm repo add \
        application-gateway-kubernetes-ingress \
        https://appgwingress.blob.core.windows.net/ingress-azure-helm-package/
    ```

## <a name="upgrade"></a>Yükseltme

1. En son sürümü almak için AGIC Held deposunu yenileyin:

    ```bash
    helm repo update
    ```

1. `application-gateway-kubernetes-ingress` grafiğin kullanılabilir sürümlerini görüntüleyin:

    ``` bash
    helm search -l application-gateway-kubernetes-ingress
    ```

    Örnek yanıt:

    ```bash
    NAME                                                    CHART VERSION   APP VERSION     DESCRIPTION
    application-gateway-kubernetes-ingress/ingress-azure    0.7.0-rc1       0.7.0-rc1       Use Azure Application Gateway as the ingress for an Azure...
    application-gateway-kubernetes-ingress/ingress-azure    0.6.0           0.6.0           Use Azure Application Gateway as the ingress for an Azure...
    ```

    Yukarıdaki listeden kullanılabilir en son sürüm: `0.7.0-rc1`

1. Şu anda yüklü olan Held grafiklerini görüntüleyin:

    ```bash
    helm list
    ```

    Örnek yanıt:

    ```bash
    NAME            REVISION        UPDATED                         STATUS  CHART                   APP VERSION     NAMESPACE
    odd-billygoat   22              Fri Jun 21 15:56:06 2019        FAILED  ingress-azure-0.7.0-rc1 0.7.0-rc1       default
    ```

    Yukarıdaki örnek yanıttan hela grafik yüklemesi `odd-billygoat`olarak adlandırılmıştır. Bu adı komutların geri kalanı için kullanacağız. Gerçek dağıtım adınız büyük olasılıkla farklı olacaktır.

1. Held dağıtımını yeni bir sürüme yükseltin:

    ```bash
    helm upgrade \
        odd-billygoat \
        application-gateway-kubernetes-ingress/ingress-azure \
        --version 0.9.0-rc2
    ```

## <a name="rollback"></a>Etkin

Held dağıtımı başarısız olursa, önceki bir sürüme geri alabilirsiniz.

1. Bilinen son sağlıklı sürüm numarasını alın:

    ```bash
    helm history odd-billygoat
    ```

    Örnek çıktı:

    ```bash
    REVISION        UPDATED                         STATUS          CHART                   DESCRIPTION
    1               Mon Jun 17 13:49:42 2019        DEPLOYED        ingress-azure-0.6.0     Install complete
    2               Fri Jun 21 15:56:06 2019        FAILED          ingress-azure-xx        xxxx
    ```

    `helm history` komutunun örnek çıktısından, `odd-billygoat` son başarılı dağıtımına benzer `1`

1. Son başarılı düzeltmeye geri al:

    ```bash
    helm rollback odd-billygoat 1
    ```
