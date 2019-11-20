---
title: Çok kiracılı SaaS desenleri
description: Azure bulut ortamında çalışan çok kiracılı hizmet olarak yazılım (SaaS) veritabanı uygulamalarının gereksinimleri ve ortak veri mimarisi desenleri hakkında bilgi edinin.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seoapril2019
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: billgib, sstein
ms.date: 01/25/2019
ms.openlocfilehash: ad7bd660ee685b490fb79c7e63fd3c5fce557977
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73822069"
---
# <a name="multi-tenant-saas-database-tenancy-patterns"></a>Çok kiracılı SaaS veritabanı Kiracı desenleri

Bu makalede, çok kiracılı bir SaaS uygulaması için kullanılabilen çeşitli kiracı modelleri açıklanmaktadır.

Çok kiracılı bir SaaS uygulaması tasarlarken, uygulamanızın ihtiyaçlarına en uygun kiracı modelini dikkatle seçmeniz gerekir.  Bir kiracı modeli, her bir kiracının verilerinin depolama ile nasıl eşleştirildiğini belirler.  Kiracı modeliniz seçiminiz uygulama tasarımını ve yönetimini etkiler.  Daha sonra farklı bir modele geçiş bazen maliyetlidir.

## <a name="a-saas-concepts-and-terminology"></a>A. SaaS kavramları ve terminolojisi

Hizmet olarak yazılım (SaaS) modelinde, şirketiniz yazılımlarınıza *Lisans* satmaz. Bunun yerine, her müşteri, her müşteriyi şirketiniz için *kiracıya* getiren, şirketinize yönelik olan ödemeleri ister.

Kiracının ödenmesine yönelik olarak, her kiracı SaaS Uygulama bileşenlerinizi erişim alır ve verileri SaaS sisteminde depolanır.

Kiracı *modelinin* terimi, kiracıların depolanan verilerinin nasıl düzenlendiğini belirtir:

- *Tek kiracılı:* &nbsp; her veritabanı, verileri yalnızca bir kiracıdan depolar.
- *Çoklu kiracı:* &nbsp; her veritabanı, verileri birden çok ayrı kiracıdan depolar (veri gizliliğini koruma mekanizmalarıyla).
- Karma kiracı modelleri de mevcuttur.

## <a name="b-how-to-choose-the-appropriate-tenancy-model"></a>B. Uygun kiracı modelini seçme

Genel olarak, kiracı modeli bir uygulamanın işlevini etkilemez, ancak genel çözümün diğer yönlerini büyük olasılıkla etkiler.  Modellerin her birini değerlendirmek için aşağıdaki ölçütler kullanılır:

- **Sorun**
    - Kiracı sayısı.
    - Kiracı başına depolama alanı.
    - Toplamada depolama.
    - Yüküne.

- **Kiracı yalıtımı:** veri yalıtımının ve performansının&nbsp; (bir kiracının iş yükünün diğerlerini etkileyip etkilemediğini).

- **Kiracı başına maliyet:** &nbsp; veritabanı maliyetleri.

- **Geliştirme karmaşıklığı:**
    - Şemadaki değişiklikler.
    - Sorgularda yapılan değişiklikler (model için gereklidir).

- **İşlemsel karmaşıklık:**
    - Performansı izleme ve yönetme.
    - Şema Yönetimi.
    - Kiracı geri yükleniyor.
    - Olağanüstü durum kurtarma.

- **Özelleştirme:** kiracıya özgü veya kiracı sınıfına özgü şema özelleştirmelerini destekleme kolaylığını&nbsp;.

Kiracı tartışması *veri* katmanına odaklanılmıştır.  Ancak *uygulama* katmanının bir süre için göz önünde bulundurun.  Uygulama katmanı tek parçalı bir varlık olarak değerlendirilir.  Uygulamayı birçok küçük bileşene böleseniz, kiracı modeli seçiminiz değişebilir.  Bazı bileşenleri, hem kiralamanın hem de depolama teknolojisinin veya platformun kullanıldığı diğer diğerlerine göre farklı şekilde ele alabilirsiniz.

## <a name="c-standalone-single-tenant-app-with-single-tenant-database"></a>C. Tek kiracılı veritabanı ile tek başına tek kiracılı uygulama

#### <a name="application-level-isolation"></a>Uygulama düzeyi yalıtımı

Bu modelde, tüm uygulama her kiracı için bir kez tekrar tekrar yüklenir.  Uygulamanın her örneği tek başına bir örnek olduğundan, hiçbir şekilde başka bir tek başına örnekle etkileşime girer.  Uygulamanın her örneği yalnızca bir kiracıya sahiptir ve bu nedenle yalnızca bir veritabanına ihtiyacı vardır.  Kiracının veritabanına hepsi sahiptir.

![Tek bir tek kiracılı veritabanı ile tek başına uygulama tasarımı.][image-standalone-app-st-db-111a]

Her uygulama örneği ayrı bir Azure Kaynak grubuna yüklenir.  Kaynak grubu, yazılım satıcısı ya da kiracıya ait olan bir aboneliğe ait olabilir.  Her iki durumda da satıcı, kiracının yazılımını yönetebilir.  Her uygulama örneği karşılık gelen veritabanına bağlanacak şekilde yapılandırılır.

Her kiracı veritabanı tek bir veritabanı olarak dağıtılır.  Bu model, en büyük veritabanı yalıtımını sağlar.  Ancak yalıtım, en yüksek yüklerini işlemek için her bir veritabanına yeterli kaynakların ayrılmasını gerektirir.  Burada, elastik havuzların farklı kaynak gruplarında veya farklı aboneliklerde dağıtılan veritabanları için kullanılamaz olması önemlidir.  Bu sınırlama, bu tek başına tek kiracılı uygulama modelini genel bir veritabanı maliyet perspektifinden en pahalı çözüm haline getirir.

#### <a name="vendor-management"></a>Satıcı yönetimi

Satıcı, uygulama örnekleri farklı kiracı aboneliklerine yüklense bile, tüm tek başına uygulama örneklerinde bulunan tüm veritabanlarına erişebilir.  Erişim, SQL bağlantıları aracılığıyla gerçekleştirilir.  Bu çapraz örnek erişimi, satıcının, raporlama veya analiz amaçları için şema yönetimini ve çapraz veritabanı sorgusunu merkezileştirme olanağı sağlayabilir.  Bu tür bir merkezi yönetim isteniyorsa, kiracı tanımlayıcılarını veritabanı URI 'leriyle eşleyen bir katalog dağıtılmalıdır.  Azure SQL veritabanı, bir katalog sağlamak için SQL veritabanıyla birlikte kullanılan bir parça kitaplığı sağlar.  Parçalı kitaplık, [elastik veritabanı Istemci kitaplığı][docu-elastic-db-client-library-536r]olarak adlandırılır.

## <a name="d-multi-tenant-app-with-database-per-tenant"></a>D. Kiracı başına veritabanı ile çok kiracılı uygulama

Bu sonraki model, tek kiracılı veritabanları olmak üzere çok sayıda veritabanına sahip çok kiracılı bir uygulama kullanır.  Her yeni kiracı için yeni bir veritabanı sağlandı.  Uygulama katmanı, düğüm başına daha fazla kaynak *eklenerek dikey olarak* ölçeklendirilir.  Ya da uygulama daha fazla düğüm *ekleyerek yatay olarak* ölçeklendirilir.  Ölçeklendirme iş yükünü temel alır ve ayrı veritabanlarının sayısından veya ölçeğinden bağımsızdır.

![Kiracı başına veritabanı ile çok kiracılı uygulama tasarımı.][image-mt-app-db-per-tenant-132d]

#### <a name="customize-for-a-tenant"></a>Kiracı için özelleştirme

Tek başına uygulama düzenine benzer şekilde, tek kiracılı veritabanlarının kullanılması güçlü kiracı yalıtımı sağlar.  Modeli yalnızca tek kiracılı veritabanlarını belirten herhangi bir uygulamada, belirli bir veritabanının şeması, kiracının kendisi için özelleştirilebilir ve en iyi duruma getirilebilir.  Bu özelleştirme, uygulamadaki diğer kiracılarını etkilemez. Belki de bir kiracının, tüm kiracıların gerek duyduğu temel veri alanları ötesinde veri ihtiyacı olabilir.  Ayrıca, ek veri alanı bir dizin gerektirebilir.

Kiracı başına veritabanı ile bir veya daha fazla kiracı için şemayı özelleştirmek basittir.  Uygulama satıcısının, şema özelleştirmelerini ölçeklendirerek dikkatle yönetmesi için yordamları tasarlaması gerekir.

#### <a name="elastic-pools"></a>Esnek havuzlar

Veritabanları aynı kaynak grubunda dağıtıldığında, elastik havuzlarda gruplanabilir.  Havuzlar, kaynakları birçok veritabanı arasında paylaşmak için uygun maliyetli bir yol sağlar.  Bu havuz seçeneği, her bir veritabanının karşılaştığı kullanım üst düzeye uyum sağlayacak kadar büyük olmasını gerektirenden çok daha ucuz.  Havuza alınmış veritabanları kaynaklara erişimi paylaşsa bile, yüksek derecede performans yalıtımına devam edebilirler.

![Elastik havuz kullanılarak, kiracı başına veritabanı ile çok kiracılı uygulama tasarımı.][image-mt-app-db-per-tenant-pool-153p]

Azure SQL veritabanı, paylaşımı yapılandırmak, izlemek ve yönetmek için gerekli araçları sağlar.  Havuz düzeyi ve veritabanı düzeyinde performans ölçümleri, Azure portal ve Azure Izleyici günlükleri aracılığıyla kullanılabilir.  Ölçümler, hem toplama hem de kiracıya özgü performansa harika Öngörüler verebilir.  Ayrı veritabanları, belirli bir kiracıya ayrılmış kaynaklar sağlamak için havuzlar arasında taşınabilir.  Bu araçlar uygun maliyetli bir şekilde performans sağlar.

#### <a name="operations-scale-for-database-per-tenant"></a>Kiracı başına veritabanı için işlem ölçeği

Azure SQL veritabanı platformunun, 100.000 veritabanlarının yanı sıra çok sayıda veritabanını ölçeklendirerek yönetmek için tasarlanan birçok yönetim özelliği vardır.  Bu özellikler, kiracı başına veritabanı düzeninin bir üst öğesine sahip olmasını kolaylaştırır.

Örneğin, bir sistemin tek bir veritabanı olarak 1000 kiracı veritabanının olduğunu varsayalım.  Veritabanında 20 dizin olabilir.  Sistem, 1000 tek kiracılı veritabanlarına sahip olmaya dönüştürdüğünde, dizinlerin miktarı 20.000 ' ye göre yapılır.  [Otomatik ayarlama][docu-sql-db-automatic-tuning-771a]işleminin bir PARÇASı olarak SQL veritabanı 'nda otomatik dizin oluşturma özellikleri varsayılan olarak etkindir.  Otomatik Dizin oluşturma, sizin için tüm 20.000 dizinleri ve devam eden oluşturma ve bırakma iyileştirmelerini yönetir.  Bu otomatikleştirilmiş eylemler tek bir veritabanı içinde gerçekleşir ve diğer veritabanlarındaki benzer eylemler tarafından koordine edilmez veya kısıtlanmaz.  Otomatik Dizin oluşturma, dizinlere, daha az meşgul bir veritabanından çok meşgul bir veritabanında farklı davranır.  Bu çok büyük yönetim görevinin el ile yapılması gerekiyorsa, bu tür bir dizin yönetimi özelleştirmesi, kiracı başına veritabanı ölçeğinde pratik bir şekilde yapılır.

Ölçeklendirilen diğer yönetim özellikleri şunlardır:

- Yerleşik yedeklemeler.
- Yüksek kullanılabilirlik.
- Disk üzerinde şifreleme.
- Performans telemetrisi.

#### <a name="automation"></a>Automation

Yönetim işlemleri, çalıştırılabilir ve bir [DevOps][http-visual-studio-devops-485m] modeliyle sunulabilir.  İşlemler bile otomatikleştirilebilir ve uygulamada açığa çıkabilir.

Örneğin, tek bir kiracının kurtarma işlemini zaman içinde daha önceki bir noktaya otomatik hale getirebilirsiniz.  Kurtarmanın yalnızca kiracıyı depolayan tek kiracılı bir veritabanını geri yüklemesi gerekir.  Bu geri yüklemenin diğer kiracılar üzerinde hiçbir etkisi yoktur. Bu, yönetim işlemlerinin her bir kiracının çok ayrıntılı düzeyinde olduğunu onaylar.

## <a name="e-multi-tenant-app-with-multi-tenant-databases"></a>E. Çok kiracılı veritabanları içeren çok kiracılı uygulama

Kullanılabilir başka bir model, çok kiracılı bir veritabanında birçok kiracıyı depolardır.  Uygulama örneği herhangi bir sayıda çok kiracılı veritabanına sahip olabilir.  Bir çok kiracılı veritabanının şeması bir veya daha fazla kiracı tanımlayıcı sütununa sahip olmalıdır, böylece belirli bir Kiracıdaki verilerin seçmeli olarak alınabilmesi için.  Ayrıca, şema yalnızca kiracıların bir alt kümesi tarafından kullanılan birkaç tablo veya sütun gerektirebilir.  Ancak, statik kod ve başvuru verileri yalnızca bir kez depolanır ve tüm kiracılar tarafından paylaşılır.

#### <a name="tenant-isolation-is-sacrificed"></a>Kiracı yalıtımı feda

*Veri:* çok kiracılı bir veritabanının&nbsp; kiracı yalıtımı olması gerektiğini.  Birden çok kiracının verileri bir veritabanında birlikte depolanır.  Geliştirme sırasında, sorguların birden fazla kiracıdan verileri hiçbir şekilde kullanıma sunduğundan emin olun.  SQL veritabanı, bir sorgudan döndürülen verilerin tek bir kiracıya kapsam uygulanmasını zorlayabilen [satır düzeyi güvenliği][docu-sql-svr-db-row-level-security-947w]destekler.

*İşleme:* çok kiracılı bir veritabanı&nbsp; tüm kiracılar genelinde işlem ve depolama kaynaklarını paylaşır.  Veritabanı bir bütün olarak, kabul edilebilir bir şekilde çalıştığından emin olmak için izlenebilir.  Ancak, Azure sisteminde tek bir kiracı tarafından bu kaynakların kullanımını izlemeye veya yönetmeye yönelik yerleşik bir yol yoktur.  Bu nedenle, çok kiracılı veritabanı, bir aşırı etkin kiracının iş yükünün aynı veritabanındaki diğer kiracıların performans deneyimini etkilediği gürültülü komşuları ile karşılaşmadan daha fazla risk taşır.  Ek uygulama düzeyi izleme, kiracı düzeyinde performansı izleyebilir.

#### <a name="lower-cost"></a>Düşük maliyet

Genel olarak, çok kiracılı veritabanlarının en düşük kiracı başına maliyeti vardır.  Tek bir veritabanı için kaynak maliyetleri, equivalently boyutlu elastik bir havuzdan daha düşüktür.  Bunlara ek olarak, kiracıların yalnızca sınırlı depolama ihtiyacı olan senaryolarda, milyonlarca kiracı büyük olasılıkla tek bir veritabanında depolanabilir.  Esnek havuz milyonlarca veritabanına sahip olamaz.  Ancak, 1000 havuzlarıyla, havuz başına 1000 veritabanı içeren bir çözüm, yönetmek için uygun hale gelme riski altında milyonlarca ölçeğe ulaşabilir.

Çok kiracılı bir veritabanı modelinin iki farklı çeşidi, parçalı çok kiracılı model en esnek ve ölçeklenebilir olacak şekilde aşağıdaki şekilde ele alınmıştır.

## <a name="f-multi-tenant-app-with-a-single-multi-tenant-database"></a>Vadeli. Tek bir çok kiracılı veritabanı ile çok kiracılı uygulama

En basit çok kiracılı veritabanı, tüm kiracılara yönelik verileri barındırmak için tek bir veritabanı kullanır.  Daha fazla kiracı eklendikçe, veritabanı daha fazla depolama ve işlem kaynaklarıyla ölçeklenir.  Bu ölçek artırma, her zaman bir en son ölçek sınırı olmasına rağmen gerekli olabilir.  Ancak, bu sınıra ulaşılmadan önce uzun bir süre, veritabanının yönetilmesi için çok daha etkin hale gelir.

Ayrı kiracılara odaklanan yönetim işlemleri çok kiracılı bir veritabanında uygulamak daha karmaşıktır.  Ve bu işlemler ölçeğinde, kabul edilebilir yavaş hale gelebilir.  Tek bir örnek, tek bir kiracının verilerinin bir nokta geri yüklemesi örneğidir.

## <a name="g-multi-tenant-app-with-sharded-multi-tenant-databases"></a>Acil. Parçalı çok kiracılı veritabanları içeren çok kiracılı uygulama

Birçok SaaS uygulaması tek seferde yalnızca bir kiracının verilerine erişir.  Bu erişim stili, kiracı verilerinin birden çok veritabanına veya parçalara dağıtılmasına olanak tanır. Bu, herhangi bir kiracının tüm verilerinin tek parça içinde yer aldığı yerdir.  Çok kiracılı bir veritabanı düzeniyle birleştirildiğinde, parçalı bir model neredeyse sınırsız ölçeğe izin verir.

![Parçalı çok kiracılı veritabanları içeren çok kiracılı uygulama tasarımı.][image-mt-app-sharded-mt-db-174s]

#### <a name="manage-shards"></a>Parçaları yönetme

Parçalama, hem tasarım hem de işletimsel yönetime karmaşıklık ekler.  Kiracılar ve veritabanları arasındaki eşlemenin korunması için bir katalog gereklidir.  Ayrıca, parçaları ve kiracı popülasyonu yönetmek için yönetim yordamları gerekir.  Örneğin, yordamlar, parçalama eklemek ve kaldırmak ve kiracı verilerini parçalar arasında taşımak için tasarlanmalıdır.  Ölçeklendirmenin bir yolu, yeni bir parça ekleyip yeni kiracılar ile doldurularak yapılır.  Diğer zamanlarda, daha seyrek doldurulmuş bir parçayı daha seyrek doldurulmuş parçalara ayırabilirsiniz.  Birkaç kiracı taşındıktan veya kullanımdan kaldırıldıktan sonra, seyrek doldurulmuş parçaları birlikte birleştirebilirsiniz.  Birleştirme, daha düşük maliyetli kaynak kullanımına neden olur.  Kiracılar iş yüklerini dengelemek için parçalar arasında da taşınabilir.

SQL veritabanı, parça kitaplığı ve Katalog veritabanıyla birlikte çalışan bir bölünmüş/birleştirme aracı sağlar.  Belirtilen uygulama parçaları bölebilir ve birleştirebilir ve kiracı verilerini parçalar arasında taşıyabilir.  Uygulama Ayrıca, bu işlemler sırasında kataloğu korur, etkilenen kiracılar onları taşımadan önce çevrimdışı olarak işaretleniyor.  Taşıma işleminden sonra uygulama, kataloğu yeni eşleme ile tekrar güncelleştirir ve kiracıyı yeniden çevrimiçi olarak işaretler.

#### <a name="smaller-databases-more-easily-managed"></a>Daha küçük veritabanları daha kolay yönetiliyor

Kiracılar birden çok veritabanına dağıtarak, parçalı çok kiracılı çözüm daha kolay yönetilebilen daha küçük veritabanlarına neden olur.  Örneğin, belirli bir kiracıyı önceki bir noktaya geri yüklemek artık, tüm kiracıların bulunduğu daha büyük bir veritabanı yerine bir yedeklemeden tek bir küçük veritabanının geri yüklenmesini içerir. Veritabanı boyutu ve veritabanı başına kiracı sayısı, iş yükünün ve yönetim çalışmalarının dengelenmesi için seçilebilir.

#### <a name="tenant-identifier-in-the-schema"></a>Şemada kiracı tanımlayıcısı

Kullanılan parça yaklaşımına bağlı olarak, veritabanı şemasına ek kısıtlamalar uygulanabilir.  SQL veritabanı bölünmüş/birleştirme uygulaması şemanın, genellikle kiracı tanımlayıcısı olan parçalama anahtarını içermesi gerekir.  Kiracı tanımlayıcısı, tüm parçalı tabloların birincil anahtarındaki önde gelen öğedir.  Kiracı tanımlayıcısı, bölünmüş/birleştirme uygulamasının belirli bir kiracı ile ilişkili verileri hızlı bir şekilde bulmasını ve taşımasını sağlar.

#### <a name="elastic-pool-for-shards"></a>Parçalar için elastik havuz

Parçalı çok kiracılı veritabanları, esnek havuzlara yerleştirilebilir.  Genel olarak, bir havuzda çok sayıda tek kiracılı veritabanı olması, birkaç çok kiracılı veritabanında birçok kiracıya sahip olmaya uygun maliyetli bir yöntemdir.  Çok kiracılı veritabanları, çok sayıda görece devre dışı kiracı olduğunda avantajlıdır.

## <a name="h-hybrid-sharded-multi-tenant-database-model"></a>olsun. Karma parça oluşturulmuş çok kiracılı veritabanı modeli

Karma modelde, tüm veritabanlarının şemasında kiracı tanımlayıcısı vardır.  Veritabanlarının hepsi birden fazla kiracı depolama kapasitesine sahiptir ve veritabanları parçalı olabilir.  Bu nedenle, şema anlamda tüm çok kiracılı veritabanları vardır.  Henüz uygulamada, bu veritabanlarından bazıları yalnızca bir kiracı içeriyor.  Ne olursa olsun, belirli bir veritabanında depolanan kiracı miktarının veritabanı şeması üzerinde hiçbir etkisi yoktur.

#### <a name="move-tenants-around"></a>Kiracılar etrafında taşı

İstediğiniz zaman, belirli bir kiracıyı kendi çok kiracılı veritabanına taşıyabilirsiniz.  İstediğiniz zaman fikrinizi değiştirebilir ve kiracıyı birden fazla kiracı içeren bir veritabanına geri taşıyabilirsiniz.  Yeni veritabanını sağladığınızda yeni tek kiracılı veritabanına da bir kiracı atayabilirsiniz.

Karma model, kaynak gereksinimlerine yönelik olarak tanımlanabilen kiracı grupları arasında büyük farklar olduğunda oluşur.  Örneğin, ücretsiz bir denemeye katılan kiracının, kiracıların abone olduğu yüksek performans düzeyine sahip olduğunu garanti etmez.  İlke, ücretsiz deneme aşamasındaki kiracıların tüm ücretsiz deneme kiracılar arasında paylaşılan çok kiracılı bir veritabanında depolanması olabilir.  Ücretsiz bir deneme kiracısı temel hizmet katmanına abone olduğunda, kiracı daha az kiracıya sahip olabilecek başka bir çok kiracılı veritabanına taşınabilir.  Premium hizmet katmanı için ödeme yapan bir abone, kendi yeni tek kiracılı veritabanına taşınabilir.

#### <a name="pools"></a>Havuzlar

Bu karma modelde, kiracı kiracılarının tek kiracılı veritabanları, kiracı başına veritabanı maliyetlerini azaltmak için kaynak havuzlarına yerleştirilebilir.  Bu, kiracı başına veritabanı modelinde da yapılır.

## <a name="i-tenancy-models-compared"></a>I. Karşılaştırılan kiracı modelleri

Aşağıdaki tabloda, ana kiracı modelleri arasındaki farklar özetlenmektedir.

| Ölçüm | Tek başına uygulama | Kiracı başına veritabanı | Parçalı çoklu kiracı |
| :---------- | :------------- | :------------------ | :------------------- |
| Ölçek | Orta<br />1-100s | Çok yüksek<br />1-100, 000s | Sınırsız<br />1-1, 000, 000s |
| Kiracı yalıtımı | Çok yüksek | Yüksek | Zayıf tek bir kiracı (bir MT DB 'de tek başına) dışında. |
| Kiracı başına veritabanı maliyeti | Geniş , tepe noktaları için boyutlandırılır. | Zayıf kullanılan havuzlar. | En düşük, MT DBs 'deki küçük kiracılar için. |
| Performans izleme ve yönetimi | Yalnızca kiracı başına | Toplu + kiracı başına | Birleşik , ancak yalnızca Singapur için kiracı başına olur. |
| Geliştirme karmaşıklığı | Düşük | Düşük | Uzunlukta parça nedeniyle. |
| İşlemsel karmaşıklık | Düşük-yüksek. Ölçekteki tek tek basit, karmaşık. | Düşük orta. Desenler ölçekteki karmaşıklığı ele. | Düşük-yüksek. Tek tek Kiracı Yönetimi karmaşıktır. |
| &nbsp; ||||

## <a name="next-steps"></a>Sonraki adımlar

- [Kiracı için veritabanı SaaS modeli kullanan çok kiracılı bir Wingtip uygulamasını dağıtın ve araştırın-Azure SQL veritabanı][docu-sql-db-saas-tutorial-deploy-wingtip-db-per-tenant-496y]

- [Wingtip bilet örnek SaaS Azure SQL veritabanı Kiracı uygulaması 'na hoş geldiniz][docu-saas-tenancy-welcome-wingtip-tickets-app-384w]


<!--  Article link references.  -->

[http-visual-studio-devops-485m]: https://www.visualstudio.com/devops/

[docu-sql-svr-db-row-level-security-947w]: https://docs.microsoft.com/sql/relational-databases/security/row-level-security

[docu-elastic-db-client-library-536r]: sql-database-elastic-database-client-library.md
[docu-sql-db-saas-tutorial-deploy-wingtip-db-per-tenant-496y]: sql-database-saas-tutorial.md
[docu-sql-db-automatic-tuning-771a]: sql-database-automatic-tuning.md
[docu-saas-tenancy-welcome-wingtip-tickets-app-384w]: saas-tenancy-welcome-wingtip-tickets-app.md


<!--  Image references.  -->

[image-standalone-app-st-db-111a]: media/saas-tenancy-app-design-patterns/saas-standalone-app-single-tenant-database-11.png "Tek bir tek kiracılı veritabanı ile tek başına uygulama tasarımı."

[image-mt-app-db-per-tenant-132d]: media/saas-tenancy-app-design-patterns/saas-multi-tenant-app-database-per-tenant-13.png "Kiracı başına veritabanı ile çok kiracılı uygulama tasarımı."

[image-mt-app-db-per-tenant-pool-153p]: media/saas-tenancy-app-design-patterns/saas-multi-tenant-app-database-per-tenant-pool-15.png "Elastik havuz kullanılarak, kiracı başına veritabanı ile çok kiracılı uygulama tasarımı."

[image-mt-app-sharded-mt-db-174s]: media/saas-tenancy-app-design-patterns/saas-multi-tenant-app-sharded-multi-tenant-databases-17.png "Parçalı çok kiracılı veritabanları içeren çok kiracılı uygulama tasarımı."

