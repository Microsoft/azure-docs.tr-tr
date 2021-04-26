---
title: Azure Disk Yedekleme destek matrisi
description: Destek ayarları ve kısıtlamaları için Azure disk yedekleme 'nin bir özetini sağlar.
ms.topic: conceptual
ms.date: 01/07/2021
ms.custom: references_regions
ms.openlocfilehash: 88ec26837cc8f69c1e84c77ea6b57ce16e462e0a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105612866"
---
# <a name="azure-disk-backup-support-matrix"></a>Azure Disk Yedekleme destek matrisi

Azure disklerini korumak için [Azure Backup](./backup-overview.md) kullanabilirsiniz. Bu makalede bölge kullanılabilirliği, desteklenen senaryolar ve sınırlamalar özetlenmektedir.

## <a name="supported-regions"></a>Desteklenen bölgeler

Azure disk yedekleme şu bölgelerde kullanılabilir: Batı ABD, Batı ABD 2, Orta Batı ABD, Doğu ABD, Doğu ABD2, Orta ABD, Orta Güney ABD, Orta Kuzey ABD, Kanada Orta, Brezilya Güney, Güney Afrika Kuzey, UK Güney, UK Batı, Batı Avrupa, Kuzey Avrupa, İsviçre Kuzey, İsviçre Batı, Almanya Orta Batı, Fransa Orta, Norveç Doğu, BAE Kuzey, Avustralya Orta, Avustralya Orta 2 , Avustralya Doğu, Kore Orta, Kore Güney, Japonya Doğu, Japonya Batı, Doğu Asya, Güneydoğu Asya, Orta Hindistan. 

Kullanılabilir olduklarında daha fazla bölge duyurulacaktır.

## <a name="limitations"></a>Sınırlamalar

- Azure disk yedekleme, paylaşılan diskler (paylaşılan Premium SSD 'Ler) dahil olmak üzere Azure yönetilen diskler için desteklenir. Yönetilmeyen diskler desteklenmez. Şu anda bu çözüm, anlık görüntü yeteneği olmadığından paylaşılan Ultra diskler dahil olmak üzere Ultra diskleri desteklemez.

- Azure disk yedekleme Yazma Hızlandırıcısı diskinin yedeklenmesini destekler. Ancak geri yükleme sırasında disk normal bir disk olarak geri yüklenir. Yazma hızlandırılmış önbellek, bir VM 'ye bağlandıktan sonra diskte etkinleştirilebilir.

- Azure Backup, Azure yönetilen disklerin gün başına birden çok yedekleme desteğiyle birlikte işletimsel (snapshot) katman yedeklemesini sağlar. Yedeklemeler yedekleme kasasına kopyalanmaz.

- Şu anda, mevcut kaynak disklerini yedeklerin alındığı yerden değiştirerek geri yükleme için Original-Location kurtarma (OLR) seçeneği desteklenmez. Kurtarma noktasından geri yükleme, yedeklemelerin alındığı kaynak disk ile aynı kaynak grubunda yeni bir disk oluşturmak için ya da başka bir kaynak grubunda geri yükleyebilirsiniz. Bu, Alternate-Location kurtarma (ALR) olarak bilinir.

- Yönetilen diskler için Azure Backup, disk başına 200 anlık görüntüsü ile sınırlı olan Artımlı anlık görüntüleri kullanır. Yedekleme ilkesi, zamanlanan yedeklemelerin yanı sıra isteğe bağlı yedekleme yapmanıza olanak tanımak için toplam yedeklemeleri 180 olarak sınırlandırır. Yönetilen diskler için [Artımlı anlık görüntü](../virtual-machines/disks-incremental-snapshots.md#restrictions) hakkında daha fazla bilgi edinin.

- Azure [aboneliği ve hizmet limitleri](../azure-resource-manager/management/azure-subscription-service-limits.md#virtual-machine-disk-limits) , her abonelik için bölge başına düşen toplam disk anlık görüntüsü sayısı için geçerlidir.

- Bir sanal makineye bağlı birden çok diskin noktadan noktaya anlık görüntüleri desteklenmez.

- Yedekleme Kasası ve yedeklenecek diskler aynı veya farklı aboneliklerde olabilir. Ancak, hem yedekleme Kasası hem de yedeklenecek disk aynı bölgede olmalıdır.

- Yedeklenecek diskler ve anlık görüntülerin yerel olarak depolandığı anlık görüntü kaynak grubu aynı abonelikte olmalıdır.

- Bir diskin yedekten aynı veya farklı bir aboneliğe geri yüklenmesi desteklenir. Ancak, geri yüklenen disk, anlık görüntüyle aynı bölgede oluşturulur.

- ADE şifreli diskler korunamaz.

- Platform tarafından yönetilen anahtarlarla şifrelenen diskler veya müşteri tarafından yönetilen anahtarlar desteklenir. Ancak, disk şifreleme anahtarı devre dışı bırakılmışsa veya silinirse, müşteri tarafından yönetilen anahtarlar (CMK) kullanılarak şifrelenmiş bir diskin geri yükleme noktaları için geri yükleme başarısız olur.

- Şu anda yedekleme ilkesi değiştirilemez ve bir diskin yedeğini yapılandırırken bir yedekleme örneğine atanmış olan anlık görüntü kaynak grubu değiştirilemez.

- Şu anda, disklerin yedeklenmesini yapılandırmak için Azure portal deneyimi aynı abonelikte en fazla 20 diskle sınırlıdır.

- Azure disk yedekleme, PowerShell 'i destekler. Şu anda Azure CLı desteklenmez.

- Yedekleme yapılandırılırken, yedeklenmek üzere seçilen disk ve anlık görüntülerin depolanacağı anlık görüntü kaynak grubu aynı aboneliğin bir parçası olmalıdır. Bu diskin aboneliği dışındaki belirli bir disk için artımlı bir anlık görüntü oluşturamazsınız. Yönetilen disk için [Artımlı anlık görüntüler](../virtual-machines/disks-incremental-snapshots.md#restrictions) hakkında daha fazla bilgi edinin. Anlık görüntü kaynak grubu seçme hakkında daha fazla bilgi için bkz.  [yedeklemeyi yapılandırma](backup-managed-disks.md#configure-backup).

- Başarılı yedekleme ve geri yükleme işlemleri için, yedekleme kasasının yönetilen kimliği için rol atamaları gereklidir. Yalnızca belgelerde verilen rol tanımlarını kullanın. Sahip, katkıda bulunan vb. gibi diğer rollerin kullanılması desteklenmez. Rolleri atadıktan sonra yedekleme veya geri yükleme işlemlerini yapılandırmaya başladıysanız izin sorunları yaşayabilirsiniz. Bunun nedeni, rol atamalarının etkili olması birkaç dakika sürer.

- Yönetilen diskler, dağıtım sırasında veya daha sonra diskin boyutunu değiştirmeden performans katmanını değiştirmeye izin verir. Azure disk yedekleme çözümü, yedeklenmekte olan kaynak diskte performans katmanı değişikliklerini destekler. Geri yükleme sırasında geri yüklenen diskin performans katmanı, yedekleme sırasında kaynak disk ile aynı olacaktır. Geri yükleme işleminden sonra diskinizin performans katmanını değiştirmek için [buradaki](../virtual-machines/disks-performance-tiers-portal.md) belgeleri izleyin.

- Yönetilen diskler için [özel bağlantılar](../virtual-machines/disks-enable-private-links-for-import-export-portal.md) desteği, yönetilen disklerin içeri ve dışarı aktarılmasını yalnızca Azure sanal ağınızda gerçekleşmeyecek şekilde kısıtlamanıza olanak tanır. Azure disk yedekleme, Özel uç noktaları etkin olan disklerin yedeklenmesini destekler. Bu, Özel uç nokta aracılığıyla erişilebilen yedekleme verilerini veya anlık görüntüleri içermez.

- Yedekleme örneğini silebilirsiniz, bu da yedeklemeyi durdurur ve tüm yedekleme verilerini de siler. Şu anda, yedeklemeyi **durdurma ve yedekleme verilerini koruma** seçeneği desteklenmediğinden yedeklemeyi devre dışı bırakamıyoruz.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure yönetilen disklerini yedekleme](backup-managed-disks.md)