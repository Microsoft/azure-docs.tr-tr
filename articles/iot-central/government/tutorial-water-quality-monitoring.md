---
title: 'Öğretici: Azure IoT Central su kalitesinde izleme uygulaması oluşturma'
description: 'Öğretici: Azure IoT Central uygulama şablonlarını kullanarak su kalite izleme uygulaması oluşturmayı öğrenin.'
author: miriambrus
ms.author: miriamb
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 7014882e7da9eb7ab7df65cd20403982a46018a3
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74112612"
---
# <a name="tutorial-create-a-water-quality-monitoring-application-in-azure-iot-central"></a>Öğretici: Azure IoT Central su kalitesinde izleme uygulaması oluşturma

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Bu öğretici, Azure IoT Central bir su kalite izleme uygulamasının oluşturulmasında size rehberlik eder. Uygulamayı Azure IoT Central **su kalitesi izleme** uygulaması şablonundan oluşturursunuz.

Bu öğreticide şunların nasıl yapıldığını öğrenirsiniz:

> [!div class="checklist"]
> * Su kalitesinde izleme uygulaması oluşturmak için **su kalitesi izleme** şablonunu kullanın.
> * Operatör panosunu keşfet ve özelleştirme.
> * Su kalitesi izleme cihaz şablonunu keşfet.
> * Sanal cihazları keşfet.
> * Kuralları keşfet ve yapılandırın.
> * İşleri yapılandırma.
> * Beyaz etiketleme kullanarak uygulama markalamasını özelleştirin.

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlayabilmeniz için bir Azure aboneliğinizin olması önerilir. Azure aboneliğiniz yoksa, [Azure kaydolma sayfasında](https://aka.ms/createazuresubscription)bir tane oluşturabilirsiniz.

## <a name="create-a-water-quality-monitoring-application-in-azure-iot-central"></a>Azure IoT Central su kalitesinde izleme uygulaması oluşturma

Bu bölümde, su kalite izleme uygulaması oluşturmak için Azure IoT Central **su kalitesi izleme** şablonunu kullanırsınız.

1. [Azure IoT Central giriş sayfasına](https://aka.ms/iotcentral)gidin.

    Azure aboneliğiniz varsa, ona erişmek için kullandığınız kimlik bilgileriyle oturum açın. Aksi takdirde, bir Microsoft hesabı oturum açın:

    ![Kuruluş hesabınızda oturum açın](./media/tutorial-waterqualitymonitoring/sign-in.png)

1. Azure IoT Central sol üst bölmesinde **Oluştur** ' u seçin ve **kamu** sekmesini seçin. Kamu bölmesinde çeşitli kamu uygulaması şablonları gösterilmektedir.

    ![Kamu uygulaması şablonları](./media/tutorial-waterqualitymonitoring/iotcentral-government-tab-overview1.png)

1. **Su kalitesi izleme** uygulaması şablonunu seçin. Bu uygulama şablonu, su kalitesinde bir cihaz şablonu, sanal cihazlar, bir operatör panosu ve önceden yapılandırılmış izleme kuralları içerir.

1. **Uygulama oluştur**' u seçin. **Yeni uygulama** bölmesi açılır ve aşağıdaki öğeleri gösterir:

    * **Uygulama adı**: varsayılan olarak, uygulama adı **su kalite izleme** ' dir ve sonrasında Azure IoT Central 'nın oluşturduğu benzersiz bir kimlik dizesidir. İsterseniz, bir görünen ad girebilir veya uygulama adını daha sonra değiştirebilirsiniz.
    * **URL**: istediğiniz URL 'yi gırebılır veya URL değerini daha sonra değiştirebilirsiniz.
    * Azure aboneliğiniz varsa **Dizin**, **Azure aboneliği**ve **bölge**değerlerini girin. Aboneliğiniz yoksa **7 günlük ücretsiz denemeyi** açabilir ve gerekli iletişim bilgilerini tamamlayabilirsiniz.

    Dizinler ve abonelikler hakkında daha fazla bilgi için bkz. [uygulama oluşturma](../core/quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) hızlı başlangıcı.

1. Sayfanın sol alt kısmında **Oluştur** düğmesini seçin.

    ![Azure IoT Central yeni uygulama sayfası](./media/tutorial-waterqualitymonitoring/new-application-waterqualitymonitoring1.png)

Artık Azure IoT Central **su kalitesi izleme** şablonunu kullanarak bir su kalite izleme uygulaması oluşturdunuz.

Yeni uygulamanız önceden yapılandırılmış bu bileşenlerle birlikte gelir:

* İşleç panoları
* Su kalitesi izleme cihaz şablonları
* Sanal su kalitesi izleme cihazları
* Kurallar ve işler
* Beyaz etiketleme kullanan marka

Uygulamanızı dilediğiniz zaman değiştirebilirsiniz.

Ardından, uygulamayı araştırıp bazı özelleştirmeler yapın.

## <a name="explore-and-customize-the-operator-dashboard"></a>Operatör panosunu keşfet ve özelleştirme

Uygulamayı oluşturduktan sonra **geniş dünya su kalitesi panosu** bölmesi açılır.

   ![Su kalitesi izleme panosu](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-dashboard1.png)

Bir Oluşturucu olarak, panolar tarafından kullanılmak üzere panoda görünümler oluşturabilir ve özelleştirebilirsiniz. Ancak özelleştirmeyi denemeden önce, önce Panoyu araştırın.

Panoda gösterilen tüm veriler, sonraki bölümde açıklanan sanal cihaz verilerini temel alır.

Pano aşağıdaki kutucuk türlerini içerir:

* **Geniş dünya su yardımcı programı resim kutucuğu**: panonun sol üst köşesindeki Ilk kutucuk Wide World adlı kurgusal yardımcı programı gösteren bir görüntüdür. Kutucuğu kendi görüntünüzü kullanacak şekilde özelleştirebilir veya kutucuğu kaldırabilirsiniz.

* **Ortalama pH KPI kutucukları**: **son 30 dakika içinde ortalama pH** gibi KPI kutucukları Pano bölmesinin en üstünde bulunur. KPI kutucukları özelleştirebilir ve her biri farklı bir tür ve zaman aralığına ayarlanabilir.

* **Su izleme alanı Haritası**: Azure IoT Central, uygulamanızın cihaz konumunu gösterecek şekilde doğrudan ayarlayabileceği Azure Maps kullanır. Ayrıca, uygulamanızdaki konum bilgilerini cihazınıza de eşleyebilir ve ardından Azure haritalar 'ı kullanarak bir haritadaki bilgileri gösterebilirsiniz. Haritanın üzerine gelin ve denetimleri deneyin.

* **Ortalama pH dağıtımı ısı haritası grafik**: cihaz telemetrisini uygulamanız için en uygun şekilde göstermek üzere farklı görselleştirme grafikleri seçebilirsiniz.

* **Kritik kalite göstergeleri çizgi grafik**: bir zaman aralığında çizgi grafik olarak çizilen cihaz telemetrisini görselleştirebilirsiniz.  

* **Kimyasal aracılar çubuk grafiğinin yoğunluğu**: cihaz telemetrisini bir çubuk grafiğinde görselleştirebilirsiniz.

* **Eylem düğmesi**: Pano, bir işlecin doğrudan izleme panosundan başlatabileceği eylemler için bir kutucuk içerir. Bir cihazın özelliklerini sıfırlamak, bu eylemlere bir örnektir.

* **Özellik listesi kutucukları**: panoda eşik bilgilerini, cihaz sistem durumu bilgilerini ve bakım bilgilerini temsil eden birden çok özellik kutucuğu vardır.

### <a name="customize-the-dashboard"></a>Panoyu özelleştirme

Bir Oluşturucu olarak, panodaki görünümleri işleçler tarafından kullanılmak üzere özelleştirebilirsiniz.

1. **Geniş dünya su kalitesi Pano** bölmesini özelleştirmek için **Düzenle** ' yi seçin. **Düzenle** menüsünde komutlar ' a tıklayarak panoyu özelleştirebilirsiniz. Pano düzenleme modundayken, yeni kutucuklar ekleyebilir veya mevcut dosyaları yapılandırabilirsiniz.

    ![Panonuzu düzenleme](./media/tutorial-waterqualitymonitoring/edit-dashboard.png)

1. Yapılandırabileceğiniz yeni bir pano oluşturmak için **+ Yeni** ' yi seçin. Birden çok panonuz olabilir ve Pano menüsünden bunlar arasında gezinebilirsiniz.

## <a name="explore-a-water-quality-monitoring-device-template"></a>Su kalitesi izleme cihaz şablonunu keşfet

Azure IoT Central bir cihaz şablonu, bir cihazın yeteneklerini tanımlar. Kullanılabilir özellikler telemetri, Özellikler ve komutlardır. Bir Oluşturucu olarak, Azure IoT Central bağlı cihazların yeteneklerini temsil eden cihaz şablonları tanımlayabilirsiniz. Ayrıca, cihaz şablonunuzu ve uygulamanızı test etmek için sanal cihazlar da oluşturabilirsiniz.

Oluşturduğunuz su kalitesinde izleme uygulaması, su kalite izleme cihaz şablonuyla birlikte gelir.

Cihaz şablonunu görüntülemek için:

1. Azure IoT Central 'de uygulamanızın en sol bölmesinde **cihaz şablonları** ' nı seçin.
1. Cihaz şablonları listesinden **su kalitesi İzleyicisi**' ni seçin. Bu cihaz şablonu açılır.

    ![Cihaz şablonu](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-devicetemplate.png)

### <a name="customize-the-device-template"></a>Cihaz şablonunu özelleştirme

Aşağıdaki cihaz şablonu ayarlarını özelleştirme alıştırması:

1. Cihaz şablonu menüsünde **Özelleştir**' i seçin.
1. **Sıcaklık** telemetri türüne gidin.
1. **Görünen ad** değerini **bildirilen sıcaklık**olarak değiştirin.
1. Ölçüm birimini değiştirin veya **En düşük değer** ve **en büyük değeri**ayarlayın.
1. **Kaydet**’i seçin.

#### <a name="add-a-cloud-property"></a>Bulut özelliği Ekle

1. Cihaz şablonu menüsünde, **bulut özelliği**' ni seçin.
1. Yeni bir bulut özelliği eklemek için **+ bulut özelliği Ekle**' yi seçin. Azure IoT Central 'de, bir cihazla ilgili olan ancak cihaz tarafından gönderilmesi beklenmez bir özellik ekleyebilirsiniz. Bu tür bir özelliğin bir örneği, yükleme alanına, varlık bilgilerine veya bakım bilgilerine özgü bir uyarı eşiğinden biridir.
1. **Kaydet**’i seçin.

### <a name="explore-views"></a>Görünümleri keşfet

Su kalitesi izleme cihaz şablonu önceden tanımlanmış görünümlerle gelir. Görünümler, işleçlerin cihaz verilerini nasıl gördiğine ve bulut özelliklerinin nasıl ayarlanacağını tanımlar. Görünümleri keşfedebilir ve değişiklikleri yapın.

  ![Cihaz şablonu görünümleri](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-devicetemplate-views.png)

### <a name="publish-the-device-template"></a>Cihaz şablonunu yayımlama

Herhangi bir değişiklik yaparsanız, cihaz şablonunu yayımlamak için **Yayımla** ' yı seçtiğinizden emin olun.

### <a name="create-a-new-device-template"></a>Yeni cihaz şablonu oluşturma

1. Yeni bir cihaz şablonu oluşturmak ve oluşturma işlemini izlemek için **+ Yeni** ' yi seçin.
1. Özel bir cihaz şablonu oluşturun veya Azure IoT cihaz kataloğundan bir cihaz şablonu seçin.

## <a name="explore-simulated-devices"></a>Sanal cihazları keşfet

Uygulama şablonundan oluşturduğunuz su kalitesinde izleme uygulamasının iki sanal cihazı vardır. Bu cihazlar su kalitesi izleme cihaz şablonuyla eşlenir.

### <a name="view-the-devices"></a>Cihazları görüntüleme

1. Uygulamanızın en sol bölmesinde bulunan **cihazlar** ' ı seçin.

   ![Cihazlar](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-devices.png)

1. Tek bir sanal cihaz seçin.

    ![Cihaz 1 ' i seçin](./media/tutorial-waterqualitymonitoring/waterqualitymonitor-device1.png)

1. **Bulut özellikleri** sekmesinde, **acidity (pH) eşik** değerini **8** ' den **9**' a değiştirin.
1. **Cihaz özellikleri** sekmesini ve **cihaz panosu** sekmesini bulun.

> [!NOTE]
> Tüm sekmeler **cihaz şablonu görünümlerinden**yapılandırıldı.

### <a name="add-new-devices"></a>Yeni cihaz ekle

Yeni bir cihaz eklemek için **cihazlar** sekmesinde **+ Yeni** ' yi seçin.

## <a name="explore-and-configure-rules"></a>Kuralları keşfet ve yapılandırma

Azure IoT Central, cihaz telemetrisini otomatik olarak izleyen kurallar oluşturabilirsiniz. Bu kurallar, koşullarından herhangi biri karşılandığında bir eylemi tetikler. Olası bir işlem e-posta bildirimleri göndermektir. Diğer olasılıklar, diğer hizmetlere veri göndermek için bir Microsoft Flow eylemi veya Web kancası eylemi içerir.

Oluşturduğunuz su kalitesinde izleme uygulamasının önceden yapılandırılmış iki kuralı vardır.

### <a name="view-rules"></a>Kuralları görüntüle

1. Uygulamanızın en sol bölmesinde bulunan **kurallar** ' ı seçin.

   ![Kurallar](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-rules.png)

1. Uygulamanın önceden yapılandırılmış kurallarından biri olan **yüksek pH uyarısı**' nı seçin.

   ![Yüksek pH uyarı kuralı](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-highphalert.png)

   **Yüksek pH uyarı** kuralı, acidity (pH) öğesinin 8 ' den büyük olduğunu denetlemek için yapılandırılır.

Sonra, kurala bir e-posta eylemi ekleyin:

1. **+ E-posta**seçeneğini belirleyin.
1. **Görünen ad** kutusunda, **yüksek pH uyarısı**' nı girin.
1. **To** kutusuna Azure IoT Central hesabınızla ilişkili e-posta adresini girin.
1. İsteğe bağlı olarak, e-posta metnine dahil etmek için bir Note girin.
1. Eylemi gerçekleştirmek için **bitti** ' yi seçin.
1. Yeni kuralı kaydetmek ve etkinleştirmek için **Kaydet** ' i seçin.

Birkaç dakika içinde, yapılandırılan koşul karşılandığında e-posta almalısınız.

> [!NOTE]
> Uygulama, her koşul karşılandığında e-posta gönderir. Bu kuraldan otomatik e-posta almayı durdurmak için kural için **devre dışı bırak** ' ı seçin.
  
Yeni bir kural oluşturmak için uygulamanızın en sol bölmesinde bulunan **kurallar** ' ı seçin ve sonra **+ Yeni**' yi seçin.

## <a name="configure-jobs"></a>İşleri yapılandırma

Azure IoT Central işleri sayesinde, güncelleştirmeleri cihaz veya bulut özelliklerine birden çok cihazda tetikleyebilirsiniz. Ayrıca, birden çok cihazda cihaz komutlarını tetiklemek için işleri de kullanabilirsiniz. Azure IoT Central, iş akışını sizin için otomatikleştirir.

1. Uygulamanızın en sol bölmesinde bulunan **işler** ' i seçin.
1. **+ Yeni** ' yi seçin ve bir veya daha fazla iş yapılandırın.

## <a name="customize-your-application"></a>Uygulamanızı özelleştirme

Bir Oluşturucu olarak, uygulamanızdaki Kullanıcı deneyimini özelleştirmek için çeşitli ayarları değiştirebilirsiniz.

1. **Uygulamanızı özelleştirmek** > **Yönetim** ' i seçin.
1. **Uygulama logosu**altında, logo olarak karşıya yüklenecek görüntüyü seçmek için **Değiştir** ' i seçin.
1. Tarayıcı **simgesinde**, tarayıcı sekmelerinde görüntülenen görüntüyü seçmek için **Değiştir** ' i seçin.
1. **Tarayıcı renkleri**altında, varsayılan değerleri HTML onaltılık renk kodlarıyla değiştirebilirsiniz.
1. **Temanın**değerini değiştirmek için **Ayarlar** ' ı seçin.

   ![Uygulamanızı özelleştirme](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-customize-your-application1.png)

### <a name="update-the-application-image"></a>Uygulama görüntüsünü güncelleştirme

1. **Yönetim** > **uygulama ayarları**' nı seçin.

1. Uygulama görüntüsü olarak karşıya yüklenecek bir görüntü seçmek için **Görüntü Seç** düğmesini kullanın.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Uygulamanızı kullanmaya devam etmeyecekecekseniz, uygulamayı aşağıdaki adımlarla silin:

1. Uygulamanızın en sol bölmesinde bulunan **Yönetim** sekmesini açın.
1. **Uygulama ayarları** ' nı seçin ve **Sil** düğmesini seçin.

    ![Uygulamanızı silme](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-application-settings-delete-app1.png)

## <a name="next-steps"></a>Sonraki adımlar

* [Su kalitesi izleme kavramları](./concepts-waterqualitymonitoring-architecture.md)hakkında daha fazla bilgi edinin.
