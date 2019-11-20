---
title: include dosyası
description: include dosyası
services: iot-fundamentals
author: robinsh
ms.service: iot-fundamentals
ms.topic: include
ms.date: 08/07/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: e696db3ad452152f6478701876b7760d7fed355b
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793101"
---
Bu makalede, Azure IoT tabanlı Nesnelerin İnterneti (IoT) altyapısının güvenliğini sağlamaya yönelik bir sonraki ayrıntı düzeyi sunulmaktadır. Her bileşeni yapılandırmak ve dağıtmak için uygulama düzeyi ayrıntılarına bağlantı sağlar. Ayrıca, çeşitli rekabet yöntemleri arasında karşılaştırmalar ve seçimler de sağlar.

Azure IoT dağıtımının güvenliği, aşağıdaki üç güvenlik alanına ayrılabilir:

* **Cihaz güvenliği**: IoT cihazını, joker karakterle dağıtıldığında güvenli hale getirme.

* **Bağlantı güvenliği**: IoT cihazı ve IoT Hub arasında aktarılan tüm verilerin gizli ve yetkisiz prova olduğundan emin olma.

* **Bulut güvenliği**: veri üzerinden aktarılırken verilerin güvenliğini sağlamak ve bulutta depolanmak için bir yol sağlar.

![Üç güvenlik alanı](./media/iot-secure-your-deployment/overview.png)

## <a name="secure-device-provisioning-and-authentication"></a>Güvenli cihaz sağlama ve kimlik doğrulama

IoT çözümü Hızlandırıcılar, aşağıdaki iki yöntemi kullanarak IoT cihazlarını güvenli hale getirme:

* Cihaz tarafından IoT Hub iletişim kurmak için kullanılabilecek benzersiz bir kimlik anahtarı (güvenlik belirteçleri) sağlayarak.

* Aygıtın kimlik doğrulamasını yapmak için bir cihaz olarak bir [X. 509.440 sertifikası](https://www.itu.int/rec/T-REC-X.509-201210-S) ve özel anahtar kullanarak IoT Hub. Bu kimlik doğrulama yöntemi, cihazdaki özel anahtarın herhangi bir zamanda cihazın dışında bilinmemesini sağlar ve daha yüksek düzeyde güvenlik sağlar.

Güvenlik belirteci yöntemi, simetrik anahtarı her çağrıyla ilişkilendirerek IoT Hub için cihaz tarafından yapılan her çağrının kimlik doğrulamasını sağlar. X. 509.440 tabanlı kimlik doğrulaması, TLS bağlantısı kurma kapsamında fiziksel katmanda bir IoT cihazının kimlik doğrulamasına izin verir. Güvenlik belirteci tabanlı yöntem, daha az güvenli bir model olan X. 509.440 kimlik doğrulaması olmadan kullanılabilir. İki yöntem arasındaki seçim öncelikle cihaz kimlik doğrulamasının ne kadar güvenli olduğu ve cihazdaki güvenli depolamanın kullanılabilirliği (özel anahtarı güvenli bir şekilde depolamak için) tarafından belirlenir.

## <a name="iot-hub-security-tokens"></a>IoT Hub güvenlik belirteçleri

IoT Hub, ağ üzerinde anahtar göndermekten kaçınmak için cihazların ve hizmetlerin kimliğini doğrulamak üzere güvenlik belirteçlerini kullanır. Ayrıca, güvenlik belirteçleri zaman geçerliliği ve kapsamında sınırlıdır. Azure IoT SDK 'Ları özel yapılandırma gerektirmeden belirteçleri otomatik olarak oluşturur. Ancak bazı senaryolar, kullanıcının güvenlik belirteçlerini doğrudan oluşturmasını ve kullanmasını gerektirir. Bu senaryolar MQTT, AMQP veya HTTP yüzeylerinin doğrudan kullanımını veya belirteç hizmeti deseninin uygulanmasını içerir.

Güvenlik belirtecinin yapısı ve kullanımları hakkında daha fazla ayrıntı aşağıdaki makalelerde bulunabilir:

* [Güvenlik belirteci yapısı](../articles/iot-hub/iot-hub-devguide-security.md#security-token-structure)

* [SAS belirteçlerini bir cihaz olarak kullanma](../articles/iot-hub/iot-hub-devguide-security.md#use-sas-tokens-in-a-device-app)

Her IoT Hub, hizmette yer alan ve cihaza yönelik buluttan cihaza iletileri içeren bir kuyruk gibi cihaz başına kaynakları oluşturmak için kullanılabilecek bir [kimlik kayıt defterine](../articles/iot-hub/iot-hub-devguide-identity-registry.md) sahiptir ve cihaza yönelik uç noktalara erişime izin verir. IoT Hub kimlik kayıt defteri, bir çözüm için cihaz kimliklerinin ve güvenlik anahtarlarının güvenli bir şekilde depolanmasını sağlar. Bir grup veya cihaz kimliği grubu, cihaz erişimi üzerinde komple denetim sağlayan bir izin verilenler listesine veya engellenenler listesine eklenebilir. Aşağıdaki makalelerde kimlik kayıt defterinin yapısı ve desteklenen işlemler hakkında daha ayrıntılı bilgi sağlanmaktadır.

[IoT Hub MQTT, AMQP ve http gibi protokolleri destekler](../articles//iot-hub/iot-hub-devguide-security.md). Bu protokollerin her biri, farklı IoT Hub için IoT cihazındaki güvenlik belirteçlerini kullanır:

* AMQP: SASL PLAIN ve AMQP talep tabanlı güvenlik (IoT Hub düzeyi belirteçleriyle`{policyName}@sas.root.{iothubName}`, cihaz kapsamlı belirteçler ile `{deviceId}`).

* MQTT: CONNECT paketi, **Kullanıcı adı** alanında `{IoThubhostname}/{deviceId}` `{ClientId}`olarak `{deviceId}` ve **parola** alanında bir SAS belirteci kullanır.

* HTTP: geçerli belirteç, yetkilendirme isteği üst bilgisinde.

IoT Hub kimlik kayıt defteri, cihaz başına güvenlik kimlik bilgilerini ve erişim denetimini yapılandırmak için kullanılabilir. Ancak, bir IoT çözümünün [özel bir cihaz kimliği kayıt defteri ve/veya kimlik doğrulama düzeninde](../articles/iot-hub/iot-hub-devguide-security.md#custom-device-and-module-authentication)önemli bir yatırımı varsa, belirteç hizmeti oluşturularak IoT Hub var olan bir altyapıyla tümleştirilebilir.

### <a name="x509-certificate-based-device-authentication"></a>X. 509.440 sertifika tabanlı cihaz kimlik doğrulaması

[Cihaz tabanlı bir X. 509.952 sertifikası](../articles/iot-hub/iot-hub-devguide-security.md) ve ilişkili özel ve ortak anahtar çiftinin kullanımı, fiziksel katmanda ek kimlik doğrulamasına izin verir. Özel anahtar cihazda güvenli bir şekilde depolanır ve cihazın dışında bulunabilir. X. 509.440 sertifikası cihaz hakkında cihaz KIMLIĞI ve diğer kuruluş ayrıntıları gibi bilgileri içerir. Sertifikanın imzası özel anahtar kullanılarak oluşturulur.

Üst düzey cihaz sağlama akışı:

* Cihaz üretimi veya komisyonlama sırasında cihazla ilişkilendirilmiş fiziksel bir cihazla (cihaz kimliği ve/veya X. 509.440 sertifikası) tanımlayıcıyı ilişkilendirin.

* IoT Hub – cihaz kimliği ve ilişkili cihaz bilgileri ' nde IoT Hub kimlik kayıt defterinde ilgili bir kimlik girişi oluşturun.

* IoT Hub Identity kayıt defterinde X. 509.440 sertifika parmak izini güvenli bir şekilde depolayın.

### <a name="root-certificate-on-device"></a>Cihazdaki kök sertifika

IoT Hub ile güvenli bir TLS bağlantısı kurarken, IoT cihazı, cihaz SDK 'sının bir parçası olan kök sertifikayı kullanarak IoT Hub doğrular. C istemcisi SDK 'Sı için sertifika, deponun kökü altında "\\C\\CERT" klasörü altında bulunur. Bu kök sertifikalar uzun süreli olsa da, hala süreleri doluyor veya iptal edilebilir. Cihazdaki sertifikayı güncelleştirme yöntemi yoksa, cihaz daha sonra IoT Hub (veya başka bir bulut hizmeti) ile bağlantı kurabilmeyebilir. IoT cihazı dağıtıldığında, bu riski etkin bir şekilde azaltır ve kök sertifikayı güncelleştirmeniz anlamına gelir.

## <a name="securing-the-connection"></a>Bağlantının güvenliğini sağlama

IoT cihazı ve IoT Hub arasındaki Internet bağlantısı, Aktarım Katmanı Güvenliği (TLS) standardı kullanılarak güvenli hale getirilir. Azure IoT, [tls 1,2](https://tools.ietf.org/html/rfc5246), TLS 1,1 ve TLS 1,0 bu sırayla desteklenir. TLS 1,0 desteği yalnızca geriye dönük uyumluluk için sağlanır. Mümkünse, en çok güvenliği sağladığından TLS 1,2 kullanın.

## <a name="securing-the-cloud"></a>Bulutun güvenliğini sağlama

Azure IoT Hub her güvenlik anahtarı için [erişim denetimi ilkelerinin](../articles/iot-hub/iot-hub-devguide-security.md) tanımına izin verir. IoT Hub uç noktalarına erişim vermek için aşağıdaki izin kümesini kullanır. İzinler, işlevselliğe göre IoT Hub erişimi sınırlar.

* **Registryread**. Kimlik kayıt defterine okuma erişimi verir. Daha fazla bilgi için bkz. [kimlik kayıt defteri](../articles/iot-hub/iot-hub-devguide-identity-registry.md).

* **Registryreadwrite**. Kimlik kayıt defterine okuma ve yazma erişimi verir. Daha fazla bilgi için bkz. [kimlik kayıt defteri](../articles/iot-hub/iot-hub-devguide-identity-registry.md).

* **Serviceconnect**. Bulut hizmetine yönelik iletişim ve izleme uç noktalarına erişim izni verir. Örneğin, arka uç bulut hizmetlerine cihazdan buluta iletileri alma, buluttan cihaza iletileri gönderme ve ilgili teslim bildirimleri alma izni verir.

* **Deviceconnect**. Cihaza yönelik uç noktalara erişim izni verir. Örneğin, cihazdan buluta iletiler gönderme ve buluttan cihaza iletileri alma izni verir. Bu izin cihazlar tarafından kullanılır.

[Güvenlik belirteçleriyle](../articles/iot-hub/iot-hub-devguide-security.md#use-sas-tokens-in-a-device-app)IoT Hub **deviceconnect** izinleri almanın iki yolu vardır: cihaz kimlik anahtarı veya paylaşılan erişim anahtarı kullanma. Üstelik, cihazlardan erişilebilen tüm işlevlerin, ön ek `/devices/{deviceId}`uç noktalar üzerinde tasarlanarak sunulduğunu unutmamak önemlidir.

[Hizmet bileşenleri yalnızca](../articles/iot-hub/iot-hub-devguide-security.md#use-security-tokens-from-service-components) uygun izinleri veren paylaşılan erişim ilkeleri kullanılarak güvenlik belirteçleri oluşturabilir.

Azure IoT Hub ve çözümün parçası olabilecek diğer hizmetler, Azure Active Directory kullanan kullanıcıların yönetimine izin verir.

Azure IoT Hub tarafından alınan veriler Azure Stream Analytics ve Azure Blob depolama gibi çeşitli hizmetler tarafından tüketilebilir. Bu hizmetler, yönetim erişimine izin verir. Bu hizmetler ve kullanılabilir seçenekler hakkında daha fazla bilgi edinin:

* [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/): öznitelik, yapılandırma ve güvenlik özellikleri gibi sağladığınız cihazların meta verilerini yöneten yarı yapılandırılmış veriler için ölçeklenebilir, tam dizinli bir veritabanı hizmetidir. Azure Cosmos DB yüksek performanslı ve yüksek performanslı işleme, verilerin şemaya belirsiz dizin oluşturma ve zengin bir SQL sorgu arabirimi sunar.

* [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/): bulutta, algılayıcılardan, altyapıdan ve uygulamalardan gerçek zamanlı içgörüler elde etmek için hızlı bir şekilde düşük maliyetli bir analiz çözümü geliştirmenize ve dağıtmanıza olanak tanıyan gerçek zamanlı akış işleme. Bu tam olarak yönetilen hizmetin verileri, yüksek aktarım hızı, düşük gecikme süresi ve dayanıklılık sağlarken herhangi bir birime ölçeklendirebilir.

* [Azure Uygulama Hizmetleri](https://azure.microsoft.com/services/app-service/): her yerden verilere bağlanan güçlü web uygulamaları ve mobil uygulamalar oluşturmak için bir bulut platformudur; bulutta veya şirket içinde. iOS, Android ve Windows için ilgi çekici mobil uygulamalar oluşturun. Hizmet olarak yazılım (SaaS) ve kurumsal uygulamalarla, onlarca bulut tabanlı hizmetlere ve kurumsal uygulamalara yönelik kullanıma hazır bağlantı ile tümleştirin. Web uygulamaları ve API 'Leri her zamankinden daha hızlı derlemek için en sevdiğiniz dilde ve IDE 'de (.NET, Node. js, PHP, Python veya Java) kod.

* [Logic Apps](https://azure.microsoft.com/services/app-service/logic/): Azure App Service Logic Apps özelliği, IoT çözümünüzü mevcut iş kolu sistemlerinizle tümleştirmenize ve iş akışı süreçlerini otomatikleştirmenize yardımcı olur. Logic Apps, geliştiricilerin bir tetikleyiciden başlayan iş akışlarını tasarlamasına ve sonra iş süreçlerinizle tümleştirilecek güçlü bağlayıcılar kullanan kurallar ve eylemler yürütebilmesini sağlar. Logic Apps SaaS, bulut tabanlı ve şirket içi uygulamalarla büyük bir geniş ekosisteme kullanıma hazır bağlantı sunar.

* [Azure Blob depolama](https://azure.microsoft.com/services/storage/): cihazlarınızın buluta gönderdikleri veriler için güvenilir, ekonomik bulut depolama.

## <a name="conclusion"></a>Sonuç

Bu makalede, Azure IoT kullanarak IoT altyapısı tasarlamaya ve dağıtmaya yönelik uygulama düzeyi ayrıntılarına genel bakış sunulmaktadır. Her bileşeni güvenli olacak şekilde yapılandırmak, genel IoT altyapısının güvenliğini sağlamaya yönelik anahtardır. Azure IoT 'de kullanılabilen tasarım seçimleri, bazı esneklik ve seçim düzeyi sağlar; Ancak, her bir seçeneğin güvenlik etkileri olabilir. Bu seçimlerin her birinin risk/maliyet değerlendirmesi aracılığıyla değerlendirilmesi önerilir.
