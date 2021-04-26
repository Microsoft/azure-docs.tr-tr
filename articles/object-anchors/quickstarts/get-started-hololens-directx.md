---
title: 'Hızlı başlangıç: DirectX ile bir HoloLens uygulaması oluşturma'
description: Bu hızlı başlangıçta, nesne bağlayıcılarını kullanarak bir HoloLens uygulaması oluşturmayı öğreneceksiniz.
author: craigktreasure
manager: virivera
ms.author: crtreasu
ms.date: 02/02/2021
ms.topic: quickstart
ms.service: azure-object-anchors
ms.openlocfilehash: b5db9f3766bdd7d754f49403665a371f9d10afd7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105047618"
---
# <a name="quickstart-create-a-hololens-app-with-azure-object-anchors-in-cwinrt-and-directx"></a>Hızlı başlangıç: C++/Wınrt ve DirectX 'te Azure nesne bağlayıcıları ile bir HoloLens uygulaması oluşturma

Bu hızlı başlangıçta, C++/Wınrt ve DirectX 'teki [Azure nesne tutturucularını](../overview.md) kullanarak bir HoloLens uygulamasının nasıl oluşturulacağı ele alınmaktadır. Azure nesne bağlantıları, HoloLens için nesne açısından algılayan karma gerçeklik deneyimlerini etkinleştiren bir yönetilen bulut hizmetidir. İşiniz bittiğinde, bir holographic DirectX 11 (Evrensel Windows) uygulamasında bir nesneyi ve onun pozsını algılayan bir HoloLens uygulamanız olacaktır.

Şunları öğrenirsiniz:

> [!div class="checklist"]
> * HoloLens uygulaması oluşturma ve dışarıdan yükleme
> * Bir nesneyi algılar ve modelini görselleştirin

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Önkoşullar

Bu hızlı başlangıcı tamamlamak için aşağıdakileri yaptığınızdan emin olun:

* Ortamınızdaki fiziksel bir nesne ve kendi 3B modeli (CAD veya taranan).
* Aşağıdaki yüklü bir Windows makinesi:
  * <a href="https://git-scm.com" target="_blank">Windows için Git</a>
  * **Evrensel Windows platformu geliştirme** iş yükü ve **Windows 10 SDK (10.0.18362.0 veya daha yeni)** bileşeniyle <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a>
* Güncel ve [Geliştirici modu](/windows/mixed-reality/using-visual-studio#enabling-developer-mode) etkin olan bir HoloLens 2 cihazı.
  * HoloLens 'te en son sürüme güncelleştirmek için **Ayarlar** uygulamasını açın, **güncelleştirme & güvenliği**' ne gidin ve ardından **Güncelleştirmeleri denetle**' yi seçin.

## <a name="open-the-sample-project"></a>Örnek projeyi açın

[!INCLUDE [Clone Sample Repo](../../../includes/object-anchors-clone-sample-repository.md)]

`quickstarts/apps/directx/DirectXAoaSampleApp.sln`Visual Studio 'da açın.

**Çözüm yapılandırmasını** **serbest** olarak değiştirin, **çözüm platformunu** **ARM64** olarak değiştirin, dağıtım hedefi seçeneklerinden **cihaz** ' ı seçin. Ardından, projeye sağ tıklayıp **Yapı**' yı seçerek **Aoimsampleapp** projesini derleyin.

:::image type="content" source="./media/vs-deploy-to-device.png" alt-text="Dağıtılacak Visual Studio projesini yapılandırma":::

## <a name="deploy-the-app-to-hololens"></a>Uygulamayı HoloLens 'e dağıtın

Örnek proje başarıyla derlendikten sonra, uygulamayı HoloLens 'e dağıtabilirsiniz.

HoloLens cihazında güç açın, oturum açın ve USB kablosu kullanarak BILGISAYARA bağlayın. **Cihazın** Seçili dağıtım hedefi olduğundan emin olun (yukarıya bakın).

**Aoimimapp** Project ' e sağ tıklayın ve ardından uygulamayı yüklemek için açılır menüden **Dağıt** ' a tıklayın. Visual Studio 'nun **Çıkış penceresi** hiçbir hata gösterilmeyecektir, uygulama HoloLens 'e yüklenir.

:::image type="content" source="./media/vs-deploy-app.png" alt-text="Uygulamayı HoloLens 'e dağıtma":::

Uygulamayı başlatmadan önce bir nesne modeli yüklemeniz gerekir. **Nesne modeli alma ve örnek bölümünü algılama** bölümündeki yönergeleri izleyin.

Uygulamayı başlatmak ve hatalarını ayıklamak için **hata ayıkla > hata ayıklamayı Başlat**' ı seçin. Uygulamayı durdurmak için, **hata ayıklamayı Durdur** ' u seçin veya **SHIFT + F5**' e basın.

## <a name="ingest-object-model-and-detect-its-instance"></a>Nesne modelini alma ve örneğini algılama

Örnek uygulamayı çalıştırmak için bir nesne modeli oluşturmanız gerekir. Alanınızda bir nesnenin zaten bir CAD veya taranmış bir 3B kafes modeli olduğunu varsayalım. [Hızlı başlangıç: model oluşturma hakkında bir 3B modeli](./get-started-model-conversion.md) alma bölümüne bakın.

Bu modeli, büyük/küçük bir **OU** 'yu bilgisayarınıza indirin. Ardından, HoloLens cihaz portalından **System > dosya Gezgini ' ni seçerek > LocalAppData > AoaSampleApp > LocalState** ' i seçin ve **bul...**' u seçin. Ardından model dosyanızı, **sandalyesi. OU** 'yu seçin ve **karşıya yükle**' yi seçin. Ardından model dosyasını yerel önbellekte görmeniz gerekir.

:::image type="content" source="../../../includes/media/object-anchors-quickstarts/portal-upload-model.png" alt-text="Portal karşıya yükleme modeli":::

HoloLens 'ten, **Aoimsampleapp** uygulamasını başlatın (zaten açıksa, kapatın ve yeniden açın). Hedef nesneye (sandalye) yakın bir şekilde (2 ölçüm mesafesi dahilinde) ilerleme ve birden çok perspektiften arayarak tarama yapın. Nesnenin etrafında pembe bir sınırlayıcı kutu görmeniz gerekir, bu, algılanan bazı sarı noktaları nesnenin yüzeyine yakın bir şekilde işlendi ve bu da algılandığını gösterir.

:::image type="content" source="./media/chair-detection.png" alt-text="Sandalye algılama":::

Şekil: sınırlayıcı kutusu (pembe), işaret bulutu (sarı) ve bir arama alanı (büyük sarı kutu) ile işlenen algılanan bir sandalye.

Sağ veya sol taraftaki uçak ile kliya tıklayarak uygulamada nesne için bir arama alanı tanımlayabilirsiniz. Arama alanı 2 Ölçüm yarıçapı, 4 milyon bir sınırlayıcı kutu ve Frustum görünümü arasında geçiş yapar. Otomobiller gibi daha büyük nesneler için en iyi seçenek, genellikle 2 Ölçüm uzaklığına ilişkin nesnenin bir köşesine bakacak şekilde Frustum seçimini de kullanacaktır.
Arama alanı her değiştiğinde, uygulama izlenmekte olan örnekleri kaldırır ve ardından yeni arama alanında bunları yeniden bulmaya çalışır.

Bu uygulama, bir seferde birden çok nesneyi izleyebilir. Bunu yapmak için, birden çok modeli **Localstate** klasörüne yükleyin ve tüm hedef nesneleri içeren bir arama alanı ayarlayın. Birden çok nesneyi tespit etmek ve izlemek daha uzun sürebilir.

Uygulama, 3B modeli fiziksel karşılığına yakından hizalar. Bir Kullanıcı, sol taraflarını kullanarak, daha doğru bir poz hesaplayan yüksek duyarlıklı izleme modunu açabilir. Bu, daha fazla sistem kaynağı tüketen bir deneysel özelliktir ve tahmini pozda daha yüksek bir değişim elde edebilir. Normal izleme moduna geri dönmek için, sol taraftaki bir kez daha dokunun.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Hızlı başlangıç: 3B modeli giriş](./get-started-model-conversion.md)

> [!div class="nextstepaction"]
> [Kavramlar: SDK 'ya genel bakış](../concepts/sdk-overview.md)

> [!div class="nextstepaction"]
> [SSS](../faq.md)