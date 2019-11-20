---
title: Azure Cosmos DB bekleyen şifreleme
description: Azure Cosmos DB, bekleyen verilerin şifrelenmesi ve nasıl uygulandığı hakkında bilgi edinin.
author: markjbrown
ms.author: sngun
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.custom: seodec18
ms.openlocfilehash: a9e89336973b0b13544c5bc0bccec41652c6952e
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72755098"
---
# <a name="data-encryption-in-azure-cosmos-db"></a>Azure Cosmos DB veri şifreleme 

Bekleyen şifreleme, genellikle katı hal sürücüleri (SSD 'Ler) ve sabit disk sürücüleri (HDD 'Ler) gibi kalıcı depolama cihazlarındaki verilerin şifrelenmesini ifade eden bir tümceciktir. Cosmos DB, birincil veritabanlarını SSD 'lerde depolar. Medya ekleri ve yedeklemeleri, genellikle HDD 'Ler tarafından yedeklenen Azure Blob depolama alanında depolanır. Cosmos DB için bekleyen şifreleme sürümü ile tüm veritabanlarınız, medya ekleri ve yedeklemeleriniz şifrelenir. Verileriniz artık aktarım sırasında (ağ üzerinden) ve bekleyen (kalıcı depolama), uçtan uca şifreleme sağlayan bir biçimde şifrelenir.

PaaS hizmeti olarak Cosmos DB kullanımı çok kolaydır. Cosmos DB içinde depolanan tüm Kullanıcı verileri REST ve aktarımda şifrelendiğinden, herhangi bir işlem gerçekleştirmeniz gerekmez. Bunu yerleştirmek için başka bir yol da, bekleyen şifrelemenin varsayılan olarak "açık" olmasını sağlar. Kapatılacak veya açık bir denetim yoktur. Azure Cosmos DB, hesabın çalıştığı tüm bölgelerde AES-256 şifrelemesini kullanır. [Kullanılabilirlik ve performans SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db)'larımızı karşılamaya devam ediyoruz, bu özelliği sağlıyoruz.

## <a name="implementation-of-encryption-at-rest-for-azure-cosmos-db"></a>Azure Cosmos DB için bekleyen şifreleme uygulama

Bekleyen şifreleme, güvenli anahtar depolama sistemleri, şifrelenmiş ağlar ve şifreleme API 'Leri dahil olmak üzere çeşitli güvenlik teknolojileri kullanılarak uygulanır. Verilerin şifresini çözen ve işleyen sistemlerin, anahtarları yöneten sistemlerle iletişim kurması gerekir. Diyagramda, şifrelenmiş verilerin ve anahtarların yönetiminin nasıl ayrıldığı gösterilmektedir. 

![Tasarım diyagramı](./media/database-encryption-at-rest/design-diagram.png)

Bir Kullanıcı isteğinin temel akışı aşağıdaki gibidir:
- Kullanıcı veritabanı hesabı kullanıma sunulacaktır ve depolama anahtarları yönetim hizmeti kaynak sağlayıcısına bir istek aracılığıyla alınır.
- Kullanıcı, HTTPS/güvenli aktarım aracılığıyla Cosmos DB bir bağlantı oluşturur. (SDK 'lar ayrıntıları soyutlar.)
- Kullanıcı, önceden oluşturulan güvenli bağlantı üzerinden depolanacak bir JSON belgesi gönderir.
- Kullanıcı dizin oluşturmayı kapatmadığı takdirde JSON belgesi dizine alınır.
- Hem JSON belgesi hem de dizin verileri, depolamayı güvenli hale getirmek için yazılır.
- Verileri düzenli aralıklarla, güvenli depolama alanından okur ve Azure şifreli blob deposuna yedeklenir.

## <a name="frequently-asked-questions"></a>Sık sorulan sorular

### <a name="q-how-much-more-does-azure-storage-cost-if-storage-service-encryption-is-enabled"></a>S: Depolama Hizmeti Şifrelemesi etkinleştirilmişse Azure depolama maliyeti ne kadar fazla olur?
Y: ek bir maliyet yoktur.

### <a name="q-who-manages-the-encryption-keys"></a>S: şifreleme anahtarlarını kim yönetir?
Y: anahtarlar Microsoft tarafından yönetilir.

### <a name="q-how-often-are-encryption-keys-rotated"></a>S: şifreleme anahtarları ne sıklıkta döndürülür?
Y: Microsoft, Cosmos DB aşağıdaki şifreleme anahtarı dönüşü için bir iç kılavuz kümesine sahiptir. Belirli yönergeler yayımlanmaz. Microsoft, iç yönergelerin bir alt kümesi olarak görülen ve geliştiriciler için faydalı en iyi yöntemlere sahip olan [güvenlik geliştirme yaşam döngüsünü (SDL)](https://www.microsoft.com/sdl/default.aspx)yayımlar.

### <a name="q-can-i-use-my-own-encryption-keys"></a>S: kendi şifreleme anahtarlarımı kullanabilir miyim?
Y: Cosmos DB bir PaaS hizmetidir ve hizmetin kolayca kullanılmasını sağlamak için çok çalıştık. Bu sorunun genellikle PCI DSS gibi bir uyumluluk gereksinimini karşılamak için bir ara sunucu sorusu olarak sorulduğu fark ettik. Bu özelliği oluşturmanın bir parçası olarak, Cosmos DB kullanan müşterilerin, anahtarları yönetmek zorunda kalmadan gereksinimlerini karşıladığından emin olmak için uyumluluk denetçileri ile çalıştık.

### <a name="q-what-regions-have-encryption-turned-on"></a>S: şifreleme hangi bölgelerde açık?
Y: tüm Azure Cosmos DB bölgelerinin tüm Kullanıcı verileri için şifrelenmesi açıktır.

### <a name="q-does-encryption-affect-the-performance-latency-and-throughput-slas"></a>S: şifreleme, performans gecikmesini ve üretilen iş SLA 'larını etkiler mi?
Y: artık mevcut ve yeni hesaplar için bekleyen şifreleme özelliğinin etkin olduğundan emin olmak için performans SLA 'Ları üzerinde hiçbir etkisi veya değişiklik yoktur. En son garantilerini görmek için [SLA Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db) sayfasında daha fazla bilgi edinebilirsiniz.

### <a name="q-does-the-local-emulator-support-encryption-at-rest"></a>S: yerel öykünücü, bekleyen şifrelemeyi destekliyor mu?
Y: öykünücü, tek başına bir geliştirme/test aracıdır ve yönetilen Cosmos DB hizmetinin kullandığı anahtar yönetim hizmetlerini kullanmaz. Önemli öykünücü testi verilerini depoladığınız sürücülerde BitLocker 'ı etkinleştirmek bizim önerimiz. [Öykünücü, varsayılan veri dizininin ve](local-emulator.md) iyi bilinen bir konumun değiştirilmesini destekler.

## <a name="next-steps"></a>Sonraki adımlar

Cosmos DB güvenliğe ve en son geliştirmelere genel bakış için bkz. [Azure Cosmos veritabanı güvenliği](database-security.md).
Microsoft sertifikaları hakkında daha fazla bilgi için [Azure Güven Merkezi](https://azure.microsoft.com/support/trust-center/)bakın.
