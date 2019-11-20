---
title: MySQL için Azure veritabanı ilişkisel veritabanı hizmetine genel bakış
description: Microsoft bulutundaki MySQL Community sürümünü temel alan ilişkisel bir veritabanı hizmeti olan MySQL için Azure veritabanı hizmeti hakkında bilgi edinin.
author: ajlam
ms.service: mysql
ms.author: andrela
ms.custom: mvc
ms.topic: overview
ms.date: 08/13/2019
ms.openlocfilehash: f193dceaf26361fececde9df9df6769e2a971543
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72178117"
---
# <a name="what-is-azure-database-for-mysql"></a>MySQL için Azure Veritabanı nedir?

MySQL için Azure veritabanı, [MySQL Community Edition](https://www.mysql.com/products/community/) 'Da (GPLv2 License) veritabanı altyapısı, sürüm 5,6, 5,7 ve 8,0 temel alınarak Microsoft bulutundaki bir ilişkisel veritabanı hizmetidir. MySQL için Azure veritabanı şunları sağlar:

- Ek ücret ödemeden yerleşik yüksek kullanılabilirlik.
- Kapsamlı kullandıkça öde fiyatlandırması kullanılarak öngörülebilir performans.
- Saniyeler içinde gerektiği gibi ölçeklendirin.
- Bekleyen ve hareket halindeki hassas verileri korumaya yönelik güvenlik.
- Otomatik yedeklemeler ve 35 güne kadar belirli bir noktaya geri yükleme.
- Kurumsal düzeyde güvenlik ve uyumluluk.

Bu özellikler neredeyse hiç yönetim gerektirmez ve tümüyle ek ücret ödemeden sağlanır. Bunlar sayesinde değerli zamanınızı ve kaynaklarınızı sanal makine ve altyapı yönetimi yerine hızlı uygulama geliştirmeye ve piyasaya sunma sürenizi kısaltmaya ayırabilirsiniz. Buna ek olarak, kendi seçtiğiniz açık kaynak araçları ve platformuyla uygulamanızı geliştirmeye devam edebilir, böylelikle yeni beceriler edinmek zorunda kalmadan işlerinizin gerektirdiği hızla ve verimlilikle kullanıma sunabilirsiniz.

![MySQL için Azure veritabanı kavramsal diyagramı](media/overview/1-azure-db-for-mysql-conceptual-diagram.png)

Bu makalede, ayrıntıları keşfetmeye yönelik bağlantılarla birlikte, MySQL için Azure veritabanı temel kavramları ve özellikleri ve performans, ölçeklenebilirlik ve yönetilebilirlik ile ilgili özellikler açıklanır. Başlamanıza yardımcı olacak şu hızlı başlangıçlara bakın:

- [Azure portalını kullanarak MySQL için Azure Veritabanı sunucusu oluşturma](quickstart-create-mysql-server-database-using-azure-portal.md)
- [Azure CLI kullanarak MySQL için Azure Veritabanı sunucusu oluşturma](quickstart-create-mysql-server-database-using-azure-cli.md)

Azure CLI örnekleri için bkz:

- [MySQL için Azure veritabanı Azure CLı örnekleri](sample-scripts-azure-cli.md)

## <a name="adjust-performance-and-scale-within-seconds"></a>Saniyeler içinde performansı ve ölçeği ayarlama
MySQL için Azure veritabanı hizmeti, birkaç hizmet katmanı sunar: temel, Genel Amaçlı ve bellek için Iyileştirilmiş. Her katman, hafiften ağıra kadar tüm iş yüklerini desteklemek üzere farklı performans ve özellikler getirir. İlk uygulamanızı aylık birkaç dolar ücretle küçük bir veritabanı üzerinde oluşturabilir ve sonra çözümünüzün gereksinimlerine göre ölçeği ayarlayabilirsiniz. Dinamik ölçeklendirebilirlik, veritabanınızın hızla değişen kaynak gereksinimlerine saydam bir şekilde yanıt verebilmesini sağlar. Yalnızca ihtiyacınız olan kaynaklar için ve yalnızca bunlara ihtiyacınız olduğunda ödeme yaparsınız. Ayrıntılar için bkz. [fiyatlandırma katmanları](concepts-service-tiers.md) .

## <a name="monitoring-and-alerting"></a>İzleme ve uyarı
Ne zaman artırılacağına ve ne zaman azaltılacağını nasıl karar verirsiniz? Yerleşik performans izleme ve uyarı özelliklerini, sanal çekirdekleri temel alan performans değerlendirmeleriyle birlikte kullanırsınız. Bu araçları kullanarak geçerli veya projeye özgü performans ihtiyaçlarınıza göre sanal çekirdeklerin ölçeğini büyütme veya küçültme işlemlerinin etkisini hızla değerlendirebilirsiniz. Ayrıntılar için bkz. [Uyarılar](howto-alert-on-metric.md).

## <a name="keep-your-app-and-business-running"></a>Uygulamanızın ve işinizin hiç kesintiye uğramamasını sağlayın
Azure'ın Microsoft yönetimindeki veri merkezlerinin küresel bir ağı tarafından desteklenen ve endüstri lideri niteliğinde %99,99'luk bir kullanılabilirlik oranı sunan hizmet düzeyi sözleşmesi (SLA), uygulamanızın 7/24 kesintiye uğramamasına yardımcı olur. Her MySQL için Azure veritabanı sunucusunda, satın almanız veya tasarlamanız, oluşturmanız ve yönetmeniz gereken yerleşik güvenlik, hataya dayanıklılık ve veri korumasından yararlanabilirsiniz. MySQL için Azure veritabanı ile, bir sunucuyu daha önceki bir duruma geri yüklemek için, 35 gün olarak geri yükleme yapabilirsiniz.

## <a name="secure-your-data"></a>Verilerinizi güvenli hale getirin
Azure veritabanı Hizmetleri, MySQL için Azure veritabanı 'nın, erişimi sınırlayan, verileri REST ve hareket halindeyken koruyan özelliklerle ve etkinliği izlemenize yardımcı olan bir gelenek veri güvenliği içerir. Azure'ın platform güvenliği hakkında bilgi edinmek için [Azure Güven Merkezi](https://www.microsoft.com/en-us/trustcenter/security)'ni ziyaret edin.

MySQL için Azure veritabanı hizmeti, bekleyen verilerin depolama şifrelemesi için FIPS 140-2 tarafından doğrulanan şifreleme modülünü kullanır. Yedeklemeler de dahil olmak üzere veriler, sorgular çalıştırılırken oluşturulan geçici dosyalar hariç olmak üzere diskte şifrelenir. Hizmet, Azure depolama şifrelemesi kapsamında yer alan AES 256-bit şifrelemesini kullanır ve anahtarlar sistem tarafından yönetilir. Depolama şifrelemesi her zaman açıktır ve devre dışı bırakılamaz.

Varsayılan olarak, MySQL için Azure veritabanı hizmeti, ağ üzerinden hareket halindeki veriler için [SSL bağlantısı güvenliği](./concepts-ssl-connection-security.md) gerektirecek şekilde yapılandırılmıştır. Veritabanı sunucunuzla istemci uygulamalarınız arasında SSL bağlantılarının zorunlu tutulması, sunucuya uygulamanız arasındaki veri akışını şifreleyerek "bağlantıyı izinsiz izleme" saldırılarına karşı korumaya yardımcı olur. İsteğe bağlı olarak, istemci uygulamanızın SSL bağlantısını desteklememesi durumunda veritabanı hizmetinize bağlanmak için SSL zorunluluğunu devre dışı bırakabilirsiniz.

## <a name="contacts"></a>Kişiler
MySQL için Azure veritabanı ile çalışmaya yönelik herhangi bir soru veya öneri için, MySQL için Azure veritabanı ekibine ([@no__t-MySQL Için Azure DB](mailto:AskAzureDBforMySQL@service.microsoft.com)) bir e-posta gönderin. Bu e-posta adresi bir teknik destek diğer adı değil.

Buna ek olarak, aşağıdaki iletişim noktalarını uygun şekilde göz önünde bulundurun:

- Azure Desteği ile iletişim kurmak için [Azure portaldan bir bilet oluşturun](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Hesabınızla ilgili bir sorun gidermek için Azure portalda bir [destek isteği](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) oluşturun.
- Görüş bildirmek veya yeni özellikler istemek için [UserVoice](https://feedback.azure.com/forums/597982-azure-database-for-mysql) aracılığıyla bir giriş oluşturun.

## <a name="next-steps"></a>Sonraki adımlar
Artık MySQL için Azure veritabanı 'na giriş okuduğunuzu ve "MySQL için Azure veritabanı nedir?" sorusunu yanıtladığınıza göre şunları yapmaya hazırsınız:

- Maliyet karşılaştırmaları ve hesaplayıcıları için fiyatlandırma sayfasına bakın. [Fiyatlandırma](https://azure.microsoft.com/pricing/details/mysql/)
- İlk sunucunuzu oluşturarak başlayın. [Azure portalını kullanarak MySQL için Azure Veritabanı sunucusu oluşturma](quickstart-create-mysql-server-database-using-azure-portal.md)
- Tercih ettiğiniz dili kullanarak ilk uygulamanızı oluşturun: [Python](./connect-python.md) | [Node. js](./connect-nodejs.md) | [Java](./connect-java.md) | [Ruby](./connect-ruby.md) | [php](./connect-php.md) | [.net (C#)](./connect-csharp.md)2[Go](./connect-go.md)
