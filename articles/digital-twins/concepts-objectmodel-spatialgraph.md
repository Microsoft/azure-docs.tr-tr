---
title: Nesne modellerini ve uzamsal zeka grafiğini anlama-Azure dijital TWINS | Microsoft Docs
description: İnsanlar, yerler ve cihazlar arasındaki ilişkileri modellemek için Azure Digital Twins’i kullanın
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 11/05/2019
ms.openlocfilehash: 85fb67248a69a2726fef99ebe4749d61807631e4
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74010099"
---
# <a name="understand-digital-twins-object-models-and-spatial-intelligence-graph"></a>Dijital TWINS nesne modellerini ve uzamsal zeka grafiğini anlama

Azure dijital TWINS, fiziksel ortamların ve ilişkili cihazların, sensörlerin ve kişilerin kapsamlı sanal sunumlarını destekleyen bir Azure IoT hizmetidir. Etki alanına özgü kavramları yararlı modellerle düzenleyerek geliştirmeyi geliştirir. Modeller daha sonra bir uzamsal zeka grafiği içinde bulunur. Bu tür kavramlar, kişiler, boşluklar ve cihazlar arasındaki ilişkileri ve etkileşimleri modelleyin.

Dijital TWINS nesne modelleri, etki alanına özgü kavramları, kategorileri ve özellikleri anlatmaktadır. Modeller, çözümü belirli gereksinimlerine uyarlamak isteyen kullanıcılar tarafından önceden tanımlanmıştır. Birlikte, bu önceden tanımlanmış dijital TWINS nesne modelleri bir _ontology_yapar. Akıllı bina ontology bölgeleri, havalandırma, zeminler, ofisleri, bölgeleri, konferans odalarını ve odak odalarını açıklar. Energy Grid ontology çeşitli güç istasyonları, alt istasyonlar, enerji kaynakları ve müşteriler hakkında da açıklanmaktadır. Dijital TWINS nesne modelleriyle ve ontoloda, farklı senaryolar ve gereksinimler özelleştirilebilir.

Dijital TWINS nesne modelleriyle ve bir ontology yerinde, _uzamsal bir grafiği_doldurabilirsiniz. Uzamsal grafikler, bir IoT çözümüyle ilgili olan boşluklar, cihazlar ve kişiler arasındaki birçok ilişkinin sanal temsilleridir. Bu diyagramda, akıllı bina ontology kullanan bir uzamsal grafiğe örnek gösterilmektedir.

[![dijital TWINS uzamsal grafik oluşturma](media/concepts/digital-twins-spatial-graph-building.png)](media/concepts/digital-twins-spatial-graph-building.png#lightbox)

Uzamsal grafik, boşluk, cihaz, algılayıcı ve Kullanıcı gibi bir araya getirir. Her biri gerçek dünyayı modelleyen bir şekilde birbirine bağlanır. Bu örnekte, mekan 43, her biri birçok farklı alana sahip dört katça sahiptir. Kullanıcılar iş istasyonlarıyla ilişkilendirilir ve grafiğin bölümlerine erişim sağlar. Bir yöneticinin uzamsal grafiğinde değişiklik yapma hakkı vardır. Bu, bir ziyaretçi yalnızca belirli bina verilerini görüntüleme haklarına sahiptir.

## <a name="digital-twins-object-models"></a>Dijital TWINS nesne modelleri

Dijital TWINS nesne modelleri bu nesne ana kategorilerini destekler:

- **Boşluklar** , `Tenant`, `Customer`, `Region`ve `Venue`gibi sanal veya fiziksel konumlardır.
- **Cihazlar** , `AwesomeCompany Device` ve `Raspberry Pi 3`gibi sanal veya fiziksel ekipman parçalarından oluşur.
- **Algılayıcılar** , örneğin `AwesomeCompany Temperature Sensor` ve `AwesomeCompany Presence Sensor`olaylarını algılayan nesnelerdir.
- **Kullanıcılar** , iştları ve bunların özelliklerini belirler.

Diğer nesne kategorileri şunlardır:

- **Kaynaklar** bir alana iliştirilir ve genellikle uzamsal grafikteki nesneler tarafından kullanılacak Azure kaynaklarını temsil eder, örneğin `IoTHub`.
- **Bloblar** nesnelere (boşluklar, cihazlar, algılayıcılar ve kullanıcılar gibi) eklenir. MIME türü ve meta veriler (örneğin, `maps`, `pictures`ve `manuals`) dosyaları olarak kullanılırlar.
- **Genişletilmiş türler** , belirli özelliklere sahip varlıkları geliştiren Genişletilebilir numaralandırmalar, örneğin `SpaceType` ve `SpaceSubtype`.
- **Ontolo,** , `Default`, `Building`, `BACnet`ve `EnergyGrid`gibi bir genişletilmiş türler kümesini temsil eder.
- **Özellik anahtarları ve değerleri** , boşlukların, cihazların, sensörların ve kullanıcıların özel özelliklerdir. Yerleşik özelliklerle birlikte kullanılabilir; Örneğin, anahtar olarak `DeltaProcessingRefreshTime` ve değer olarak `10`.
- **Roller** , uzamsal grafikteki kullanıcılara ve cihazlara atanan izin kümeleridir; örneğin, `Space Administrator`, `User Administrator`ve `Device Administrator`.
- **Rol atamaları** , uzamsal grafikteki bir rol ve bir nesne arasındaki ilişkidir. Örneğin, bir kullanıcı veya hizmet sorumlusu, uzamsal grafikteki bir alanı yönetmek için izin verebilir.
- **Güvenlik anahtarı depoları** , cihazın dijital TWINS ile güvenli bir şekilde iletişim kurmasına izin vermek için belirli bir alan nesnesinin altındaki hiyerarşideki tüm cihazlar için güvenlik anahtarlarını sağlar.
- **Kullanıcı tanımlı işlevler** (UDF 'ler), uzamsal grafik içinde özelleştirilebilir algılayıcı telemetri işlemesine izin verir. Örneğin, bir UDF şunları yapabilir:
  - Bir algılayıcı değeri ayarlayın.
  - Algılayıcı okumaları temelinde özel mantık gerçekleştirin ve çıktıyı bir boşluk olarak ayarlayın.
  - Bir alana meta veriler ekleyin.
  - Önceden tanımlanmış koşullar karşılandığında bildirim gönder. Şu anda, UDF 'ler JavaScript 'te yazılabilir.
- **Eşleştiriciler** , belirli bir telemetri iletisi Için hangi UDF 'leri çalıştırılacağını belirleyen nesnelerdir.
- **Uç noktalar** telemetri Iletilerinin ve dijital TWINS olaylarının yönlendirilebileceği konumlardır; örneğin, `Event Hub`, `Service Bus`ve `Event Grid`.

## <a name="spatial-intelligence-graph"></a>Uzamsal zeka grafı

Uzamsal grafik, dijital TWINS nesne modelinde tanımlanan boşlukların, cihazların ve kişilerin hiyerarşik grafiktir. Uzamsal grafik devralma, filtreleme, geçiş, ölçeklenebilirlik ve genişletilebilirliği destekler. Bir REST API koleksiyonu ile uzamsal grafınızı yönetebilir ve bunlarla etkileşim kurabilirsiniz.

Aboneliğinizde bir dijital TWINS hizmeti dağıtırsanız, kök düğümün genel yöneticisi olursunuz. Böylece, tüm yapıya otomatik olarak tam erişim hakkı vermiş olursunuz. Boşluk API 'sini kullanarak grafikte boşluk sağlayın. Algılayıcı API 'sini kullanarak cihaz API 'sini ve algılayıcıları kullanarak hizmetleri sağlayın. [Açık kaynak araçları](https://github.com/Azure-Samples/digital-twins-samples-csharp) , grafiği toplu olarak sağlamak için de kullanılabilir.

**Grafik devralma**. Devralma, bir üst düğümden altındaki tüm düğümlere yönelik olan izinler ve özellikler için geçerlidir. Örneğin, bir rol belirli bir düğümdeki bir kullanıcıya atandığında, Kullanıcı o rolün verilen düğüm ve altındaki her düğüm için bu izinlere sahip olur. Belirli bir düğüm için tanımlanan her özellik anahtarı ve genişletilmiş tür, bu düğümün altındaki tüm düğümler tarafından devralınır.

**Grafik filtreleme**. Filtreleme, istek sonuçlarının kapsamını daraltmak için kullanılır. Kimlikler, ad, türler, alt türler, üst boşluk ve ilişkili alanlarla filtreleyebilirsiniz. Ayrıca, algılayıcı veri türlerine, özellik anahtarlarına ve değerlerine, *geçiş*, *MinLevel*, *maxlevel*ve diğer OData filtre parametrelerine göre de filtre uygulayabilirsiniz.

**Grafik geçişi**. Uzamsal grafiği derinliğine ve enine göre çapraz geçiş yapabilirsiniz. Derinlemesine *geçiş*, *MinLevel*ve *maxlevel*parametrelerini kullanarak grafik yukarıdan aşağıya veya alt alta çapraz geçiş yapın. Bir üst alana veya alt öğelerinden birine doğrudan bağlı eşdüzey düğümleri almak için grafiği gezin. Bir nesneyi sorgulayıp, GET API 'lerinin *içerme* parametresini kullanarak bu nesneyle ilişkiler içeren ilgili tüm nesneleri alabilirsiniz.

**Grafik ölçeklenebilirliği**. Dijital TWINS, grafik ölçeklenebilirliğini garanti eder, böylece gerçek dünyada iş yüklerinizi işleyebilir. Dijital TWINS, büyük ölçekli emlak, altyapı, cihaz, sensör, telemetri ve daha fazlasını temsil etmek için kullanılabilir.

**Grafik genişletilebilirliği**. Temel alınan dijital TWINS nesne modellerini yeni türler ve ontolode ile özelleştirmek için genişletilebilirlik kullanın. Ayrıca, dijital TWINS verileriniz Genişletilebilir Özellikler ve değerlerle de zenginleştirilebilir.

### <a name="spatial-intelligence-graph-management-apis"></a>Uzamsal zeka grafik yönetimi API 'Leri

[Azure Portal](https://portal.azure.com)dijital TWINS 'i dağıttıktan sonra, yönetim API 'lerinin [Swagger](https://swagger.io/tools/swagger-ui/) URL 'si otomatik olarak oluşturulur. **Genel bakış** bölümündeki Azure Portal aşağıdaki biçimde gösterilir.

```plaintext
https://YOUR_INSTANCE_NAME.YOUR_LOCATION.azuresmartspaces.net/management/swagger
```

| Ad | Şununla değiştir |
| --- | --- |
| YOUR_INSTANCE_NAME | Dijital İkizlerini örneğinizin adı |
| YOUR_LOCATION | Örneğiniz üzerinde barındırılıyorsa hangi sunucu bölge |

 Tam URL biçimi bu görüntüde görünür.

[![Digital TWINS Portal Yönetim API 'SI](media/concepts/digital-twins-spatial-graph-management-api-url.png)](media/concepts/digital-twins-spatial-graph-management-api-url.png#lightbox)

Uzamsal zeka grafiklerini kullanma hakkında daha fazla bilgi için Azure Digital TWINS yönetim API 'Leri gizli Önizleme sayfasını ziyaret edin.

> [!TIP]
> API özelliği kümesini göstermek için Swagger gizli dizi önizlemesi sağlanır.
> [Docs.westcentralus.azuresmartspaces.net/Management/Swagger](https://docs.westcentralus.azuresmartspaces.net/management/swagger)adresinde barındırılır.

[Swagger kullanma](how-to-use-swagger.md)hakkında daha fazla bilgi edinin.

Tüm API çağrılarının kimliği, [OAuth](https://docs.microsoft.com/azure/active-directory/develop/v1-protocols-oauth-code)kullanılarak yapılmalıdır. API 'Ler [Microsoft REST API yönergeleri kurallarını](https://github.com/Microsoft/api-guidelines/blob/master/Guidelines.md)izler. Koleksiyonları döndüren API 'lerin çoğu [OData](https://www.odata.org/getting-started/basic-tutorial/#queryData) sistem sorgu seçeneklerini destekler.

## <a name="next-steps"></a>Sonraki adımlar

- Cihaz bağlantısı ve dijital TWINS 'e telemetri iletileri gönderme hakkında bilgi edinmek için [Azure dijital TWINS cihaz bağlantısı ve telemetri](concepts-device-ingress.md)girişi makalesini okuyun.

- Yönetim API 'SI sınırlamaları ve kısıtları hakkında bilgi edinmek için [Azure Digital TWINS API Yönetimi ve sınırlamaları](concepts-service-limits.md)makalesini okuyun.
