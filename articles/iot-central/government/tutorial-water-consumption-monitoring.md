---
title: 'Öğretici: Azure IoT Central su tüketim izleme uygulaması oluşturma'
description: 'Öğretici: Azure IoT Central uygulama şablonlarını kullanarak su tüketim izleme uygulaması oluşturmayı öğrenin.'
author: miriambrus
ms.author: miriamb
ms.date: 12/11/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: b65013bbf21faa8bffdcf799a991952b69f5fead
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714479"
---
# <a name="tutorial-create-a-water-consumption-monitoring-application-with-azure-iot-central"></a>Öğretici: Azure IoT Central su tüketim izleme uygulaması oluşturma

Bu öğreticide, Azure IoT Central su tüketim izleme uygulaması şablonunu kullanarak Azure IoT Central su tüketim izleme uygulaması oluşturma işlemlerinin nasıl yapılacağı gösterilmektedir.

Bu öğreticide şunların nasıl yapıldığını öğrenirsiniz:

> [!div class="checklist"]
> * Su tüketim izleme uygulamanızı oluşturmak için Azure IoT Central su tüketimi İzleme şablonunu kullanın.
> * Operatör panosunu gezin ve özelleştirin.
> * Cihaz şablonlarını keşfet.
> * Sanal cihazları keşfet.
> * Kuralları keşfet ve yapılandırın.
> * İşleri yapılandırma.
> * Beyaz etiketleme kullanarak uygulama markanızı özelleştirin.

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlamak için aşağıdakiler gerekir:

- Azure aboneliği. Azure aboneliğiniz yoksa, [Azure kaydolma sayfasında](https://aka.ms/createazuresubscription)bir tane oluşturabilirsiniz.

## <a name="create-a-water-consumption-monitoring-app-with-azure-iot-central"></a>Azure IoT Central su tüketim izleme uygulaması oluşturma

Bu bölümde, Azure IoT Central 'de su tüketim izleme uygulamanızı oluşturmak için Azure IoT Central su tüketimi İzleme şablonunu kullanırsınız.

Yeni bir Azure IoT Central su tüketim izleme uygulaması oluşturmak için:

1. [Azure IoT Central giriş sayfası](https://aka.ms/iotcentral) Web sitesine gidin.

    Azure aboneliğiniz varsa, ona erişmek için kullandığınız kimlik bilgileriyle oturum açın. Aksi takdirde, Microsoft hesabı kullanarak oturum açın.

    ![Kuruluş hesabınızı girin](media/tutorial-waterconsumptionmonitoring/sign-in.png)

1. Sol bölmede **Oluştur** ' u seçin ve **kamu** sekmesini seçin. **Kamu** sayfasında çeşitli kamu uygulaması şablonları görüntülenir.

   ![Kamu uygulaması şablonları oluşturun](./media/tutorial-waterconsumptionmonitoring/iotcentral-government-tab-overview1.png)

1. **Su tüketim izleme** uygulaması şablonunu seçin.
Bu şablon, örnek bir su tüketim cihaz şablonu, sanal cihaz, bir operatör panosu ve önceden yapılandırılmış izleme kurallarını içerir.

1. **Yeni uygulama** oluşturma formunu aşağıdaki alanlarla açmak Için **uygulama oluştur** ' u seçin:
    * **Uygulama adı**: varsayılan olarak, uygulama *su tüketim izlemeyi* kullanır ve ardından Azure IoT Central 'nın oluşturduğu benzersiz bir kimlik dizesidir. İsteğe bağlı olarak, kolay bir uygulama adı seçin. Uygulama adını da daha sonra değiştirebilirsiniz.
    * **URL**: Azure IoT Central, uygulama adına göre bir URL oluşturur. URL 'YI dilediğiniz şekilde güncelleştirmeyi tercih edebilirsiniz. URL 'yi daha sonra da değiştirebilirsiniz.
    * Azure aboneliğiniz varsa, **dizininizi**, **Azure aboneliğinizi** ve **konum** bilgilerini girin. Aboneliğiniz yoksa, **7 günlük ücretsiz deneme** seçeneğini belirleyip gerekli iletişim bilgilerini tamamlayabilirsiniz.

    Dizinler ve abonelikler hakkında daha fazla bilgi için bkz. [uygulama hızlı başlangıcı oluşturma](../core/quick-deploy-iot-central.md).

1. Sayfanın alt kısmında **Oluştur** ' u seçin.

    ![Azure IoT Central yeni uygulama sayfası](./media/tutorial-waterconsumptionmonitoring/new-application-waterconsumptionmonitoring.png)

    ![Azure IoT Central faturalandırma bilgileri sayfası](./media/tutorial-waterconsumptionmonitoring/new-application-waterconsumptionmonitoring-billinginfo.png)

Artık Azure IoT Central su tüketim İzleme şablonunu kullanarak bir su tüketim izleme uygulaması oluşturdunuz.

Su tüketim izleme uygulaması önceden yapılandırılmış olarak gelir:

* Örnek işleç panoları.
* Örnek önceden tanımlanmış su akışı ve Vana cihaz şablonları.
* Sanal su akışı ve akıllı vana cihazları.
* Kurallar ve işler.
* Beyaz etiketleme kullanarak örnek marka.

Bu sizin uygulamanız ve dilediğiniz zaman değiştirebilirsiniz. Şimdi uygulamayı araştıralım ve bazı özelleştirmeler yapalim.

## <a name="explore-and-customize-the-operator-dashboard"></a>Operatör panosunu keşfet ve özelleştirme

Uygulamayı oluşturduktan sonra örnek **geniş dünya su tüketim panosu** açılır.

   ![Su tüketim izleme panosu](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-dashboardfull.png)

Bir Oluşturucu olarak, işleçler için panoda görünümler oluşturabilir ve özelleştirebilirsiniz. Özelleştirmeyi denemeden önce Panoyu araştıralım.

> [!NOTE]
> Panoda görünen tüm veriler, bir sonraki bölümde araştırılacak olan sanal cihaz verilerini temel alır.
  
Pano farklı kutucuk türlerinden oluşur:

* **Geniş dünya su yardımcı programı resim kutucuğu**: panodaki ilk kutucuk, kurgusal su yardımcı programı genelindeki dünya su bir görüntü kutucuğudur. Kutucuğu, kendi resminizi ekleyerek veya kaldırarak özelleştirebilirsiniz.
* **Ortalama su akışı KPI kutucuğu**: KPI kutucuğu, *son 30 dakika içinde ortalama* bir örnek olarak görüntülenmek üzere yapılandırılmıştır. KPI kutucuğunu özelleştirebilir ve farklı bir tür ve zaman aralığına ayarlayabilirsiniz.
* **Cihaz komut kutucukları**: Bu kutucuklar **Kapatma vanası**, **Açık Vana** ve **vanasız konum** kutucukları içerir. Komutların seçilmesi sizi sanal cihaz komut sayfasına götürür. Azure IoT Central, bir *komut* bir *cihaz yetenek* türüdür. Bu kavramı daha sonra Bu öğreticinin "cihaz şablonu" bölümünde araştıracağız.
* **Su dağıtım alanı Haritası**: eşleme, Azure IoT Central 'da doğrudan yapılandırabileceğiniz Azure haritalarını kullanır. Harita kutucuğu cihaz konumunu görüntüler. Haritanın üzerine gelin ve harita üzerinde *Yakınlaştır*, *uzaklaştır* veya *Genişlet* gibi denetimleri deneyin.

    ![Su tüketim izleme Pano Haritası](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-dashboard-map.png)

* **Ortalama su akış çizgisi grafiği** ve **çevresel koşul çizgisi grafiği**: istenen bir zaman aralığında çizgi grafik olarak çizilen bir veya daha fazla cihaz Telemetriler görselleştirebilirsiniz.
* **Ortalama Vana basıncı ısı haritasını grafiği**: bir renk diziniyle bir zaman aralığında dağıtılmış görmek istediğiniz cihaz Telemetri verilerinin ısı haritasını görselleştirme türünü seçebilirsiniz.
* **Uyarı eşiklerini Sıfırla içerik kutucuğu**: eylem çağrısı içerik kutucukları dahil edebilir ve bir eylem sayfasına bağlantı katabilirsiniz. Bu durumda, sıfırlama uyarısı eşiği sizi cihaz özelliklerine yönelik güncelleştirmeleri çalıştırabileceğiniz uygulama **işlerine** götürür. Daha sonra Bu öğreticinin "işleri yapılandırma" bölümünde bu seçeneği keşfedeceğiz.
* **Özellik kutucukları**: Pano, **Vana işlem bilgilerini**, **akış uyarısı eşiklerini** ve **bakım bilgileri** kutucuklarını görüntüler.

### <a name="customize-the-dashboard"></a>Panoyu özelleştirme

Bir Oluşturucu olarak, işleçler için panodaki görünümleri özelleştirebilirsiniz.

1. **Geniş dünya su tüketim panosunu** özelleştirmek için **Düzenle** ' yi seçin. Panoyu, **Düzenle** menüsünü seçerek özelleştirebilirsiniz. Pano **düzenleme** modundayken, yeni kutucuk ekleyebilir veya onu yapılandırabilirsiniz.

     ![Panoyu düzenle](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-edit-dashboard.png)

1. Yeni bir pano oluşturmak ve sıfırdan yapılandırmak için **+ Yeni** ' yi seçin. Birden çok panonuz olabilir ve Pano menüsündeki panolarınız arasında geçiş yapabilirsiniz.

## <a name="explore-the-device-template"></a>Cihaz şablonunu keşfet

Azure IoT Central bir cihaz şablonu, telemetri, özellik veya komut olabilecek bir cihazın yeteneklerini tanımlar. Bir Oluşturucu olarak, Azure IoT Central, bağlandığınız cihazların yeteneklerini temsil eden bir veya daha fazla cihaz şablonu tanımlayabilirsiniz.

Su tüketim izleme uygulaması, *akış ölçer* ve *akıllı vana* cihazını temsil eden iki başvuru cihaz şablonu ile birlikte gelir.

Cihaz şablonunu görüntülemek için:

1. Azure IoT Central 'de uygulamanızın sol bölmesindeki **cihaz şablonları** ' nı seçin. **Cihaz şablonları** listesinde, iki cihaz şablonu, **akıllı vana** ve **akış ölçümü** görürsünüz.

   ![Cihaz şablonu](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devicetemplate.png)

1. **Akış ölçer** cihaz şablonunu seçin ve cihaz yeteneklerini öğrenmeye çalışın.

     ![Cihaz şablonu akış ölçümü](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devicetemplate-flowmeter.png)

### <a name="customize-the-device-template"></a>Cihaz şablonunu özelleştirme

Cihaz şablonunu özelleştirmek için:

1. **Cihaz şablonları** menüsünde **Özelleştir** ' e gidin.
1. `Temperature`Telemetri türünü bulun.
1. **Görünen adını** `Temperature` olarak güncelleştirin `Reported temperature` .
1. Ölçüm birimini güncelleştirin veya **en küçük değeri** ve **en büyük değeri** ayarlayın.
1. Değişiklikleri kaydetmek için **Kaydet** ' i seçin.

### <a name="add-a-cloud-property"></a>Bulut özelliği Ekle

1. **Cihaz şablonları** menüsünde **bulut özellikleri** ' ne gidin.
1. **+ Bulut özelliği Ekle** seçeneğini belirleyerek yeni bir bulut özelliği ekleyin.
    Azure IoT Central, cihazla ilgili bir özellik ekleyebilirsiniz. Örnek olarak, bir bulut özelliği bir yükleme alanına, varlık bilgilerine veya diğer bakım bilgilerine özgü bir uyarı eşiğine sahip olabilir.
1. Değişiklikleri kaydetmek için **Kaydet** ' i seçin.

### <a name="views"></a>Görünümler

Su tüketim İzleyicisi cihaz şablonu önceden tanımlanmış görünümlerle gelir. Görünümleri keşfedebilir ve güncelleştirme yapabilirsiniz. Görünümler, işleçlerin cihaz verilerini nasıl gördiğine, ayrıca bulut özelliklerini de girdiğine tanımlar.

  ![Cihaz şablonu görünümleri](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devicetemplate-views.png)

### <a name="publish"></a>Yayımlama

Herhangi bir değişiklik yaptıysanız, cihaz şablonunu **yayımladığınızdan** emin olun.

### <a name="create-a-new-device-template"></a>Yeni cihaz şablonu oluşturma

Yeni bir cihaz şablonu oluşturmak ve oluşturma işlemini izlemek için **+ Yeni** ' yi seçin.
Sıfırdan özel bir cihaz şablonu oluşturabilir veya Azure cihaz kataloğundan bir cihaz şablonu seçebilirsiniz.

## <a name="explore-simulated-devices"></a>Sanal cihazları keşfet

Azure IoT Central, cihaz şablonunuzu ve uygulamanızı test etmek için sanal cihazlar oluşturabilirsiniz. Su tüketim izleme uygulamasının **akış ölçer** ve **akıllı vana** cihaz şablonlarıyla eşleştirilmiş iki sanal cihaz vardır.

### <a name="view-the-devices"></a>Cihazları görüntüleme

1. Sol bölmedeki **cihazlar**  >  **tüm cihazlar** ' ı seçin.

   ![Tüm cihazlar bölmesi](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devices.png)

1. **Akıllı vana 1**' i seçin.

    ![Akıllı vana 1](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitor-device1.png)

1. **Komutlar** sekmesinde, **akıllı vana** cihaz şablonunda tanımlanan yetenekler olan üç cihaz komutunu **(kapatma vanası**, **Açık Vana** ve **set Vana konumu**) görebilirsiniz.

1. **Cihaz özellikleri** sekmesini ve **cihaz panosu** sekmesini bulun.

> [!NOTE]
> Tüm sekmelerin cihaz şablonu görünümlerinden yapılandırıldığını unutmayın.

### <a name="add-new-devices"></a>Yeni cihaz ekle

**Cihazlar** sekmesinde **+ Yeni** ' ye tıklayarak yeni cihaz ekleyin.

## <a name="explore-and-configure-rules"></a>Kuralları keşfet ve yapılandırma

Azure IoT Central 'de, bir veya daha fazla koşul karşılandığında cihaz telemetrisini otomatik olarak izlemeye yönelik kurallar oluşturabilir ve eylemleri tetikleyebilirsiniz. Eylemler e-posta bildirimleri göndermeyi veya Microsoft Power otomatikleştir eylemini tetiklemeyi veya diğer hizmetlere veri göndermek için bir Web kancası eylemini içerebilir.

Oluşturduğunuz su tüketim izleme uygulamasının üç önceden yapılandırılmış kuralı vardır.

### <a name="view-rules"></a>Kuralları görüntüle

1. Sol bölmedeki **kurallar** ' ı seçin.

   ![Kurallar bölmesi](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-rules.png)

1. Uygulamanın önceden yapılandırılmış kurallarından biri olan **yüksek su akış uyarısı**' nı seçin.

     ![Yüksek pH uyarısı](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-highflowalert.png)

    `High water flow alert`Kural, koşula karşı denetlenecek şekilde yapılandırılır `Flow` `greater than` `Max flow threshold` . Maksimum akış eşiği, **akıllı vana** cihaz şablonunda tanımlanan bir bulut özelliğidir. Değeri `Max flow threshold` cihaz örneği başına ayarlanır.

Şimdi bir e-posta eylemi oluşturalım.

Kurala bir eylem eklemek için:

1. **+ E-posta** seçeneğini belirleyin.
1. Eylem için kolay **görünen ad** olarak **yüksek akış uyarısı** girin.
1. **' Deki** Azure IoT Central hesabınızla ilişkili e-posta adresini girin.
1. İsteğe bağlı olarak, e-posta metnine dahil etmek için bir Note girin.
1. Eylemi gerçekleştirmek için **bitti** ' yi seçin.
1. Yeni kuralı kaydetmek için **Kaydet** ' i seçin.
1. Kuralı etkinleştirin.

Birkaç dakika içinde, yapılandırılan koşul karşılandıktan sonra bir e-posta almalısınız.

> [!NOTE]
> Bir koşul her karşılandığında uygulama bir e-posta gönderir. Otomatik kuraldan e-posta almayı durdur kuralını devre dışı bırakmak için **devre dışı bırak** ' ı seçin.
  
Yeni bir kural oluşturmak için:

* Sol bölmedeki **kurallar** sekmesinde **+ Yeni** ' yi seçin.

## <a name="configure-jobs"></a>İşleri yapılandırma

Azure IoT Central, işler cihaz veya bulut Özellik güncelleştirmelerini birden çok cihazda tetiklemeniz için izin verir. Özelliklere ek olarak, birden çok cihazda cihaz komutlarını tetiklemek için işleri de kullanabilirsiniz. Azure IoT Central, iş akışını sizin için otomatikleştirir.

1. Sol bölmedeki **işler** ' i seçin.
1. **+ Yeni**' yi seçin ve bir veya daha fazla iş yapılandırın.

## <a name="customize-your-application"></a>Uygulamanızı özelleştirme

Bir Oluşturucu olarak, uygulamanızdaki Kullanıcı deneyimini özelleştirmek için çeşitli ayarları değiştirebilirsiniz.

1. Uygulamanızı **Özelleştir ' i seçin**  >  .
1. **Uygulama logosu** olarak karşıya yüklenecek bir görüntü seçmek için **Değiştir** düğmesini seçin.
1. Tarayıcı sekmelerinde görünecek bir **tarayıcı simgesi** görüntüsü seçmek için **Değiştir** düğmesini seçin.
1. Varsayılan **tarayıcı renklerini** , HTML onaltılık renk kodları ekleyerek de değiştirebilirsiniz.

   ![Uygulama logosu, tarayıcı simgesi ve tarayıcı renkleri için seçimler](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-customize-your-application.png)

1. Ayrıca, uygulama görüntülerini **Yönetim**  >  **uygulama ayarları**' nı seçerek değiştirebilirsiniz. Uygulama görüntüsü olarak karşıya yüklenecek bir görüntü seçmek için **Görüntü Seç** düğmesini seçin.
1. Son olarak, uygulamanın sağ üst köşesindeki **Ayarlar** simgesini seçerek **Temayı** da değiştirebilirsiniz.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu uygulamayı kullanmaya devam etmeyecekecekseniz, silin.

1. Azure IoT Central uygulamanızın sol bölmesinde **Yönetim** ' i seçin.
1. **Uygulama ayarları**' nı seçin ve ardından sayfanın alt kısmındaki **Sil** ' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

* Şu konular hakkında daha fazla bilgi edinin: 

> [!div class="nextstepaction"]
> [Su tüketim izleme kavramları](./concepts-waterconsumptionmonitoring-architecture.md).
