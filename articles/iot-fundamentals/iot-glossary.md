---
title: Azure IoT sözlüğü terimleri | Microsoft Docs
description: Geliştirici Kılavuzu-Azure IoT makalelerinde kullanılan bazı yaygın koşulları açıklayan bir sözlük.
author: dominicbetts
ms.author: dobett
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/08/2021
ms.openlocfilehash: a45b8db1f17ead8e55bac2b2bf82aa44cb7f080e
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107304723"
---
# <a name="glossary-of-iot-terms"></a>IoT terimleri sözlüğü

Bu makalede, IoT makalelerinde kullanılan yaygın terimlerin bazıları listelenir.

## <a name="a"></a>A

### <a name="advanced-message-queueing-protocol"></a>Gelişmiş Ileti kuyruğu Protokolü

[Gelişmiş Ileti sıraya alma Protokolü (AMQP)](https://www.amqp.org/) , cihazlarla iletişim kurmak için [IoT Hub](#iot-hub) desteklediği mesajlaşma protokollerinden biridir. IoT Hub desteklediği mesajlaşma protokolleri hakkında daha fazla bilgi için, bkz. [IoT Hub ile Ileti gönderme ve alma](../iot-hub/iot-hub-devguide-messaging.md).

### <a name="allocation-policy"></a>Ayırma ilkesi

[Cihaz sağlama hizmetinde](#device-provisioning-service), ayırma ilkesi hizmetin cihazları [bağlantılı IoT Hub 'larına](#linked-iot-hub)nasıl atayabileceğini belirler.

### <a name="attestation-mechanism"></a>Kanıtlama mekanizması

[Cihaz sağlama hizmetinde](#device-provisioning-service), kanıtlama mekanizması bir cihazın kimliğini onaylamak için kullanılan yöntemdir. Kanıtlama mekanizması bir [kayıt](#enrollment)üzerinde yapılandırılır.

Kanıtlama mekanizmaları arasında X. 509.440 sertifikaları, güvenilir platform modülleri ve simetrik anahtarlar bulunur.

### <a name="automatic-deployment"></a>Otomatik dağıtım

IoT Edge, otomatik dağıtım, bir IoT Edge modülleri kümesini çalıştırmak için hedef IoT Edge cihazları kümesini yapılandırır. Her dağıtım, yeni cihazlar oluşturulduğunda veya hedef koşulla eşleşecek şekilde değiştirilse bile, hedef durumuyla eşleşen tüm cihazların belirtilen modül kümesini çalıştırdığından sürekli olarak emin olur. Her IoT Edge cihaz yalnızca hedef koşulunu karşılayan en yüksek öncelikli dağıtımı alır. [IoT Edge otomatik dağıtım](../iot-edge/module-deployment-monitoring.md)hakkında daha fazla bilgi edinin.

### <a name="automatic-device-configuration"></a>Otomatik cihaz yapılandırması

Çözüm arka ucu, bir dizi [cihaz WINS](#device-twin) kümesine istenen özellikleri atamak ve sistem ölçümleri ve özel ölçümler kullanarak rapor durumu için [otomatik cihaz yapılandırması](../iot-hub/iot-hub-automatic-device-management.md) kullanabilir.

### <a name="automatic-device-management"></a>Otomatik cihaz yönetimi

Azure IoT Hub otomatik cihaz yönetimi, büyük cihaz filliklerini tümüyle yaşam döngülerine göre yönetmenin yinelenen ve karmaşık görevlerinin çoğunu otomatikleştirir. Otomatik cihaz yönetimi sayesinde, özelliklerini temel alarak bir cihaz kümesini hedefleyebilir, istenen yapılandırmayı tanımlayabilir ve IoT Hub cihazları kapsama geldiğinde güncelleştirme sağlayabilirsiniz.  [Otomatik cihaz yapılandırmalarının](../iot-hub/iot-hub-automatic-device-management.md) ve [IoT Edge otomatik dağıtımlarından](../iot-edge/how-to-deploy-at-scale.md)oluşur.

### <a name="azure-digital-twins"></a>Azure Digital Twins

Azure dijital TWINS, gerçek dünyada nesnelerin, yerlerin, iş süreçlerinin ve kişilerin dijital sunumlarını oluşturmaya yönelik bir hizmet olarak platform (PaaS) sunudur. Tüm ortamları temsil eden bilgi grafikleri oluşturun ve bunları kullanarak daha iyi ürünlerin yanı sıra işlemleri ve maliyetleri iyileştirin, daha fazla müşteri deneyimi oluşturun. Daha fazla bilgi için bkz. [Azure dijital TWINS](../digital-twins/index.yml).

### <a name="azure-digital-twins-instance"></a>Azure dijital TWINS örneği

Bir müşterinin aboneliğindeki Azure dijital TWINS hizmetinin tek bir örneği. Azure [dijital TWINS](#azure-digital-twins) , Azure hizmetine bir bütün olarak başvurduğunda, Azure dijital TWINS **örneğiniz** tek bir Azure dijital TWINS kaynağıdır.

### <a name="azure-iot-device-sdks"></a>Azure IoT cihaz SDK 'Ları

IoT Hub ile etkileşime geçen [cihaz uygulamaları](#device-app) oluşturmanızı sağlayan birden çok dil için kullanılabilir _cihaz SDK 'ları_ vardır. IoT Hub öğreticileri, bu cihaz SDK 'larını nasıl kullanacağınızı gösterir. Kaynak kodu ve bu GitHub [deposundaki](https://github.com/Azure/azure-iot-sdks)cihaz SDK 'ları hakkında daha fazla bilgi bulabilirsiniz.

### <a name="azure-iot-explorer"></a>Azure IoT Gezgini

[Azure IoT Gezgini](https://github.com/Azure/azure-iot-explorer) , cihazın gönderdiği Telemetriyi görüntülemek, cihaz özellikleriyle çalışmak ve komutları çağırmak için kullanılır. Ayrıca, cihazlarınızı etkileşmek ve test etmek ve [ıot Tak ve kullan cihazlarını](#iot-plug-and-play-device)yönetmek için de Gezginini kullanabilirsiniz.

### <a name="azure-iot-service-sdks"></a>Azure IoT hizmeti SDK 'Ları

Bir IoT Hub ile etkileşime geçen [arka uç uygulamaları](#back-end-app) oluşturmanızı sağlayan birden çok dil için kullanılabilir _hizmet SDK 'ları_ vardır. IoT Hub öğreticileri, bu hizmet SDK 'larını nasıl kullanacağınızı gösterir. Kaynak kodu ve bu GitHub [deposundaki](https://github.com/Azure/azure-iot-sdks)hizmet SDK 'ları hakkında daha fazla bilgi bulabilirsiniz.

### <a name="azure-iot-tools"></a>Azure IoT Araçları

[Azure IoT araçları](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) , VS Code Azure IoT Hub ve cihazlarını yönetmenize yardımcı olan platformlar arası, açık kaynaklı Visual Studio Code uzantısıdır. IoT geliştiricileri, Azure IoT araçlarıyla VS Code ' de kolayca IoT projesi geliştirebilir.

## <a name="b"></a>B

### <a name="back-end-app"></a>Arka uç uygulaması

[IoT Hub](#iot-hub)bağlamında, bir arka uç uygulaması, bir IoT Hub 'ındaki hizmete yönelik uç noktalardan birine bağlanan bir uygulamadır. Örneğin, bir arka uç uygulaması [cihazdan buluta](#device-to-cloud) iletileri alabilir veya [kimlik kayıt defterini](#identity-registry)yönetebilir. Genellikle, bir arka uç uygulaması bulutta çalışır, ancak arka uç uygulamalarının çoğu öğreticilerde yerel geliştirme makinenizde çalışan konsol uygulamaları vardır.

### <a name="built-in-endpoints"></a>Yerleşik uç noktalar

IoT Hub yerleşik bir [uç nokta](#endpoint) türü. Her IoT Hub 'ı, Event hub ile uyumlu olan yerleşik bir [uç nokta](../iot-hub/iot-hub-devguide-endpoints.md) içerir. Bu uç noktadan cihazdan buluta iletileri okumak için, Event Hubs ile birlikte çalışarak herhangi bir mekanizmayı kullanabilirsiniz.

## <a name="c"></a>C

### <a name="cloud-gateway"></a>Bulut ağ geçidi

Bulut ağ geçidi, [IoT Hub](#iot-hub)doğrudan bağlanamadan cihazlara bağlantı sağlar. Bir bulut ağ geçidi, kuruluşunuzda yerel olarak çalışan bir [alan ağ geçidine](#field-gateway) karşılık olarak bulutta barındırılır. Bir bulut ağ geçidi için tipik kullanım örneği, cihazlarınız için protokol çevirisi uygulamaktır.

### <a name="cloud-to-device"></a>Buluttan cihaza

IoT Hub 'ından bağlı bir cihaza gönderilen iletileri ifade eder. Genellikle, bu iletiler cihaza bir işlem yapması talimatını veren komutlardır. Daha fazla bilgi için bkz. [IoT Hub ile Ileti gönderme ve alma](../iot-hub/iot-hub-devguide-messaging.md).

### <a name="commands"></a>Komutlar

IoT Tak ve Kullan, bir [arabirimde](#interface) tanımlanan komutlar [dijital ikizi](#digital-twin)yürütülebilecek yöntemleri temsil eder. Örneğin, bir cihazı yeniden başlatmak için bir komut.

### <a name="component"></a>Bileşen

IoT Tak ve Kullan ve Azure dijital TWINS 'de, bileşenler diğer arabirimlerin derlemesi olarak bir model [arabirimi](#interface) oluşturmanızı sağlar. Bir [cihaz modeli](#device-model) , birden çok arabirimi bileşen olarak birleştirebilir. Örneğin, bir model bir switch bileşeni ve termostat bileşeni içerebilir. Bir modeldeki birden çok bileşen aynı arabirim türünü de kullanabilir. Örneğin, bir model iki termostat bileşeni içerebilir.

### <a name="configuration"></a>Yapılandırma

[Otomatik cihaz yapılandırması](../iot-hub/iot-hub-automatic-device-management.md)bağlamında IoT Hub içindeki bir yapılandırma, bir dizi cihaz için istenen yapılandırmayı tanımlar ve durum ve ilerlemeyi raporlamak için bir dizi ölçüm sağlar.

### <a name="connection-string"></a>Bağlantı dizesi

Bir uç noktaya bağlanmak için gereken bilgileri kapsüllemek için uygulama kodunuzda bağlantı dizeleri kullanırsınız. Bağlantı dizesi genellikle uç nokta ve güvenlik bilgilerinin adresini içerir, ancak bağlantı dizesi biçimleri hizmetler arasında farklılık gösterir. IoT Hub hizmetiyle ilişkili iki tür bağlantı dizesi vardır:

- *Cihaz bağlantı dizeleri* , cihazların bir IoT Hub 'ında cihaza yönelik uç noktalara bağlanmasını sağlar.

- *IoT Hub bağlantı dizeleri* , arka uç uygulamalarının bir IoT Hub 'ında hizmete yönelik uç noktalara bağlanmasını sağlar.

### <a name="custom-endpoints"></a>Özel uç noktalar

Bir [yönlendirme kuralı](#routing-rules)tarafından dağıtılan iletileri teslim etmek Için bir IoT Hub 'ında özel [uç noktalar](../iot-hub/iot-hub-devguide-endpoints.md) oluşturabilirsiniz. Özel uç noktalar doğrudan bir olay hub 'ına, Service Bus kuyruğuna veya bir Service Bus konusuna bağlanır.

### <a name="custom-gateway"></a>Özel ağ geçidi

Ağ Geçidi, [IoT Hub](#iot-hub)doğrudan bağlanamadan cihazlara bağlantı sağlar. İletileri, özel protokol dönüştürmelerini ve kenardaki diğer işlemleri işlemek için özel mantık uygulayan özel ağ geçitleri oluşturmak üzere Azure IoT Edge kullanabilirsiniz.

## <a name="d"></a>D

### <a name="data-point-message"></a>Veri noktası iletisi

Veri noktası iletisi, Rüzgar hızı veya sıcaklık gibi [telemetri](#telemetry) verilerini içeren [cihazdan buluta](#device-to-cloud) iletidir.

### <a name="default-component"></a>Varsayılan bileşen

IoT Tak ve Kullan, tüm [cihaz modellerinin](#device-model) varsayılan bir bileşeni vardır. Basit bir cihaz modelinde yalnızca varsayılan bir bileşen vardır. böyle bir model, bileşen cihazı yok olarak da bilinir. Daha karmaşık bir modelde, varsayılan bileşenin altında iç içe yerleştirilmiş birden çok bileşen vardır.

### <a name="deployment-manifest"></a>Dağıtım bildirimi

[IoT Edge](#iot-edge), dağıtım bildirimi bir veya daha fazla IoT Edge cihazın modül, yol ve ilişkili modül istenen özelliklerini dağıtmak için bir veya daha fazla cihazda kopyalanacak bilgileri IÇEREN bir JSON belgesidir.

### <a name="desired-configuration"></a>İstenen yapılandırma

Bir [cihaz ikizi](../iot-hub/iot-hub-devguide-device-twins.md)bağlamında, istenen yapılandırma cihaz ikizi cihaz ile eşitlenmesi gereken tüm özellikler ve meta veriler kümesini ifade eder.

### <a name="desired-properties"></a>İstenen özellikler

Bir [cihaz ikizi](../iot-hub/iot-hub-devguide-device-twins.md)bağlamında, istenen özellikler cihaz ikizi 'nin cihaz yapılandırmasını veya koşulunu eşitlemeye yönelik [bildirilen özelliklerle](#reported-properties) birlikte kullanılan bir alt bölümü olur. İstenen özellikler yalnızca bir [arka uç uygulaması](#back-end-app) tarafından ayarlanabilir ve [cihaz uygulaması](#device-app)tarafından gözlemlenebilir.

### <a name="device"></a>Cihaz

IoT bağlamında, bir cihaz genellikle veri toplayabilen veya diğer cihazları denetleyebilen Küçük ölçekli, tek başına bilgi işlem cihazıdır. Örneğin, bir cihaz bir çevre izleme aygıtı ya da bir ortamla ilgili bir ortam ve havalandırma sistemleri için bir denetleyici olabilir. [Cihaz Kataloğu](https://catalog.azureiotsolutions.com/) , [IoT Hub](#iot-hub)ile çalışmak üzere sertifikalı donanım cihazlarının bir listesini sağlar.

### <a name="device-app"></a>Cihaz uygulaması

Cihaz uygulaması [cihazınızda](#device) çalışır ve [IoT Hub](#iot-hub)'ınızla iletişimi işler. Genellikle, bir cihaz uygulaması uyguladığınızda [Azure IoT cihaz SDK](#azure-iot-device-sdks) 'larından birini kullanırsınız. Birçok IoT öğreticilerinde, kolaylık sağlaması için sanal bir [cihaz](#simulated-device) kullanırsınız.

### <a name="device-builder"></a>Cihaz Oluşturucu

Bir cihaz Oluşturucu, [ıot Tak ve kullan cihazında](#iot-plug-and-play-device)çalıştırılacak kodu uygularken bir [cihaz modeli](#device-model) ve [arabirimleri](#interface) kullanır. Cihaz oluşturucular genellikle cihaz istemcisini uygulamak için [Azure IoT cihaz SDK](#azure-iot-device-sdks) 'larından birini kullanır.

### <a name="device-certification"></a>Cihaz sertifikasyonu

IoT Tak ve Kullan cihaz Sertifikası programı, bir cihazın IoT Tak ve Kullan sertifika gereksinimlerini karşıladığını doğrular. [Azure IoT cihaz kataloğuna yönelik ortak sertifikalı](https://aka.ms/devicecatalog)sertifikalı bir cihaz ekleyebilirsiniz.

### <a name="device-condition"></a>Cihaz koşulu

Bir [cihaz uygulaması](#device-app)tarafından raporlanarak, kullanılmakta olan bağlantı yöntemi gibi cihaz durum bilgilerini gösterir. [Cihaz uygulamaları](#device-app) , yeteneklerini de bildirebilir. Cihaz ikizlerini kullanarak koşul ve yetenek bilgileri için sorgulama yapabilirsiniz.

### <a name="device-data"></a>Cihaz verileri

Cihaz verileri IoT Hub [kimlik kayıt defterinde](#identity-registry)depolanan cihaz başına verileri ifade eder. Bu verileri içeri ve dışarı aktarmak mümkündür.

### <a name="device-identity"></a>Cihaz kimliği

Cihaz kimliği (veya cihaz KIMLIĞI), IoT Hub [kimlik kayıt defterine](#identity-registry)kayıtlı her cihaza atanan benzersiz tanıtıcıdır.

### <a name="device-management"></a>Cihaz yönetimi

Cihaz yönetimi, IoT çözümünüzdeki planlama, sağlama, yapılandırma, izleme ve devre dışı bırakma dahil cihazların yönetimiyle ilişkili tam yaşam döngüsünü kapsar.

### <a name="device-management-patterns"></a>Cihaz yönetimi modelleri

[IoT Hub](#iot-hub) 'ı yeniden başlatma, fabrika sıfırlamaları gerçekleştirme ve cihazlarınızda bellenim güncelleştirmelerini gerçekleştirme dahil ortak cihaz yönetimi düzenlerini mümkün.

### <a name="device-model"></a>Cihaz modeli

Bir cihaz modeli, IoT Tak ve Kullan cihazının yeteneklerini anlatmak için [dijital TWINS tanım dilini](#digital-twins-definition-language-dtdl) kullanan bir [model](#model) türüdür. Basit bir cihaz modeli, cihaz yeteneklerini anlatmak için tek bir arabirim kullanır. Daha karmaşık bir cihaz modeli, her biri bir özellik kümesini açıklayan birden çok bileşen içerir. Daha fazla bilgi için bkz. [modellerdeki ıot Tak ve kullan bileşenleri](../iot-pnp/concepts-components.md).

### <a name="device-modeling"></a>Cihaz modellemesi

Bir [cihaz Oluşturucu](#device-builder) ya da [Modül oluşturucusu](#module-builder), [IoT Tak ve kullan cihazının](#iot-plug-and-play-device)yeteneklerini modellemek için [dijital TWINS tanım dilini](#digital-twins-definition-language-dtdl) kullanır. Bir [çözüm Oluşturucusu](#solution-builder) , bir IoT çözümünü modelden yapılandırabilir.

### <a name="device-provisioning"></a>Cihaz sağlama

Cihaz sağlama, ilk [Cihaz verilerini](#device-data) çözümünüzdeki depolara ekleme işlemidir. Yeni bir cihazın hub 'ınıza bağlanmasını sağlamak için IoT Hub [kimlik kayıt defterine](#identity-registry)BIR cihaz kimliği ve anahtarı eklemeniz gerekir. Sağlama sürecinin bir parçası olarak, diğer çözüm depolarında cihaza özgü verileri de başlatmak gerekebilir.

### <a name="device-provisioning-service"></a>Cihaz Sağlama Hizmeti

IoT Hub cihaz sağlama hizmeti (DPS), belirli bir IoT Hub 'ına sıfır Touch cihaz sağlamayı yapılandırmak için kullandığınız [IoT Hub](#iot-hub) yardımcı hizmettir. DPS sayesinde, milyonlarca cihazı güvenli ve ölçeklenebilir bir şekilde sağlayabilirsiniz.

### <a name="device-rest-api"></a>Cihaz REST API

Bir IoT Hub 'ına cihazdan buluta iletiler göndermek ve bir IoT Hub 'ından [buluttan cihaza](#cloud-to-device) iletileri almak için bir cihazdan cihaz [REST API](/rest/api/iothub/device) kullanabilirsiniz. Genellikle, IoT Hub öğreticilerde gösterildiği gibi daha yüksek düzey [cihaz SDK 'lardan](#azure-iot-device-sdks) birini kullanmanız gerekir.

### <a name="device-twin"></a>Cihaz çifti

Bir cihaz ikizi, meta veriler, konfigürasyonlar ve koşullar gibi cihaz durum bilgilerini depolayan JSON belgesidir. IoT Hub, IoT Hub 'ınızda sağladığınız her cihaz için bir cihaz ikizi devam ettirir. Cihaz TWINS, cihaz ve çözüm arka ucu arasındaki cihaz koşullarını ve konfigürasyonları eşitlemenize olanak tanır. Belirli cihazları bulmak ve uzun süreli işlemlerin durumu için cihaz iklerini sorgulama yapabilirsiniz.

### <a name="device-to-cloud"></a>Cihazdan buluta

Bağlı bir cihazdan [IoT Hub](#iot-hub)gönderilen iletileri ifade eder. Bu iletiler [veri noktası](#data-point-message) veya [etkileşimli](#interactive-message) iletiler olabilir. Daha fazla bilgi için bkz. [IoT Hub ile Ileti gönderme ve alma](../iot-hub/iot-hub-devguide-messaging.md).

### <a name="digital-twin"></a>Dijital ikizi

Dijital ikizi, fiziksel bir nesneyi temsil eden bir dijital veri koleksiyonudur. Fiziksel nesnedeki değişiklikler dijital ikizi yansıtılır. Bazı senaryolarda, fiziksel nesneyi işlemek için dijital ikizi kullanabilirsiniz. [Azure dijital TWINS hizmeti](../digital-twins/index.yml) , [dijital TWINS tanım DILI 'NDE (dtdl)](#digital-twins-definition-language-dtdl) gösterilen [modelleri](#model) kullanarak fiziksel cihazların dijital iklerini veya üst düzey soyut iş kavramlarını temsil eder ve çok çeşitli bulut tabanlı dijital ikizi çözümlerini etkinleştirir. [Iot Tak ve kullan](../iot-pnp/index.yml) cihazının bir dtdl [cihaz modeliyle](#device-model)tanımlanan dijital bir ikizi vardır.

### <a name="digital-twin-change-events"></a>Dijital ikiz değişiklik olayları

[Iot Tak ve Kullan cihazı](#iot-plug-and-play-device) bir IoT Hub 'ına bağlandığında, hub, dijital ikizi değişikliklerinin bildirimlerini göndermek için yönlendirme özelliğini kullanabilir. Örneğin, bir cihazdaki [özellik](#properties) değeri her değiştiğinde IoT Hub, bir olay hub 'ı gibi bir uç noktaya bildirim gönderebilir.

### <a name="digital-twin-route"></a>Dijital ikizi rotası

Bir IoT Hub 'ında, bir olay hub 'ı gibi bir uç noktaya [dijital ikizi değişiklik olayları](#digital-twin-change-events) teslim etmek için bir yol ayarlandı.

### <a name="digital-twins-definition-language-dtdl"></a>Dijital İkiz Tanımlama Dili (DTDL)

[Iot Tak ve kullan cihazları](#iot-plug-and-play-device) ve [Azure dijital TWINS](../digital-twins/index.yml) varlıkları için [modelleri](#model) ve [arabirimleri](#interface) açıklamak üzere bir JSON-ld dili. [Dijital ikizi](#digital-twin) özelliklerini anlatmak Için [dijital TWINS tanım dili sürüm 2](https://github.com/Azure/opendigitaltwins-dtdl) ' nı kullanın ve IoT Platformu ile IoT çözümlerini varlığın semantiğini kullanacak şekilde etkinleştirin. Dijital TWINS tanım dili genellikle DTDL olarak kısaltılır.

### <a name="direct-method"></a>Direct yöntemi

[Doğrudan yöntem](../iot-hub/iot-hub-devguide-direct-methods.md) , IoT Hub 'ıNıZDA bir API çağırarak bir cihazdaki bir yöntemi yürütmek için bir yöntem tetiklemeniz için bir yoldur.

### <a name="downstream-services"></a>Aşağı akış Hizmetleri

Geçerli bağlamdan veri alan Hizmetleri açıklayan göreli bir terim. Örneğin, Azure dijital TWINS 'nin bağlamını düşünüyorsanız, verilerinizi Azure dijital TWINS 'ten Time Series Insights akışa almak üzere ayarlarsanız [Time Series Insights](../time-series-insights/index.yml) bir aşağı akış hizmeti olarak kabul edilir.

## <a name="e"></a>E

### <a name="endpoint"></a>Uç Nokta

Diğer hizmetlerden veri alabilen bir veri yönlendirme hizmetinin Adlandırılmış temsili.

IoT Hub 'ı, uygulamalarınızın IoT Hub 'ına bağlanmasını sağlayan birden çok [uç nokta](../iot-hub/iot-hub-devguide-endpoints.md) sunar. Cihazların, [cihazdan buluta](#device-to-cloud) iletileri gönderme ve [buluttan cihaza](#cloud-to-device) iletileri alma gibi işlemleri gerçekleştirmesini sağlayan cihaza yönelik uç noktalar vardır. [Arka uç uygulamalarının](#back-end-app) [cihaz kimlik](#device-identity) yönetimi ve cihaz ikizi yönetimi gibi işlemleri gerçekleştirmesini sağlayan hizmete yönelik yönetim uç noktaları vardır. Cihazdan buluta iletileri okumak için hizmete yönelik [yerleşik uç noktalar](#built-in-endpoints) vardır. Bir [yönlendirme kuralı](#routing-rules)tarafından dağıtılan cihazdan buluta iletileri almak için [Özel uç noktalar](#custom-endpoints) oluşturabilirsiniz.

### <a name="enrollment"></a>Kayıt

[Cihaz sağlama hizmeti](#device-provisioning-service)'nde kayıt, bir [bağlı IoT Hub](#linked-iot-hub) ile, oto sağlama aracılığıyla kaydedebilen ayrı cihazların veya cihaz gruplarının kaydıdır.

### <a name="enrollment-group"></a>Kayıt grubu

[Cihaz sağlama hizmetinde](#device-provisioning-service)bir kayıt grubu, bir X. 509.440 veya simetrik anahtar [kanıtlama mekanizmasını](#attestation-mechanism)paylaşan bir cihaz grubunu tanımlar.

### <a name="event-handlers"></a>Olay işleyicileri

Bu, bir olayın varışı tarafından tetiklenen ve bazı işleme eylemli bir işleme başvurabilir. Olay işleyicilerini oluşturmanın bir yolu, bir Azure işlevine olay işleme kodu ekleyerek ve [uç noktalar](#endpoint) ve [olay yönlendirme](#event-routing)kullanarak veri göndererek.

### <a name="event-hub-compatible-endpoint"></a>Olay Hub 'ı ile uyumlu uç nokta

IoT Hub 'ınıza gönderilen [cihazdan buluta](#device-to-cloud) iletileri okumak için, hub 'ınızdaki bir uç noktaya bağlanabilir ve bu iletileri okumak Için Event hub ile uyumlu herhangi bir yöntemi kullanabilirsiniz. Event hub ile uyumlu yöntemler [Event Hubs SDK 'ları](../event-hubs/event-hubs-programming-guide.md) ve [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md)kullanmayı içerir.

### <a name="event-routing"></a>Olay yönlendirme

Bir cihazdan veya hizmetten başka birinin [uç noktasına](#endpoint) olay ve veri gönderme işlemi. 

IoT Hub 'da, iletilerin nasıl gönderilmesi gerektiğini tanımlamak için [yönlendirme kuralları](#routing-rules) tanımlayabilirsiniz. Azure dijital TWINS 'de olay rotaları, bu amaçla oluşturulan varlıklardır. Azure dijital TWINS olay rotaları, her bir uç noktaya hangi tür olayların gönderileceğini sınırlamak için filtre içerebilir.

## <a name="f"></a>F

### <a name="field-gateway"></a>Alan ağ geçidi

Alan ağ geçidi, [IoT Hub](#iot-hub) doğrudan bağlanamamakta olan ve genellikle cihazlarınızla yerel olarak dağıtılan cihazlara bağlantı sağlar. Daha fazla bilgi için bkz. [Azure IoT Hub nedir?](../iot-hub/about-iot-hub.md).

## <a name="g"></a>G

### <a name="gateway"></a>Ağ geçidi

Ağ Geçidi, [IoT Hub](#iot-hub)doğrudan bağlanamadan cihazlara bağlantı sağlar. Ayrıca bkz. [alan ağ geçidi](#field-gateway), [bulut ağ](#cloud-gateway)geçidi ve [özel ağ geçidi](#custom-gateway).

### <a name="gateway-device"></a>Ağ geçidi cihazı

Bir cihaz bir [alan ağ geçidi](#field-gateway)örneğidir. Bir ağ geçidi cihazı standart IoT [cihazı](#device) veya IoT Edge bir [cihaz](#iot-edge-device)olabilir.

Bir ağ geçidi cihazı, [IoT Hub](#iot-hub)doğrudan bağlanamalı aşağı akış cihazları için bağlantı sağlar.

## <a name="h"></a>H

### <a name="hardware-security-module"></a>Donanım güvenlik modülü

Donanım güvenlik modülü (HSM), cihaz gizliliklerin güvenli, donanım tabanlı depolaması için kullanılır. Bir cihaz için en güvenli gizli depolama biçimidir. Hem X. 509.440 sertifikaları hem de simetrik anahtarlar bir HSM 'de depolanabilir. [Cihaz sağlama hizmetinde](#device-provisioning-service)bir [KANıTLAMA mekanizması](#attestation-mechanism) bir HSM kullanabilir.

## <a name="i"></a>I

### <a name="id-scope"></a>KIMLIK kapsamı

KIMLIK kapsamı, oluşturulduğu sırada bir [cihaz sağlama hizmeti (DPS)](#device-provisioning-service) örneğine atanan benzersiz bir değerdir.

IoT Central uygulamalar, DPS örneklerini kullanır ve KIMLIK kapsamını IoT Central kullanıcı arabirimi aracılığıyla kullanılabilir hale getirir.

### <a name="identity-registry"></a>Kimlik kayıt defteri

[Kimlik kayıt defteri](../iot-hub/iot-hub-devguide-identity-registry.md) , IoT Hub 'ına bağlanmasına izin verilen ayrı cihazlarla ilgili bilgileri depolayan bir IoT Hub 'ın yerleşik bileşenidir.

### <a name="individual-enrollment"></a>Bireysel kayıt

[Cihaz sağlama hizmetinde](#device-provisioning-service), tek bir kayıt bir X. 509.440 yaprak sertifikası veya simetrik anahtarı bir [kanıtlama mekanizması](#attestation-mechanism)olarak kullanan tek bir cihazı tanımlar.

### <a name="interactive-message"></a>Etkileşimli ileti

Etkileşimli ileti, çözüm arka ucunda acil bir eylemi tetikleyen bir [buluttan cihaza](#cloud-to-device) iletidir. Örneğin, bir cihaz bir CRM sisteminde otomatik olarak oturum açmış olması gereken bir hata hakkında alarm gönderebilir.

### <a name="interface"></a>Arabirim

IoT Tak ve Kullan, bir arabirim [ıot Tak ve Kullan cihazı](#iot-plug-and-play-device) veya [dijital ikizi](#digital-twin)tarafından uygulanan ilgili özellikleri açıklar. Arabirimleri farklı [cihaz modelleriyle](#device-model)yeniden kullanabilirsiniz. Bir arabirim bir cihaz modelinde kullanıldığında, cihazın bir [bileşenini](#component) tanımlar. Basit bir cihaz yalnızca bir varsayılan arabirim içerir.

Azure dijital TWINS 'de, *arabirim* bir [dtdl](#digital-twins-definition-language-dtdl) model tanımındaki en üst düzey kod öğesine başvurmak için kullanılabilir.

### <a name="iot-edge"></a>IoT Edge

Azure IoT Edge, şirket içi cihazlara Azure hizmetlerinin ve çözüme özgü kodun bulut odaklı dağıtımına olanak sağlar. [IoT Edge cihazlar](#iot-edge-device) verileri buluta göndermeden önce bilgi işlem ve analiz yapmak için diğer cihazlardan verileri toplayabilir. Daha fazla bilgi için bkz. [Azure IoT Edge](../iot-edge/index.yml).

### <a name="iot-edge-agent"></a>IoT Edge Aracısı

IoT Edge çalışma zamanının, modülleri dağıtmaktan ve izlemekten sorumlu olan bölümü.

### <a name="iot-edge-device"></a>IoT Edge cihazı

IoT Edge cihaz, Azure Hizmetleri, üçüncü taraf hizmetleri veya kendi kodunuzu çalıştırmak için Kapsayıcılı IoT Edge [modüller](#modules) kullanır. IoT Edge cihazda, [IoT Edge çalışma zamanı](#iot-edge-runtime) modülleri yönetir. IoT Edge bir cihazı buluttan uzaktan izleyebilir ve yönetebilirsiniz.

### <a name="iot-edge-hub"></a>IoT Edge hub 'ı

Modül iletişimleri, yukarı akış (IoT Hub ve IoT Hub) iletişimlerine ait modülden sorumlu IoT Edge çalışma zamanının parçasıdır.

### <a name="iot-edge-runtime"></a>IoT Edge çalışma zamanı

IoT Edge Runtime, Microsoft 'un IoT Edge bir cihaza yükleneceği her şeyi içerir. Edge Aracısı, Edge hub ve IoT Edge güvenlik cini içerir.

### <a name="iot-extension-for-azure-cli"></a>Azure CLı için IoT uzantısı

[Azure CLI Için IoT uzantısı](https://github.com/Azure/azure-iot-cli-extension) , platformlar arası bir komut satırı aracıdır. Araç, [kimlik kayıt defterinde](#identity-registry)cihazlarınızı yönetmenizi, cihazlarınızdaki iletileri ve dosyaları göndermenizi ve almanızı ve IoT Hub işlemlerinizi izlemenizi sağlar.

### <a name="iot-hub"></a>IoT Hub

IoT Hub, milyonlarca cihaz ile bir çözüm arka ucu arasında güvenilir ve güvenli çift yönlü iletişimler sağlayan, tam olarak yönetilen bir Azure hizmetidir. Daha fazla bilgi için bkz. [Azure IoT Hub nedir?](../iot-hub/about-iot-hub.md). Azure aboneliğinizi kullanarak IoT mesajlaşma iş yüklerinizi idare etmek için IoT Hub 'ları oluşturabilirsiniz.

### <a name="iot-hub-metrics"></a>IoT Hub ölçümleri

IoT Hub ölçümler, Azure aboneliğinizdeki IoT Hub 'larının durumu hakkında size veri sağlar. IoT Hub ölçümler, hizmetin ve ona bağlı cihazların genel sistem durumunu değerlendirmenizi sağlar. IoT Hub ölçümler, IoT Hub 'ınız ile neler olduğunu görmenizi ve Azure desteği ile iletişim kurmaya gerek kalmadan kök neden sorunlarını araştırmanıza yardımcı olabilir. Daha fazla bilgi için bkz. [izleyici IoT Hub](../iot-hub/monitor-iot-hub.md).

### <a name="iot-hub-query-language"></a>IoT Hub sorgu dili

[IoT Hub sorgu dili](../iot-hub/iot-hub-devguide-query-language.md) , [işinizi](#job), dijital ikişlerinizi ve cihaz ikizlerini SORGULAMANıZı sağlayan bir SQL benzeri dildir.

### <a name="iot-hub-resource-rest-api"></a>Kaynak REST API IoT Hub

Hub oluşturma, güncelleştirme ve silme gibi işlemleri gerçekleştirmek için Azure aboneliğinizdeki IoT Hub 'larını yönetmek üzere [IoT Hub kaynak REST API](/rest/api/iothub/iothubresource) kullanabilirsiniz.

### <a name="iot-plug-and-play-bridge"></a>IoT Tak Çalıştır köprüsü

IoT Tak ve Kullan Köprüsü, Windows veya Linux ağ geçitlerine eklenen mevcut sensörler ve çevre birimlerinin [ıot Tak ve kullan cihazları](#iot-plug-and-play-device)olarak bağlanmasına olanak sağlayan açık kaynaklı bir uygulamadır.

### <a name="iot-plug-and-play-conventions"></a>IoT Tak Çalıştır kuralları

IoT Tak ve Kullan [cihazların](#iot-plug-and-play-device) bir çözüm ile veri alışverişi yaparken bir dizi kural izlemesi beklenir.

### <a name="iot-plug-and-play-device"></a>IoT Tak ve Kullan cihazı

IoT Tak ve Kullan cihazı genellikle veri toplayan veya diğer cihazları denetleyen ve bir [cihaz modeli](#device-model)uygulayan yazılım veya bellenim çalıştıran bir küçük ölçekli, tek başına bilgi işlem aygıtıdır.  Örneğin, bir IoT Tak ve Kullan cihazı, bir çevre izleme cihazı veya bir akıllı tarım sulama sistemi denetleyicisi olabilir. IoT Tak ve Kullan cihazı doğrudan veya bir IoT Edge modülü olarak uygulanabilir. Komut, denetim ve IoT Tak ve Kullan cihazlarındaki verileri almak için bulutta barındırılan bir IoT çözümü yazabilirsiniz.

### <a name="iot-solution-accelerators"></a>IoT çözüm hızlandırıcıları

Azure IoT Çözüm Hızlandırıcıları, çözümlerle birden çok Azure hizmetini birlikte paketleyebilir. Bu çözümler, yaygın IoT senaryolarına yönelik uçtan uca uygulamalarla hızla başlamanıza olanak sağlar. Daha fazla bilgi için bkz. [Azure IoT Çözüm Hızlandırıcıları nelerdir?](../iot-accelerators/about-iot-accelerators.md)

## <a name="j"></a>J

### <a name="job"></a>İş

Çözüm arka ucu, IoT Hub 'ınıza kayıtlı bir dizi cihazda etkinlikleri zamanlamak ve izlemek için [işleri](../iot-hub/iot-hub-devguide-jobs.md) kullanabilir. Etkinlikler, cihaz ikizi [istenen özellikleri](#desired-properties)güncelleştirmek, Device ikizi [etiketlerini](#tags)güncelleştirmek ve [doğrudan yöntemleri](#direct-method)çağırmak içerir. [IoT Hub](#iot-hub) , [kimlik kayıt defterinden](#identity-registry) [içeri ve dışarı aktarmak](../iot-hub/iot-hub-devguide-identity-registry.md#import-and-export-device-identities) için de kullanılır.

## <a name="l"></a>L

### <a name="leaf-device"></a>Yaprak cihaz

[IoT Edge](#iot-edge), yaprak cihaz, aşağı akış cihazı olmayan bir cihazdır.

### <a name="lifecycle-event"></a>Yaşam döngüsü olayı

Azure dijital TWINS 'de, Azure dijital TWINS örneğinizden dijital ikizi, bir ilişki veya bir olay işleyicisi gibi bir veri öğesi oluşturulduğunda veya silindiğinde bu tür bir olay tetiklenir.

### <a name="linked-iot-hub"></a>Bağlantılı IoT Hub 'ı

[Cihaz sağlama hizmeti (DPS)](#device-provisioning-service), cihazları onunla bağlantılı olan IoT Hub 'larına sağlayabilir. Bir IoT Hub 'ını bir DPS örneğine bağlamak, hizmetin cihaz KIMLIĞINI kaydetmesini ve cihaz ikizi ilk yapılandırmasını ayarlamanızı sağlar.

## <a name="m"></a>M

### <a name="model"></a>Modelleme

Model, fiziksel ortamınızda özellikler, Telemetriler, bileşenler ve bazen diğer bilgiler dahil olmak üzere bir varlık türünü tanımlar. Modeller, bu türün belirli fiziksel nesnelerini temsil eden [dijital TWINS](#digital-twin) oluşturmak için kullanılır. Modeller [dijital TWINS tanım diline](#digital-twins-definition-language-dtdl)yazılır.

[Azure dijital TWINS hizmetinde](../digital-twins/index.yml), modeller cihazları veya üst düzey soyut iş kavramlarını tanımlayabilir. [Iot Tak ve kullan](../iot-pnp/index.yml)'de [cihaz modelleri](#device-model) , cihazları özellikle anlatmak için kullanılır.

### <a name="model-id"></a>Model Kimliği

IoT Tak ve Kullan cihazı bir IoT Hub bağlandığı zaman, uyguladığı [Dtdl](#digital-twins-definition-language-dtdl) MODELININ **model kimliğini** gönderir. Bu KIMLIK, çözümün cihaz modelini bulmasını sağlar.

### <a name="model-repository"></a>Model deposu

Model deposu [cihaz modellerini](#device-model) ve [arabirimlerini](#interface)depolar.

### <a name="model-repository-rest-api"></a>Model deposu REST API

Model deposunu yönetmek ve bunlarla etkileşim kurmak için bir API. Örneğin, API 'yi, [cihaz modellerini](#device-model)eklemek ve aramak için kullanabilirsiniz.

### <a name="module-builder"></a>Modül Oluşturucu

Modül Oluşturucusu, [ıot Tak ve kullan cihazında](#iot-plug-and-play-device)çalıştırılacak kodu uygularken bir [cihaz modeli](#device-model) ve [arabirimleri](#interface) kullanır. Modül oluşturucular, bir cihazdaki IoT Edge çalışma zamanına dağıtmak için kodu bir modül veya IoT Edge modülü olarak uygular.

### <a name="module-identity"></a>Modül kimliği

Modül kimliği, bir cihaza ait olan her modüle atanan benzersiz tanıtıcıdır. Modül kimliği [kimlik kayıt defterine](#identity-registry)de kaydedilir.

Modül, modülün [IoT Hub](#iot-hub) kimlik doğrulaması için kullandığı güvenlik kimlik bilgileri veya [IoT Edge Hub 'ına](#iot-edge-hub)bir IoT Edge modülü olması durumunda ayrıntıları belirler.

### <a name="module-image"></a>Modül resmi

[IoT Edge çalışma zamanının](#iot-edge-runtime) modül örnekleri örneğini oluşturmak için kullandığı Docker görüntüsü.

### <a name="module-twin"></a>Modül ikizi

Cihaz ikizi benzer bir modül ikizi, meta veriler, konfigürasyonlar ve koşullar gibi modül durum bilgilerini depolayan JSON belgesidir. IoT Hub, IoT Hub 'ınızdaki bir cihaz kimliği altında sağladığınız her modül kimliği için bir modül ikizi devam ettirir. Modül TWINS, modül ve çözüm arka ucu arasındaki modül koşullarını ve konfigürasyonları eşitlemenize olanak tanır. Belirli modülleri bulmak ve uzun süreli işlemlerin durumunu sorgulamak için modül TWINS sorgulama yapabilirsiniz.

### <a name="modules"></a>Modül

Cihaz tarafında IoT Hub cihaz SDK 'Ları, her birinin IoT Hub için bağımsız bir bağlantı açtığı [modüller](../iot-hub/iot-hub-devguide-module-twins.md) oluşturmanızı sağlar. Bu işlevsellik, cihazınızdaki farklı bileşenler için ayrı ad alanları kullanmanıza olanak sağlar.

Modül kimliği ve modül ikizi, [cihaz kimliği](#device-identity) ve [cihaz ikizi](#device-twin) ile aynı özellikleri sağlar, ancak daha ayrıntılı bir ayrıntı düzeyine sahiptir. Bu daha ayrıntılı ayrıntı düzeyi, bu bileşenlerin her biri için yapılandırma ve koşulları yalıtmak amacıyla, işletim sistemi tabanlı cihazlar veya birden çok bileşeni yöneten bellenim cihazları gibi özellikli cihazların kullanılmasını sağlar.

[IoT Edge](#iot-edge), modül IoT Edge cihazlara dağıtabileceğiniz bir Docker kapsayıcısıdır. Bir cihazdan ileti almak, ileti dönüştürmek veya IoT Hub 'ına ileti göndermek gibi belirli bir görevi gerçekleştirir. Diğer modüllerle iletişim kurar ve [IoT Edge çalışma zamanına](#iot-edge-runtime)veri gönderir.

### <a name="mqtt"></a>MQTT

[MQTT](https://mqtt.org/) , cihazlarla iletişim kurmak için [IoT Hub](#iot-hub) desteklediği mesajlaşma protokollerinden biridir. IoT Hub desteklediği mesajlaşma protokolleri hakkında daha fazla bilgi için, bkz. [IoT Hub ile Ileti gönderme ve alma](../iot-hub/iot-hub-devguide-messaging.md).

## <a name="o"></a>O

### <a name="ontology"></a>Ontology

Belirli bir etki alanı için, örneğin Emlak, akıllı şehir, IoT sistemleri, enerji kılavuzları ve daha fazlası gibi modeller kümesi. Ontoloiler, endüstri standartlarına ve en iyi yöntemlere dayalı bir başlangıç noktası sağladığından [Azure dijital TWINS](#azure-digital-twins)'teki gibi bilgi grafikler için genellikle şemalar olarak kullanılır.

Ontolo, hakkında daha fazla bilgi için bkz. [ontology nedir?](../digital-twins/concepts-ontologies.md)

### <a name="operations-monitoring"></a>İşlemleri izleme

IoT Hub [işlemler izleme](../iot-hub/iot-hub-operations-monitoring.md) , IoT Hub 'ınızdaki işlemlerin durumunu gerçek zamanlı olarak izlemenize olanak sağlar. [IoT Hub](#iot-hub) birkaç işlem kategorisi içindeki olayları izler. İşlenmek üzere bir veya daha fazla kategoriden bir IoT Hub uç noktasına olay göndermeyi tercih edebilirsiniz. Verileri hatalara göre izleyebilir veya veri desenlerine göre daha karmaşık işleme ayarlayabilirsiniz.

## <a name="p"></a>P

### <a name="physical-device"></a>Fiziksel cihaz

Fiziksel bir cihaz, bir IoT Hub 'ına bağlanan Raspberry PI gibi gerçek bir cihazdır. Kolaylık sağlaması için IoT Hub öğreticilerinin birçoğu, yerel makinenizde örnek çalıştırmanızı sağlamak üzere [sanal cihazları](#simulated-device) kullanır.

### <a name="primary-and-secondary-keys"></a>Birincil ve ikincil anahtarlar

Bir IoT Hub 'ında cihaza yönelik veya hizmete yönelik bir uç noktaya bağlandığınızda, [bağlantı dizeniz](#connection-string) size erişim sağlamak için anahtar içerir. [Kimlik kayıt defterine](#identity-registry) bir cihaz eklediğinizde veya hub 'ınıza [paylaşılan bir erişim ilkesi](#shared-access-policy) eklediğinizde hizmet bir birincil ve ikincil anahtar oluşturur. İki anahtara sahip olmak, IoT Hub 'ına erişimi kaybetmeksizin bir anahtarı güncelleştirdiğinizde bir anahtardan diğerine geri alma olanağı sağlar.

### <a name="properties"></a>Özellikler

Özellikler, bir [dijital ikizi](#digital-twin)kalıcı durumunu temsil eden bir [arabirimde](#interface) tanımlanan veri alanlarıdır. Özellikleri salt okunurdur veya yazılabilir olarak bildirebilirsiniz. Seri numarası gibi salt yazılır özellikler, [ıot Tak ve kullan cihazının](#iot-plug-and-play-device) kendisi üzerinde çalışan kodla ayarlanır. Bir alarm eşiği gibi yazılabilir özellikler genellikle bulut tabanlı IoT çözümünden ayarlanır.

### <a name="property-change-event"></a>Özellik değiştirme olayı

[Dijital bir ikizi](#digital-twin)Özellik değişikliğinden kaynaklanan bir olay.

### <a name="protocol-gateway"></a>Protokol ağ geçidi

Bir protokol ağ geçidi genellikle buluta dağıtılır ve [IoT Hub](#iot-hub)bağlanan cihazlar için protokol çevirisi hizmetleri sağlar. Daha fazla bilgi için bkz. [Azure IoT Hub nedir?](../iot-hub/about-iot-hub.md).

## <a name="r"></a>R

### <a name="registration"></a>Kayıt

Kayıt, IoT Hub [kimlik kayıt defterindeki](#identity-registry)bir cihazın kaydıdır. Cihaz kaydını otomatik hale getirmek için doğrudan veya cihazı kaydedebilir veya cihaz [sağlama hizmetini](#device-provisioning-service) kullanabilirsiniz.

### <a name="registration-id"></a>Kayıt KIMLIĞI

Kayıt KIMLIĞI cihaz [sağlama hizmeti](#device-provisioning-service)ile bir cihaz [kaydını](#registration) benzersiz şekilde tanımlamak için kullanılır. Kayıt KIMLIĞI, [cihaz kimliğiyle](#device-identity)aynı değer olabilir.

### <a name="relationship"></a>İlişki

[Azure dijital TWINS](../digital-twins/index.yml) hizmetinde ilişkiler, [dijital TWINS](#digital-twin) 'i tüm fiziksel ortamınızı dijital olarak temsil eden bilgi grafikleriyle bağlamak için kullanılır. İkizlerini 'nizin sahip olduğu ilişki türleri, TWINS ' [model](#model) tanımlarının bir parçası olarak tanımlanmıştır — belirli bir ikizi türü için [dtdl](#digital-twins-definition-language-dtdl) modeli, diğer TWINS için hangi ilişkilerle ilgili bilgiler içerir.

### <a name="reported-configuration"></a>Bildirilen yapılandırma

Bir [cihaz ikizi](../iot-hub/iot-hub-devguide-device-twins.md)bağlamında, bildirilen yapılandırma cihaz ikizi ' de çözüm arka ucuna bildirilmesi gereken tüm özellik ve meta veri kümesini ifade eder.

### <a name="reported-properties"></a>Bildirilen özellikler

Bir [cihaz ikizi](../iot-hub/iot-hub-devguide-device-twins.md)bağlamında, bildirilen özellikler cihaz yapılandırması veya koşulu eşitlenmesi için [istenen özelliklerle](#desired-properties) kullanılan cihaz ikizi 'nin bir alt bölümü olur. Bildirilen özellikler yalnızca [cihaz uygulaması](#device-app) tarafından ayarlanabilir, bir [arka uç uygulaması](#back-end-app)tarafından okunabilir ve sorgulanabilir.

### <a name="retry-policy"></a>Yeniden Deneme ilkesi

Bir bulut hizmetine bağlandığınızda [geçici hataları](/azure/architecture/best-practices/transient-faults) işlemek için bir yeniden deneme ilkesi kullanırsınız.

### <a name="routing-rules"></a>Yönlendirme kuralları

Cihaz-bulut iletilerini [yerleşik bir uç noktaya](#built-in-endpoints) veya çözüm arka uçınızdan işlemek için [Özel uç noktalara](#custom-endpoints) yönlendirmek üzere IoT Hub 'ınızdaki [yönlendirme kurallarını](../iot-hub/iot-hub-devguide-messages-read-custom.md) yapılandırırsınız.

## <a name="s"></a>S

### <a name="sasl-plain"></a>SASL PLAIN

SASL PLAIN, AMQP protokolünün güvenlik belirteçlerini aktarmak için kullandığı bir protokoldür.

### <a name="service-operations-endpoint"></a>Hizmet işlemleri uç noktası

Hizmet Yöneticisi tarafından kullanılan hizmet ayarlarını yönetmek için bir [uç nokta](#endpoint) . Örneğin, [cihaz sağlama hizmetinde](#device-provisioning-service) kayıtları yönetmek için hizmet uç noktasını kullanırsınız.

### <a name="service-rest-api"></a>Hizmet REST API'si

Cihazlarınızı yönetmek için çözüm arka ucundan [hizmet REST API](/rest/api/iothub/service/configuration) kullanabilirsiniz. API, [cihaz ikizi](#device-twin) özelliklerini alıp güncelleştirmenizi, [doğrudan Yöntemler](#direct-method)çağırmayı ve [işleri](#job)zamanlamayı sağlar. Genellikle, IoT Hub öğreticilerde gösterildiği gibi daha yüksek düzey [hizmet SDK 'lardan](#azure-iot-service-sdks) birini kullanmanız gerekir.

### <a name="shared-access-policy"></a>Paylaşılan erişim ilkesi

Paylaşılan erişim ilkesi, ilgili ilkeyle ilişkilendirilmiş geçerli bir [birincil veya ikincil anahtarı](#primary-and-secondary-keys) olan herkese verilen izinleri tanımlar. Portalda hub 'ınız için paylaşılan erişim ilkelerini ve anahtarlarını yönetebilirsiniz.

### <a name="shared-access-signature"></a>Paylaşılan erişim imzası

Paylaşılan erişim Imzaları (SAS), SHA-256 güvenli karmaları veya URI 'Leri temel alan bir kimlik doğrulama mekanizmasıdır. SAS kimlik doğrulamasının iki bileşeni vardır: _paylaşılan erişim ilkesi_ ve _paylaşılan erişim imzası_ (genellikle belirteç olarak adlandırılır). Bir cihaz, IoT Hub ile kimlik doğrulamak için SAS kullanır. [Arka uç uygulamalar](#back-end-app) , bir IoT Hub 'ında hizmete yönelik uç noktalarla kimlik doğrulaması yapmak için SAS kullanır. Genellikle, bir uygulamanın bir IoT Hub 'ına bağlantı kurmak için kullandığı [bağlantı DIZESINE](#connection-string) SAS belirtecini dahil edersiniz.

### <a name="simulated-device"></a>Sanal cihaz

Kolaylık sağlaması için IoT Hub öğreticilerinin birçoğu, yerel makinenizde örnek çalıştırmanızı sağlamak üzere sanal cihazları kullanır. Buna karşılık, [fiziksel cihaz](#physical-device) , IoT Hub 'ına bağlanan Raspberry Pi gibi gerçek bir cihazdır.

### <a name="solution"></a>Çözüm

Bir _çözüm_ , bir veya daha fazla proje Içeren bir Visual Studio çözümüne başvurabilir. Bir _çözüm_ Ayrıca cihazlar, [cihaz uygulamaları](#device-app), IoT Hub, diğer Azure hizmetleri ve [arka uç uygulamaları](#back-end-app)gibi öğeleri içeren bir IoT çözümüne de başvurabilir.

### <a name="solution-builder"></a>Çözüm Oluşturucu

Çözüm Oluşturucu, çözüm arka ucu oluşturur. Çözüm Oluşturucu genellikle IoT Hub ve [model depoları](#model-repository)gibi Azure kaynaklarıyla birlikte kullanılabilir.

### <a name="system-properties"></a>Sistem özellikleri

Bir [cihaz ikizi](../iot-hub/iot-hub-devguide-device-twins.md)bağlamında Sistem özellikleri salt okunurdur ve cihaz kullanımı ile ilgili son etkinlik zamanı ve bağlantı durumu gibi bilgileri içerir.

## <a name="t"></a>T

### <a name="tags"></a>Etiketler

Bir [cihaz ikizi](../iot-hub/iot-hub-devguide-device-twins.md)bağlamında Etiketler, bir JSON belgesi biçiminde, çözüm arka ucu tarafından depolanan ve alınan cihaz meta verilerlerdir. Etiketler bir cihazdaki uygulamalara görünür değildir.

### <a name="target-condition"></a>Hedef koşul

IoT Edge dağıtımında hedef koşul dağıtımın hedef cihazlarını seçer, örneğin **Tag. Environment = prod**. Hedef koşul sürekli olarak gereksinimleri karşılayan yeni cihazları içerecek şekilde değerlendirilir veya artık olmayan cihazları kaldırır.

### <a name="telemetry"></a>Telemetri

Cihazlar, Rüzgar hızı veya sıcaklık gibi telemetri verileri toplar ve Telemetriyi bir IoT Hub 'ına göndermek için veri noktası iletilerini kullanır.

IoT Tak ve Kullan ve Azure dijital TWINS 'de, bir [arabirimde](#interface) tanımlanan telemetri alanları ölçümleri temsil eder. Bu ölçümler genellikle, bir veri akışı olarak [ıot Tak ve kullan cihazları](#iot-plug-and-play-device)gibi cihazlar tarafından gönderilen algılayıcı okumaları gibi değerlerdir.

[Özelliklerden](#properties)farklı olarak, telemetri [dijital bir ikizi](#digital-twin)depolanmaz; Bu, gerçekleşdikleri sırada işlenmesi gereken zamana bağlanan veri olaylarının bir akışıdır.

### <a name="telemetry-event"></a>Telemetri olayı

Telemetri verilerinin gelişini gösteren bir olay.

### <a name="token-service"></a>Belirteç hizmeti

Cihazlarınız için bir kimlik doğrulama mekanizması uygulamak üzere bir belirteç hizmeti kullanabilirsiniz. *Cihaz kapsamlı* belirteçler oluşturmak Için **deviceconnect** izinleriyle IoT Hub [paylaşılan erişim ilkesi](#shared-access-policy) kullanır. Bu belirteçler, bir cihazın IoT Hub 'ınıza bağlanmasını sağlar. Bir cihaz, belirteç hizmeti ile kimlik doğrulamak için özel bir kimlik doğrulama mekanizması kullanır. Cihaz başarıyla kimlik doğrulaması gerçekleştiriyorsa, belirteç hizmeti cihazın IoT Hub 'ınıza erişmek için kullanacağı bir SAS belirteci yayınlar.

### <a name="twin-graph-or-digital-twin-graph"></a>İkizi Graf (veya Digital ikizi Graf)

[Azure dijital TWINS](../digital-twins/index.yml) hizmetinde, tüm fiziksel ortamınızı dijital olarak temsil eden bilgi grafikleri oluşturmak için, [dijital TWINS](#digital-twin) 'i [ilişkilerle](#relationship) bağlayabilirsiniz. Tek bir [Azure dijital TWINS örneği](#azure-digital-twins-instance) , çok sayıda bağlantısı kesilen grafiği veya tek bağlantılı tek bir grafiği barındırabilirler.

### <a name="twin-queries"></a>İkizi sorguları

[Cihaz ve modül ikizi sorguları](../iot-hub/iot-hub-devguide-query-language.md) , cihaz ikiklerine veya modül TWINS 'inizden bilgi almak için SQL benzeri IoT Hub sorgu dilini kullanır. IoT Hub 'ınızda çalışan bir [iş](#job) hakkında bilgi almak için aynı IoT Hub sorgu dilini kullanabilirsiniz.

### <a name="twin-synchronization"></a>İkizi eşitleme

İkizi eşitleme, cihazlarınızı veya modüllerinizi yapılandırmak ve [bildirilen özellikleri](#reported-properties) ikizi içinde depolanacak şekilde almak için, cihazlarınızdaki veya modül TWINS 'de [istenen özellikleri](#desired-properties) kullanır.

## <a name="u"></a>U

### <a name="upstream-services"></a>Yukarı akış Hizmetleri

Verileri geçerli bağlamda akışa alan Hizmetleri açıklayan göreli bir terim. Örneğin, Azure dijital TWINS 'nin bağlamını düşünüyorsanız, veriler IoT Hub Azure dijital TWINS 'e akacağından IoT Hub bir yukarı akış hizmeti olarak kabul edilir.

## <a name="x"></a>X

### <a name="x509-client-certificate"></a>X. 509.440 istemci sertifikası

Bir cihaz, [IoT Hub](#iot-hub)kimlik doğrulaması yapmak Için bir X. 509.440 sertifikası kullanabilir. X. 509.440 sertifikası kullanmak [SAS belirteci](#shared-access-signature)kullanmanın bir alternatifidir.
