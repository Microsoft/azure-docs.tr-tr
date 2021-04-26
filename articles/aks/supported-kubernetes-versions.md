---
title: Azure Kubernetes Service'te desteklenen Kubernetes sürümleri
description: Azure Kubernetes Service 'teki (AKS) Kubernetes sürüm destek ilkesini ve kümelerin yaşam döngüsünü anlayın
services: container-service
ms.topic: article
ms.date: 03/29/2021
author: palma21
ms.author: jpalma
ms.openlocfilehash: a46d3e8362d48225e5fec11213bfbff370252d8e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107782684"
---
# <a name="supported-kubernetes-versions-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Hizmeti’nde (AKS) desteklenen Kubernetes sürümleri

Kubernetes topluluğu, her üç ayda bir ikincil sürümleri kabaca yayınlar. Son olarak, Kubernetes topluluğu, sürüm 1,19 ' den başlayarak, [her sürüm için destek penceresini 9 aydan 12 aya kadar artırmıştır](https://kubernetes.io/blog/2020/08/31/kubernetes-1-19-feature-one-year-support/). 

İkincil sürüm sürümleri yeni özellikler ve geliştirmeler içerir. Düzeltme Eki sürümleri daha sık (bazen haftalık) ve küçük bir sürüm içindeki kritik hata düzeltmeleri için tasarlanmıştır. Düzeltme Eki sürümleri, güvenlik açıklarına veya önemli hatalara yönelik düzeltmeler içerir.

## <a name="kubernetes-versions"></a>Kubernetes sürümleri

Kubernetes, her sürüm için standart [anlamsal sürüm](https://semver.org/) oluşturma şemasını kullanır:

```
[major].[minor].[patch]

Example:
  1.17.7
  1.17.8
```

Sürümdeki her bir sayı, önceki sürümle genel uyumluluğu gösterir:

* **Büyük sürümler** uyumsuz API güncelleştirmeleri veya geriye doğru uyumluluk bozuk olabilir.
* **İkincil sürümler** , diğer küçük yayınlar ile geriye doğru uyumlu işlevsellik güncelleştirmeleri yapıldığında değişir.
* Geriye dönük olarak uyumlu hata düzeltmeleri yapıldığında **Düzeltme Eki sürümleri** değişir.

Çalıştırmakta olduğunuz ikincil sürümün en son düzeltme eki sürümünü çalıştırmak için hedefleyin. Örneğin, üretim kümeniz açık **`1.17.7`** . **`1.17.8`** , *1,17* serisi için kullanılabilen en son düzeltme eki sürümüdür. **`1.17.8`** Kümenizin tamamen düzeltme eki uygulanmış ve desteklendiğinden emin olmak için en kısa sürede ' a yükseltmeniz gerekir.

## <a name="kubernetes-version-support-policy"></a>Kubernetes sürüm desteği ilkesi

AKS, tüm SLO veya SLA ölçümlerine etkin olan ve tüm bölgelerde kullanılabilen bir sürüm olarak genel kullanıma sunulan bir sürümü tanımlar. AKS, Kubernetes 'in üç GA sürümlerini destekler:

* AKS içinde yayınlanan en son GA alt sürümü (N olarak adlandırılıyoruz).
* Önceki iki alt sürüm.
    * Desteklenen her bir alt sürüm ayrıca en fazla iki (2) kararlı düzeltme eki destekler.

AKS, açıkça etiketlenmiş ve [Önizleme hüküm ve koşullarına][preview-terms]tabi olan önizleme sürümlerini de destekleyebilir.

> [!NOTE]
> AKS, aşamalı bölge dağıtımını içeren güvenli dağıtım uygulamalarını kullanır. Bu, yeni bir sürüm veya yeni bir sürümün tüm bölgelerde kullanılabilir olması için 10 iş günü sürebileceği anlamına gelir.

AKS üzerinde Kubernetes sürümlerinin desteklenen penceresi "N-2" olarak bilinir: (N (en son sürüm)-2 (ikincil sürümler)).

Örneğin, AKS *1.17.* bugün, aşağıdaki sürümler için destek sağlanır:

Yeni ikincil sürüm    |    Desteklenen sürüm listesi
-----------------    |    ----------------------
1.17. a               |    1.17. a, 1.17. b, 1.16. c, 1.16. d, 1.15. e, 1.15. f

Burada ". letter", düzeltme eki sürümlerinin temsilcisidir.

Yeni bir alt sürüm ortaya çıkarıldığında, desteklenen en eski ikincil sürüm ve düzeltme eki sürümleri kullanım dışıdır ve kaldırılır. Örneğin, desteklenen geçerli sürüm listesi şu şekilde olur:

```
1.17.a
1.17.b
1.16.c
1.16.d
1.15.e
1.15.f
```

AKS yayınları 1,18. \* , tüm 1,15. \* sürümleri 30 gün içinde desteklenmez.

> [!NOTE]
> Müşteriler desteklenmeyen bir Kubernetes sürümü çalıştırıyorsa, bu, küme için destek istendiğinde yükseltilmeleri istenir. Desteklenmeyen Kubernetes yayınları çalıştıran kümeler [aks destek ilkeleri](./support-policies.md)kapsamında değildir.

Yukarıdaki ' a ek olarak, AKS, belirli bir alt sürümün en fazla iki **Düzeltme Eki** sürümünü destekler. Bu nedenle, aşağıdaki desteklenen sürümler verilmiştir:

```
Current Supported Version List
------------------------------
1.17.8, 1.17.7, 1.16.10, 1.16.9
```

AKS yayınları `1.17.9` ve `1.16.11` , en eski düzeltme eki sürümleri kullanım dışıdır ve kaldırılır ve desteklenen sürüm listesi şu şekilde olur:

```
New Supported Version List
----------------------
1.17.*9*, 1.17.*8*, 1.16.*11*, 1.16.*10*
```

### <a name="supported-kubectl-versions"></a>Desteklenen `kubectl` sürümler

`kubectl` [Kubectl Için Kubernetes destek ilkesiyle](https://kubernetes.io/docs/setup/release/version-skew-policy/#kubectl)tutarlı, *Kuto-apiserver* sürümünüze göre daha eski veya daha yeni bir sürümünü kullanabilirsiniz.

Örneğin, *ku1,18* to *-apiserver* *1,17* ise,  `kubectl` Bu *kuin-apiserver* ile 1,16 arası sürümlerini kullanabilirsiniz.

Sürümünüzü yüklemek veya güncelleştirmek için `kubectl` ' i çalıştırın `az aks install-cli` .

## <a name="release-and-deprecation-process"></a>Serbest bırakma ve kullanımdan kaldırma işlemi

[Aks Kubernetes sürüm takviminde](#aks-kubernetes-release-calendar)yaklaşan sürüm yayınlarına ve kullanım dışı bırakılmaları için başvuru yapabilirsiniz.

Kubernetes 'in yeni **İkincil** sürümleri için:
  * AKS, yeni sürüm sürümünün planlı tarihi ile bir ön duyuru yayımlar ve ilgili eski sürüm, kaldırma işleminden önce en az 30 gün önce, [aks sürüm notlarını](https://aka.ms/aks/releasenotes) kullanımdan kaldırır.
  * AKS, kullanım dışı bırakılmış API 'Ler nedeniyle yeni bir sürüm kümesinde sorunlara neden olursa kullanıcıları uyarmak için [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-overview) 'ı kullanır. Azure Advisor, şu anda destek dışı olduklarında kullanıcıyı uyarmak için de kullanılır.
  * AKS, AKS ve Portal erişimi olan tüm kullanıcılar için kullanılabilir bir [hizmet durumu bildirimi](../service-health/service-health-overview.md) yayımlar ve abonelik yöneticilerine planlanan sürüm kaldırma tarihleriyle bir e-posta gönderir.

    > [!NOTE]
    > Abonelik yöneticilerinizi kim olduğunu öğrenmek veya değiştirmek için lütfen [Azure aboneliklerini yönetme](../cost-management-billing/manage/add-change-subscription-administrator.md#assign-a-subscription-administrator)bölümüne bakın.
    
  * Kullanıcıların, destek almaya devam etmek için desteklenen bir ikincil sürüm sürümüne yükseltilmesi için sürüm kaldırma işleminden **30 gün** daha vardır.

Kubernetes 'in yeni **Düzeltme Eki** sürümleri için:
  * Düzeltme Eki sürümlerinin acil doğası nedeniyle, kullanılabilir olduklarında hizmete tanıtılamaz.
  * Genel olarak, AKS yeni yama sürümlerinin yayını için büyük ölçüde iletişim kurmaz. Ancak, AKS 'ler sürekli olarak bunları düzenli bir şekilde desteklemek için kullanılabilir CVE düzeltme eklerini izler ve doğrular. Kritik bir düzeltme eki bulunursa veya Kullanıcı eylemi gerekliyse, AKS kullanıcıları yeni kullanılabilir düzeltme ekine yükseltmek üzere bilgilendirilir.
  * Kullanıcıların desteklenen bir düzeltme ekine yükseltmek ve destek almaya devam etmek için bir düzeltme ekinin, bir yama sürümünden kaldırma işleminden **30 gün** daha vardır.

### <a name="supported-versions-policy-exceptions"></a>Desteklenen sürümler ilkesi özel durumları

AKS, bir veya daha fazla kritik üretim etkileyen hata veya güvenlik sorunlarını fark etmeden yeni/mevcut sürümler ekleme veya kaldırma hakkını saklı tutar.

Hatanın veya güvenlik sorununun önem derecesine bağlı olarak, belirli düzeltme eki sürümleri atlanabilir veya piyasaya sürülme hızlandırılır.

## <a name="azure-portal-and-cli-versions"></a>Azure portal ve CLı sürümleri

Portal 'da veya Azure CLı ile bir AKS kümesi dağıttığınızda, küme varsayılan olarak N-1 alt sürüm ve en son düzeltme ekini alır. Örneğin, AKS, *1.17. a*, *1.17. b*, *1.16. c*, *1.16. d*, *1.15. e* ve *1.15. f*' i destekliyorsa, seçilen varsayılan sürüm *1.16. c*' dir.

Aboneliğiniz ve bölgeniz için şu anda hangi sürümlerin kullanılabildiğini öğrenmek için [az aks get-versions][az-aks-get-versions] komutunu kullanın. Aşağıdaki örnek *EastUS* bölgesi Için kullanılabilir Kubernetes sürümlerini listeler:

```azurecli-interactive
az aks get-versions --location eastus --output table
```

## <a name="aks-kubernetes-release-calendar"></a>AKS Kubernetes Yayın takvimi

Son sürüm geçmişi için bkz. [Kubernetes](https://en.wikipedia.org/wiki/Kubernetes#History).

|  K8s sürümü | Yukarı akış yayını  | AKS önizlemesi  | AKS GA  | Yaşam sonu |
|--------------|-------------------|--------------|---------|-------------|
| 1,18  | Mar-23-20  | Mayıs 2020   | Ağu 2020  | 1,21 GA | 
| 1,19  | Ağu-04-20  | Eyl 2020   | Kas 2020  | 1,22 GA | 
| 1.20  | Ara-08-20  | Ocak 2021   | Mar 2021  | 1,23 GA |
| 1,21  | Nis-08-21 | Mayıs 2021   | Haz 2021  | 1,24 GA |



## <a name="faq"></a>SSS

**Microsoft yeni Kubernetes sürümlerini nasıl bilgilendirir?**

AKS ekibi, belgelerimizde yeni Kubernetes sürümlerinin planlı tarihleriyle ön Duyurular yayınlar, [GitHub](https://github.com/Azure/AKS/releases) 'umuz ve destek dışı tutulacak kümelerin sahibi olan abonelik yöneticilerine e-postalar yayınlar.  Duyurulara ek olarak, AKS Ayrıca, Azure Portal 'ın bu müşterilerin destek dışında kullanıcıları uyarmasını ve uygulama veya geliştirme sürecini etkileyecek kullanım dışı API 'lerden haberdar olmasını sağlamak için [Azure Danışmanı](https://docs.microsoft.com/azure/advisor/advisor-overview) 'nı kullanır. 

**Kubernetes sürümlerini desteklemeyi ne sıklıkta desteklemem gerekir?**

Kubernetes 1,19 ile başlayarak, [Açık kaynaklı topluluk 1 yıla kadar destek genişletmiştir](https://kubernetes.io/blog/2020/08/31/kubernetes-1-19-feature-one-year-support/). AKS, yukarı akış taahhütleriyle eşleşen düzeltme eklerini ve desteği etkinleştirir. 1,19 ve üzeri AKS kümelerinde, desteklenen bir sürümde kalmak için yılda en az bir kez yükseltmeniz mümkün olacaktır. 

1,18 veya sonraki sürümlerde, destek penceresi 9 ayda kalır ve desteklenen bir sürümde kalmak için her 9 ayda bir yükseltmeyi gerektirir. Yeni sürümleri düzenli olarak test edin ve Kubernetes içindeki en son kararlı geliştirmeleri yakalamak için daha yeni sürümlere yükseltmeye hazırlıklı olun.

**Bir Kullanıcı bir Kubernetes kümesini desteklenmeyen küçük bir sürümle yükseltdiğinde ne olur?**

*N-3* veya daha eski bir sürüm kullanıyorsanız, destek dışında olduğunuz ve yükseltmeniz istenecek demektir. N-3 sürümünden n-2 ' ye yükseltme başarılı olursa, destek ilkeleriniz dahilinde geri dönebilirsiniz. Örnek:

- Desteklenen en eski AKS sürümü *1.15. a* ise ve *1.14. b* veya daha eski bir sürümdaysanız, destek dışında olursunuz.
- *1.14. b* ' den *1.15. a* veya daha yüksek bir sürüme başarıyla yükselttiğinizde, destek ilkeleriniz içinde geri dönebilirsiniz.

Eski sürüme düşürme işlemleri desteklenmez.

**' Destek dışında ' ne anlama geliyor?**

' Destek dışında ' şu anlama gelir:
* Çalıştırmakta olduğunuz sürüm desteklenen sürümler listesinin dışında.
* Sürüm kullanımdan kaldırıldıktan sonra 30 günlük yetkisiz kullanım süresi içinde olmadığınız takdirde, destek istemek için kümeyi desteklenen bir sürüme yükseltmeniz istenir. 

Ayrıca, AKS, desteklenen sürümler listesinin dışındaki kümeler için herhangi bir çalışma zamanı veya başka garanti yapmaz.

**Bir Kullanıcı bir Kubernetes kümesini desteklenmeyen küçük bir sürümle ölçeklendirdiğinde ne olur?**

AKS tarafından desteklenmeyen alt sürümler için, ölçekleme veya küçültme çalışmaya devam etmelidir. Hizmet garantisi kalitesi olmadığından, kümenizi yeniden desteğe getirmek için yükseltmeniz önerilir.

**Bir Kullanıcı bir Kubernetes sürümünde sonsuza kadar kalabilir mi?**

Bir kümenin üçten fazla (3) alt sürüm desteği tükenmiştir ve güvenlik riskleri taşıyan bir güvenlik riski varsa, Azure, kümenizi yükseltmek için size güvenli bir şekilde iletişim kurar. Daha fazla işlem yapmazsanız, Azure, kümenizi sizin adınıza otomatik olarak yükseltme hakkını saklı tutar.

**Düğüm havuzu desteklenen AKS sürümlerinden birinde değilse, denetim düzlemi hangi sürümü destekler?**

Denetim düzlemi tüm düğüm havuzlarındaki sürümlerin bir penceresi içinde olmalıdır. Denetim düzlemi veya düğüm havuzlarını yükseltme hakkında daha fazla bilgi için, [düğüm havuzlarını yükseltme](use-multiple-node-pools.md#upgrade-a-cluster-control-plane-with-multiple-node-pools)hakkındaki belgeleri ziyaret edin.

**Küme yükseltmesi sırasında birden çok AKS sürümünü atlayabilir miyim?**

Desteklenen bir AKS kümesini yükselttiğinizde, Kubernetes ikincil sürümleri atlanamaz. Örneğin, şu işlemleri arasında yükseltmeler:
  * *1.12. x*  ->  *1.13. x*: izin verilir.
  * *1.13. x*  ->  *1.14. x*: izin verilir.
  * *1.12. x*  ->  *1.14. x*: izin verilmiyor.

*1.12. x*  ->  *1.14. x* sürümünden yükseltmek için:
1. *1.12. x*  ->  *1.13. x* sürümünden yükseltme.
1. *1.13. x*  ->  *1.14. x* sürümünden yükseltme.

Birden çok sürüm atlanması yalnızca desteklenmeyen bir sürümden desteklenen bir sürüme yükseltilirken yapılabilir. Örneğin, desteklenmeyen bir *1,10. x* ' den desteklenen bir *1.15. x*' e yükseltebilirsiniz.

**30 günlük destek penceresi sırasında yeni bir 1. xx. x kümesi oluşturabilir miyim?**

Hayır. Bir sürüm kullanım dışı olduktan/kaldırıldıktan sonra, bu sürümü içeren bir küme oluşturamazsınız. Değişiklik sırasında, sürüm listenizden kaldırılan eski sürümü görürsünüz. Bu işlem, iki haftada bir duyurudan, zaman aşamalı olarak bölgeye göre sürebilir.

**Artık kullanımdan kalkmış bir sürümm var, yeni düğüm havuzları eklemeye devam edebilir miyim? Ya da yükseltmem gerekir mi?**

Hayır. Kullanım dışı bırakılan sürümün düğüm havuzlarını kümenize eklemenize izin verilmez. Yeni bir sürümün düğüm havuzlarını ekleyebilirsiniz. Bununla birlikte, bu durum önce denetim düzlemi 'ni güncelleştirmenizi gerektirebilir. 

## <a name="next-steps"></a>Sonraki adımlar

Kümenizi yükseltme hakkında daha fazla bilgi için bkz. [Azure Kubernetes Service (AKS) kümesini yükseltme][aks-upgrade].

<!-- LINKS - External -->
[aks-engine]: https://github.com/Azure/aks-engine
[azure-update-channel]: https://azure.microsoft.com/updates/?product=kubernetes-service

<!-- LINKS - Internal -->
[aks-upgrade]: upgrade-cluster.md
[az-aks-get-versions]: /cli/azure/aks#az_aks_get_versions
[preview-terms]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
