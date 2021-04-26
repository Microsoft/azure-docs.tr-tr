---
title: Azure Percept DK 'yi ayarlama
description: Geliştirme setinizi Azure 'a bağlama ve ilk AI modelinizi dağıtma
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: quickstart
ms.date: 03/17/2021
ms.custom: template-quickstart
ms.openlocfilehash: 8cecd1b57395fa51fd95c824e88885d9c3ae3f09
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105023308"
---
# <a name="set-up-your-azure-percept-dk-and-deploy-your-first-ai-model"></a>Azure Percept DK 'yi ayarlama ve ilk AI modelinizi dağıtma

Geliştirme setini yapılandırmak ve ilk AI modelinizi dağıtmak için Azure Percept DK kurulum deneyimini doldurun. Azure hesabınızın Azure Percept ile uyumlu olduğunu doğruladıktan sonra şunları yapmanız gerekir:

- Geliştirme setini bir Wi-Fi ağa bağlama
- Geliştirme se'nize uzaktan erişim için SSH oturum açma ayarlama
- Azure Percept ile kullanmak için yeni IoT Hub oluşturma
- Geliştirme setinizi IoT Hub ve Azure hesabınıza bağlama

Bu işlem sırasında herhangi bir sorunla karşılaşırsanız, olası çözümler için [Kurulum sorun giderme kılavuzuna](./how-to-troubleshoot-setup.md) bakın.

## <a name="prerequisites"></a>Önkoşullar

- Bir Azure Percept DK (Dev Kit).
- Wi-Fi özelliğine ve bir Web tarayıcısına sahip bir Windows, Linux veya OS X tabanlı ana bilgisayar.
- Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Azure hesabının, abonelikte **sahip** veya **katkıda bulunan** rolü olmalıdır. Azure hesap rolünüzü denetlemek için aşağıdaki adımları izleyin. Azure rol tanımları hakkında daha fazla bilgi için [Azure rol tabanlı erişim denetimi belgelerine](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles)göz atın.

    > [!CAUTION]
    > Birden çok Azure hesabınız varsa, tarayıcınız kimlik bilgilerini başka bir hesaptan önbelleğe alabilir. Karışıklığı önlemek için, kullanılmayan tüm tarayıcı pencerelerini kapatmanız ve kurulum deneyimini başlatmadan önce [Azure Portal](https://portal.azure.com/) oturum açmanız önerilir. Doğru hesap ile oturum açtığınızdan emin olmak hakkında daha fazla bilgi için [Kurulum sorun giderme kılavuzu 'na](./how-to-troubleshoot-setup.md) bakın.

### <a name="check-your-azure-account-role"></a>Azure hesap rolünüzü denetleyin

Azure hesabınızın abonelik içinde "sahip" veya "katkıda bulunan" olup olmadığını doğrulamak için şu adımları izleyin:

1. [Azure Portal](https://portal.azure.com/) gidin ve Azure Percept ile kullanmayı düşündüğünüz Azure hesabıyla oturum açın.

1. **Abonelikler** simgesine tıklayın (sarı bir anahtar gibi görünür).

1. Listeden aboneliğinizi seçin. Aboneliğinizi görmüyorsanız doğru Azure hesabıyla oturum açtığınızdan emin olun. Yeni bir abonelik oluşturmak isterseniz, [aşağıdaki adımları](../cost-management-billing/manage/create-subscription.md)izleyin.

1. Abonelik menüsünden **erişim denetimi (IAM)** seçeneğini belirleyin.
1. **Erişimimi görüntüle**' ye tıklayın.
1. Rolü denetleyin:
    - Rolünüzün **okuyucu** olarak listelenmesi veya rolleri görme izniniz olmadığını belirten bir ileti alırsanız, hesap rolünüzü yükseltmek için kuruluşunuzdaki gerekli süreci izlemeniz gerekir.
    - Rolünüz **sahip** veya **katkıda bulunan** olarak listeleniyorsa, hesabınız Azure Percept ile çalışır ve kurulum deneyimiyle devam edebilirsiniz.

## <a name="launch-the-azure-percept-dk-setup-experience"></a>Azure Percept DK kurulum deneyimini başlatın

1. Ana bilgisayarınızı doğrudan geliştirme seti Wi-Fi erişim noktasına bağlayın. Diğer bir Wi-Fi ağına bağlanma gibi, bilgisayarınızda ağ ve İnternet ayarlarını açın, aşağıdaki ağa tıklayın ve istendiğinde ağ parolasını girin:

    - **Ağ adı**: Dev Kit 'in işletim sistemi sürümüne bağlı olarak, Wi-Fi erişim noktasının adı **SCZ-xxxx** veya **APD-xxxx** olur (burada "xxxx", Dev Kit 'in Mac adresinin son dört rakamı)
    - **Parola**: Dev Kit Ile gelen hoş geldiniz kartında bulunabilir

    > [!WARNING]
    > Azure Percept DK Wi-Fi erişim noktasına bağlıyken, ana bilgisayarınız Internet bağlantısını geçici olarak kaybedecektir. Etkin görüntülü konferans çağrıları, web akışı veya diğer ağ tabanlı deneyimler kesintiye uğracaktır.

1. Geliştirme setinin Wi-Fi erişim noktasına bağlandıktan sonra ana bilgisayar, kurulum deneyimini otomatik olarak yeni bir tarayıcı penceresinde, adres çubuğunda **. yeni. cihazınızın/** ile başlatır. Sekme otomatik olarak açılmadığından, ' ye giderek kurulum deneyimini başlatın [http://10.1.1.1](http://10.1.1.1) . Tarayıcınızın Azure Percept ile kullanmayı düşündüğünüz Azure hesabı kimlik bilgileriyle oturum açtığınızdan emin olun.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-01-welcome.png" alt-text="Hoş geldiniz sayfası.":::

    > [!CAUTION]
    > Kurulum deneyimi Web hizmeti, 30 dakikalık kullanım dışı süre sonra kapatılacak. Bu durumda, Dev Kit 'in Wi-Fi erişim noktası ile bağlantı sorunlarını önlemek için geliştirme setini yeniden başlatın.

## <a name="complete-the-azure-percept-dk-setup-experience"></a>Azure Percept DK kurulum deneyimini doldurun

1. **Hoş geldiniz** ekranında **İleri** ' ye tıklayın.

1. **Ağ bağlantısı** sayfasında, **Yeni bir WiFi ağına bağlan ' a** tıklayın.

    Dev Kit 'i Wi-Fi ağınıza zaten bağladıysanız **Atla**' ya tıklayın.

1. Kullanılabilir ağlar listesinden Wi-Fi ağınızı seçin ve **Bağlan**' a tıklayın. İstendiğinde ağ parolanızı girin.

1. Geliştirme setiniz istediğiniz ağa başarıyla bağlandıktan sonra, sayfada Dev Kit 'e atanan IPv4 adresi görüntülenir. **Sayfada görünen IPv4 adresini yazın.** Sorun giderme ve cihaz güncelleştirmeleri için SSH üzerinden Dev Kit 'e bağlanırken IP adresine ihtiyacınız olacak.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-04-success-wi-fi.png" alt-text="IP adresini kopyalayın.":::

    > [!NOTE]
    > IP adresi, her bir cihaz önyüklemesi ile değişebilir.

1. Lisans sözleşmesini okuyun, **Lisans sözleşmesini okudum ve kabul** ediyorum ' u seçin (Sözleşmenin en altına doğru kaymanız gerekir) ve **İleri**' ye tıklayın.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-05-eula.png" alt-text="EULA 'Yı kabul edin.":::

1. Bir SSH hesap adı ve parola girin ve **daha sonra kullanmak üzere oturum açma bilgilerinizi yazın**.

    > [!NOTE]
    > SSH (Secure Shell), bir ana bilgisayar aracılığıyla Dev Kit 'e uzaktan bağlanmanızı sağlayan bir ağ protokolüdür.

1. Azure hesabınızda yeni bir cihaz oluşturmak için bir sonraki sayfada **yeni cihaz olarak ayarla** ' ya tıklayın.

1. Cihaz kodunuzu kopyalamak için **Kopyala** ' ya tıklayın. Daha sonra **Azure 'Da oturum aç**' a tıklayın.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-08-copy-code.png" alt-text="Cihaz kodunu kopyalayın.":::

1. Yeni bir tarayıcı sekmesi, **kod girin** yazan bir pencere ile açılır. Kodu pencereye yapıştırın ve **İleri**' ye tıklayın. Kurulum deneyimiyle **hoş geldiniz** sekmesini kapatmayın.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-09-enter-code.png" alt-text="Cihaz kodunu girin.":::

1. Geliştirme se'iyle kullanacağınız Azure hesabı kimlik bilgilerini kullanarak Azure Percept 'de oturum açın. Tamamlandığında tarayıcı sekmesini açık bırakın.

    > [!CAUTION]
    > Tarayıcınız diğer kimlik bilgilerini otomatik olarak önbelleğe alabilir. Doğru hesapla oturum açmak için çift kontrol edin.

    Cihazda% Azure 'da başarıyla oturum açtıktan sonra, kurulum deneyimine devam etmek için **hoş geldiniz** sekmesine dönün.

1. **Cihazınızı Azure IoT Hub 'ye atama** , **hoş geldiniz** sekmesinde görüntülendiğinde aşağıdaki eylemlerden birini gerçekleştirin:

    - Azure Percept ile kullanmak istediğiniz bir IoT Hub zaten varsa ve bu sayfada listelenmişse, seçin ve 15. adıma atlayın.
    - Bir IoT Hub yoksa veya yeni bir tane oluşturmak istiyorsanız **yeni Azure IoT Hub oluştur ' a** tıklayın.

    > [!IMPORTANT]
    > Bir IoT Hub varsa ancak listede yoksa, Azure Percept 'de yanlış kimlik bilgileriyle oturum açmış olabilirsiniz. Yardım için [Kurulum sorun giderme kılavuzuna](./how-to-troubleshoot-setup.md) bakın.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-13-iot-hub-select.png" alt-text="Bir IoT Hub seçin.":::

1. Yeni bir IoT Hub oluşturmak için aşağıdaki alanları doldurun:

    - Azure Percept ile kullanacağınız Azure aboneliğini seçin.
    - Var olan bir kaynak grubunu seçin. Bunlardan biri yoksa, **Yeni oluştur** ' a tıklayın ve istemleri izleyin.
    - Fiziksel konumunuza en yakın Azure bölgesini seçin.
    - Yeni IoT Hub bir ad verin.
    - S1 (Standart) fiyatlandırma katmanını seçin.

    > [!NOTE]
    > Edge AI uygulamalarınız için daha yüksek bir [ileti işleme](https://docs.microsoft.com/azure/iot-hub/iot-hub-scaling#message-throughput) gereksinimi varsa, IoT Hub Istediğiniz zaman Azure portalında [daha yüksek bir Standart katmana yükseltebilirsiniz](https://docs.microsoft.com/azure/iot-hub/iot-hub-upgrade) . B ve F katmanları Azure Percept 'ı desteklemez.

1. IoT Hub dağıtım birkaç dakika sürebilir. Dağıtım tamamlandığında **Kaydet**' e tıklayın.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-16-iot-hub-success.png" alt-text="IoT Hub başarıyla dağıtıldı.":::

1. Geliştirme seti için bir cihaz adı girin ve **İleri**' ye tıklayın.

1. Cihaz modüllerinin indirilmesini bekleyin; bu işlem birkaç dakika sürer.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-18-finalize.png" alt-text="Kurulum sonlandırılıyor.":::

1. **Cihaz kurulumunu Tamam** ' a tıkladığınızda! sayfasında, Dev Kit IoT Hub başarıyla bağlandı ve gerekli yazılımları indirdi. Geliştirme seti, bu iki bildirim ile sonuçlanan Wi-Fi erişim noktası bağlantısını otomatik olarak keser:

    <!---
    > [!NOTE]
    > The onboarding process and connection to the device Wifi access to your host computer shuts down at this point, but your dev kit will stay connected to the internet.   You can restart the onboarding experience with a dev kit reboot, which will allow you to go back through the onboarding and reconnect the device to a different IOT hub associated with the same or a different Azure Subscription..
    --->

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-19-0-warning.png" alt-text="Kurulum deneyimi bağlantı kesme uyarısı.":::

1. 2. adımda, ana bilgisayarınızı devkit 'e bağlı Wi-Fi ağa bağlayın.

1. **Azure Portal devam et ' e** tıklayın.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-20-azure-portal-continue.png" alt-text="Azure Percept Studio 'ya gidin.":::

## <a name="view-your-dev-kit-video-stream-and-deploy-a-sample-model"></a>Geliştirme Seti video akışınızı görüntüleyin ve örnek bir model dağıtın

1. [Azure Percept Studio 'Ya genel bakış sayfası](https://go.microsoft.com/fwlink/?linkid=2135819) , hem başlangıç hem de GELIŞMIŞ uç AI çözüm geliştirmesi için birçok farklı iş akışının erişimine yönelik başlatma noktancıdır. Başlamak için sol menüdeki **cihazlar** ' a tıklayın.

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-01-get-device-list.png" alt-text="Cihaz listenizi görüntüleyin.":::

1. Geliştirme Seti 'nizin **bağlı** olarak listelendiğini doğrulayıp cihaz sayfasını görüntülemek için bu öğeyi tıklatın.

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-02-select-device.png" alt-text="Cihazınızı seçin.":::

1. **Cihaz akışınızı görüntüle**' ye tıklayın. Cihazınızın video akışını ilk kez görüntüleiyorsa, sağ üst köşede yeni bir modelin dağıtıldığını belirten bir bildirim görürsünüz. Bu birkaç dakika sürebilir.

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-03-1-start-video-stream.png" alt-text="Video akışınızı görüntüleyin.":::

    Model dağıtıldıktan sonra, bir **Görünüm akışı** bağlantısına sahip başka bir bildirim alırsınız. Azure Percept Vision kameranızdan yeni bir tarayıcı penceresinde video akışını görüntülemek için bağlantıya tıklayın. Geliştirme seti, birçok ortak nesne için otomatik olarak nesne algılamayı gerçekleştiren bir AI modeliyle önceden yüklenir.

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-03-2-object-detection.png" alt-text="Bkz. nesne algılama.":::

1. Azure Percept Studio Ayrıca birçok örnek AI modeline sahiptir. Geliştirme setize örnek bir model dağıtmak için, cihaz sayfanıza geri gidin ve **örnek model Dağıt ' a** tıklayın.

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-04-explore-prebuilt.png" alt-text="Önceden oluşturulmuş modelleri keşfet.":::

1. Kitaplıktan bir örnek model seçin ve **cihaza dağıt**' a tıklayın.

    :::image type="content" source="./media/quickstart-percept-dk-setup/portal-05-2-select-journey.png" alt-text="Bkz. nesne algılama işlemi sürüyor.":::

1. Model başarıyla dağıtıldıktan sonra, ekranın sağ üst köşesinde bulunan **akış görünümü** bağlantısını içeren bir bildirim görürsünüz. Model içinde sınırlama eylemini görüntülemek için, bildirimde bulunan bağlantıya tıklayın veya cihaz sayfasına dönün ve **cihaz akışınızı görüntüle**' ye tıklayın. Daha önce Dev Kit üzerinde çalışan tüm modeller artık yeni modelle birlikte kullanılacaktır.

## <a name="video-walkthrough"></a>Görüntülü kılavuz

Yukarıda açıklanan adımlara görsel bir anlatım için lütfen aşağıdaki videoya bakın (kurulum deneyimi 0:50 ' de başlar):

</br>

> [!VIDEO https://www.youtube.com/embed/-dmcE2aQkDE]

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Kod içermeyen bir Vision çözümü oluşturun](./tutorial-nocode-vision.md)