---
title: Azure Red Hat OpenShift destek yaşam döngüsü
description: Azure Red Hat OpenShift için destek yaşam döngüsünü ve desteklenen sürümleri anlayın
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 03/06/2021
ms.openlocfilehash: 35cbaf2b6d2618d116d2dd322ae5ac28f578cecc
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107303551"
---
# <a name="support-lifecycle-for-azure-red-hat-openshift-4"></a>Azure Red Hat OpenShift 4 için destek yaşam döngüsü

Red hat, her üç ayda bir Kırmızı Hat OpenShift kapsayıcı platformu (OCP) için ikincil sürümleri yayımlar. Bu sürümler yeni özellikler ve geliştirmeler içerir. Düzeltme Eki sürümleri daha sık (genellikle haftalık) ve yalnızca küçük bir sürüm içindeki kritik hata düzeltmeleri için tasarlanmıştır. Bu düzeltme eki sürümleri güvenlik açıklarına veya önemli hatalara yönelik düzeltmeler içerebilir.

Azure Red Hat OpenShift, OCP 'nin belirli sürümlerinden oluşturulmuştur. Bu makalede, Azure Red Hat OpenShift ve yükseltmeler, kullanım dışı bırakma ve destek ilkesi ile ilgili ayrıntılar için desteklenen OCP sürümleri ele alınmaktadır.

## <a name="red-hat-openshift-versions"></a>Red Hat OpenShift sürümleri

Red Hat OpenShift kapsayıcı platformu anlam sürümü oluşturma özelliğini kullanır. Anlamsal sürüm oluşturma farklı sürüm numarası düzeylerini kullanarak farklı sürüm oluşturma düzeylerini belirler. Aşağıdaki tabloda, 4.4.11 örnek sürüm numarası kullanılarak anlamsal sürüm numarasının farklı bölümleri gösterilmektedir.

|Ana sürüm (x)|İkincil sürüm (y)|Patch (z)|
|-|-|-|
|4|4|11|

Sürümdeki her bir sayı, önceki sürümle genel uyumluluğu gösterir:

* **Ana sürüm**: Şu anda ana sürüm sürümleri planlanmadı. Büyük sürümler uyumsuz API değişiklikleri veya geriye dönük uyumluluk bozuk olduğunda değişir.
* **İkincil sürüm**: yaklaşık olarak üç ayda bir yayınlandı. Küçük sürüm yükseltmeleri, özellik eklemeleri, geliştirmeler, kullanımdan kalkmalar, kaldırma işlemleri, hata düzeltmeleri, güvenlik geliştirmeleri ve diğer geliştirmeleri içerebilir.
* **Düzeltme ekleri**: genellikle her hafta veya gerektiğinde yayımlanır. Düzeltme Eki sürüm yükseltmeleri hata düzeltmeleri, güvenlik geliştirmeleri ve diğer geliştirmeleri içerebilir.

Müşteriler, çalıştırıldıkları ana sürümün en son küçük sürümünü çalıştırmayı hedeflemelidir. Örneğin, üretim kümeniz 4,4 ' de ise ve 4 serisinin en son genel kullanıma sunulan alt sürümünde 4,5 ise, her zaman, 4,5 ' ye yükseltmeniz gerekir. 

### <a name="upgrade-channels"></a>Yükseltme kanalları

Yükseltme kanalları, Red Hat OpenShift kapsayıcı platformunun (OCP) küçük bir sürümüne bağlıdır. Örneğin, OCP 4,4 yükseltme kanalları hiçbir şekilde 4,5 sürümüne yükseltme içermez. Yükseltme kanalları denetimi yalnızca sürüm seçimi ve küme sürümünü etkilemez.

Azure Red Hat OpenShift 4 yalnızca kararlı kanalları destekler. Örneğin: Stable-4,4.

Daha önceki bir Azure Red Hat OpenShift sürümünden yükseltmek için, Stable-4,5 kanalını kullanabilirsiniz. Hızlı, ön sürüm ve aday kanallar kullanılarak yükseltilen kümeler desteklenmeyecektir.

Geçerli sürümünüzü içermeyen bir kanala geçiş yaparsanız bir uyarı görüntülenir ve hiçbir güncelleştirme önerilmez, ancak dilediğiniz zaman herhangi bir zamanda özgün kanalınıza güvenle geçiş yapabilirsiniz.

## <a name="red-hat-openshift-container-platform-version-support-policy"></a>Red Hat OpenShift kapsayıcı platformu sürüm desteği ilkesi

Azure Red Hat OpenShift, Red Hat OpenShift kapsayıcı platformunun genel kullanıma açık (GA) alt sürümlerini destekler:
* Azure Red Hat OpenShift 'te yayınlanan en son GA alt sürümü (N olarak başvuracağız)
* Önceki bir küçük sürüm (N-1)

Kararlı bir yükseltme kanalında kullanılabiliyorsa, yukarı akış OCP 'de bulunan daha yeni küçük yayınlar (N + 1, N + 2) de desteklenebilir.

Kritik Düzeltme Eki güncelleştirmeleri, Azure Red Hat OpenShift site güvenilirlik mühendisleri (SRE) tarafından otomatik olarak kümelere uygulanır. Düzeltme Eki güncelleştirmelerini önceden yüklemek isteyen müşteriler ücretsizdir.

Örneğin, Azure Red Hat OpenShift bugün 4.5. z 'yi kullanıyorsa, aşağıdaki sürümler için destek sağlanır:

|Yeni ikincil sürüm|Desteklenen sürüm listesi|
|-|-|
|4.5. z|4.5. z, 4.4. z|

". z", düzeltme eki sürümlerinin temsilcisidir. Kararlı bir yükseltme kanalında kullanılabiliyorsa müşteriler 4.6. z sürümüne de yükseltemeyebilir.

Yeni bir alt sürüm ortaya çıkarıldığında, en eski ikincil sürüm kullanım dışıdır ve kaldırılır. Örneğin, desteklenen geçerli sürüm listesinin 4.5. z ve 4.4. z olduğunu varsayalım. Azure Red Hat OpenShift 4.6. z 'yi yayınlarken, 4.4. z sürümü kaldırılır ve 30 gün içinde destek dışı olacaktır.

> [!NOTE]
> Müşteriler desteklenmeyen bir Red Hat OpenShift sürümü çalıştırıyorsa, küme için destek istendiğinde yükseltmeniz istenebilir. Desteklenmeyen Red Hat OpenShift yayınları çalıştıran kümeler, Azure Red Hat OpenShift SLA 'Sı kapsamında değildir.

## <a name="release-and-deprecation-process"></a>Serbest bırakma ve kullanımdan kaldırma işlemi

Azure Red Hat OpenShift yayın takviminde yaklaşan sürüm yayınlarına ve kullanım dışı bırakılanlardan başvurabilirsiniz.

Red Hat OpenShift kapsayıcı platformunun yeni ikincil sürümleri için:
* Azure Red Hat OpenShift SRE ekibi, yeni sürüm sürümünün planlı tarihi ile bir ön duyuru yayımlar ve eski sürüm, kaldırma işleminden önce en az 30 gün sonra [Azure Red Hat OpenShift sürüm notlarını](https://github.com/Azure/OpenShift/releases) kullanımdan kaldırır.
* Azure Red Hat OpenShift SRE ekibi, Azure Red Hat OpenShift ve Portal erişimi olan tüm müşterilere sunulan bir hizmet durumu bildirimi yayımlar ve abonelik yöneticilerine planlı sürüm kaldırma tarihleriyle bir e-posta gönderir.
* Müşterilerin, destek almaya devam etmek için desteklenen bir ikincil sürüm sürümüne yükseltilmesi için sürüm kaldırma işleminden 30 gün daha sahip olması gerekir.

Red Hat OpenShift kapsayıcı platformunun yeni düzeltme eki sürümleri için:
* Düzeltme Eki sürümlerinin acil doğası nedeniyle, bunlar kullanılabilir hale geldiğinde Azure Red Hat OpenShift SRE ekibi tarafından hizmete tanıtımda bulunabilir.
* Genel olarak, Azure Red Hat OpenShift SRE ekibi, yeni yama sürümlerinin yüklenmesiyle ilgili geniş iletişimler gerçekleştirmez. Bununla birlikte, ekip sürekli olarak bunları desteklemek için kullanılabilir CVE düzeltme eklerini izler ve doğrular. Müşteri eylemi gerekliyse, takım müşterileri yükseltmeye yönelik olarak uyarır.

## <a name="supported-versions-policy-exceptions"></a>Desteklenen sürümler ilkesi özel durumları

Azure Red Hat OpenShift SRE ekibi, yeni/mevcut sürümleri ekleme veya kaldırma hakkını saklı tutar ya da bir veya daha fazla kritik üretim, sorun veya güvenlik sorunlarını önceden etkilemeden etkileyen bir veya daha fazla kritik üretime sahip olacak şekilde tanımlanır.

Belirli düzeltme eki sürümleri atlanabilir veya dağıtım, hatanın veya güvenlik sorununun önem derecesine bağlı olarak hızlandırılmış olabilir.

## <a name="azure-portal-and-cli-versions"></a>Azure portal ve CLı sürümleri

Portalda veya Azure CLı ile bir Azure Red Hat OpenShift kümesi dağıttığınızda, küme varsayılan olarak en son (N) alt sürüme ve en son kritik düzeltme ekine ayarlanır. Örneğin, Azure Red Hat OpenShift 4.5. z ve 4.4. z 'yi destekliyorsa, yeni yüklemeler için varsayılan sürüm 4.5. z ' dir. En son yukarı akış OCP Minor sürümünü (n + 1, N + 2) kullanmak isteyen müşteriler, kendi kümesini kararlı yükseltme kanallarındaki herhangi bir yayına dilediğiniz zaman yükseltebilir.

## <a name="azure-red-hat-openshift-release-calendar"></a>Azure Red Hat OpenShift Yayın takvimi

[Son Red Hat OpenShift kapsayıcı platformu (yukarı akış) yayın geçmişi](https://access.redhat.com/support/policy/updates/openshift/#dates)için aşağıdaki kılavuza bakın.

|OCP sürümü|Yukarı akış yayını|Azure Red Hat OpenShift genel kullanılabilirliği|Yaşam sonu|
|-|-|-|-|
|4.3|Ocak 2020|Nisan 2020| Ağustos 2020|
|4.4|Mayıs 2020|Temmuz 2020|4,6 GA|
|4,5|Temmuz 2020| Kasım 2020|4,7 GA
|4,6|Ekim 2020| Şubat 2021|4,8 GA|

## <a name="faq"></a>SSS

**Bir Kullanıcı bir OpenShift kümesini desteklenmeyen bir ikincil sürümle yükselttiğinde ne olur?**

N-2 veya daha eski bir sürümdaysanız, destek dışında olduğunuz ve destek almaya devam etmek için yükseltme yapmanız istenecek demektir. N-2 sürümünden N-1 ' e yükseltme başarılı olursa, destek içine geri dönebilirsiniz. N-3 sürümünden veya daha eski sürümden desteklenen bir sürüme yükseltmek zor olabilir ve bazı durumlarda mümkün değildir. Olası yükseltme sorunlarından kaçınmak için kümenizi en son OpenShift sürümünde tutmanızı öneririz. Örnek:
* Desteklenen en eski Azure Red Hat OpenShift sürümü 4.4. z ise ve 4.3. z veya daha eski bir sürümdaysanız, destek dışında olursunuz.
* 4.3. z 'den 4.4. z 'ye veya daha yüksek bir sürüme yükseltme başarılı olduğunda, destek ilkilerimize geri dönebilirsiniz. 

Kümenizi önceki bir sürüme geri döndürme veya geri alma desteklenmez. Yalnızca daha yeni bir sürüme yükseltme desteklenir.

**"Destek dışında" ne anlama geliyor?**

ARO kümeniz desteklenen sürümler listesinde olmayan bir OpenShift sürümü çalıştırıyorsa veya [Desteklenmeyen bir küme yapılandırması](https://docs.microsoft.com/azure/openshift/support-policies-v4)kullanıyorsa, kümeniz "destek dışında" olur. Sonuç olarak:
- Kümeniz için bir destek bileti açılırken, kümeyi desteklenen bir sürüme yükseltmeniz istenir. destek almadan önce, sürüm desteği bittikten sonra 30 günlük yetkisiz kullanım süresi içinde olmadığınız durumlar dışında. 
- Destek dışındaki kümeler için herhangi bir çalışma zamanı veya SLA garantisi hükümsüz kılındı.
- Destek dışındaki kümeler yalnızca en iyi çaba temelinde düzeltme eki uygulanır.
- Destek dışındaki kümeler izlenmeyecektir.
