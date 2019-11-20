---
title: MariaDB için Azure veritabanı ilişkisel veritabanı hizmetine genel bakış
description: MySQL Community Edition 'ı temel alan Microsoft bulutundaki bir ilişkisel veritabanı hizmeti olan MariaDB hizmeti için Azure veritabanı hakkında bilgi edinin.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: overview
ms.custom: mvc
ms.date: 08/13/2019
ms.openlocfilehash: 46ba972c489326157cbdea19a1363a30e18f72b8
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72177971"
---
# <a name="what-is-azure-database-for-mariadb"></a>MariaDB için Azure veritabanı nedir?

MariaDB için Azure veritabanı, Microsoft bulutundaki bir ilişkisel veritabanı hizmetidir. MariaDB için Azure veritabanı, [MariaDB Community Edition](https://mariadb.org/download/) 'ı (GPLv2 lisansı altında bulunur) veritabanı altyapısı, sürüm 10,2 ve 10,3 temel alır.

MariaDB için Azure veritabanı şunları sağlar:

- Ek ücret ödemeden yerleşik yüksek kullanılabilirlik.
- Kapsamlı Kullandıkça Öde fiyatlandırması kullanılarak öngörülebilir performans.
- Saniyeler içinde gerektiği şekilde ölçeklendirin.
- Bekleyen ve hareketli hassas verilerin güvenli koruması.
- Otomatik yedeklemeler ve zaman içindeki süre-geri yükleme 35 güne kadar.
- Kurumsal düzeyde güvenlik ve uyumluluk.

Bu yetenekler neredeyse hiçbir yönetim gerektirmez. Bunlar ek bir ücret ödemeden sağlanırlar. MariaDB için Azure veritabanı, uygulamanızı hızlı bir şekilde geliştirmenize ve pazara yönelik zamandan hızlandırmanıza yardımcı olabilir. Sanal makineleri ve altyapıyı yönetmek için değerli zaman ve kaynak ayırmanız gerekmez. Ayrıca, seçtiğiniz açık kaynaklı araçları ve platformu kullanarak uygulamanızı geliştirmeye devam edebilirsiniz. Yeni beceriler öğrenmeksizin iş taleplerinizin hızını ve verimliliğini sunun.

Performans, ölçeklenebilirlik ve yönetilebilirlik dahil olmak üzere MariaDB için Azure veritabanı 'nda temel kavramlar ve özellikler hakkında bilgi edinmek için şu hızlı başlangıçlara bakın:

- [Azure portal kullanarak MariaDB sunucusu için Azure veritabanı oluşturma](quickstart-create-mariadb-server-database-using-azure-portal.md)
- [Azure CLı kullanarak MariaDB sunucusu için Azure veritabanı oluşturma](quickstart-create-mariadb-server-database-using-azure-cli.md)

<!--
For a set of Azure CLI samples, see:
- [Azure CLI samples for Azure Database for MariaDB](sample-scripts-azure-cli.md) 
-->

## <a name="adjust-performance-and-scale-within-seconds"></a>Performansı ayarlama ve saniyeler içinde ölçeklendirme

MariaDB için Azure veritabanı hizmeti birçok hizmet katmanı sunar: temel, Genel Amaçlı ve bellek için Iyileştirilmiş. Her katman hafif ve ağır veritabanı iş yüklerini desteklemeye yönelik farklı performans ve yetenekler sunar. İlk uygulamanızı bir ay birkaç ABD Doları için küçük bir veritabanında oluşturabilir ve sonra ölçeklendirmeyi çözümünüzün ihtiyaçlarını karşılayacak şekilde ayarlayabilirsiniz. Dinamik ölçeklenebilirlik, veritabanınızın hızla değişen kaynak gereksinimlerine şeffaf bir şekilde yanıt vermesini sağlar. Yalnızca ihtiyacınız olan kaynaklar için ve yalnızca ihtiyacınız olduğunda ödeme yaparsınız. Ayrıntılar için bkz. [fiyatlandırma katmanları](concepts-pricing-tiers.md) .

## <a name="monitoring-and-alerting"></a>İzleme ve uyarma

Ne zaman ölçekleneceğine veya azaltılamayacağına nasıl karar verirsiniz? MariaDB için Azure veritabanı 'nın yerleşik performans izleme ve uyarı özelliklerini, sanal çekirdekler tabanlı performans derecelendirmeleriyle birlikte kullanabilirsiniz. Bu araçları kullanarak, geçerli veya tahmini performans gereksinimlerinize göre sanal çekirdekleri ölçeklendirmenin etkisini hızla değerlendirebilirsiniz. Ayrıntılar için bkz. [Uyarılar](howto-alert-metric.md) .

## <a name="keep-your-app-and-business-running"></a>Uygulamanızı ve işinizi çalışır durumda tutun

Azure 'un sektörde önde gelen% 99,99 kullanılabilirlik SLA 'Sı, Microsoft tarafından yönetilen küresel bir veri merkezi ağı ile desteklenmektedir. Ağ, uygulamanızı 24/7 çalıştırmaya devam etmenize yardımcı olur. MariaDB için Azure veritabanı 'nda yerleşik güvenlik, hataya dayanıklılık ve veri koruması avantajlarından yararlanabilirsiniz. MariaDB için Azure veritabanı ile, bir sunucuyu daha önceki bir duruma 35 gün kadar geri kurtarmak için belirli bir noktaya geri yükleme kullanabilirsiniz.

## <a name="secure-your-data"></a>Verilerinizin güvenliğini sağlama

Azure veritabanı Hizmetleri, MariaDB için Azure veritabanı 'nın sahip olduğu bir gelenek veri güvenliği içerir. MariaDB için Azure veritabanı, erişimi sınırlayan, bekleyen ve hareketli verileri koruyan ve etkinliği izlemenize yardımcı olan özellikler sunar. Azure 'un Platform güvenliği hakkında bilgi edinmek için [Azure Güven Merkezi](https://www.microsoft.com/en-us/trustcenter/security) ziyaret edin.

MariaDB hizmeti için Azure veritabanı, bekleyen verilerin depolama şifrelemesi için FIPS 140-2 tarafından doğrulanan şifreleme modülünü kullanır. Yedeklemeler de dahil olmak üzere veriler, sorgular çalıştırılırken oluşturulan geçici dosyalar hariç olmak üzere diskte şifrelenir. Hizmet, Azure depolama şifrelemesi 'ne dahil olan AES 256 bit şifre kullanır. Anahtarlar sistem tarafından yönetilmektedir. Depolama şifrelemesi her zaman açıktır ve devre dışı bırakılamaz.

Varsayılan olarak, MariaDB için Azure veritabanı hizmeti, ağ üzerinden hareket halindeki veriler için [SSL bağlantısı güvenliği](./concepts-ssl-connection-security.md) gerektirecek şekilde yapılandırılmıştır. Veritabanı sunucunuz ile istemci uygulamalarınız arasında SSL bağlantılarını zorunlu tutma, sunucu ile uygulamanız arasındaki veri akışını şifreleyerek "ortadaki adam" saldırılarına karşı korunmaya yardımcı olur. İsteğe bağlı olarak, istemci uygulamanız SSL bağlantısını desteklemiyorsa, veritabanı hizmetinize bağlanmak için SSL gereksinimini devre dışı bırakabilirsiniz.

## <a name="contacts"></a>Kişiler

MariaDB için Azure veritabanı ile çalışmak üzere sahip olduğunuz herhangi bir soruyu veya önerisi, [MariaDB ekibine yönelik Azure veritabanı](mailto:AskAzureDBforMariaDB@service.microsoft.com) 'na (teknik destek diğer adı değil) gönderebilirsiniz.

Aşağıdaki iletişim noktalarını da kullanabilirsiniz:
- Azure desteği ile iletişim kurmak için Azure portal [bir destek isteği açın](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) .
- Hesabınızla ilgili bir sorunu onarmak için Azure portal [bir destek isteği açın](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) .
- Geri bildirim sağlamak veya yeni özellikler istemek için [Azure geri bildirim forumlarında](https://feedback.azure.com/forums/915439-azure-database-for-mariadb)bir giriş oluşturun.

## <a name="next-steps"></a>Sonraki adımlar

MariaDB için Azure veritabanı 'na giriş okudığınıza göre şu şekilde hazırsınız:
- Maliyet karşılaştırmaları ve hesaplayıcıları için [fiyatlandırma](https://azure.microsoft.com/pricing/details/mariadb/) sayfasına bakın. 
- [İlk sunucunuzu oluşturarak](quickstart-create-mariadb-server-database-using-azure-portal.md)başlayın.

<!--- - Build your first app using your preferred language: [Python](./connect-python.md) | [Node.JS](./connect-nodejs.md) | [Java](./connect-java.md) | [Ruby](./connect-ruby.md) | [PHP](./connect-php.md) | [.NET (C#)](./connect-csharp.md) | [Go](./connect-go.md) --->
