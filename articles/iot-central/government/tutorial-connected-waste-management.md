---
title: 'Öğretici: Azure IoT Central bağlı bir çöp yönetimi uygulaması oluşturma'
description: 'Öğretici: Azure IoT Central uygulama şablonlarını kullanarak bağlı bir çöp yönetimi uygulaması oluşturma oluşturmayı öğrenin.'
author: miriambrus
ms.author: miriamb
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 38da444779a56f39d4119f3797ddb5bd2f31aeae
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74112650"
---
# <a name="tutorial-create-a-connected-waste-management-application-in-iot-central"></a>Öğretici: IoT Central bağlı bir çöp yönetimi uygulaması oluşturma

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Bu öğretici, IoT Central **bağlı çöp yönetimi** uygulama şablonundan Azure IoT Central bağlı bir çöp yönetimi uygulaması oluşturmanıza kılavuzluk eder. 

Öğretici şunları yapmayı öğrenirsiniz: 

> [!div class="checklist"]
> * Bağlı çöp yönetimi uygulamanızı oluşturmak için Azure IoT Central **bağlı çöp yönetimi** şablonunu kullanın
> * Operatör panosunu keşfet ve özelleştirme 
> * Bağlı çöp kutusu cihaz şablonunu keşfet
> * Sanal cihazları keşfet
> * Kuralları keşfet ve yapılandırma
> * İşleri yapılandırma
> * Beyaz etiketleme kullanarak uygulama markanızı özelleştirme

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlayabilmeniz için şunlar gerekir:
-  Bir Azure aboneliği önerilir. İsteğe bağlı olarak 7 günlük ücretsiz deneme kullanabilirsiniz. Azure aboneliğiniz yoksa, [Azure kaydolma sayfasında](https://aka.ms/createazuresubscription)bir tane oluşturabilirsiniz.

## <a name="create-connected-waste-management-app-in-iot-central"></a>IoT Central bağlı çöp yönetimi uygulaması oluşturma

Bu bölümde, bağlı çöp yönetimi uygulamanızı IoT Central oluşturmak için Azure IoT Central **bağlı çöp yönetimi şablonunu** kullanacağız.

Yeni bir Azure IoT Central bağlı çöp yönetimi uygulaması oluşturmak için:  

1. [Azure IoT Central giriş sayfası](https://aka.ms/iotcentral) Web sitesine gidin.

    Azure aboneliğiniz varsa, ona erişmek için kullandığınız kimlik bilgileriyle oturum açın, aksi takdirde Microsoft hesabı kullanarak oturum açın:

    ![Kuruluş hesabınızı girin](./media/tutorial-connectedwastemanagement/sign-in.png)

2. Sol bölmeden **Oluştur** ' a tıklayın ve **kamu** sekmesini seçin. Kamu sayfasında çeşitli kamu uygulaması şablonları görüntülenir.

    ![Kamu uygulaması şablonları oluşturun](./media/tutorial-connectedwastemanagement/iotcentral-government-tab-overview.png)

1. **Bağlı çöp yönetimi** uygulama şablonunu seçin. Bu şablon, örnek bağlı çöp kutusu cihaz şablonu, sanal cihaz, işleç panosu ve önceden yapılandırılmış izleme kurallarını içerir.    

2. Aşağıdaki alanlarla **Yeni uygulama** oluşturma formunu açacak **uygulama oluştur**' a tıklayın:
    * **Uygulama adı**. Varsayılan olarak, uygulama *bağlı çöp yönetimini* ve ardından IoT Central oluşturduğu BENZERSIZ bir kimlik dizesini kullanır. İsteğe bağlı olarak, kolay bir uygulama adı seçin. Uygulama adını daha sonra değiştirebilirsiniz.
    * **URL** : isteğe bağlı olarak, istediğiniz URL 'yi seçebilirsiniz. URL 'YI daha sonra değiştirebilirsiniz. 
    * Azure aboneliğiniz varsa *dizininizi, Azure aboneliğinizi ve bölgenizi*girin. Aboneliğiniz yoksa, **7 günlük ücretsiz denemeyi** etkinleştirebilir ve gerekli iletişim bilgilerini tamamlayabilirsiniz.  

    Dizinler ve abonelikler hakkında daha fazla bilgi için bkz. [Uygulama oluşturma hızlı başlangıcı](../preview/quick-deploy-iot-central.md).

5. Sayfanın alt kısmındaki **Oluştur** düğmesine tıklayın. 

    ![Azure IoT Central bağlı çöp uygulama oluştur sayfası](./media/tutorial-connectedwastemanagement/new-application-connectedwastemanagement.png)

6. Artık Azure IoT Central **bağlı çöp yönetimi şablonunu**kullanarak bağlı bir çöp yönetimi uygulaması oluşturdunuz. 

Tebrikler! Yeni oluşturduğunuz uygulamanız önceden yapılandırılmış olarak gelir:
* Örnek işleç panoları
* Örnek önceden tanımlanmış bağlı çöp kutusu cihaz şablonları
* Benzetimli bağlı Atık sepeti cihazları
* Önceden yapılandırılmış kurallar ve işler
* Beyaz etiketleme kullanarak örnek marka 

Bu sizin uygulamanız ve dilediğiniz zaman değiştirebilirsiniz. Şimdi uygulamayı araştırıp bazı özelleştirmeler yapalim.  

## <a name="explore-and-customize-operator-dashboard"></a>Operatör panosunu keşfet ve özelleştirme 
Uygulamayı oluşturduktan sonra, **geniş bir atık bağlantılı çöp yönetimi panosuna**olursunuz.

   ![Bağlı çöp yönetimi panosu](./media/tutorial-connectedwastemanagement/connectedwastemanagement-dashboard1.png)

Bir Oluşturucu olarak, işleçler için panoda görünümler oluşturabilir ve özelleştirebilirsiniz. Özelleştirmeyi denemeden önce Panoyu keşfedelim. 

> [!NOTE]
> Panoda görünen tüm veriler, sonraki bölümde araştırılan sanal cihaz verilerini temel alır. 

Pano farklı kutucuk türlerinden oluşur:

* ***Geniş dünya atık yardımcı programı resim kutucuğu***: panodaki ilk kutucuk, kurgusal bir atık yardımcı programın "geniş dünya atık" bir görüntü kutucuğudur. Kutucuğu özelleştirebilir ve kendi görüntünüzü yerleştirebilir veya kaldırabilirsiniz. 

* ***Çöp kutusu resim kutucuğu***: bir açıklayıcı metinle birlikte izlenmekte olan cihazın görsel gösterimini oluşturmak için görüntü ve içerik kutucukları kullanabilirsiniz. 

* ***Doldur DÜZEYI KPI kutucuğu***: kutucuk, bir çöp kutusu içindeki bir *Fill level* algılayıcısı tarafından bildirilen bir değeri görüntüler. Bir çöp kutusu ölçüm *düzeyi* ve *oçi ölçümü* ya da *ağırlığı* gibi diğer sensörlere uzaktan izlenebilirler. Bir operatör çöp toplama kamyonu dağıtma gibi eylem gerçekleştirebilir. 

*  ***Çöp izleme alanı Haritası***: eşleme, Azure IoT Central 'da doğrudan yapılandırabileceğiniz Azure haritalar 'ı kullanıyor. Harita kutucuğu cihaz konumunu görüntülüyor. Haritanın üzerine gelin ve yakınlaştırma, büyütme veya genişletme gibi harita üzerindeki denetimleri deneyin.

     ![Bağlı çöp yönetimi Pano eşlemesi](./media/tutorial-connectedwastemanagement/connectedwastemanagement-dashboard-map.png)


* ***dolgusu, oçi, ağırlık düzeyi çubuk grafiği**: bir çubuk grafikte bir veya birden çok cihaz telemetri verisi görselleştirebilirsiniz. Çubuk grafiği de genişletebilirsiniz.  

  ![Bağlı çöp yönetimi Pano çubuğu grafiği](./media/tutorial-connectedwastemanagement/connectedwastemanagement-dashboard-barchart.png)


* **Alan Hizmetleri içerik kutucuğu**: Pano, Azure IoT Central uygulamanızdan Dynamics 365 alan hizmetleriyle tümleştirme bağlantısı içerir. Örnek olarak, çöp toplama Hizmetleri 'ni dağıtırken Bilet oluşturmak için alan hizmetlerini kullanabilirsiniz. 


### <a name="customize-dashboard"></a>Panoyu özelleştirme 

Oluşturucu olarak, işleçler için panodaki görünümleri özelleştirebilirsiniz. Şunları deneyebilirsiniz:
1. **Geniş dünya bağlantılı çöp kutusu yönetim panosunu**özelleştirmek için **Düzenle** ' ye tıklayın. **Düzen** menüsüne tıklayarak panoyu özelleştirebilirsiniz. Pano **düzenleme** modundayken, yeni kutucuk ekleyebilir veya 

    ![Panoyu Düzenle](./media/tutorial-connectedwastemanagement/edit-dashboard.png)

2. Yeni Pano oluşturmak ve sıfırdan yapılandırmak için **+ Yeni** seçeneğine de tıklayabilirsiniz. Birden çok panonuz olabilir ve Pano menüsünden panolarınız arasında gezinebilirsiniz. 

## <a name="explore-connected-waste-bin-device-template"></a>Bağlı çöp kutusu cihaz şablonunu keşfet

Azure IoT Central bir cihaz şablonu, telemetri, özellikler veya komut olabilen bir cihazın yeteneklerini tanımlar. Bir Oluşturucu olarak, bağlandığınız cihazların yeteneklerini temsil eden cihaz şablonlarını tanımlayabilirsiniz. 
 

**Bağlı çöp yönetimi** uygulaması, örnek bağlı bir çöp kutusu cihaz şablonuyla birlikte gelir.

Cihaz şablonunu görüntülemek için:

1. IoT Central ' de uygulamanızın sol gezinti bölmesinden **cihaz şablonları** ' na tıklayın. 

    ![Cihaz şablonu](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devicetemplate.png)

2. Cihaz şablonları listesinde **bağlı çöp kutusu**' nu görürsünüz. Ada tıklayarak açın.

3. Familirize cihaz şablonuyla özelliklere. Bunun için, *Fill düzeyi*, *oçi ölçümü*, *Ağırlık*, *konum* vb. gibi algılayıcılar tanımlanabileceğini görebilirsiniz.

   ![Cihaz şablonu](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devicetemplate-connectedbin.png)

   

### <a name="customizing-the-device-template"></a>Cihaz şablonunu özelleştirme

Aşağıdakileri özelleştirmeyi deneyin:
1. Cihaz şablonu menüsünden **Özelleştir** 'e gidin
2. `Odor meter` telemetri türünü bulun
3. `Odor meter` **görünen adını** `Odor level` olarak güncelleştir
4. Ayrıca, ölçüm birimini Güncelleştir ' i veya *Minimum değer* ve *en büyük değeri* ayarla ' yı deneyebilirsiniz
5. Değişiklikleri **Kaydet** 

### <a name="add-a-cloud-property"></a>Bulut özelliği Ekle 

1. Cihaz şablonu menüsünden **Cloud özelliğine** gidin
2. **+ Bulut özelliği Ekle**' ye tıklayarak yeni bir bulut özelliği ekleyin. IoT Central, cihazla ilgili olan ancak cihaz tarafından gönderilmesi beklenmediği bir özelliği ekleyebilirsiniz. Örnek olarak, bir bulut özelliği yükleme alanına, varlık bilgilerine veya bakım bilgilerine özgü bir uyarı eşiğine sahip olabilir. 
3. Değişiklikleri **Kaydet** 
 
### <a name="views"></a>Görünümler 
* Bağlı çöp kutusu cihaz şablonu önceden tanımlanmış görünümlerle gelir. Görünümleri keşfedebilir ve güncelleştirme yapabilirsiniz. Görünümler, operatörlerin cihaz verilerini nasıl göre, ancak bulut özelliklerinin de nasıl görüntüleneceğini tanımlar. 

  ![Cihaz şablonu görünümleri](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devicetemplate-views.png)

### <a name="publish"></a>Yayımlama 

* Herhangi bir değişiklik yaptıysanız, cihaz şablonunu **yayımlamayı** unutmayın. 

### <a name="create-a-new-device-template"></a>Yeni cihaz şablonu oluşturma 

* Yeni bir cihaz şablonu oluşturmak ve oluşturma işlemini izlemek için **+ Yeni** ' yi seçin. Sıfırdan özel bir cihaz şablonu oluşturabileceğiniz gibi, Azure cihaz kataloğundan bir cihaz şablonu seçebilirsiniz. 

## <a name="explore-simulated-devices"></a>Sanal cihazları keşfet

IoT Central, cihaz şablonunuzu ve uygulamanızı test etmek için sanal cihazlar oluşturabilirsiniz. 

**Bağlı çöp** kutusu cihaz şablonuyla eşleştirilmiş iki sanal cihaz vardır. 

### <a name="to-view-the-devices"></a>Cihazları görüntülemek için:

1. IoT Central sol gezinti bölmesinden **cihaz** ' a gidin. 

   ![Cihazlar](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devices.png)

2. Bağlı çöp kutusu cihazını seçin ve tıklayın.  

     ![Cihaz 1](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devices-bin1.png)

3. **Bulut özellikleri** sekmesine gidin `95` `Bin full alert threshold` değerini `100`olarak güncelleştirmeyi deneyin. 
* **Cihaz özellikleri** sekmesini ve **cihaz panosu** sekmesini bulun. 

> [!NOTE]
> Tüm sekmelerin **cihaz şablonu görünümlerinden**yapılandırıldığını unutmayın.

### <a name="add-new-devices"></a>Yeni cihaz ekle

* **Cihazlar** sekmesinde **+ Yeni** ' ye tıklayarak yeni cihazlar ekleyebilirsiniz. 

## <a name="explore-and-configure-rules"></a>Kuralları keşfet ve yapılandırma

Azure IoT Central 'de cihaz Telemetriyi otomatik olarak izlemek için kurallar oluşturabilir ve bir veya daha fazla koşul karşılandığında eylemleri tetikleyebilirsiniz. Eylemler e-posta bildirimleri göndermeyi, Microsoft Flow eylemi tetiklemeyi veya diğer hizmetlere veri göndermek için bir Web kancası eylemini içerebilir.

**Bağlı çöp yönetimi** uygulamasının dört örnek kuralı vardır.

### <a name="to-view-rules"></a>Kuralları görüntülemek için:
1. IoT Central sol gezinti bölmesinden **kurallara** git

   ![Kurallar](./media/tutorial-connectedwastemanagement/connectedwastemanagement-rules.png)

2. **Bin tam uyarısını** seçin

     ![Bin tam uyarısı](./media/tutorial-connectedwastemanagement/connectedwastemanagement-binfullalert.png)

 3. `Bin full alert` **koşulun** `Fill level is greater than or equal to Bin full alert threshold`ne zaman olduğunu denetler.

    `Bin full alert threshold`, `Connected waste bin` cihaz şablonunda tanımlanan bir *bulut özelliğidir* . 

Şimdi bir e-posta eylemi oluşturalım.

### <a name="create-an-email-action"></a>E-posta eylemi oluşturma
Kuralın Eylemler listesinde bir e-posta eylemi yapılandırmak için:
1. **+ E-posta**seçeneğini belirleyin. 
2. Eylem için kolay **görünen ad** olarak *yüksek pH uyarısı* girin.
3. **' Deki**IoT Central hesabınızla ilişkili e-posta adresini girin. 
4. İsteğe bağlı olarak, e-posta metnine dahil etmek için bir Note girin.
5. Eylemi gerçekleştirmek için **bitti** ' yi seçin.
6. Yeni kuralı kaydetmek ve etkinleştirmek için **Kaydet** ' i seçin. 

Yapılandırılan **koşul** karşılandığında e-posta almalısınız.

> [!NOTE]
> Bir koşul her karşılandığında uygulama e-posta gönderir. Otomatik kuraldan e-posta almayı durdurma kuralını **devre dışı bırakın** . 
  
Yeni bir kural oluşturmak için: 
1. Sol gezinti bölmesindeki **kurallarda** **+ Yeni** ' yi seçin.

## <a name="configure-jobs"></a>Işleri yapılandırma

IoT Central, işler cihaz veya bulut özellikleri güncelleştirmelerini birden çok cihazda tetiklemeniz sağlar. Özelliklere ek olarak, birden çok cihazda cihaz komutlarını tetiklemek için işleri de kullanabilirsiniz. IoT Central, iş akışını sizin için otomatikleştirecektir. 

1. Sol gezinti bölmesindeki **işler** ' e gidin. 
2. **+ Yeni** ' ye tıklayın ve bir veya daha fazla iş yapılandırın. 


## <a name="customize-your-application"></a>Uygulamanızı özelleştirme 

Bir Oluşturucu olarak, uygulamanızdaki Kullanıcı deneyimini özelleştirmek için çeşitli ayarları değiştirebilirsiniz.

### <a name="to-change-the-application-theme"></a>Uygulama temasını değiştirmek için:

1. **Yönetim > uygulamanızı özelleştirmek için yöneticinize**gidin.
3. **Uygulama logosu**olarak karşıya yüklenecek bir görüntü seçmek için **Değiştir** düğmesini kullanın.
4. Tarayıcı sekmelerinde görünecek bir **tarayıcı simgesi** görüntüsü seçmek için **Değiştir** düğmesini kullanın.
5. Varsayılan **tarayıcı renklerini** , HTML onaltılık renk kodları ekleyerek de değiştirebilirsiniz.

   ![Azure IoT Central uygulamanızı özelleştirme](./media/tutorial-connectedwastemanagement/connectedwastemanagement-customize-your-application.png)

6. Uygulama görüntülerini **yönetim > uygulama ayarları** ' na giderek ve **Görüntü düğmesini seçerek** de uygulama görüntüsü olarak karşıya yüklenecek bir görüntü seçebilirsiniz.
7. Son olarak, uygulamanın künyesi üzerindeki **Ayarlar** ' a tıklayarak **Temayı** da değiştirebilirsiniz.

  
## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu uygulamayı kullanmaya devam etmeyecekecekseniz, aşağıdaki adımlarla uygulamanızı silin:

1. IoT Central uygulamanızın sol bölmesindeki Yönetim sekmesini açın.
2. Uygulama ayarları ' nı seçin ve sayfanın altındaki Sil düğmesine tıklayın.

  

## <a name="next-steps"></a>Sonraki adımlar

* [Bağlı çöp yönetimi kavramları](./concepts-connectedwastemanagement-architecture.md) hakkında daha fazla bilgi edinin
