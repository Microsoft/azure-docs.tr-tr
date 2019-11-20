---
title: Azure IoT Central nedir? | Microsoft Docs
description: Azure IoT Central, IoT çözümlerinin oluşturulmasını kolaylaştıran ve IoT yönetim işlemlerinin ve geliştirmenin yükünü ve maliyetini azaltmaya yardımcı olan bir IoT uygulama platformudur. Bu makalede Azure IoT Central’ın özelliklerine genel bir bakış sunulmaktadır.
author: dominicbetts
ms.author: dobett
ms.date: 11/12/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: timlt
ms.openlocfilehash: 5e84b8777fc7671a19b6d8974f1309eb5af35bd3
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74048024"
---
# <a name="what-is-azure-iot-central-preview-features"></a>Azure IoT Central (Önizleme özellikleri) nedir?

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

> [!WARNING]
> Azure IoT Central [ıot Tak ve kullan](../../iot-pnp/overview-iot-plug-and-play.md) özellikleri şu anda genel önizlemededir. Üretim iş yükleri için bir IoT Tak ve Kullan özellikli IoT Central [uygulama şablonu](../core/concepts-app-templates.md?toc=/azure/iot-central/preview/toc.json&bc=/azure/iot-central/preview/breadcrumb/toc.json) kullanmayın. Üretim ortamları için, geçerli, genel olarak kullanılabilir bir [uygulama şablonundan](../core/concepts-app-templates.md?toc=/azure/iot-central/preview/toc.json&bc=/azure/iot-central/preview/breadcrumb/toc.json)oluşturulan bir IoT Merkezi uygulaması kullanın.

IoT Central, kurumsal düzeyde IoT çözümlerinin geliştirilmesine, yönetilmesine ve bakımının yükünü ve maliyetini azaltan bir IoT uygulama platformudur. IoT Central ile oluşturmayı seçtiğinizde, yalnızca karmaşık ve sürekli gelişen IoT altyapısını korumak ve güncelleştirmek yerine, işletmenizin IoT verileriyle dönüştürülmesi için zaman, para ve enerji tasarrufu sağlama fırsatı sunulmaktadır.

Web Kullanıcı arabirimi, cihaz koşullarını izlemenizi, kurallar oluşturmanızı ve milyonlarca cihazı ve bunların yaşam döngüsü boyunca verilerini yönetmenizi sağlar. Ayrıca, IoT Intelligence 'ı iş kolu uygulamalarına genişleterek cihaz öngörülerine göre hareket etmenizi sağlar.

Bu makalede IoT Central için özetlenmektedir:

- Bir projeyle ilişkili tipik kişilikler.
- Uygulamanızı oluşturma.
- Cihazlarınızı uygulamanıza bağlama
- Uygulamanızı yönetme.
- IoT Central Azure IoT Edge özellikleri.
- Azure IoT Edge çalışma zamanı ile desteklenen cihazlarınızı uygulamanıza bağlama.

## <a name="known-issues"></a>Bilinen sorunlar

> [!Note]
> Bu bilinen sorunlar yalnızca IoT Central önizleme uygulamaları için geçerlidir.

- Kurallar tüm eylemleri desteklemez (yalnızca e-posta).
- Karmaşık türler için-kurallar, analiz ve cihaz grupları desteklenmez.
- Sürekli veri dışa aktarma avro biçimini (uyumsuzluk) desteklemez.
- Sanal cihazlar tüm karmaşık türleri desteklemez.
- GeoJSON Şu anda desteklenmiyor.
- Harita kutucuğu Şu anda desteklenmiyor.
- İşler karmaşık türleri desteklemez.
- Dizi şeması türleri desteklenmez.
- Uygulama şablonu dışarı aktarma ve uygulama kopyalama desteklenmez.
- Yalnızca C cihaz SDK 'Sı ve Node. js cihaz ve hizmet SDK 'Ları desteklenir.
- Yalnızca Kuzey Avrupa ve Orta ABD bölgeleri mevcuttur.
- Cihaz yetenek modelleri aynı dosyada satır içi tanımlanmış tüm arabirimlere sahip olmalıdır.

## <a name="personas"></a>Kişilikler

IoT Central belgeler, IoT Central bir uygulamayla etkileşime geçen dört kişilik anlamına gelir:

- _Çözüm Oluşturucu_ , uygulamaya bağlanan ve uygulamayı operatör için özelleştiren cihazların türlerini tanımlamaktan sorumludur.
- _Operatör_, uygulamaya bağlı cihazları yönetir.
- _Yönetici_ , uygulama içindeki [Kullanıcı rollerini ve izinleri](howto-administer.md) yönetme gibi yönetim görevlerinden sorumludur.
- Bir _cihaz geliştiricisi_ , uygulamanıza bağlı bir cihazda veya IoT Edge modülde çalışan kodu oluşturur.

## <a name="create-your-iot-central-application"></a>IoT Central uygulamanızı oluşturma

Bir çözüm Oluşturucusu olarak, kuruluşunuz için özel, bulutta barındırılan bir IoT çözümü oluşturmak üzere IoT Central kullanırsınız. Özel bir IoT çözümü genellikle aşağıdakilerden oluşur:

- Cihazlarınızdan telemetri alan ve bu cihazları yönetmenizi sağlayan bulut tabanlı bir uygulama.
- Bulut tabanlı uygulamanıza bağlı özel kod çalıştıran birden fazla cihaz.

Yeni bir IoT Central uygulamasını hızlıca dağıtabilir ve sonra tarayıcınızda özel gereksinimlerinize göre özelleştirebilirsiniz. Bir çözüm Oluşturucusu olarak, uygulamanıza bağlanan cihazlar için bir _cihaz şablonu_ oluşturmak üzere Web tabanlı araçları kullanın. Cihaz şablonu, şöyle bir cihaz türünün özelliklerini ve davranışını tanımlayan şema ' dir:

- Gönderdiği telemetri.
- Bir operatörün değiştirebileceği iş özellikleri.
- Bir cihaz tarafından ayarlanan ve uygulamada salt okunur özellikte olan cihaz özellikleri.
- Bir işleç kümelerinin, cihazın davranışını belirleyen özellikler.

Bu cihaz şablonu şunları içerir:

- Bir cihazın gönderdiği telemetri ve rapor aldığı özellikler gibi uygulaması gereken özellikleri açıklayan bir _cihaz yetenek modeli_ .
- Cihazda depolanmayan bulut özellikleri.
- IoT Central uygulamanızın parçası olan özelleştirmeler, panolar ve formlar.

### <a name="create-device-templates"></a>Cihaz şablonları oluşturma

[Iot Tak ve kullan](../../iot-pnp/overview-iot-plug-and-play.md) , hiçbir katıştırılmış cihaz kodu yazmadan IoT Central cihazları tümleştirmenize olanak sağlar. IoT Tak ve Kullan 'nin çekirdeğinden, cihaz yeteneklerini tanımlayan bir cihaz yetenek modeli şemadır. IoT Central önizleme uygulamasında, cihaz şablonları bu IoT Tak ve Kullan cihaz yeteneği modellerini kullanır.

Bir çözüm Oluşturucusu olarak, cihaz şablonları oluşturmak için çeşitli seçenekleriniz vardır:

- IoT Central cihaz şablonunu tasarlayın ve cihaz kodunuzda cihaz yetenek modelini uygulayın.
- [IoT cihaz kataloğu Için Azure Sertifikalı](https://aka.ms/iotdevcat) bir cihaz yetenek modeli içeri aktarın ve ardından IoT Central uygulamanızın ihtiyaç duyacağı tüm bulut özelliklerini, özelleştirmeleri ve panoları ekleyin.
- Visual Studio Code 'u kullanarak bir cihaz yetenek modeli oluşturun. Modelden cihaz kodunuzu uygulayın ve cihazınızı IoT Central uygulamanıza bağlayın. IoT Central, cihaz yetenek modelini bir depodan bulur ve sizin için basit bir cihaz şablonu oluşturur.
- Visual Studio Code 'u kullanarak bir cihaz yetenek modeli oluşturun. Modelden cihaz kodunuzu uygulayın. Cihaz yetenek modelini IoT Central uygulamanıza el ile aktarın ve ardından IoT Central uygulamanızın ihtiyaç duyacağı tüm bulut özelliklerini, özelleştirmeleri ve panoları ekleyin.

Bir çözüm Oluşturucusu olarak, cihaz şablonlarınızı doğrulamak üzere test cihazları için kod oluşturmak üzere IoT Central kullanabilirsiniz.

### <a name="customize-the-ui"></a>Kullanıcı arabirimini özelleştirme

Bir çözüm Oluşturucusu olarak, uygulamanın günlük kullanımıyla sorumlu operatörler için IoT Central uygulama kullanıcı arabirimini de özelleştirebilirsiniz. Bir çözüm oluşturucusunun yapaiçereme özelleştirmeleri şunlardır:

- Bir cihaz şablonundaki özellik ve ayarların düzenini tanımlama.
- Operatörlerin içgörüleri keşfetmesine ve sorunları daha hızlı çözümlemesine yardımcı olacak özel panoları yapılandırma.
- Bağlı cihazlarınızdan zaman serisi verilerini keşfetmek için özel analizler yapılandırma.

## <a name="connect-your-devices"></a>Cihazlarınızı bağlama

Oluşturucunun uygulamaya bağlanabilen cihaz türlerini tanımlamasından sonra, cihaz geliştiricisi cihazlar üzerinde çalıştırılacak kodu oluşturur. Cihaz geliştiricisi olarak, cihaz kodunuzu oluşturmak için Microsoft'un açık kaynak [Azure IoT SDK’larını](https://github.com/Azure/azure-iot-sdks) kullanırsınız. Bu SDK 'lar, cihazlarınızı IoT Central uygulamanıza bağlamak için gereksinimlerinizi karşılayacak geniş dil, platform ve protokol desteğine sahiptir. SDK 'lar aşağıdaki cihaz yeteneklerini uygulamanıza yardımcı olur:

- Güvenli bir bağlantı oluşturma.
- Telemetri gönderme.
- Durum bildirme.
- Yapılandırma güncelleştirmeleri alma.

Daha fazla bilgi için, [Azure IoT SDK’larını kullanmanın avantajları ve kullanmamanız durumunda kaçınılması gereken tuzaklar](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/) başlıklı blog gönderisine bakın.

### <a name="azure-iot-edge-devices"></a>Azure IOT Edge cihazları

Ayrıca, [Azure IoT SDK 'ları](https://github.com/Azure/azure-iot-sdks)kullanılarak oluşturulan cihazların yanı sıra, [Azure IoT Edge cihazlarını](../../iot-edge/about-iot-edge.md) bir IoT Central uygulamasına da bağlayabilirsiniz. Azure IoT Edge, IoT Central tarafından yönetilen IoT cihazlarında doğrudan bulut zekasını ve özel mantık çalıştırmanızı sağlar. IoT Edge çalışma zamanı şunları yapmanızı sağlar:

- Cihaza iş yüklerini yükleyip güncelleştirin.
- Cihazda Azure IoT Edge güvenlik standartlarının bakımını yapın.
- IoT Edge modüllerinin her zaman çalıştığından emin olun.
- Uzaktan izleme için modül durumunu buluta bildirin.
- Bir IoT Edge cihazdaki modüller arasında ve bir IoT Edge cihaz ile bulut arasında aşağı akış yaprak cihazları ve bir IoT Edge cihazı arasındaki iletişimi yönetin.

Daha fazla bilgi için bkz. [Azure IoT Edge cihazlar ve IoT Central](./concepts-architecture.md#azure-iot-edge-devices).

## <a name="manage-your-application"></a>Uygulamanızı yönetme

IoT Central uygulamalar Microsoft tarafından tamamen barındırılır ve uygulamalarınızın yönetilmesi için yönetim yükünü azaltır.

Bir operatör olarak, IoT Central çözümünüzdeki cihazları yönetmek için IoT Central uygulamasını kullanırsınız. İşleçler gibi görevler şunlardır:

- Uygulamaya bağlı cihazları izleme.
- Cihazlarla ilgili sorunları giderme ve düzeltme.
- Yeni cihazlar hazırlama.

Bir çözüm Oluşturucusu olarak, bağlı cihazlardan veri akışı üzerinde çalışan özel kurallar ve eylemler tanımlayabilirsiniz. Operatör ise uygulama içindeki görevleri denetlemek ve otomatik hale getirmek için cihaz düzeyinde bu kuralları etkinleştirebilir ya da devre dışı bırakabilir.

Yöneticiler [, Kullanıcı rolleri ve izinleriyle](howto-administer.md)uygulamanıza erişimi yönetir.

## <a name="next-steps"></a>Sonraki adımlar

Artık IoT Central bir genel bakışın olduğuna göre, aşağıdaki adımlar önerilir:

- [IoT Central Ile Azure IoT Çözüm Hızlandırıcıları](../core/overview-iot-options.md?toc=/azure/iot-central/preview/toc.json&bc=/azure/iot-central/preview/breadcrumb/toc.json)arasındaki farkları anlayın.
- [Azure IoT Central Kullanıcı Arabirimi](overview-iot-central-tour.md)’ni tanıma.
- [Azure IoT Central uygulaması oluşturmaya](quick-deploy-iot-central.md) bağlama.
- [Iot Tak ve kullan](../../iot-pnp/overview-iot-plug-and-play.md) hakkında daha fazla bilgi edinin
- [Azure IoT Edge cihaz şablonu oluşturmayı](./tutorial-define-edge-device-type.md) öğrenin
