---
title: Visual Studio App Center ve Azure hizmetleriyle bulutta uygulama verilerini depolayın, yönetin ve kalıcı hale getirin
description: Mobil uygulama verilerini bulutta depolamanıza, yönetmenize ve kalıcı hale getirebilmenizi sağlayan App Center gibi hizmetler hakkında bilgi edinin.
author: elamalani
ms.assetid: 12344321-0123-4678-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: b7043c7e9bd944bfb3633397edfa3c35609bd8ec
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795581"
---
# <a name="store-sync-and-query-mobile-application-data-from-the-cloud"></a>Buluttan mobil uygulama verilerini depolayın, eşitleyin ve sorgulayın
Ne tür bir uygulama oluşturmakta olursanız olun, büyük olasılıkla verileri oluşturmak ve işlemek olacaktır. Kullanıcı beklentileri yüksektir ve uygulamanın her durumda hızlı ve sorunsuz çalışmasını ister. Birçok uygulama birden çok cihazda çalışır, bu nedenle uygulamanıza eriştiğinizde, bu cihaza bir masaüstü veya mobil cihazdan erişebilirsiniz. Birden çok kullanıcı aynı anda uygulamayı kullanıyor ve verilere anında ve gerçek zamanlı erişim elde etme beklentisiyle veri paylaşımında olabilir.

Bunun üzerine, uygulama kullanıcılarınız her zaman internet bağlantısına sahip olmayacaktır. uygulamalar tasarlanır ve internet bağlantısı olmadan veya bunlarla birlikte çalışıyor olması beklenir. Bu büyüyen karmaşıklıklarla, geliştiricilerin uygulamaları için harika bir müşteri deneyimi sağlamak amacıyla verilerini buluta depolamak ve eşitlemek için doğru çözümü seçmesi çok büyük bir görevdir.

Microsoft, sunucuları alma, veritabanınızı seçme veya mümkün olduğunca zengin deneyim sağlamak için ölçek veya güvenlik konusunda endişelenmeye gerek duymanızı ortadan kaldıran çeşitli hizmetler sunar. Bu hizmetler, SQL veya NoSQL API 'Leri kullanarak uygulama verilerini bulutta depolamanıza, tüm cihazlarda verileri eşitlemenize ve ölçeklenebilir ve dayanıklı hale getirmenize yardımcı olmak üzere bir ağ bağlantısı olmadan uygulamanın çalışmasını sağlamanıza olanak tanıyan harika bir geliştirici deneyimi sağlar. uygulamaları.

Mobil uygulama verilerini bulutta yönetmek ve depolamak için aşağıdaki hizmetleri kullanın.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
[App Center veriler](/appcenter/data/) , uygulamaların bulutta bulunan uygulama verilerini hem çevrimiçi hem de çevrimdışı senaryolarda farklı cihazlar ve platformlar arasında yönetmesine, kalıcı hale getirebilmesine ve eşitlemesini sağlayan bir veri yönetim hizmetidir. Kullanıcı tabanınız ve uygulama sayısı arttıkça ölçeklendirilebilen **Cosmos DB hizmeti üzerine inşa** edilen bu hizmet, tüm verileriniz için düşük gecikme süresi, yüksek kullanılabilirlik ve yüksek ölçeklenebilirlik sağlar.

**Temel Özellikler**
- Kolayca **Yeni bir Cosmos DB veritabanı sağlayın** veya App Center portalından **var olan bir Cosmos db veritabanına bağlanın** .
- Uygulama verilerini kolayca depolamak, eşitlemek ve sorgulamak için **NoSQL veritabanı desteği** .
- **Cosmos DB üzerine inşa edilen**bu hizmet, Azure Cosmos DB tarafından sunulan başlıca özelliklerin çoğunu devralır ve iş gereksinimlerinizi karşılamak için **küresel olarak ölçeklenebilirler** .
- Verileri cihazlarda eşitlemek için **çevrimiçi ve çevrimdışı eşitleme** özellikleri.
- Her iki özel uygulama verisini kolayca yönetmenizi sağlayan mobil **Istemci SDK 'ları** .
- **Platform desteği** -IOS, Android, Xamarin, yerel olarak tepki verme.

**Başvur**
- [App Center kaydolun](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [App Center verilerle çalışmaya başlama](/appcenter/data/getting-started)

## <a name="azure-cosmos-db"></a>Azure Cosmos DB
[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) , Microsoft 'un genel olarak dağıtılmış, çok modelli bir veritabanı hizmetidir ve bu, Planet ölçekli uygulamalar oluşturmanıza yardımcı olur. Bir düğmeye tıklayarak Cosmos DB, dünyanın dört bir yanındaki çok sayıda Azure bölgesinde işleme ve depolamayı esnek ve bağımsız olarak ölçeklendirmenizi sağlar. SQL, MongoDB, Cassandra, tablolar veya Gremlin dahil en sevdiğiniz API yüzeylerinizi kullanarak, işleme ve depolamayı esnek ölçeklendirebilir ve hızlı, tek basamaklı milisaniyelik veri erişiminden yararlanabilirsiniz. Cosmos DB, üretilen iş, gecikme süresi, kullanılabilirlik ve tutarlılık için kapsamlı hizmet düzeyi sözleşmeleri (SLA 'Lar) sağlar.

**Temel Özellikler**
- , SQL (Core) API, Cassandra API, MongoDB API, Gremlin API ve Tablo API'si dahil olmak üzere **çok çeşitli API 'leri** destekler.
- **Anahtar alma küresel dağıtım** , kullanıcılarınızın nerede olduğu her yerde verilerinizi çoğalttığından, böylece kullanıcılarınız kendilerine en yakın verilerin bir çoğaltmayla etkileşime geçebilir.
- Veritabanı altyapısı tam olarak şema belirsiz olduğundan **şema veya dizin yönetimi yoktur** .
- Ortak buluttaki 54 + bölgeleri de dahil olmak üzere, dünyanın her yerindeki tüm Azure bölgelerinde kullanılabilir Cosmos DB için **Ubititous bölgesel varlığı** .
- Tam olarak tanımlanmış, Cosmos DB çok yöneticili çoğaltma protokolü olarak **çok sayıda tutarlılık seçeneği** , iyi tanımlanmış beş tutarlılık seçimi (güçlü, sınırlanmış Eskime durumu, oturum, tutarlı ön ek ve nihai) sunmak için dikkatle tasarlanmıştır.
- okuma ve yazma işlemleri için **% 99,999 kullanılabilirlik** .
- **Programlı olarak (veya Portal aracılığıyla)** uygulamanızın bölgesel bir olağanüstü duruma karşı tasarlandığından emin olmak Için Cosmos hesabınızın bölgesel yük devretmesini çağırın.
- **En fazla 99. yüzdebirlik**, dünya çapındaki.

**Başvur**
- [Azure portalda](https://portal.azure.com) 
- [Belgelerle](/azure/cosmos-db/introduction)   

## <a name="azure-sql-database"></a>Azure SQL Veritabanı
 [Azure SQL veritabanı](https://azure.microsoft.com/services/sql-database/) , Azure buluttaki uygulamalar ve çözümler için yüksek düzeyde kullanılabilir ve yüksek performanslı bir veri depolama katmanı oluşturmanızı sağlayan genel amaçlı ilişkisel veritabanı tarafından yönetilen bir hizmettir.

**Temel Özellikler**
- **Elastik veritabanı modelleri ve araçları** -elastik bir veritabanı, geliştiricilerin bir veritabanı grubu arasında kullanmak üzere kaynakları havuzlamasına olanak sağlar. Bu, daha sonra bir betiği iş olarak göndererek yönetici tarafından YÖNETILEBILIR ve SQL veritabanı, veritabanları genelinde betiği.
- **Yüksek performans** -yüksek performanslı uygulamalar, %25 daha fazla Premium veritabanı gücü sunan en son sürümden yararlanabilir.
- **Yedeklemeler, çoğaltma ve yüksek kullanılabilirliğe** sahip yerleşik çoğaltma ve veritabanı düzeyinde Microsoft tarafından desteklenen bir SLA, uygulama sürekliliği ve olağanüstü olaylara karşı koruma sağlar. Etkin coğrafi çoğaltma, "kurtarma" üzerinde tam denetim sağlayan, yük devretmeyi ve self servis geri yüklemeyi yapılandırmanıza olanak tanır (35 güne kadar kullanılabilir veri yedeklemelerinden veri geri yükleme).
- **Neredeyse sıfır bakım** -otomatik yazılım hizmetin bir parçasıdır ve yerleşik sistem çoğaltmaları, devralınan veri koruma, veritabanı çalışma süresi ve sistem kararlılığını sunmaya yardımcı olur. Sistem kopyaları, eski bilgisayarlar kapandıkça hareket halindeyken sağlanan yeni bilgisayarlara otomatik olarak taşınır.
- **Güvenlik** -SQL veritabanı, kurumsal veya sektör-uygulanan uyumluluk ilkelerini karşılamak için bir güvenlik özellikleri portföyü sunar
    - Denetim, geliştiricilere uyumlulukla ilgili görevleri gerçekleştirme ve etkinlikler hakkında bilgi elde etme olanağı sunar.
    - Geliştiriciler ve, Azure SQL veritabanı için satır düzeyinde güvenlik, dinamik veri maskeleme ve saydam veri şifrelemesi ile hassas verilere erişimi sınırlamaya yardımcı olması için veritabanı düzeyinde ilkeler uygulayabilir.
    - SQL veritabanı, HIPAA BAA, ISO/ıEC 27001:2005, Fedrampa ve AB model yan tümceleri gibi başlıca Azure uyumluluk sertifikaları ve onaylarının bir parçası olarak önemli bulut denetleyicileri tarafından doğrulanır.

**Başvur**
- [Azure portalda](https://portal.azure.com) 
- [Belgelerle](/azure/sql-database/)
   