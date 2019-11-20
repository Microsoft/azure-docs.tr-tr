---
title: Ayrılmış kapasiteye sahip blob depolaması maliyetlerini iyileştirin-Azure depolama
description: Blok Blobu ve Azure Data Lake Storage 2. kaynakları üzerinde maliyetleri kaydetmek için Azure Depolama ayrılmış kapasitesi satın alma hakkında bilgi edinin.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: cb21291d4beb9fbba27a56089f13bd0363604eab
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73686709"
---
# <a name="optimize-costs-for-blob-storage-with-reserved-capacity"></a>Ayrılmış kapasiteye sahip blob depolaması için maliyetleri iyileştirin

Azure Depolama ayrılmış kapasitesine sahip blob verileri için depolama maliyetlerine para tasarrufu sağlayabilirsiniz. Azure Depolama ayrılmış kapasitesi, bir yıl veya üç yıl için bir ayırmaya kayıt yaptığınızda, blok Blobları için kapasite ve standart depolama hesaplarında Azure Data Lake Storage 2. verileri için bir indirim sağlar. Ayırma, ayırma dönemi için sabit miktarda depolama kapasitesi sağlar.

Azure Depolama ayrılmış kapasitesi, blok Blobları ve Azure Data Lake Storage 2. verileri için kapasite maliyetlerinizi önemli ölçüde azaltabilir. Elde edilen maliyet tasarrufları, rezervasyonunuzun süresine, ayırmak için seçtiğiniz toplam kapasiteye ve depolama hesabınız için seçtiğiniz yedekliliğe ve erişim katmanına ve bu erişimin türüne bağlıdır. Ayrılmış kapasite bir faturalandırma indirimi sağlar ve Azure depolama kaynaklarınızın durumunu etkilemez.

Azure depolama rezervasyonu fiyatlandırması hakkında daha fazla bilgi için bkz. [Blok Blobu fiyatlandırması](https://azure.microsoft.com/pricing/details/storage/blobs/) ve [Azure Data Lake Storage Gen 2 fiyatlandırması](https://azure.microsoft.com/pricing/details/storage/data-lake/).

## <a name="reservation-terms-for-azure-storage"></a>Azure depolama için rezervasyon koşulları

Aşağıdaki bölümlerde bir Azure depolama ayırmasının koşulları açıklanır.

### <a name="reservation-capacity"></a>Rezervasyon kapasitesi

Azure Depolama ayrılmış kapasitesini, bir yıllık veya üç yıllık dönem için aylık 100 TB ve 1 PB birimlerde satın alabilirsiniz.

### <a name="reservation-scope"></a>Ayırma kapsamı

Azure Depolama ayrılmış kapasitesi, tek bir abonelik veya paylaşılan bir kaynak grubu için kullanılabilir. Azure Depolama ayrılmış kapasitesi satın aldığınızda, hem Blok Blobu hem de Azure Data Lake Storage 2. verileri için ayırmanızdan yararlanabilirsiniz. Bir ayırma, satın alınan kapsamda kullanımınıza uygulanır ve abonelik içindeki belirli bir depolama hesabı, kapsayıcı veya nesneyle sınırlandırılamıyor. Bir ayırma birden çok aboneliğe bölünemiyor.

Azure depolama ayırması yalnızca bir abonelikte veya paylaşılan kaynak grubunda depolanan veri miktarını içerir. Erken silme, işlemler, bant genişliği ve veri aktarımı ücretleri ayırmaya dahil edilmez. Bir rezervasyon satın alarak, rezervasyon öznitelikleriyle eşleşen kapasite ücretleri, Kullandıkça Öde tarifesine göre değil, indirimli tarifeler üzerinden ücretlendirilir. Azure ayırmaları hakkında daha fazla bilgi için bkz. [Azure ayırmaları nelerdir?](/azure/billing/billing-save-compute-costs-reservations).

### <a name="supported-account-types-tiers-and-redundancy-options"></a>Desteklenen hesap türleri, Katmanlar ve artıklık seçenekleri

Azure Depolama ayrılmış kapasitesi, genel amaçlı v2 (GPv2) ve BLOB depolama hesapları dahil olmak üzere standart depolama hesaplarındaki kaynaklar için kullanılabilir.

Tüm erişim katmanları (sık erişimli, seyrek erişimli ve arşiv) ayırmalar için desteklenir. Erişim katmanları hakkında daha fazla bilgi için bkz. [Azure Blob depolama: sık erişimli, seyrek erişimli ve arşiv erişim katmanları](storage-blob-storage-tiers.md).

Tüm artıklık türleri ayırmalar için desteklenir. Artıklık seçenekleri hakkında daha fazla bilgi için bkz. [Azure depolama artıklığı](../common/storage-redundancy.md).

> [!NOTE]
> Premium Depolama hesapları, genel amaçlı v1 (GPv1) depolama hesapları, Azure Data Lake Storage 1., sayfa Blobları, Azure kuyruk depolama, Azure Tablo depolama veya Azure dosyaları için Azure Depolama ayrılmış kapasitesi kullanılamaz.  

### <a name="security-requirements-for-purchase"></a>Satın alma için güvenlik gereksinimleri

Ayrılmış kapasiteyi satın almak için:

- Kullandıkça Öde tarifesine sahip en az bir kuruluş veya ayrı bir abonelik için **sahip** rolünde olmanız gerekir.
- Kurumsal abonelikler için, **ayrılmış örneklerin EKLENMESI** EA portalında etkinleştirilmelidir. Ya da bu ayar devre dışı bırakılırsa, abonelikte bir EA yöneticisi olmanız gerekir.
- Bulut çözümü sağlayıcısı (CSP) programı için, yalnızca yönetici aracıları veya satış aracıları Azure Cosmos DB ayrılmış kapasiteyi satın alabilir.

## <a name="determine-required-capacity-before-purchase"></a>Satın almadan önce gerekli kapasiteyi belirleme

Bir Azure depolama ayırması satın aldığınızda, ayırma için bölge, erişim katmanı ve artıklık seçeneğini seçmeniz gerekir. Rezervasyon yalnızca ilgili bölgede, erişim katmanında ve artıklık düzeyinde depolanan veriler için geçerlidir. Örneğin, bölgesel olarak yedekli depolama (ZRS) kullanarak sık erişimli katman için ABD Batı verileri için bir rezervasyon satın aldığınızı varsayalım. ABD Doğu veriler için aynı ayırmayı, arşiv katmanındaki verileri veya coğrafi olarak yedekli depolama (GRS) verilerini kullanamazsınız. Ancak, ek gereksinimleriniz için başka bir rezervasyon satın alabilirsiniz.  

Ayırmalar, 100 TB veya 1 PB blok için bugün, 1 PB blok için daha yüksek indirimlerle kullanılabilir. Azure portal bir ayırma satın aldığınızda, Microsoft size satın almanız gereken ayırmayı belirlemede yardımcı olması için size önceki kullanımınıza göre öneriler verebilir.

## <a name="purchase-azure-storage-reserved-capacity"></a>Azure Depolama ayrılmış kapasitesi satın alma

Azure Depolama ayrılmış kapasitesini [Azure Portal](https://portal.azure.com)aracılığıyla satın alabilirsiniz. Rezervasyon için veya aylık ödemeler için ödeme yapın. Aylık ödemeler ile satın alma hakkında daha fazla bilgi için bkz. [ön veya aylık ödemeler Ile Azure ayırmaları satın alma](/azure/billing/billing-monthly-payments-reservations).

Senaryonuz için uygun olan rezervasyon koşullarını belirlemeye yönelik yardım için bkz. [Azure Storage ayrılmış kapasite ıskontosunu anlama](../../billing/billing-understand-storage-charges.md).

Ayrılmış kapasiteyi satın almak için aşağıdaki adımları izleyin:

1. Azure portal [satın alma rezervasyonları](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/Browse_AddCommand) bölmesine gidin.  
1. Yeni bir ayırma satın almak için **Azure Blob depolama** ' yı seçin.  
1. Gerekli alanları aşağıdaki tabloda açıklandığı gibi girin:

    ![Ayrılmış kapasitenin nasıl satın alınacağını gösteren ekran görüntüsü](media/storage-blob-reserved-capacity/select-reserved-capacity.png)

   |Alan  |Açıklama  |
   |---------|---------|
   |**Kapsam**   |  Rezervasyonla ilişkili faturalandırma avantajını kaç tane aboneliğin kullanabileceğinizi gösterir. Ayrıca, rezervasyonun belirli aboneliklere nasıl uygulanacağını de denetler. <br/><br/> **Paylaşılan**' i seçerseniz, rezervasyon indirimi, faturalandırma bağlamınızın içindeki herhangi bir abonelikte Azure depolama kapasitesine uygulanır. Faturalandırma bağlamı, Azure 'a kaydolmanızı temel alır. Kurumsal müşteriler için, paylaşılan kapsam kayıt içindedir ve kayıt dahilindeki tüm abonelikleri içerir. Kullandıkça Öde müşterileri için, paylaşılan kapsam, hesap yöneticisi tarafından oluşturulan Kullandıkça Öde tarifesine sahip tüm bireysel abonelikleri içerir.  <br/><br/>  **Tek bir abonelik**seçerseniz, rezervasyon indirimi seçili abonelikte Azure depolama kapasitesine uygulanır. <br/><br/> **Tek kaynak grubu**' nu seçerseniz, rezervasyon indirimi seçili abonelikteki Azure depolama kapasitesine ve bu abonelikteki seçili kaynak grubuna uygulanır. <br/><br/> Ayırmayı satın aldıktan sonra rezervasyon kapsamını değiştirebilirsiniz.  |
   |**Abonelik**  | Azure depolama ayırması için ödeme yapmak üzere kullanılan abonelik. Seçili abonelikteki ödeme yöntemi, maliyetleri borçlandırmak üzere kullanılır. Abonelik aşağıdaki türlerden biri olmalıdır: <br/><br/>  Kurumsal Anlaşma (teklif numaraları: MS-AZR-0017P veya MS-AZR-0148P): kurumsal bir abonelik Için ücretler, kayıt parasal taahhüt bakiyesinden düşülür veya fazla kullanım olarak ücretlendirilir. <br/><br/> Kullandıkça Öde tarifesine sahip tek tek abonelik (teklif numaraları: MS-AZR-0003P veya MS-AZR-0023P): Kullandıkça Öde tarifesine sahip bireysel bir abonelik Için ücretler, abonelik üzerindeki kredi kartına veya fatura ödeme yöntemine faturalandırılır.    |
   | **Bölge** | Ayırmanın etkin olduğu bölge. |
   | **Erişim katmanı** | Ayırmanın etkin olduğu erişim katmanı. Seçenekler arasında *sık*erişimli *, seyrek*erişimli veya *Arşiv*bulunur. Erişim katmanları hakkında daha fazla bilgi için bkz. [Azure Blob depolama: sık erişimli, seyrek erişimli ve arşiv erişim katmanları](storage-blob-storage-tiers.md). |
   | **Yedeklilik** | Ayırma için artıklık seçeneği. Seçenekler *LRS*, *ZRS*, *GRS*ve *ra-GZRS*' i içerir. Artıklık seçenekleri hakkında daha fazla bilgi için bkz. [Azure depolama artıklığı](../common/storage-redundancy.md). |
   | **Faturalandırma sıklığı** | Hesabın rezervasyon için ne sıklıkta faturalandırıldığını gösterir. Seçenekler *ayda* veya *önünde*bulunur. |
   | **Boyut** | Ayırmanın etkin olduğu bölge. |
   |**Terimli**  | Bir yıl veya üç yıl.   |

1. Rezervasyonunuzun parametrelerini seçtikten sonra Azure portal maliyeti görüntüler. Portal Ayrıca Kullandıkça Öde faturalandırmasına göre indirim yüzdesini gösterir.

1. **Satın alma rezervasyonları** bölmesinde, rezervasyonun toplam maliyetini gözden geçirin. Ayırma için de bir ad sağlayabilirsiniz.

    ![Ayırma satın almayı gösteren ekran görüntüsü](media/storage-blob-reserved-capacity/purchase-reservations.png)

Bir ayırma satın aldıktan sonra, mevcut Azure Storage blok blob 'una veya rezervasyon koşullarına uyan Azure Data Lake Storage 2. kaynaklara otomatik olarak uygulanır. Henüz hiç Azure Storage kaynağı oluşturmadıysanız, rezervasyon koşullarına uyan bir kaynak oluşturduğunuzda rezervasyon uygulanır. Her iki durumda da, rezervasyon dönemi başarılı bir satın alma işleminden hemen sonra başlar.

## <a name="exchange-or-refund-a-reservation"></a>Bir ayırmayı Exchange veya para iadesi

Belirli sınırlamalara sahip bir ayırmayı Exchange veya para iadesi yapabilirsiniz. Bu sınırlamalar aşağıdaki bölümlerde açıklanmıştır.

Bir ayırmayı Exchange veya para iadesi için Azure portal rezervasyon ayrıntılarına gidin. **Exchange** veya **para iadesi**' ni seçin ve destek isteği göndermek için yönergeleri izleyin. İstek işlendiğinde, Microsoft isteğin tamamlandığını onaylamak için size bir e-posta gönderir.

Azure ayırmaları ilkeleri hakkında daha fazla bilgi için bkz. [Azure ayırmaları Için self servis değişimlerinin ve para iadesi](../../billing/billing-azure-reservations-self-service-exchange-and-refund.md).

### <a name="exchange-a-reservation"></a>Bir ayırma Exchange

Ayırma değişimi, rezervasyonun kullanılmayan kısmına göre eşit olarak dağıtılmış bir para iadesi almanızı sağlar. Daha sonra para iadesini yeni bir Azure depolama ayırmasının satın alma fiyatına uygulayabilirsiniz.

Gerçekleştirebileceğiniz değiştirme işlemlerinin sayısında herhangi bir sınırlama yoktur. Değiştirme işlemi için ek ücret de uygulanmaz. Satın aldığınız yeni rezervasyon, orijinal rezervasyondan eşit veya daha büyük bir değere eşit veya daha büyük olmalıdır. Azure depolama rezervasyonu, diğer Azure hizmetleri için bir ayırma için değil, yalnızca başka bir Azure depolama ayırması için takas edilebilir.

### <a name="refund-a-reservation"></a>Ayırmayı geri ödeme

Bir Azure depolama ayırmayı dilediğiniz zaman iptal edebilirsiniz. İptal ettiğinizde, rezervasyonun kalan terimine göre eşit olarak dağıtılmış bir para iadesi elde edersiniz ve yüzde 12 erken sonlandırma ücreti alınır. Yıl başına maksimum para iadesi $50.000 ' dir.

Bir ayırmanın iptal edilmesi hemen ayırmayı sonlandırır ve kalan ayları Microsoft 'a geri döndürür. Kalan eşit oranda dağıtılmış Bakiye, eksi ücret, orijinal satın alma formunuza iade edilir.

## <a name="expiration-of-a-reservation"></a>Bir ayırmanın süre sonu

Bir rezervasyon süresi dolmuşsa, bu rezervasyon altında kullandığınız tüm Azure depolama kapasitesi, Kullandıkça Öde fiyatı üzerinden faturalandırılır. Rezervasyonlar otomatik olarak yenilenmez.

Ayırmanın süresinin dolması için 30 gün önce ve sona erme tarihi üzerinden bir e-posta bildirimi alacaksınız. Bir rezervasyonun sağladığı maliyet tasarruflarından yararlanmaya devam etmek için süreyi, sona erme tarihinden sonra değil, yenileyin.

## <a name="need-help-contact-us"></a>Yardım mı gerekiyor? Bizimle iletişim kurun

Sorularınız varsa ya da yardıma gereksinim duyuyorsanız [destek isteği oluşturun](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Sonraki adımlar

- [Azure Ayrılmış Sanal Makine Örnekleri nedir?](../../billing/billing-save-compute-costs-reservations.md)
- [Rezervasyon iskontosunun Azure depolama 'ya nasıl uygulanacağını anlayın](../../billing/billing-understand-storage-charges.md)
