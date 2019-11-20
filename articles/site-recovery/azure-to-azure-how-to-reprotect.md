---
title: Azure VM 'lerini Azure Site Recovery ile birincil bölgeye yeniden koruma | Microsoft Docs
description: Azure VM 'lerinin yük devretme sonrasında, ikincil ve birincil bölge olan Azure Site Recovery kullanarak nasıl yeniden korunacağını açıklar.
services: site-recovery
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: rajanaki
ms.openlocfilehash: 955e1b84f897a5eb877033e0a58b8d661f143a14
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73954180"
---
# <a name="reprotect-failed-over-azure-vms-to-the-primary-region"></a>Azure VM 'lerinden birincil bölgeye yeniden koruma başarısız oldu


Azure VM 'lerinden [Azure Site Recovery](site-recovery-overview.md)kullanarak bir bölgeden diğerine [Yük devretmek](site-recovery-failover.md) için VM 'ler, korunmayan bir durumda ikincil bölgede önyüklenir. VM 'Leri birincil bölgeye geri devretmek için aşağıdakileri yapmanız gerekir:

- İkincil bölgedeki VM 'Leri, birincil bölgeye çoğaltılmaya başlayacak şekilde yeniden koruyun.
- Yeniden koruma tamamlandıktan ve VM 'Ler çoğaltıldıktan sonra, ikincil sunucudan birincil bölgeye yük devredebilirler.

## <a name="prerequisites"></a>Önkoşullar
1. Birincil sunucudan ikincil bölgeye sanal makine yük devretmesi uygulanmalıdır.
2. Birincil hedef site kullanılabilir olmalıdır ve bu bölgedeki kaynaklara erişebiliyor veya kaynak oluşturmanız gerekir.

## <a name="reprotect-a-vm"></a>VM 'yi yeniden koruma

1. **Kasa** > **çoğaltılan öğeler**' de, yük devredilen VM 'ye sağ tıklayın ve **yeniden koru**' yı seçin. Yeniden koruma yönü ikincilden birinciye göstermelidir.

   ![Yeniden koruma](./media/site-recovery-how-to-reprotect-azure-to-azure/reprotect.png)

2. Kaynak grubu, ağ, depolama ve kullanılabilirlik kümelerini gözden geçirin. Daha sonra, **Tamam**'a tıklayın. Yeni olarak işaretlenmiş kaynaklar varsa, bunlar yeniden koruma sürecinin bir parçası olarak oluşturulur.
3. Yeniden koruma işi hedef siteye en son verileri ekler. Bu işlemi tamamladıktan sonra değişim çoğaltması gerçekleşir. Ardından, birincil siteye yük devretmek için geri dönebilirsiniz. Özelleştirme seçeneğini kullanarak, yeniden koruma sırasında kullanmak istediğiniz depolama hesabını veya ağı seçebilirsiniz.

   ![Özelleştirme seçeneği](./media/site-recovery-how-to-reprotect-azure-to-azure/customize.png)

### <a name="customize-reprotect-settings"></a>Yeniden koruma ayarlarını özelleştirme

Yeniden koruma sırasında hedef VNET 'in aşağıdaki özelliklerini özelleştirebilirsiniz.

![Özelleştir](./media/site-recovery-how-to-reprotect-azure-to-azure/customizeblade.png)

|Özellik |Notlar  |
|---------|---------|
|Hedef kaynak grubu     | VM 'nin oluşturulduğu hedef kaynak grubunu değiştirin. Yeniden korumanın parçası olarak, hedef sanal makine silinir. Yük devretmeden sonra VM oluşturmak için yeni bir kaynak grubu seçebilirsiniz.        |
|Hedef sanal ağ     | Hedef ağ, yeniden koruma işi sırasında değiştirilemez. Ağı değiştirmek için Ağ eşlemesini yeniden yapın.         |
|Hedef depolama (Ikincil VM yönetilen diskleri kullanmıyor)     | Yük devretmeden sonra VM 'nin kullandığı depolama hesabını değiştirebilirsiniz.         |
|Yönetilen çoğaltma diskleri (Ikincil VM yönetilen diskleri kullanır)    | Site Recovery, birincil bölgede, ikincil VM 'nin yönetilen disklerini yansıtmak için çoğaltma yönetilen diskler oluşturur.         |
|Önbellek depolama     | Çoğaltma sırasında kullanılacak bir önbellek depolama hesabı belirtebilirsiniz. Varsayılan olarak, mevcut değilse yeni bir önbellek depolama hesabı oluşturulur.         |
|Kullanılabilirlik Kümesi     |İkincil bölgedeki VM bir kullanılabilirlik kümesinin parçasıysa, birincil bölgedeki hedef VM için bir kullanılabilirlik kümesi seçebilirsiniz. Varsayılan olarak, Site Recovery mevcut kullanılabilirlik kümesini birincil bölgede bulmaya çalışır ve onu kullanır. Özelleştirme sırasında yeni bir kullanılabilirlik kümesi belirtebilirsiniz.         |


### <a name="what-happens-during-reprotection"></a>Yeniden koruma sırasında ne olur?

Varsayılan olarak aşağıdakiler gerçekleşir:

1. Yük devredilen VM 'nin çalıştığı bölgede bir önbellek depolama hesabı oluşturulur.
2. Hedef depolama hesabı (birincil bölgedeki orijinal depolama hesabı) yoksa, yeni bir tane oluşturulur. Atanan depolama hesabı adı, ikincil VM tarafından kullanılan depolama hesabının adıdır ve "ASR" ile sonlenir.
3. VM 'niz yönetilen diskler kullanıyorsa, ikincil VM 'nin disklerinden çoğaltılan verileri depolamak için birincil bölgede çoğaltma yönetilen diskler oluşturulur.
4. Hedef kullanılabilirlik kümesi yoksa, gerekirse yeniden koruma işinin bir parçası olarak yeni bir tane oluşturulur. Yeniden koruma ayarlarını özelleştirdiyseniz, seçili küme kullanılır.

Yeniden koruma işi tetikliyorsanız ve hedef VM varsa, aşağıdakiler gerçekleşir:

1. Çalışıyorsa, hedef tarafı VM kapalıdır.
2. VM yönetilen diskleri kullanıyorsa, özgün disklerin bir kopyası '-ASRReplica ' soneki ile oluşturulur. Özgün diskler silinir. '-ASRReplica ' kopyaları çoğaltma için kullanılır.
3. VM, yönetilmeyen diskler kullanıyorsa, hedef sanal makinenin veri diskleri ayrılır ve çoğaltma için kullanılır. İşletim sistemi diskinin bir kopyası oluşturulur ve VM 'ye eklenir. Özgün işletim sistemi diski ayrılır ve çoğaltma için kullanılır.
4. Yalnızca kaynak disk ve hedef disk arasındaki değişiklikler eşitlenir. Farklılıklar, her iki diski karşılaştırarak ve sonra aktarılarak hesaplanır. Tahmini zaman denetimini bulmak için.
5. Eşitleme tamamlandıktan sonra, Delta çoğaltma başlar ve çoğaltma ilkesiyle birlikte bir kurtarma noktası oluşturur.

Yeniden koruma işi tetikliyorsanız ve hedef VM ve diskler yoksa, şunlar olur:
1. VM yönetilen diskleri kullanıyorsa, çoğaltma diskleri '-ASRReplica ' soneki ile oluşturulur. '-ASRReplica ' kopyaları çoğaltma için kullanılır.
2. VM, yönetilmeyen diskler kullanıyorsa, çoğaltma diskleri hedef depolama hesabında oluşturulur.
3. Tüm diskler, yük devredilen bölgeden yeni hedef bölgeye kopyalanır.
4. Eşitleme tamamlandıktan sonra, Delta çoğaltma başlar ve çoğaltma ilkesiyle birlikte bir kurtarma noktası oluşturur.

#### <a name="estimated-time--to-do-the-reprotection"></a>Yeniden koruma için tahmini süre 

Çoğu durumda Azure Site Recovery, tüm verileri kaynak bölgeye çoğaltmaz. Aşağıda, ne kadar veri çoğaltılacağı belirleyen koşullar verilmiştir:

1.  Kaynak VM verileri silinirse, kaynak grubu değiştirme/silme gibi bir nedenden dolayı bir süre sonra yeniden koruma sırasında, kullanılacak kaynak bölgede hiç veri bulunmadığından, yeniden koruma sırasında tamamlanmış IR olur.
2.  Kaynak VM verileri erişilebilir ise, yalnızca farklılıklar, diskler karşılaştırılarak ve sonra aktarıldıktan sonra hesaplanır. Tahmini süreyi almak için aşağıdaki tabloyu denetleyin 

|\* * Örnek durum * * | \* * Yeniden korumak için geçen süre * * |
|--- | --- |
|Kaynak bölgesinin 1 TB standart diski olan 1 VM 'si vardır<br/>-Yalnızca 127 GB veri kullanılıyor ve diskin geri kalanı boş<br/>-Disk türü 60 MIB/sn aktarım hızı ile standart<br/>-Yük devretmeden sonra veri değişikliği yok| Yaklaşık 45 dakika – 1,5 saat<br/> -Yeniden koruma sırasında Site Recovery, tüm verilerin sağlama toplamını dolduracaktır ve bu süre 127 GB/45 MB ~ 45 dakika sürer<br/>-Site Recovery 20-30 dakika olan otomatik ölçeklendirme yapmak için bazı ek yük süresi gerekir<br/>-Çıkış ücreti yok |
|Kaynak bölgesinin 1 TB standart diski olan 1 VM 'si vardır<br/>-Yalnızca 127 GB veri kullanılıyor ve diskin geri kalanı boş<br/>-Disk türü 60 MIB/sn aktarım hızı ile standart<br/>-Yük devretmeden sonra 45 GB veri değişikliği| Yaklaşık 1 saat – 2 saat<br/>-Yeniden koruma sırasında Site Recovery, tüm verilerin sağlama toplamını dolduracaktır ve bu süre 127 GB/45 MB ~ 45 dakika sürer<br/>-Aktarım süresi 45 GB/45 MBps ~ 17 dakika olan 45 GB değişiklik uygular<br/>-Çıkış ücretleri yalnızca, sağlama toplamı için değil 45 GB veri için olacaktır|
 



## <a name="next-steps"></a>Sonraki adımlar

VM korunduktan sonra bir yük devretme işlemi başlatabilirsiniz. Yük devretme, İkincil bölgedeki sanal makineyi kapatır ve bazı küçük kapalı kalma süresiyle birincil bölgede VM 'ler oluşturur ve önyükleme yapar. Birincil siteye tam yük devretme başlatmadan önce bir zaman ve yük devretme testi çalıştırmanızı öneririz. Yük devretme hakkında [daha fazla bilgi edinin](site-recovery-failover.md) .
