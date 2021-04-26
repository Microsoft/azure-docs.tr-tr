---
title: Genel Bakış-MariaDB için Azure veritabanı
description: MySQL Community Edition 'ı temel alan Microsoft bulutundaki bir ilişkisel veritabanı hizmeti olan MariaDB hizmeti için Azure veritabanı hakkında bilgi edinin.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: overview
ms.custom: mvc
ms.date: 3/18/2020
ms.openlocfilehash: 8115625099543d378728a6313a8cc4c95fec0cd2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98662026"
---
# <a name="what-is-azure-database-for-mariadb"></a>MariaDB için Azure Veritabanı nedir?

MariaDB için Azure Veritabanı, Microsoft bulutunda sunulan bir ilişkisel veritabanı hizmetidir. MariaDB için Azure veritabanı, [MariaDB Community Edition](https://mariadb.org/download/) 'ı (GPLv2 lisansı altında bulunur) veritabanı altyapısı, sürüm 10,2 ve 10,3 temel alır.

MariaDB için Azure Veritabanı şu özellikleri sunar:

- Ek ücret olmadan yerleşik yüksek kullanılabilirlik.
- Kullandıkça öde fiyatlandırması ile tahmin edilebilir performans.
- Saniyeler içinde ihtiyaca göre ölçeklendirme.
- Bekleyen ve hareket halindeki hassas veriler için güvenli koruma.
- Otomatik yedeklemeler ve 35 güne kadar zamanda belirli bir noktaya geri yükleme.
- Kurumsal düzeyde güvenlik ve uyumluluk.

Bu özelliklerin yönetilmesine neredeyse hiç gerek yoktur. Bu özellikler ücretsiz sunulur. MariaDB için Azure Veritabanı, uygulamanızı hızla geliştirmenize ve pazara ulaşma sürenizi kısaltmanıza yardımcı olabilir. Değerli vaktinizi ve kaynaklarınızı sanal makine ve altyapı yönetimine ayırmanıza gerek yoktur. Ayrıca dilediğiniz açık kaynak araçlarını ve platformu kullanarak uygulamanızı geliştirmeye devam edebilirsiniz. Yeni beceri edinmeye gerek kalmadan işletmenizin ihtiyaç duyduğu hız ve verimlilikle çalışın.

Performans, ölçeklenebilirlik ve yönetilebilirlik dahil olmak üzere MariaDB için Azure Veritabanı'nın temel kavramları ve özellikleri hakkında daha fazla bilgi edinmek için şu hızlı başlangıçlara bakın:

- [Azure portalı kullanarak MariaDB için Azure Veritabanı sunucusu oluşturma](quickstart-create-mariadb-server-database-using-azure-portal.md)
- [Azure CLI kullanarak MariaDB için Azure Veritabanı sunucusu oluşturma](quickstart-create-mariadb-server-database-using-azure-cli.md)

<!--
For a set of Azure CLI samples, see:
- [Azure CLI samples for Azure Database for MariaDB](sample-scripts-azure-cli.md) 
-->

## <a name="adjust-performance-and-scale-within-seconds"></a>Saniyeler içinde performansı ve ölçeği ayarlama

MariaDB için Azure veritabanı hizmeti birçok hizmet katmanı sunar: temel, Genel Amaçlı ve bellek için Iyileştirilmiş. Her katman, hafiften ağıra kadar tüm iş yüklerini desteklemek üzere farklı performans ve özellikler getirir. İlk uygulamanızı aylık birkaç dolar ücretle küçük bir veritabanı üzerinde oluşturabilir ve sonra çözümünüzün gereksinimlerine göre ölçeği ayarlayabilirsiniz. Dinamik ölçeklendirebilirlik, veritabanınızın hızla değişen kaynak gereksinimlerine saydam bir şekilde yanıt verebilmesine yardımcı olur. Yalnızca ihtiyacınız olan kaynaklar için ve yalnızca bunlara ihtiyacınız olduğunda ödeme yaparsınız. Ayrıntılar için bkz. [Fiyatlandırma katmanları](concepts-pricing-tiers.md).

## <a name="monitoring-and-alerting"></a>İzleme ve uyarı

Ölçeğin ne zaman artırılacağına veya azaltılacağına nasıl karar verirsiniz? MariaDB için Azure Veritabanı'nın yerleşik performans izleme ve uyarı özelliklerini, sanal çekirdekleri temel alan performans değerlendirmeleriyle birlikte kullanabilirsiniz. Bu araçları kullanarak geçerli veya projeye özgü performans ihtiyaçlarınıza göre sanal çekirdeklerin ölçeğini büyütme veya küçültme işlemlerinin etkisini hızla değerlendirebilirsiniz. Ayrıntılar için bkz. [Uyarılar](howto-alert-metric.md).

## <a name="keep-your-app-and-business-running"></a>Uygulamanızın ve işinizin hiç kesintiye uğramamasını sağlayın

Azure 'un sektörde önde gelen% 99,99 kullanılabilirlik SLA 'Sı, Microsoft tarafından yönetilen küresel bir veri merkezi ağı ile desteklenmektedir. Ağ, uygulamanızın 7/24 çalışmasına yardımcı olur. MariaDB için Azure Veritabanı'ndaki yerleşik güvenlik, hataya dayanıklılık ve veri koruma özelliklerinden faydalanabilirsiniz. MariaDB için Azure Veritabanı ile, sunucuyu daha önceki bir durumuna (35 güne kadar) döndürüp kurtarmak için belirli bir noktaya geri yükleme işlemini gerçekleştirebilirsiniz.

## <a name="secure-your-data"></a>Verilerinizin güvenliğini sağlama

Azure veritabanı hizmetlerinin sahip olduğu veri güvenliği anlayışı, MariaDB için Azure Veritabanı'nda da devam etmektedir. MariaDB için Azure Veritabanı bekleyen ve hareket halindeki verileri koruyan, erişimi sınırlandıran ve etkinliği izlemenize yardımcı olan özellikler sunmaktadır. Azure'ın platform güvenliği hakkında bilgi edinmek için [Azure Güven Merkezi](https://www.microsoft.com/trustcenter/security)'ni ziyaret edin. MariaDB güvenlik özellikleri için Azure veritabanı hakkında daha fazla bilgi için bkz. [Güvenliğe genel bakış](concepts-security.md).

## <a name="contacts"></a>Kişiler

MariaDB için Azure Veritabanı’yla çalışma hakkındaki sorularınızı veya önerilerinizi [MariaDB için Azure Veritabanı Ekibine](mailto:AskAzureDBforMariaDB@service.microsoft.com) (teknik destek diğer adı değildir) gönderebilirsiniz.

Ayrıca aşağıdaki iletişim noktalarını da kullanabilirsiniz:
- Azure Desteğine başvurmak için Azure portalda [bir destek isteği açın](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Hesabınızla ilgili bir sorun gidermek için Azure portalda bir [destek isteği açın](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
- Görüş bildirmek veya yeni özellik isteği göndermek için [Azure Geri Bildirim Forumlarında](https://feedback.azure.com/forums/915439-azure-database-for-mariadb) bir giriş oluşturun.

## <a name="next-steps"></a>Sonraki adımlar

MariaDB için Azure Veritabanı hakkında giriş bilgilerini incelediğinize göre şu adımlar için hazırsınız:
- Maliyet karşılaştırmaları ve hesaplayıcıları için [fiyatlandırma](https://azure.microsoft.com/pricing/details/mariadb/) sayfasına bakın. 
- [İlk sunucunuzu oluşturarak](quickstart-create-mariadb-server-database-using-azure-portal.md) başlayın.

<!--- - Build your first app using your preferred language: [Python](./connect-python.md) | [Node.JS](./connect-nodejs.md) | [Java](./connect-java.md) | [Ruby](./connect-ruby.md) | [PHP](./connect-php.md) | [.NET (C#)](./connect-csharp.md) | [Go](./connect-go.md) --->
