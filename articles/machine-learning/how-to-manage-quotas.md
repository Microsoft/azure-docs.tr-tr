---
title: Kaynakları ve kotaları yönetme
titleSuffix: Azure Machine Learning
description: Azure Machine Learning için kaynaklardaki kotalar ve sınırlamalar hakkında bilgi edinin ve kota artışlarının nasıl isteneceğini öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: jmartens
author: SimranArora904
ms.author: siarora
ms.date: 12/1/2020
ms.topic: conceptual
ms.custom: troubleshooting,contperf-fy20q4, contperf-fy21q2
ms.openlocfilehash: 4e61a15b86d1d2d05889253f615eec0865c87a70
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102520396"
---
# <a name="manage-and-increase-quotas-for-resources-with-azure-machine-learning"></a>Azure Machine Learning olan kaynaklar için kotaları yönetme ve artırma

Azure, sahtekarlık ve Azure kapasite kısıtlamalarını dikkate almak için limitlerin ve kotaların kullanımını engeller. Üretim iş yükleri için ölçeklendirirseniz bu limitleri değerlendirin. Bu makalede şunları öğreneceksiniz:

> [!div class="checklist"]
> + [Azure Machine Learning](overview-what-is-azure-ml.md)ilişkili Azure kaynakları için varsayılan sınırlamalar.
> + Çalışma alanı düzeyinde kotalar oluşturuluyor.
> + Kotaları ve limitlerinizi görüntüleme.
> + İstenen kota artışı.
> + Özel uç nokta ve DNS kotaları.

Kotaları yönetme ile birlikte, [Azure Machine Learning maliyetlerini nasıl planlayacağınızı ve yöneteceğinizi](concept-plan-manage-cost.md) öğrenin veya [Azure Machine Learning hizmet sınırlarını](resource-limits-quotas-capacity.md)öğrenin.

## <a name="special-considerations"></a>Özel Konular

+ Kota, kapasite garantisi değil, kredi limiti olur. Büyük ölçekli kapasite gereksinimleriniz varsa, [kotayı artırmak Için Azure desteğine başvurun](#request-quota-increases).

+ Bir kota, aboneliklerinizde Azure Machine Learning dahil olmak üzere tüm hizmetler arasında paylaşılır. Kapasiteyi değerlendirirken tüm hizmetler genelinde kullanımı hesaplayın.
 
  Azure Machine Learning işlem bir özel durumdur. Temel işlem kotasından ayrı bir kotası vardır. 

+ Varsayılan sınırlar, ücretsiz deneme, Kullandıkça öde ve sanal makine (VM) serisi (dv2, F ve G gibi) için teklif kategorisi türüne göre farklılık gösterir.

## <a name="default-resource-quotas"></a>Varsayılan kaynak kotaları

Bu bölümde, aşağıdaki kaynaklar için varsayılan ve en yüksek kota sınırları hakkında bilgi edineceksiniz:

+ Azure Machine Learning varlıklar
  + Azure Machine Learning işlem
  + Azure Machine Learning işlem hatları
+ Sanal makineler
+ Azure Container Instances
+ Azure Depolama

> [!IMPORTANT]
> Sınırlar değişikliğe tabidir. En son bilgiler için  [Azure Machine Learning hizmet sınırlarına](resource-limits-quotas-capacity.md)bakın.



### <a name="azure-machine-learning-assets"></a>Azure Machine Learning varlıklar
Varlıklar için aşağıdaki sınırlar her çalışma alanı temelinde geçerlidir. 

| **Kaynak** | **Üst sınır** |
| --- | --- |
| Veri kümeleri | 10 milyon |
| Çalıştırmalar | 10 milyon |
| Modeller | 10 milyon|
| Artifacts | 10 milyon |

Ayrıca, maksimum **çalışma süresi** 30 gündür ve **her çalıştırma için günlüğe kaydedilen** en fazla ölçüm sayısı 1.000.000 ' dir.

### <a name="azure-machine-learning-compute"></a>Azure Machine Learning İşlemi
[Azure Machine Learning işlem](concept-compute-target.md#azure-machine-learning-compute-managed) , hem çekirdek sayısı hem de bir abonelikte bölge başına izin verilen benzersiz işlem kaynakları sayısı üzerinde varsayılan bir kota sınırına sahiptir. Bu kota, yalnızca Azure Machine Learning yönetilen işlem kaynakları için geçerli olduğundan, önceki bölümde listelenen VM Core kotasından ayrıdır.

Bu bölümdeki çeşitli VM ailesi çekirdek kotaları, toplam abonelik çekirdek kotaları ve kaynakları için sınırları artırmak üzere [bir kota artışı isteyin](#request-quota-increases) .

Kullanılabilir kaynaklar:
+ Abonelik teklif türüne bağlı olarak, **bölge başına adanmış çekirdekler** varsayılan değer olan 24 ile 300 arasında olmalıdır. Her VM ailesi için abonelik başına adanmış çekirdek sayısını artırabilirsiniz. NCv2, NCv3 veya ND serisi gibi özelleştirilmiş sanal makine aileleri, varsayılan sıfır çekirdeklerle başlar.

+ Abonelik teklifi türüne bağlı olarak, **bölge başına düşük öncelikli çekirdekler** 100 ile 3.000 arasında varsayılan sınıra sahiptir. Abonelik başına düşük öncelikli çekirdek sayısı artırılabilir ve VM aileleri arasında tek bir değer olabilir.

+ **Bölge başına kümelerin** varsayılan 200 limiti vardır. Bunlar, bir eğitim kümesi ve bir işlem örneği arasında paylaşılır. (Bir işlem örneği, kota amaçları için tek düğümlü bir küme olarak kabul edilir.)

> [!TIP]
> Kota artışı için gereken VM ailesi hakkında daha fazla bilgi edinmek için [Azure 'da sanal makine boyutlarını](../virtual-machines/sizes.md)inceleyin. Örneğin GPU VM aileleri, Aile adında bir "N" ile başlar (örn. NCv3 serisi)

Aşağıdaki tabloda, platformda ek sınırlamalar gösterilmektedir. Lütfen bir özel durum istemek için bir **Teknik** destek bileti aracılığıyla AzureML ürün ekibine ulaşın.

| **Kaynak veya eylem** | **Üst sınır** |
| --- | --- |
| Her kaynak grubu için çalışma alanı | 800 |
| Tek bir Azure Machine Learning COMPUTE (AmlCompute) **kümesi** kurulumunda, iletişim etkin olmayan bir havuz olarak (MPI işleri çalıştırılamaz) düğümler | 100 düğüm, ancak 65000 ' e kadar düğüm yapılandırılabilir |
| Tek bir paralel çalıştırma adımındaki düğümler Azure Machine Learning Işlem (AmlCompute) kümesi üzerinde **çalıştırılır** | 100 düğüm, ancak kümeniz yukarıya ölçeklendirmeye ayarlandığında, 65000 ' e kadar düğüm yapılandırılabilir |
| Tek bir Azure Machine Learning Işlem (AmlCompute) **kümesi** içindeki düğümler iletişim etkin bir havuz olarak ayarlandı | 300 düğüm, ancak 4000 ' e kadar düğüm yapılandırılabilir |
| Tek bir Azure Machine Learning Işlem (AmlCompute) **kümesi** içindeki DÜĞÜMLER, RDMA özellıklı bir VM ailesinde iletişim etkin bir havuz olarak ayarlandı | 100 düğümleri |
| Tek bir MPı içindeki düğümler Azure Machine Learning Işlem (AmlCompute) kümesi üzerinde **çalışır** | 100 düğüm, ancak 300 düğümlere artırılabilir |
| Düğüm başına GPU MPı işlemi | 1-4 |
| Düğüm başına GPU çalışanları | 1-4 |
| İş ömrü | 21 gün<sup>1</sup> |
| Düşük öncelikli bir düğümdeki iş ömrü | 7 gün<sup>2</sup> |
| Düğüm başına parametre sunucuları | 1 |

<sup>1</sup> en fazla ömür süresi, bir çalıştırmanın başladığı ve bittiği zaman arasındaki süredir. Tamamlanan çalıştırmalar süresiz olarak devam ederse. Maksimum yaşam süresi içinde tamamlanmayan çalışma verilerine erişilemiyor.
<sup>2</sup> bir kapasite kısıtlaması olduğunda düşük öncelikli bir düğümdeki 2 iş değiştirilebilir. İşinizdeki denetim noktalarını uygulamanızı öneririz.

#### <a name="azure-machine-learning-pipelines"></a>Azure Machine Learning işlem hatları
[Azure Machine Learning işlem hatları](concept-ml-pipelines.md) Aşağıdaki sınırlara sahiptir.

| **Kaynak** | **Sınırlı** |
| --- | --- |
| İşlem hattı adımları | 30.000 |
| Her kaynak grubu için çalışma alanı | 800 |

### <a name="virtual-machines"></a>Sanal makineler
Her Azure aboneliğinin, tüm hizmetler genelinde sanal makine sayısıyla sınırlı bir limiti vardır. Sanal makine çekirdekleri, boyut serisi başına bölgesel toplam sınıra ve bölgesel sınıra sahiptir. Her iki sınır de ayrı olarak zorlanır.

Örneğin, ABD Doğu toplam VM çekirdek limiti 30, A serisi çekirdek limiti 30 ve D serisi çekirdek limiti 30 olan bir abonelik düşünün. Bu aboneliğin 30 a1 VM 'Leri, 30 D1 VM 'yi veya bir toplam 30 çekirdeği aşmayan bir bileşimini dağıtmaya izin verilir.

Aşağıdaki tabloda gösterilen değerlerin üstünde sanal makineler için sınır yükseltemezsiniz.

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../../includes/azure-subscription-limits-azure-resource-manager.md)]

### <a name="container-instances"></a>Container Instances

Daha fazla bilgi için bkz. [Container Instances sınırları](../azure-resource-manager/management/azure-subscription-service-limits.md#container-instances-limits).

### <a name="storage"></a>Depolama
Azure depolama 'nın, her abonelik için bölge başına 250 depolama hesabı sınırlaması vardır. Bu sınır, hem standart hem de Premium Depolama hesaplarını içerir.

Limiti artırmak için [Azure desteği](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/)aracılığıyla bir istek yapın. Azure depolama ekibi, büyük/küçük harf durumunu inceleyerek bir bölge için en fazla 250 depolama hesabı onaylayabilir.


## <a name="workspace-level-quotas"></a>Çalışma alanı düzeyinde kotalar

Aynı abonelikteki birden çok [çalışma alanı](concept-workspace.md) arasında Azure Machine Learning işlem hedefi ayırmayı yönetmek için çalışma alanı düzeyinde kotalar kullanın.

Varsayılan olarak, tüm çalışma alanları VM aileleri için abonelik düzeyi kotasıyla aynı kotayı paylaşır. Ancak, bir abonelikteki çalışma alanlarında ayrı VM aileleri için maksimum kota ayarlayabilirsiniz. Bu, kapasiteyi paylaşmanızı ve kaynak çekişme sorunlarından kaçınmanızı sağlar.

1. Aboneliğinizdeki herhangi bir çalışma alanına gidin.
1. Sol bölmede, **kullanımlar + kotalar**' ı seçin.
1. Kotaları görüntülemek için **kotaları Yapılandır** sekmesini seçin.
1. Bir VM ailesini genişletin.
1. Bu VM ailesi altında listelenen herhangi bir çalışma alanında bir kota sınırı ayarlayın.

Abonelik düzeyi kotasından daha yüksek bir negatif değer veya değer ayarlayamazsınız.

[![Azure Machine Learning çalışma alanı düzeyi kotasını gösteren ekran görüntüsü.](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)](./media/how-to-manage-quotas/azure-machine-learning-workspace-quota.png)

> [!NOTE]
> Çalışma alanı düzeyinde bir kota ayarlamak için abonelik düzeyinde izinlere ihtiyacınız vardır.

## <a name="view-your-usage-and-quotas"></a>Kullanımınızı ve kotaları görüntüleme

Sanal makineler, depolama veya ağ gibi çeşitli Azure kaynaklarına yönelik kotayı görüntülemek için Azure portal kullanın:

1. Sol bölmede, **tüm hizmetler** ' i seçin ve **genel** kategorisi altında **abonelikler** ' i seçin.

2. Abonelikler listesinden, kotasını Aradığınız aboneliği seçin.

3. Geçerli kota limitlerinizi ve kullanımınızı görüntülemek için **kullanım + kotalar** ' ı seçin. Sağlayıcıyı ve konumları seçmek için filtreleri kullanın. 

Aboneliğinizde Azure Machine Learning işlem kotasını diğer Azure kotalarından ayrı olarak yönetirsiniz: 

1. Azure portal **Azure Machine Learning** çalışma alanınıza gidin.

2. Sol bölmede, **destek + sorun giderme** bölümünde, geçerli kota limitlerinizi ve kullanımınızı görüntülemek için **kullanım + kotalar** ' i seçin.

3. Kota sınırlarını görüntülemek için bir abonelik seçin. İlgilendiğiniz bölgeye filtre uygulayın.

4. Abonelik düzeyinde bir görünüm ve çalışma alanı düzeyinde bir görünüm arasında geçiş yapabilirsiniz.

## <a name="request-quota-increases"></a>Kota artışı isteme

Sınırı veya kotayı varsayılan sınırın üzerinde yükseltmek için, ücretsiz [bir çevrimiçi müşteri destek isteği açın](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest/) .

Önceki tablolarda gösterilen en büyük değerlerin üzerine sınır yükseltemezsiniz. Maksimum sınır yoksa, kaynağın sınırını ayarlayamazsınız.

Bir kota artışı istediğinizde, göz önünde bulundurmanız gereken hizmeti seçin. Örneğin Azure Machine Learning, Container Instances veya depolama ' yı seçin. Azure Machine Learning işlem için, önceki adımlarda kotayı görüntülerken **Kota iste** düğmesini seçebilirsiniz.

> [!NOTE]
> [Ücretsiz deneme abonelikleri](https://azure.microsoft.com/offers/ms-azr-0044p) sınır veya kota artışına uygun değildir. Ücretsiz bir deneme aboneliğiniz varsa, bir [Kullandıkça Öde](https://azure.microsoft.com/offers/ms-azr-0003p/) aboneliğine yükseltebilirsiniz. Daha fazla bilgi için bkz. [Azure Ücretsiz deneme sürümünü Kullandıkça Öde](../cost-management-billing/manage/upgrade-azure-subscription.md) ve [ücretsiz Azure hesabına yükseltme hakkında SSS](https://azure.microsoft.com/free/free-account-faq).

## <a name="private-endpoint-and-private-dns-quota-increases"></a>Özel uç nokta ve özel DNS kota artışları

Bir abonelikte oluşturabileceğiniz özel uç noktaların ve özel DNS bölgelerinin sayısı için sınırlamalar vardır.

Azure Machine Learning (müşteri) aboneliğinizde kaynak oluşturuyor, ancak bazı senaryolar Microsoft 'a ait bir abonelikte kaynak oluşturuyor.

 Aşağıdaki senaryolarda, Microsoft 'a ait abonelikte bir kota indirimi istemeniz gerekebilir:

* Azure özel bağlantısı, müşteri tarafından yönetilen bir anahtarla etkin çalışma alanı (CMK)
* Çalışma alanınıza Özel Bağlantı özellikli Azure Kubernetes Service kümesi ekleme

Bu senaryolar için bir kesinti istemek üzere aşağıdaki adımları kullanın:

1. [Bir Azure destek Isteği oluşturun](../azure-portal/supportability/how-to-create-azure-support-request.md#create-a-support-request) ve __temel bilgiler__ bölümünde aşağıdaki seçenekleri belirleyin:

    | Alan | Seçim |
    | ----- | ----- |
    | Sorun türü | **Teknik** |
    | Hizmet | **Hizmetlerim**. Ardından aşağı açılan listeden __Machine Learning__ ' yi seçin. |
    | Sorun türü | **Çalışma alanı yapılandırması ve güvenliği** |
    | Sorun alt türü | **Özel uç nokta ve Özel DNS bölge kesintisi isteği** |

2. __Ayrıntılar__ bölümünde, Azure bölgesini ve kullanmayı planladığınız senaryoyu sağlamak için __Açıklama__ alanını kullanın. Birden çok abonelik için kota artışı istemeniz gerekiyorsa, bu alandaki abonelik kimliklerini listeleyin.

3. İsteği oluşturmak için __Oluştur__ ' u seçin.

:::image type="content" source="media/how-to-manage-quotas/quota-increase-private-endpoint.png" alt-text="Özel bir uç noktanın ve özel DNS kota artışı isteğinin ekran görüntüsü.":::

## <a name="next-steps"></a>Sonraki adımlar

+ [Azure Machine Learning maliyetlerini planlayın ve yönetin](concept-plan-manage-cost.md)
+ [Azure Machine Learning hizmet limitleri](resource-limits-quotas-capacity.md)