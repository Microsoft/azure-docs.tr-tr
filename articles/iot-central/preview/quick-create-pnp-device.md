---
title: Azure IoT Central sanal cihaz ekleme | Microsoft Docs
description: Bir cihaz şablonu oluşturun ve IoT Central uygulamanıza sanal bir cihaz ekleyin.
author: dominicbetts
ms.author: dobett
ms.date: 10/24/2019
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 8b6720af6328242a1fe297e60c9d413d0b83f3d2
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73893826"
---
# <a name="quickstart-add-a-simulated-device-to-your-iot-central-application-preview-features"></a>Hızlı başlangıç: IoT Central uygulamanıza sanal cihaz ekleme (Önizleme özellikleri)

*Bu makale, işleçler, oluşturucular ve yöneticiler için geçerlidir.*

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Bir cihaz şablonu, IoT Central uygulamanıza bağlanan bir cihazın yeteneklerini tanımlar. Özellikler cihaz gönderme, cihaz özellikleri ve bir cihazın yanıt verdiği komutları içerir. Bir cihaz şablonundan, bir Oluşturucu veya operatör bir uygulamaya hem gerçek hem de sanal cihaz ekleyebilir. Sanal cihazlar, gerçek cihazları bağlanmadan önce IoT Central uygulamanızın davranışını test etmek için kullanışlıdır.

Bu hızlı başlangıçta bir **ortam algılayıcı** cihaz şablonu oluşturup sanal cihaz eklersiniz. Bir ortam algılayıcı cihazı:

* Sıcaklık gibi telemetri gönderir.
* , Parlaklık düzeyi gibi cihaza özgü özellikleri raporlar.
* Açma ve kapatma gibi komutlara yanıt verir.
* Bellenim sürümü ve seri numarası gibi genel cihaz özelliklerini raporlar.

## <a name="prerequisites"></a>Ön koşullar

**Özel uygulama > önizleme uygulaması** şablonunu kullanarak bir IoT Central uygulaması oluşturmak için [Azure IoT Central uygulaması oluşturma (Önizleme özellikleri)](./quick-deploy-iot-central.md) hızlı başlangıcını doldurun.

[Iot Tak ve kullan](../../iot-pnp/overview-iot-plug-and-play.md) cihaz yetenek modelini Içeren **Environmentalsensorline. capabilitymodel. JSON** dosyasının yerel bir kopyasına de ihtiyacınız vardır. [Buradan](https://raw.githubusercontent.com/Azure/IoTPlugandPlay/master/samples/EnvironmentalSensorInline.capabilitymodel.json)indirebilirsiniz. Sayfaya sağ tıklayın ve **farklı kaydet**' i seçin.

Dosyayı indirdikten sonra bir metin düzenleyicisinde açın ve `<YOUR_COMPANY_NAME_HERE>` iki örneğini adınızla değiştirin. Yalnızca a-z, A-Z, 0-9 ve alt çizgi karakterlerini kullanın.

## <a name="create-a-template"></a>Şablon oluşturma

Bir Oluşturucu olarak, uygulamanızda cihaz şablonları oluşturabilir ve düzenleyebilirsiniz. Bir cihaz şablonu yayımladıktan sonra, sanal cihaz oluşturabilir veya cihaz şablonunu uygulayan gerçek cihazları bağlayabilirsiniz. Sanal cihazlar, gerçek bir cihazı bağlanmadan önce uygulamanızın davranışını test etmenize olanak sağlar.

Uygulamanıza yeni bir cihaz şablonu eklemek için **cihaz şablonları** sayfasına gidin. Bunu yapmak için sol bölmedeki **cihaz şablonları** sekmesini seçin.

![Cihaz şablonları sayfası](./media/quick-create-pnp-device/devicedefinitions.png)

### <a name="add-a-device-capability-model"></a>Cihaz yetenek modeli ekleme

IoT Central bir cihaz yetenek modeli yazmak için çeşitli seçenekler vardır. Sıfırdan özel bir model oluşturmayı, bir dosyadan içeri aktarmayı, cihaz kataloğundan seçim yapmak ya da cihaz yetenek modelinin ortak depoda yayımlandığı cihaz-ilk bağlantı aracılığıyla bir IoT Tak ve Kullan cihazını bağlamayı seçebilirsiniz. Bu öğreticide bir cihaz yetenek modelini bir dosyadan içeri aktarırsınız.

Aşağıdaki adımlarda, bir **ortam algılayıcı** cihazı için yetenek modelinin nasıl içeri aktarılacağı gösterilmektedir. Bu cihazlar, uygulamanıza sıcaklık gibi telemetri gönderir:

1. Yeni bir cihaz şablonu eklemek için **cihaz şablonları** sayfasında **+** ' yi seçin.

1. Özel cihaz şablonları listesinden **IoT cihazı** ' nı seçin, **İleri**' yi seçin, ardından **İleri**' yi seçin ve ardından **Oluştur**' u seçin.

1. Cihaz şablonunuzun adı olarak **çevresel algılayıcı** girin.

1. Bir JSON dosyasından yeni bir cihaz yetenek modeli oluşturmak için **yetenek modeli Içeri aktar** ' ı seçin. Yerel makinenizde **Environmentalsensorline. capabilitymodel. JSON** dosyasını kaydettiğiniz klasöre gidin. **Environmentalsensorline. capabilitymodel. JSON** dosyasını seçin ve **Aç**' ı seçin. Çevresel algılayıcı yetenek modeli, **ortam algılayıcısı** ve **cihaz bilgileri** arabirimlerini içerir:

    ![Çevresel algılayıcı cihaz yeteneği modeli](./media/quick-create-pnp-device/newdevicecapabilitymodel.png)

    Bu arabirimler, bir **ortam algılayıcısı** cihazının yeteneklerini tanımlar. Özellikler, bir cihazın gönderdiği Telemetriyi, bir cihaz rapor özelliklerini ve bir cihazın yanıt verdiği komutları içerir.

### <a name="add-cloud-properties"></a>Bulut özellikleri ekle

Bir cihaz şablonu, bulut özellikleri içerebilir. Bulut özellikleri yalnızca IoT Central uygulamasında bulunur ve bir cihaza hiçbir şekilde gönderilmez veya buradan alınmaz.

1. **Bulut özellikleri** ' ni seçin ve **+ bulut özelliği Ekle**' ye tıklayın. Cihaz şablonunuza bir bulut özelliği eklemek için aşağıdaki tablodaki bilgileri kullanın.

    | Görünen Ad      | Anlamsal tür | Şema |
    | ----------------- | ------------- | ------ |
    | Son Hizmet Tarihi | None          | Tarih   |
    | Müşteri adı     | None          | Dize |

1. Değişikliklerinizi kaydetmek için **Kaydet** ' i seçin:

    ![Bulut özellikleri](media/quick-create-pnp-device/cloudproperties.png)

## <a name="create-views"></a>Görünüm oluşturma

Bir Oluşturucu olarak, uygulamayı bir işlecine çevresel algılayıcı cihazı hakkındaki ilgili bilgileri görüntüleyecek şekilde özelleştirebilirsiniz. Özelleştirmeleriniz, uygulamaya bağlı çevresel algılayıcı cihazlarını yönetmek için işlecini etkinleştirir. Cihazlarla etkileşim kurmak için kullanılacak bir operatör için iki tür görünüm oluşturabilirsiniz:

* Cihaz ve bulut özelliklerini görüntüleme ve düzenleme için formlar.
* Cihazları görselleştirmek için panolar.

### <a name="generate-default-views"></a>Varsayılan görünümleri oluştur

Varsayılan görünümleri oluşturmak, önemli cihaz bilgilerinizi görselleştirmeyi kullanmaya başlamak için hızlı bir yoldur. Cihaz şablonunuz için en fazla üç varsayılan görünüm oluşturmuş olabilirsiniz:

* **Komutlar** görünümü, operatörünüzün komutlarınıza erişmenizi sağlar.
* **Genel bakış** görünümü cihaz telemetrisini görüntülemek için grafikleri ve ölçümleri kullanır.
* **Hakkında** görünümü cihaz özelliklerini görüntüler.

**Görünümler** ' i ve ardından **varsayılan görünümleri oluştur**' u seçin.

### <a name="configure-a-view-to-visualize-devices"></a>Cihazları görselleştirmek için bir görünüm yapılandırma

Bir cihaz panosu, bir işlecin grafikleri ve ölçümleri kullanarak bir cihazı görselleştirmesine olanak sağlar. Oluşturucu olarak, bir cihaz panosunda hangi bilgilerin gösterileceğini tanımlayabilirsiniz. Cihazlar için birden çok Pano tanımlayabilirsiniz. Ortam algılayıcısı telemetrisini görselleştirmek üzere bir pano oluşturmak için, **Görünümler** ' i seçin ve ardından **cihazı görselleştirmeyi**seçin:

1. Tüm cihaz özellikleri, bulut özellikleri, telemetri ve statik Seçenekler **Özellikler**altında listelenmiştir. Bu öğelerin herhangi birini görünüme sürükleyip bırakabilirsiniz. **Parlaklık düzeyi** özelliğini görünüme sürükleyin. Kutucuğu, dişli simgesini kullanarak yapılandırabilirsiniz.

1. Telemetriyi çiztireeden bir grafik eklemek için **nem** ve **sıcaklık**' yı ve ardından **Birleştir**' i seçin. Bu grafiği, pasta grafiği veya çubuk grafik gibi farklı bir biçimde görüntülemek için kutucuğun en üstündeki **görselleştirmeyi Değiştir** düğmesini seçin.

1. Görünümünüzü kaydetmek için **Kaydet** ' i seçin:

Diğer özellikleri veya telemetri değerlerini gösteren daha fazla kutucuk ekleyebilirsiniz. Statik metin, bağlantılar ve görüntüler de ekleyebilirsiniz. Panodaki bir kutucuğu taşımak veya yeniden boyutlandırmak için fare işaretçisini döşemenin üzerine taşıyın ve kutucuğu yeni bir konuma sürükleyin veya yeniden boyutlandırın.

### <a name="add-a-device-form"></a>Bir cihaz formu Ekle

Bir cihaz formu, bir işlecin yazılabilir cihaz özelliklerini ve bulut özelliklerini düzenlemesini sağlar. Bir Oluşturucu olarak, birden çok form tanımlayabilir ve her formda hangi cihaz ve bulut özelliklerinin gösterileceğini seçebilirsiniz. Ayrıca, salt okunurdur cihaz özelliklerini bir formda görüntüleyebilirsiniz.

Ortam algılayıcısı özelliklerini görüntülemek ve düzenlemek için bir form oluşturmak için:

1. **Ortam algılayıcı** şablonundaki **görünümlere** gidin. Yeni bir görünüm eklemek için **cihazı ve bulut verilerini Düzenle** kutucuğunu seçin.

1. **Çevresel algılayıcı özellikleri**form adını girin.

1. **Müşteri adı** ve **son hizmet tarihi** bulutu özelliklerini formdaki mevcut bölüme sürükleyin.

1. **Parlaklık düzeyini** ve **cihaz durumu** cihaz özelliklerini seçin. Ardından **Bölüm Ekle**' yi seçin. Bölümün başlığını **algılayıcı özellikleri**olacak şekilde düzenleyin. **Uygula**’yı seçin.

1. **Cihaz modelini**, **yazılım sürümünü**, **üreticiyi**ve **işlemci üreticisi** cihaz özelliklerini seçin. Ardından **Bölüm Ekle**' yi seçin. Bölüm başlığını **cihaz özellikleri**olacak şekilde düzenleyin. **Uygula**’yı seçin.

1. Görünümünüzü kaydetmek için **Kaydet** ' i seçin.

## <a name="publish-device-template"></a>Cihaz şablonunu Yayımla

Sanal bir ortam algılayıcısı oluşturmadan veya gerçek bir ortam sensöre bağlanmadan önce, cihaz şablonunuzu yayımlamanız gerekir.

Bir cihaz şablonunu yayımlamak için:

1. Cihaz **şablonları** sayfasından cihaz şablonunuza gidin.

1. **Yayımla**’yı seçin.

1. **Cihaz şablonu Yayımla** Iletişim kutusunda **Yayımla**' yı seçin.

    ![Yayımlanan model](media/quick-create-pnp-device/publishedmodel.png)

Bir cihaz şablonu yayımlandıktan sonra **cihazlar** sayfasında ve işlecine görünür. Yayımlanmış bir cihaz şablonunda, yeni bir sürüm oluşturmadan bir cihaz yetenek modeli düzenleyemezsiniz. Ancak, bulut özellikleri, özelleştirmeler ve görünümlerde, sürüm oluşturmadan yayımlanmış bir cihaz şablonunda güncelleştirmeler yapabilirsiniz. Herhangi bir değişiklik yaptıktan sonra, bu değişiklikleri işletmenizin dışına göndermek için **Yayımla** ' yı seçin.

## <a name="add-a-simulated-device"></a>Sanal cihaz ekleme

Uygulamanıza sanal bir cihaz eklemek için, oluşturduğunuz **çevresel algılayıcı** cihaz şablonunu kullanırsınız.

1. Yeni bir cihazı operatör olarak eklemek için sol bölmedeki **cihazlar** ' ı seçin. **Cihazlar** sekmesi **tüm cihazları** ve **çevresel algılayıcı** cihaz şablonunu gösterir. **Çevresel algılayıcı**' ı seçin.

1. Sanal bir çevresel algılayıcı cihazı eklemek için **+ Yeni**' yi seçin. Önerilen **CIHAZ kimliğini** kullanın veya kendi küçük BIR **Cihaz Kimliğinizi**girin. Ayrıca, yeni cihazınız için bir ad girebilirsiniz. **Benzetimli** geçişi **Açık** olarak değiştirin ve ardından **Oluştur**' u seçin.

    ![Sanal cihaz](./media/quick-create-pnp-device/simulated-device.png)

Artık, benzetimli verileri kullanarak cihaz şablonu için Oluşturucu tarafından oluşturulan görünümlerle etkileşime geçebilirsiniz.

## <a name="use-a-simulated-device-to-improve-views"></a>Görünümleri geliştirmek için sanal cihaz kullanma

Yeni bir sanal cihaz oluşturduktan sonra, Oluşturucu bu cihazı kullanarak cihaz şablonunun görünümlerini iyileştirmeye ve oluşturmaya devam edebilir.

1. Sol bölmedeki **cihaz şablonları** ' nı seçin ve **çevresel algılayıcı** şablonunu seçin.

1. Düzenlemek istediğiniz görünümlerden birini seçin veya yeni bir görünüm oluşturun. **Önizleme cihazını Yapılandır**' a tıklayın ve ardından **çalışan bir cihazdan seçim**yapın. Burada, test için yapılandırabileceğiniz gerçek bir cihazı kullanarak veya IoT Central eklediğiniz mevcut bir cihazdan bir önizleme cihazı olmadan seçim yapabilirsiniz.

1. Listeden sanal cihazınızı seçin. Ardından **Uygula**' yı seçin. Artık cihaz şablonu görünümlerini oluşturma deneyiminizdeki aynı sanal cihazı görebilirsiniz. Bu görünüm, grafikler ve diğer görselleştirmeler için kullanışlıdır.

    ![Önizleme cihazını Yapılandır](./media/quick-create-pnp-device/configure-preview.png)

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, bir **ortam algılayıcısı** cihaz şablonu oluşturmayı ve uygulamanıza sanal bir cihaz eklemeyi öğrendiniz.

Uygulamanıza bağlı olan cihazları izleme hakkında daha fazla bilgi edinmek için hızlı başlangıç ile devam edin:

> [!div class="nextstepaction"]
> [Kuralları ve eylemleri yapılandırma](./quick-configure-rules.md)
