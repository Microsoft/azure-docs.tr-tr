---
title: "Öğretici: Azure 'da bir mağaza Analytics uygulaması oluşturma IoT Central"
description: Bu öğreticide, IoT Central ' de bir mağaza Analizi perakende uygulamasının nasıl oluşturulacağı gösterilmektedir. Bunu oluşturacak, özelleştirecek ve algılayıcı cihazları ekleyeceğiz.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.custom:
- iot-storeAnalytics-checkout
- iot-p0-scenario
ms.author: timlt
author: timlt
ms.date: 11/12/2019
ms.openlocfilehash: 071d432ba2769705213cb98cee644b64e9e81b6e
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74112892"
---
# <a name="tutorial-create-an-in-store-analytics-application-in-azure-iot-central"></a>Öğretici: Azure 'da bir mağaza Analytics uygulaması oluşturma IoT Central

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Öğreticide, bir Azure IoT Central mağaza 'da analiz uygulaması oluşturma çözümü üreticileri gösterilmektedir. Örnek uygulama, perakende mağaza içindir. Bu, yaygın iş ihtiyacı olan bir çözümdür ve bu da, doluluk ve çevre koşullarını izleyip uyarlayabilir.

Oluşturduğunuz örnek uygulama üç gerçek cihaz içerir: bir Rigado basamaklı 500 ağ geçidi ve iki RuuviTag algılayıcı. Öğretici Ayrıca, test amacıyla uygulama şablonunda içerilen sanal doluluk algılayıcısı 'nın nasıl kullanılacağını gösterir. Rigado C500 ağ geçidi, uygulamanızdaki iletişim hub 'ı olarak görev yapar. Deponuzdaki sensörlerle iletişim kurar ve buluta bağlantılarını yönetir. RuuviTag, sıcaklık, nem ve basınç dahil telemetri sağlayan bir ortam sensördur. Sanal doluluk algılayıcısı, bir deponun kullanıma alma alanlarında hareket ve varlığı izlemek için bir yol sağlar. 

Bu öğretici, Rigado ve RuuviTag cihazlarını uygulamanıza bağlamak için yönergeler içerir. Başka bir ağ geçidiniz ve sensöriniz varsa, uygulamanızı oluşturmak için yine de adımları izleyebilirsiniz. Öğretici Ayrıca sanal RuuviTag sensörlerinden nasıl oluşturulacağını gösterir. Sanal algılayıcılar, gerçek cihazlarınız yoksa uygulamayı oluşturmanızı sağlar. 

Kullanıma alma ve koşul izleme çözümünü üç bölümde geliştirebilirsiniz:

* Uygulama oluşturma ve cihazları izlemek için cihazları bağlama
* Operatörlerin cihazları izlemesini ve yönetmesini sağlamak için panoyu özelleştirme
* Mağaza yöneticilerinin analiz çalıştırmasını ve öngörüleri görselleştirmesini sağlamak için veri vermeyi yapılandırma

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:
> [!div class="checklist"]
> * Azure IoT Central **Mağaza içi analiz-kullanıma alma** şablonunu kullanarak bir perakende mağaza uygulaması oluşturun
> * Uygulama ayarlarını özelleştirme
> * IoT cihaz şablonları oluşturma ve özelleştirme
> * Cihazları uygulamanıza bağlama
> * Koşulları izlemek için kurallar ve eylemler ekleme

## <a name="prerequisites"></a>Önkoşullar

Bu öğretici serisini tamamlayabilmeniz için şunlar gerekir:
* Bir Azure aboneliği önerilir. İsteğe bağlı olarak 7 günlük ücretsiz deneme kullanabilirsiniz. Azure aboneliğiniz yoksa, [Azure kaydolma sayfasında](https://aka.ms/createazuresubscription)bir tane oluşturabilirsiniz.
* Bir ağ geçidi cihazına ve iki çevresel sensöre erişim (isteğe bağlı olarak, öğreticide açıklandığı gibi sanal cihazları kullanabilirsiniz)
* Kullandığınız cihazların cihaz şablonları (öğreticide kullanılan tüm cihazlar için şablonlar sağlanır)

## <a name="create-an-application"></a>Uygulama oluşturma
Bu bölümde, bir şablondan yeni bir Azure IoT Central uygulaması oluşturacaksınız. Bu uygulamayı, tüm bir çözüm oluşturmak için öğretici serisi boyunca kullanacaksınız.

Yeni bir Azure IoT Central uygulaması oluşturmak için:

1. [Azure IoT Central uygulama Yöneticisi](https://aka.ms/iotcentral) Web sitesine gidin.

1. Azure aboneliğiniz varsa, ona erişmek için kullandığınız kimlik bilgileriyle oturum açın, aksi takdirde Microsoft hesabı kullanarak oturum açın:

    ![Kuruluş hesabınızı girin](./media/tutorial-in-store-analytics-create-app-pnp/sign-in.png)

1. Yeni bir Azure IoT Central uygulaması oluşturmaya başlamak için **Yeni Uygulama**'yı seçin.

1. **Perakende**' ı seçin.  Perakende sayfasında çeşitli perakende uygulama şablonları görüntülenir.

Önizleme özelliklerini kullanan yeni bir mağaza içi analiz kullanıma alma uygulaması oluşturmak için:  

1. **Mağaza içi analiz-kullanıma alma** uygulama şablonunu seçin. Bu şablon, RuuviTag algılayıcı haricinde öğreticide kullanılan tüm cihazların cihaz şablonlarını içerir. Şablon Ayrıca, kullanıma alma ve çevre koşullarını ve cihaz durumunu izlemek için bir operatör panosu sağlar. 

1. İsteğe bağlı olarak, kolay bir **uygulama adı**seçin. Bu uygulama, contoso adlı kurgusal bir perakende mağazayı temel alır. Öğretici, *contoso kullanıma alma* **uygulama adını** kullanır. Uygulama şablonu kurgusal şirket Northwind ' i temel alır. Bu öğreticide, uygulamayı özelleştirmeyi öğrenmek için Contoso 'yu kullanırsınız.

    > [!NOTE]
    > Kolay bir **uygulama adı**kullanırsanız, uygulama **URL 'si**için yine de benzersiz bir değer kullanmanız gerekir.

1. Azure aboneliğiniz varsa *dizininizi, Azure aboneliğinizi ve bölgenizi*girin. Aboneliğiniz yoksa, **7 günlük ücretsiz denemeyi** etkinleştirebilir ve gerekli iletişim bilgilerini tamamlayabilirsiniz.  

    Dizinler ve abonelikler hakkında daha fazla bilgi için bkz. [Uygulama oluşturma hızlı başlangıcı](../preview/quick-deploy-iot-central.md).

1. **Oluştur**'u seçin.

    ![Azure IoT Central Uygulama Oluştur sayfası](./media/tutorial-in-store-analytics-create-app-pnp/preview-application-template.png)

## <a name="customize-application-settings"></a>Uygulama ayarlarını özelleştirme
Bir Oluşturucu olarak, uygulamanızdaki Kullanıcı deneyimini özelleştirmek için çeşitli ayarları değiştirebilirsiniz. Bu bölümde, önceden tanımlanmış bir uygulama teması seçersiniz. İsteğe bağlı olarak, özel bir tema oluşturmayı ve uygulama görüntüsünü güncelleştirmeyi öğreneceksiniz. Özel bir tema uygulama tarayıcısı renklerini, tarayıcı simgesini ve künyesi içinde görüntülenen uygulama logosunu ayarlamanıza olanak sağlar.

Önceden tanımlanmış bir uygulama teması seçmek için:

1. Künyesi üzerindeki **ayarları** seçin.

    ![Azure IoT Central uygulama ayarları](./media/tutorial-in-store-analytics-create-app-pnp/settings-icon.png)

2. Yeni bir **Tema**seçin.

3. **Kaydet**’i seçin.

Önceden tanımlanmış bir tema kullanmak yerine, özel bir tema oluşturabilirsiniz. Uygulamayı özelleştirmek ve öğreticiyi gerçekleştirmek için bir örnek görüntüler kümesi kullanmak istiyorsanız, [contoso örnek görüntülerini](https://github.com/Azure-Samples/iot-central-docs-samples/tree/master/retail)indirin.

Özel bir tema oluşturmak için:

1. Henüz genişletilmediyse sol bölmeyi genişletin.

    ![Azure IoT Central sol bölmesi](./media/tutorial-in-store-analytics-create-app-pnp/dashboard-expand.png)

1. **Uygulamanızı özelleştirmek > yönetim**' i seçin.

1. **Uygulama logosu**olarak karşıya yüklenecek bir görüntü seçmek için **Değiştir** düğmesini kullanın. İsteğe bağlı olarak, **logo alt metni**için bir değer belirtin. 

1. Tarayıcı sekmelerinde görünecek bir **tarayıcı simgesi** görüntüsü seçmek için **Değiştir** düğmesini kullanın.

1. İsteğe bağlı olarak, varsayılan **tarayıcı RENKLERINI** HTML onaltılık renk kodları ekleyerek değiştirin. **Üst bilgi**için *#008575*ekleyin.  **Vurgu**için *#A1F3EA*ekleyin. 

1. **Kaydet**’i seçin. 

    ![Azure IoT Central Özelleştirilmiş logosu](./media/tutorial-in-store-analytics-create-app-pnp/select-application-logo.png)

    Kaydettikten sonra uygulama, tarayıcı renklerini, künyesi içindeki logoyu ve tarayıcı simgesini günceller. 

    ![Azure IoT Central uygulama ayarlarını güncelleştirmiş](./media/tutorial-in-store-analytics-create-app-pnp/saved-application-settings.png)

Uygulama görüntüsünü güncelleştirmek için:

1. **Yönetim > uygulama ayarları**' nı seçin.

1. Uygulama görüntüsü olarak karşıya yüklenecek bir görüntü seçmek için **Görüntü Seç** düğmesini kullanın. Bu görüntü, IoT Central uygulama yöneticisinin **uygulamalarım** sayfasındaki uygulama kutucuğunda görünür.

1. **Kaydet**’i seçin.

1. İsteğe bağlı olarak, [Azure IoT Central uygulama Yöneticisi](https://aka.ms/iotcentral) Web sitesindeki **uygulamalarım** görünümüne gidin. Uygulama kutucuğu güncelleştirilmiş uygulama görüntüsünü görüntüler.

    ![Azure IoT Central uygulama görüntüsünü özelleştirme](./media/tutorial-in-store-analytics-create-app-pnp/customize-application-image.png)

## <a name="create-device-templates"></a>Cihaz şablonları oluşturma
Bir Oluşturucu olarak, size ve uygulama işletmenlerine cihaz yapılandırma ve yönetme olanağı sunan cihaz şablonları oluşturabilirsiniz. Bir şablonu, var olan bir şablon dosyasını içeri aktararak veya Azure IoT cihaz kataloğundan bir şablon içeri aktararak oluşturun. Bir cihaz şablonu oluşturup özelleştirdikten sonra, gerçek cihazları uygulamanıza bağlamak için onu kullanın. İsteğe bağlı olarak, test için sanal cihazlar oluşturmak üzere bir cihaz şablonu kullanın.

**Mağaza içi analiz-kullanıma alma** uygulama şablonunda birkaç cihaz için cihaz şablonları vardır.  Uygulamada kullandığınız üç cihazdan ikisi için cihaz şablonları vardır. RuuviTag cihaz şablonu, **Mağaza içi analiz-kullanıma alma** uygulama şablonuna dahil değildir. Bu bölümde, uygulamanız için RuuviTag sensörlerinden bir cihaz şablonu eklersiniz.

Uygulamanıza bir RuuviTag cihaz şablonu eklemek için:

1. Sol bölmedeki **cihaz şablonları** ' nı seçin.

1. Yeni bir cihaz şablonu oluşturmak için **+ Yeni** ' yi seçin.

1. Azure IoT cihaz kataloğunda **Ruuvitag** algılayıcı cihaz şablonunu bulun ve seçin. 

1. **İleri ' yi seçin: Özelleştir**.

    ![Azure IoT Central RuuviTag algılayıcı cihaz şablonu](./media/tutorial-in-store-analytics-create-app-pnp/ruuvitag-device-template.png)

1. **Oluştur**'u seçin. Uygulama, RuuviTag cihaz şablonunu ekler.

1. Sol bölmedeki **cihaz şablonları** ' nı seçin. Sayfa, uygulama şablonuna dahil edilen tüm cihaz şablonlarını ve yeni eklediğiniz RuuviTag cihaz şablonunu görüntüler.

    ![Azure IoT Central RuuviTag algılayıcı cihaz şablonu](./media/tutorial-in-store-analytics-create-app-pnp/device-templates-list.png)

## <a name="customize-device-templates"></a>Cihaz şablonlarını özelleştirme
Uygulamanızdaki cihaz şablonlarını üç şekilde özelleştirebilirsiniz. İlk olarak, cihaz yeteneklerini değiştirerek cihazlarınızdaki yerel yerleşik arabirimleri özelleştirirsiniz. Örneğin, sıcaklık algılayıcısı ile sıcaklık arabiriminin görünen adı, veri türü, ölçü birimleri ve en düşük ve en yüksek işletim aralıkları gibi ayrıntıları değiştirebilirsiniz. 

İkincisi, bulut özellikleri ekleyerek cihaz şablonlarınızı özelleştirin. Bulut özellikleri yerleşik cihaz özelliklerinin bir parçası değildir. Bulut özellikleri, Azure IoT Central uygulamanızın cihazlarınızla oluşturduğu, depoladığı ve ilişkilendiğinde özel verilere sahiptir. Bir bulut özelliğine örnek olarak hesaplanmış bir değer veya bir cihaz kümesiyle ilişkilendirmek istediğiniz konum gibi meta veriler olabilir. 

Üçüncü olarak, özel görünümler oluşturarak cihaz şablonlarını özelleştirin. Görünümler, operatörlerin cihaz ölçümleri ve sistem durumu gibi telemetri ve meta verileri görselleştirmesine yönelik bir yol sağlar.

Burada, RuuviTag sensörlerinizin cihaz şablonunu özelleştirmek için ilk iki yöntemi kullanın. Sensörleriniz için görünüm oluşturma hakkında daha fazla bilgi için, IoT Central uygulamanızın hızlı başlangıç başlığına [sanal cihaz ekleme](../preview/quick-create-pnp-device.md) hızlı başlangıç bölümüne bakın.

RuuviTag cihaz şablonunun yerleşik arabirimlerini özelleştirmek için:

1. Sol bölmedeki **cihaz şablonları** ' nı seçin. 

1. RuuviTag algılayıcılar şablonunu seçin. 

1. Sol Bölmeyi gizleyin. Şablonun Özet görünümü cihaz yeteneklerini görüntüler.

    ![Azure IoT Central RuuviTag cihaz şablonu Özet görünümü](./media/tutorial-in-store-analytics-create-app-pnp/ruuvitag-device-summary-view.png)

1. RuuviTag cihaz şablonu menüsünde **Özelleştir** ' i seçin. 

1. Özellik listesinde ilerleyin ve `humidity` telemetri türünü bulun. Bu, düzenlenebilir **görünen ad** değeri *nem*olan satır öğesidir.

Aşağıdaki adımlarda, RuuviTag sensörlerinden `humidity` telemetri türünü özelleştirirsiniz. İsteğe bağlı olarak, diğer telemetri türlerinden bazılarını özelleştirin.

`humidity` telemetri türü için aşağıdaki değişiklikleri yapın:

1. Satır için şema ayrıntılarını genişletmek üzere **genişletme** denetimini seçin.

1. **Görünen ad** değerini *nem* 'ten *bağıl nem*gibi özel bir değere güncelleştirin.

1. **Anlamsal tür** seçeneğini *none* iken *nem*olarak değiştirin.  İsteğe bağlı olarak, genişletilmiş şema görünümünde nem telemetri türü için şema değerlerini ayarlayın. Şema ayarları, sensörlerinizin izlediğiniz veriler için ayrıntılı doğrulama gereksinimleri oluşturmanıza imkan tanır. Örneğin, belirli bir arabirim için en düşük ve en yüksek işletim aralığı değerlerini ayarlayabilirsiniz.

1. Seçin **Kaydet** yaptığınız değişiklikleri kaydedin.

    ![Azure IoT Central RuuviTag cihaz şablonu özelleştirmesi](./media/tutorial-in-store-analytics-create-app-pnp/ruuvitag-device-template-customize.png)

Uygulamanızdaki bir cihaz şablonuna bir bulut özelliği eklemek için:

1. RuuviTag cihaz şablonu menüsünde **bulut özellikleri** ' ni seçin.

1. **Bulut özelliği Ekle**' yi seçin. 

Her bir cihazın konumunu depolamak üzere özel bir özellik oluşturmak için aşağıdaki değerleri belirtin:

1. **Görünen ad**Için değer *konumunu* girin. Bu değer, özellik için kolay bir ad olan **ad** alanına otomatik olarak kopyalanır. Kopyalanmış değeri kullanabilir veya değiştirebilirsiniz.

1. **Şema** açılan menüsünde *dize* ' yi seçin. Bir dize türü, bir konum adı dizesini şablona dayalı herhangi bir cihazla ilişkilendirmenizi sağlar. Örneğin, bir depodaki bir alanı her cihazla ilişkilendirebilirsiniz. İsteğe bağlı olarak, özelliğin **anlam türünü** *konum*olarak ayarlayabilirsiniz ve bu, **Şemayı** otomatik olarak *geopoint*olarak ayarlar. Bu, GPS koordinatlarını bir cihazla ilişkilendirmenizi sağlar. 

1. **Minimum uzunluğu** *2*olarak ayarlayın. 

1. **Boşluk Kırp** 'ı **Açık**olarak ayarlayın.

1. Özel bulut özelliğini kaydetmek için **Kaydet** ' i seçin.

    ![Azure IoT Central RuuviTag cihaz şablonu özelleştirmesi](./media/tutorial-in-store-analytics-create-app-pnp/ruuvitag-device-template-cloud-property.png)

1. **Yayımla**’yı seçin. 

    Bir cihaz şablonu yayımlandığında uygulama işleçleri görünür hale gelir. Bir şablon yayımladıktan sonra, test etmek için sanal cihazlar oluşturmak veya gerçek cihazları uygulamanıza bağlamak için kullanın. Uygulamanıza bağlı cihazlarınız zaten varsa, özelleştirilmiş bir şablon yayımlandığında bu değişiklikler cihazlara bağlanır.

## <a name="add-devices"></a>Cihazları ekleme
Cihaz şablonlarını oluşturup özelleştirdikten sonra cihaz ekleme zamanı. 

Bu öğreticide, uygulamayı derlemek için aşağıdaki gerçek ve sanal cihaz kümesini kullanacaksınız:
- Gerçek bir Rigado C500 ağ geçidi
- İki gerçek RuuviTag sensöri
- Sanal bir **doluluk** algılayıcısı. Sanal algılayıcı, uygulama şablonunda bulunur, bu nedenle oluşturmanız gerekmez. 

> [!NOTE]
> Gerçek cihazlarınız yoksa, benzetimli RuuviTag algılayıcılar oluşturarak bu öğreticiyi tamamlayabilirsiniz. Aşağıdaki yönergeler, sanal bir RuuviTag oluşturma adımlarını içerir. Sanal ağ geçidi oluşturmanız gerekmez.

Gerçek bir Rigado ağ geçidini ve RuuviTag sensörleriyle bağlantı kurmak için aşağıdaki iki makalede bulunan adımları izleyin. İşiniz bittiğinde Bu öğreticiye geri dönün. Bu öğreticide zaten cihaz şablonları oluşturmuş olduğunuzdan, bunları aşağıdaki yönergeler kümesinde yeniden oluşturmanız gerekmez.

- Bir Rigado ağ geçidini bağlamak için bkz. [Azure IoT Central uygulamanıza bir Rigado basamakla 500 bağlama](../preview/howto-connect-rigado-cascade-500.md?toc=/azure/iot-central/retail/toc.json&bc=/azure/iot-central/retail/breadcrumb/toc.json).
- RuuviTag sensörlerinden bağlantı kurmak için bkz. [Azure IoT Central uygulamanıza bir ruuvitag algılayıcısı bağlama](../preview/howto-connect-ruuvi.md?toc=/azure/iot-central/retail/toc.json&bc=/azure/iot-central/retail/breadcrumb/toc.json). Gerekirse iki sanal algılayıcı oluşturmak için bu yönergeleri de kullanabilirsiniz.

## <a name="add-rules-and-actions"></a>Kural ve eylem ekleme
Koşulları izlemek için Azure IoT Central uygulamanızda sensörler kullanmanın bir parçası olarak, belirli koşullar karşılandığında eylemleri çalıştırmak için kurallar oluşturabilirsiniz. Bir kural bir cihaz şablonuyla ve bir veya daha fazla cihazla ilişkilendirilir ve cihaz telemetri veya olaylarına göre karşılanması gereken koşulları içerir. Bir kuralda bir veya daha fazla ilişkili eylem de vardır. Eylemler e-posta bildirimleri göndermeyi veya diğer hizmetlere veri göndermek için bir Web kancası eylemi tetiklemesini içerebilir. **Mağaza içi analiz-kullanıma alma** uygulama şablonu, uygulamadaki cihazlar için önceden tanımlanmış bazı kurallar içerir.

Bu bölümde, RuuviTag algılayıcı telemetrisine göre maksimum göreli nem düzeyini denetleyen yeni bir kural oluşturacaksınız. Kurala bir eylem eklersiniz, böylece nem en büyük değeri aşarsa, uygulama e-posta gönderir. 

Bir kural oluşturmak için: 

1. Sol Bölmeyi genişletin.

1. **Kurallar**' ı seçin.

1. **+ Yeni**seçeneğini belirleyin.

1. Kuralın adı olarak *nem düzeyini* girin. 

1. **Kapsamlardaki**RuuviTag cihaz şablonunu seçin. Tanımladığınız kural, bu şablona dayalı tüm sensörler için geçerlidir. İsteğe bağlı olarak, kuralı yalnızca sensörların tanımlı bir alt kümesine uygulayacak bir filtre oluşturabilirsiniz. 

1. **Telemetri**olarak `Relative humidity` seçin. Bu, önceki bir adımda özelleştirdiğiniz cihaz özelliğidir.

1. **İşleç**olarak `Is greater than` seçin. 

1. Ortamınız için, **değer**olarak tipik bir büyük erimli ıntıl nem düzeyi girin. Örneğin *65*yazın. Bir RuuviTag gerçek veya benzetim algılayıcısı bu değeri aşarsa, kuralınız için bir koşul ayarlarsınız. Ortamınızdaki normal nem aralığına göre değeri yukarı veya aşağı ayarlamanız gerekebilir.  

   ![Azure IoT Central kural koşulları ekleme](./media/tutorial-in-store-analytics-create-app-pnp/rules-add-conditions.png)

Kurala bir eylem eklemek için:

1. **+ E-posta**seçeneğini belirleyin. 

1. Eylem için kolay **görünen ad** olarak *yüksek nem bildirimi* girin. 

1. Hesabınıza ait hesabınızla ilişkili e-posta adresini girin **.** Farklı bir e-posta kullanıyorsanız, kullandığınız adres uygulamaya eklenmiş olan bir kullanıcı için olmalıdır. Kullanıcının aynı zamanda oturum açması ve en az bir kez oturum açması gerekir.

1. İsteğe bağlı olarak, e-posta metnine dahil etmek için bir Note girin.

1. Eylemi gerçekleştirmek için **bitti** ' yi seçin.

   ![Azure IoT Central kurallara eylem ekleme](./media/tutorial-in-store-analytics-create-app-pnp/rules-add-action.png)

1. Yeni kuralı kaydetmek ve etkinleştirmek için **Kaydet** ' i seçin. 

    Birkaç dakika içinde, belirtilen e-posta hesabı e-posta almaya başlamalıdır. Uygulama, bir algılayıcı her defasında, nem düzeyinin koşuldaki değeri aştığını gösterdiği her seferinde e-posta gönderir.

## <a name="next-steps"></a>Sonraki adımlar
Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

* Azure IoT Central **Mağaza içi analiz-kullanıma alma** şablonunu kullanarak bir perakende mağaza uygulaması oluşturun
* Uygulama ayarlarını özelleştirme
* IoT cihaz şablonları oluşturma ve özelleştirme
* Cihazları uygulamanıza bağlama
* Koşulları izlemek için kurallar ve eylemler ekleme

Artık bir Azure IoT Central Condition izleme uygulaması oluşturduğunuza göre, önerilen sonraki adım aşağıda verilmiştir:

> [!div class="nextstepaction"]
> [Operatör panosunu özelleştirme](./tutorial-in-store-analytics-customize-dashboard-pnp.md)
