---
title: Hizmet kotaları ve limitleri
description: Varsayılan Azure Batch kotaları, sınırları ve kısıtlamaları hakkında bilgi edinin ve kota artışlarının nasıl isteneceğini öğrenin
ms.topic: conceptual
ms.date: 04/06/2021
ms.custom: seodec18
ms.openlocfilehash: 6e17a90cc573205bcb964a0428e0b7320323b8a6
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106553558"
---
# <a name="batch-service-quotas-and-limits"></a>Batch hizmet kotaları ve limitleri

Diğer Azure hizmetlerinde olduğu gibi, Batch hizmetiyle ilişkili belirli kaynaklarda sınırlamalar vardır. Bu limitlerin birçoğu, Azure tarafından abonelik veya hesap düzeyinde uygulanan varsayılan kotalardır.

Batch iş yüklerinizi tasarlarken ve ölçeklendirebilmeniz için bu kotaları göz önünde bulundurun. Örneğin, havuzunuz belirttiğiniz işlem düğümlerinin hedef sayısına ulaşmazsa, Batch hesabınız için çekirdek kota sınırına ulaşmış olabilirsiniz.

Tek bir Batch hesabında birden fazla Batch iş yükü çalıştırabilir ya da iş yüklerinizi aynı abonelikte ancak farklı Azure bölgelerindeki Batch hesapları arasında dağıtabilirsiniz.

Batch 'de üretim iş yüklerini çalıştırmayı planlıyorsanız, varsayılan olarak bir veya daha fazla kotayı artırmanız gerekebilir. Kotayı artırmak için ücret ödemeden [bir kota artışı](#increase-a-quota) isteyebilirsiniz.

## <a name="resource-quotas"></a>Kaynak kotaları

Kota, kapasite garantisi değil bir sınıra sahiptir. Büyük ölçekli kapasite gereksinimleriniz varsa lütfen Azure desteğine başvurun.

Ayrıca kotaların garantili değerler olmadığına de unutmayın. Kotalar, Batch hizmetinden değişikliklere göre değişiklik gösterebilir veya bir Kullanıcı isteği kota değerini değiştirebilir.

[!INCLUDE [azure-batch-limits](../../includes/azure-batch-limits.md)]

## <a name="core-quotas"></a>Çekirdek kotalar

### <a name="cores-quotas-in-batch-service-mode"></a>Batch hizmeti modundaki çekirdek kotaları

Toplu Işlem tarafından desteklenen her bir VM Serisi için çekirdek kotalar vardır. Bu çekirdek kotalar Azure portal **Kotalar** sayfasında görüntülenir. VM Serisi kota sınırları, aşağıda açıklandığı gibi bir destek isteğiyle de yapılandırılabilir. Adanmış düğümler için Batch her bir VM Serisi için bir çekirdek kota sınırı ve tüm Batch hesabı için toplam çekirdek kota sınırı uygular. Düşük öncelikli düğümler için Batch, farklı VM serileri arasında ayrım yapmadan Batch hesabı için yalnızca toplam çekirdek kotasını zorlar.

### <a name="cores-quotas-in-user-subscription-mode"></a>Kullanıcı abonelik modundaki çekirdek kotaları

Havuz ayırma modu **Kullanıcı aboneliğine** ayarlanmış bir [Batch hesabı](accounts.md) oluşturduysanız, bir havuz oluşturulduğunda veya yeniden boyutlandırılırken, Batch VM 'leri ve diğer kaynaklar doğrudan aboneliğinizde oluşturulur. Azure Batch çekirdek kotaları, bölgesel işlem çekirdekleri, seri başına bilgi işlem çekirdekleri ve diğer kaynaklar kullanılır ve zorlanır.

Bu kotalar hakkında daha fazla bilgi edinmek için bkz. [Azure aboneliği ve hizmet limitleri, Kotalar ve kısıtlamalar](../azure-resource-manager/management/azure-subscription-service-limits.md).

## <a name="pool-size-limits"></a>Havuz boyutu sınırları

Havuz boyutu sınırları Batch hizmeti tarafından ayarlanır. [Kaynak kotalarından](#resource-quotas)farklı olarak, bu değerler değiştirilemez. Yalnızca düğümler arası iletişim ve özel görüntülere sahip havuzlar standart kotadan farklı kısıtlamalara sahiptir.

| **Kaynak** | **Maksimum sınır** |
| --- | --- |
| **[Düğüm içi iletişim etkin havuzunda](batch-mpi.md) işlem düğümleri**  ||
| Batch hizmeti havuz ayırma modu | 100 |
| Batch abonelik havuzu ayırma modu | 80 |
| **[Yönetilen görüntü kaynağı 1 ile oluşturulan havuzdaki](batch-custom-images.md)işlem düğümleri**<sup></sup> ||
| Ayrılmış düğümler | 2000 |
| Düşük öncelikli düğümler | 1000 |

düğüm içi iletişim olmayan havuzlar için <sup>1</sup> .

## <a name="other-limits"></a>Diğer sınırlar

Bu ek sınırlar Batch hizmeti tarafından ayarlanır. [Kaynak kotalarından](#resource-quotas)farklı olarak, bu değerler değiştirilemez.

| **Kaynak** | **Maksimum sınır** |
| --- | --- |
| İşlem düğümü başına [eşzamanlı görevler](batch-parallel-node-tasks.md) | 4 x düğüm çekirdeği sayısı |
| Batch hesabı başına [uygulamalar](batch-application-packages.md) | 20 |
| Uygulama başına uygulama paketleri | 40 |
| Havuz başına uygulama paketleri | 10 |
| En fazla görev ömrü | 180 gün<sup>1</sup> |
| İşlem düğümü başına [bağlama](virtual-file-mount.md) | 10 |
| Havuz başına sertifika sayısı | 12 |

<sup>1</sup> bir görevin, ne zaman tamamlandığında işe eklendiği zaman, 180 gün olur. Tamamlanan görevler yedi gün boyunca devam ederse; Maksimum yaşam süresi içinde tamamlanmayan görevlere yönelik verilere erişilemiyor.

## <a name="view-batch-quotas"></a>Batch kotalarını görüntüleme

Batch hesabı kotalarınızı [Azure Portal](https://portal.azure.com)görüntülemek için:

1. **Batch hesapları**' nı seçin ve ilgilendiğiniz Batch hesabını seçin.
1. Batch hesabının menüsünde **Kotalar** ' ı seçin.
1. Batch hesabına uygulanmış olan kotaları görüntüleyin.

:::image type="content" source="./media/batch-quota-limit/account-quota-portal.png" alt-text="Azure portal Batch hesabı kotalarını gösteren ekran görüntüsü.":::

## <a name="increase-a-quota"></a>Kotayı artırma

[Azure Portal](https://portal.azure.com) veya [Azure kota REST API](#azure-quota-rest-api)kullanarak Batch hesabınız veya aboneliğiniz için bir kota artışı isteyebilirsiniz.

Kota artışı türü, Batch hesabınızın havuz ayırma moduna bağlıdır. Kota artışı istemek için, kotayı artırmak istediğiniz VM serisini dahil etmeniz gerekir. Kota artışı uygulandığında, tüm VM dizisine uygulanır.

Destek isteğinizi gönderdikten sonra, Azure desteği sizinle iletişim kuracaktır. Kota istekleri, birkaç dakika veya en fazla iki iş günü içinde tamamlanabilir.

### <a name="azure-portal"></a>Azure portalı

1. **Kotalar** sayfasında, **istek kotası artışı**' nı seçin. Alternatif olarak, Portal panonuzda **Yardım + Destek** kutucuğunu (veya portalın sağ üst köşesindeki soru işareti (**?**) seçerek **Yeni destek isteği** ' ni seçebilirsiniz.

1. **Temel bilgiler**:

    1. **Sorun türü** için **hizmet ve abonelik sınırları (kotalar)** öğesini seçin.
    1. Aboneliğinizi seçin.
    1. **Kota türü** için **Batch**' i seçin.
    1. Devam etmek için **İleri** seçeneğini belirleyin.

1. **Ayrıntılar** bölümünde:

    1. **Ayrıntıları sağla** bölümünde, konumu, kota türünü ve Batch hesabını (varsa) belirtin ve ardından, arttırabileceğiniz kotayı seçin.

       :::image type="content" source="media/batch-quota-limit/quota-increase.png" alt-text="Kota artışı isteği istenirken kota ayrıntıları ekranının ekran görüntüsü.":::

       Kota türleri şunlardır:

       - **Batch hesabı başına**  
         Adanmış ve düşük öncelikli çekirdekler ve işlerin ve havuzların sayısı dahil olmak üzere tek bir Batch hesabına özgü kota artışları istemek için bu seçeneği kullanın.

         Bu seçeneği belirlerseniz, bu isteğin uygulanacağı Batch hesabını belirtin ve ardından güncelleştirmek istediğiniz kotayı seçin. Her kaynak için istediğiniz yeni limiti sağlayın.

         Düşük öncelikli kota, tüm VM serileri genelinde tek bir değerdir. Kısıtlanmış SKU 'Lara ihtiyacınız varsa **düşük öncelikli çekirdekler** ' ı seçmeniz ve isteğe sanal makine aileleri eklemeniz gerekir.

       - **Bu bölgedeki tüm hesaplar**  
         Bu seçeneği, bir bölgedeki tüm Batch hesaplarına uygulanan kota artışına (örneğin, her abonelik için bölge başına toplu Işlem hesaplarının sayısı) izin vermek için kullanın.

    1. **Destek yöntemi**' nde, [iş etki](https://aka.ms/supportseverity) ve tercih ettiğiniz iletişim yönteminiz ve destek diliniz doğrultusunda bir **önem derecesi** seçin.

    1. **İletişim bilgileri**' nde, gerekli iletişim ayrıntılarını doğrulayın ve girin.

1. Destek isteğini göndermek için **gözden geçir + oluştur**' u seçin ve **Oluştur** ' u seçin.

### <a name="azure-quota-rest-api"></a>Azure kota REST API

Abonelik düzeyinde veya Batch hesabı düzeyinde bir kota artışı istemek için Azure kota REST API kullanabilirsiniz.

Ayrıntılar ve örnekler için bkz. [Azure desteği REST API ile kota artışı isteği](/rest/api/support/quota-payload#azure-batch).

## <a name="related-quotas-for-vm-pools"></a>VM havuzları için ilgili kotalar

[Bir Azure sanal ağında dağıtılan sanal makine yapılandırmasındaki toplu iş havuzları,](batch-virtual-network.md) otomatik olarak ek Azure ağ kaynakları ayırır. Bu kaynaklar, Batch havuzu oluşturulurken sağlanan sanal ağı içeren abonelikte oluşturulur.

Bir sanal ağdaki her 100 havuz düğümü için aşağıdaki kaynaklar oluşturulur:

- Bir [ağ güvenlik grubu](../virtual-network/network-security-groups-overview.md#network-security-groups)
- Bir [genel IP adresi](../virtual-network/public-ip-addresses.md)
- Bir [yük dengeleyici](../load-balancer/load-balancer-overview.md)

Bu kaynaklar, aboneliğin [kaynak kotalarıyla](../azure-resource-manager/management/azure-subscription-service-limits.md) sınırlıdır. Büyük havuz dağıtımlarını bir sanal ağda planlıyorsanız, bir veya daha fazla kaynağın bir veya daha fazlası için bir kota artışı istemeniz gerekebilir.

## <a name="next-steps"></a>Sonraki adımlar

- [Batch hizmeti iş akışı ve](batch-service-workflow-features.md) havuzlar, düğümler, işler ve görevler gibi birincil kaynaklar hakkında bilgi edinin.
- [Azure aboneliği ve hizmet limitleri, Kotalar ve kısıtlamalar](../azure-resource-manager/management/azure-subscription-service-limits.md)hakkında bilgi edinin.