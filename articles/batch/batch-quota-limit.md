---
title: Hizmet kotaları ve limitleri-Azure Batch | Microsoft Docs
description: Varsayılan Azure Batch kotaları, sınırları ve kısıtlamaları hakkında bilgi edinin ve kota artışlarının nasıl isteneceğini öğrenin
services: batch
documentationcenter: ''
author: laurenhughes
manager: gwallace
editor: ''
ms.assetid: 28998df4-8693-431d-b6ad-974c2f8db5fb
ms.service: batch
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/13/2019
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: 8b089a1b32ee70479072522372c060713108957c
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71350101"
---
# <a name="batch-service-quotas-and-limits"></a>Batch hizmet kotaları ve limitleri

Diğer Azure hizmetlerinde olduğu gibi, Batch hizmetiyle ilişkili belirli kaynaklarda sınırlamalar vardır. Bu limitlerin birçoğu, Azure tarafından abonelik veya hesap düzeyinde uygulanan varsayılan kotalardır. Bu makalede bu varsayılanlar ve kota artışlarının nasıl isteneceğini ele alınmaktadır.

Batch iş yüklerinizi tasarlarken ve ölçeklendirebilmeniz için bu kotaları göz önünde bulundurun. Örneğin, havuzunuz belirttiğiniz işlem düğümlerinin hedef sayısına ulaşmazsa, Batch hesabınız için çekirdek kota sınırına ulaşmış olabilirsiniz.

Tek bir Batch hesabında birden fazla Batch iş yükü çalıştırabilir ya da iş yüklerinizi aynı abonelik ve farklı Azure bölgelerindeki Batch hesapları arasında dağıtabilirsiniz.

Batch 'de üretim iş yüklerini çalıştırmayı planlıyorsanız, varsayılan olarak bir veya daha fazla kotayı artırmanız gerekebilir. Bir kota yükseltmek istiyorsanız, çevrimiçi bir [Müşteri Destek isteğini](#increase-a-quota) ücretsiz olarak açabilirsiniz.

## <a name="resource-quotas"></a>Kaynak kotaları

Kota kapasitesini garanti bir kredi sınırına ' dir. Büyük ölçekli kapasite gereksinimleriniz varsa lütfen Azure desteğine başvurun.

Ayrıca kotaların garantili değerler olmadığına de unutmayın. Kotalar, Batch hizmetinden değişikliklere göre değişiklik gösterebilir veya bir Kullanıcı isteği kota değerini değiştirebilir.

[!INCLUDE [azure-batch-limits](../../includes/azure-batch-limits.md)]

### <a name="cores-quotas-in-user-subscription-mode"></a>Kullanıcı abonelik modundaki çekirdek kotaları

Havuz ayırma modu **Kullanıcı aboneliğine**ayarlanmış bir Batch hesabı oluşturduysanız, Kotalar farklı şekilde uygulanır. Bu modda, bir havuz oluşturulduğunda Batch VM 'Leri ve diğer kaynaklar doğrudan aboneliğinizde oluşturulur. Azure Batch çekirdekler kotaları, bu modda oluşturulan bir hesaba uygulanmaz. Bunun yerine, bölgesel işlem çekirdekleri ve diğer kaynaklar için aboneliğinizdeki kotalar uygulanır. [Azure aboneliği ve hizmet sınırları, Kotalar ve kısıtlamalarında](../azure-subscription-service-limits.md)Bu kotalar hakkında daha fazla bilgi edinin.

## <a name="pool-size-limits"></a>Havuz boyutu sınırları

Havuz boyutu sınırları Batch hizmeti tarafından ayarlanır. [Kaynak kotalarından](#resource-quotas)farklı olarak, bu değerler değiştirilemez. Yalnızca düğümler arası iletişim ve özel görüntülere sahip havuzlar standart kotadan farklı kısıtlamalara sahiptir.

| **Kaynak** | **Üst Sınır** |
| --- | --- |
| **[Düğüm içi iletişim etkin havuzunda](batch-mpi.md) işlem düğümleri**  ||
| Batch hizmeti havuz ayırma modu | 100 |
| Batch abonelik havuzu ayırma modu | 80 |
| **[Yönetilen görüntü kaynağı 1 ile oluşturulan havuzdaki](batch-custom-images.md)işlem düğümleri**<sup></sup> ||
| Adanmış düğümler | 2000 |
| Düşük öncelikli düğümler | 1000 |

düğüm içi iletişim olmayan havuzlar için <sup>1</sup> .

## <a name="other-limits"></a>Diğer sınırlar

Batch hizmeti tarafından ayarlanan ek sınırlar. [Kaynak kotalarından](#resource-quotas)farklı olarak, bu değerler değiştirilemez.

| **Kaynak** | **Üst Sınır** |
| --- | --- |
| İşlem düğümü başına [eşzamanlı görevler](batch-parallel-node-tasks.md) | 4 x düğüm çekirdeği sayısı |
| Batch hesabı başına [uygulamalar](batch-application-packages.md) | 20 |
| Uygulama başına uygulama paketleri | 40 |
| Havuz başına uygulama paketleri | 10 |
| En fazla görev ömrü | 180 gün<sup>1</sup> |
| İşlem düğümü başına [bağlama](virtual-file-mount.md) | 10 |

<sup>1</sup> bir görevin, ne zaman tamamlandığında işe eklendiği zaman, 180 gün olur. Tamamlanan görevler yedi gün boyunca devam ederse; Maksimum yaşam süresi içinde tamamlanmayan görevlere yönelik verilere erişilemiyor.

## <a name="view-batch-quotas"></a>Batch kotalarını görüntüleme

Batch hesabı kotalarınızı [Azure Portal][portal]görüntüleyin.

1. Portalda **Batch hesapları** ' nı seçin ve ardından ilgilendiğiniz Batch hesabını seçin.
1. Batch hesabının menüsünde **Kotalar** ' ı seçin.
1. Batch hesabına uygulanmış olan kotaları görüntüleme

    ![Batch hesabı kotaları][account_quotas]

## <a name="increase-a-quota"></a>Kotayı artırma

[Azure Portal][portal]kullanarak Batch hesabınız veya aboneliğiniz için bir kota artışı istemek üzere bu adımları izleyin. Kota artışı türü, Batch hesabınızın havuz ayırma moduna bağlıdır. Kota artışı istemek için, kotayı artırmak istediğiniz VM serisini dahil etmeniz gerekir. Kota artışı uygulandığında, tüm VM dizisine uygulanır.

### <a name="increase-cores-quota-in-batch"></a>Toplu Işteki çekirdek kotasını artırma 

1. Portal panonuzda **Yardım + Destek** kutucuğunu veya portalın sağ üst köşesindeki soru işaretini ( **?** ) seçin.
1. **Yeni destek isteği** > **temelleri**' ni seçin.
1. **Temel bilgiler**:
   
    a. **Sorun türü** > **hizmeti ve abonelik limitleri (kotalar)**
   
    b. Aboneliğinizi seçin.
   
    c. **Kota türü** > **toplu işlem**
      
    **İleri**’yi seçin.
    
1. İçinde **ayrıntıları**:
      
    a. **Ayrıntıları sağla**bölümünde konumu, kota türünü ve Batch hesabını belirtin.
    
    ![Toplu kota artışı][quota_increase]

    Kota türleri şunlardır:

    * **Batch hesabı başına**  
        Adanmış ve düşük öncelikli çekirdekler ve iş ve havuz sayısı dahil olmak üzere tek bir Batch hesabına özgü değerler.
        
    * **Bölge başına**  
        Bir bölgedeki tüm Batch hesaplarına uygulanan ve abonelik başına bölge başına toplu Işlem hesaplarının sayısını içeren değerler.

    Düşük öncelikli kota, tüm VM serileri genelinde tek bir değerdir. Kısıtlanmış SKU 'Lara ihtiyacınız varsa **düşük öncelikli çekirdekler** ' ı seçmeniz ve isteğe sanal makine aileleri eklemeniz gerekir.

    b. [İşletmenizin etkilerine][support_sev]göre **önem derecesi** seçin.

    **İleri**’yi seçin.

1. **İletişim bilgileri**:
   
    a. **Tercih edilen bir iletişim yöntemi**seçin.
   
    b. Gerekli iletişim ayrıntılarını doğrulayın ve girin.
   
    Destek isteğini göndermek için **Oluştur** ' u seçin.

Destek isteğinizi gönderdikten sonra, Azure desteği sizinle iletişim kuracaktır. Kota istekleri, birkaç dakika veya en fazla iki iş günü içinde tamamlanabilir.

## <a name="related-quotas-for-vm-pools"></a>VM havuzları için ilgili kotalar

Bir Azure sanal ağında dağıtılan sanal makine yapılandırmasındaki toplu iş havuzları, otomatik olarak ek Azure ağ kaynakları ayırır. Bir sanal ağdaki her 50 havuz düğümü için aşağıdaki kaynaklar gereklidir:

* Bir [ağ güvenlik grubu](../virtual-network/security-overview.md#network-security-groups)
* Bir [genel IP adresi](../virtual-network/virtual-network-ip-addresses-overview-arm.md)
* Bir [yük dengeleyici](../load-balancer/load-balancer-overview.md)

Bu kaynaklar, Batch havuzu oluşturulurken sağlanan sanal ağı içeren abonelikte ayrılır. Bu kaynaklar, aboneliğin [kaynak kotalarıyla](../azure-subscription-service-limits.md) sınırlıdır. Büyük havuz dağıtımlarını bir sanal ağda planlarsanız, bu kaynaklar için aboneliğin kotalarını kontrol edin. Gerekirse, **Yardım + Destek**' i seçerek Azure Portal bir artış isteyin.


## <a name="related-topics"></a>İlgili konular
* [Azure portal kullanarak Azure Batch hesabı oluşturma](batch-account-create-portal.md)
* [Azure Batch özelliğe genel bakış](batch-api-basics.md)
* [Azure aboneliği ve hizmet limitleri, kotalar ve kısıtlamalar](../azure-subscription-service-limits.md)

[portal]: https://portal.azure.com
[portal_classic_increase]: https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/
[support_sev]: https://aka.ms/supportseverity

[account_quotas]: ./media/batch-quota-limit/accountquota_portal.png
[quota_increase]: ./media/batch-quota-limit/quota-increase.png
