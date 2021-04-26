---
title: 'Öğretici: ortam oluşturma-Azure Time Series Insights | Microsoft Docs'
description: Sanal cihazlardan alınan verilerle doldurulan bir Azure Time Series Insights ortamı oluşturmayı öğrenin.
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.service: time-series-insights
ms.topic: tutorial
ms.date: 10/01/2020
ms.custom: seodec18
ms.openlocfilehash: ea57a623546df99408d488da4522411f55ab0dd9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101094361"
---
# <a name="tutorial-create-an-azure-time-series-insights-gen1-environment"></a>Öğretici: Azure Time Series Insights Gen1 ortamı oluşturma

> [!CAUTION]
> Bu bir Gen1 makaledir.

Bu öğretici, sanal cihazlardan alınan verilerle doldurulan bir Azure Time Series Insights ortamı oluşturma sürecinde size rehberlik eder. Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
>
> * Azure Time Series Insights ortamı oluşturun.
> * IoT Hub içeren bir cihaz benzetim çözümü oluşturun.
> * Azure Time Series Insights ortamını IoT Hub 'ına bağlayın.
> * Azure Time Series Insights ortamına veri akışı sağlamak için bir cihaz benzetimi çalıştırın.
> * Sanal telemetri verilerini doğrulayın.

> [!IMPORTANT]
> Henüz yoksa ücretsiz bir [Azure aboneliğine](https://azure.microsoft.com/free/) kaydolun.

## <a name="prerequisites"></a>Önkoşullar

* Azure oturum açma hesabınız Ayrıca aboneliğin **sahip** rolünün bir üyesi olmalıdır. Daha fazla bilgi için [Azure Portal kullanarak Azure rolleri atama](../role-based-access-control/role-assignments-portal.md)makalesini okuyun.

## <a name="review-video"></a>Videoyu gözden geçirin

Azure IoT Çözüm Hızlandırıcısı kullanarak veri oluşturma ve Azure Time Series Insights kullanmaya başlama hakkında bilgi edinin.

> [!VIDEO https://www.youtube.com/embed/6ehNf6AJkFo]

## <a name="overview"></a>Genel Bakış

Azure Time Series Insights ortamı, cihaz verilerinin toplandığı ve depolandığı yerdir. Depolanan [Azure Time Series Insights Gezgini](time-series-quickstart.md) ve [Azure Time Series Insights sorgu API 'si](/rest/api/time-series-insights/gen1-query-api) , verileri sorgulamak ve analiz etmek için kullanılabilir.

Azure IoT Hub, Azure bulutuna güvenli bir şekilde bağlanmak ve veri aktarmak için öğreticide tüm cihazlar (benzetimli veya fiziksel) tarafından kullanılan olay kaynağıdır.

Bu öğretici Ayrıca IoT Hub için örnek telemetri verileri oluşturmak ve akışa almak için bir [IoT Çözüm Hızlandırıcısı](https://www.azureiotsolutions.com) kullanır.

>[!TIP]
> [IoT Çözüm Hızlandırıcıları](https://www.azureiotsolutions.com) , özel IoT çözümlerinin geliştirilmesini hızlandırmak için kullanabileceğiniz kurumsal sınıf önceden yapılandırılmış çözümler sunar.

## <a name="create-a-device-simulation"></a>Cihaz benzetimi oluşturma

İlk olarak, Azure Time Series Insights ortamınızı doldurmak için test verileri oluşturan cihaz benzetim çözümünü oluşturun.

1. Ayrı bir pencere veya sekmede [azureiotsolutions.com](https://www.azureiotsolutions.com)adresine gidin. Aynı Azure aboneliği hesabını kullanarak oturum açın ve **cihaz benzetimi** hızlandırıcıyı seçin.

   [![Cihaz Benzetimi hızlandırıcısını çalıştırma](media/tutorial-create-populate-tsi-environment/iot-solution-accelerators-landing-page.png)](media/tutorial-create-populate-tsi-environment/iot-solution-accelerators-landing-page.png#lightbox)

1. **Şimdi dene**' yi seçin. Ardından, **cihaz simülasyonu çözümü oluştur** sayfasında gerekli parametreleri girin.

   Parametre|Açıklama
   ---|---
   **Dağıtım adı** | Bu benzersiz değer, yeni bir kaynak grubu oluşturmak için kullanılır. Listelenen Azure kaynakları oluşturulur ve kaynak grubuna atanır.
   **Azure aboneliği** | Önceki bölümde Azure Time Series Insights ortamınızı oluşturmak için kullanılan aboneliği belirtin.
   **Dağıtım seçenekleri** | Bu öğreticiye özgü yeni bir IoT Hub 'ı oluşturmak için **yeni IoT Hub sağla** ' yı seçin.
   **Azure konumu** | Önceki bölümde Azure Time Series Insights ortamınızı oluşturmak için kullanılan bölgeyi belirtin.

   İşiniz bittiğinde, çözümün Azure kaynaklarını sağlamak için **Oluştur** ' u seçin. Bu işlemin tamamlanması 20 dakikaya kadar sürebilir.

   [![Cihaz benzetimi çözümünü sağlama](media/tutorial-create-populate-tsi-environment/iot-solution-accelerators-configuration.png)](media/tutorial-create-populate-tsi-environment/iot-solution-accelerators-configuration.png#lightbox)

1. Sağlama tamamlandıktan sonra, dağıtım durumunun **sağlamadan** **başlamaya** taşındığını bildiren iki güncelleştirme görüntülenir.

   >[!IMPORTANT]
   > Çözüm hızlandırıcıyı henüz girmeyin! Daha sonra geri döneceksiniz, bu Web sayfasını açık tutun.

   [![Cihaz benzetimi çözümünü sağlama tamamlandı](media/tutorial-create-populate-tsi-environment/iot-solution-accelerator-ready.png)](media/tutorial-create-populate-tsi-environment/iot-solution-accelerator-ready.png#lightbox)

1. Şimdi, Azure portal yeni oluşturulan kaynakları inceleyin. **Kaynak grupları** sayfasında, son adımda sunulan **çözüm adı** kullanılarak yeni bir kaynak grubunun oluşturulduğunu fark edersiniz. Cihaz benzetimi için oluşturulan kaynakları unutmayın.

   [![Cihaz benzetimi kaynakları](media/tutorial-create-populate-tsi-environment/tsi-device-sim-solution-resources.png)](media/tutorial-create-populate-tsi-environment/tsi-device-sim-solution-resources.png#lightbox)

## <a name="create-an-environment"></a>Ortam oluşturma

İkinci olarak, Azure aboneliğinizde bir Azure Time Series Insights ortamı oluşturun.

1. Azure abonelik hesabınızı kullanarak [Azure Portal](https://portal.azure.com) oturum açın.
1. Üstteki menüden **+ Kaynak oluştur**'u seçin.
1. **Nesnelerin interneti** kategorisini seçin ve ardından **Time Series Insights**' ı seçin.

   [![Azure Time Series Insights ortamı kaynağını seçin](media/tutorial-create-populate-tsi-environment/tsi-create-new-environment.png)](media/tutorial-create-populate-tsi-environment/tsi-create-new-environment.png#lightbox)

1. **Time Series Insights ortamı** sayfasında, gerekli parametreleri girin.

   Parametre|Açıklama
   ---|---
   **Ortam adı** | Azure Time Series Insights ortamı için benzersiz bir ad seçin. Adlar Azure Time Series Insights Gezgini ve [sorgu API 'leri](/rest/api/time-series-insights/gen1-query)tarafından kullanılır.
   **Abonelik** | Abonelikler, Azure kaynaklarına yönelik kapsayıcılardır. Azure Time Series Insights ortamını oluşturmak için bir abonelik seçin.
   **Kaynak grubu** | Kaynak grubu, Azure kaynaklarına yönelik bir kapsayıcıdır. Mevcut bir kaynak grubunu seçin veya Azure Time Series Insights ortam kaynağı için yeni bir tane oluşturun.
   **Konum** | Azure Time Series Insights ortamınız için bir veri merkezi bölgesi seçin. Ek gecikmeyi önlemek için, diğer IoT kaynaklarıyla aynı bölgede Azure Time Series Insights ortamı oluşturun.
   **Katman** | Gerekli aktarım hızını seçin. **S1**' i seçin.
   **Kapasite** | Kapasite, seçilen SKU ile ilişkili giriş hızına ve depolama kapasitesine uygulanan çarpandır. Oluşturulduktan sonra kapasiteyi değiştirebilirsiniz. **1** kapasitesini seçin.

   İşiniz bittiğinde sonraki adıma geçmek için **Sonraki: olay kaynağı** ' nı seçin.

   [![Azure Time Series Insights ortam kaynağı oluşturma](media/tutorial-create-populate-tsi-environment/tsi-create-resource-tsi-params.png)](media/tutorial-create-populate-tsi-environment/tsi-create-resource-tsi-params.png#lightbox)

1. Şimdi, Azure Time Series Insights ortamını Çözüm Hızlandırıcısı tarafından oluşturulan IoT Hub 'ına bağlayın. **Hub seçin seçeneğini** belirleyin `Select existing` . Daha sonra, **IoT Hub adı** ayarlanırken Çözüm Hızlandırıcısı tarafından oluşturulan IoT Hub 'ını seçin.

   [![Azure Time Series Insights ortamını oluşturulan IoT Hub 'ına bağlama](media/tutorial-create-populate-tsi-environment/tsi-create-resource-iot-hub.png)](media/tutorial-create-populate-tsi-environment/tsi-create-resource-iot-hub.png#lightbox)

   Son olarak, **gözden geçir + oluştur**' u seçin.

1. Dağıtım tamamlamayı izlemek için **Bildirimler** panelini kontrol edin.

   [![Azure Time Series Insights ortamı dağıtımı başarılı oldu](media/tutorial-create-populate-tsi-environment/create-resource-tsi-deployment-succeeded.png)](media/tutorial-create-populate-tsi-environment/create-resource-tsi-deployment-succeeded.png#lightbox)

## <a name="run-device-simulation"></a>Cihaz benzetimi Çalıştır

Dağıtım ve ilk yapılandırmanın tamamlandığına göre, Azure Time Series Insights ortamını [Hızlandırıcı tarafından oluşturulan sanal cihazlardan](#create-a-device-simulation)örnek verilerle doldurun.

IoT Hub ile birlikte, sanal cihaz telemetrisini oluşturmak ve iletmek için bir Azure App Service Web uygulaması oluşturulmuştur.

1. [Çözüm hızlandırıcıları panonuza](https://www.azureiotsolutions.com/Accelerators#dashboard) geri dönün. Gerekirse, bu öğreticide kullandığınız Azure hesabını kullanarak yeniden oturum açın. "Cihaz çözümünüzü" seçin ve ardından dağıtılan çözümünüzü başlatmak için **çözüm hızlandırıcısına gidin** .

   [![Çözüm hızlandırıcıları panosu](media/tutorial-create-populate-tsi-environment/iot-solution-accelerator-ready.png)](media/tutorial-create-populate-tsi-environment/iot-solution-accelerator-ready.png#lightbox)

1. Cihaz benzetimi Web uygulaması, Web uygulamasına **oturum açma hakkı vermenizi ve profilinizi okumanızı** isteyip istemediğinizi isteyerek başlar. Bu izin, uygulamanın, uygulamanın çalışmasını desteklemek için gereken kullanıcı profili bilgilerini almasına izin verir.

   [![Cihaz benzetimi web uygulaması onayı](media/tutorial-create-populate-tsi-environment/sawa-signin-consent.png)](media/tutorial-create-populate-tsi-environment/sawa-signin-consent.png#lightbox)

1. **+ Yeni benzetim**' i seçin. **Simülasyon kurulum** sayfası yüklendikten sonra gerekli parametreleri girin.

   Parametre|Açıklama
   ---|---
   **Hedef IoT Hub'ı** | **Önceden sağlanmış IoT Hub kullan**' ı seçin.
   **Cihaz modeli** | **Chiller**'i seçin.
   **Cihaz sayısı**  | `10` **Miktar** altında girin.
   **Telemetri sıklığı** | `10`Saniyeleri girin.
   **Benzetim süresi** | **Bitiş: '** i seçin ve `5` dakikaları girin.

   İşiniz bittiğinde **Benzetim Başlat**' ı seçin. Simülasyon toplam 5 dakika boyunca çalışır. Her 10 saniyede 1.000 sanal cihazdan veri üretir.

   [![Cihaz benzetimi ayarı](media/tutorial-create-populate-tsi-environment/sawa-simulation-setup.png)](media/tutorial-create-populate-tsi-environment/sawa-simulation-setup.png#lightbox)

1. Simülasyon çalışırken saniyede **toplam ileti** ve **mesaj** , yaklaşık olarak her 10 saniyede bir güncelleştirme güncelleştiğine dikkat edin. Simülasyon yaklaşık 5 dakikada bir sona erer ve sizi **simülasyon kurulumuna** döndürür.

   [![Cihaz benzetimi çalışıyor](media/tutorial-create-populate-tsi-environment/sawa-simulation-running.png)](media/tutorial-create-populate-tsi-environment/sawa-simulation-running.png#lightbox)

## <a name="verify-the-telemetry-data"></a>Telemetri verilerini doğrulama

Bu son bölümde Telemetri verilerinin Azure Time Series Insights ortamda oluşturulduğunu ve depolandığını doğrularsınız. Verileri doğrulamak için telemetri verilerini sorgulamak ve analiz etmek üzere kullanılan Azure Time Series Insights Gezginini kullanırsınız.

1. Azure Time Series Insights ortamının kaynak grubuna **genel bakış** sayfasına dönün. Azure Time Series Insights ortamını seçin.

   [![Azure Time Series Insights ortamı kaynak grubu ve ortamı](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-rg.png)](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-rg.png#lightbox)

1. Azure Time Series Insights ortamına **genel bakış** sayfasında, Azure Time Series Insights Gezginini açmak Için **Time Series Insights gezgin URL 'sini** seçin.

   [![Azure Time Series Insights Gezgini](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-explorer-url.png)](media/tutorial-create-populate-tsi-environment/ap-view-tsi-env-explorer-url.png#lightbox)

1. Azure Time Series Insights Gezgini Azure portal hesabınızı kullanarak yükler ve kimliğini doğrular. Başlangıçta, Azure Time Series Insights ortamının, sanal telemetri verileriyle birlikte doldurulduğu grafik alanı görüntülenir. Daha dar bir zaman aralığını filtrelemek için sol üst köşedeki açılan eklentiyi seçin. Cihaz benzetimi süresini kapsayacak kadar büyük bir zaman aralığı girin. Ardından, arama Büyüteç Camı ' nı seçin.

   [![Azure Time Series Insights Explorer zaman aralığı Filtresi](media/tutorial-create-populate-tsi-environment/tsie-filter-time-range.png)](media/tutorial-create-populate-tsi-environment/tsie-filter-time-range.png#lightbox)

1. Zaman aralığını daraltmak grafiğin, IoT Hub 'ına ve Azure Time Series Insights ortamına veri aktarımının farklı dünyanlarına yakınlaştırmasını sağlar. Ayrıca, sağ üst köşedeki **akış tam** metnine, bulunan toplam olay sayısını gösteren dikkat edin. Ayrıca, grafikteki çizim ayrıntı düzeyini denetlemek için **Aralık boyutu** kaydırıcısını sürükleyebilirsiniz.

   [![Azure Time Series Insights Explorer zaman aralığı filtrelenmiş görünümü](media/tutorial-create-populate-tsi-environment/tsie-view-time-range.png)](media/tutorial-create-populate-tsi-environment/tsie-view-time-range.png#lightbox)

1. Son olarak, bir aralığı filtrelemek için bir bölgeye da sol tıklayabilirsiniz. Ardından sağ tıklayıp tablo **olayları** görünümünde olay ayrıntılarını göstermek Için **olayları keşfet** ' i kullanın.

   [![Azure Time Series Insights Explorer zaman aralığı filtrelenmiş görünümü ve olayları](media/tutorial-create-populate-tsi-environment/tsie-view-time-range-events.png)](media/tutorial-create-populate-tsi-environment/tsie-view-time-range-events.png#lightbox)

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu öğreticide, Azure Time Series Insights ortamı ve cihaz benzetimi çözümünü desteklemek için birkaç çalışan Azure hizmeti oluşturulur. Bunları kaldırmak için Azure portal geri gidin.

Azure portal sol taraftaki menüden:

1. **Kaynak grupları** simgesini seçin. Ardından Azure Time Series Insights ortamı için oluşturduğunuz kaynak grubunu seçin. Sayfanın üst kısmında, **kaynak grubunu sil**' i seçin, kaynak grubunun adını girin ve **Sil**' i seçin.

1. **Kaynak grupları** simgesini seçin. Ardından cihaz benzetimi Çözüm Hızlandırıcısı tarafından oluşturulan kaynak grubunu seçin. Sayfanın üst kısmında, **kaynak grubunu sil**' i seçin, kaynak grubunun adını girin ve **Sil**' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
>
> * Azure Time Series Insights ortamı oluşturun.
> * IoT Hub içeren bir cihaz benzetim çözümü oluşturun.
> * Azure Time Series Insights ortamını IoT Hub 'ına bağlayın.
> * Azure Time Series Insights ortamına veri akışı sağlamak için bir cihaz benzetimi çalıştırın.
> * Sanal telemetri verilerini doğrulayın.

Kendi Azure Time Series Insights ortamınızı oluşturmayı öğrenmiş olduğunuza göre, bir Azure Time Series Insights ortamından veri tüketen bir Web uygulaması oluşturmayı öğrenin:

> [!div class="nextstepaction"]
> [Barındırılan istemci SDK görselleştirme örneklerini oku](https://tsiclientsample.azurewebsites.net/)