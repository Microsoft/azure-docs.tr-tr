---
title: Soru-Cevap Oluşturma hizmetini yapılandırma-Soru-Cevap Oluşturma
description: Bu belgede Soru-Cevap Oluşturma kaynaklarınız için gelişmiş yapılandırma özetlenmektedir.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 02/18/2021
ms.openlocfilehash: 3c6f75eafad51c99f60b78ce49862d2488d5926f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102220167"
---
# <a name="configure-qna-maker-resources"></a>Soru-Cevap Oluşturma kaynaklarını yapılandırma

Kullanıcı farklı bir bilişsel arama kaynağı kullanmak için Soru-Cevap Oluşturma yapılandırabilir. Ayrıca, Soru-Cevap Oluşturma GA kullanılıyorsa App Service ayarlarını da yapılandırabilir.

# <a name="qna-maker-ga-stable-release"></a>[Soru-Cevap Oluşturma GA (kararlı sürüm)](#tab/v1)

### <a name="configure-qna-maker-to-use-different-cognitive-search-resource"></a>Soru-Cevap Oluşturma farklı Bilişsel Arama kaynağı kullanacak şekilde yapılandırma

Portal üzerinden bir QnA hizmeti ve bağımlılıklarını (arama gibi) oluşturursanız, sizin için bir arama hizmeti oluşturulur ve Soru-Cevap Oluşturma hizmetine bağlanır. Bu kaynaklar oluşturulduktan sonra, önceden var olan bir arama hizmetini kullanmak için App Service ayarını güncelleştirebilir ve yeni oluşturduğunuz birini kaldırabilirsiniz.

Soru-Cevap Oluşturma **App Service** kaynağı bilişsel arama kaynağını kullanır. Soru-Cevap Oluşturma tarafından kullanılan Bilişsel Arama kaynağını değiştirmek için Azure portal ayarı değiştirmeniz gerekir.

1. Soru-Cevap Oluşturma kullanmak istediğiniz Bilişsel Arama kaynağın **yönetici anahtarını** ve **adını** alın.

1. [Azure Portal](https://portal.azure.com) oturum açın ve soru-cevap oluşturma kaynağınız ile ilişkili **App Service** bulun. Her ikisi de aynı ada sahip.

1. **Ayarlar**' ı ve ardından **yapılandırma**' yı seçin. Bu, Soru-Cevap Oluşturma App Service tüm mevcut ayarlarını görüntüler.

    > [!div class="mx-imgBorder"]
    > ![App Service yapılandırma ayarlarını gösteren Azure portal ekran görüntüsü](../media/qnamaker-how-to-upgrade-qnamaker/change-search-service-app-service-configuration.png)

1. Aşağıdaki anahtarlar için değerleri değiştirin:

    * **AzureSearchAdminKey**
    * **AzureSearchName**

1. Yeni ayarları kullanmak için App Service 'i yeniden başlatmanız gerekir. **Genel bakış**' ı ve ardından **Yeniden Başlat**' ı seçin

    > [!div class="mx-imgBorder"]
    > ![Yapılandırma ayarları değişikliğinden sonra App Service yeniden başlatma Azure portal ekran görüntüsü](../media/qnamaker-how-to-upgrade-qnamaker/screenshot-azure-portal-restart-app-service.png)

Azure Resource Manager şablonları aracılığıyla bir QnA hizmeti oluşturursanız, tüm kaynakları oluşturabilir ve App Service oluşturmayı, var olan bir arama hizmetini kullanacak şekilde denetleyebilirsiniz.

App Service [uygulama ayarlarını](../../../app-service/configure-common.md#configure-app-settings)yapılandırma hakkında daha fazla bilgi edinin.

### <a name="get-the-latest-runtime-updates"></a>En son çalışma zamanı güncelleştirmelerini al

QnAMaker çalışma zamanı, Azure portal [bir qnaoluşturucu hizmeti oluşturduğunuzda](./set-up-qnamaker-service-azure.md) dağıtılan Azure App Service örneğinin bir parçasıdır. Güncelleştirmeler çalışma zamanında düzenli olarak yapılır. Soru-Cevap Oluşturma App Service örneği, Nisan 2019 site uzantısı sürümünden (sürüm 5 +) sonra otomatik güncelleştirme modunda. Bu güncelleştirme, yükseltmeler sırasında sıfır kesinti olması için tasarlanmıştır.

Geçerli sürümünüzü adresinde denetleyebilirsiniz https://www.qnamaker.ai/UserSettings . Sürümünüz 5. x sürümünden eskiyse, en son güncelleştirmeleri uygulamak için App Service yeniden başlatmanız gerekir:

1. [Azure Portal](https://portal.azure.com)QnAMaker hizmetinize (kaynak grubu) gidin.

    > [!div class="mx-imgBorder"]
    > ![QnAMaker Azure Kaynak grubu](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. App Service örneğini seçin ve **genel bakış** bölümünü açın.

    > [!div class="mx-imgBorder"]
    > ![QnAMaker App Service örneği](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)


1. App Service yeniden başlatın. Güncelleştirme işlemi birkaç saniye içinde bitmelidir. Bu QnAMaker hizmetini kullanan bağımlı uygulamalar veya botlar, bu yeniden başlatma döneminde son kullanıcılar için kullanılamaz.

    ![QnAMaker App Service örneğini yeniden başlatma](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

### <a name="configure-app-service-idle-setting-to-avoid-timeout"></a>Zaman aşımını önlemek için App Service boşta ayarını yapılandırın

Yayımlanmış bir bilgi tabanı için Soru-Cevap Oluşturma tahmin çalışma zamanına hizmet eden App Service, boşta kalma zaman aşımı yapılandırmasına sahiptir ve bu hizmet boşta kalırsa otomatik olarak zaman aşımına uğrar. Soru-Cevap Oluşturma için bu, tahmin çalışma zamanı generateAnswer API 'nizin zaman zaman trafik olmadan sonra zaman aşımına uğraymasına yol gösterir.

Tahmin uç noktası uygulamasının trafik olmadığında bile yüklenmesini sağlamak için boşta seçeneğini her zaman açık olarak ayarlayın.

1. [Azure portalında](https://portal.azure.com) oturum açın.
1. Soru-Cevap Oluşturma kaynağınızın App Service 'i arayın ve seçin. Soru-Cevap Oluşturma kaynağıyla aynı ada sahip olur, ancak farklı **türde** App Service olacaktır.
1. **Ayarları** bulun ve **yapılandırma**' yı seçin.
1. Yapılandırma bölmesinde **Genel ayarlar**' ı seçin, **her zaman açık**' i bulun ve değer olarak **Açık** ' ı seçin.

    > [!div class="mx-imgBorder"]
    > ![Yapılandırma bölmesinde, * * Genel Ayarlar * * öğesini seçin, ardından * * Always on * * öğesini bulun ve değer olarak * * seçeneğini belirleyin.](../media/qnamaker-how-to-upgrade-qnamaker/configure-app-service-idle-timeout.png)

1. Yapılandırmayı kaydetmek için **Kaydet** ' i seçin.
1. Yeni ayarı kullanmak için uygulamayı yeniden başlatmak isteyip istemediğiniz sorulur. **Devam**’ı seçin.

App Service [genel ayarlarını](../../../app-service/configure-common.md#configure-general-settings)yapılandırma hakkında daha fazla bilgi edinin.

### <a name="business-continuity-with-traffic-manager"></a>Traffic Manager ile iş sürekliliği

İş sürekliliği planının birincil amacı dayanıklı bir bilgi tabanı uç noktası oluşturmaktır, bu, bot veya onu kullanan uygulama için zaman kaybı olmamasını sağlar.

> [!div class="mx-imgBorder"]
> ![Soru-Cevap Oluşturma bcp planı](../media/qnamaker-how-to-bcp-plan/qnamaker-bcp-plan.png)

Yukarıda gösterilen üst düzey fikir aşağıdaki gibidir:

1. [Azure eşlenmiş bölgelerde](../../../best-practices-availability-paired-regions.md)iki paralel [soru-cevap oluşturma hizmeti](set-up-qnamaker-service-azure.md) ayarlayın.

1. Birincil Soru-Cevap Oluşturma App Service 'i [yedekleyin](../../../app-service/manage-backup.md) ve ikincil kuruluma [geri yükleyin](../../../app-service/web-sites-restore.md) . Bu, her iki kurulum 'un aynı ana bilgisayar adı ve anahtarlarla çalışmasını sağlayacaktır.

1. Birincil ve ikincil Azure arama dizinlerini eşitlenmiş halde tutun. Azure dizinlerini yedekleme ve geri yükleme işlemlerinin nasıl yapılacağını görmek için [burada](https://github.com/pchoudhari/QnAMakerBackupRestore) GitHub örneğini kullanın.

1. Application Insights [sürekli dışarı aktarma](../../../azure-monitor/app/export-telemetry.md)kullanarak yedekleyin.

1. Birincil ve ikincil yığınlar kurulduktan sonra, iki uç noktayı yapılandırmak ve bir yönlendirme yöntemi ayarlamak için [Traffic Manager](../../../traffic-manager/traffic-manager-overview.md) 'ı kullanın.

1. Traffic Manager uç noktanız için önceden Güvenli Yuva Katmanı (SSL) olarak bilinen bir Aktarım Katmanı Güvenliği (TLS) oluşturmanız gerekir. Uygulama hizmetlerinize [TLS/SSL sertifikası bağlayın](../../../app-service/configure-ssl-bindings.md) .

1. Son olarak, bot veya uygulamanızdaki Traffic Manager uç noktasını kullanın.

# <a name="qna-maker-managed-preview-release"></a>[Soru-Cevap Oluşturma Managed (Önizleme sürümü)](#tab/v2)

### <a name="configure-qna-maker-managed-preview-service-to-use-different-cognitive-search-resource"></a>Soru-Cevap Oluşturma yönetilen (Önizleme) hizmetini farklı Bilişsel Arama kaynağı kullanacak şekilde yapılandırma

Portal üzerinden bir QnA hizmeti yönetimli (Önizleme) ve bağımlılıklarını (arama gibi) oluşturursanız, sizin için bir arama hizmeti oluşturulur ve Soru-Cevap Oluşturma yönetilen (Önizleme) hizmetine bağlanır. Bu kaynaklar oluşturulduktan sonra, arama hizmetini **yapılandırma** sekmesinden güncelleştirebilirsiniz.

1. Azure portal Soru-Cevap Oluşturma yönetilen (Önizleme) hizmetinize gidin.

1. **Yapılandırma** ' yı seçin ve soru-cevap oluşturma yönetilen (Önizleme) hizmetinize bağlamak istediğiniz Azure bilişsel arama hizmetini seçin.

    ![Soru-Cevap Oluşturma yönetilen (Önizleme) yapılandırma sayfasının ekran görüntüsü](../media/qnamaker-how-to-upgrade-qnamaker/change-search-service-configuration.png)

1. **Kaydet**’e tıklayın.

> [!NOTE]
> Soru-Cevap Oluşturma ilişkili Azure Search hizmetini değiştirirseniz, zaten içinde mevcut olan tüm bilgi tabanlarına erişiminizi kaybedersiniz. Azure Search hizmetini değiştirmeden önce mevcut bilgi temellerini dışarı aktardığınızdan emin olun.

---
