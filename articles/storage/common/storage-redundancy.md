---
title: Veri yedekliği
titleSuffix: Azure Storage
description: Azure Storage 'da veri yedekliliği anlayın. Microsoft Azure Depolama hesabınızdaki veriler, dayanıklılık ve yüksek kullanılabilirlik için çoğaltılır.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 04/16/2021
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 3665421ddbdd9cf079ff4aab9377fc9164a1599c
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107575369"
---
# <a name="azure-storage-redundancy"></a>Azure Depolama yedekliliği

Azure depolama, geçici donanım arızaları, ağ veya güç kesintileri ve çok büyük doğal felaketler de dahil olmak üzere planlı ve planlanmamış olaylardan korunabilmesi için verilerinizin birden çok kopyasını her zaman depolar. Artıklık, depolama hesabınızın, başarısızlık durumunda bile kullanılabilirlik ve dayanıklılık hedeflerini karşıladığından emin olmanızı sağlar.

Senaryonuz için en uygun artıklık seçeneğinin hangisi olduğuna karar verirken, düşük maliyetler ve daha yüksek kullanılabilirlik arasındaki avantajları göz önünde bulundurun. Hangi artıklık seçeneğini belirlemenizi gerektiğine yardımcı olan faktörler şunlardır:  

- Verileriniz birincil bölgede nasıl çoğaltılır
- Verilerinizin birincil bölgeye coğrafi olarak uzaktaki ikinci bir bölgeye çoğaltılıp çoğaltılmayacağı, bölgesel olağanüstü durumlara karşı koruma
- Birincil bölge herhangi bir nedenle kullanılamaz duruma gelirse, uygulamanızın ikincil bölgedeki çoğaltılan verilere okuma erişimi gerektirip gerektirmediğini belirtir

## <a name="redundancy-in-the-primary-region"></a>Birincil bölgedeki artıklık

Azure depolama hesabındaki veriler her zaman birincil bölgede üç kez çoğaltılır. Azure Depolama, verilerinizin birincil bölgede çoğaltılmasına yönelik iki seçenek sunar:

- **Yerel olarak yedekli depolama (LRS)** , verilerinizi, birincil bölgedeki tek bir fiziksel konum içinde zaman uyumlu olarak üç kez kopyalar. LRS, en az maliyetli çoğaltma seçeneğidir, ancak yüksek kullanılabilirlik gerektiren uygulamalar için önerilmez.
- Bölgesel olarak **yedekli depolama (ZRS)** , verilerinizi, birincil bölgedeki üç Azure kullanılabilirlik bölgesi üzerinden eşzamanlı olarak kopyalar. Microsoft, yüksek kullanılabilirlik gerektiren uygulamalar için birincil bölgede ZRS kullanımını ve ayrıca bir ikincil bölgeye çoğaltmayı önerir.

> [!NOTE]
> Microsoft, Azure Data Lake Storage 2. iş yükleri için birincil bölgede ZRS kullanılmasını önerir.

### <a name="locally-redundant-storage"></a>Yerel olarak yedekli depolama

Yerel olarak yedekli depolama (LRS), verilerinizi birincil bölgedeki tek bir veri merkezi içinde üç kez çoğaltır. LRS, belirli bir yıl boyunca nesnelerin en az% 99,999999999 (11 nines) dayanıklılığı sağlar.

LRS, en düşük maliyetli artıklık seçeneğidir ve diğer seçeneklere kıyasla en az dayanıklılık sağlar. LRS, verilerinizi sunucu rafı ve sürücü hatalarıyla karşı korur. Ancak, veri merkezinde yangın ya da taşması gibi bir olağanüstü durum oluşursa, LRS kullanan bir depolama hesabının tüm çoğaltmaları kaybolabilir veya kurtarılamaz olabilir. Bu riski azaltmak için Microsoft, bölgesel olarak [yedekli depolama (](#zone-redundant-storage) ZRS), coğrafi olarak [yedekli depolama](#geo-redundant-storage) (GRS) veya [coğrafi bölge-yedekli depolama](#geo-zone-redundant-storage) (GZRS) kullanmanızı önerir.

LRS kullanan bir depolama hesabına yazma isteği eşzamanlı olarak gerçekleştirilir. Yazma işlemi, yalnızca veriler üç kopyaya yazıldıktan sonra başarıyla döndürülür.

Aşağıdaki diyagramda, LRS ile tek bir veri merkezi içinde verilerinizin nasıl çoğaltılacağı gösterilmektedir:

:::image type="content" source="media/storage-redundancy/locally-redundant-storage.png" alt-text="LRS ile tek bir veri merkezinde verilerin nasıl çoğaltılacağı gösteren diyagram":::

LRS aşağıdaki senaryolar için iyi bir seçimdir:

- Uygulamanız veri kaybı oluşursa kolayca yeniden oluşturabileceğiniz verileri depoluyorsa LRS 'yi kabul edebilirsiniz.
- Uygulamanız veri idare gereksinimleri nedeniyle yalnızca bir ülke veya bölge içinde veri çoğaltmaya sınırlı olursa LRS 'yi kabul edebilirsiniz. Bazı durumlarda, verilerin coğrafi olarak çoğaltılacağı eşleştirilmiş bölgeler başka bir ülkede veya bölgede olabilir. Eşleştirilmiş bölgeler hakkında daha fazla bilgi için bkz. [Azure bölgeleri](https://azure.microsoft.com/regions/).

### <a name="zone-redundant-storage"></a>Alanlar arası yedekli depolama

Bölgesel olarak yedekli depolama (ZRS), Azure depolama verilerinizi birincil bölgedeki üç Azure kullanılabilirlik bölgesinde eşzamanlı olarak çoğaltır. Her kullanılabilirlik alanı bağımsız enerji, soğutma ve ağ altyapısına sahip olan ayrı bir fiziksel konumu ifade eder. ZRS, belirli bir yıl boyunca en az% 99,9999999999 (12 9 ' a) Azure depolama veri nesneleri için dayanıklılık sağlar.

ZRS ile, bir bölge kullanılamaz hale gelirse bile verilerinize hem okuma hem de yazma işlemleri için erişilebilir. Bir bölge kullanılamaz duruma gelirse Azure, DNS yeniden işaretleme gibi ağ güncelleştirmelerini alır. Güncelleştirmeler tamamlanmadan önce verilere erişmeniz durumunda bu güncelleştirmeler uygulamanızı etkileyebilir. ZRS için uygulama tasarlarken, üstel geri alma ile yeniden deneme ilkeleri uygulama da dahil olmak üzere geçici hata işleme için uygulamaları izleyin.

ZRS kullanan bir depolama hesabına yazma isteği eşzamanlı olarak gerçekleştirilir. Yazma işlemi, veriler yalnızca üç kullanılabilirlik alanı genelinde tüm yinelemelere yazıldıktan sonra başarıyla döndürülür.

Microsoft, birincil bölgede, tutarlılık, dayanıklılık ve yüksek kullanılabilirlik gerektiren senaryolar için ZRS kullanılmasını önerir. ZRS, veri idare gereksinimlerini karşılamak üzere bir ülke veya bölge dahilinde verilerin çoğaltılmasını kısıtlamak için de önerilir.

Aşağıdaki diyagramda, ZRS ile birincil bölgedeki kullanılabilirlik alanları arasında verilerinizin nasıl çoğaltılacağı gösterilmektedir:

:::image type="content" source="media/storage-redundancy/zone-redundant-storage.png" alt-text="Birincil bölgede ZRS ile verilerin nasıl çoğaltılacağı gösteren diyagram":::

ZRS, geçici olarak kullanılamaz duruma gelirse verileriniz için mükemmel performans, düşük gecikme süresi ve dayanıklılık sağlar. Ancak, ZRS, verileri birden çok bölgenin kalıcı olarak etkilediği bölgesel bir olağanüstü duruma karşı koruyamayabilir. Bölgesel felate karşı koruma için, Microsoft birincil bölgede ZRS kullanan ve ayrıca verilerinizi ikincil bir bölgeye çoğaltan [coğrafi bölge yedekli depolama](#geo-zone-redundant-storage) (GZRS) kullanılmasını önerir.

Aşağıdaki tabloda hangi depolama hesabı türlerinin hangi bölgelerde ZRS destekledikleri gösterilmektedir:

| Depolama hesabı türü | Desteklenen bölgeler | Desteklenen hizmetler |
|--|--|--|
| Genel amaçlı v2<sup>1</sup> | Doğu Güney Afrika Kuzey<br /> (Asya Pasifik) Güneydoğu Asya<br /> (Asya Pasifik) Avustralya Doğu<br /> (Asya Pasifik) Doğu Japonya<br /> 'Da Kanada Orta<br /> 'Ya Kuzey Avrupa<br /> 'Ya Batı Avrupa<br /> 'Ya Fransa Orta<br /> 'Ya Almanya Orta Batı<br /> 'Ya UK Güney<br /> (Güney Amerika) Brezilya Güney<br /> ABD Orta ABD<br /> (ABD) Doğu ABD<br /> ABD Doğu ABD 2<br /> ABD Orta Güney ABD<br /> ABD Batı ABD<br /> ABD Batı ABD 2 | Blok blobları<br /> Sayfa Blobları<sup>2</sup><br /> Dosya paylaşımları (Standart)<br /> Tables<br /> Kuyruklar<br /> |
| BlockBlobStorage<sup>1</sup> | Güneydoğu Asya<br /> Doğu Avustralya<br /> Kuzey Avrupa<br /> Batı Avrupa<br /> Orta Fransa <br /> Doğu Japonya<br /> Güney Birleşik Krallık <br /> ABD Doğu <br /> ABD Doğu 2 <br /> ABD Batı 2| Yalnızca Premium blok Blobları |
| Dosya depolama | Güneydoğu Asya<br /> Doğu Avustralya<br /> Kuzey Avrupa<br /> Batı Avrupa<br /> Orta Fransa <br /> Doğu Japonya<br /> Güney Birleşik Krallık <br /> ABD Doğu <br /> ABD Doğu 2 <br /> ABD Batı 2 | Yalnızca Premium dosya paylaşımları |

<sup>1</sup> arşiv katmanı Şu anda ZRS hesapları için desteklenmiyor.<br />
<sup>2</sup> sanal makineler için Azure yönetilen diskleri içeren depolama hesapları her zaman LRS kullanır. Azure yönetilmeyen diskler de LRS kullanmalıdır. GRS kullanan Azure yönetilmeyen diskler için bir depolama hesabı oluşturmak mümkündür, ancak zaman uyumsuz coğrafi çoğaltma üzerinde tutarlılık nedeniyle olası sorunlar nedeniyle bu önerilmez. Yönetilen veya yönetilmeyen diskler ZRS veya GZRS 'yi desteklemez. Yönetilen diskler hakkında daha fazla bilgi için bkz. [Azure yönetilen diskler fiyatlandırması](https://azure.microsoft.com/pricing/details/managed-disks/).

ZRS 'yi destekleyen bölgeler hakkında daha fazla bilgi için, [Azure kullanılabilirlik alanları nedir?](../../availability-zones/az-overview.md)bölümünde **bölgeye göre hizmetlere göre destek** bölümüne bakın.

## <a name="redundancy-in-a-secondary-region"></a>İkincil bölgede artıklık

Yüksek kullanılabilirlik gerektiren uygulamalar için, Depolama hesabınızdaki verileri, birincil bölgeden yüzlerce mil uzakta olan ikincil bir bölgeye kopyalamayı tercih edebilirsiniz. Depolama Hesabınız ikincil bir bölgeye kopyalanırsa, tüm bölgesel bir kesinti veya birincil bölgenin kurtarılamaz bir olağanüstü durum durumunda bile verileriniz dayanıklı olur.

Bir depolama hesabı oluşturduğunuzda, hesabın birincil bölgesini seçersiniz. Eşleştirilmiş ikincil bölge, birincil bölgeye göre belirlenir ve değiştirilemez. Azure tarafından desteklenen bölgeler hakkında daha fazla bilgi için bkz. [Azure bölgeleri](https://azure.microsoft.com/global-infrastructure/regions/).

Azure depolama, verilerinizi ikincil bir bölgeye kopyalamak için iki seçenek sunar:

- **Coğrafi olarak yedekli depolama (GRS)**, LRS kullanarak verilerinizi birincil bölge içindeki tek bir fiziksel konumda eşzamanlı olarak üç kez kopyalar. Ardından verilerinizi zaman uyumsuz şekilde ikincil bölgedeki tek bir fiziksel konuma kopyalar. İkincil bölge içinde, verileriniz LRS kullanılarak eşzamanlı olarak üç kez kopyalanır.
- **Coğrafi bölge yedekli depolama (GZRS)** , ZRS kullanarak birincil bölgedeki üç Azure kullanılabilirlik bölgesi arasında verilerinizi eşzamanlı olarak kopyalar. Ardından verilerinizi zaman uyumsuz şekilde ikincil bölgedeki tek bir fiziksel konuma kopyalar. İkincil bölge içinde, verileriniz LRS kullanılarak eşzamanlı olarak üç kez kopyalanır.

> [!NOTE]
> GRS ve GZRS arasındaki birincil fark, verilerin birincil bölgede nasıl çoğaltıladır. İkincil bölge içinde, veriler her zaman eş zamanlı olarak LRS kullanılarak çoğaltılır. İkincil bölgedeki LRS, verilerinizi donanım arızalarına karşı korur.

GRS veya GZRS ile ikincil bölgedeki veriler, ikincil bölgede bir yük devretme işlemi olmadıkça okuma veya yazma erişimi için kullanılamaz. İkincil bölgeye okuma erişimi için, depolama hesabınızı Okuma Erişimli Coğrafi olarak yedekli depolama (RA-GRS) veya Okuma Erişimli Coğrafi bölge-yedekli depolama (RA-GZRS) kullanacak şekilde yapılandırın. Daha fazla bilgi için bkz. [İkincil bölgedeki verilere okuma erişimi](#read-access-to-data-in-the-secondary-region).

Birincil bölge kullanılamaz duruma gelirse, ikincil bölgeye yük devretmek için seçim yapabilirsiniz. Yük devretme tamamlandıktan sonra, ikincil bölge birincil bölge olur ve verileri okuyabilir ve yazabilirsiniz. Olağanüstü durum kurtarma hakkında daha fazla bilgi edinmek ve ikincil bölgeye yük devretmeyi öğrenmek için bkz. [olağanüstü durum kurtarma ve depolama hesabı yük devretme](storage-disaster-recovery-guidance.md).

> [!IMPORTANT]
> Veriler ikincil bölgeye zaman uyumsuz olarak çoğaltıldığından, birincil bölgeyi etkileyen bir hata, birincil bölge kurtarılamazsa veri kaybına neden olabilir. Birincil bölgeye en son yazma işlemleri ile ikincil bölgeye son yazma arasındaki Aralık, kurtarma noktası hedefi (RPO) olarak bilinir. RPO, verilerin kurtarılabileceği noktayı gösterir. Azure depolama, genellikle 15 dakikadan daha kısa bir RPO 'ya sahiptir, ancak şu anda verilerin ikincil bölgeye çoğaltılmasının ne kadar sürdüğü konusunda bir SLA yoktur.

### <a name="geo-redundant-storage"></a>Coğrafi olarak yedekli depolama

Coğrafi olarak yedekli depolama (GRS), LRS kullanarak verilerinizi birincil bölge içindeki tek bir fiziksel konumda eşzamanlı olarak üç kez kopyalar. Daha sonra verilerinizi zaman uyumsuz bir şekilde birincil bölgeden yüzlerce mil olan ikincil bölgedeki tek bir fiziksel konuma kopyalar. GRS, belirli bir yıl boyunca en az% 99.99999999999999 (16 9) Azure depolama veri nesneleri için dayanıklılık sağlar.

İlk olarak birincil konuma bir yazma işlemi kaydedilir ve LRS kullanılarak çoğaltılır. Güncelleştirme daha sonra ikincil bölgeye zaman uyumsuz olarak çoğaltılır. Veriler ikincil konuma yazıldığında, LRS kullanarak bu konumda da çoğaltılır.

Aşağıdaki diyagramda, verileriniz GRS veya RA-GRS ile nasıl çoğaltılır gösterilmektedir:

:::image type="content" source="media/storage-redundancy/geo-redundant-storage.png" alt-text="Verilerin GRS veya RA-GRS ile nasıl çoğaltılacağı gösteren diyagram":::

### <a name="geo-zone-redundant-storage"></a>Coğrafi alanlar arası yedekli depolama

Coğrafi olarak yedekli depolama (GZRS), coğrafi çoğaltma tarafından sunulan bölgesel kesintilerden korunarak kullanılabilirlik alanları genelinde artıklık tarafından sağlanmış yüksek kullanılabilirliği birleştirir. Bir GZRS Storage hesabındaki veriler, birincil bölgedeki üç [Azure kullanılabilirlik bölgesine](../../availability-zones/az-overview.md) kopyalanır ve ayrıca bölgesel felaketlerden koruma için ikincil bir coğrafi bölgeye çoğaltılır. Microsoft, olağanüstü durum kurtarma için maksimum tutarlılık, dayanıklılık ve kullanılabilirlik, mükemmel performans ve esnekliği gerektiren uygulamalar için GZRS kullanılmasını önerir.

Bir GZRS depolama hesabı ile bir kullanılabilirlik alanı kullanılamaz hale gelirse veya kurtarılamaz durumdaysa verileri okumaya ve yazmaya devam edebilirsiniz. Ayrıca, tüm bölgesel bir kesinti veya birincil bölgenin kurtarılamaz bir olağanüstü durum durumunda verileriniz de dayanıklı olur. GZRS belirli bir yıl boyunca nesnelerin en az% 99.99999999999999 (16 9) oranında dayanıklılığını sağlamak üzere tasarlanmıştır.

Aşağıdaki diyagramda, verilerinizin GZRS veya RA-GZRS ile nasıl çoğaltılacağı gösterilmektedir:

:::image type="content" source="media/storage-redundancy/geo-zone-redundant-storage.png" alt-text="GZRS veya RA-GZRS ile verilerin nasıl çoğaltılacağı gösteren diyagram":::

GZRS ve RA-GZRS desteği yalnızca genel amaçlı v2 depolama hesaplarıdır. Depolama hesabı türleri hakkında daha fazla bilgi için bkz. [Azure depolama hesabına genel bakış](storage-account-overview.md). GZRS ve RA-GZRS desteği blok Blobları, sayfa Blobları (VHD diskleri hariç), dosyalar, tablolar ve kuyruklar.

GZRS ve RA-GZRS aşağıdaki bölgelerde desteklenir:

- Doğu Güney Afrika Kuzey
- (Asya Pasifik) Doğu Asya
- (Asya Pasifik) Güneydoğu Asya
- (Asya Pasifik) Avustralya Doğu
- (Asya Pasifik) Orta Hindistan
- (Asya Pasifik) Doğu Japonya
- (Asya Pasifik) Kore Orta
- 'Da Kanada Orta
- 'Ya Kuzey Avrupa
- 'Ya Batı Avrupa
- 'Ya Fransa Orta
- 'Ya Almanya Orta Batı
- 'Ya Norveç Doğu
- 'Ya İsviçre Kuzey
- 'Ya UK Güney
- (Orta Doğu) BAE Kuzey
- (Güney Amerika) Brezilya Güney
- ABD Orta ABD
- (ABD) Doğu ABD
- ABD Doğu ABD 2
- ABD Orta Kuzey ABD
- ABD Orta Güney ABD
- ABD Batı ABD
- ABD Batı ABD 2

Fiyatlandırma hakkında daha fazla bilgi için bkz. [Bloblar](https://azure.microsoft.com/pricing/details/storage/blobs), [dosyalar](https://azure.microsoft.com/pricing/details/storage/files/), [Kuyruklar](https://azure.microsoft.com/pricing/details/storage/queues/)ve [Tablolar](https://azure.microsoft.com/pricing/details/storage/tables/)için fiyatlandırma ayrıntıları.

## <a name="read-access-to-data-in-the-secondary-region"></a>İkincil bölgedeki verilere yönelik okuma erişimi

Coğrafi olarak yedekli depolama (GRS veya GZRS ile), bölgesel kesintilere karşı koruma sağlamak için verilerinizi ikincil bölgedeki başka bir fiziksel konuma çoğaltır. Ancak, bu veriler yalnızca müşteri veya Microsoft birincili ikincil bölgeye yük devretme işlemi başlattığında okunabilir. İkincil bölgeye okuma erişimini etkinleştirdiğinizde, verileriniz her zaman okunabilir ve birincil bölgenin kullanılamaz hale geldiği bir durum da dahil olmak üzere kullanılabilir. İkincil bölgeye okuma erişimi için Okuma Erişimli Coğrafi olarak yedekli depolamayı (RA-GRS) veya Okuma Erişimli Coğrafi bölge-yedekli depolamayı (RA-GZRS) etkinleştirin.

> [!NOTE]
> Azure dosyaları, Okuma Erişimli Coğrafi olarak yedekli depolamayı (RA-GRS) ve Okuma Erişimli Coğrafi bölge erişimli yedekli depolamayı (RA-GZRS) desteklemez.

### <a name="design-your-applications-for-read-access-to-the-secondary"></a>Uygulamalarınızı ikincil için okuma erişimi için tasarlama

Depolama Hesabınız ikincil bölgeye okuma erişimi için yapılandırılmışsa, birincil bölge herhangi bir nedenden dolayı kullanılamaz hale gelirse, uygulamalarınızı ikincil bölgeden verileri okumak için sorunsuz bir şekilde kaydırma yapmak üzere tasarlayabilirsiniz. 

RA-GRS veya RA-GZRS etkinleştirildikten sonra ikincil bölge, okuma erişimi için kullanılabilir. böylece, bir kesinti durumunda ikinciye doğru şekilde okunabilmesi için uygulamanızı önceden test edebilirsiniz. Uygulamalarınızı yüksek kullanılabilirliğe göre tasarlamak hakkında daha fazla bilgi için bkz. [coğrafi artıklığı kullanarak yüksek oranda kullanılabilir uygulamalar tasarlama](geo-redundant-design.md).

İkincil öğesine okuma erişimi etkinleştirildiğinde, uygulamanız ikincil uç noktadan ve birincil uç noktadan okunabilir. İkincil uç nokta son eki *–* hesap adına ekler. Örneğin, BLOB depolama için birincil uç noktanız ise `myaccount.blob.core.windows.net` İkincil uç nokta olur `myaccount-secondary.blob.core.windows.net` . Depolama hesabınızın hesap erişim anahtarları, hem birincil hem de ikincil uç noktalar için aynıdır.

### <a name="check-the-last-sync-time-property"></a>Son Eşitleme Zamanı özelliğini denetleme

Veriler ikincil bölgeye zaman uyumsuz olarak çoğaltıldığından, ikincil bölge genellikle birincil bölgenin arkasında olur. Birincil bölgede bir hata oluşursa, birinciye yazma işlemleri henüz ikinciye çoğaltılmayacak.

Hangi yazma işlemlerinin ikincil bölgeye çoğaltıldığını öğrenmek için, uygulamanız depolama hesabınızın **son eşitleme zamanı** özelliğini denetleyebilir. Birincil bölgeye son eşitleme zamanından önce yazılan tüm yazma işlemleri, ikincil bölgeye başarıyla çoğaltılmıştı ve bu, ikincilden okunmak üzere kullanılabilir. Son eşitleme zamanından sonra birincil bölgeye yazılan yazma işlemleri, ikincil bölgeye çoğaltılmayabilir veya bu, okuma işlemleri için uygun olmayabilir.

**Son eşitleme zamanı** özelliğinin değerini Azure PowerShell, Azure CLI veya Azure Storage istemci kitaplıklarından birini kullanarak sorgulayabilirsiniz. **Son eşitleme zamanı** ÖZELLIĞI bir GMT Tarih/saat değeri. Daha fazla bilgi için bkz. [depolama hesabı Için Son eşitleme zamanı özelliğini denetleme](last-sync-time-get.md).

## <a name="summary-of-redundancy-options"></a>Artıklık seçeneklerinin Özeti

Aşağıdaki bölümlerde yer alarak bulunan tablolar, Azure depolama için kullanılabilen artıklık seçeneklerini özetler

### <a name="durability-and-availability-parameters"></a>Dayanıklılık ve kullanılabilirlik parametreleri

Aşağıdaki tabloda her artıklık seçeneği için anahtar parametreleri açıklanmaktadır:

| Parametre | LRS | ZRS | GRS/RA-GRS | GZRS/RA-GZRS |
|:-|:-|:-|:-|:-|
| Belirli bir yıl boyunca nesnelerin yüzde dayanıklılığı | en az% 99,999999999 (11 9) | en az% 99,9999999999 (12 9) | en az% 99.99999999999999 (16 9) | en az% 99.99999999999999 (16 9) |
| Okuma istekleri için kullanılabilirlik | En az% 99,9 (Seyrek Erişimli Katman için %99) | En az% 99,9 (Seyrek Erişimli Katman için %99) | GRS için en az% 99,9 (Seyrek Erişimli Katman için %99)<br /><br />RA-GRS için en az% 99,99 (Seyrek Erişimli Katman için% 99,9) | GZRS için en az% 99,9 (Seyrek Erişimli Katman için %99)<br /><br />RA-GZRS için en az% 99,99 (Seyrek Erişimli Katman için% 99,9) |
| Yazma istekleri için kullanılabilirlik | En az% 99,9 (Seyrek Erişimli Katman için %99) | En az% 99,9 (Seyrek Erişimli Katman için %99) | En az% 99,9 (Seyrek Erişimli Katman için %99) | En az% 99,9 (Seyrek Erişimli Katman için %99) |
| Ayrı düğümlerde tutulan verilerin kopyalarının sayısı | Tek bir bölge içinde üç kopya | Tek bir bölgedeki ayrı kullanılabilirlik alanları genelinde üç kopya | Birincil bölgede üç kopya ve ikincil bölgede üç kopya | Birincil bölgedeki ayrı kullanılabilirlik alanları genelinde üç kopya ve ikincil bölgede yerel olarak yedekli kopya sayısı dahildir. |

### <a name="durability-and-availability-by-outage-scenario"></a>Kesinti senaryosuna göre dayanıklılık ve kullanılabilirlik

Aşağıdaki tabloda, depolama hesabınız için hangi artıklık türünün geçerli olduğuna bağlı olarak verilerinizin belirli bir senaryoda dayanıklı ve kullanılabilir olup olmadığı gösterilir:

| Kesinti senaryosu | LRS | ZRS | GRS/RA-GRS | GZRS/RA-GZRS |
|:-|:-|:-|:-|:-|
| Veri Merkezi içindeki bir düğüm kullanılamaz duruma gelir | Yes | Yes | Yes | Yes |
| Tüm veri merkezi (zonal veya ZGen olmayan) kullanılamaz hale gelir | Hayır | Yes | Evet<sup>1</sup> | Yes |
| Birincil bölgede bölge genelinde bir kesinti meydana gelir | Hayır | Hayır | Evet<sup>1</sup> | Evet<sup>1</sup> |
| Birincil bölge kullanılamaz hale gelirse ikincil bölgeye okuma erişimi kullanılabilir | Hayır | Hayır | Evet (RA-GRS ile) | Evet (RA-GZRS ile) |

birincil bölge kullanılamaz duruma gelirse, yazma kullanılabilirliğini geri yüklemek için <sup>1</sup> hesap yük devretmesi gerekir. Daha fazla bilgi için bkz. [olağanüstü durum kurtarma ve depolama hesabı yük devretme](storage-disaster-recovery-guidance.md).

### <a name="supported-azure-storage-services"></a>Desteklenen Azure depolama hizmetleri

Aşağıdaki tabloda, her bir Azure depolama hizmeti tarafından hangi artıklık seçeneklerinin desteklendiği gösterilmektedir.

| LRS | ZRS | GRS/RA-GRS | GZRS/RA-GZRS |
|:-|:-|:-|:-|
| Blob depolama<br />Kuyruk depolama<br />Tablo depolama<br />Azure Dosyaları<br />Azure yönetilen diskler | Blob depolama<br />Kuyruk depolama<br />Tablo depolama<br />Azure Dosyaları | Blob depolama<br />Kuyruk depolama<br />Tablo depolama<br />Azure Dosyaları<br /> | Blob depolama<br />Kuyruk depolama<br />Tablo depolama<br />Azure Dosyaları<br /> |

### <a name="supported-storage-account-types"></a>Desteklenen depolama hesabı türleri

Aşağıdaki tabloda, her bir depolama hesabı türü tarafından hangi artıklık seçeneklerinin desteklendiği gösterilmektedir. Depolama hesabı türleri hakkında bilgi için bkz. [depolama hesabına genel bakış](storage-account-overview.md).

| LRS | ZRS | GRS/RA-GRS | GZRS/RA-GZRS |
|:-|:-|:-|:-|
| Genel amaçlı v2<br /> Genel amaçlı v1<br /> BlockBlobStorage<br /> BlobStorage<br /> Dosya depolama | Genel amaçlı v2<br /> BlockBlobStorage<br /> Dosya depolama | Genel amaçlı v2<br /> Genel amaçlı v1<br /> BlobStorage | Genel amaçlı v2 |

Tüm depolama hesaplarına ait tüm veriler, depolama hesabının artıklık seçeneğine göre kopyalanır. Blok Blobları, ekleme Blobları, sayfa Blobları, kuyruklar, tablolar ve dosyalar da dahil olmak üzere nesneler kopyalanır. Arşiv katmanı da dahil olmak üzere tüm katmanlardaki veriler kopyalanır. Blob katmanları hakkında daha fazla bilgi için bkz. [Azure Blob depolama: sık erişimli, seyrek erişimli ve arşiv erişim katmanları](../blobs/storage-blob-storage-tiers.md).

Her artıklık seçeneği için fiyatlandırma bilgileri için bkz. [Azure Storage fiyatlandırması](https://azure.microsoft.com/pricing/details/storage/).

> [!NOTE]
> Azure Premium Disk Depolama şu anda yalnızca yerel olarak yedekli depolamayı (LRS) desteklemektedir. Blok Blob depolama hesapları, belirli bölgelerde yerel olarak yedekli depolamayı (LRS) ve bölge yedekli depolamayı (ZRS) destekler.

## <a name="data-integrity"></a>Veri bütünlüğü

Azure depolama, Döngüsel artıklık denetimleri (CRCs) kullanılarak depolanan verilerin bütünlüğünü düzenli olarak doğrular. Veri bozulması algılanırsa, bu, gereksiz veriler kullanılarak onarılır. Azure Storage Ayrıca veri depolarken veya alırken veri paketlerinin bozulmasını algılamak için tüm ağ trafiğinde sağlama toplamlarını hesaplar.

## <a name="see-also"></a>Ayrıca bkz.

- [Bir depolama hesabı için son eşitleme zamanı özelliğini denetleyin](last-sync-time-get.md)
- [Depolama hesabı için artıklık seçeneğini değiştirme](redundancy-migration.md)
- [Yüksek oranda kullanılabilir uygulamalar tasarlamak için coğrafi artıklığı kullanın](geo-redundant-design.md)
- [Olağanüstü durum kurtarma ve depolama hesabı yükünü devretme](storage-disaster-recovery-guidance.md)
