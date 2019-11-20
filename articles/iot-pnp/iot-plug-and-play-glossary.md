---
title: Terimler sözlüğü-IoT Tak ve Kullan önizlemesi | Microsoft Docs
description: Kavramlar-IoT Tak ve Kullan önizlemesiyle ilgili yaygın koşulların bir sözlüğü.
author: ChrisGMsft
ms.author: chrisgre
ms.date: 10/10/2019
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: 22b2cb8caa815b2ce8343a060a5f12133b763b83
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73890350"
---
# <a name="glossary-of-terms-for-iot-plug-and-play-preview"></a>IoT Tak ve Kullan önizlemesi için terimler sözlüğü

IoT Tak ve Kullan makalelerinde kullanılan yaygın terimlerin tanımları.

## <a name="azure-certified-for-iot-portal"></a>IoT portalı için Azure sertifikası

[IoT Için Azure Sertifikalı](https://aka.ms/ACFI) Web sitesini kullanarak şunları yapabilirsiniz:

- [Iot Tak ve kullan cihazınız](#iot-plug-and-play-device)için [sertifika sürecini](#device-certification) doldurun.
- [Cihaz yetenek modellerini](#device-capability-model)bulun.
- [Ortak model deposuna](#public-model-repository)bir cihaz yetenek modeli yayımlayın.

## <a name="azure-cli"></a>Azure CLI

Azure CLı, Azure kaynaklarını yönetmeye yönelik platformlar arası bir komut satırı aracıdır. Azure CLı için Azure IoT uzantısı, ile etkileşimde bulunmak ve [ıot Tak ve kullan cihazlarını](#iot-plug-and-play-device)test etmek için bir komut satırı aracıdır. Uzantıyı şu şekilde kullanabilirsiniz:

- IoT Tak ve Kullan cihazına bağlanın.
- Cihazın gönderdiği [Telemetriyi](#telemetry) görüntüleyin.
- Cihaz [özellikleriyle](#properties)çalışın.
- Çağrı cihazı [komutları](#commands).
- [Model depoları](#model-repository), [arabirimler](#interface)ve [cihaz yetenek modellerini](#device-capability-model)yönetin.

## <a name="azure-iot-central"></a>Azure IoT Central

Azure IoT Central, [ıot Tak ve kullan cihazlarınızı](#iot-plug-and-play-device)bağlamayı, izlemeyi ve yönetmeyi kolaylaştıran, tam olarak yönetilen bir hizmet olarak yazılım çözümüdür. Cihazlarınızı izlemek ve yönetmek için bir IoT Central uygulamasını otomatik olarak yapılandırmak üzere [cihaz yetenek modellerini](#device-capability-model) kullanabilirsiniz.

## <a name="azure-iot-certification-service"></a>Azure IoT sertifika hizmeti

Azure IoT sertifika hizmeti, [Azure Sertifikalı IoT portalı](#azure-certified-for-iot-portal)aracılığıyla sertifika Için bir [IoT Tak ve Kullan cihazı](#iot-plug-and-play-device) gönderdiğinizde bir sertifika testleri kümesi çalıştırır. [IoT cihaz kataloğuna sertifikalı](#certified-for-iot-device-catalog)bir cihaz ekleyebilmeniz için önce cihazın sertifikalı olması gerekir.

## <a name="azure-iot-tools-extension"></a>Azure IoT araçları uzantısı

Azure IoT araçları, [Visual Studio Code](#visual-studio-code) 'da IoT Hub etkileşime geçerek IoT cihazları geliştirmenize yardımcı olan bir uzantılar koleksiyonudur. IoT Tak ve Kullan cihaz geliştirme için şunları yapmanıza yardımcı olur:

- [Cihaz yetenek modellerini](#device-capability-model) ve [arabirimlerini](#interface)yazar.
- [Model depolarında](#model-repository)yayımlayın.
- Cihaz uygulamasını uygulamak için iskelet kodu oluşturun.

## <a name="azure-iot-explorer-tool"></a>Azure IoT gezgin aracı

Azure IoT Explorer, [ıot Tak ve kullan cihazlarınızla](#iot-plug-and-play-device)etkileşim kurmak ve test etmek için kullanabileceğiniz bir grafik aracıdır. Aracı yerel makinenize yükledikten sonra, şunları yapmak için kullanabilirsiniz:

- [IoT Hub 'ınıza](#azure-iot-hub)bağlı cihazları görüntüleyin.
- IoT Tak ve Kullan cihazına bağlanın.
- Cihazın gönderdiği [Telemetriyi](#telemetry) görüntüleyin.
- Cihaz [özellikleriyle](#properties)çalışın.
- Çağrı cihazı [komutları](#commands).

## <a name="azure-iot-hub"></a>Azure IoT Hub'ı

IoT Hub, bulutta barındırılan ve IoT uygulamanız ve yönettiği cihazlar arasındaki iki yönlü iletişim için merkezi ileti hub’ı görevi gören, yönetilen bir hizmettir. [Iot Tak ve kullan cihazları](#iot-plug-and-play-device) , IoT Hub 'ına bağlanabilir. IoT çözümü, şunları sağlamak için bir IoT Hub 'ı kullanır:

- Bulut tabanlı bir çözüme telemetri göndermek için cihazlar.
- Bağlı cihazları yönetmek için bulut tabanlı bir çözüm.

## <a name="azure-iot-device-sdk"></a>Azure IoT cihaz SDK 'Sı

IoT Tak ve Kullan cihaz istemci uygulamaları oluşturmak için kullanabileceğiniz birden çok dil için cihaz SDK 'Ları vardır. Cihaz [sertifikası](#device-certification) gereksinimlerinden biri, cihaz Istemci kodunun Azure IoT cihaz SDK 'larından birini kullanmasıdır.

## <a name="certified-for-iot-device-catalog"></a>IoT cihaz kataloğu için sertifikalı

[IoT cihaz kataloğu sertifikalı](https://catalog.azureiotsolutions.com/) , [cihaz sertifika](#device-certification) testlerini geçen [IoT Tak ve kullan cihazlarını](#iot-plug-and-play-device) listeler. Bu cihaz, katalogdaki IoT Tak ve Kullan cihazları için ve ortak model deposunda yayınlanan [yetenek modelleridir](#device-capability-model) .

## <a name="commands"></a>Komutlar

Bir [arabirimde](#interface) tanımlanan komutlar, [dijital ikizi](#digital-twin)yürütülebilecek yöntemleri temsil eder. Örneğin, bir cihazı yeniden başlatmak için bir komut.

## <a name="common-interface"></a>Ortak arabirim

Tüm [ıot Tak ve kullan cihazlarının](#iot-plug-and-play-device) bazı ortak [arabirimleri](#interface)uygulaması beklenir. Örneğin, cihaz bilgi arabirimi, cihaz ile ilgili donanım ve işletim sistemi bilgilerini tanımlar. [Cihaz sertifikası](#device-certification) , cihazınızın birkaç ortak arabirimi uygulamasını gerektirir. Ortak model deposundan ortak arabirim tanımlarını elde edebilirsiniz.

## <a name="company-model-repository"></a>Şirket modeli deposu

Bir kuruluş, [cihaz yeteneği modelleri](#device-capability-model) ve [arabirimleri](#interface)için özel bir mağaza olarak bir şirket [modeli deposu](#model-repository) kullanabilir.

## <a name="connection-string"></a>Bağlantı dizesi

Bir bağlantı dizesi, bir uç noktaya bağlanmak için gereken bilgileri kapsüller. Bağlantı dizesi genellikle uç nokta ve güvenlik bilgilerinin adresini içerir, ancak bağlantı dizesi biçimleri hizmetler arasında farklılık gösterir. IoT Hub hizmetiyle ilişkili iki tür bağlantı dizesi vardır:

- Cihaz bağlantı dizeleri IoT Hub 'ındaki cihaza yönelik uç noktalara bağlanmak için [ıot Tak ve kullan cihazları](#iot-plug-and-play-device) etkinleştirir. Bir cihazdaki istemci kodu, IoT Hub ile güvenli bir bağlantı kurmak için bağlantı dizesini kullanır.
- IoT Hub bağlantı dizeleri, bir IoT Hub 'ında hizmete yönelik uç noktalara güvenli bir şekilde bağlanmak için arka uç çözümlerini ve araçlarını etkinleştirir. Bu çözümler ve araçlar, IoT Hub 'ını ve ona bağlı olan cihazları yönetir.
- Şirket modeli deposu bağlantı dizeleri, bir [Şirket modeli deposuna](#company-model-repository)güvenli bir şekilde bağlanmak için arka uç çözümlerini ve araçlarını etkinleştirir. Bu çözümler ve araçlar depodaki [cihaz yeteneği modellerini](#device-capability-model) ve [arabirimlerini](#interface) kullanır veya yönetir.

## <a name="device-capability-model"></a>Cihaz yetenek modeli

Cihaz yetenek modeli, [ıot Tak ve kullan cihazını](#iot-plug-and-play-device) açıklar ve cihaz tarafından uygulanan [arabirimlerin](#interface) kümesini tanımlar. Bir cihaz yetenek modeli, genellikle fiziksel bir cihaza, ürüne veya SKU 'ya karşılık gelir. Bir cihaz yetenek modeli tanımlamak için [Digital Ikizi tanım dilini](#digital-twin-definition-language) kullanırsınız.

## <a name="device-certification"></a>Cihaz Sertifikası

Cihaz sertifikası, IoT [cihaz kataloğuna](#certified-for-iot-device-catalog) ve [cihaz yetenek modeline](#device-capability-model) ve genel modele eklenen [arabirimlere](#interface) eklenebilmeleri için bir [IoT Tak ve kullan cihazının](#iot-plug-and-play-device) sertifikalandırılması işlemidir [ Depo](#public-model-repository).

## <a name="device-developer"></a>Cihaz geliştirici

Bir cihaz geliştiricisi, [ıot Tak ve kullan cihazında](#iot-plug-and-play-device)çalıştırılacak kodu uygulamak için bir [cihaz yetenek modeli](#device-capability-model), [arabirimler](#interface)ve bir [Azure IoT cihaz SDK 'sı](#azure-iot-device-sdk) kullanır.

## <a name="device-modeling"></a>Cihaz modellemesi

Bir [cihaz geliştiricisi](#device-developer) , [IoT Tak ve kullan cihazının](#iot-plug-and-play-device)yeteneklerini modellemek Için [Digital ikizi tanım dilini](#digital-twin-definition-language) kullanır. Model bir model deposu kullanılarak paylaşılabilir. Bir cihaz geliştiricisi modelden iskelet cihaz kodu oluşturabilir. Bir [çözüm geliştiricisi](#solution-developer) , bir IoT çözümünü modelden yapılandırabilir.

## <a name="device-provisioning-service"></a>Cihaz sağlama hizmeti

[Azure IoT Central](#azure-iot-central) , tüm cihaz kaydı ve bağlantılarını yönetmek Için cihaz sağlama hizmetini kullanır. Daha fazla bilgi için bkz. [Azure IoT Central cihaz bağlantısı](../iot-central/preview/overview-iot-central-get-connected.md). Cihaz kaydı ve IoT Hub tabanlı IoT çözümünüze bağlantı yönetmek için cihaz sağlama hizmetini de kullanabilirsiniz. Daha fazla bilgi için bkz. [Azure IoT Hub cihaz sağlama hizmeti ile cihazları sağlama](../iot-dps/about-iot-dps.md).

## <a name="device-registration"></a>Cihaz kaydı

[Iot Tak ve kullan cihazının](#iot-plug-and-play-device) bir IoT çözümüne bağlanabilmesi için, çözüme kayıtlı olması gerekir. [Azure IoT Central](#azure-iot-central) cihaz kaydını yönetmek Için [cihaz sağlama hizmetini](#device-provisioning-service) kullanır. Özel bir IoT çözümünde, IoT Hub 'ınıza cihazları Azure portal veya program aracılığıyla kaydedebilirsiniz.

## <a name="device-first"></a>Cihaz-önce

[Azure IoT Central](#azure-iot-central) , bir cihazın ilk kaydını ve bağlantı senaryosunu destekler. Bu senaryoda, [ıot Tak ve Kullan bir cihaz](#iot-plug-and-play-device) bir IoT Central uygulamasına önceden kaydolmadan bağlanabilir. Bir cihaz uygulamaya ilk kez bağlandığında kayıt otomatik olarak gerçekleşir.

## <a name="digital-twin"></a>Dijital ikizi

Dijital ikizi, [ıot Tak ve kullan cihazının](#iot-plug-and-play-device)bir modelidir. Dijital bir ikizi, [dijital Ikizi tanım dili](#digital-twin-definition-language)kullanılarak modellenir. Çalışma zamanında dijital TWINS ile etkileşim kurmak için [Azure IoT cihaz SDK](#azure-iot-device-sdk) 'larını kullanabilirsiniz. Örneğin, bir cihazdaki dijital ikizi bir özellik değeri ayarlayabilirsiniz ve SDK bu değişikliği buluttaki IoT çözümünüz ile iletişim kurar.

## <a name="digital-twin-change-events"></a>Dijital ikizi değişiklik olayları

[Iot Tak ve Kullan cihazı](#iot-plug-and-play-device) bir [IoT Hub 'ına](#azure-iot-hub)bağlandığında, hub, dijital ikizi değişikliklerinin bildirimlerini göndermek için yönlendirme özelliğini kullanabilir. Örneğin, bir cihazdaki [özellik](#properties) değeri her değiştiğinde IoT Hub Service Bus kuyruğu gibi bir uç noktaya bildirim gönderebilir.

## <a name="digital-twin-definition-language"></a>Digital Ikizi tanım dili

[Iot Tak ve kullan cihazları](#iot-plug-and-play-device)için modelleri ve arabirimleri tanımlamaya yönelik bir dil. Dijital [Ikizi tanım dilini](https://aka.ms/DTDL) kullanarak bir [Digital ikizi 'ın](#digital-twin) yeteneklerini ve IoT platformunu ve IoT çözümlerini, varlığın semantiklerinden yararlanmak için etkinleştirin.

## <a name="digital-twin-route"></a>Dijital ikizi rotası

Bir [IoT Hub 'ında](#azure-iot-hub) , [dijital ikizi değişiklik olayları](#digital-twin-change-events) ve Service Bus kuyruğu gibi uç nokta sunmak için bir yol ayarlandı.

## <a name="interface"></a>Arayüz

Bir arabirim, [ıot Tak ve kullan cihazından](#iot-plug-and-play-device) veya [dijital ikizi](#digital-twin)tarafından uygulanan ilgili özellikleri açıklar. Arabirimleri farklı [cihaz yeteneği modelleri](#device-capability-model)genelinde yeniden kullanabilirsiniz.

## <a name="iot-hub-query-language"></a>IoT Hub sorgu dili

IoT Hub sorgu dili birden çok amaçla kullanılır. Örneğin, IoT Hub 'ınıza [kayıtlı cihazları](#device-registration) aramak veya [dijital ikizi yönlendirme](#digital-twin-route) davranışını daraltmak için dilini kullanabilirsiniz.

## <a name="iot-plug-and-play-device"></a>IoT Tak ve Kullan cihazı

IoT Tak ve Kullan cihazı genellikle veri toplayan veya diğer cihazları denetleyen ve [cihaz yetenek modeli](#device-capability-model)uygulayan yazılım veya bellenim çalıştıran bir küçük ölçekli, tek başına bilgi işlem aygıtıdır.  Örneğin, bir IoT Tak ve Kullan cihazı, bir çevre izleme cihazı veya bir akıllı tarım sulama sistemi denetleyicisi olabilir. Komut, denetim ve IoT Tak ve Kullan cihazlarındaki verileri almak için bulutta barındırılan bir IoT çözümü yazabilirsiniz. [IoT Için Azure Sertifikalı cihaz kataloğu](#certified-for-iot-device-catalog) , kullanılabilir IoT Tak ve kullan cihazları listeler. Katalogdaki her bir IoT Tak ve Kullan aygıtı doğrulandıktan sonra bir [cihaz yetenek modeli](#device-capability-model)vardır.

## <a name="microsoft-partner-center"></a>Microsoft Iş Ortağı Merkezi

[Microsoft Iş Ortağı Merkezi](https://docs.microsoft.com/partner-center/) , kuruluşunuzun uçtan uca ilişkilerini Microsoft ile yönetmektedir. IoT [Tak ve kullan cihazınızı](#iot-plug-and-play-device) [IoT portalındaki Azure sertifikası](#azure-certified-for-iot-portal)'nda doğrulayabilmeniz Için bir Microsoft iş ortağı Merkezi hesabına ihtiyacınız vardır.

## <a name="model-discovery"></a>Model keşfi

[Iot Tak ve Kullan cihazı](#iot-plug-and-play-device) bir IoT çözümüne bağlanırsa, çözüm cihaz [yetenek modelini](#device-capability-model)bularak cihazın yeteneklerini bulabilir. Bir cihaz, yetenek modelini çözüme gönderebilir veya çözüm bir [model deposunda](#model-repository)cihaz yetenek modeli bulabilir.

## <a name="model-repository"></a>Model deposu

Model deposu [cihaz yeteneği modellerini](#device-capability-model) ve [arabirimlerini](#interface)depolar. Tek bir [ortak model deposu](#public-model-repository)vardır. Kuruluşlar kendi kuruluş modeli depolamalarını oluşturabilir.

## <a name="model-repository-rest-api"></a>Model deposu REST API

Model depoları yönetmek ve bunlarla etkileşim kurmak için bir API. Örneğin, API 'yi [cihaz yetenek modelleri](#device-capability-model) eklemek ve yetenek modellerini aramak için kullanabilirsiniz.

## <a name="properties"></a>Özellikler

Özellikler, bir Digital ikizi 'ın bazı durumlarını temsil eden bir [arabirimde](#interface) tanımlanan veri alanlarıdır. Özellikleri salt okunurdur veya yazılabilir olarak bildirebilirsiniz. Seri numarası gibi salt yazılır özellikler, [ıot Tak ve kullan cihazının](#iot-plug-and-play-device) kendisi üzerinde çalışan kodla ayarlanır.  Bir alarm eşiği gibi yazılabilir özellikler genellikle bulut tabanlı IoT çözümünden ayarlanır.

## <a name="public-model-repository"></a>Ortak model deposu

[Sertifikalı cihazlar](#device-certification)için [cihaz yetenek modellerini](#device-capability-model) ve [arabirimlerini](#interface) depolayan tek bir ortak model deposu vardır. Ortak model deposu Ayrıca [ortak arabirim](#common-interface) tanımlarını depolar.

## <a name="registration-id"></a>Kayıt KIMLIĞI

Kayıt KIMLIĞI cihaz [sağlama hizmetindeki](#device-provisioning-service)bir cihazı benzersiz şekilde tanımlar. Bu KIMLIK, [IoT Hub 'ındaki](#azure-iot-hub)bir cihaz için benzersiz bir tanımlayıcı olan cihaz kimliğiyle aynı değildir.

## <a name="scope-id"></a>Kapsam KIMLIĞI

Kapsam KIMLIĞI kapsamı, bir [cihaz sağlama hizmeti](#device-provisioning-service) örneğini benzersiz bir şekilde tanımlar.

## <a name="shared-access-signature"></a>Paylaşılan erişim imzası

Paylaşılan erişim imzaları, SHA-256 güvenli karmaları veya URI 'Leri temel alan bir kimlik doğrulama mekanizmasıdır. Paylaşılan erişim imzası kimlik doğrulaması iki bileşene sahiptir: paylaşılan erişim ilkesi ve paylaşılan erişim imzası (genellikle belirteç olarak adlandırılır). [Iot Tak ve Kullan cihazı](#iot-plug-and-play-device) , [IoT Hub](#azure-iot-hub)ile kimlik doğrulamak için paylaşılan erişim imzasını kullanır.

## <a name="solution-developer"></a>Çözüm geliştiricisi

Çözüm geliştiricisi, çözüm arka ucu oluşturur. Çözüm geliştiricisi, genellikle [IoT Hub](#azure-iot-hub) ve [model depoları](#model-repository)gibi Azure kaynaklarıyla birlikte çalışarak, [IoT Central](#azure-iot-central)ile birlikte çalışabilir.

## <a name="telemetry"></a>Telemetri

Bir [arabirimde](#interface) tanımlanan telemetri alanları ölçümleri temsil eder. Bu ölçümler genellikle [ıot Tak ve kullan cihazının](#iot-plug-and-play-device) veri akışı olarak gönderdiği algılayıcı ayarları gibi değerlerdir.

## <a name="visual-studio-code"></a>Visual Studio kodu

Visual Studio Code, birden çok platform için kullanılabilen modern bir kod düzenleyicisidir. [Azure IoT araçları](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) paketinde bulunanlar gibi uzantılar, düzenleyici 'yi çok çeşitli geliştirme senaryolarını destekleyecek şekilde özelleştirmenize olanak tanır.
