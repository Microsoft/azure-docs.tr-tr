---
title: "Hızlı başlangıç: Visual Studio 'Yu kullanarak Azure 'da ilk işlevinizi oluşturma"
description: Bu hızlı başlangıçta, Visual Studio kullanarak bir HTTP tetikleyici Azure Işlevi oluşturmayı ve yayımlamayı öğreneceksiniz.
ms.assetid: 82db1177-2295-4e39-bd42-763f6082e796
ms.topic: quickstart
ms.date: 09/30/2020
ms.custom: devx-track-csharp, mvc, devcenter, vs-azure, 23113853-34f2-4f
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./functions-create-your-first-function-visual-studio-uiex
ms.openlocfilehash: d691ee60f624f75c89e44e905e5343bf9c71fd4d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101701424"
---
# <a name="quickstart-create-your-first-function-in-azure-using-visual-studio"></a>Hızlı başlangıç: Visual Studio 'Yu kullanarak Azure 'da ilk işlevinizi oluşturma

Bu makalede, HTTP isteklerine yanıt veren C# sınıf kitaplığı tabanlı bir işlev oluşturmak için Visual Studio 'Yu kullanırsınız. Kodu yerel olarak test ettikten sonra, Azure Işlevlerinin sunucusuz ortamına dağıtırsınız.  

Bu hızlı başlangıcı tamamlamak, Azure hesabınızda birkaç ABD Doları veya daha kısa bir ücret doğurur.

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlayabilmeniz için, önce [Visual Studio 2019](https://azure.microsoft.com/downloads/)' i yüklemeniz gerekir. Yükleme sırasında **Azure geliştirme** iş yükünü seçtiğinizden emin olun. Bunun yerine Visual Studio 2017 kullanarak bir Azure Işlevleri projesi oluşturmak istiyorsanız, önce [en son Azure işlevleri araçlarını](functions-develop-vs.md#check-your-tools-version)yüklemeniz gerekir.

![Azure geliştirme iş yüküyle Visual Studio 'Yu yükler](media/functions-create-your-first-function-visual-studio/functions-vs-workloads.png)

[Azure aboneliğiniz](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing) yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/dotnet/) oluşturun.

## <a name="create-a-function-app-project"></a>İşlev uygulaması projesi oluşturma

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

Visual Studio, HTTP tetikleyici işlev türü için ortak kod içeren bir proje ve sınıf oluşturur. Ortak kod, istek gövdesi veya sorgu dizesinde yer alan bir değeri içeren bir HTTP yanıtı gönderir. `HttpTrigger`Özniteliği, işlevin BIR http isteği tarafından tetiklendiğini belirtir. 

## <a name="rename-the-function"></a>İşlevi yeniden adlandırma

`FunctionName`Yöntem özniteliği, varsayılan olarak olarak oluşturulan işlevin adını ayarlar `Function1` . Araç, projenizi oluştururken varsayılan işlev adını geçersiz kılmanıza izin vermediğinden, işlev sınıfı, dosya ve meta veriler için daha iyi bir ad oluşturmak üzere bir dakikanızı alın.

1. **Dosya Gezgini**'nde işlev1. cs dosyasına sağ tıklayın ve olarak yeniden adlandırın `HttpExample.cs` .

1. Kodda, Işlev1 sınıfını ' HttpExample ' olarak yeniden adlandırın.

1. `HttpTrigger`Adlı yöntemde `Run` , `FunctionName` yöntemi özniteliğini olarak yeniden adlandırın `HttpExample` .

Artık işlevi yeniden adlandırdığınıza göre, bunu yerel bilgisayarınızda test edebilirsiniz.

## <a name="run-the-function-locally"></a>İşlevi yerel olarak çalıştırma

Visual Studio, tüm Azure Işlevleri çalışma zamanını kullanarak işlevlerinizi yerel olarak test edebilmeniz için Azure Functions Core Tools ile tümleşir.  

[!INCLUDE [functions-run-function-test-local-vs](../../includes/functions-run-function-test-local-vs.md)]

İşlevin yerel bilgisayarınızda düzgün çalıştığını doğruladıktan sonra, projeyi Azure'da yayımlamanın zamanı gelmiştir.

## <a name="publish-the-project-to-azure"></a>Projeyi Azure'da yayımlama

Projenizi yayımlayabilmeniz için önce Azure aboneliğinizde bir işlev uygulamasına sahip olmanız gerekir. Visual Studio yayımlama, projenizi ilk kez yayımladığınızda sizin için bir işlev uygulaması oluşturur.

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

## <a name="test-your-function-in-azure"></a>Azure'da işlevinizi test etme

1. Cloud Explorer 'da yeni işlev uygulamanız seçilmelidir. Aksi takdirde, abonelik > **uygulama hizmetleri**' ni genişletin ve yeni işlev uygulamanızı seçin.

1. İşlev uygulamasına sağ tıklayın ve **tarayıcıda aç**' ı seçin. Bu, işlev uygulamanızın kökünü varsayılan Web tarayıcınızda açar ve işlev uygulamanızın çalıştığını gösteren sayfayı görüntüler. 

    :::image type="content" source="media/functions-create-your-first-function-visual-studio/function-app-running-azure.png" alt-text="İşlev uygulaması çalışıyor":::

1. Tarayıcıdaki Adres çubuğunda, dizeyi `/api/HttpExample?name=Functions` temel URL 'ye ekleyin ve isteği çalıştırın.

    HTTP tetikleyici işlevinizi çağıran URL aşağıdaki biçimdedir:

    `http://<APP_NAME>.azurewebsites.net/api/HttpExample?name=Functions`

2. Bu URL 'ye gidin ve aşağıdaki örnekte olduğu gibi işlev tarafından döndürülen uzaktan GET isteğine tarayıcıda bir yanıt görürsünüz:

    :::image type="content" source="media/functions-create-your-first-function-visual-studio/functions-create-your-first-function-visual-studio-browser-azure.png" alt-text="Tarayıcıda işlev yanıtı":::

## <a name="clean-up-resources"></a>Kaynakları temizleme

Bu koleksiyondaki diğer hızlı başlangıçlar, bu hızlı başlangıcı temel alır. Sonraki hızlı başlangıçlarla, öğreticilerle veya bu hızlı başlangıçta oluşturduğunuz hizmetlerden herhangi biriyle çalışmayı planlıyorsanız, kaynakları temizlemeyin.

Azure’da *Kaynaklar*; işlev uygulamalarını, işlevleri, depolama hesaplarını ve benzeri öğeleri ifade eder. Bunlar *kaynak grupları* halinde gruplandırılır ve grubu silerek bir gruptaki her şeyi silebilirsiniz. 

Bu hızlı başlangıçları tamamlamak için kaynaklar oluşturdunuz. [Hesap durumunuza](https://azure.microsoft.com/account/) ve [hizmet fiyatlandırmanıza](https://azure.microsoft.com/pricing/) bağlı olarak size bu kaynakların ücretleri yansıtılabilir. Kaynaklara artık ihtiyacınız yoksa, şunları yaparak silebilirsiniz:

1. Cloud Explorer 'da, abonelik > **uygulama hizmetleri**' ni genişletin, işlev uygulamanıza sağ tıklayın ve **portalda aç**' ı seçin. 

1. İşlev uygulaması sayfasında **genel bakış** sekmesini seçin ve **kaynak grubu**' nun altındaki bağlantıyı seçin.

   :::image type="content" source="media/functions-create-your-first-function-visual-studio/functions-app-delete-resource-group.png" alt-text="İşlev uygulaması sayfasından silinecek kaynak grubunu seçin":::

2. **Kaynak grubu** sayfasında, dahil edilen kaynakların listesini gözden geçirin ve silmek istedikleriniz olduklarından emin olun.
 
3. **Kaynak grubunu sil**’i seçin ve yönergeleri izleyin.

   Silme işlemi birkaç dakika sürebilir. İşlem tamamlandığında, birkaç saniye boyunca bir bildirim görüntülenir. Bildirimi görüntülemek için sayfanın üst kısmındaki zil simgesini de seçebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta, Visual Studio 'Yu kullanarak basit bir HTTP tetikleyici işlevi ile Azure 'da bir C# işlev uygulaması oluşturun ve yayımlayın. 

İşleviniz için bir Azure depolama kuyruğu bağlamayı nasıl ekleyeceğinizi öğrenmek için sonraki makaleye ilerleyin:
> [!div class="nextstepaction"]
> [İşleviniz için bir Azure depolama kuyruğu bağlama ekleme](functions-add-output-binding-storage-queue-vs.md)

