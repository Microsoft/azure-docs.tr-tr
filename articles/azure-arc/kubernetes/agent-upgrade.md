---
title: Azure Arc etkin Kubernetes aracılarını yükseltme
services: azure-arc
ms.service: azure-arc
ms.date: 03/03/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
description: Azure Arc etkin Kubernetes için Denetim Aracısı yükseltmeleri
keywords: Kubernetes, yay, Azure, K8s, kapsayıcılar, aracı, yükseltme
ms.openlocfilehash: 29df9973ead167e7774924cc3a0c685606092d28
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106442533"
---
# <a name="upgrading-azure-arc-enabled-kubernetes-agents"></a>Azure Arc etkin Kubernetes aracılarını yükseltme

Azure Arc etkin Kubernetes, aracıları için otomatik yükseltme ve el ile yükseltme olanakları sağlar. Otomatik yükseltmeyi devre dışı bırak seçeneğini kullanırsanız ve bunun yerine el ile yükseltme yaparsanız, sürüm destek ilkesi Arc aracıları ve temel alınan Kubernetes kümesi için geçerlidir.

## <a name="toggle-auto-upgrade-on-or-off-when-connecting-cluster-to-azure-arc"></a>Kümeyi Azure yaya bağlarken otomatik yükseltmeyi aç veya kapat

Azure Arc etkin Kubernetes, kendi aracılarını kullanıma hazır otomatik yükseltme özellikleri sağlar.

Aşağıdaki komut otomatik yükseltme **etkin** bir şekilde bir kümeyi Azure yaya bağlar:

```console
az connectedk8s connect --name AzureArcTest1 --resource-group AzureArcTest
```

Otomatik yükseltme etkinken, aracı, aracıların daha yeni bir sürümünün kullanılabilirliği için saatlik olarak Azure 'u yoklar. Aracı daha yeni bir sürümü bulursa, Azure Arc aracıları için bir hela grafik yükseltmesini tetikler.

Otomatik yükseltmeyi devre dışı bırakmak için, `--disable-auto-upgrade` kümeyi Azure yaya bağlarken parametresini belirtin. Aşağıdaki komut otomatik yükseltme **devre dışı** olarak bir kümeyi Azure yaya bağlar:

```console
az connectedk8s connect --name AzureArcTest1 --resource-group AzureArcTest --disable-auto-upgrade
```

> [!TIP]
> Otomatik yükseltmeyi devre dışı bırakmayı planlıyorsanız lütfen Azure Arc etkin Kubernetes [sürüm desteği ilkesine](#version-support-policy) bakın.

## <a name="toggle-auto-upgrade-onoff-after-connecting-cluster-to-azure-arc"></a>Kümeyi Azure yaya bağladıktan sonra otomatik yükseltmeyi aç/kapat

Bir kümeyi Azure yaya bağladığınızda, `az connectedk8s update` aşağıda gösterildiği gibi otomatik yükseltme özelliğini komutuyla değiştirebilirsiniz:

```console
az connectedk8s update --name AzureArcTest1 --resource-group AzureArcTest --auto-upgrade false
```

## <a name="manually-upgrade-agents"></a>Aracıları el ile yükseltme

Aracılar için otomatik yükseltmeyi devre dışı bırakırsanız, `az connectedk8s upgrade` aşağıda gösterildiği gibi komutunu kullanarak bu aracıların yükseltmelerini el ile başlatabilirsiniz:

```console
az connectedk8s upgrade -g AzureArcTest1 -n AzureArcTest --agent-version 1.1.0
```

Azure Arc etkin Kubernetes, aracılarını sürüm oluşturma için standart [anlam sürümü oluşturma şemasını](https://semver.org/) izler `MAJOR.MINOR.PATCH` . 

Sürümdeki her bir sayı, önceki sürümle genel uyumluluğu gösterir:

* **Büyük sürümler** uyumsuz API güncelleştirmeleri olduğunda değişir veya geriye doğru uyumluluk bozulabilir.
* **Küçük sürümler** , işlevsellik değişiklikleri diğer küçük yayınlar için geriye doğru uyumluysa değişir.
* Geriye dönük olarak uyumlu hata düzeltmeleri yapıldığında **Düzeltme Eki sürümleri** değişir.

## <a name="version-support-policy"></a>Sürüm destek ilkesi

Destek sorunları oluşturduğunuzda, Azure Arc etkin Kubernetes aşağıdaki sürüm destek ilkesini destekler:

* Azure Arc etkin Kubernetes aracılarında, aracıların en son küçük sürümü olan "N-2" destek penceresi bulunur. 
  * Örneğin, Azure Arc etkin Kubernetes, 0.28 tanıtır. bugün, 0.28. a, 0.28. b, 0.27. c, 0.27. d, 0.26. e ve 0.26. f sürümleri Azure Arc tarafından desteklenir.

* Azure yaya bağlanan Kubernetes kümeleri, "N-2" destek penceresine sahiptir, burada ' N ' [yukarı akış Kubernetes](https://github.com/kubernetes/kubernetes/releases)'in en son kararlı küçük sürümüdür. 
  * Örneğin, Kubernetes 1,20 tanıtır. bugün, 1,20. a, 1,20. b, 1.19. c, 1.19. d, 1.18. e ve 1.18. f sürümleri desteklenir.

### <a name="how-often-are-minor-version-releases-of-azure-arc-enabled-kubernetes-available"></a>Azure Arc etkin Kubernetes 'in küçük sürüm sürümleri ne sıklıkta kullanılabilir?

Azure Arc etkin Kubernetes aracılarının bir alt sürümü ayda yaklaşık bir kez serbest bırakılır.

### <a name="what-happens-if-im-using-an-agent-version-or-a-kubernetes-version-outside-the-official-support-window"></a>Resmi destek penceresi dışında bir aracı sürümü veya Kubernetes sürümü kullanıyorsam ne olur?

' Destek dışında ', çalıştırdığınız sürümlerin, aracıların ve yukarı akış Kubernetes kümelerinin "N-2" desteklenen sürümlerinin dışında olduğu anlamına gelir. Destek sorunu ile devam etmek için kümeyi ve aracıları desteklenen bir sürüme yükseltmeniz istenir.

## <a name="next-steps"></a>Sonraki adımlar

* [Bir Kubernetes kümesini Azure yaya bağlamak](./quickstart-connect-cluster.md)için hızlı başlangıç deneyimimizi gözden geçir.
* Zaten bir Kubernetes kümesine bağlı Azure yayı zaten var mı? [Arc etkin Kubernetes kümesinde yapılandırma oluşturma](./tutorial-use-gitops-connected-cluster.md).
* Uygun [ölçekte yapılandırma uygulamak Için Azure ilkesi](./use-azure-policy.md)'ni nasıl kullanacağınızı öğrenin.