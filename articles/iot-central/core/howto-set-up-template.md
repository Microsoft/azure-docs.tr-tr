---
title: Azure IoT Central yeni bir IoT cihaz türü tanımlayın | Microsoft Docs
description: Bu makalede, bir çözüm Oluşturucu olarak Azure IoT Central uygulamanızda yeni bir Azure IoT cihaz şablonu oluşturma yöntemi gösterilmektedir. Türü için telemetri, durum, özellik ve komutları tanımlarsınız.
author: dominicbetts
ms.author: dobett
ms.date: 12/06/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom:
- contperf-fy21q1
- device-developer
ms.openlocfilehash: 9ef4be54f5c6025a09314a04504cff40fc39478d
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106168170"
---
# <a name="define-a-new-iot-device-type-in-your-azure-iot-central-application"></a>Azure IoT Central uygulamanızda yeni bir cihaz türü tanımlama

*Bu makale, çözüm oluşturucular ve cihaz geliştiricileri için geçerlidir.*

Bir cihaz şablonu, bir [Azure IoT Central uygulamasına](concepts-app-templates.md)bağlanan bir cihaz türünün özelliklerini ve davranışlarını tanımlayan bir şema.

Örneğin, bir Oluşturucu, bağlı bir fan için aşağıdaki özelliklere sahip bir cihaz şablonu oluşturabilir:

- Sıcaklık telemetrisini gönderir
- Location özelliğini gönderir
- Fan Motoru hata olaylarını gönderir
- Fan çalışma durumunu gönderir
- Yazılabilir fan hızı özelliği sağlar
- Cihazı yeniden başlatmak için bir komut sağlar
- Bir görünüm kullanarak cihazın genel görünümünü sağlar

Bu cihaz şablonundan bir operatör gerçek fan cihazları oluşturup bağlayabilirler. Tüm bu fanların ölçümleri, özellikleri ve operatörlerin bunları izlemek ve yönetmek için kullandığı komutları vardır. İşleçler, fan cihazlarıyla etkileşim kurmak için [cihaz görünümlerini](#add-views) ve formlarını kullanır. Bir cihaz geliştiricisi, cihazın uygulamayla nasıl etkileşime gireceğini anlamak için şablonu kullanır. Daha fazla bilgi için bkz. [telemetri, özellik ve komut yükleri](concepts-telemetry-properties-commands.md).

> [!NOTE]
> Yalnızca oluşturucular ve yöneticiler cihaz şablonları oluşturabilir, düzenleyebilir ve silebilir. Herhangi bir Kullanıcı, mevcut cihaz şablonlarından **cihazlar** sayfasında cihaz oluşturabilir.

Bir IoT Central uygulamasında cihaz şablonu, bir cihazın yeteneklerini betimleyen bir cihaz modeli kullanır. Bir Oluşturucu olarak, cihaz şablonları oluşturmak için çeşitli seçenekleriniz vardır:

- IoT Central cihaz şablonunu tasarlayın ve cihaz [kodunuzda cihaz modelini uygulayın](concepts-telemetry-properties-commands.md).
- [IoT cihaz kataloğu Için Azure Sertifikalı](https://aka.ms/iotdevcat)bir cihaz şablonu içeri aktarın. Cihaz şablonunu IoT Central gereksinimlerinize göre özelleştirin.
> [!NOTE]
> IoT Central, model deposundan bir modeli içeri aktardığınızda tam sürümü almak için "genişletilmiş" anahtar sözcüğünü kullanarak tam modeli aynı dosyada bulunan tüm başvurulan arabirimlere gerektirir.
Örneğin. https://devicemodels.azure.com/dtmi/com/example/thermostat-1.expanded.json

- [Dijital TWINS tanım dili (DTDL)-sürüm 2](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)kullanarak bir cihaz modeli yazar. Visual Studio Code, DTDL modellerini yazmayı destekleyen bir uzantıya sahiptir. Daha fazla bilgi için bkz. [DTDL yazma araçlarını yüklemek ve kullanmak](../../iot-pnp/howto-use-dtdl-authoring-tools.md). Ardından modeli ortak model deposuna yayımlayın. Daha fazla bilgi için bkz. [cihaz modeli deposu](../../iot-pnp/concepts-model-repository.md). Modelden cihaz kodunuzu uygulayın ve gerçek cihazınızı IoT Central uygulamanıza bağlayın. IoT Central, cihaz modelini sizin için ortak depodan bulur ve içeri aktarır ve bir cihaz şablonu oluşturur. Daha sonra, IoT Central uygulamanızın ihtiyaçlarını cihaz şablonuna eklemek için tüm bulut özelliklerini, özelleştirmeleri ve görünümleri ekleyebilirsiniz.
- DTDL 'yi kullanarak bir cihaz modeli yazar. Modelden cihaz kodunuzu uygulayın. Cihaz modelini IoT Central uygulamanıza el ile içeri aktarın ve ardından tüm bulut özelliklerini, özelleştirmeleri ve IoT Central uygulamanızın ihtiyaçlarını görüntüler.

> [!TIP]
> IoT Central, tüm başvurulan arabirimlerin aynı dosyada tam model olmasını gerektirir. Model deposundan bir modeli içeri aktardığınızda, tam sürümü almak için *genişletilmiş* anahtar sözcüğünü kullanın.
> Örneğin, [https://devicemodels.azure.com/dtmi/com/example/thermostat-1.expanded.json](https://devicemodels.azure.com/dtmi/com/example/thermostat-1.expanded.json).

Ayrıca, [REST API](/learn/modules/manage-iot-central-apps-with-rest-api/) veya [clı](howto-manage-iot-central-from-cli.md)kullanarak bir IoT Central uygulamasına cihaz şablonları ekleyebilirsiniz.

Bazı [uygulama şablonları](concepts-app-templates.md) , uygulama şablonunun desteklediği senaryoda faydalı olan cihaz şablonlarını zaten içeriyor. Örneğin, bkz. [Mağaza içi analiz mimarisi](../retail/store-analytics-architecture.md).

## <a name="create-a-device-template-from-the-device-catalog"></a>Cihaz kataloğundan cihaz şablonu oluşturma

Bir Oluşturucu olarak, sertifikalı bir cihaz kullanarak çözümünüzü oluşturmaya hızlı bir başlangıç yapabilirsiniz. [Azure IoT cihaz kataloğunda](https://devicecatalog.azure.com)listeyi inceleyin. IoT Central, cihaz kataloğunu tümleştirerek sertifikalı cihazlardan herhangi birinden bir cihaz modeli aktarabilirsiniz. IoT Central içinde bu cihazlardan birini bir cihaz şablonu oluşturmak için:

1. IoT Central uygulamanızda **cihaz şablonları** sayfasına gidin.
1. **+ Yeni**' yi seçin ve ardından katalogda sertifikalı cihazlardan birini seçin. IoT Central, bu cihaz modelini temel alan bir cihaz şablonu oluşturur.
1. Cihaz şablonunuza tüm bulut özelliklerini, özelleştirmeleri veya görünümlerini ekleyin.
1. Şablonu operatörlerin cihazları görüntülemesi ve bağlanabilmesi için kullanılabilir hale getirmek için **Yayımla** ' yı seçin.

## <a name="create-a-device-template-from-scratch"></a>Sıfırdan bir cihaz şablonu oluşturma

Bir cihaz şablonu şunları içerir:

- Cihazın uyguladığı telemetri, özellik ve komutları belirten bir _cihaz modeli_ . Bu yetenekler bir veya daha fazla bileşen halinde düzenlenmiştir.
- IoT Central uygulamanızın cihazlarınızla ilgili depoladığını belirten bilgileri tanımlayan _bulut özellikleri_ . Örneğin, bir bulut özelliği bir cihazın en son hizmet verdiği tarihi kaydedebilir. Bu bilgiler hiçbir şekilde cihazla paylaşılmaz.
- _Özelleştirmeler_ , oluşturucunun cihaz modelindeki bazı tanımları geçersiz kılmasına izin verir. Örneğin, Oluşturucu bir cihaz özelliğinin adını geçersiz kılabilir. Özellik adları IoT Central görünümler ve formlarda görüntülenir.
- _Görünümler ve formlar_ , oluşturucunun uygulamanıza bağlı olan cihazları izleyip yönetmesine olanak tanıyan bir kullanıcı arabirimi oluşturmasını sağlar.

IoT Central bir cihaz şablonu oluşturmak için:

1. IoT Central uygulamanızda **cihaz şablonları** sayfasına gidin.
1. **+ Yeni**  >  **IoT cihazı**' nı seçin. Ardından **İleri: Özelleştir**' i seçin.
1. Şablonunuz için **termostat** gibi bir ad girin. Sonra **İleri** ' yi seçin ve ardından **Oluştur**' u seçin.
1. IoT Central boş bir cihaz şablonu oluşturur ve sıfırdan özel bir model oluşturmayı veya bir DTDL modelini içeri aktarmayı seçmenizi sağlar.

## <a name="manage-a-device-template"></a>Bir cihaz şablonunu yönetme

Şablonun giriş sayfasından bir şablonu yeniden adlandırabilir veya silebilirsiniz.

Şablonunuza bir cihaz modeli ekledikten sonra, bunu yayımlayabilirsiniz. Şablonu yayımlaana kadar, işletmenler için bu şablona dayalı bir cihazı, **cihazlar** sayfasında görmek üzere bağlanamazsınız.

## <a name="create-a-capability-model"></a>Yetenek modeli oluşturma

Bir cihaz modeli oluşturmak için şunları yapabilirsiniz:

- Sıfırdan özel bir model oluşturmak için IoT Central kullanın.
- Bir JSON dosyasından DTDL modeli içeri aktarın. Bir cihaz Oluşturucu, uygulamanız için bir cihaz modeli yazmak üzere Visual Studio Code kullanmış olabilir.
- Cihaz kataloğundan cihazlardan birini seçin. Bu seçenek, üreticinin Bu cihaz için yayımladığı cihaz modelini içeri aktarır. Bu şekilde içeri aktarılan bir cihaz modeli otomatik olarak yayımlanır.

## <a name="manage-a-capability-model"></a>Yetenek modeli yönetme

Bir cihaz modeli oluşturduktan sonra şunları yapabilirsiniz:

- Modele bileşen ekleyin. Bir modelde en az bir bileşen olmalıdır.
- KIMLIĞI, ad alanı ve adı gibi model meta verilerini düzenleyin.
- Modeli silin.

## <a name="create-a-component"></a>Bileşen oluşturma

Bir cihaz modelinde en az bir varsayılan bileşen olmalıdır. Bir bileşen, yeniden kullanılabilir bir özellik koleksiyonudur.

Bir bileşen oluşturmak için:

1. Cihaz modelinize gidin ve **+ Bileşen Ekle**' yi seçin.

1. **Bileşen arabirimi ekleme** sayfasında şunları yapabilirsiniz:

    - Sıfırdan özel bir bileşen oluşturun.
    - Bir DTDL dosyasından mevcut bir bileşeni içeri aktarın. Cihaz Oluşturucu, cihazınızın bileşen arabirimini yazmak için Visual Studio Code kullanmış olabilir.

1. Bir bileşeni oluşturduktan sonra, bileşenin görünen adını değiştirmek için **kimliği Düzenle** ' yi seçin.

1. Sıfırdan özel bir bileşen oluşturmayı seçerseniz, cihazınızın yeteneklerini ekleyebilirsiniz. Cihaz özellikleri telemetri, Özellikler ve komutlardır.

### <a name="telemetry"></a>Telemetri

Telemetri, genellikle bir sensörden cihazdan gönderilen değerlerin bir akışıdır. Örneğin, bir algılayıcı çevresel sıcaklığa rapor edebilir.

Aşağıdaki tabloda bir telemetri yeteneğinin yapılandırma ayarları gösterilmektedir:

| Alan | Açıklama |
| ----- | ----------- |
| Görünen Ad | Görünümler ve formlarda kullanılan telemetri değeri için görünen ad. |
| Name | Telemetri iletisindeki alanın adı. IoT Central görünen adından Bu alan için bir değer oluşturur, ancak gerekirse kendi değerini seçebilirsiniz. Bu alanın alfasayısal olması gerekir. |
| Yetenek Türü | Telemetri. |
| Anlamsal tür | Telemetrinin sıcaklık, durum veya olay gibi anlam türü. Anlamsal tür seçimi aşağıdaki alanlardan hangisinin kullanılabildiğini belirler. |
| Şema | Çift, dize veya vektör gibi telemetri veri türü. Kullanılabilir seçimler anlamsal tür tarafından belirlenir. Şema, olay ve durum anlam türleri için kullanılamaz. |
| Önem derecesi | Yalnızca olay anlam türü için kullanılabilir. Önem derecesi **hata**, **bilgi** veya **uyarılardır**. |
| Durum değerleri | Yalnızca durum anlam türü için kullanılabilir. Her birinin görünen adı, adı, sabit listesi türü ve değeri olan olası durum değerlerini tanımlayın. |
| Birim | Bir telemetri değeri için **mph**, **%** veya **&deg; C** gibi bir birim. |
| Görüntüleme birimi | Görünümler ve formlarda kullanılmak üzere bir görüntüleme birimi. |
| Yorum | Telemetri yeteneği hakkında herhangi bir yorum. |
| Description | Telemetri yeteneğinin açıklaması. |

### <a name="properties"></a>Özellikler

Özellikler, zaman içinde nokta değerlerini temsil eder. Örneğin, bir cihaz, ulaşmaya çalıştığı hedef sıcaklığın raporlanabilmesi için bir özelliği kullanabilir. IoT Central yazılabilir özellikleri ayarlayabilirsiniz.

Aşağıdaki tabloda bir özellik yeteneği için yapılandırma ayarları gösterilmektedir:

| Alan | Açıklama |
| ----- | ----------- |
| Görünen Ad | Görünümler ve formlarda kullanılan özellik değeri için görünen ad. |
| Name | Özelliğin adı. IoT Central görünen adından Bu alan için bir değer oluşturur, ancak gerekirse kendi değerini seçebilirsiniz. Bu alanın alfasayısal olması gerekir. |
| Yetenek Türü | Özelliði. |
| Anlamsal tür | Özelliğin sıcaklık, durum veya olay gibi anlam türü. Anlamsal tür seçimi aşağıdaki alanlardan hangisinin kullanılabildiğini belirler. |
| Şema | Double, String veya Vector gibi özellik veri türü. Kullanılabilir seçimler anlamsal tür tarafından belirlenir. Şema, olay ve durum anlam türleri için kullanılamaz. |
| Yazılabilir | Özellik yazılabilir değilse, cihaz özellik değerlerini IoT Central rapor edebilir. Özellik yazılabilir ise, cihaz özellik değerlerini IoT Central rapor edebilir ve IoT Central Özellik güncelleştirmelerini cihaza gönderebilir.
| Önem derecesi | Yalnızca olay anlam türü için kullanılabilir. Önem derecesi **hata**, **bilgi** veya **uyarılardır**. |
| Durum değerleri | Yalnızca durum anlam türü için kullanılabilir. Her birinin görünen adı, adı, sabit listesi türü ve değeri olan olası durum değerlerini tanımlayın. |
| Birim | **Mph**, **%** veya **&deg; C** gibi özellik değeri için bir birim. |
| Görüntüleme birimi | Görünümler ve formlarda kullanılmak üzere bir görüntüleme birimi. |
| Yorum | Özellik yeteneği hakkında herhangi bir açıklama. |
| Description | Özellik yeteneğinin açıklaması. |

### <a name="commands"></a>Komutlar

IoT Central 'tan cihaz komutlarını çağırabilirsiniz. Komutlar isteğe bağlı olarak parametreleri cihaza iletir ve cihazdan bir yanıt alır. Örneğin, bir cihazı 10 saniye içinde yeniden başlatmak için bir komut çağırabilirsiniz.

Aşağıdaki tabloda, bir komut özelliğine ait yapılandırma ayarları gösterilmektedir:

| Alan | Açıklama |
| ----- | ----------- |
| Görünen Ad | Görünümler ve formlarda kullanılan komutun görünen adı. |
| Name | Komutun adı. IoT Central görünen adından Bu alan için bir değer oluşturur, ancak gerekirse kendi değerini seçebilirsiniz. Bu alanın alfasayısal olması gerekir. |
| Yetenek Türü | Komutundaki. |
| Yorum | Komut özelliğiyle ilgili herhangi bir yorum. |
| Description | Komut yeteneğinin açıklaması. |
| İstek | Etkinleştirilirse, istek parametresinin tanımı: ad, görünen ad, şema, birim ve görüntü birimi. |
| Yanıt | Etkinleştirilirse, komut yanıtının tanımı: ad, görünen ad, şema, birim ve görüntü birimi. |

Cihazların komutları nasıl uygulayabileceği hakkında daha fazla bilgi edinmek için bkz. [telemetri, özellik ve komut yükleri > komutlar ve uzun süre çalışan komutlar](concepts-telemetry-properties-commands.md#commands).

#### <a name="offline-commands"></a>Çevrimdışı komutlar

Cihaz şablonundaki bir komutla ilgili olarak, bir cihaz çevrimdışıyken **sırayı** etkinleştirerek kuyruk komutları ' nı seçebilirsiniz.

Bu seçenek, cihazlara bildirim göndermek için buluttan cihaza iletileri IoT Hub kullanır. Daha fazla bilgi edinmek için bkz. [buluttan cihaza Ileti gönderme](../../iot-hub/iot-hub-devguide-messages-c2d.md)IoT Hub makalesi.

Buluttan cihaza iletiler:

- , Çözümünüzde cihaza yönelik tek yönlü bildirimlerdir.
- En az bir kez ileti teslimi garantisi. IoT Hub, cihaz başına kuyruklarda buluttan cihaza iletileri sürdürür, bağlantı ve cihaz hatalarıyla dayanıklılık sağlar.
- Cihazın buluttan cihaza iletisini işlemesi için bir ileti işleyicisi uygulaması gerekir.

> [!NOTE]
> Bu seçenek yalnızca IoT Central Web Kullanıcı arabiriminde kullanılabilir. Bu ayar, cihaz şablonundan bir modeli veya bileşeni dışa aktardığınızda dahil değildir.

## <a name="manage-a-component"></a>Bir bileşeni yönetme

Bileşeni yayımlamadıysanız, bileşen tarafından tanımlanan özellikleri düzenleyebilirsiniz. Bileşeni yayımladıktan sonra, herhangi bir değişiklik yapmak istiyorsanız, cihaz şablonunun yeni bir sürümünü oluşturmanız ve [bileşeni sürümüne](howto-version-device-template.md)uygulamanız gerekir. **Özelleştirme** bölümünde, sürüm oluşturma gerektirmeyen değişiklikler (görünen adlar veya birimler gibi) yapabilirsiniz.

Ayrıca, başka bir yetenek modelinde yeniden kullanmak istiyorsanız bileşeni bir JSON dosyası olarak dışarı aktarabilirsiniz.

## <a name="add-cloud-properties"></a>Bulut özelliklerini ekleme

IoT Central içindeki cihazlarla ilgili bilgileri depolamak için bulut özelliklerini kullanın. Bulut özellikleri hiçbir şekilde cihaza gönderilmez. Örneğin, bulut özelliklerini, cihazı yüklemiş olan müşterinin adını veya cihazın son hizmet tarihini depolamak için kullanabilirsiniz.

Aşağıdaki tabloda bir bulut özelliğinin yapılandırma ayarları gösterilmektedir:

| Alan | Açıklama |
| ----- | ----------- |
| Görünen Ad | Görünümler ve formlarda kullanılan bulut özelliği değeri için görünen ad. |
| Name | Bulut özelliğinin adı. IoT Central görünen adından Bu alan için bir değer oluşturur, ancak gerekirse kendi değerini seçebilirsiniz. |
| Anlamsal tür | Özelliğin sıcaklık, durum veya olay gibi anlam türü. Anlamsal tür seçimi aşağıdaki alanlardan hangisinin kullanılabildiğini belirler. |
| Şema | Çift, dize veya vektör gibi bulut özelliği veri türü. Kullanılabilir seçimler anlamsal tür tarafından belirlenir. |

## <a name="add-customizations"></a>Özelleştirmeler ekleme

İçeri aktarılan bir bileşeni değiştirmeniz veya bir özelliğe özgü IoT Central özellikler eklemeniz gerektiğinde özelleştirmeler kullanın. Yalnızca bileşen uyumluluğunu kesen alanları özelleştirebilirsiniz. Örneğin, şunları yapabilirsiniz:

- Bir özelliğin görünen adını ve birimlerini özelleştirin.
- Değer bir grafikte göründüğünde kullanılacak varsayılan rengi ekleyin.
- Bir özellik için başlangıçtaki, minimum ve maksimum değerleri belirtin.

Yetenek adını veya yetenek türünü özelleştiremezsiniz. **Özelleştirme** bölümünde yapameyeceğiniz değişiklikler varsa, özelliği değiştirmek için cihaz şablonunuzu ve bileşenini sürüm yapmanız gerekir.

### <a name="generate-default-views"></a>Varsayılan görünümleri oluştur

Varsayılan görünümleri oluşturmak, önemli cihaz bilgilerinizin görselleştirilmesine yönelik hızlı bir yoldur. Cihaz şablonunuz için en fazla üç varsayılan görünümünüz oluşturulmuştur:

- **Komutlar**: cihaz komutları içeren bir görünüm ve işletmenizin bu cihazları cihazınıza gönderme olanağı sağlar.
- **Genel bakış**: cihaz telemetrisine sahip bir görünüm, grafikleri ve ölçümleri görüntüleme.
- **Hakkında**: cihaz bilgilerini görüntüleyen bir görünüm, cihaz özelliklerini görüntülüyor.

**Varsayılan görünümleri oluştur**' u seçtikten sonra, cihaz şablonunuzun **Görünümler** bölümü altında otomatik olarak eklendiğini görürsünüz.

## <a name="add-views"></a>Görünümler ekleme

İşleçlerin grafikleri ve ölçümleri kullanarak bir cihazı görselleştirmesini sağlamak için bir cihaz şablonuna görünümler ekleyin. Bir cihaz şablonu için birden çok görünümünüz olabilir.

Bir cihaz şablonuna bir görünüm eklemek için:

1. Cihaz şablonunuza gidin ve **Görünümler**' i seçin.
1. **Cihazı görselleştirmeyi** seçin.
1. Görünüm **adında** görünüm için bir ad girin.
1. Statik, özellik, bulut özelliği, telemetri ve komut kutucukları listesinden görünüminize kutucuk ekleyin. Görünümüne eklemek istediğiniz kutucukları sürükleyip bırakın.
1. Tek bir grafik kutucuğunda birden çok telemetri değeri çizmek için telemetri değerlerini seçin ve ardından **Birleştir**' i seçin.
1. Eklediğiniz her kutucuğu, verilerin nasıl görüntüleneceğini özelleştirmek için yapılandırın. Dişli simgesini seçerek veya grafik kutucuğunda **yapılandırmayı Değiştir** ' i seçerek bu seçeneğe erişin.
1. Görünüminizdeki kutucukları düzenleyin ve yeniden boyutlandırın.
1. Değişiklikleri kaydedin.

### <a name="configure-preview-device-to-view"></a>Önizleme cihazını görüntülenecek şekilde yapılandırma

Görünümünüzü görüntülemek ve test etmek için **Önizleme cihazını Yapılandır**' ı seçin. Bu özellik, operatörünüz yayımlandıktan sonra gördüğü görünümü görmenizi sağlar. Görünümlerinizin doğru verileri göstermesini doğrulamak için bu özelliği kullanın. Aşağıdaki seçeneklerden birini belirtebilirsiniz:

- Önizleme cihazı yok.
- Cihaz şablonunuz için yapılandırdığınız gerçek test cihazı.
- Uygulamanızdaki mevcut bir cihaz, cihaz KIMLIĞINI kullanarak.

## <a name="add-forms"></a>Form Ekle

İşleçleri, özellikleri görüntüleyip ayarlayarak bir cihazı yönetmesine olanak tanımak için bir cihaz şablonuna form ekleyin. İşleçler yalnızca bulut özelliklerini ve yazılabilir cihaz özelliklerini düzenleyebilir. Bir cihaz şablonu için birden çok form kullanabilirsiniz.

Bir cihaz şablonuna form eklemek için:

1. Cihaz şablonunuza gidin ve **Görünümler**' i seçin.
1. **Cihaz ve bulut verilerini Düzenle '** yi seçin.
1. Form **adı**' na formunuz için bir ad girin.
1. Formunuzu düzenlemek için kullanılacak sütun sayısını seçin.
1. Formunuzdaki mevcut bir bölüme özellikler ekleyin veya Özellikler ' i seçin ve **Bölüm Ekle**' yi seçin. Formunuzdaki özellikleri gruplamak için bölümleri kullanın. Bir bölüme başlık ekleyebilirsiniz.
1. Davranışını özelleştirmek için formdaki her özelliği yapılandırın.
1. Formunuzdaki özellikleri düzenleyin.
1. Değişiklikleri kaydedin.

## <a name="publish-a-device-template"></a>Cihaz şablonunu yayımlama

Cihaz modelinizi uygulayan bir cihazı bağlayabilmeniz için önce cihaz şablonunuzu yayımlamanız gerekir.

Bir cihaz şablonunu yayımladıktan sonra, yalnızca cihaz modelinde sınırlı değişiklikler yapabilirsiniz. Bir bileşeni değiştirmek için [Yeni bir sürüm oluşturmanız ve yayımlamanız](./howto-version-device-template.md)gerekir.

Bir cihaz şablonu yayımlamak için, cihaz şablonunuza gidin ve **Yayımla**' yı seçin.

Bir cihaz şablonu yayımladıktan sonra, bir operatör **cihazlar** sayfasına gidebilir ve cihaz şablonunuzu kullanan gerçek ya da sanal cihazları ekleyebilir. Değişiklik yaparken cihaz şablonunuzu değiştirmeye ve kaydetmeye devam edebilirsiniz. Bu değişiklikleri, **cihazlar** sayfasında görüntülenecek olan işlece iletmek istediğinizde, her seferinde **Yayımla** ' yı seçmeniz gerekir.

## <a name="next-steps"></a>Sonraki adımlar

Bir cihaz geliştiricisiyseniz, önerilen bir sonraki adım [cihaz şablonu sürümü oluşturma](./howto-version-device-template.md)hakkında bilgi almak için kullanılır.
