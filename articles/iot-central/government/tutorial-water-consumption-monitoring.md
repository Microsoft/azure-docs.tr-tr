---
title: 'Öğretici: Azure IoT Central su tüketim izleme uygulaması oluşturma'
description: 'Öğretici: Azure IoT Central uygulama şablonlarını kullanarak su tüketim izleme uygulaması oluşturmayı öğrenin.'
author: miriambrus
ms.author: miriamb
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 0b100e0bebc7c5b26449e396650da5434334075d
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74112630"
---
# <a name="tutorial-create-a-water-consumption-monitoring-application-in-iot-central"></a>Öğretici: IoT Central bir su tüketim izleme uygulaması oluşturma

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Bu öğretici, IoT Central su tüketim Izleme uygulaması şablonundan bir Azure IoT Central su tüketim izleme uygulaması oluşturmanıza kılavuzluk eder. 

Öğretici şunları yapmayı öğrenirsiniz: 

> [!div class="checklist"]
> * Su tüketim izleme uygulamanızı oluşturmak için Azure IoT Central **su tüketim izleme** şablonunu kullanın
> * Operatör panosunu keşfet ve özelleştirme 
> * Cihaz şablonunu keşfet
> * Sanal cihazları keşfet
> * Kuralları keşfet ve yapılandırma
> * İşleri yapılandırma
> * Beyaz etiketleme kullanarak uygulama markanızı özelleştirme

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlayabilmeniz için şunlar gerekir:
-  Bir Azure aboneliği önerilir. Azure aboneliğiniz yoksa, [Azure kaydolma sayfasında](https://aka.ms/createazuresubscription)bir tane oluşturabilirsiniz.

## <a name="create-water-consumption-monitoring-app-in-iot-central"></a>IoT Central su tüketim izleme uygulaması oluşturma

Bu bölümde, su tüketim izleme uygulamanızı IoT Central oluşturmak için Azure IoT Central **su tüketim İzleme şablonunu** kullanacağız.

Yeni bir Azure IoT Central su tüketim izleme uygulaması oluşturmak için:  

1. [Azure IoT Central giriş sayfası](https://aka.ms/iotcentral) Web sitesine gidin.

    Azure aboneliğiniz varsa, ona erişmek için kullandığınız kimlik bilgileriyle oturum açın, aksi takdirde Microsoft hesabı kullanarak oturum açın:

    ![Kuruluş hesabınızı girin](media/tutorial-waterconsumptionmonitoring/sign-in.png)

2. Sol bölmeden **Oluştur** ' a tıklayın ve **kamu** sekmesini seçin. Kamu sayfasında çeşitli kamu uygulaması şablonları görüntülenir.

   ![Kamu uygulaması şablonları oluşturun](./media/tutorial-waterconsumptionmonitoring/iotcentral-government-tab-overview1.png)

1. **Su tüketim izleme** uygulaması şablonunu seçin. Bu şablon örnek su tüketim cihaz şablonu, sanal cihaz, operatör panosu ve önceden yapılandırılmış izleme kurallarını içerir.    

2. Aşağıdaki alanlarla **Yeni uygulama** oluşturma formunu açacak **uygulama oluştur**' a tıklayın:
    * **Uygulama adı**: uygulama varsayılan olarak *su tüketim izlemeyi* kullanır ve ardından IoT Central üreten benzersiz bir kimlik dizesidir. İsteğe bağlı olarak, kolay bir uygulama adı seçin. Uygulama adını daha sonra değiştirebilirsiniz.
    * **URL**: IoT Central uygulama adına göre sizin için bir URL 'yi otomatik olarak üretir. URL 'YI dilediğiniz şekilde güncelleştirmeyi tercih edebilirsiniz. URL 'YI daha sonra değiştirebilirsiniz. 
    * Azure aboneliğiniz varsa *dizininizi, Azure aboneliğinizi ve bölgenizi*girin. Aboneliğiniz yoksa, **7 günlük ücretsiz denemeyi** etkinleştirebilir ve gerekli iletişim bilgilerini tamamlayabilirsiniz.  

    Dizinler ve abonelikler hakkında daha fazla bilgi için bkz. [Uygulama oluşturma hızlı başlangıcı](../preview/quick-deploy-iot-central.md).

5. Sayfanın alt kısmındaki **Oluştur** düğmesine tıklayın. 

    ![Azure IoT Central Uygulama Oluştur sayfası](./media/tutorial-waterconsumptionmonitoring/new-application-waterconsumptionmonitoring.png)

6. Artık Azure IoT Central **su tüketim izleme** şablonunu kullanarak bir su tüketim izleme uygulaması oluşturdunuz.

Tebrikler! Su kalitesi izleme uygulamanızı oluşturmayı tamamladınız ve önceden yapılandırılmış olarak gelir:
* Örnek işleç panoları
* Örnek önceden tanımlanmış su akışı ve Vana cihaz şablonları
* Sanal su akışı ve akıllı vana cihazları
* Önceden yapılandırılmış kurallar ve işler
* Beyaz etiketleme kullanarak örnek marka 

Bu sizin uygulamanız ve dilediğiniz zaman değiştirebilirsiniz. Şimdi uygulamayı araştırıp bazı özelleştirmeler yapalim.  

## <a name="explore-and-customize-operator-dashboard"></a>Operatör panosunu keşfet ve özelleştirme 
Uygulamayı oluşturduktan sonra, **geniş dünya su tüketimi izleme panosu**adlı örnek operatör panosuna gireceksiniz.

   ![Su tüketim Izleme panosu](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-dashboardfull.png)

Bir Oluşturucu olarak, işleçler için panoda görünümler oluşturabilir ve özelleştirebilirsiniz. Özelleştirmeyi denemeden önce Panoyu keşfedelim. 

> [!NOTE]
> Panoda görünen tüm veriler, bir sonraki bölümde araştırılan sanal cihaz verilerini temel alır. 
  
Pano farklı kutucuk türlerinden oluşur:

* **Geniş dünya su yardımcı programı resim kutucuğu**: panodaki ilk kutucuk, kurgusal bir su yardımcı programının "geniş dünya su" resim kutucuğudur. Kutucuğu özelleştirebilir ve kendi görüntünüzü yerleştirebilir veya kaldırabilirsiniz. 

* **Ortalama su akışı KPI kutucuğu**: KPI kutucuğu, *son 30 dakika içinde ortalama*bir örnek olarak görüntülenmek üzere yapılandırılmıştır. KPI kutucuklarını özelleştirebilir ve farklı bir tür ve zaman aralığına ayarlayabilirsiniz.

* Daha sonra Pano *cihaz komut* kutucuklarında **vanasını kapatmak**, **Vana açmak**veya **Vana konumunu ayarlamak**için sağ tarafta bulunur. Komutlara tıkladığınızda, sizi sanal cihaz cihazı komut sayfasına götürür. IoT Central bir *komut* , Bu öğreticinin **cihaz şablonu bölümünde** daha sonra araştırılacak bir *cihaz yetenek* türüdür.

*  **Su dağıtım alanı Haritası**: eşleme, Azure IoT Central 'da doğrudan yapılandırabileceğiniz Azure haritalar 'ı kullanıyor. Harita kutucuğu cihaz konumunu görüntülüyor. Haritanın üzerine gelin ve *Yakınlaştırma*, *büyütme* veya *genişletme*gibi harita üzerindeki denetimleri deneyin. 

    ![Su tüketim Izleme Pano Haritası](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-dashboard-map.png)

* **Ortalama su akış çizgisi grafiği** ve **ortam koşulları çizgi grafiği**: istenen bir zaman aralığında çizgi grafik olarak çizilen bir veya daha fazla cihaz Telemetriler görselleştirebilirsiniz.  

* **Ortalama Vana basıncı ısı haritasını grafiği**: bir renk diziniyle bir zaman aralığı boyunca dağıtıma bakmayı ilgilendiğiniz cihaz Telemetri verilerinin ısı haritasını görselleştirme türünü seçebilirsiniz.

* **Uyarı eşiği içerik kutucuğunu Sıfırla**: bir eylem sayfasına bağlantıyı gömmeye yönelik eylem içeriği kutucuklarına çağrı ekleyebilirsiniz. Bu durumda sıfırlama uyarısı eşiği sizi, Bu öğreticinin daha sonra **iş yapılandırma** bölümünde araştırılacak cihaz özelliklerine güncelleştirmeler çalıştırabileceğiniz uygulama **işlerine** götürür.

* **Özellik kutucukları**: Pano, cihaz özellikleri olan **Vana işlem bilgilerini**, **akış uyarısı eşiklerini**ve **bakım bilgilerini** görüntüler.  


### <a name="customize-dashboard"></a>Panoyu özelleştirme 

Oluşturucu olarak, işleçler için panodaki görünümleri özelleştirebilirsiniz. Şunları deneyebilirsiniz:
1. **Geniş dünya su tüketimi izleme panosunu**özelleştirmek için **Düzenle** ' ye tıklayın. **Düzen** menüsüne tıklayarak panoyu özelleştirebilirsiniz. Pano **düzenleme** modundayken, yeni kutucuk ekleyebilir veya 

     ![Panoyu Düzenle](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-edit-dashboard.png)

2. Yeni Pano oluşturmak ve sıfırdan yapılandırmak için **+ Yeni** seçeneğine tıklayın. Birden çok panonuz olabilir ve Pano menüsünden panolarınız arasında gezinebilirsiniz. 

## <a name="explore-device-template"></a>Cihaz şablonunu keşfet
Azure IoT Central bir cihaz şablonu, telemetri, özellik veya komut olabilecek bir cihazın yeteneklerini tanımlar. Bir Oluşturucu olarak, bağlandığınız cihazların yeteneklerini temsil eden IoT Central bir veya daha fazla cihaz şablonu tanımlayabilirsiniz. 
 

**Su tüketim izleme** uygulaması, *akış ölçerini* ve *akıllı vana* cihazını temsil eden iki başvuru cihaz şablonu ile birlikte gelir. 

Cihaz şablonunu görüntülemek için:

1. IoT Central ' de uygulamanızın sol gezinti bölmesinden **cihaz şablonları** ' na tıklayın. 
    Cihaz şablonları listesinde, iki cihaz şablonu **Flow meterr** ve **akıllı vana** 'yi görürsünüz

   ![Cihaz şablonu](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devicetemplate.png)

2. **Akış ölçer** cihaz şablonu ' na tıklayın ve cihaz özelliklerine alışın 

     ![Cihaz şablonu akış ölçümü](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devicetemplate-flowmeter.png)

### <a name="customizing-the-device-template"></a>Cihaz şablonunu özelleştirme

Aşağıdakileri özelleştirmeyi deneyin:
1. Cihaz şablonu menüsünden **Özelleştir** 'e gidin
2. `Temperature` telemetri türünü bulun
3. `Temperature` **görünen adını** `Reported temperature` olarak güncelleştir
4. Ölçüm birimini Güncelleştir veya *En düşük değer* ve *en büyük değeri* ayarla
5. Değişiklikleri **Kaydet** 

### <a name="add-a-cloud-property"></a>Bulut özelliği Ekle 
1. Cihaz şablonu menüsünden **Cloud özelliğine** gidin
2. **+ Bulut özelliği Ekle**' ye tıklayarak yeni bir bulut özelliği ekleyin. 
    IoT Central, cihazla ilgili bir özelliği ekleyebilirsiniz. Örnek olarak, bir bulut özelliği yükleme alanına, varlık bilgilerine veya bakım bilgilerine özgü bir uyarı eşiğine sahip olabilir. 
3. Değişiklikleri **Kaydet** 
 
### <a name="views"></a>Görünümler 
Su tüketim İzleyicisi cihaz şablonu önceden tanımlanmış görünümlerle gelir. Görünümleri keşfedebilir ve güncelleştirme yapabilirsiniz. Görünümler, operatörlerin cihaz verilerini nasıl göre, ancak bulut özelliklerinin de nasıl görüntüleneceğini tanımlar. 

  ![Cihaz şablonu görünümleri](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devicetemplate-views.png)

### <a name="publish"></a>Yayımlama 
Herhangi bir değişiklik yaptıysanız, cihaz şablonunu **yayımlamayı** unutmayın. 

### <a name="create-a-new-device-template"></a>Yeni cihaz şablonu oluşturma 
- Yeni bir cihaz şablonu oluşturmak ve oluşturma işlemini izlemek için **+ Yeni** ' yi seçin. Sıfırdan özel bir cihaz şablonu oluşturabileceğiniz gibi, Azure cihaz kataloğundan bir cihaz şablonu seçebilirsiniz. 

## <a name="explore-simulated-devices"></a>Sanal cihazları keşfet
IoT Central, cihaz şablonunuzu ve uygulamanızı test etmek için sanal cihazlar oluşturabilirsiniz. **Su tüketim izleme** uygulamasının *akış ölçer* ve *akıllı vana* cihaz şablonlarıyla eşleştirilmiş iki sanal cihaz vardır. 

### <a name="to-view-the-devices"></a>Cihazları görüntülemek için:
1. IoT Central sol gezinti bölmesinden **cihaz** ' a gidin. 

   ![Cihazlar](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-devices.png)

2. Tek bir **akıllı vana 1** öğesine tıklayın 

    ![Cihaz 1](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitor-device1.png)

3.  **Cihaz komutlarında** , üç cihaz komutunu *Açık Vana*, *vanadan kapatma*ve *akıllı vana* cihaz şablonunda tanımlanan özellik *vanası konumunu ayarlayabilirsiniz* . 
4. **Cihaz özellikleri** sekmesini ve **cihaz panosu** sekmesini bulun. 

> [!NOTE]
> Tüm sekmelerin cihaz şablonu görünümlerinden yapılandırıldığını unutmayın.

### <a name="add-new-devices"></a>Yeni cihaz ekle
* **Cihazlar** sekmesinde **+ Yeni** ' ye tıklayarak yeni cihaz ekleyin. 

## <a name="explore-and-configure-rules"></a>Kuralları keşfet ve yapılandırma

Azure IoT Central cihaz Telemetriyi otomatik olarak izlemek için kurallar oluşturabilir ve bir veya daha fazla koşul karşılandığında eylemleri tetikleyebilirsiniz. Eylemler e-posta bildirimleri göndermeyi veya diğer hizmetlere veri göndermek için bir Microsoft Flow eylemi veya Web kancası eylemini tetiklemesini içerebilir.

Oluşturduğunuz **su tüketim izleme** uygulamasının, önceden yapılandırılmış üç kuralı vardır.

### <a name="to-view-rules"></a>Kuralları görüntülemek için:
1. IoT Central sol gezinti bölmesinden **kurallar** ' a gidin. 

   ![Kurallar](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-rules.png)

2. Uygulamanın önceden yapılandırılmış kurallarından biri olan **yüksek pH uyarısı** ' nı seçin ve tıklayın.

     ![Yüksek pH uyarısı](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-highflowalert.png)

    `High flow alert` kural, `Max flow threshold``greater than` `Acidity (pH)` koşula karşı denetlenecek şekilde yapılandırılır. Maksimum akış eşiği, cihaz *akıllı vana* cihaz şablonunda tanımlanan bir bulut özelliğidir. `Max flow threshold` değeri cihaz örneği başına ayarlanır. 

Şimdi bir e-posta eylemi oluşturalım.

Kurala bir eylem eklemek için:

1. **+ E-posta**seçeneğini belirleyin. 
1. Eylem için kolay **görünen ad** olarak *yüksek pH uyarısı* girin.
    * **' Deki**IoT Central hesabınızla ilişkili e-posta adresini girin. 
1. İsteğe bağlı olarak, e-posta metnine dahil etmek için bir Note girin.
1. Eylemi gerçekleştirmek için **bitti** ' yi seçin.
1. Yeni kuralı kaydetmek ve etkinleştirmek için **Kaydet** ' i seçin. 

Birkaç dakika içinde, yapılandırılan **koşul** karşılandığında e-posta almalısınız.

> [!NOTE]
> Bir koşul her karşılandığında uygulama e-posta gönderir. Otomatik kuraldan e-posta almayı durdurma kuralını **devre dışı bırakın** . 
  
Yeni bir kural oluşturmak için: 
- Sol gezinti bölmesindeki **kurallarda** **+ Yeni** ' yi seçin.

## <a name="configure-jobs"></a>Işleri yapılandırma

IoT Central, işler cihaz veya bulut Özellik güncelleştirmelerini birden çok cihazda tetiklemeniz için izin verir. Özelliklere ek olarak, birden çok cihazda cihaz komutlarını tetiklemek için işleri de kullanabilirsiniz. IoT Central, iş akışını sizin için otomatikleştirecektir. 

1. Sol gezinti bölmesindeki **işler** ' e gidin. 
2. **+ Yeni** ' ye tıklayın ve bir veya daha fazla iş yapılandırın. 


## <a name="customize-your-application"></a>Uygulamanızı özelleştirme 
Bir Oluşturucu olarak, uygulamanızdaki Kullanıcı deneyimini özelleştirmek için çeşitli ayarları değiştirebilirsiniz.

1.  **Yönetim > uygulamanızı özelleştirmek için yöneticinize**gidin.
3. **Uygulama logosu**olarak karşıya yüklenecek bir görüntü seçmek için **Değiştir** düğmesini kullanın.
4. Tarayıcı sekmelerinde görünecek bir **tarayıcı simgesi** görüntüsü seçmek için **Değiştir** düğmesini kullanın.
5. Varsayılan **tarayıcı renklerini** , HTML onaltılık renk kodları ekleyerek de değiştirebilirsiniz.

   ![Azure IoT Central uygulamanızı özelleştirme](./media/tutorial-waterconsumptionmonitoring/waterconsumptionmonitoring-customize-your-application.png)

1.  Uygulama görüntülerini **yönetim > uygulama ayarları** ' na giderek ve **Görüntü düğmesini seçerek** de uygulama görüntüsü olarak karşıya yüklenecek bir görüntü seçebilirsiniz. 
2. Son olarak, uygulamanın künyesi üzerindeki **Ayarlar** ' a tıklayarak **Temayı** da değiştirebilirsiniz. 

  
## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu uygulamayı kullanmaya devam etmeyecekecekseniz, aşağıdaki adımlarla uygulamanızı silin:

1. IoT Central uygulamanızın sol bölmesindeki Yönetim sekmesini açın. 
2. Uygulama ayarları ' nı seçin ve sayfanın altındaki Sil düğmesine tıklayın. 


## <a name="next-steps"></a>Sonraki adımlar

* [Su tüketimi izleme kavramları](./concepts-waterconsumptionmonitoring-architecture.md) hakkında daha fazla bilgi edinin
