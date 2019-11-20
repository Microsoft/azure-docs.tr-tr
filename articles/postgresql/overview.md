---
title: PostgreSQL için Azure Veritabanı ilişkisel veritabanı hizmetine genel bakış
description: PostgreSQL için Azure Veritabanı ilişkisel veritabanı hizmetine genel bir bakış sağlar.
author: jonels-msft
ms.author: jonels
ms.custom: mvc
ms.service: postgresql
ms.topic: overview
ms.date: 05/06/2019
ms.openlocfilehash: 9d4389ed2c0cc5d7b632380fba691ae2e9a6b257
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73466744"
---
# <a name="what-is-azure-database-for-postgresql"></a>PostgreSQL için Azure Veritabanı nedir?
PostgreSQL için Azure veritabanı, geliştiriciler için derlenmiş Microsoft bulutundaki bir ilişkisel veritabanı hizmetidir. Bu, açık kaynak [PostgreSQL](https://www.postgresql.org/) veritabanı altyapısının topluluk sürümüne dayalıdır ve iki dağıtım seçeneklerinde mevcuttur: tek sunucu ve hiper ölçek (Citus).

## <a name="azure-database-for-postgresql---single-server"></a>PostgreSQL için Azure veritabanı-tek sunucu
Tek sunuculu dağıtım seçeneği şunları sağlar:

- Ek ücret ödemeden yerleşik yüksek kullanılabilirlik (% 99,99 SLA)
- Kapsamlı kullandıkça öde fiyatlandırması kullanılarak öngörülebilir performans
- Saniyeler içinde gereken şekilde dikey ölçek
- Ölçeklendirmenin etkilerini hızlı bir şekilde değerlendirmek için izleme ve uyarma
- Bekleyen ve hareket halindeki hassas verileri korumaya yönelik güvenlik
- Otomatik yedeklemeler ve 35 güne kadar belirli bir noktaya geri yükleme
- Kurumsal düzeyde güvenlik ve uyumluluk

Tüm bu özellikler neredeyse hiç yönetim gerektirmez ve tümüyle ek ücret ödemeden sağlanır. Bu kişiler, sanal makineleri ve altyapıyı yönetmek için değerli zaman ve kaynakları harcamaktansa hızlı uygulama geliştirmeye odaklanmanıza ve zamanınızı pazara sunma sürecinize olanak tanır. Yeni beceriler öğrenmeye gerek kalmadan, seçtiğiniz açık kaynaklı araçlar ve platformla uygulamanızı geliştirmeye devam edebilirsiniz.

Tek sunuculu dağıtım seçeneği üç fiyatlandırma katmanı sunar: temel, Genel Amaçlı ve bellek için Iyileştirilmiş. Her katman veritabanı iş yükünüzü desteklemek için farklı kaynak özellikleri sunar. İlk uygulamanızı aylık birkaç dolar ücretle küçük bir veritabanı üzerinde oluşturabilir ve sonra çözümünüzün gereksinimlerine göre ölçeği ayarlayabilirsiniz. Dinamik ölçeklendirebilirlik, veritabanınızın hızla değişen kaynak gereksinimlerine saydam bir şekilde yanıt verebilmesini sağlar. Yalnızca ihtiyacınız olan kaynaklar için ve yalnızca bunlara ihtiyacınız olduğunda ödeme yaparsınız. Ayrıntılar için bkz. [fiyatlandırma katmanları](concepts-pricing-tiers.md) .

## <a name="azure-database-for-postgresql---hyperscale-citus"></a>PostgreSQL için Azure veritabanı-hiper ölçek (Citus)
Hyperscale (Citus) seçeneği, parçaları kullanarak birden çok makine genelinde sorguları yatay olarak ölçeklendirir. Sorgu altyapısı, büyük veri kümelerinde daha hızlı yanıtlar için bu sunucular genelinde gelen SQL sorgularını paralelleştiriyor. Daha fazla ölçek ve performans gerektiren uygulamalara, genellikle yaklaştığı veya en fazla-100 GB veri aşmış olan iş yükleri sunar.

Hyperscale (Citus) dağıtım seçeneği şunları sağlar:

- Parçalara ayırma kullanılarak birden çok makine arasında yatay ölçeklendirme
- Büyük veri kümelerinde daha hızlı yanıtlar için bu sunucular genelinde paralelleştirme sorgula
- Çok kiracılı uygulamalar, gerçek zamanlı işlem analizi ve yüksek verimlilik işlem iş yükleri için mükemmel destek

PostgreSQL için derlenmiş uygulamalar, standart [bağlantı kitaplıkları](./concepts-connection-libraries.md) ve en az değişiklikle hiper ölçekte (Citus) dağıtılmış sorgular çalıştırabilir.

## <a name="data-security"></a>Veri güvenliği
PostgreSQL için Azure veritabanı, Azure veritabanı Hizmetleri ' gelenek of Data Security 'yi içerir. Erişimi sınırlayan, verileri REST ve hareket halindeyken koruyan ve etkinliği izlemenize yardımcı olan özelliklere sahiptir. Azure'ın platform güvenliği hakkında bilgi edinmek için [Azure Güven Merkezi](https://azure.microsoft.com/overview/trusted-cloud/)'ni ziyaret edin.

PostgreSQL için Azure veritabanı hizmeti, bekleyen verilerin depolama şifrelemesi için FIPS 140-2 tarafından doğrulanan şifreleme modülünü kullanır. Yedeklemeler de dahil olmak üzere veriler, sorgular çalıştırılırken oluşturulan geçici dosyalar hariç olmak üzere diskte şifrelenir. Hizmet, Azure depolama şifrelemesi 'ne dahil olan AES 256 bitlik şifrelemeyi kullanır ve anahtarlar sistem tarafından yönetilir. Depolama şifrelemesi her zaman açıktır ve devre dışı bırakılamaz. Varsayılan olarak, PostgreSQL için Azure veritabanı hizmeti, hem ağ hem de veritabanı ile istemci uygulaması arasında hareket halindeki veriler için güvenli bağlantılar gerektirir.

## <a name="contacts"></a>Kişiler
PostgreSQL için Azure veritabanı ile çalışmaya yönelik herhangi bir soru veya öneri için, PostgreSQL için Azure veritabanı ekibine bir e-posta gönderin ([@Ask PostgreSQL Için Azure DB](mailto:AskAzureDBforPostgreSQL@service.microsoft.com)). Bu adres destek bileti yerine genel sorulara yöneliktir.

Ayrıca, bu iletişim noktalarını uygun şekilde göz önünde bulundurun:
- Azure desteği ile iletişim kurmak veya hesabınızla ilgili bir sorunu sabitlemek için [Azure Portal bir bilet](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)gönderin.
- Görüş bildirmek veya yeni özellikler istemek için [UserVoice](https://feedback.azure.com/forums/597976-azure-database-for-postgresql) aracılığıyla bir giriş oluşturun.

## <a name="next-steps"></a>Sonraki adımlar
- Maliyet karşılaştırmaları ve hesaplayıcıları için [fiyatlandırma sayfasına](https://azure.microsoft.com/pricing/details/postgresql/) bakın.
- İlk PostgreSQL için Azure veritabanınızı [tek sunuculu](./quickstart-create-server-database-portal.md) veya [hiper ölçekte oluşturmaya başlayın (Citus)](./quickstart-create-hyperscale-portal.md)
- Python, PHP, Ruby, C\#, Java, Node.js'de ilk uygulamanızı oluşturun: [Bağlantı kitaplıkları](./concepts-connection-libraries.md)
