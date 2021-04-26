---
title: Azure Cosmos DB bekleyen şifreleme
description: Azure Cosmos DB, bekleyen verilerin şifrelenmesi ve nasıl uygulandığı hakkında bilgi edinin.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/19/2020
ms.custom: seodec18
ms.openlocfilehash: abe4bb3a53e3177857cb8041fd255ec916985054
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93089874"
---
# <a name="data-encryption-in-azure-cosmos-db"></a>Azure Cosmos DB'de veri şifreleme 
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Bekleyen şifreleme, genellikle katı hal sürücüleri (SSD 'Ler) ve sabit disk sürücüleri (HDD 'Ler) gibi kalıcı depolama cihazlarındaki verilerin şifrelenmesini ifade eden bir tümceciktir. Cosmos DB, birincil veritabanlarını SSD 'lerde depolar. Medya ekleri ve yedeklemeleri, genellikle HDD 'Ler tarafından yedeklenen Azure Blob depolama alanında depolanır. Cosmos DB için bekleyen şifreleme sürümü ile tüm veritabanlarınız, medya ekleri ve yedeklemeleriniz şifrelenir. Verileriniz artık aktarım sırasında (ağ üzerinden) ve bekleyen (kalıcı depolama), uçtan uca şifreleme sağlayan bir biçimde şifrelenir.

PaaS hizmeti olarak Azure Cosmos DB kullanımı çok kolaydır. Azure Cosmos DB içinde depolanan tüm Kullanıcı verileri REST ve aktarımda şifrelendiğinden, herhangi bir işlem gerçekleştirmeniz gerekmez. Bunu yerleştirmek için başka bir yol da, bekleyen şifrelemenin varsayılan olarak "açık" olmasını sağlar. Kapatılacak veya açık bir denetim yoktur. Azure Cosmos DB, hesabın çalıştığı tüm bölgelerde AES-256 şifrelemesini kullanır. [Kullanılabilirlik ve performans SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db)'larımızı karşılamaya devam ediyoruz, bu özelliği sağlıyoruz. Azure Cosmos hesabınızda depolanan veriler, Microsoft tarafından yönetilen anahtarlarla otomatik olarak ve sorunsuz bir şekilde şifrelenir (hizmet tarafından yönetilen anahtarlar). İsteğe bağlı olarak, [müşteri tarafından yönetilen anahtarlar](how-to-setup-cmk.md) makalesinde açıklandığı gibi kendi anahtarlarınız ile ikinci bir şifreleme katmanı eklemeyi tercih edebilirsiniz.

## <a name="implementation-of-encryption-at-rest-for-azure-cosmos-db"></a>Azure Cosmos DB için bekleyen şifreleme uygulama

Bekleyen şifreleme, güvenli anahtar depolama sistemleri, şifrelenmiş ağlar ve şifreleme API 'Leri dahil olmak üzere çeşitli güvenlik teknolojileri kullanılarak uygulanır. Verilerin şifresini çözen ve işleyen sistemlerin, anahtarları yöneten sistemlerle iletişim kurması gerekir. Diyagramda, şifrelenmiş verilerin ve anahtarların yönetiminin nasıl ayrıldığı gösterilmektedir. 

:::image type="content" source="./media/database-encryption-at-rest/design-diagram.png" alt-text="Tasarım diyagramı" border="false":::

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
Y: Evet, bu özellik artık yeni Azure Cosmos DB hesapları için kullanılabilir ve bu, hesap oluşturma sırasında yapılmalıdır. Daha fazla bilgi için lütfen [müşteri tarafından yönetilen anahtarlar](./how-to-setup-cmk.md) belgesine gidin.

### <a name="q-what-regions-have-encryption-turned-on"></a>S: şifreleme hangi bölgelerde açık?
Y: tüm Azure Cosmos DB bölgelerinin tüm Kullanıcı verileri için şifrelenmesi açıktır.

### <a name="q-does-encryption-affect-the-performance-latency-and-throughput-slas"></a>S: şifreleme, performans gecikmesini ve üretilen iş SLA 'larını etkiler mi?
Y: artık mevcut ve yeni hesaplar için bekleyen şifreleme özelliğinin etkin olduğundan emin olmak için performans SLA 'Ları üzerinde hiçbir etkisi veya değişiklik yoktur. En son garantilerini görmek için [SLA Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db) sayfasında daha fazla bilgi edinebilirsiniz.

### <a name="q-does-the-local-emulator-support-encryption-at-rest"></a>S: yerel öykünücü, bekleyen şifrelemeyi destekliyor mu?
Y: öykünücü, tek başına bir geliştirme/test aracıdır ve yönetilen Cosmos DB hizmetinin kullandığı anahtar yönetim hizmetlerini kullanmaz. Önemli öykünücü testi verilerini depoladığınız sürücülerde BitLocker 'ı etkinleştirmek bizim önerimiz. [Öykünücü, varsayılan veri dizininin ve](local-emulator.md) iyi bilinen bir konumun değiştirilmesini destekler.

## <a name="next-steps"></a>Sonraki adımlar

* Daha fazla bilgi edinmek için, kendi anahtarlarınız ile ikinci bir şifreleme katmanı eklemeyi seçebilirsiniz, daha fazla bilgi edinmek için [müşteri tarafından yönetilen anahtarlar](how-to-setup-cmk.md) makalesine bakın.
* Cosmos DB güvenliğe ve en son geliştirmelere genel bakış için bkz. [Azure Cosmos veritabanı güvenliği](database-security.md).
* Microsoft sertifikaları hakkında daha fazla bilgi için [Azure Güven Merkezi](https://azure.microsoft.com/support/trust-center/)bakın.