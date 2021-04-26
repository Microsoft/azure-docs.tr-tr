---
title: Azure Işlevlerinizi bir paketten çalıştırın
description: Azure Işlevleri çalışma zamanının, işlev uygulaması proje dosyalarınızı içeren bir dağıtım paketi dosyası bağlayarak işlevlerinizi çalıştırmasını sağlayabilirsiniz.
ms.topic: conceptual
ms.date: 07/15/2019
ms.openlocfilehash: aad6991d0ddd5c439d03e41adec63837a21db87b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104581601"
---
# <a name="run-your-azure-functions-from-a-package-file"></a>Azure Işlevlerinizi bir paket dosyasından çalıştırın

Azure 'da işlevlerinizi doğrudan işlev uygulamanızdaki bir dağıtım paketi dosyasından çalıştırabilirsiniz. Diğer seçenek, dosyalarınızı `d:\home\site\wwwroot` işlev uygulamanızın dizinine dağıtmaktır.

Bu makalede, işlevlerinizi bir paketten çalıştırmanın avantajları açıklanmaktadır. Ayrıca, işlev uygulamanızda bu işlevselliği nasıl etkinleştireceğinizi gösterir.

## <a name="benefits-of-running-from-a-package-file"></a>Bir paket dosyasından çalıştırmanın avantajları
  
Bir paket dosyasından çalıştırmanın çeşitli avantajları vardır:

+ Dosya kopyalama kilitleme sorunları riskini azaltır.
+ , Bir üretim uygulamasına dağıtılabilir (yeniden başlatma ile).
+ Uygulamanızda çalışmakta olan dosyalardan emin olabilirsiniz.
+ [Azure Resource Manager dağıtımlarının](functions-infrastructure-as-code.md)performansını geliştirir.
+ Özellikle büyük NPM paket ağaçları olan JavaScript işlevleri için soğuk başlangıç zamanlarını azaltabilir.

Daha fazla bilgi için [bu duyuruya](https://github.com/Azure/app-service-announcements/issues/84)bakın.

## <a name="enabling-functions-to-run-from-a-package"></a>İşlevleri bir paketten çalıştırmak için etkinleştirme

İşlev uygulamanızın bir paketten çalıştırılmasını sağlamak için, `WEBSITE_RUN_FROM_PACKAGE` işlev uygulaması ayarlarınıza yalnızca bir ayar eklersiniz. `WEBSITE_RUN_FROM_PACKAGE`Ayar aşağıdaki değerlerden birine sahip olabilir:

| Değer  | Açıklama  |
|---------|---------|
| **`1`**  | Windows üzerinde çalışan işlev uygulamaları için önerilir. İşlev uygulamanızın klasöründeki bir paket dosyasından çalıştırın `d:\home\data\SitePackages` . [ZIP dağıtımı ile dağıtılmadığından](#integration-with-zip-deployment), bu seçenek klasörün ayrıca adlı bir dosyaya sahip olmasını gerektirir `packagename.txt` . Bu dosya, bir boşluk olmadan yalnızca klasördeki paket dosyasının adını içerir. |
|**`<URL>`**  | Çalıştırmak istediğiniz belirli bir paket dosyasının konumu. Blob depolamayı kullanırken, Işlevlerin çalışma zamanının pakete erişmesini sağlamak için [paylaşılan erişim imzası (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) olan bir özel kapsayıcı kullanmanız gerekir. Paket dosyalarını BLOB depolama hesabınıza yüklemek için [Azure Depolama Gezgini](../vs-azure-tools-storage-manage-with-storage-explorer.md) kullanabilirsiniz. Bir URL belirttiğinizde, güncelleştirilmiş bir paket yayımladıktan sonra [Tetikleyicileri de eşitlemeniz](functions-deployment-technologies.md#trigger-syncing) gerekir. |

> [!CAUTION]
> Windows üzerinde bir işlev uygulaması çalıştırırken, dış URL seçeneği daha kötü soğuk başlangıç performansı verir. İşlev uygulamanızı Windows 'a dağıttığınızda, ' a ayarlamanız `WEBSITE_RUN_FROM_PACKAGE` `1` ve ZIP dağıtımına yayımlamanız gerekir.

Aşağıda, Azure Blob depolamada barındırılan bir. zip dosyasından çalışacak şekilde yapılandırılmış bir işlev uygulaması gösterilmektedir:

![WEBSITE_RUN_FROM_ZIP uygulama ayarı](./media/run-functions-from-deployment-package/run-from-zip-app-setting-portal.png)

> [!NOTE]
> Şu anda yalnızca. zip paketi dosyaları desteklenir.

## <a name="integration-with-zip-deployment"></a>ZIP dağıtımıyla tümleştirme

[ZIP dağıtımı][Zip deployment for Azure Functions] , işlev uygulaması projenizi dizine dağıtmanıza olanak sağlayan bir Azure App Service özelliğidir `wwwroot` . Proje bir. zip dağıtım dosyası olarak paketlenmiştir. Paketinizi klasöre dağıtmak için aynı API 'Ler kullanılabilir `d:\home\data\SitePackages` . `WEBSITE_RUN_FROM_PACKAGE`Uygulama ayarı değeri ile `1` , ZIP dağıtım API 'leri `d:\home\data\SitePackages` dosyaları içine ayıklamak yerine paketini klasörüne kopyalar `d:\home\site\wwwroot` . Ayrıca dosyayı da oluşturur `packagename.txt` . Yeniden başlatmadan sonra, paket `wwwroot` salt okunurdur. ZIP dağıtımı hakkında daha fazla bilgi için bkz. [Azure işlevleri Için zip dağıtımı](deployment-zip-push.md).

> [!NOTE]
> Bir dağıtım gerçekleştiğinde, işlev uygulamasının yeniden başlatılması tetiklenir. Yeniden başlatmadan önce, var olan tüm işlev yürütmeleri tamamlanana veya zaman aşımına izin verilir. Daha fazla bilgi için bkz. [dağıtım davranışları](functions-deployment-technologies.md#deployment-behaviors).

## <a name="adding-the-website_run_from_package-setting"></a>WEBSITE_RUN_FROM_PACKAGE ayarı ekleniyor

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]


## <a name="troubleshooting"></a>Sorun giderme

- Paketten Çalıştır `wwwroot` salt okunurdur, bu nedenle dosyaları bu dizine yazarken bir hata alırsınız.
- Tar ve gzip biçimleri desteklenmez.
- ZIP dosyası en fazla 1 GB olabilir.
- Bu özellik yerel önbellek ile oluşturmaz.
- İyileştirilmiş soğuk başlangıç performansı için yerel ZIP seçeneğini ( `WEBSITE_RUN_FROM_PACKAGE` = 1) kullanın.
- Paketten Çalıştır, dağıtım özelleştirme seçeneği () ile uyumlu değil `SCM_DO_BUILD_DURING_DEPLOYMENT=true` , dağıtım sırasında derleme adımı yok sayılır.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Azure Işlevleri için sürekli dağıtım](functions-continuous-deployment.md)

[Zip deployment for Azure Functions]: deployment-zip-push.md
