---
title: En iyi yöntemler için dağıtımları denetleyin
titleSuffix: Azure Kubernetes Service
description: KUIN-Advisor kullanarak Azure Kubernetes Service 'teki dağıtımlarınızdaki en iyi yöntemlerin uygulanmasını nasıl denetleyeceğinizi öğrenin
services: container-service
author: seanmck
ms.topic: troubleshooting
ms.date: 11/05/2018
ms.author: seanmck
ms.openlocfilehash: 7730146f30487eb5d20f0d3138e9e5ba799daa99
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94681535"
---
# <a name="checking-for-kubernetes-best-practices-in-your-cluster"></a>Kümenizdeki Kubernetes en iyi yöntemlerini denetleme

Uygulamalarınızın en iyi performansını ve esnekliği sağlamak için Kubernetes dağıtımlarınızda izlemeniz gereken birkaç en iyi uygulama vardır. Kuin-Advisor aracını, bu önerileri takip eden dağıtımları aramak için kullanabilirsiniz.

## <a name="about-kube-advisor"></a>Kuin-Advisor hakkında

[Kuin-Advisor aracı][kube-advisor-github] , kümenizde çalıştırılmak üzere tasarlanan tek bir kapsayıcıdır. Dağıtım hakkında bilgi edinmek için Kubernetes API sunucusunu sorgular ve önerilen iyileştirmeler kümesini döndürür.

Kumak-Advisor Aracı, Windows Uygulamaları ve Linux uygulamaları için pod özelliklerinin yanı sıra kaynak isteği ve limitleri rapor edebilir, ancak Kuto-Advisor aracının kendisi bir Linux pod üzerinde zamanlanmalıdır. Pod 'un yapılandırmasındaki [düğüm seçicisini][k8s-node-selector] kullanarak belirli bir işletim sistemine sahip bir düğüm havuzunda çalışacak bir pod zamanlayabilirsiniz.

> [!NOTE]
> Kuin-Advisor Aracı, Microsoft tarafından en iyi çaba temelinde desteklenir. Sorun ve önerilerin GitHub 'da dosyalanmış olması gerekir.

## <a name="running-kube-advisor"></a>Kuto Danışmanı çalıştırma

Aracı, aşağıdaki komutları kullanarak [Kubernetes rol tabanlı erişim denetimi (KUBERNETES RBAC)](./azure-ad-integration-cli.md)için yapılandırılmış bir kümede çalıştırmak için. İlk komut bir Kubernetes hizmet hesabı oluşturur. İkinci komut, aracı bu hizmet hesabını kullanarak bir pod içinde çalıştırır ve kaldırıldıktan sonra Pod 'yi silinmek üzere yapılandırır. 

```bash
kubectl apply -f https://raw.githubusercontent.com/Azure/kube-advisor/master/sa.yaml

kubectl run --rm -i -t kubeadvisor --image=mcr.microsoft.com/aks/kubeadvisor --restart=Never --overrides="{ \"apiVersion\": \"v1\", \"spec\": { \"serviceAccountName\": \"kube-advisor\" } }" --namespace default
```

Kubernetes RBAC kullanmıyorsanız, komutu aşağıdaki gibi çalıştırabilirsiniz:

```bash
kubectl run --rm -i -t kubeadvisor --image=mcr.microsoft.com/aks/kubeadvisor --restart=Never
```

Birkaç saniye içinde, dağıtımlarınıza yönelik olası geliştirmeleri açıklayan bir tablo görmeniz gerekir.

![Kuin-Advisor çıkışı](media/kube-advisor-tool/kube-advisor-output.png)

## <a name="checks-performed"></a>Gerçekleştirilen denetimler

Araç birkaç Kubernetes en iyi uygulamalarını doğrular ve bunların her biri önerilen düzeltmeleri vardır.

### <a name="resource-requests-and-limits"></a>Kaynak istekleri ve sınırlar

Kubernetes [, Pod belirtimlerinde kaynak istekleri ve sınırları][kube-cpumem]tanımlamayı destekler. İstek, kapsayıcıyı çalıştırmak için gereken en düşük CPU ve belleği tanımlar. Sınır, izin verilmesi gereken en fazla CPU ve belleği tanımlar.

Varsayılan olarak, Pod belirtimlerinde hiçbir istek veya sınır ayarlanır. Bu, düğümlerin aşırı zamanlanmasına ve kapsayıcıların ortaya çıkmasına neden olabilir. Kuin-Advisor Aracı, istek ve limitler olmadan Pod 'yi vurgular.

## <a name="cleaning-up"></a>Temizleme

Kümenizde Kubernetes RBAC etkinse, `ClusterRoleBinding` aşağıdaki komutu kullanarak aracı çalıştırdıktan sonra ' yi temizleyebilirsiniz:

```bash
kubectl delete -f https://raw.githubusercontent.com/Azure/kube-advisor/master/sa.yaml
```

Aracı, Kubernetes RBAC özellikli olmayan bir kümeye karşı çalıştırıyorsanız, temizlik gerekmez.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Kubernetes hizmeti ile ilgili sorunları giderme](troubleshooting.md)

<!-- RESOURCES -->

[kube-cpumem]: https://github.com/Azure/azure-quickstart-templates
[kube-advisor-github]: https://github.com/azure/kube-advisor
[k8s-node-selector]: concepts-clusters-workloads.md#node-selectors
