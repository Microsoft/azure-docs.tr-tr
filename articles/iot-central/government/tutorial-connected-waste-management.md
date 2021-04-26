---
title: 'Öğretici: Azure IoT Central bağlı bir çöp yönetimi uygulaması oluşturma'
description: 'Öğretici: Azure IoT Central uygulama şablonlarını kullanarak bağlı bir çöp yönetimi uygulaması derlemeyi öğrenin'
author: miriambrus
ms.author: miriamb
ms.date: 12/11/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: ff7cfb8c7aa8469111d4531da17c7cd184920f9b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101727575"
---
# <a name="tutorial-create-a-connected-waste-management-app"></a>Öğretici: bağlı bir çöp yönetimi uygulaması oluşturma

Bu öğreticide, bağlı bir çöp yönetimi uygulaması oluşturmak için Azure IoT Central nasıl kullanılacağı gösterilmektedir. 

Özellikle şunları nasıl yapacağınızı öğreneceksiniz: 

> [!div class="checklist"]
> * Uygulamanızı oluşturmak için Azure IoT Central *bağlı çöp yönetimi* şablonunu kullanın.
> * Operatör panosunu gezin ve özelleştirin. 
> * Bağlı çöp kutusu cihaz şablonunu gezin.
> * Sanal cihazları keşfet.
> * Kuralları keşfet ve yapılandırın.
> * İşleri yapılandırma.
> * Uygulama markanızı özelleştirin.

## <a name="prerequisites"></a>Önkoşullar

Bir Azure aboneliği önerilir. Alternatif olarak, 7 günlük ücretsiz deneme sürümünü kullanabilirsiniz. Azure aboneliğiniz yoksa, [Azure kaydolma sayfasında](https://aka.ms/createazuresubscription)bir tane oluşturabilirsiniz.

## <a name="create-your-app-in-azure-iot-central"></a>Uygulamanızı Azure IoT Central oluşturma

Bu bölümde, Azure IoT Central 'da uygulamanızı oluşturmak için bağlı çöp yönetimi şablonunu kullanırsınız. Aşağıdaki adımları uygulayın:

1. [Azure IoT Central](https://aka.ms/iotcentral)'ye gidin.

    Azure aboneliğiniz varsa, ona erişmek için kullandığınız kimlik bilgileriyle oturum açın. Aksi takdirde, Microsoft hesabı kullanarak oturum açın:

    ![Microsoft oturum açma ekranının ekran görüntüsü.](./media/tutorial-connectedwastemanagement/sign-in.png)

1. Sol bölmeden **Oluştur**' u seçin. Ardından **kamu** sekmesini seçin. Kamu sayfasında çeşitli kamu uygulaması şablonları görüntülenir.

    ![Azure IoT Central derleme sayfasının ekran görüntüsü.](./media/tutorial-connectedwastemanagement/iotcentral-government-tab-overview.png)

1. **Bağlı çöp yönetimi** uygulama şablonunu seçin. Bu şablon, örnek bağlı bir çöp kutusu cihaz şablonu, sanal cihaz, bir operatör panosu ve önceden yapılandırılmış izleme kurallarını içerir.    

1. **Yeni uygulama** iletişim kutusunu açan **uygulama oluştur**' u seçin. Aşağıdaki alanlar için bilgileri girin:
    * **Uygulama adı**. Varsayılan olarak, uygulama **bağlı çöp yönetimini** kullanır ve ardından Azure IoT Central üreten BENZERSIZ bir kimlik dizesidir. İsteğe bağlı olarak, kolay bir uygulama adı seçebilirsiniz. Uygulama adını da daha sonra değiştirebilirsiniz.
    * **URL**. İsteğe bağlı olarak, istediğiniz URL 'yi seçebilirsiniz. URL 'YI daha sonra değiştirebilirsiniz. 
    * **Fiyatlandırma planı**. Azure aboneliğiniz varsa, **faturalama bilgileri** iletişim kutusundaki uygun alanlara dizininizi, Azure aboneliğinizi ve bölgenizi girin. Aboneliğiniz yoksa, 7 günlük deneme aboneliğini etkinleştirmek için **ücretsiz** ' i seçin ve gerekli iletişim bilgilerini doldurun.  

    Dizinler ve abonelikler hakkında daha fazla bilgi için bkz. [hızlı başlangıç-Azure IoT Central uygulaması oluşturma](../core/quick-deploy-iot-central.md).

1. Sayfanın alt kısmındaki **Oluştur**' u seçin. 

    ![Azure IoT Central yeni uygulama oluştur iletişim kutusunun ekran görüntüsü.](./media/tutorial-connectedwastemanagement/new-application-connectedwastemanagement.png)
    
    ![Azure IoT Central faturalandırma bilgileri iletişim kutusunun ekran görüntüsü.](./media/tutorial-connectedwastemanagement/new-application-connectedwastemanagement-billinginfo.png)

 
Yeni oluşturduğunuz uygulamanız önceden yapılandırılmış olarak gelir:
* Örnek işleç panoları.
* Örnek önceden tanımlanmış bağlı çöp kutusu cihaz şablonları.
* Benzetimli bağlı çöp kutusu cihazları.
* Kurallar ve işler.
* Örnek marka. 

Bu sizin uygulamanız ve dilediğiniz zaman değiştirebilirsiniz. Şimdi uygulamayı araştırıp bazı özelleştirmeler yapalim.  

## <a name="explore-and-customize-the-operator-dashboard"></a>Operatör panosunu keşfet ve özelleştirme 

Uygulamanızı oluşturduktan sonra gördüğünüz **geniş dünya atık yönetimi panosuna** göz atın.

   ![Geniş dünya atık yönetim panosunun ekran görüntüsü.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-dashboard1.png)

Bir Oluşturucu olarak, işleçler için panoda görünümler oluşturabilir ve özelleştirebilirsiniz. İlk olarak panoyu keşfedelim. 

>[!NOTE]
>Panoda gösterilen tüm veriler, bir sonraki bölümde daha fazla bilgi göreceğiniz sanal cihaz verilerini temel alır. 

Pano farklı kutucuklardan oluşur:

* **Geniş dünya atık yardımcı programı resim kutucuğu**: panodaki ilk kutucuk, kurgusal bir çöp kutusu, "geniş dünya atık" olan bir görüntü kutucuğudur. Kutucuğu özelleştirebilir ve kendi yansımanıza koyabilirsiniz veya kaldırabilirsiniz. 

* **Çöp kutusu resim kutucuğu**: izlenen aygıtın bir açıklama ile birlikte bir görsel temsilini oluşturmak için görüntü ve içerik kutucukları kullanabilirsiniz. 

* **Doldur DÜZEYI KPI kutucuğu**: bu kutucuk, bir çöp kutusu içindeki bir *Fill level* algılayıcısı tarafından bildirilen bir değeri görüntüler. Bir çöp kutusu içindeki *oçi ölçümü* veya *ağırlığı* gibi, Fill düzeyi ve diğer sensörler, uzaktan izlenebilirler. Bir işleç, çöp toplama kamyonu gönderme gibi eylem gerçekleştirebilir. 

* **Çöp izleme alanı Haritası**: bu kutucuk, Azure IoT Central 'da doğrudan yapılandırabileceğiniz Azure haritalar 'ı kullanır. Harita kutucuğu cihaz konumunu görüntüler. Haritanın üzerine geldiğinizde, yakınlaştırma, büyütme veya genişletme gibi denetimleri eşleme üzerinde yapmayı deneyin.

     ![Bağlı çöp yönetimi şablonu Pano eşlemesinin ekran görüntüsü.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-dashboard-map.png)


* **Doldur, oçi, ağırlık düzeyi çubuk grafiği**: bir çubuk grafikte bir veya birden çok cihaz telemetri verisini görselleştirebilirsiniz. Çubuk grafiği de genişletebilirsiniz.  

  ![Bağlı çöp yönetimi şablonu Pano çubuğu grafiğinin ekran görüntüsü.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-dashboard-barchart.png)


* **Alan Hizmetleri**: Pano, Azure IoT Central uygulamanızın Dynamics 365 alan hizmetleriyle nasıl tümleştirileceği ile ilgili bir bağlantı içerir. Örneğin, çöp toplama Hizmetleri 'ni dağıtırken Bilet oluşturmak için alan hizmetlerini kullanabilirsiniz. 


### <a name="customize-the-dashboard"></a>Panoyu özelleştirme 

Panoyu, **Düzenle** menüsünü seçerek özelleştirebilirsiniz. Ardından, yeni kutucuklar ekleyebilir veya mevcut olanları yapılandırabilirsiniz. Panonun, düzen modunda nasıl göründüğü aşağıda verilmiştir: 

![Bağlı çöp yönetimi şablonu panosunun, düzen modunda ekran görüntüsü.](./media/tutorial-connectedwastemanagement/edit-dashboard.png)

Yeni bir pano oluşturmak ve sıfırdan yapılandırmak için **+ Yeni** ' yi de seçebilirsiniz. Birden çok panonuz olabilir ve Pano menüsünden panolarınız arasında geçiş yapabilirsiniz. 

## <a name="explore-the-device-template"></a>Cihaz şablonunu keşfet

Azure IoT Central bir cihaz şablonu, bir cihazın telemetri, özellik veya komutlarını içerebilen yeteneklerini tanımlar. Bir Oluşturucu olarak, bağlandığınız cihazların yeteneklerini temsil eden cihaz şablonlarını tanımlayabilirsiniz. 

Bağlı atık yönetim uygulaması, bağlı bir çöp kutusu cihazı için örnek şablonla birlikte gelir.

Cihaz şablonunu görüntülemek için:

1. Azure IoT Central uygulamanızın sol bölmesinde **cihaz şablonları**' nı seçin. 

    ![Uygulamadaki cihaz şablonlarının listesini gösteren ekran görüntüsü.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devicetemplate.png)

1. **Cihaz şablonları** listesinde **bağlı çöp kutusu**' nu seçin.

1. Cihaz şablonu yeteneklerini inceleyin. Bunun için, **Fill level**, **oçi ölçümü**, **Ağırlık** ve **konum** gibi algılayıcılar tanımladığını görebilirsiniz.

   ![Bağlı çöp kutusu cihaz şablonunun ayrıntılarını gösteren ekran görüntüsü.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devicetemplate-connectedbin.png)


### <a name="customize-the-device-template"></a>Cihaz şablonunu özelleştirme

Aşağıdakileri özelleştirmeyi deneyin:
1. Cihaz şablonu menüsünde **Özelleştir**' i seçin.
1. **Oçi ölçer** telemetri türünü bulun.
1. **Oçi ölçerin** **görünen adını** **oçi düzeyi** olarak güncelleştirin.
1. Ölçüm birimini güncelleştirmeyi deneyin veya **Minimum değer** ve **en büyük değeri** ayarlayın.
1. **Kaydet**’i seçin. 

### <a name="add-a-cloud-property"></a>Bulut özelliği Ekle 

Aşağıdaki adımları uygulayın:
1. Cihaz şablonu menüsünde, **bulut özelliği**' ni seçin.
1. **+ Bulut özelliği Ekle**' yi seçin. Azure IoT Central 'de, cihazla ilgili olan ancak bir cihaz tarafından gönderilmesi beklenmediği bir özellik ekleyebilirsiniz. Örneğin, bir bulut özelliği, yükleme alanına, varlık bilgilerine veya bakım bilgilerine özgü bir uyarı eşiği olabilir. 
1. **Kaydet**’i seçin. 
 
### <a name="views"></a>Görünümler 
Bağlı çöp kutusu cihaz şablonu önceden tanımlanmış görünümlerle gelir. Görünümleri araştırın ve isterseniz bunları güncelleştirin. Görünümler, işleçlerin cihaz verilerini ve giriş bulutu özelliklerini nasıl görekullandığını tanımlar. 

  ![Bağlı çöp yönetimi şablonu cihaz şablonları görünümlerinin ekran görüntüsü.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devicetemplate-views.png)

### <a name="publish"></a>Yayımlama 

Herhangi bir değişiklik yaptıysanız, cihaz şablonunu yayımlamayı unutmayın. 

### <a name="create-a-new-device-template"></a>Yeni cihaz şablonu oluşturma 

Yeni bir cihaz şablonu oluşturmak için **+ Yeni**' yi seçin ve adımları izleyin. Sıfırdan özel bir cihaz şablonu oluşturabilir veya Azure cihaz kataloğundan bir cihaz şablonu seçebilirsiniz. 

## <a name="explore-simulated-devices"></a>Sanal cihazları keşfet

Azure IoT Central, cihaz şablonunuzu ve uygulamanızı test etmek için sanal cihazlar oluşturabilirsiniz. 

Bağlı çöp kutusu cihaz şablonuyla ilişkili iki sanal cihaz vardır. 

### <a name="view-the-devices"></a>Cihazları görüntüleme

1. Azure IoT Central sol bölmesinden **cihaz**' ı seçin. 

   ![Bağlı çöp yönetimi şablonu cihazlarının ekran görüntüsü.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devices.png)

1. **Bağlı çöp kutusu** cihazını seçin.  

     ![Bağlı çöp yönetimi şablonu cihaz özelliklerinin ekran görüntüsü.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-devices-bin1.png)

1. **Bulut özellikleri** sekmesine gidin. **Bin tam uyarı eşiğinin** değerini **95** **100** olarak güncelleştirin. 

**Cihaz özelliklerini** ve **cihaz panosu** sekmelerini gezin. 

> [!NOTE]
> Tüm sekmeler cihaz şablonu görünümlerinden yapılandırıldı.

### <a name="add-new-devices"></a>Yeni cihaz ekle

**Cihazlar** sekmesinde **+ Yeni** ' ye tıklayarak yeni cihaz ekleyebilirsiniz. 

## <a name="explore-and-configure-rules"></a>Kuralları keşfet ve yapılandırma

Azure IoT Central, cihaz telemetrisini otomatik olarak izlemek ve bir veya daha fazla koşul karşılandığında eylemleri tetiklemek için kurallar oluşturabilirsiniz. Eylemler e-posta bildirimleri göndermeyi, Power otomatikleştirebileceğiniz bir eylemi tetiklemeyi veya diğer hizmetlere veri göndermek için bir Web kancası eylemi başlatmayı içerebilir.

Bağlı çöp yönetimi uygulamasının dört örnek kuralı vardır.

### <a name="view-rules"></a>Kuralları görüntüle
1. Azure IoT Central sol bölmesinden **kurallar**' ı seçin.

   ![Bağlı çöp yönetimi şablonu kurallarının ekran görüntüsü.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-rules.png)

1. **Bin tam uyarısı**' nı seçin.

     ![Bin tam uyarı ekran görüntüsü.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-binfullalert.png)

 1. **Bin tam uyarısı** aşağıdaki koşulu denetler: **Fill level, bin tam uyarı eşiğine eşit veya daha büyüktür**.

    **Bin tam uyarı eşiği** , bağlı çöp kutusu cihaz şablonunda tanımlanan bir bulut özelliğidir. 

Şimdi bir e-posta eylemi oluşturalım.

### <a name="create-an-email-action"></a>E-posta eylemi oluşturma

Kuralın **Eylemler** listesinde, bir e-posta eylemi yapılandırabilirsiniz:
1. **+ E-posta** seçeneğini belirleyin. 
1. **Görünen ad** Için **yüksek pH uyarısı** girin.
1. **İçin**, Azure IoT Central hesabınızla ilişkili e-posta adresini girin. 
1. İsteğe bağlı olarak, e-posta metnine dahil etmek için bir Note girin.
1. **Bitti**  >  **kaydetmeyi** seçin. 

Artık yapılandırılmış koşul karşılandığında bir e-posta alacaksınız.

>[!NOTE]
>Uygulama, her koşul karşılandığında e-posta gönderir. Otomatik kuraldan e-posta almayı durdurma kuralını devre dışı bırakın. 
  
Yeni bir kural oluşturmak için **kuralların** sol bölmesinden **+ Yeni**' yi seçin.

## <a name="configure-jobs"></a>İşleri yapılandırma

Azure IoT Central, işler cihaz veya bulut özellikleri güncelleştirmelerini birden çok cihazda tetiklemeye olanak tanır. Ayrıca, birden çok cihazda cihaz komutlarını tetiklemek için işleri de kullanabilirsiniz. Azure IoT Central, iş akışını sizin için otomatikleştirir. 

1. Azure IoT Central sol bölmesinden **işler**' i seçin. 
1. **+ Yeni**' yi seçin ve bir veya daha fazla iş yapılandırın. 

## <a name="customize-your-application"></a>Uygulamanızı özelleştirme 

Bir Oluşturucu olarak, uygulamanızdaki Kullanıcı deneyimini özelleştirmek için çeşitli ayarları değiştirebilirsiniz.

### <a name="change-the-application-theme"></a>Uygulama temasını değiştirme

Aşağıdaki adımları uygulayın:
1. **Yönetim** bölümüne giderek  >  **uygulamanızı özelleştirin**.
1. **Uygulama logosu** için karşıya yüklenecek bir görüntü seçmek için **Değiştir** ' i seçin.
1. **Tarayıcı simgesine** yüklenecek bir görüntü seçmek için **Değiştir** ' i seçin (tarayıcı sekmelerinde görünecek bir görüntü).
1. Varsayılan tarayıcı renklerini, HTML onaltılık renk kodları ekleyerek de değiştirebilirsiniz. Bu amaçla **Header** ve **aksan** alanlarını kullanın.

   ![Bağlı WAST yönetim şablonunun ekran görüntüsü uygulamanızı özelleştirin.](./media/tutorial-connectedwastemanagement/connectedwastemanagement-customize-your-application.png)

1. Ayrıca, uygulama görüntülerini da değiştirebilirsiniz. **Yönetim**  >  **uygulaması ayarlarını** seçin görüntü ' i seçerek  >   uygulama görüntüsü olarak karşıya yüklenecek bir görüntü seçin.
1. Son olarak, uygulamanın künyesi üzerinde **Ayarlar** ' ı seçerek temayı da değiştirebilirsiniz.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu uygulamayı kullanmaya devam etmeyecekecekseniz, aşağıdaki adımlarla uygulamanızı silin:

1. Azure IoT Central uygulamanızın sol bölmesinde **Yönetim**' i seçin.
1. **Uygulama ayarlarını**  >  **Sil**' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Bağlı çöp yönetimi kavramları](./concepts-connectedwastemanagement-architecture.md)
