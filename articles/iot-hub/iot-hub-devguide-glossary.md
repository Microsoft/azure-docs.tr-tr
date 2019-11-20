---
title: Azure IoT Hub koşulların sözlüğü | Microsoft Docs
description: Geliştirici Kılavuzu-Azure IoT Hub makalelerinde kullanılan bazı yaygın koşulları açıklayan bir sözlük.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/15/2019
ms.openlocfilehash: 6c4215933b3816b445c55282bae9a36e88c0311f
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73890536"
---
# <a name="glossary-of-iot-hub-terms"></a>IoT Hub terimlerin sözlüğü
Bu makalede IoT Hub makalelerinde kullanılan yaygın terimlerin bazıları listelenmektedir.

## <a name="advanced-message-queueing-protocol"></a>Gelişmiş Ileti kuyruğu Protokolü
[Gelişmiş Ileti sıraya alma Protokolü (AMQP)](https://www.amqp.org/) , cihazlarla iletişim kurmak için [IoT Hub](#iot-hub) desteklediği mesajlaşma protokollerinden biridir. IoT Hub desteklediği mesajlaşma protokolleri hakkında daha fazla bilgi için, bkz. [IoT Hub ile Ileti gönderme ve alma](iot-hub-devguide-messaging.md).

## <a name="automatic-device-management"></a>Otomatik cihaz yönetimi
Azure IoT Hub otomatik cihaz yönetimi, büyük cihaz filliklerini tümüyle yaşam döngülerine göre yönetmenin yinelenen ve karmaşık görevlerinin çoğunu otomatikleştirir. Otomatik cihaz yönetimi sayesinde, özelliklerini temel alarak bir cihaz kümesini hedefleyebilir, istenen yapılandırmayı tanımlayabilir ve IoT Hub cihazları kapsama geldiğinde güncelleştirme sağlayabilirsiniz.  [Otomatik cihaz yapılandırmalarının](iot-hub-auto-device-config.md) ve [IoT Edge otomatik dağıtımlarından](../iot-edge/how-to-deploy-monitor.md)oluşur.

## <a name="automatic-device-configuration"></a>Otomatik cihaz yapılandırması
Çözüm arka ucu, bir dizi [cihaz WINS](#device-twin) kümesine istenen özellikleri atamak ve sistem ölçümleri ve özel ölçümler kullanarak rapor durumu için [otomatik cihaz yapılandırması](iot-hub-auto-device-config.md) kullanabilir. 

## <a name="azure-classic-cli"></a>Azure klasik CLI
[Klasik Azure CLI](../cli-install-nodejs.md) , Microsoft Azure Kaynak oluşturmak ve yönetmek için platformlar arası, açık kaynaklı, kabuk tabanlı bir komut aracıdır. CLı 'nın bu sürümü yalnızca klasik dağıtımlar için kullanılmalıdır.

## <a name="azure-cli"></a>Azure CLI
[Azure CLI](https://docs.microsoft.com/cli/azure/install-az-cli2) , Microsoft Azure Kaynak oluşturmak ve yönetmek için platformlar arası, açık kaynaklı, kabuk tabanlı bir komut aracıdır.


## <a name="azure-iot-device-sdks"></a>Azure IoT cihaz SDK 'Ları
IoT Hub ile etkileşime geçen [cihaz uygulamaları](#device-app) oluşturmanızı sağlayan birden çok dil için kullanılabilir _cihaz SDK 'ları_ vardır. IoT Hub öğreticileri, bu cihaz SDK 'larını nasıl kullanacağınızı gösterir. Kaynak kodu ve bu GitHub [deposundaki](https://github.com/Azure/azure-iot-sdks)cihaz SDK 'ları hakkında daha fazla bilgi bulabilirsiniz.

## <a name="azure-iot-service-sdks"></a>Azure IoT hizmeti SDK 'Ları
Bir IoT Hub ile etkileşime geçen [arka uç uygulamaları](#back-end-app) oluşturmanızı sağlayan birden çok dil için kullanılabilir _hizmet SDK 'ları_ vardır. IoT Hub öğreticileri, bu hizmet SDK 'larını nasıl kullanacağınızı gösterir. Kaynak kodu ve bu GitHub [deposundaki](https://github.com/Azure/azure-iot-sdks)hizmet SDK 'ları hakkında daha fazla bilgi bulabilirsiniz.

## <a name="azure-iot-tools"></a>Azure IoT Araçları
[Azure IoT araçları](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) , VS Code Azure IoT Hub ve cihazlarını yönetmenize yardımcı olan platformlar arası, açık kaynaklı Visual Studio Code uzantısıdır. IoT geliştiricileri, Azure IoT araçlarıyla VS Code ' de kolayca IoT projesi geliştirebilir.

## <a name="azure-portal"></a>Azure portal
[Microsoft Azure Portal](https://portal.azure.com) , Azure kaynaklarınızı sağlayabileceğiniz ve yönetebileceğiniz merkezi bir yerdir. Dikey _pencereleri kullanarak içeriğini_düzenler.

## <a name="azure-powershell"></a>Azure PowerShell
[Azure PowerShell](/powershell/azure/overview) , Azure 'ı Windows PowerShell ile yönetmek için kullanabileceğiniz cmdlet 'lerin bir koleksiyonudur. Cmdlet 'lerini kullanarak Azure platformunda sunulan çözümleri ve Hizmetleri oluşturabilir, test edebilir, dağıtabilir ve yönetebilirsiniz.

## <a name="azure-resource-manager"></a>Azure Resource Manager
[Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) çözümünüzdeki kaynaklarla bir grup olarak çalışmanıza olanak sağlar. Çözümünüze yönelik kaynakları tek ve eşgüdümlü bir işlemle dağıtabilir, güncelleştirebilir veya silebilirsiniz.

## <a name="azure-service-bus"></a>Azure Service Bus
[Service Bus](../service-bus/index.md) , şirket içi çözümlere bulut ile bağlanmanıza yardımcı olan kurumsal mesajlaşma ve geçişli iletişim ile bulut özellikli iletişim sağlar. Bazı IoT Hub öğreticileri Service Bus [kuyrukları](../service-bus-messaging/service-bus-messaging-overview.md)kullanır.

## <a name="azure-storage"></a>Azure Storage
[Azure depolama](../storage/common/storage-introduction.md) , bulut depolama çözümüdür. Yapılandırılmamış nesne verilerini depolamak için kullanabileceğiniz blob Storage hizmetini içerir. Bazı IoT Hub öğreticileri blob Storage kullanır.

## <a name="back-end-app"></a>Arka uç uygulaması
[IoT Hub](#iot-hub)bağlamında, bir arka uç uygulaması, bir IoT Hub 'ındaki hizmete yönelik uç noktalardan birine bağlanan bir uygulamadır. Örneğin, bir arka uç uygulaması [cihazdan buluta](#device-to-cloud) iletileri alabilir veya [kimlik kayıt defterini](#identity-registry)yönetebilir. Genellikle, bir arka uç uygulaması bulutta çalışır, ancak arka uç uygulamalarının çoğu öğreticilerde yerel geliştirme makinenizde çalışan konsol uygulamaları vardır.

## <a name="built-in-endpoints"></a>Yerleşik uç noktalar
Her IoT Hub 'ı, Event hub ile uyumlu olan yerleşik bir [uç nokta](iot-hub-devguide-endpoints.md) içerir. Bu uç noktadan cihazdan buluta iletileri okumak için, Event Hubs ile birlikte çalışarak herhangi bir mekanizmayı kullanabilirsiniz.

## <a name="cloud-gateway"></a>Bulut ağ geçidi
Bulut ağ geçidi, [IoT Hub](#iot-hub)doğrudan bağlanamadan cihazlara bağlantı sağlar. Bir bulut ağ geçidi, kuruluşunuzda yerel olarak çalışan bir [alan ağ geçidine](#field-gateway) karşılık olarak bulutta barındırılır. Bir bulut ağ geçidi için tipik kullanım örneği, cihazlarınız için protokol çevirisi uygulamaktır.

## <a name="cloud-to-device"></a>Buluttan cihaza
IoT Hub 'ından bağlı bir cihaza gönderilen iletileri ifade eder. Genellikle, bu iletiler cihaza bir işlem yapması talimatını veren komutlardır. Daha fazla bilgi için bkz. [IoT Hub ile Ileti gönderme ve alma](iot-hub-devguide-messaging.md).

## <a name="configuration"></a>Yapılandırma
[Otomatik cihaz yapılandırması](iot-hub-auto-device-config.md)bağlamında IoT Hub içindeki bir yapılandırma, bir dizi cihaz için istenen yapılandırmayı tanımlar ve durum ve ilerlemeyi raporlamak için bir dizi ölçüm sağlar.

## <a name="connection-string"></a>Bağlantı dizesi
Bir uç noktaya bağlanmak için gereken bilgileri kapsüllemek için uygulama kodunuzda bağlantı dizeleri kullanırsınız. Bağlantı dizesi genellikle uç nokta ve güvenlik bilgilerinin adresini içerir, ancak bağlantı dizesi biçimleri hizmetler arasında farklılık gösterir. IoT Hub hizmetiyle ilişkili iki tür bağlantı dizesi vardır:
- *Cihaz bağlantı dizeleri* , cihazların bir IoT Hub 'ında cihaza yönelik uç noktalara bağlanmasını sağlar.
- *IoT Hub bağlantı dizeleri* , arka uç uygulamalarının bir IoT Hub 'ında hizmete yönelik uç noktalara bağlanmasını sağlar.

## <a name="custom-endpoints"></a>Özel uç noktalar
Bir [yönlendirme kuralı](#routing-rules)tarafından dağıtılan iletileri teslim etmek Için bir IoT Hub 'ında özel [uç noktalar](iot-hub-devguide-endpoints.md) oluşturabilirsiniz. Özel uç noktalar doğrudan bir olay hub 'ına, Service Bus kuyruğuna veya bir Service Bus konusuna bağlanır.

## <a name="custom-gateway"></a>Özel ağ geçidi
Ağ Geçidi, [IoT Hub](#iot-hub)doğrudan bağlanamadan cihazlara bağlantı sağlar. İletileri, özel protokol dönüştürmelerini ve kenardaki diğer işlemleri işlemek için özel mantık uygulayan özel ağ geçitleri oluşturmak üzere Azure IoT Edge kullanabilirsiniz.

## <a name="data-point-message"></a>Veri noktası iletisi
Veri noktası iletisi, Rüzgar hızı veya sıcaklık gibi [telemetri](#telemetry) verilerini içeren [cihazdan buluta](#device-to-cloud) iletidir.

## <a name="desired-configuration"></a>İstenen yapılandırma
Bir [cihaz ikizi](iot-hub-devguide-device-twins.md)bağlamında, istenen yapılandırma cihaz ikizi cihaz ile eşitlenmesi gereken tüm özellikler ve meta veriler kümesini ifade eder.

## <a name="desired-properties"></a>İstenen özellikler
Bir [cihaz ikizi](iot-hub-devguide-device-twins.md)bağlamında, istenen özellikler cihaz ikizi 'nin cihaz yapılandırmasını veya koşulunu eşitlemeye yönelik [bildirilen özelliklerle](#reported-properties) birlikte kullanılan bir alt bölümü olur. İstenen özellikler yalnızca bir [arka uç uygulaması](#back-end-app) tarafından ayarlanabilir ve [cihaz uygulaması](#device-app)tarafından gözlemlenebilir.

## <a name="device-to-cloud"></a>Cihazdan buluta
Bağlı bir cihazdan [IoT Hub](#iot-hub)gönderilen iletileri ifade eder. Bu iletiler [veri noktası](#data-point-message) veya [etkileşimli](#interactive-message) iletiler olabilir. Daha fazla bilgi için bkz. [IoT Hub ile Ileti gönderme ve alma](iot-hub-devguide-messaging.md).

## <a name="device"></a>Cihaz
IoT bağlamında, bir cihaz genellikle veri toplayabilen veya diğer cihazları denetleyebilen Küçük ölçekli, tek başına bilgi işlem cihazıdır. Örneğin, bir cihaz bir çevre izleme aygıtı ya da bir ortamla ilgili bir ortam ve havalandırma sistemleri için bir denetleyici olabilir. [Cihaz Kataloğu](https://catalog.azureiotsolutions.com/) , [IoT Hub](#iot-hub)ile çalışmak üzere sertifikalı donanım cihazlarının bir listesini sağlar.

## <a name="device-app"></a>Cihaz uygulaması
Cihaz uygulaması [cihazınızda](#device) çalışır ve [IoT Hub](#iot-hub)'ınızla iletişimi işler. Genellikle, bir cihaz uygulaması uyguladığınızda [Azure IoT cihaz SDK](#azure-iot-device-sdks) 'larından birini kullanırsınız. Birçok IoT öğreticilerinde, kolaylık sağlaması için sanal bir [cihaz](#simulated-device) kullanırsınız.

## <a name="device-condition"></a>Cihaz koşulu
Bir [cihaz uygulaması](#device-app)tarafından raporlanarak, kullanılmakta olan bağlantı yöntemi gibi cihaz durum bilgilerini gösterir. [Cihaz uygulamaları](#device-app) , yeteneklerini de bildirebilir. Cihaz ikizlerini kullanarak koşul ve yetenek bilgileri için sorgulama yapabilirsiniz.

## <a name="device-data"></a>Cihaz verileri
Cihaz verileri IoT Hub [kimlik kayıt defterinde](#identity-registry)depolanan cihaz başına verileri ifade eder. Bu verileri içeri ve dışarı aktarmak mümkündür.

## <a name="device-explorer"></a>Device Explorer
[Cihaz Gezgini](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) , Windows üzerinde çalışan ve [kimlik kayıt defterinde](#identity-registry)cihazlarınızı yönetmenizi sağlayan bir araçtır. Araç ayrıca cihazlarınıza ileti gönderebilir ve alabilir.

## <a name="device-identity"></a>Cihaz kimliği
Cihaz kimliği, [kimlik kayıt defterine](#identity-registry)kayıtlı olan her cihaza atanan benzersiz tanıtıcıdır.

## <a name="device-management"></a>Cihaz yönetimi
Cihaz yönetimi, IoT çözümünüzdeki planlama, sağlama, yapılandırma, izleme ve devre dışı bırakma dahil cihazların yönetimiyle ilişkili tam yaşam döngüsünü kapsar.

## <a name="device-management-patterns"></a>Cihaz yönetimi modelleri
[IoT Hub](#iot-hub) 'ı yeniden başlatma, fabrika sıfırlamaları gerçekleştirme ve cihazlarınızda bellenim güncelleştirmelerini gerçekleştirme dahil ortak cihaz yönetimi düzenlerini mümkün.

## <a name="device-rest-api"></a>Cihaz REST API
Bir IoT Hub 'ına cihazdan buluta iletiler göndermek ve bir IoT Hub 'ından [buluttan cihaza](#cloud-to-device) iletileri almak için bir cihazdan cihaz [REST API](https://docs.microsoft.com/rest/api/iothub/device) kullanabilirsiniz. Genellikle, IoT Hub öğreticilerde gösterildiği gibi daha yüksek düzey [cihaz SDK 'lardan](#azure-iot-device-sdks) birini kullanmanız gerekir.

## <a name="device-provisioning"></a>Cihaz sağlama
Cihaz sağlama, ilk [Cihaz verilerini](#device-data) çözümünüzdeki depolara ekleme işlemidir. Yeni bir cihazın hub 'ınıza bağlanmasını sağlamak için IoT Hub [kimlik kayıt defterine](#identity-registry)BIR cihaz kimliği ve anahtarı eklemeniz gerekir. Sağlama sürecinin bir parçası olarak, diğer çözüm depolarında cihaza özgü verileri de başlatmak gerekebilir.

## <a name="device-twin"></a>Cihaz çifti
Bir [cihaz ikizi](iot-hub-devguide-device-twins.md) , meta veriler, konfigürasyonlar ve koşullar gibi cihaz durum BILGILERINI depolayan JSON belgesidir. [IoT Hub](#iot-hub) , IoT Hub 'ınızda sağladığınız her cihaz için bir cihaz ikizi devam ettirir. Cihaz TWINS, cihaz ve çözüm arka ucu arasındaki [cihaz koşullarını](#device-condition) ve konfigürasyonları eşitlemenize olanak tanır. Belirli cihazları bulmak ve uzun süre çalışan işlemlerin durumunu sorgulamak için cihaz iklerini sorgulayabilirsiniz.

## <a name="direct-method"></a>Direct yöntemi
[Doğrudan yöntem](iot-hub-devguide-direct-methods.md) , IoT Hub 'ıNıZDA bir API çağırarak bir cihazdaki bir yöntemi yürütmek için bir yöntem tetiklemeniz için bir yoldur.

## <a name="endpoint"></a>Uç Nokta
IoT Hub 'ı, uygulamalarınızın IoT Hub 'ına bağlanmasını sağlayan birden çok [uç nokta](iot-hub-devguide-endpoints.md) sunar. Cihazların, [cihazdan buluta](#device-to-cloud) iletileri gönderme ve [buluttan cihaza](#cloud-to-device) iletileri alma gibi işlemleri gerçekleştirmesini sağlayan cihaza yönelik uç noktalar vardır. [Arka uç uygulamalarının](#back-end-app) [cihaz kimlik](#device-identity) yönetimi ve cihaz ikizi yönetimi gibi işlemleri gerçekleştirmesini sağlayan hizmete yönelik yönetim uç noktaları vardır. Cihazdan buluta iletileri okumak için hizmete yönelik [yerleşik uç noktalar](#built-in-endpoints) vardır. Bir [yönlendirme kuralı](#routing-rules)tarafından dağıtılan cihazdan buluta iletileri almak için [Özel uç noktalar](#custom-endpoints) oluşturabilirsiniz.

## <a name="event-hubs-service"></a>Event Hubs hizmeti
[Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md) , saniyede milyonlarca olayı alabilen, yüksek oranda ölçeklenebilir bir veri giriş hizmetidir. Hizmet, bağlı cihazlarınız ve uygulamalarınız tarafından üretilen büyük miktarda verileri işlemenizi ve çözümlemenize olanak sağlar. IoT Hub hizmetiyle bir karşılaştırma için bkz. [azure IoT Hub ve azure Event Hubs karşılaştırması](iot-hub-compare-event-hubs.md).

## <a name="event-hub-compatible-endpoint"></a>Olay Hub 'ı ile uyumlu uç nokta
IoT Hub 'ınıza gönderilen [cihazdan buluta](#device-to-cloud) iletileri okumak için, hub 'ınızdaki bir uç noktaya bağlanabilir ve bu iletileri okumak Için Event hub ile uyumlu herhangi bir yöntemi kullanabilirsiniz. Event hub ile uyumlu yöntemler [Event Hubs SDK 'ları](../event-hubs/event-hubs-programming-guide.md) ve [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md)kullanmayı içerir.

## <a name="field-gateway"></a>Alan ağ geçidi
Alan ağ geçidi, [IoT Hub](#iot-hub) doğrudan bağlanamamakta olan ve genellikle cihazlarınızla yerel olarak dağıtılan cihazlara bağlantı sağlar. Daha fazla bilgi için bkz. [Azure IoT Hub nedir?](about-iot-hub.md)

## <a name="free-account"></a>Ücretsiz hesap
IoT Hub öğreticileri tamamlamaya yönelik [ücretsiz bir Azure hesabı](https://azure.microsoft.com/pricing/free-trial/) oluşturabilir ve IoT Hub hizmeti (ve diğer Azure Hizmetleri) ile denemeler yapabilirsiniz.

## <a name="gateway"></a>Ağ geçidi
Ağ Geçidi, [IoT Hub](#iot-hub)doğrudan bağlanamadan cihazlara bağlantı sağlar. Ayrıca bkz. [alan ağ geçidi](#field-gateway), [bulut ağ](#cloud-gateway)geçidi ve [özel ağ geçidi](#custom-gateway).

## <a name="identity-registry"></a>Kimlik kayıt defteri
[Kimlik kayıt defteri](iot-hub-devguide-identity-registry.md) , IoT Hub 'ına bağlanmasına izin verilen ayrı cihazlarla ilgili bilgileri depolayan bir IoT Hub 'ın yerleşik bileşenidir.

## <a name="interactive-message"></a>Etkileşimli ileti
Etkileşimli ileti, çözüm arka ucunda acil bir eylemi tetikleyen bir [buluttan cihaza](#cloud-to-device) iletidir. Örneğin, bir cihaz bir CRM sisteminde otomatik olarak oturum açmış olması gereken bir hata hakkında alarm gönderebilir.

[!INCLUDE [azure-iot-hub-edge-glossary-includes](../../includes/azure-iot-hub-edge-glossary-includes.md)]

## <a name="iot-hub"></a>IoT Hub’ı
IoT Hub, milyonlarca cihaz ile bir çözüm arka ucu arasında güvenilir ve güvenli çift yönlü iletişimler sağlayan, tam olarak yönetilen bir Azure hizmetidir. Daha fazla bilgi için bkz. [Azure IoT Hub nedir?](about-iot-hub.md) [Azure aboneliğinizi](#subscription)kullanarak IoT mesajlaşma iş yüklerinizi idare etmek için IoT Hub 'ları oluşturabilirsiniz.

## <a name="iot-hub-metrics"></a>IoT Hub ölçümleri
[IoT Hub ölçümler](iot-hub-metrics.md) , [Azure aboneliğinizdeki](#subscription)IoT Hub 'larının durumu hakkında size veri sağlar. IoT Hub ölçümler, hizmetin ve ona bağlı cihazların genel sistem durumunu değerlendirmenizi sağlar. IoT Hub ölçümler, IoT Hub 'ınız ile neler olduğunu görmenizi ve Azure desteği ile iletişim kurmaya gerek kalmadan kök neden sorunlarını araştırmanıza yardımcı olabilir.

## <a name="iot-hub-query-language"></a>IoT Hub sorgu dili
[IoT Hub sorgu dili](iot-hub-devguide-query-language.md) , ve cihaz iklerinizi [](#job) SORGULAMANıZı sağlayan bir SQL benzeri dildir.

## <a name="iot-hub-resource-rest-api"></a>Kaynak REST API IoT Hub
Hub oluşturma, güncelleştirme ve silme gibi işlemleri gerçekleştirmek için [Azure aboneliğinizdeki](#subscription) IoT Hub 'larını yönetmek üzere [IoT Hub kaynak REST API](https://docs.microsoft.com/rest/api/iothub/iothubresource) kullanabilirsiniz.

## <a name="iot-solution-accelerators"></a>IoT çözüm hızlandırıcıları
Azure IoT Çözüm Hızlandırıcıları, çözümlerle birden çok Azure hizmetini birlikte paketleyebilir. Bu çözümler, yaygın IoT senaryolarına yönelik uçtan uca uygulamalarla hızla başlamanıza olanak sağlar. Daha fazla bilgi için bkz. [Azure IoT Çözüm Hızlandırıcıları nelerdir?](../iot-accelerators/about-iot-accelerators.md)

## <a name="the-iot-extension-for-azure-cli"></a>Azure CLı için IoT uzantısı 
[Azure CLI Için IoT uzantısı](https://github.com/Azure/azure-iot-cli-extension) , platformlar arası bir komut satırı aracıdır. Araç, [kimlik kayıt defterinde](#identity-registry)cihazlarınızı yönetmenizi, cihazlarınızdaki iletileri ve dosyaları göndermenizi ve almanızı ve IoT Hub işlemlerinizi izlemenizi sağlar.

## <a name="job"></a>İş
Çözüm arka ucu, IoT Hub 'ınıza kayıtlı bir dizi cihazda etkinlikleri zamanlamak ve izlemek için [işleri](iot-hub-devguide-jobs.md) kullanabilir. Etkinlikler, cihaz ikizi [istenen özellikleri](#desired-properties)güncelleştirmek, Device ikizi [etiketlerini](#tags)güncelleştirmek ve [doğrudan yöntemleri](#direct-method)çağırmak içerir. [IoT Hub](#iot-hub) , [kimlik kayıt defterinden](#identity-registry) [içeri ve dışarı aktarmak](iot-hub-devguide-identity-registry.md#import-and-export-device-identities) için de kullanılır.

## <a name="modules"></a>Modüller
Cihaz tarafında IoT Hub cihaz SDK 'Ları, her birinin IoT Hub için bağımsız bir bağlantı açtığı [modüller](iot-hub-devguide-module-twins.md) oluşturmanızı sağlar. Bu işlevsellik, cihazınızdaki farklı bileşenler için ayrı ad alanları kullanmanıza olanak sağlar.

Modül kimliği ve modül ikizi, [cihaz kimliği](#device-identity) ve [cihaz ikizi](#device-twin) ile aynı özellikleri sağlar, ancak daha ayrıntılı bir ayrıntı düzeyine sahiptir. Bu daha ayrıntılı ayrıntı düzeyi, bu bileşenlerin her biri için yapılandırma ve koşulları yalıtmak amacıyla, işletim sistemi tabanlı cihazlar veya birden çok bileşeni yöneten bellenim cihazları gibi özellikli cihazların kullanılmasını sağlar.

## <a name="module-identity"></a>Modül kimliği
Modül kimliği, bir cihaza ait olan her modüle atanan benzersiz tanıtıcıdır. Modül kimliği [kimlik kayıt defterine](#identity-registry)de kaydedilir.

## <a name="module-twin"></a>Modül ikizi
Cihaz ikizi benzer bir modül ikizi, meta veriler, konfigürasyonlar ve koşullar gibi modül durum bilgilerini depolayan JSON belgesidir. IoT Hub, IoT Hub 'ınızdaki bir cihaz kimliği altında sağladığınız her modül kimliği için bir modül ikizi devam ettirir. Modül TWINS, modül ve çözüm arka ucu arasındaki modül koşullarını ve konfigürasyonları eşitlemenize olanak tanır. Belirli modülleri bulmak ve uzun süreli işlemlerin durumunu sorgulamak için modül TWINS sorgulama yapabilirsiniz.

## <a name="mqtt"></a>MQTT
[MQTT](https://mqtt.org/) , cihazlarla iletişim kurmak için [IoT Hub](#iot-hub) desteklediği mesajlaşma protokollerinden biridir. IoT Hub desteklediği mesajlaşma protokolleri hakkında daha fazla bilgi için, bkz. [IoT Hub ile Ileti gönderme ve alma](iot-hub-devguide-messaging.md).

## <a name="operations-monitoring"></a>İşlemleri izleme
IoT Hub [işlemler izleme](iot-hub-operations-monitoring.md) , IoT Hub 'ınızdaki işlemlerin durumunu gerçek zamanlı olarak izlemenize olanak sağlar. [IoT Hub](#iot-hub) birkaç işlem kategorisi içindeki olayları izler. İşlenmek üzere bir veya daha fazla kategoriden bir IoT Hub uç noktasına olay göndermeyi tercih edebilirsiniz. Verileri hatalara göre izleyebilir veya veri desenlerine göre daha karmaşık işleme ayarlayabilirsiniz.

## <a name="physical-device"></a>Fiziksel cihaz
Fiziksel bir cihaz, bir IoT Hub 'ına bağlanan Raspberry PI gibi gerçek bir cihazdır. Kolaylık sağlaması için IoT Hub öğreticilerinin birçoğu, yerel makinenizde örnek çalıştırmanızı sağlamak üzere [sanal cihazları](#simulated-device) kullanır.

## <a name="primary-and-secondary-keys"></a>Birincil ve ikincil anahtarlar
Bir IoT Hub 'ında cihaza yönelik veya hizmete yönelik bir uç noktaya bağlandığınızda, [bağlantı dizeniz](#connection-string) size erişim sağlamak için anahtar içerir. [Kimlik kayıt defterine](#identity-registry) bir cihaz eklediğinizde veya hub 'ınıza [paylaşılan bir erişim ilkesi](#shared-access-policy) eklediğinizde hizmet bir birincil ve ikincil anahtar oluşturur. İki anahtara sahip olmak, IoT Hub 'ına erişimi kaybetmeksizin bir anahtarı güncelleştirdiğinizde bir anahtardan diğerine geri alma olanağı sağlar.

## <a name="protocol-gateway"></a>Protokol ağ geçidi
Bir protokol ağ geçidi genellikle buluta dağıtılır ve [IoT Hub](#iot-hub)bağlanan cihazlar için protokol çevirisi hizmetleri sağlar. Daha fazla bilgi için bkz. [Azure IoT Hub nedir?](about-iot-hub.md)

## <a name="quotas-and-throttling"></a>Kotalar ve azaltma
[IoT Hub](#iot-hub)kullanımı için uygulanan çeşitli [Kotalar](iot-hub-devguide-quotas-throttling.md) vardır. çoğu kota, IoT Hub 'ın katmanına göre farklılık gösterir. [IoT Hub](#iot-hub) Ayrıca, çalışma zamanında hizmeti kullanmanıza yönelik [Azaltıcı](iot-hub-devguide-quotas-throttling.md) lıkları uygular.

## <a name="reported-configuration"></a>Bildirilen yapılandırma
Bir [cihaz ikizi](iot-hub-devguide-device-twins.md)bağlamında, bildirilen yapılandırma cihaz ikizi ' de çözüm arka ucuna bildirilmesi gereken tüm özellik ve meta veri kümesini ifade eder.

## <a name="reported-properties"></a>Bildirilen özellikler
Bir [cihaz ikizi](iot-hub-devguide-device-twins.md)bağlamında, bildirilen özellikler cihaz yapılandırması veya koşulu eşitlenmesi için [istenen özelliklerle](#desired-properties) kullanılan cihaz ikizi 'nin bir alt bölümü olur. Bildirilen özellikler yalnızca [cihaz uygulaması](#device-app) tarafından ayarlanabilir, bir [arka uç uygulaması](#back-end-app)tarafından okunabilir ve sorgulanabilir.

## <a name="resource-group"></a>Kaynak grubu
[Azure Resource Manager](#azure-resource-manager) , ilgili kaynakları gruplamak için kaynak gruplarını kullanır. Gruptaki tüm kaynaklar üzerinde aynı anda işlem gerçekleştirmek için bir kaynak grubu kullanabilirsiniz.

## <a name="retry-policy"></a>Yeniden deneme ilkesi
Bir bulut hizmetine bağlandığınızda [geçici hataları](/azure/architecture/best-practices/transient-faults) işlemek için bir yeniden deneme ilkesi kullanırsınız.

## <a name="routing-rules"></a>Yönlendirme kuralları
Cihaz-bulut iletilerini [yerleşik bir uç noktaya](#built-in-endpoints) veya çözüm arka uçınızdan işlemek için [Özel uç noktalara](#custom-endpoints) yönlendirmek üzere IoT Hub 'ınızdaki [yönlendirme kurallarını](iot-hub-devguide-messages-read-custom.md) yapılandırırsınız.

## <a name="sasl-plain"></a>SASL PLAIN
SASL PLAIN, AMQP protokolünün güvenlik belirteçlerini aktarmak için kullandığı bir protokoldür.

## <a name="service-rest-api"></a>Hizmet REST API'si
Cihazlarınızı yönetmek için çözüm arka ucundan [hizmet REST API](https://docs.microsoft.com/rest/api/iothub/service) kullanabilirsiniz. API, [cihaz ikizi](#device-twin) özelliklerini alıp güncelleştirmenizi, [doğrudan Yöntemler](#direct-method)çağırmayı ve [işleri](#job)zamanlamayı sağlar. Genellikle, IoT Hub öğreticilerde gösterildiği gibi daha yüksek düzey [hizmet SDK 'lardan](#azure-iot-service-sdks) birini kullanmanız gerekir.

## <a name="shared-access-signature"></a>Paylaşılan erişim imzası
Paylaşılan erişim Imzaları (SAS), SHA-256 güvenli karmaları veya URI 'Leri temel alan bir kimlik doğrulama mekanizmasıdır. SAS kimlik doğrulamasının iki bileşeni vardır: _paylaşılan erişim ilkesi_ ve _paylaşılan erişim imzası_ (genellikle belirteç olarak adlandırılır). Bir cihaz, IoT Hub ile kimlik doğrulamak için SAS kullanır. [Arka uç uygulamalar](#back-end-app) , bir IoT Hub 'ında hizmete yönelik uç noktalarla kimlik doğrulaması yapmak için SAS kullanır. Genellikle, bir uygulamanın bir IoT Hub 'ına bağlantı kurmak için kullandığı [bağlantı DIZESINE](#connection-string) SAS belirtecini dahil edersiniz.

## <a name="shared-access-policy"></a>Paylaşılan erişim ilkesi
Paylaşılan erişim ilkesi, ilgili ilkeyle ilişkilendirilmiş geçerli bir [birincil veya ikincil anahtarı](#primary-and-secondary-keys) olan herkese verilen izinleri tanımlar. [Portalda](#azure-portal)hub 'ınız için paylaşılan erişim ilkelerini ve anahtarlarını yönetebilirsiniz.

## <a name="simulated-device"></a>Sanal cihaz
Kolaylık sağlaması için IoT Hub öğreticilerinin birçoğu, yerel makinenizde örnek çalıştırmanızı sağlamak üzere sanal cihazları kullanır. Buna karşılık, [fiziksel cihaz](#physical-device) , IoT Hub 'ına bağlanan Raspberry Pi gibi gerçek bir cihazdır.

## <a name="solution"></a>Çözüm
Bir _çözüm_ , bir veya daha fazla proje Içeren bir Visual Studio çözümüne başvurabilir. Bir _çözüm_ Ayrıca cihazlar, [cihaz uygulamaları](#device-app), IoT Hub, diğer Azure hizmetleri ve [arka uç uygulamaları](#back-end-app)gibi öğeleri içeren bir IoT çözümüne de başvurabilir.

## <a name="subscription"></a>Abonelik
Azure aboneliği, faturalandırma 'nin gerçekleştiği yerdir. Oluşturduğunuz her Azure kaynağı veya kullandığınız Azure hizmeti tek bir abonelikle ilişkilendirilir. Birçok kota de bir abonelik düzeyinde geçerlidir.

## <a name="system-properties"></a>Sistem özellikleri
Bir [cihaz ikizi](iot-hub-devguide-device-twins.md)bağlamında Sistem özellikleri salt okunurdur ve cihaz kullanımı ile ilgili son etkinlik zamanı ve bağlantı durumu gibi bilgileri içerir.

## <a name="tags"></a>Etiketler
Bir [cihaz ikizi](iot-hub-devguide-device-twins.md)bağlamında Etiketler, bir JSON belgesi biçiminde, çözüm arka ucu tarafından depolanan ve alınan cihaz meta verilerlerdir. Etiketler bir cihazdaki uygulamalara görünür değildir.

## <a name="telemetry"></a>Telemetri
Cihazlar, Rüzgar hızı veya sıcaklık gibi telemetri verileri toplar ve Telemetriyi bir IoT Hub 'ına göndermek için veri noktası iletilerini kullanır.

## <a name="token-service"></a>Belirteç hizmeti
Cihazlarınız için bir kimlik doğrulama mekanizması uygulamak üzere bir belirteç hizmeti kullanabilirsiniz. *Cihaz kapsamlı* belirteçler oluşturmak Için **deviceconnect** izinleriyle IoT Hub [paylaşılan erişim ilkesi](#shared-access-policy) kullanır. Bu belirteçler, bir cihazın IoT Hub 'ınıza bağlanmasını sağlar. Bir cihaz, belirteç hizmeti ile kimlik doğrulamak için özel bir kimlik doğrulama mekanizması kullanır. Cihaz başarıyla kimlik doğrulaması gerçekleştiriyorsa, belirteç hizmeti cihazın IoT Hub 'ınıza erişmek için kullanacağı bir SAS belirteci yayınlar.

## <a name="twin-queries"></a>İkizi sorguları
[Cihaz ve modül ikizi sorguları](iot-hub-devguide-query-language.md) , cihaz ikiklerine veya modül TWINS 'inizden bilgi almak için SQL benzeri IoT Hub sorgu dilini kullanır. IoT Hub 'ınızda çalışan hakkında [](#job) bilgi almak için aynı IoT Hub sorgu dilini kullanabilirsiniz.

## <a name="twin-synchronization"></a>İkizi eşitleme
İkizi eşitleme, cihazlarınızı veya modüllerinizi yapılandırmak ve [bildirilen özellikleri](#reported-properties) ikizi içinde depolanacak şekilde almak için, cihazlarınızdaki veya modül TWINS 'de [istenen özellikleri](#desired-properties) kullanır.

## <a name="x509-client-certificate"></a>X. 509.440 istemci sertifikası
Bir cihaz, [IoT Hub](#iot-hub)kimlik doğrulaması yapmak Için bir X. 509.440 sertifikası kullanabilir. X. 509.440 sertifikası kullanmak [SAS belirteci](#shared-access-signature)kullanmanın bir alternatifidir.
