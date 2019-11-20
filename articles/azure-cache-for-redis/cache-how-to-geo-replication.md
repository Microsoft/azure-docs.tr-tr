---
title: Redsıs için Azure önbelleği için Coğrafi çoğaltmayı ayarlama | Microsoft Docs
description: Coğrafi bölgeler arasında Redsıs örnekleri için Azure önbelleğinizi çoğaltmayı öğrenin.
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 03/06/2019
ms.author: yegu
ms.openlocfilehash: ce50c665fa79c361f638fda4ec373d5215c407f8
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/16/2019
ms.locfileid: "74129422"
---
# <a name="how-to-set-up-geo-replication-for-azure-cache-for-redis"></a>Redsıs için Azure önbelleği için Coğrafi çoğaltmayı ayarlama

Coğrafi çoğaltma, Redsıs örnekleri için iki Premium katman Azure önbelleğinin bağlanmasına yönelik bir mekanizma sağlar. Birincil bağlı önbellek ve diğeri ikincil bağlantılı önbellek olarak bir önbellek seçilir. İkincil bağlantılı önbellek salt okunurdur ve birincil önbelleğe yazılan veriler ikincil bağlantılı önbelleğe çoğaltılır. Bu işlev, Azure bölgeleri arasında bir önbelleği çoğaltmak için kullanılabilir. Bu makale, Redsıs örnekleri için Premium katman Azure önbelleğiniz için Coğrafi çoğaltmayı yapılandırmaya yönelik bir kılavuz sağlar.

## <a name="geo-replication-prerequisites"></a>Coğrafi çoğaltma önkoşulları

İki önbellek arasında Coğrafi çoğaltmayı yapılandırmak için aşağıdaki önkoşulların karşılanması gerekir:

- Her iki önbellek de [Premium katman](cache-premium-tier-intro.md) önbellekler.
- Her iki önbellek de aynı Azure aboneliğinde bulunur.
- İkincil bağlantılı önbellek, birincil bağlı önbellekten aynı önbellek boyutu veya daha büyük bir önbellek boyutudur.
- Her iki önbellek de oluşturulur ve çalışır durumda.

Bazı özellikler coğrafi çoğaltma ile desteklenmez:

- Kalıcılık, coğrafi çoğaltma ile desteklenmez.
- Her iki önbellekte de kümeleme etkinse ve aynı sayıda parça varsa kümeleme desteklenir.
- Aynı VNET 'teki önbellekler desteklenir.
- Farklı VNET 'lerdeki önbellekler uyarılarla desteklenir. Daha fazla bilgi için bkz. [VNET 'te önbellekler ile Coğrafi çoğaltmayı kullanabilir miyim?](#can-i-use-geo-replication-with-my-caches-in-a-vnet)

Coğrafi Çoğaltma yapılandırıldıktan sonra, aşağıdaki kısıtlamalar bağlı önbellek çiftiniz için geçerlidir:

- İkincil bağlantılı önbellek salt okunurdur; buradan okuyabilirsiniz, ancak buraya veri yazılamaz. 
- Bağlantı eklenmeden önce ikincil bağlantılı önbellekte bulunan tüm veriler kaldırılır. Coğrafi çoğaltma daha sonra kaldırılırsa, çoğaltılan veriler ikincil bağlantılı önbellekte kalır.
- Önbellekler bağlıyken her iki önbelleği [ölçeklendirebilirsiniz](cache-how-to-scale.md) .
- Önbellekte kümeleme etkinse [, parça sayısını değiştiremezsiniz](cache-how-to-premium-clustering.md) .
- Her iki önbellekte de kalıcılığı etkinleştiremezsiniz.
- Her iki önbellekten de [dışarı aktarabilirsiniz](cache-how-to-import-export-data.md#export) .
- İkincil bağlantılı önbelleğe [Içeri aktaramazsınız](cache-how-to-import-export-data.md#import) .
- Önbellekler bağlantısını kaldırana kadar bağlı önbelleği veya bunları içeren kaynak grubunu silemezsiniz. Daha fazla bilgi için bkz [. bağlı önbelleğinizi silmeye çalıştığımda işlem başarısız oldu mu?](#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)
- Önbellekler farklı bölgelerde ise, ağ çıkış maliyetleri bölgeler arasında taşınan veriler için geçerlidir. Daha fazla bilgi için bkz. [verileri Azure bölgelerinde çoğaltma maliyeti ne kadar sürer?](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)
- Birincil ve ikincil bağlantılı önbellek arasında otomatik yük devretme gerçekleşmez. İstemci uygulamasının yük devretmesi hakkında daha fazla bilgi ve bilgi için bkz. [İkincil bağlantılı önbelleğin yükünü devretme nasıl çalışır?](#how-does-failing-over-to-the-secondary-linked-cache-work)

## <a name="add-a-geo-replication-link"></a>Coğrafi çoğaltma bağlantısı ekleme

1. İki önbelleği coğrafi çoğaltma için birlikte bağlamak üzere, birincil bağlantılı önbellek olmasını istediğiniz önbelleğin kaynak menüsünde **coğrafi çoğaltma** ' ya tıklayın. Sonra, **coğrafi çoğaltma** dikey penceresinden **önbellek çoğaltma bağlantısı ekle** ' ye tıklayın.

    ![Bağlantı ekle](./media/cache-how-to-geo-replication/cache-geo-location-menu.png)

2. **Uyumlu önbellekler** listesinden amaçlanan ikincil önbelleğiniz adına tıklayın. İkincil önbelleğiniz listede görüntülenmiyorsa, ikincil önbelleğin [coğrafi çoğaltma ön koşullarının](#geo-replication-prerequisites) karşılandığını doğrulayın. Önbellekleri bölgeye göre filtrelemek için, haritadaki bölgeye tıklayarak yalnızca **uyumlu önbellekler** listesindeki önbellekleri görüntüleyin.

    ![Coğrafi çoğaltma ile uyumlu önbellekler](./media/cache-how-to-geo-replication/cache-geo-location-select-link.png)
    
    Ayrıca bağlama işlemini başlatabilir veya bağlam menüsünü kullanarak ikincil önbellek hakkındaki ayrıntıları görüntüleyebilirsiniz.

    ![Coğrafi çoğaltma bağlam menüsü](./media/cache-how-to-geo-replication/cache-geo-location-select-link-context-menu.png)

3. İki önbelleği birlikte bağlamak ve çoğaltma işlemini başlatmak için **bağlantı** ' ya tıklayın.

    ![Bağlantı önbellekleri](./media/cache-how-to-geo-replication/cache-geo-location-confirm-link.png)

4. Çoğaltma işleminin ilerlemesini **coğrafi çoğaltma** dikey penceresinde görebilirsiniz.

    ![Bağlama durumu](./media/cache-how-to-geo-replication/cache-geo-location-linking.png)

    Ayrıca, birincil ve ikincil önbellekler için **genel bakış** dikey penceresinde bağlama durumunu da görüntüleyebilirsiniz.

    ![Önbellek durumu](./media/cache-how-to-geo-replication/cache-geo-location-link-status.png)

    Çoğaltma işlemi tamamlandıktan sonra **bağlantı durumu** **başarılı**olarak değişir.

    ![Önbellek durumu](./media/cache-how-to-geo-replication/cache-geo-location-link-successful.png)

    Birincil bağlantılı önbellek, bağlama işlemi sırasında kullanılabilir kalır. İkincil bağlantılı önbellek, bağlama işlemi tamamlanana kadar kullanılamaz.

## <a name="remove-a-geo-replication-link"></a>Coğrafi çoğaltma bağlantısını kaldırma

1. İki önbellek arasındaki bağlantıyı kaldırmak ve Coğrafi çoğaltmayı durdurmak için, **coğrafi çoğaltma** dikey penceresinden **önbellekler bağlantısını** Kaldır ' a tıklayın.
    
    ![Önbelleklerin bağlantısını kaldır](./media/cache-how-to-geo-replication/cache-geo-location-unlink.png)

    Bağlantı kaldırma işlemi tamamlandığında, ikincil önbellek hem okuma hem de yazma işlemleri için kullanılabilir.

>[!NOTE]
>Coğrafi çoğaltma bağlantısı kaldırıldığında, birincil bağlantılı önbellekteki çoğaltılan veriler ikincil önbellekte kalır.
>
>

## <a name="geo-replication-faq"></a>Coğrafi çoğaltma hakkında SSS

- [Standart veya temel katman önbelleğiyle Coğrafi çoğaltmayı kullanabilir miyim?](#can-i-use-geo-replication-with-a-standard-or-basic-tier-cache)
- [Önbelleğim bağlama veya bağlantısı kaldırma işlemi sırasında kullanılabilir mi?](#is-my-cache-available-for-use-during-the-linking-or-unlinking-process)
- [İkiden fazla önbelleğim birlikte bağlanabilir miyim?](#can-i-link-more-than-two-caches-together)
- [Farklı Azure aboneliklerinden iki önbelleğe bağlanabilir miyim?](#can-i-link-two-caches-from-different-azure-subscriptions)
- [İki önbelleğe farklı boyutlarda bağlanabilir miyim?](#can-i-link-two-caches-with-different-sizes)
- [Kümeleme özelliği etkinken Coğrafi çoğaltmayı kullanabilir miyim?](#can-i-use-geo-replication-with-clustering-enabled)
- [VNET 'teki önbelleklerim ile Coğrafi çoğaltmayı kullanabilir miyim?](#can-i-use-geo-replication-with-my-caches-in-a-vnet)
- [Redsıs coğrafi çoğaltma için çoğaltma zamanlaması nedir?](#what-is-the-replication-schedule-for-redis-geo-replication)
- [Coğrafi çoğaltma çoğaltması ne kadar sürer?](#how-long-does-geo-replication-replication-take)
- [Çoğaltma kurtarma noktası garanti edilir mi?](#is-the-replication-recovery-point-guaranteed)
- [Coğrafi çoğaltmayı yönetmek için PowerShell veya Azure CLı kullanabilir miyim?](#can-i-use-powershell-or-azure-cli-to-manage-geo-replication)
- [Verileri Azure bölgeleri arasında çoğaltma maliyeti ne kadar sürer?](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)
- [Bağlı önbelleğinizi silmeye çalıştığımda işlem başarısız oldu mu?](#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)
- [İkincil bağlantılı önbelleğim için hangi bölgeyi kullanmalıyım?](#what-region-should-i-use-for-my-secondary-linked-cache)
- [İkincil bağlantılı önbelleğin yük devri nasıl çalışır?](#how-does-failing-over-to-the-secondary-linked-cache-work)

### <a name="can-i-use-geo-replication-with-a-standard-or-basic-tier-cache"></a>Standart veya temel katman önbelleğiyle Coğrafi çoğaltmayı kullanabilir miyim?

Hayır, coğrafi çoğaltma yalnızca Premium katman önbellekler için kullanılabilir.

### <a name="is-my-cache-available-for-use-during-the-linking-or-unlinking-process"></a>Önbelleğim bağlama veya bağlantısı kaldırma işlemi sırasında kullanılabilir mi?

- Bağlama sırasında, bağlama işlemi tamamlanırken birincil bağlantılı önbellek kullanılabilir kalır.
- Bağlama sırasında, ikincil bağlantılı önbellek, bağlama işlemi tamamlanana kadar kullanılamaz.
- Bağlantı kaldırılırken her iki önbellek de bağlantı kaldırılıyor işlemi tamamlandığında kullanılabilir kalır.

### <a name="can-i-link-more-than-two-caches-together"></a>İkiden fazla önbelleğim birlikte bağlanabilir miyim?

Hayır, iki önbelleği yalnızca bir araya bağlayabilirsiniz.

### <a name="can-i-link-two-caches-from-different-azure-subscriptions"></a>Farklı Azure aboneliklerinden iki önbelleğe bağlanabilir miyim?

Hayır, her iki önbellek de aynı Azure aboneliğinde olmalıdır.

### <a name="can-i-link-two-caches-with-different-sizes"></a>İki önbelleğe farklı boyutlarda bağlanabilir miyim?

Evet, ikincil bağlantılı önbelleğin birincil bağlı önbellekten daha büyük olduğu sürece.

### <a name="can-i-use-geo-replication-with-clustering-enabled"></a>Kümeleme özelliği etkinken Coğrafi çoğaltmayı kullanabilir miyim?

Evet, her iki önbellekte de aynı sayıda parça olduğu sürece.

### <a name="can-i-use-geo-replication-with-my-caches-in-a-vnet"></a>VNET 'teki önbelleklerim ile Coğrafi çoğaltmayı kullanabilir miyim?

Evet, VNET 'lerdeki önbelleklerin coğrafi yinelemesi uyarılarla desteklenir:

- Aynı VNET 'teki önbellekler arasında coğrafi çoğaltma desteklenir.
- Farklı VNET 'lerdeki önbellekler arasında coğrafi çoğaltma de desteklenir.
  - Sanal ağlar aynı bölgedeyse [VNET eşlemesi](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) veya [VPN Gateway VNET-VNet bağlantısı](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways#V2V)kullanarak bunları bağlayabilirsiniz.
  - Sanal ağlar farklı bölgelerde ise, temel iç yük dengeleyiciler içeren bir kısıtlama nedeniyle VNET eşlemesi kullanılarak coğrafi çoğaltma desteklenmez. VNET eşleme kısıtlamaları hakkında daha fazla bilgi için bkz. [sanal ağ eşleme-gereksinimler ve kısıtlamalar](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering#requirements-and-constraints). Önerilen çözüm VPN Gateway VNET 'ten VNET 'e bağlantı kullanmaktır.

[Bu Azure şablonunu](https://azure.microsoft.com/resources/templates/201-redis-vnet-geo-replication/)kullanarak, VPN Gateway VNET-VNET bağlantısıyla bağlı bir sanal ağa hızla iki coğrafi çoğaltılan önbellek dağıtabilirsiniz.

### <a name="what-is-the-replication-schedule-for-redis-geo-replication"></a>Redsıs coğrafi çoğaltma için çoğaltma zamanlaması nedir?

Çoğaltma sürekli ve zaman uyumsuzdur ve belirli bir zamanlamaya göre gerçekleşmez. Birincil öğesine yapılan tüm yazma işlemleri, ikincil üzerinde anında ve zaman uyumsuz olarak çoğaltılır.

### <a name="how-long-does-geo-replication-replication-take"></a>Coğrafi çoğaltma çoğaltması ne kadar sürer?

Çoğaltma artımlı, zaman uyumsuz ve sürekli ve bölge genelindeki gecikme süresinden çok farklı değildir. Belirli koşullarda, birincil önbellekten verilerin tam olarak eşitlenmesini sağlamak için ikincil önbelleğin kullanılması gerekebilir. Bu durumda çoğaltma süresi; birincil önbellekte yükleme, kullanılabilir ağ bant genişliği ve bölgeler arası gecikme gibi faktörlerin sayısına bağlıdır. Tam 53 GB 'lık coğrafi olarak çoğaltılan çift için çoğaltma süresi bulduk ve 5 ila 10 dakika arasında bir değer olabilir.

### <a name="is-the-replication-recovery-point-guaranteed"></a>Çoğaltma kurtarma noktası garanti edilir mi?

Coğrafi olarak çoğaltılan bir moddaki önbellekler için Kalıcılık devre dışıdır. Coğrafi olarak çoğaltılan bir çiftin bir müşteri tarafından başlatılan yük devretme gibi bağlantısı kesildiğinde, ikincil bağlantılı önbellek eşitlenmiş verileri o zaman noktasına kadar korur. Böyle durumlarda herhangi bir kurtarma noktası garanti edilmez.

Kurtarma noktası almak için, herhangi bir önbellekten [dışarı aktarın](cache-how-to-import-export-data.md#export) . Daha sonra birincil bağlantılı önbelleğe [Içeri aktarabilirsiniz](cache-how-to-import-export-data.md#import) .

### <a name="can-i-use-powershell-or-azure-cli-to-manage-geo-replication"></a>Coğrafi çoğaltmayı yönetmek için PowerShell veya Azure CLı kullanabilir miyim?

Evet, coğrafi çoğaltma Azure portal, PowerShell veya Azure CLı kullanılarak yönetilebilir. Daha fazla bilgi için bkz. [PowerShell belgeleri](https://docs.microsoft.com/powershell/module/az.rediscache/?view=azps-1.4.0#redis_cache) veya [Azure CLI belgeleri](https://docs.microsoft.com/cli/azure/redis/server-link?view=azure-cli-latest).

### <a name="how-much-does-it-cost-to-replicate-my-data-across-azure-regions"></a>Verileri Azure bölgeleri arasında çoğaltma maliyeti ne kadar sürer?

Coğrafi çoğaltma kullanılırken, birincil bağlantılı önbellekteki veriler ikincil bağlantılı önbelleğe çoğaltılır. İki bağlı önbellek aynı bölgedeyse veri aktarımı ücretsizdir. İki bağlı önbellek farklı bölgelerde ise, veri aktarımı ücreti her iki bölge arasında taşınan verilerin ağ çıkış maliyetidir. Daha fazla bilgi için bkz. [bant genişliği fiyatlandırma ayrıntıları](https://azure.microsoft.com/pricing/details/bandwidth/).

### <a name="why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache"></a>Bağlı önbelleğinizi silmeye çalıştığımda işlem başarısız oldu mu?

Coğrafi çoğaltma bağlantısını kaldırana kadar, coğrafi olarak çoğaltılan önbellekler ve bunların kaynak grupları bağlantı sırasında silinemez. Bağlı önbelleklerinin birini veya her ikisini içeren kaynak grubunu silmeye çalışırsanız, kaynak grubundaki diğer kaynaklar silinir, ancak kaynak grubu `deleting` durumunda kalır ve kaynak grubundaki tüm bağlantılı önbellekler `running` durumunda kalır. Kaynak grubunu ve içindeki bağlı önbellekleri tamamen silmek için, [coğrafi çoğaltma bağlantısını kaldırma](#remove-a-geo-replication-link)başlığı altında açıklandığı gibi önbellekler bağlantısını kaldırın.

### <a name="what-region-should-i-use-for-my-secondary-linked-cache"></a>İkincil bağlantılı önbelleğim için hangi bölgeyi kullanmalıyım?

Genellikle önbelleğinizin, kendisine erişen uygulamayla aynı Azure bölgesinde bulunması önerilir. Ayrı birincil ve geri dönüş bölgeleri olan uygulamalarda, birincil ve ikincil önbellekler aynı bölgelerde olmalıdır. Eşleştirilmiş bölgeler hakkında daha fazla bilgi için bkz. [En Iyi uygulamalar – Azure eşleştirilmiş bölgeleri](../best-practices-availability-paired-regions.md).

### <a name="how-does-failing-over-to-the-secondary-linked-cache-work"></a>İkincil bağlantılı önbelleğin yük devri nasıl çalışır?

Azure bölgelerinde otomatik yük devretme, coğrafi çoğaltılan önbelleklerde desteklenmez. Olağanüstü durum kurtarma senaryosunda, müşteriler tüm uygulama yığınını yedekleme bölgelerinde koordine edilmiş bir şekilde getirmelidir. Tek tek uygulama bileşenlerinin kendi yedeklemelerine ne zaman geçileceğine izin vermek performansı olumsuz etkileyebilir. Redin 'ın önemli avantajlarından biri, çok düşük gecikme süreli bir depodır. Müşterinin ana uygulaması önbelleğinden farklı bir bölgedeyse, eklenen gidiş dönüş süresi performans üzerinde belirgin bir etkiye sahip olacaktır. Bu nedenle, geçici kullanılabilirlik sorunları nedeniyle otomatik olarak yük devretmemeye özen gösterin.

Müşteri tarafından başlatılan bir yük devretmeyi başlatmak için öncelikle önbellekler bağlantısını kaldırın. Ardından, Redsıs istemcinizi (eski adıyla bağlantılı) ikincil önbelleğin bağlantı uç noktasını kullanacak şekilde değiştirin. İki önbelleğin bağlantısı kesildiğinde, ikincil önbellek normal bir okuma-yazma önbelleği yeniden oluşturur ve istekleri doğrudan Redsıs istemcilerinden kabul eder.

## <a name="next-steps"></a>Sonraki adımlar

[Redsıs Premium katmanı Için Azure önbelleği](cache-premium-tier-intro.md)hakkında daha fazla bilgi edinin.
