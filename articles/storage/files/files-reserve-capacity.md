---
title: Ayrılmış kapasiteye sahip Azure dosyaları için maliyetleri iyileştirin
titleSuffix: Azure Files
description: Azure dosyaları ayrılmış kapasitesini kullanarak Azure dosya paylaşma dağıtımlarında maliyetleri nasıl kaydedeceğinizi öğrenin.
services: storage
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 03/23/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 918320cdb24442e551249e4e67d65e4ba85846c8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105027662"
---
# <a name="optimize-costs-for-azure-files-with-reserved-capacity"></a>Ayrılmış kapasiteye sahip Azure dosyaları için maliyetleri iyileştirin
Kapasite rezervasyonları olan Azure dosya paylaşımları için depolama maliyetlerine para tasarrufu sağlayabilirsiniz. Azure dosyaları ayrılmış kapasitesi, bir yıllık veya üç yıl için bir ayırmaya çalıştığınızda depolama maliyetlerine yönelik bir indirim sağlar. Ayırma, ayırma dönemi için sabit miktarda depolama kapasitesi sağlar.

Azure dosyaları ayrılmış kapasitesi, verileri Azure dosya paylaşımlarınızdan depolamaya yönelik kapasite maliyetlerinizi önemli ölçüde azaltabilir. Ne kadar tasarruf etmeniz, rezervasyonunuzun süresine, ayırmak için seçtiğiniz toplam kapasiteye ve Azure dosya paylaşımlarınız için seçtiğiniz katman ve artıklık ayarlarına bağlıdır. Ayrılmış kapasite bir faturalandırma indirimi sağlar ve Azure dosya paylaşımlarınızın durumunu etkilemez.

Azure dosyaları için rezervasyon kapasitesi hakkında fiyatlandırma bilgileri için bkz. [Azure dosyaları fiyatlandırması](https://azure.microsoft.com/pricing/details/storage/files/).

## <a name="reservation-terms-for-azure-files"></a>Azure dosyaları için rezervasyon koşulları
Aşağıdaki bölümlerde bir Azure dosya kapasitesi ayırma koşulları açıklanır.

### <a name="reservation-capacity"></a>Rezervasyon kapasitesi
Azure dosyaları ayrılmış kapasitesini, bir yıllık veya üç yıllık dönem için aylık 10 TiB ve 100 TiB birimlerinde satın alabilirsiniz.

### <a name="reservation-scope"></a>Ayırma kapsamı
Azure dosyaları ayrılmış kapasitesi, tek bir abonelik veya birden çok abonelik (paylaşılan kapsam) için kullanılabilir. Tek bir aboneliğe kapsama eklendiğinde, rezervasyon iskontosu yalnızca seçili aboneliğe uygulanır. Birden çok aboneliğe kapsama eklendiğinde, rezervasyon indirimi müşterinin faturalandırma bağlamındaki bu abonelikler arasında paylaşılır. Bir ayırma, satın alınan kapsamda kullanımınıza uygulanır ve abonelik içindeki belirli bir depolama hesabı, kapsayıcı veya nesneyle sınırlandırılamıyor.

Azure dosyaları için kapasite ayırması yalnızca bir abonelikte veya paylaşılan kaynak grubunda depolanan veri miktarını içerir. İşlem, bant genişliği ve veri aktarımı ücretleri ayırmaya dahil edilmez. Bir rezervasyon satın alarak, rezervasyon öznitelikleriyle eşleşen kapasite ücretleri, Kullandıkça Öde ücretleri yerine indirim ücretlerine göre ücretlendirilir. Azure ayırmaları hakkında daha fazla bilgi için bkz. [Azure ayırmaları nelerdir?](../../cost-management-billing/reservations/save-compute-costs-reservations.md).

### <a name="supported-tiers-and-redundancy-options"></a>Desteklenen katmanlar ve artıklık seçenekleri
Azure dosyaları ayrılmış kapasitesi yalnızca genel amaçlı sürüm 2 depolama hesaplarında dağıtılan standart dosya paylaşımları için kullanılabilir (GPv2). Ayrılmış kapasite, Premium veya işlem için iyileştirilmiş katmanlardaki Azure dosya paylaşımları için kullanılamaz.

Şu anda yalnızca sık erişimli ve Seyrek Erişimli katmanlardaki Azure dosya paylaşımları rezervasyonları destekler. Tüm depolama artıklıkları ayırmaları için destek. Artıklık seçenekleri hakkında daha fazla bilgi için bkz. [Azure dosyaları yedekliği](storage-files-planning.md#redundancy).

### <a name="security-requirements-for-purchase"></a>Satın alma için güvenlik gereksinimleri
Ayrılmış kapasiteyi satın almak için:

- Kullandıkça Öde tarifesine sahip en az bir kuruluş veya ayrı bir abonelik için **sahip** rolünde olmanız gerekir.
- Kurumsal abonelikler için, **ayrılmış örneklerin EKLENMESI** EA portalında etkinleştirilmelidir. Ya da bu ayar devre dışı bırakılırsa, abonelikte bir EA yöneticisi olmanız gerekir.
- Bulut çözümü sağlayıcısı (CSP) programı için yalnızca yönetici aracıları veya satış aracıları, Azure dosyaları ayrılmış kapasitesini satın alabilir.

## <a name="determine-required-capacity-before-purchase"></a>Satın almadan önce gerekli kapasiteyi belirleme
Bir Azure dosyaları ayırması satın aldığınızda, ayırma için bölge, katman ve artıklık seçeneğini seçmeniz gerekir. Ayırma işlemi yalnızca bu bölgede, katmanda ve artıklık düzeyinde depolanan veriler için geçerlidir. Örneğin, bölgesel olarak yedekli depolama (ZRS) kullanarak sık erişimli katman için ABD Batı verileri için bir rezervasyon satın aldığınızı varsayalım. Bu rezervasyon ABD Doğu, seyrek erişimli katmandaki veriler veya coğrafi olarak yedekli depolama (GRS) verileri için geçerlidir. Ancak, ek gereksinimleriniz için başka bir rezervasyon satın alabilirsiniz.  

Ayırmalar, 100 TiB blokları için daha yüksek indirimlerle 10 TiB veya 100 TiB bloklarında kullanılabilir. Azure portal bir ayırma satın aldığınızda, Microsoft size satın almanız gereken ayırmayı belirlemede yardımcı olması için size önceki kullanımınıza göre öneriler verebilir.

## <a name="purchase-azure-files-reserved-capacity"></a>Azure dosyaları ayrılmış kapasitesini satın alma
Azure dosyaları ayrılmış kapasitesini [Azure Portal](https://portal.azure.com)aracılığıyla satın alabilirsiniz. Peşin olarak veya aylık ödemelerle rezervasyon ödemesi yapın. Aylık ödemeler ile satın alma hakkında daha fazla bilgi için bkz. [ön veya aylık ödemeler Ile Azure ayırmaları satın alma](../../cost-management-billing/reservations/prepare-buy-reservation.md).

Senaryonuz için uygun olan rezervasyon koşullarını tanımlamaya yardımcı olması için bkz. [Azure Storage ayrılmış kapasite ıskontosunu anlama](../../cost-management-billing/reservations/understand-storage-charges.md).

Ayrılmış kapasiteyi satın almak için aşağıdaki adımları izleyin:

1. Azure portal [satın alma rezervasyonları](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/Browse_AddCommand) dikey penceresine gidin.  
1. Yeni bir ayırma satın almak için **Azure dosyaları** ' nı seçin.  
1. Gerekli alanları aşağıdaki tabloda açıklandığı gibi girin:

    ![Ayrılmış kapasitenin nasıl satın alınacağını gösteren ekran görüntüsü](./media/files-reserve-capacity/select-reserved-capacity.png)

   |Alan  |Açıklama  |
   |---------|---------|
   |**Kapsam**   |  Rezervasyonla ilişkili faturalandırma avantajını kaç tane aboneliğin kullanabileceğinizi gösterir. Ayrıca, rezervasyonun belirli aboneliklere nasıl uygulanacağını de denetler. <br/><br/> **Paylaşılan**' i seçerseniz, rezervasyon indirimi, faturalandırma bağlamınızın içindeki herhangi bir abonelikte Azure dosyaları kapasitesine uygulanır. Faturalandırma bağlamı, Azure 'a kaydolmanızı temel alır. Kurumsal müşteriler için, paylaşılan kapsam kayıt içindedir ve kayıt dahilindeki tüm abonelikleri içerir. Kullandıkça Öde müşterileri için, paylaşılan kapsam, hesap yöneticisi tarafından oluşturulan Kullandıkça Öde tarifesine sahip tüm bireysel abonelikleri içerir.  <br/><br/>  **Tek abonelik**' ı seçerseniz, rezervasyon indirimi seçili abonelikteki Azure dosyaları kapasitesine uygulanır. <br/><br/> **Tek kaynak grubu**' nu seçerseniz, rezervasyon indirimi seçili abonelikteki Azure dosyaları kapasitesine ve bu abonelikte seçilen kaynak grubuna uygulanır. <br/><br/> Ayırmayı satın aldıktan sonra rezervasyon kapsamını değiştirebilirsiniz.  |
   |**Abonelik**  | Azure dosyaları ayırması için ödeme yapmak üzere kullanılan abonelik. Seçili abonelikteki ödeme yöntemi, maliyetleri borçlandırmak üzere kullanılır. Abonelik aşağıdaki türlerden biri olmalıdır: <br/><br/>  Kurumsal Anlaşma (teklif numaraları: MS-AZR-0017P veya MS-AZR-0148P): kurumsal bir abonelik Için ücretler, kaydın Azure ön ödemesinin (daha önce parasal taahhüt olarak adlandırılır) bakiyesinden düşülür veya fazla kullanım olarak ücretlendirilir. <br/><br/> Kullandıkça Öde tarifesine sahip tek tek abonelik (teklif numaraları: MS-AZR-0003P veya MS-AZR-0023P): Kullandıkça Öde tarifesine sahip bireysel bir abonelik Için ücretler, abonelik üzerindeki kredi kartına veya fatura ödeme yöntemine faturalandırılır.    |
   | **Bölge** | Ayırmanın etkin olduğu bölge. |
   | **Katman** | Ayırmanın etkin olduğu katman. Seçenekler *sık* *ve seyrek* erişimli. |
   | **Yedeklilik** | Ayırma için artıklık seçeneği. Seçenekler *LRS*, *ZRS*, *GRS* ve *GZRS* içerir. Artıklık seçenekleri hakkında daha fazla bilgi için bkz. [Azure dosyaları yedekliği](storage-files-planning.md#redundancy). |
   | **Faturalandırma sıklığı** | Hesabın rezervasyon için ne sıklıkta faturalandırıldığını gösterir. Seçenekler *ayda* veya *önünde* bulunur. |
   | **Boyut** | Ayrılacak kapasite miktarı. |
   |**Süre**  | Bir yıl veya üç yıl.   |

1. Rezervasyonunuzun parametrelerini seçtikten sonra Azure portal maliyeti görüntüler. Portal Ayrıca Kullandıkça Öde faturalandırmasına göre indirim yüzdesini gösterir.

1. **Satın alma rezervasyonları** dikey penceresinde, rezervasyonun toplam maliyetini gözden geçirin. Ayırma için de bir ad sağlayabilirsiniz.

Bir ayırma satın aldıktan sonra, rezervasyon koşullarına uyan mevcut Azure dosya paylaşımlarına otomatik olarak uygulanır. Henüz hiç Azure dosya paylaşımı oluşturmadıysanız, rezervasyon koşullarına uyan her kaynak oluşturduğunuzda rezervasyon uygulanır. Her iki durumda da, rezervasyon dönemi başarılı bir satın alma işleminden hemen sonra başlar.

## <a name="exchange-or-refund-a-reservation"></a>Bir ayırmayı Exchange veya para iadesi
Belirli sınırlamalara sahip bir ayırmayı Exchange veya para iadesi yapabilirsiniz. Bu sınırlamalar aşağıdaki bölümlerde açıklanmıştır.

Bir ayırmayı Exchange veya para iadesi için Azure portal rezervasyon ayrıntılarına gidin. **Exchange** veya **para iadesi**' ni seçin ve destek isteği göndermek için yönergeleri izleyin. İstek işlendiğinde, Microsoft isteğin tamamlandığını onaylamak için size bir e-posta gönderir.

Azure ayırmaları ilkeleri hakkında daha fazla bilgi için bkz. [Azure ayırmaları Için self servis değişimlerinin ve para iadesi](../../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

### <a name="exchange-a-reservation"></a>Bir ayırma Exchange
Ayırma değişimi, rezervasyonun kullanılmayan kısmına göre eşit olarak dağıtılmış bir para iadesi almanızı sağlar. Daha sonra para iadesini yeni bir Azure dosyaları rezervasyonunun satın alma fiyatına uygulayabilirsiniz.

Yapabileceğiniz değişim sayısı için bir sınır yoktur. Ayrıca, bir Exchange ile ilişkili ücret alınmaz. Satın aldığınız yeni rezervasyon, orijinal rezervasyondan eşit veya daha büyük bir değere eşit veya daha büyük olmalıdır. Azure dosyaları ayırması, diğer Azure hizmetleri için bir ayırma için değil, yalnızca başka bir Azure dosya ayırması için değiş tokuş edilebilir.

### <a name="refund-a-reservation"></a>Ayırmayı geri ödeme
Azure dosya ayırmayı dilediğiniz zaman iptal edebilirsiniz. İptal ettiğinizde, rezervasyonun kalan terimine göre eşit olarak dağıtılmış bir para iadesi elde edersiniz ve yüzde 12 erken sonlandırma ücreti alınır. Yıl başına maksimum para iadesi $50.000 ' dir.

Bir ayırmanın iptal edilmesi hemen ayırmayı sonlandırır ve kalan ayları Microsoft 'a geri döndürür. Kalan eşit oranda dağıtılmış Bakiye, eksi ücret, orijinal satın alma formunuza iade edilir.

## <a name="expiration-of-a-reservation"></a>Bir ayırmanın süre sonu
Bir rezervasyon süresi dolmuşsa, bu rezervasyon altında kullandığınız tüm Azure dosyaları kapasitesi Kullandıkça Öde fiyatı üzerinden faturalandırılır. Rezervasyonlar otomatik olarak yenilenmez.

Ayırmanın süresinin dolması için 30 gün önce ve sona erme tarihi üzerinden bir e-posta bildirimi alacaksınız. Bir rezervasyonun sağladığı maliyet tasarruflarından yararlanmaya devam etmek için süreyi, sona erme tarihinden sonra değil, yenileyin.

## <a name="need-help-contact-us"></a>Yardıma mı ihtiyacınız var? Bizimle iletişim kurun
Sorularınız varsa ya da yardıma gereksinim duyuyorsanız [destek isteği oluşturun](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Sonraki adımlar
- [Azure Ayrılmış Sanal Makine Örnekleri nedir?](../../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Azure depolama hizmetlerine rezervasyon iskontolarının nasıl uygulandığını anlayın](../../cost-management-billing/reservations/understand-storage-charges.md)