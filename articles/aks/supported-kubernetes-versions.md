---
title: Azure Kubernetes hizmetinde desteklenen Kubernetes sürümleri
description: Azure Kubernetes Service 'teki (AKS) Kubernetes sürüm destek ilkesini ve kümelerin yaşam döngüsünü anlayın
services: container-service
author: sauryadas
ms.service: container-service
ms.topic: article
ms.date: 05/20/2019
ms.author: saudas
ms.openlocfilehash: 27b180d8d95d7dad967b8ac2495a795ed70836b9
ms.sourcegitcommit: aaa82f3797d548c324f375b5aad5d54cb03c7288
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/29/2019
ms.locfileid: "70147232"
---
# <a name="supported-kubernetes-versions-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) içinde desteklenen Kubernetes sürümleri

Kubernetes topluluğu, küçük sürümleri yaklaşık üç ayda bir yayınlamaktadır. Bu yayınlar yeni özellikler ve geliştirmeler içerir. Düzeltme eki yayınları daha sıktır (bazen haftalık) ve yalnızca bir küçük sürümdeki kritik hata düzeltmelerine yöneliktir. Bu düzeltme eki sürümleri, Kubernetes temelinde üretimde çalışan çok sayıda müşteriyi ve ürünü etkileyen güvenlik açıklarına veya önemli hatalara yönelik düzeltmeler içerir.

Bir yukarı akış sürümünün 30 gün içinde yeni Kubernetes sürümlerini onaylamak ve serbest bırakmak, yayının kararlılığını ele almak için AKS aks.

## <a name="kubernetes-versions"></a>Kubernetes sürümleri

Kubernetes, standart [anlamsal sürüm](https://semver.org/) oluşturma düzenini kullanır. Bu, her bir Kubernetes sürümünün bu numaralandırma düzenini izlediği anlamına gelir:

```
[major].[minor].[patch]

Example:
  1.12.14
  1.12.15
```

Sürümdeki her bir sayı, önceki sürümle genel uyumluluğu gösterir:

* Büyük sürümler uyumsuz API değişiklikleri veya geriye dönük uyumluluk bozuk olduğunda değişir.
* Küçük sürümler, diğer küçük yayınlar ile geriye doğru uyumlu olan işlevsellik değişiklikleri yapıldığında değişir.
* Geriye dönük olarak uyumlu hata düzeltmeleri yapıldığında düzeltme eki sürümleri değişir.

Genel olarak, kullanıcılar çalıştırdıkları ikincil sürümün en son düzeltme eki sürümünü çalıştırmak için Endeavor gerekir. Örneğin, üretim kümeniz *1.12.14* üzerinde ise ve *1.12.15* *1,12* serisi için kullanılabilir en son düzeltme eki sürümündedir , kümenizin tamamen düzeltme eki uygulanmış ve desteklenen olduğundan emin olmak için *1.12.15* sürümüne yükseltmeniz gerekir.

## <a name="kubernetes-version-support-policy"></a>Kubernetes sürüm destek ilkesi

AKS, Kubernetes’in dört küçük sürümünü destekler:

* AKS içinde Yayınlanan geçerli ikincil sürüm (N)
* Önceki üç küçük sürüm. Desteklenen her küçük sürüm, ayrıca iki kararlı düzeltme eki de destekler.

Bu "N-3" (N (en son sürüm)-3 (alt sürümler)) olarak bilinir.

Örneğin, AKS *1.13.* bugün, aşağıdaki sürümler için destek sağlanır:

Yeni ikincil sürüm    |    Desteklenen sürüm listesi
-----------------    |    ----------------------
1.13. a               |    1.12. a, 1.12. b, 1.11. a, 1.11. b, 1,10. a, 1,10. b

Burada ". a" ve ". b", temsilci düzeltme eki sürümleridir. " "from 1.13. a, 1.12. a öğesinden farklı olabilir. Örneğin, 1.13.9 ve 1.12.8.

Sürüm değişiklikleri ve beklentileri hakkındaki iletişimlerle ilgili ayrıntılar için aşağıdaki "Iletişimler" bölümüne bakın.

Yeni bir alt sürüm ortaya çıkarıldığında, desteklenen en eski ikincil sürüm ve düzeltme eki sürümleri kullanım dışıdır ve kaldırılır. Örneğin, geçerli desteklenen sürüm listesi şu ise:

```
1.12.a
1.12.b
1.11.a
1.11.b
1.10.a
1.10.b
1.9.a
1.9.b
```

Ve AKS yayınları 1,13. *Bu, 1,9 anlamına gelir.* sürümler (tüm 1,9 sürümler) kaldırılır ve destek dışı bırakılır.

> [!NOTE]
> Müşteriler desteklenmeyen bir Kubernetes sürümü çalıştırıyorsa, bu, küme için destek istendiğinde yükseltmeniz istenir. Desteklenmeyen Kubernetes yayınları çalıştıran kümeler [aks destek ilkeleri](https://docs.microsoft.com/azure/aks/support-policies)kapsamında değildir.


AKS, yukarıdaki küçük sürümlere ek olarak, belirli bir alt sürümün en son *Düzeltme Eki** sürümlerini destekler. Örneğin, aşağıdaki desteklenen sürümler verildiğinde:

```
Current Supported Version List
------------------------------
1.12.1, 1.12.2, 1.11.4, 1.11.5
```

Yukarı akış Kubernetes 1.12.3 ve 1.11.6 ve AKS Bu düzeltme eki sürümlerini yayımlarsa, en eski düzeltme eki sürümleri kullanım dışıdır ve kaldırılır ve desteklenen sürüm listesi şu şekilde olur:

```
New Supported Version List
----------------------
1.12.*2*, 1.12.*3*, 1.11.*5*, 1.11.*6*
```

> [!NOTE]
> Müşteriler küme oluşturmayı, CI 'yi veya diğer otomatikleştirilmiş işleri Belirli bir düzeltme eki yayınlarına sabitleyemez. 

### <a name="communications"></a>İletişim

* Kubernetes 'in yeni **İkincil** sürümleri için
  * Tüm kullanıcılar, yeni sürümden ve hangi sürümün kaldırılabileceği herkese açık bir şekilde bildirilir.
  * Yeni bir yama sürümü yayınlandığında, en eski düzeltme eki sürümü aynı anda kaldırılır.
  * Müşterilerin, desteklenen bir alt sürüm sürümüne yükseltmek için genel bildirim tarihinden itibaren **60 gün** vardır.
* Kubernetes 'in yeni **Düzeltme Eki** sürümleri için
  * Tüm kullanıcılara, yayımlanmakta olan yeni yama sürümü bildirilir ve en son düzeltme eki sürümüne yükseltilir.
  * Kullanıcıların daha yeni, desteklenen bir yama sürümüne yükseltilmesi **30 gün** daha vardır. Kullanıcıların, en eski kaldırılmadan önce desteklenen bir yama sürümüne yükseltilmesi **30 gün** daha vardır.

AKS, tüm SLO/kalite ölçümlerine ve tüm bölgelerde kullanılabilir olan genel kullanılabilirlik olarak "yayınlanan" tanımlar.

> [!NOTE]
> Kubernetes sürüm yayınları ve kullanım dışı bırakmaları, küçük bir sürümün kullanım dışı bırakıldığına/kaldırıldığına yönelik olarak, desteklenen bir sürüme yükseltmek için 60 gün verildiğinde müşteriler tarafından bilgilendirilir. Yama sürümleri söz konusu olduğunda, müşterilere desteklenen bir sürüme yükseltmek için 30 gün verilir.

Bildirimleri aracılığıyla gönderilir:

* [AKS sürüm notları](https://aka.ms/aks/releasenotes)
* Azure portal bildirimleri
* [Azure güncelleştirme kanalı][azure-update-channel]

### <a name="policy-exceptions"></a>İlke özel durumları

AKS, hata veya güvenlik sorunlarını önceden etkilemeden bir veya daha fazla kritik üretime sahip olmak için tanımlanmış yeni/mevcut sürümlerini ekleme veya kaldırma hakkını saklı tutar.

Hatanın veya güvenlik sorununun önem derecesine bağlı olarak belirli düzeltme eki sürümleri atlanabilir veya dağıtım hızlandırılır.

### <a name="azure-portal-and-cli-default-versions"></a>Azure portal ve CLı varsayılan sürümleri

Portal 'da veya Azure CLı ile bir AKS kümesi dağıttığınızda, küme her zaman N-1 alt sürümüne ve en son düzeltme ekine ayarlanır. Örneğin, aks *1.13. a*, *1.12. a* + *1.12. b*, *1.11. a* + *1.11. b*, *1,10. a* + *1,10 b*' yi destekliyorsa, yeni kümeler için varsayılan sürüm 1.12 ' dir *. b* .

AKS, müşterilere varsayılan olarak bilinen, kararlı ve düzeltme eki uygulanmış bir sürüm sağlamak için N-1 (Minor. latestPatch, EG 1.12. b) değerini alır.

## <a name="list-currently-supported-versions"></a>Şu anda desteklenen sürümleri Listele

Aboneliğiniz ve bölgeniz için şu anda hangi sürümlerin kullanılabildiğini öğrenmek için [az aks get-versions][az-aks-get-versions] komutunu kullanın. Aşağıdaki örnek *EastUS* bölgesi Için kullanılabilir Kubernetes sürümlerini listeler:

```azurecli-interactive
az aks get-versions --location eastus --output table
```

Çıktı aşağıdaki örneğe benzer. Bu, Kubernetes sürüm *1.14.6* 'in kullanılabilir en son sürüm olduğunu gösterir:

```
KubernetesVersion    Upgrades
-------------------  ------------------------
1.14.6               None available
1.14.5               1.14.6
1.13.10              1.14.5, 1.14.6
1.13.9               1.13.10, 1.14.5, 1.14.6
1.12.8               1.13.9, 1.13.10
1.12.7               1.12.8, 1.13.9, 1.13.10
1.11.10              1.12.7, 1.12.8
1.11.9               1.11.10, 1.12.7, 1.12.8
1.10.13              1.11.9, 1.11.10
1.10.12              1.10.13, 1.11.9, 1.11.10
```

## <a name="faq"></a>SSS

**Bir müşteri bir Kubernetes kümesini desteklenmeyen küçük bir sürümle yükseltdiğinde ne olur?**

*N-4* sürümdaysanız, destek dışında olursunuz ve yükseltmeniz istenir. N-4 sürümünden n-3 ' e yükseltme başarılı olursa, şimdi destek ilkeleriniz dahilinde olursunuz. Örneğin:

- Desteklenen aks sürümleri *1.13. a*, *1.12. b* + *1.12. c*, *1.11. d* + *1.11. e*ve *1,10. f* + *1,10. g* ve 1.9. *h* veya *1.9. i* , destek dışında olursunuz.
- *1.9. h* veya *1.9. i* 'den *1,10. f* ya da *1,10. g* ile yükseltme başarılı olursa, destek ilkilerimizin içinden geri dönebilirsiniz.

*N-4* ' ten eski sürümlere yükseltmeler desteklenmez. Bu gibi durumlarda, müşterilerin yeni AKS kümeleri oluşturup iş yüklerini yeniden dağıtmanıza önerilir.

**' Destek dışı ' anlamına gelir**

' Destek dışında ', çalıştırdığınız sürümün desteklenen sürümler listesinin dışında olduğu anlamına gelir ve destek istemek için kümeyi desteklenen bir sürüme yükseltmeniz istenir. Ayrıca, AKS, desteklenen sürümler listesinin dışındaki kümeler için herhangi bir çalışma zamanı veya başka bir garanti yapmaz.

**Bir müşteri bir Kubernetes kümesini desteklenmeyen küçük bir sürümle ölçeklendiğinde ne olur?**

AKS tarafından desteklenmeyen alt sürümler için, ölçekleme veya küçültme herhangi bir sorun olmadan çalışmaya devam eder.

**Bir müşteri, bir Kubernetes sürümünde sonsuza kadar kalabilir mi?**

Evet. Ancak, küme AKS tarafından desteklenen sürümlerden birinde değilse, küme AKS destek ilkelerinden çıkar. Azure, kümenizi otomatik olarak yükseltmez veya silmez.

**Aracı kümesi desteklenen AKS sürümlerinden birinde değilse, ana destek hangi sürümü destekler?**

Ana, desteklenen en son sürüme otomatik olarak güncelleştirilir.

## <a name="next-steps"></a>Sonraki adımlar

Kümenizi yükseltme hakkında daha fazla bilgi için bkz. [Azure Kubernetes Service (AKS) kümesini yükseltme][aks-upgrade].

<!-- LINKS - External -->
[aks-engine]: https://github.com/Azure/aks-engine
[azure-update-channel]: https://azure.microsoft.com/updates/?product=kubernetes-service

<!-- LINKS - Internal -->
[aks-upgrade]: upgrade-cluster.md
[az-aks-get-versions]: /cli/azure/aks#az-aks-get-versions
