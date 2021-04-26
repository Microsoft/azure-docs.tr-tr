---
title: Azure IoT Central nedir? | Microsoft Docs
description: Azure IoT Central, IoT çözümlerinin oluşturulmasını kolaylaştıran ve IoT yönetim işlemlerinin ve geliştirmenin yükünü ve maliyetini azaltmaya yardımcı olan bir IoT uygulama platformudur. Bu makalede Azure IoT Central’ın özelliklerine genel bir bakış sunulmaktadır.
author: dominicbetts
ms.author: dobett
ms.date: 04/19/2021
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc, contperf-fy21q2
ms.openlocfilehash: 88f59c1b3fc1014cef5035845f1f2e8616bea908
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739917"
---
# <a name="what-is-azure-iot-central"></a>Azure IoT Central nedir?

IoT Central, kurumsal düzeyde IoT çözümlerinin geliştirilmesine, yönetilmesine ve bakımının yapılmasına yönelik maliyet ve yükü azaltan bir IoT uygulama platformudur. IoT Central ile oluşturmayı seçtiğinizde, yalnızca karmaşık ve sürekli gelişen IoT altyapısını korumak ve güncelleştirmek yerine, işletmenizin IoT verileriyle dönüştürülmesi için zaman, para ve enerji tasarrufu sağlama fırsatı sunulmaktadır.

Web Kullanıcı arabirimi, cihazları Hızlı bir şekilde bağlamanıza, cihaz koşullarını izlemenize, kurallar oluşturmanıza ve milyonlarca cihazı ve verilerini yaşam döngüsü boyunca yönetmenize olanak sağlar. Ayrıca, IoT Intelligence 'ı iş kolu uygulamalarına genişleterek cihaz öngörülerine göre hareket etmenizi sağlar.

Bu makalede IoT Central için özetlenmektedir:

- Bir projeyle ilişkili tipik Kullanıcı rolleri.
- Uygulamanızı oluşturma.
- Cihazlarınızı uygulamanıza bağlama
- Uygulamanızı yönetme.
- IoT Central Azure IoT Edge özellikleri.
- Azure IoT Edge çalışma zamanı ile desteklenen cihazlarınızı uygulamanıza bağlama.

## <a name="user-roles"></a>Kullanıcı rolleri

IoT Central belgeler, bir IoT Central uygulamayla etkileşime geçen dört kullanıcı rolüne başvurur:

- Bir _çözüm Oluşturucusu_ , [uygulama oluşturmaktan](quick-deploy-iot-central.md), [kuralları ve eylemleri yapılandırmadan](quick-configure-rules.md), [diğer hizmetlerle tümleştirmelere tanımlamayı](howto-export-data.md)ve işleçler ve cihaz geliştiricileri için uygulamayı daha fazla özelleştirmeyi sağlamaktan sorumludur.
- Bir _işleç_ , uygulamaya bağlı [aygıtları yönetir](howto-manage-devices.md) .
- _Yönetici_ , uygulama içindeki [Kullanıcı rollerini ve izinleri](howto-administer.md) yönetme gibi yönetim görevlerinden sorumludur.
- Bir _cihaz geliştiricisi_ , uygulamanıza bağlı bir cihazda veya [IoT Edge modülde](concepts-iot-edge.md) [çalışan kodu oluşturur](concepts-telemetry-properties-commands.md) .

## <a name="create-your-iot-central-application"></a>IoT Central uygulamanızı oluşturma

Yeni bir IoT Central uygulamasını hızlıca dağıtabilir ve ardından özel gereksinimlerinize göre özelleştirebilirsiniz. Genel bir _uygulama şablonuyla_ veya sektör odaklı uygulama şablonlarından biriyle başlayın:

- [Perakende](../retail/overview-iot-central-retail.md)
- [Enerji](../energy/overview-iot-central-energy.md)
- [Kamu](../government/overview-iot-central-government.md)
- [Sağlık](../healthcare/overview-iot-central-healthcare.md).

İlk uygulamanızı nasıl oluşturacağınız hakkında daha fazla bilgi için bkz. [Yeni uygulama](quick-deploy-iot-central.md) hızlı başlangıcı oluşturma.

## <a name="connect-devices"></a>Cihazları bağlama

Uygulamanızı oluşturduktan sonra ilk adım cihazları oluşturmak ve bağlamak için kullanılır. IoT Central bağlı her cihaz bir _cihaz şablonu_ kullanır. Cihaz şablonu, şöyle bir cihaz türünün özelliklerini ve davranışını tanımlayan şema ' dir:

- Gönderdiği telemetri. Sıcaklık ve nem örnekleri verilebilir. Telemetri veri akışı.
- Bir operatörün değiştirebileceği iş özellikleri. Örneğin, bir müşteri adresi ve son hizmet verilen tarih verilebilir.
- Bir cihaz tarafından ayarlanan ve uygulamada salt okunur özellikte olan cihaz özellikleri. Örneğin, bir vanaın açık ya da kapatma olarak durumu.
- Bir işleç kümelerinin, cihazın davranışını belirleyen özellikler. Örneğin, cihaz için bir hedef sıcaklık.
- Bir işlecin, bir cihazda çalışan, çağırabilirler. Örneğin, bir cihazı uzaktan yeniden başlatmak için bir komut.

Her [cihaz şablonu](howto-set-up-template.md) şunları içerir:

- Bir cihazın uygulaması gereken özellikleri açıklayan bir _cihaz modeli_ . Cihaz özellikleri şunları içerir:

  - IoT Central için akış yaptığı telemetri.
  - Durumu IoT Central raporlamak için kullandığı salt okunurdur özellikleri.
  - Cihaz durumunu ayarlamak için IoT Central aldığı yazılabilir Özellikler.
  - IoT Central ' den çağrılan komutlar.

- Cihazda depolanmayan bulut özellikleri.
- IoT Central uygulamanızın parçası olan özelleştirmeler, panolar ve formlar.

Cihaz şablonları oluşturmak için çeşitli seçenekleriniz vardır:

- IoT Central cihaz şablonunu tasarlayın ve cihaz kodunuzda cihaz modelini uygulayın.
- Visual Studio Code 'u kullanarak bir cihaz modeli oluşturun ve modeli bir depoya yayımlayın. Modelden cihaz kodunuzu uygulayın ve cihazınızı IoT Central uygulamanıza bağlayın. IoT Central, depodan cihaz modelini bulur ve sizin için basit bir cihaz şablonu oluşturur.
- Visual Studio Code 'u kullanarak bir cihaz modeli oluşturun. Modelden cihaz kodunuzu uygulayın. Cihaz modelini IoT Central uygulamanıza el ile aktarın ve ardından IoT Central uygulamanızın ihtiyaç duyacağı tüm bulut özelliklerini, özelleştirmeleri ve panoları ekleyin.

İlk cihazınızı oluşturma ve bağlama hakkında daha fazla bilgi için [sanal cihaz ekleme](quick-create-simulated-device.md) hızlı başlangıç bölümüne bakın.

### <a name="customize-the-ui"></a>Kullanıcı arabirimini özelleştirme

Uygulamanın günlük kullanımı için sorumlu olan operatörler için IoT Central uygulama kullanıcı arabirimini de özelleştirebilirsiniz. Yapabileceğiniz özelleştirmeler şunlar olabilir:

- Operatörlerin içgörüleri keşfetmesine ve sorunları daha hızlı çözümlemesine yardımcı olacak özel panoları yapılandırma.
- Bağlı cihazlarınızdan zaman serisi verilerini keşfetmek için özel analizler yapılandırma.
- Bir cihaz şablonundaki özellik ve ayarların düzenini tanımlama.

## <a name="manage-your-devices"></a>Cihazlarınızı yönetme

Bir operatör olarak, IoT Central çözümünüzdeki [cihazları yönetmek](howto-manage-devices.md) için IoT Central uygulamasını kullanırsınız. İşleçler gibi görevler şunlardır:

- Uygulamaya bağlı cihazları izleme.
- Cihazlarla ilgili sorunları giderme ve düzeltme.
- Yeni cihazlar hazırlama.

Bağlı cihazlardan veri akışı üzerinde çalışan [özel kurallar ve eylemler tanımlayabilirsiniz](howto-configure-rules.md) . Operatör ise uygulama içindeki görevleri denetlemek ve otomatik hale getirmek için cihaz düzeyinde bu kuralları etkinleştirebilir ya da devre dışı bırakabilir.

Ölçekli olarak çalışacak şekilde tasarlanan tüm IoT çözümünde olduğu gibi, cihaz yönetimine yönelik yapılandırılmış bir yaklaşım da önemlidir. Cihazlarınızı buluta bağlamak yeterli değildir, cihazlarınızı bağlı ve sağlıklı tutmanız gerekir. Aşağıdaki IoT Central yeteneklerini kullanarak cihazlarınızı uygulama yaşam döngüsü boyunca yönetin:

### <a name="dashboards"></a>Panolar

Yerleşik [panolar](./howto-set-up-template.md#generate-default-views) cihaz sistem durumunu ve telemetrisini izlemek için özelleştirilebilir bir kullanıcı arabirimi sağlar. [Uygulama şablonunda](howto-use-app-templates.md) önceden oluşturulmuş bir panoyla başlayın veya işleçlerinizin ihtiyaçlarına göre özel kendi panolarınızı oluşturun. Panoları uygulamanızdaki tüm kullanıcılarla paylaşabilir veya özel tutabilirsiniz.

### <a name="rules-and-actions"></a>Kurallar ve eylemler

Dikkat edilmesi gereken cihazları tanımlamak için cihaz durumu ve telemetri temelinde [özel kurallar](tutorial-create-telemetry-rules.md) oluşturun. Doğru kişilere bildirimde bulunan eylemleri yapılandırın ve düzeltici ölçülerin zamanında alındığından emin olun.

### <a name="jobs"></a>İşler

[İşler](howto-run-a-job.md) , özellikleri ayarlayarak veya komutları çağırarak cihazlara tek veya toplu güncelleştirmeler uygulamanıza olanak tanır.

## <a name="integrate-with-other-services"></a>Diğer hizmetlerle tümleştirme

Bir uygulama platformu olarak, IoT Central IoT verilerinizi, işlem yapılabilir sonuçları sağlayan iş öngörülerine dönüştürmenize olanak tanır. [Kurallar](./tutorial-create-telemetry-rules.md), [veri dışa aktarma](./howto-export-data.md)ve [genel REST API](/learn/modules/manage-iot-central-apps-with-rest-api/) , IoT Central iş kolu uygulamalarıyla nasıl tümleştirebileceğinizi gösteren örneklerdir:

![IoT verilerinizi nasıl dönüştürebileceğinizi IoT Central](media/overview-iot-central/transform.png)

Cihazlarınızdan Telemetriyi işlemek ve sonuçları depolamak için özel analiz işlem hatları oluşturarak makine verimlilik eğilimlerini belirleme veya bir fabrika katında gelecekteki enerji kullanımını tahmin etme gibi iş öngörüleri oluşturabilirsiniz. Tercih ettiğiniz araçlarla verileri analiz edebilirsiniz, depolayabileceğiniz ve görselleştirebileceğiniz diğer hizmetlere telemetri, cihaz özelliği değişikliklerini ve cihaz şablonu değişikliklerini dışarı aktarmak için IoT Central uygulamanızdaki veri dışarı aktarmaları yapılandırın.

### <a name="build-custom-iot-solutions-and-integrations-with-the-rest-apis"></a>REST API 'Leriyle özel IoT çözümleri ve tümleştirmeler oluşturun

Şu gibi IoT çözümleri oluşturun:

- Cihazları uzaktan ayarlayıp denetleyebileceği mobil yardımcı uygulamalar.
- Mevcut iş kolu uygulamalarının IoT cihazlarınızla ve verilerinizle etkileşime geçmesini sağlayan özel tümleştirmeler.
- Cihaz modellemesi, ekleme, yönetim ve veri erişimi için cihaz yönetimi uygulamaları.

## <a name="administer-your-application"></a>Uygulamanızı yönetme

IoT Central uygulamalar Microsoft tarafından tamamen barındırılır ve uygulamalarınızın yönetilmesi için yönetim yükünü azaltır. Yöneticiler [, Kullanıcı rolleri ve izinleriyle](howto-administer.md)uygulamanıza erişimi yönetir.

## <a name="pricing"></a>Fiyatlandırma

7 günlük ücretsiz deneme sürümünü kullanarak IoT Central uygulaması oluşturabilir veya standart bir fiyatlandırma planı kullanabilirsiniz.

- *Ücretsiz* planı kullanarak oluşturduğunuz uygulamalar yedi gün boyunca ücretsizdir ve en fazla beş cihaz destekler. Bunları, süresi dolmadan önce istediğiniz zaman standart bir fiyatlandırma planı kullanacak şekilde dönüştürebilirsiniz.
- *Standart* planı kullanarak oluşturduğunuz uygulamalar cihaz başına alınarak faturalandırılır, ilk iki cihazla serbest bırakılmakta olan **Standart 0**, **Standart 1** veya **Standart 2** fiyatlandırma planı arasından seçim yapabilirsiniz. [IoT Central fiyatlandırması](https://aka.ms/iotcentral-pricing)hakkında daha fazla bilgi edinin.

## <a name="quotas"></a>Kotalar

Her Azure aboneliğinin, IoT çözümünüzün kapsamını etkileyebilecek varsayılan kotaları vardır. Şu anda IoT Central bir abonelikte dağıtabileceğiniz uygulama sayısını 10 ' a kısıtlar. Bu sınırı artırmanız gerekiyorsa, [Microsoft desteği](https://azure.microsoft.com/support/options/)'ne başvurun.

## <a name="known-issues"></a>Bilinen sorunlar

- Sürekli veri dışa aktarma avro biçimini (uyumsuzluk) desteklemez.
- GeoJSON Şu anda desteklenmiyor.
- Harita kutucuğu Şu anda desteklenmiyor.
- Dizi şeması türleri desteklenmez.
- Yalnızca C cihaz SDK 'Sı ve Node.js cihaz ve hizmet SDK 'Ları desteklenir.
- IoT Central Şu anda Birleşik Devletler, Avrupa, Asya Pasifik, Avustralya, Birleşik Krallık ve Japonya konumlarında sunulmaktadır.

## <a name="next-steps"></a>Sonraki adımlar

Artık IoT Central bir genel bakışın olduğuna göre, aşağıda önerilen bazı adımlar verilmiştir:

- Bir cihaz geliştiricisiyseniz ve bazı kodlara gitmek istiyorsanız, önerilen sonraki adım, [bir istemci uygulamasını oluşturmak ve Azure IoT Central uygulamanıza bağlamak](./tutorial-connect-device.md)için kullanılır.
- [Azure IoT Central Kullanıcı Arabirimi](overview-iot-central-tour.md)’ni tanıma.
- [Azure IoT Central uygulaması oluşturmaya](quick-deploy-iot-central.md) bağlama.
