---
title: Öğrenciler için Azure başlangıç kullanarak bir işlev oluşturma
description: Öğrenciler için Azure başlangıç aboneliği içinden bir Azure Işlevi oluşturmayı öğrenin
ms.topic: how-to
ms.date: 04/29/2020
ms.openlocfilehash: 3ca91999182e717500eacfa73dda834e9cf89562
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106067155"
---
# <a name="create-a-function-using-azure-for-students-starter"></a>Öğrenciler için Azure başlangıç kullanarak bir işlev oluşturma

Bu öğreticide, öğrenciler için Azure başlangıç aboneliğinde bir "Hello World" HTTP işlevi oluşturacağız. Ayrıca, bu abonelik türündeki Azure Işlevleri 'nde nelerin kullanılabildiğini de inceleyeceğiz.

*Öğrenciler için Microsoft Azure başlangıç* , ücretsiz olarak bulutta geliştirme yapmanız gereken Azure ürünlerini kullanmaya başlamanızı sağlar. [Bu teklif hakkında daha fazla bilgi edinin.](https://azure.microsoft.com/offers/ms-azr-0144p/)

Azure İşlevleri, öncelikle bir VM oluşturmak veya bir web uygulaması yayımlamak zorunda kalmadan kodunuzu [sunucusuz](https://azure.microsoft.com/solutions/serverless/) bir ortamda yürütmenize olanak tanır. [Burada Işlevler hakkında daha fazla bilgi edinin.](./functions-overview.md)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-function"></a>İşlev oluşturma

 Bu makalede, Azure Işlevleri 'ni kullanarak Azure portal bir "Hello World" HTTP tetikleyici işlevi oluşturma hakkında bilgi edinin.

![Azure portalında işlev uygulaması oluşturma](./media/functions-create-student-starter/function-app-in-portal-editor.png)

## <a name="sign-in-to-azure"></a>Azure'da oturum açma

Azure hesabınızla [Azure portalında](https://portal.azure.com) oturum açın.

## <a name="create-a-function-app"></a>İşlev uygulaması oluşturma

İşlevlerinizin yürütülmesini barındıran bir işlev uygulamasına sahip olmanız gerekir. İşlev uygulaması, kaynakların daha kolay yönetilmesi, dağıtılması, ölçeklendirilmesi ve paylaşılması için işlevleri bir mantıksal birim olarak gruplandırmanıza olanak tanır.

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

Ardından, yeni işlev uygulamasında bir işlev oluşturun.

## <a name="create-an-http-trigger-function"></a><a name="create-function"></a>HTTP tetikleyici işlevi oluşturma

1. **İşlevler** penceresinin sol menüsünde **işlevler**' i seçin ve ardından üst menüden **Ekle** ' yi seçin. 
 
1. **Yeni işlev** penceresinden **http tetikleyicisi**' ni seçin.

    ![HTTP tetikleyici işlevini seçin](./media/functions-create-student-starter/function-app-select-http-trigger.png)

1. **Yeni işlev** penceresinde, **yeni işlev** için varsayılan adı kabul edin veya yeni bir ad girin. 

1. **Yetkilendirme düzeyi** açılan listesinden **anonim** ' i seçin ve ardından **işlev oluştur**' u seçin.

    Azure, HTTP tetikleyici işlevini oluşturur. Artık bir HTTP isteği göndererek yeni işlevi çalıştırabilirsiniz.

## <a name="test-the-function"></a>İşlevi test etme

1. Yeni HTTP tetikleyici işlevinizde, sol menüden **Code + test** ' i seçin ve ardından üstteki menüden **Işlev URL 'sini al** ' ı seçin.

    ![İşlev URL 'sini Al ' ı seçin](./media/functions-create-student-starter/function-app-select-get-function-url.png)

1. **İşlev URL 'Sini al** iletişim kutusunda, açılan listeden **varsayılan** ' ı seçin ve ardından **Panoya Kopyala** simgesini seçin. 

    ![Azure portalından işlev URL’sini kopyalama](./media/functions-create-student-starter/function-app-develop-tab-testing.png)

1. İşlev URL'sini tarayıcınızın adres çubuğuna yapıştırın. Sorgu dizesi değerini `?name=<your_name>` Bu URL 'nin sonuna ekleyin ve isteği çalıştırmak Için ENTER 'a basın. 

    Aşağıdaki örnekte tarayıcıdaki yanıt gösterilmektedir:

    ![Tarayıcıdaki işlev yanıtı.](./media/functions-create-student-starter/function-app-browser-testing.png)

    İstek URL’si, işlevinize HTTP üzerinden erişmek için varsayılan olarak gerekli olan bir anahtar içerir.

1. İşleviniz çalıştığında, izleme bilgileri günlüklere yazılır. İzleme çıktısını görmek için, portalda **Code + test** sayfasına dönüp sayfanın altındaki **Günlükler** okunu genişletin.

   ![Azure portalında İşlevler günlük görüntüleyicisi.](./media/functions-create-student-starter/function-view-logs.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="supported-features-in-azure-for-students-starter"></a>Öğrenciler için Azure başlangıç 'ta desteklenen özellikler

Öğrenciler için Azure başlangıç bölümünde, Azure Işlevleri çalışma zamanı özelliklerinin çoğuna aşağıda listelenen birkaç anahtar sınırlaması ile erişebilirsiniz:

* HTTP tetikleyicisi desteklenen tek tetikleyici türüdür.
    * Tüm giriş ve çıkış bağlamaları desteklenir! [Tam listeye buradan bakın.](functions-triggers-bindings.md)
* Desteklenen Diller: 
    * C# (.NET Core 2)
    * JavaScript (Node.js 8 & 10)
    * F # (.NET Core 2)
    * [Daha yüksek planlarda desteklenen dillere bakın](supported-languages.md)
* Windows desteklenen tek işletim sistemidir.
* Ölçek, her gün 60 dakika boyunca çalışan [bir ücretsiz katman örneğiyle](https://azure.microsoft.com/pricing/details/app-service/windows/) kısıtlıdır. HTTP trafiği alındığından, ancak başka bir şekilde 0 ' dan 1 ' e kadar örnek olarak ölçeklendirirsiniz.
* Işlevler çalışma zamanının yalnızca [sürüm 2. x ve üzeri](functions-versions.md) sürümleri desteklenir.
* Tüm geliştirici araçları, işlevleri düzenlenmek ve yayımlamak için desteklenir. Buna VS Code, Visual Studio, Azure CLı ve Azure portal dahildir. Portal dışında bir şey kullanmak isterseniz, önce portalda bir uygulama oluşturmanız ve ardından tercih ettiğiniz araçta bir dağıtım hedefi olarak bu uygulamayı seçmeniz gerekir.

## <a name="next-steps"></a>Sonraki adımlar

Artık basit bir HTTP tetikleyici işlevi olan bir işlev uygulaması oluşturmayı tamamladınız. Ardından, yerel araçları, daha fazla dili, izlemeyi ve tümleştirmeleri inceleyebilirsiniz.

 * [Visual Studio kullanarak ilk işlevinizi oluşturma](./functions-create-your-first-function-visual-studio.md)
 * [Visual Studio Code kullanarak ilk işlevinizi oluşturma](./create-first-function-vs-code-csharp.md)
 * [Azure Işlevleri JavaScript Geliştirici Kılavuzu](./functions-reference-node.md)
 * [Azure Işlevleri 'ni kullanarak bir Azure SQL veritabanına bağlanma](./functions-scenario-database-table-cleanup.md)
 * [Azure IŞLEVLERI http bağlamaları hakkında daha fazla bilgi edinin](./functions-bindings-http-webhook.md).
 * [Azure Işlevlerinizi izleyin](./functions-monitoring.md)