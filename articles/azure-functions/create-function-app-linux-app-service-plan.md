---
title: Linux üzerinde Azure portal bir işlev uygulaması oluşturun | Microsoft Docs
description: Azure portalını kullanarak sunucusuz yürütme için ilk Azure İşlevinizi oluşturma hakkında bilgi edinin.
author: ggailey777
manager: gwallace
ms.service: azure-functions
ms.topic: quickstart
ms.date: 02/28/2019
ms.author: glenga
ms.openlocfilehash: 89755e8b70a490f1c5746b23e0d36c5d90b624c1
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73571661"
---
# <a name="create-a-function-app-on-linux-in-an-azure-app-service-plan"></a>Azure App Service planında Linux üzerinde bir işlev uygulaması oluşturma

Azure İşlevleri, işlevlerinizi Linux’ta varsayılan bir Azure App Service kapsayıcısında barındırmanıza olanak sağlar. Bu makalede, bir [App Service planında](functions-scale.md#app-service-plan)çalışan Linux ile barındırılan bir işlev uygulaması oluşturmak için [Azure Portal](https://portal.azure.com) nasıl kullanılacağı açıklanmaktadır. Ayrıca [kendi özel kapsayıcınızı getirebilirsiniz](functions-create-function-linux-custom-image.md).

![Azure portalında işlev uygulaması oluşturma](./media/create-function-app-linux-app-service-plan/function-app-in-portal-editor.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="sign-in-to-azure"></a>Azure'da oturum açma

Azure hesabınızla Azure portalında <https://portal.azure.com> sayfasında oturum açın.

## <a name="create-a-function-app"></a>İşlev uygulaması oluşturma

Linux’ta işlevlerinizin yürütülmesini barındıran bir işlev uygulamasına sahip olmanız gerekir. İşlev uygulaması, işlev kodunuzun yürütülmesine yönelik bir ortam sağlar. Kaynakların daha kolay yönetilmesi, dağıtılması ve paylaşılması için işlevleri bir mantıksal birim olarak gruplandırmanıza olanak tanır. Bu makalede, işlev uygulamanızı oluştururken bir App Service planı oluşturacaksınız.

1. Azure portal sol üst köşesinde bulunan **kaynak oluştur** düğmesini seçin ve ardından **işlem** > **işlev uygulaması**' yı seçin.

    ![Azure portalında işlev uygulaması oluşturma](./media/create-function-app-linux-app-service-plan/function-app-create-flow.png)

2. Görüntünün altındaki tabloda belirtilen işlev uygulaması ayarlarını kullanın.

    ![Yeni işlev uygulaması ayarlarını tanımlama](./media/create-function-app-linux-app-service-plan/function-app-create-flow2.png)

    | Ayar      | Önerilen değer  | Açıklama                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Uygulama adı** | Genel olarak benzersiz bir ad | Yeni işlev uygulamanızı tanımlayan ad. Geçerli karakterler: `a-z`, `0-9`, ve `-`.  | 
    | **Abonelik** | Aboneliğiniz | Bu yeni işlev uygulamasının oluşturulduğu abonelik. | 
    | **[Kaynak Grubu](../azure-resource-manager/resource-group-overview.md)** |  myResourceGroup | İşlev uygulamanızın oluşturulacağı yeni kaynak grubunun adı. |
    | **OS** | Linux | İşlev uygulaması Linux üzerinde çalışır. |
    | **Yayımlama** | Kod | **Çalışma zamanı yığınınızın** varsayılan Linux kapsayıcısı kullanılır. İhtiyacınız olan tek şey, işlev uygulaması proje kodsundır. Başka bir seçenek de özel bir [Docker görüntüsü](functions-create-function-linux-custom-image.md)yayımlamaktır. |
    | **[Barındırma planı](functions-scale.md)** | App Service planı | Kaynakların işlev uygulamanıza nasıl ayrılacağını tanımlayan barındırma planı. App Service bir planda çalıştırdığınızda, [işlev uygulamanızın ölçeklendirmesini](functions-scale.md)kontrol edebilirsiniz.  |
    | **Plan/konum App Service** | Plan oluştur | **Yeni oluştur** ' a tıklayın ve bir **App Service plan** adı sağlayın. Size yakın bir [bölgede](https://azure.microsoft.com/regions/) veya işlevlerinizin erişebileceği diğer hizmetlere yakın bir **konum** seçin. İstediğiniz **[fiyatlandırma katmanını](https://azure.microsoft.com/pricing/details/app-service/linux/)** seçin. <br/>Aynı App Service planında hem Linux hem de Windows işlev uygulamalarını çalıştıramazsınız. |
    | **Çalışma zamanı yığını** | Tercih edilen dil | Tercih ettiğiniz işlev programlama dilini destekleyen bir çalışma zamanı seçin. C# ve F# için **.NET** işlevlerini seçin. |
    | **[Depolama](../storage/common/storage-quickstart-create-account.md)** |  Genel olarak benzersiz bir ad |  İşlev uygulamanız tarafından kullanılan bir depolama hesabı oluşturun. Depolama hesabı adları 3 ile 24 karakter arasında olmalı ve yalnızca sayıyla küçük harf içermelidir. Dilerseniz [depolama hesabı gereksinimlerini](functions-scale.md#storage-account-requirements) karşılayan mevcut bir hesap da kullanabilirsiniz. |
    | **[Application Insights](functions-monitoring.md)** | Etkin | Application Insights varsayılan olarak devre dışıdır. Application Insights tümleştirmesinin etkinleştirilmesini ve App Service planı konumunuz yakınında bir barındırma konumu seçmenize önerilir. Bunu daha sonra yapmak istiyorsanız bkz. [Azure Işlevlerini izleme](functions-monitoring.md).  |

3. İşlev uygulamasını sağlamak ve dağıtmak için **Oluştur**'u seçin.

4. Portalın sağ üst köşesindeki Bildirim simgesini seçin ve **Dağıtım başarılı** iletisini bekleyin.

    ![Yeni işlev uygulaması ayarlarını tanımlama](./media/create-function-app-linux-app-service-plan/function-app-create-notification.png)

5. Yeni işlev uygulamanızı görüntülemek için **Kaynağa git**’i seçin.

Ardından, yeni işlev uygulamasında bir işlev oluşturun. İşlev uygulamanız kullanılabilir olsa bile, tam olarak başlatılması birkaç dakika sürebilir.

## <a name="create-function"></a>HTTP ile tetiklenen bir işlev oluşturma

Bu bölümde, portalda yeni işlev uygulamanızda nasıl bir işlev oluşturacağınız gösterilmektedir.

> [!NOTE]
> Portal geliştirme deneyimi, Azure Işlevleri 'ni denemek için yararlı olabilir. Çoğu senaryoda, işlevlerinizi yerel olarak geliştirmeyi ve [Visual Studio Code](functions-create-first-function-vs-code.md#create-an-azure-functions-project) ya da [Azure Functions Core Tools](functions-run-local.md#create-a-local-functions-project)kullanarak projeyi işlev uygulamanızda yayımlamayı düşünün.  

1. Yeni işlev uygulamanızda **genel bakış** sekmesini seçin ve tamamen yüklendikten sonra **+ yeni işlev**' i seçin.

    ![Genel Bakış sekmesinden yeni bir işlev oluşturma](./media/create-function-app-linux-app-service-plan/overview-create-function.png)

1. **Hızlı başlangıç** sekmesinde **Portal içi**' i seçin ve **devam**' ı seçin.

    ![İşlev geliştirme platformunuzu seçin.](./media/create-function-app-linux-app-service-plan/function-app-quickstart-choose-portal.png)

1. **Web Kancası + API**'yi ve ardından **Oluştur**'u seçin.

    ![Azure portalındaki İşlevler hızlı başlangıcı.](./media/create-function-app-linux-app-service-plan/function-app-quickstart-node-webhook.png)

Dile özgü HTTP ile tetiklenen işlev şablonu kullanılarak bir işlev oluşturulur.

Artık bir HTTP isteği göndererek yeni işlevi çalıştırabilirsiniz.

## <a name="test-the-function"></a>İşlevi test etme

1. Yeni işlevinizde sağ üst kısımdaki **</> İşlev URL'sini al**'a tıklayın, **varsayılan (İşlev anahtarı)** seçeneğini belirleyin ve ardından **Kopyala**'ya tıklayın. 

    ![Azure portalından işlev URL’sini kopyalama](./media/create-function-app-linux-app-service-plan/function-app-develop-tab-testing.png)

2. İşlev URL'sini tarayıcınızın adres çubuğuna yapıştırın. `&name=<yourname>` sorgu dizesi değerini bu URL’nin sonuna ekleyin ve isteği yürütmek için klavyenizdeki `Enter` tuşuna basın. İşlev tarafından döndürülen yanıtın tarayıcıda gösterildiğini görürsünüz.  

    Aşağıdaki örnekte tarayıcıdaki yanıt gösterilmektedir:

    ![Tarayıcıdaki işlev yanıtı.](./media/create-function-app-linux-app-service-plan/function-app-browser-testing.png)

    İstek URL’si, işlevinize HTTP üzerinden erişmek için varsayılan olarak gerekli olan bir anahtar içerir.

3. İşleviniz çalıştığında, izleme bilgileri günlüklere yazılır. Önceki yürütme işleminden alınan izleme çıktısını görmek için, portalda işlevinize geri dönün ve ekranın altındaki oka tıklayarak **Günlükler**’i genişletin.

   ![Azure portalında İşlevler günlük görüntüleyicisi.](./media/create-function-app-linux-app-service-plan/function-view-logs.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Sonraki adımlar

HTTP ile tetiklenen basit bir işlevi kullanarak işlev uygulaması oluşturdunuz.  

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

Daha fazla bilgi için bkz. [Azure İşlevleri HTTP bağlamaları](functions-bindings-http-webhook.md).
