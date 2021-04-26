---
title: Öğretici-Azure IoT Central sürekli bir hasta izleme uygulaması oluşturma | Microsoft Docs
description: Bu öğreticide, Azure IoT Central uygulama şablonlarını kullanarak sürekli bir hasta izleme uygulaması oluşturmayı öğreneceksiniz.
author: philmea
ms.author: philmea
ms.date: 09/24/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: 56ff43980aafc75d5936b86c6ba2cd400311c5fa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101719109"
---
# <a name="tutorial-deploy-and-walkthrough-a-continuous-patient-monitoring-app-template"></a>Öğretici: sürekli hasta izleme uygulama şablonunu dağıtma ve gözden geçirme

Bu öğreticide, bir çözüm Oluşturucu olarak IoT Central bir sürekli hasta izleme uygulama şablonu dağıtarak nasıl başlacağınız gösterilmektedir. Şablonu dağıtmayı ve kullanmayı öğrenirsiniz.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Uygulama şablonu oluşturma
> * Uygulama şablonunu gözden geçir

## <a name="prerequisites"></a>Önkoşullar

Bir Azure aboneliği önerilir. Alternatif olarak, 7 günlük ücretsiz deneme sürümünü kullanabilirsiniz. Azure aboneliğiniz yoksa, [Azure kaydolma sayfasında](https://aka.ms/createazuresubscription)bir tane oluşturabilirsiniz.

## <a name="create-an-application-template"></a>Uygulama şablonu oluşturma

[Azure IoT Central uygulama Yöneticisi Web sitesine](https://apps.azureiotcentral.com/)gidin. Sol taraftaki Gezinti çubuğundan **Oluştur** ' u seçin ve ardından **sağlık** sekmesi ' ni seçin.

:::image type="content" source="media/app-manager-health.png" alt-text="Healthcre uygulama şablonu":::

Uygulamanızı oluşturmaya başlamak için uygulama **Oluştur** düğmesini seçin ve ardından bir Microsoft kişisel, iş veya okul hesabıyla oturum açın. Bu işlem sizi **Yeni uygulama** sayfasına götürür.

![Uygulama sağlık hizmetleri oluşturma](media/app-manager-health-create.png)

![Uygulama sağlık bilgileri faturalandırma bilgilerini oluşturma](media/app-manager-health-create-billinginfo.png)

Uygulamanızı oluşturmak için:

1. Azure IoT Central, seçtiğiniz şablona göre otomatik olarak bir uygulama adı önerir. Bu adı kabul edebilir veya **sürekli hasta izleme** gibi kendi kolay uygulama adınızı girebilirsiniz. Azure IoT Central, uygulama adına göre sizin için benzersiz bir URL ön eki de oluşturur. İsterseniz bu URL önekini daha kolay bir şekilde değiştirebilirsiniz.

2. *Ücretsiz* fiyatlandırma planı veya *Standart* fiyatlandırma planlarından birini kullanarak uygulamayı oluşturmak isteyip istemediğinizi seçebilirsiniz. Ücretsiz planı kullanarak oluşturduğunuz uygulamalar, süreleri dolmadan yedi gün önce ücretsizdir ve beş adede kadar ücretsiz cihaza izin verir. Ücretsiz plandaki bir uygulamayı, süresi dolmadan önce istediğiniz zaman standart bir fiyatlandırma planına taşıyabilirsiniz. Ücretsiz planı seçerseniz, iletişim bilgilerinizi girmeniz ve Microsoft 'tan bilgi ve ipucu alıp almadığınızı belirlemeniz gerekir. Standart bir plan kullanarak oluşturduğunuz uygulamalar, en fazla iki ücretsiz cihazı destekler ve faturalandırma için Azure abonelik bilgilerinizi girmenizi gerektirir.

3. Uygulamanızı dağıtmak için sayfanın alt kısmındaki **Oluştur** ' u seçin.

## <a name="walk-through-the-application-template"></a>Uygulama şablonunu gözden geçir

### <a name="dashboards"></a>Panolar

Uygulama şablonu dağıttıktan sonra, önce **Lamna in hasta izleme panosuna** gideceksiniz. Lamna sağlık, iki hastaneler: Woodgrove Hospstanve Burkville hospstani içeren kurgusal bir barındırma sistemidir. Woodgrove Hospstanoperator panosunda şunları yapabilirsiniz:

* Cihazınızın **pil düzeyi** veya **bağlantı** durumu gibi cihaz telemetrisi ve özellikleri konusuna bakın.

* Akıllı vitals yaması cihazının **kat planını** ve konumunu görüntüleyin.

* Yeni bir hasta için akıllı Vintals düzeltme ekini **yeniden sağlayın** .

* Bir hastanma ekibinin hastalarını izlemek için görebileceğini bir **sağlayıcı panosu** örneğine bakın.

* Cihazın hasta veya uzak bir senaryo için kullanıldığını belirtmek üzere cihazınızın **hasta durumunu** değiştirin.

:::image type="content" source="media/lamna-in-patient.png" alt-text="Hasta durumu":::

Burkville Hospstanu işleci panosunu görmek için **uzak hasta panosuna git** ' i de seçebilirsiniz. Bu pano benzer bir eylem, telemetri ve bilgi kümesi içerir. Ayrıca, kullanımda olan birden çok cihazı görebilir ve her birinde **bellenimi güncelleştirmeyi** seçebilirsiniz.

:::image type="content" source="media/lamna-remote.png" alt-text="Uzak Operatör Panosu":::

### <a name="device-templates"></a>Cihaz şablonları

**Cihaz şablonları**' nı seçerseniz, şablonda iki cihaz türünü görürsünüz:

* **Akıllı vitals düzeltme eki**: Bu cihaz, çeşitli önemli işaretleri ölçen bir düzeltme ekini temsil eder. Bu, hoçte ve dışından hastaların izlenmesi için kullanılır. Şablonu seçerseniz, düzeltme ekinin hem pil düzeyi ve cihaz sıcaklığı gibi cihaz verilerini hem de hastam oranı ve kan basıncı gibi hasta sistem durumu verilerini göndereceğini görürsünüz.

* **Akıllı Knee ayracı**: Bu cihaz, bir Knee değiştirme Sury 'den kurtarma yaparken, hastaların kullandığı bir Knee ayracını temsil eder. Bu şablonu seçerseniz cihaz verileri, hareket aralığı ve hızlandırma gibi işlevleri görürsünüz.

:::image type="content" source="media/smart-vitals-device-template.png" alt-text="Akıllı yama şablonu":::

### <a name="device-groups"></a>Device groups

Bir cihaz kümesini mantıksal olarak gruplamak ve ardından bunlar üzerinde toplu sorgular veya işlemler çalıştırmak için cihaz gruplarını kullanın.

Cihaz grupları sekmesini seçerseniz, uygulamadaki her bir cihaz şablonu için varsayılan bir cihaz grubu görürsünüz. Ayrıca, **sağlama cihazları** ve **cihazları güncel olmayan üretici yazılımına sahip olan** iki ek örnek cihaz grubu da oluşturulmuştur. Bu örnek cihaz gruplarını, uygulamadaki bazı [işleri](#jobs) çalıştırmak için giriş olarak kullanabilirsiniz.

### <a name="rules"></a>Kurallar

**Kurallar**' ı seçerseniz, şablonda üç kuralı görürsünüz:

* **Küme ayracı yüksek**: Bu kural, akıllı Knee ayracın cihaz sıcaklığının &deg; 5 dakikalık bir pencere üzerinde 95 F 'den büyük olduğunda tetiklenir. Hasta ve bakım ekibine uyarı vermek ve cihazı uzaktan yavaşlatmak için bu kuralı kullanın.

* **Düşüş tespit edildi**: bir hasta olursa bu kural tetiklenir. Bu kuralı, işlemsel bir ekibi dağıtmak üzere bir eylem yapılandırmak için kullanın.

* **Düzeltme Eki pili düşük**: Bu kural, cihazdaki pil düzeyi %10 ' un altına gittiğinde tetiklenir. Cihazını ücretlendirmesi için bir bildirim tetiklemek üzere bu kuralı kullanın.

:::image type="content" source="media/brace-temp-rule.png" alt-text="Kurallar":::

### <a name="jobs"></a>İşler

İşler, giriş olarak [cihaz gruplarını](#device-groups) kullanarak bir dizi cihazda toplu işlemler çalıştırmanızı sağlar. Uygulama şablonunda bir işlecin çalışacağı iki örnek iş vardır:

* **Knee ayraç bellenimini Güncelleştir**: Bu iş, cihaz grubu **cihazlarındaki cihazları güncel olmayan bellenim ile** bulur ve bu cihazları en son bellenim sürümüne güncelleştirmek için bir komut çalıştırır. Bu örnek iş, cihazların bir **güncelleştirme** komutunu işleyebileceğini varsayar ve ardından bellenim dosyalarını buluttan getirir.  

* **Cihazları yeniden sağlama**: yakın zamanda hastana 'ya döndürülen bir cihaz kümesine sahipsiniz. Bu iş cihaz grubu **sağlama cihazlarda** cihazları bulur ve sonraki hastalar için bunları yeniden sağlamak üzere bir komut çalıştırır.

### <a name="devices"></a>Cihazlar

**Cihazlar** sekmesini seçin ve ardından **akıllı Knee ayracın** bir örneğini seçin. Seçtiğiniz belirli bir cihazla ilgili bilgileri araştırmak için üç görünüm vardır. Bu görünümler, cihazınız için cihaz şablonu oluşturduğunuzda oluşturulur ve yayımlanır. Bu nedenle, bu görünümler, bağlandığınız veya benzetim yaptığınız tüm cihazlarda tutarlıdır.

**Pano** görünümü cihazdan operatör odaklı telemetri ve özelliklere genel bir bakış sunar.

**Özellikler** sekmesi, bulut özelliklerini düzenlemenize ve cihaz özelliklerini okuma/yazma olanağı sağlar.

**Komutlar** sekmesi cihazdaki komutları çalıştırmanızı sağlar.

:::image type="content" source="media/knee-brace-dashboard.png" alt-text="Knee küme ayracı panosu":::

### <a name="data-export"></a>Veri dışarı aktarma

Veri dışa aktarma işlemi, cihaz verilerinizi [FHıR Için Azure API 'si](concept-continuous-patient-monitoring-architecture.md#export-to-azure-api-for-fhir)de dahil olmak üzere diğer Azure hizmetlerine sürekli olarak aktarmanıza olanak tanır.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu uygulamayı kullanmaya devam edemeyecekiniz, **yönetim > uygulama ayarlarını** ziyaret ederek uygulamayı silin ve **Sil**' e tıklayın.

:::image type="content" source="media/admin-delete.png" alt-text="Tidy kaynakları":::

## <a name="next-steps"></a>Sonraki adımlar

IoT Central uygulamanıza bağlanan bir sağlayıcı panosunun nasıl oluşturulacağını öğrenmek için sonraki makaleye ilerleyin.

> [!div class="nextstepaction"]
> [Sağlayıcı panosu oluşturma](tutorial-health-data-triage.md)