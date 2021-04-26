---
title: Visual Studio Code-Azure Işlevleri kullanarak bir Python işlevi oluşturma
description: Visual Studio Code ' deki Azure Işlevleri uzantısını kullanarak, bir Python işlevi oluşturmayı ve ardından yerel projeyi Azure Işlevleri 'nde sunucusuz barındırma için yayımlamayı öğrenin.
ms.topic: quickstart
ms.date: 11/04/2020
ms.custom: devx-track-python
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./create-first-function-vs-code-python-uiex
ms.openlocfilehash: 3dfafd4412110d8d12a656fbae587087745677f2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101723733"
---
# <a name="quickstart-create-a-function-in-azure-with-python-using-visual-studio-code"></a>Hızlı başlangıç: Visual Studio Code kullanarak Azure 'da Python ile bir işlev oluşturma

[!INCLUDE [functions-language-selector-quickstart-vs-code](../../includes/functions-language-selector-quickstart-vs-code.md)]

Bu makalede, HTTP isteklerine yanıt veren bir Python işlevi oluşturmak için Visual Studio Code kullanırsınız. Kodu yerel olarak test ettikten sonra, Azure Işlevlerinin sunucusuz ortamına dağıtırsınız.

Bu hızlı başlangıcı tamamlamak, Azure hesabınızda birkaç ABD Doları veya daha kısa bir ücret doğurur.

Bu makalenin [CLI tabanlı bir sürümü](create-first-function-cli-python.md) de vardır.

## <a name="configure-your-environment"></a>Ortamınızı yapılandırma

Başlamadan önce, aşağıdaki gereksinimlerin yerinde olduğundan emin olun:

+ Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) sürüm 3. x.

+ [Azure Işlevleri tarafından desteklenen Python sürümleri](supported-languages.md#languages-by-runtime-version)

+ [Desteklenen platformlardan](https://code.visualstudio.com/docs/supporting/requirements#_platforms) birinde çalışan [Visual Studio Code](https://code.visualstudio.com/).

+ Visual Studio Code için [Python uzantısı](https://marketplace.visualstudio.com/items?itemName=ms-python.python) .  

+ Visual Studio Code için [Azure İşlevleri uzantısı](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions).

## <a name="create-your-local-project"></a><a name="create-an-azure-functions-project"></a>Yerel projenizi oluşturma

Bu bölümde, Python 'da yerel bir Azure Işlevleri projesi oluşturmak için Visual Studio Code kullanırsınız. Bu makalenin ilerleyen bölümlerinde işlev kodunu Azure'da yayımlayacaksınız.

1. Etkinlik çubuğundan Azure simgesini seçtikten sonra **Azure: İşlevler** alanında **Yeni proje oluştur...** simgesini seçin.

    ![Yeni proje oluştur'u seçin](./media/functions-create-first-function-vs-code/create-new-project.png)

1. Projenizin çalışma alanı için bir dizin konumu seçtikten sonra **Seç**'i belirleyin.

    > [!NOTE]
    > Bu adımlar, bir çalışma alanının dışında tamamlanacak şekilde tasarlandı. Bu örnekte, bir çalışma alanının parçası olan bir proje klasörünü seçmeyin.

1. İstemlerde aşağıdaki bilgileri sağlayın:

    + **İşlev projeniz için bir dil seçin**: `Python` öğesini seçin.

    + **Sanal ortam oluşturmak için bir Python diğer adı seçin**: Python yorumlayıcının konumunu seçin.  
    Konum gösterilmemişse, Python ikilisinin tam yolunu yazın.  

    + **Projenizin ilk işlevi için bir şablon seçin**: `HTTP trigger` öğesini seçin.

    + **Bir işlev adı sağlayın**: `HttpExample` yazın.

    + **Yetkilendirme düzeyi**: `Anonymous` herhangi bir kişinin işlev uç noktanızı çağırmasını sağlayan öğesini seçin. Yetkilendirme düzeyi hakkında bilgi edinmek için bkz. [Yetkilendirme anahtarları](functions-bindings-http-webhook-trigger.md#authorization-keys).

    + **Projenizi nasıl açmak istediğinizi seçin**: `Add to workspace` öğesini seçin.

1. Bu bilgileri kullanarak, Visual Studio Code HTTP tetikleyicisiyle bir Azure Işlevleri projesi oluşturur. Yerel proje dosyalarını Gezgin içinde görüntüleyebilirsiniz. Oluşturulan dosyalar hakkında daha fazla bilgi edinmek için bkz. [oluşturulan proje dosyaları](functions-develop-vs-code.md#generated-project-files).

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

İşlevin yerel bilgisayarınızda düzgün çalıştığını doğruladıktan sonra, projeyi doğrudan Azure 'da yayımlamak için Visual Studio Code kullanma zamanı vardır.

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

## <a name="publish-the-project-to-azure"></a>Projeyi Azure'da yayımlama

Bu bölümde, Azure aboneliğinizde bir işlev uygulaması ve ilgili kaynaklar oluşturup kodunuzu dağıtırsınız. 

> [!IMPORTANT]
> Varolan bir işlev uygulamasına yayımladığınızda Azure’daki uygulamanın içeriğinin üzerine yazılır. 

1. Etkinlik çubuğunda Azure simgesini seçin, sonra **Azure: işlevler** alanında, **işlev uygulamasına dağıt uygulama...** düğmesini seçin.

    ![Projenizi Azure'da yayımlama](../../includes/media/functions-publish-project-vscode/function-app-publish-project.png)

1. İstemlerde aşağıdaki bilgileri sağlayın:

    + **Klasör seçin**: çalışma alanınızdan bir klasör seçin veya işlev uygulamanızı içeren bir klasöre gidin.   
    Zaten açılmış geçerli bir işlev uygulamanız varsa bunu göremezsiniz.

    + **Abonelik seçin**: kullanılacak aboneliği seçin.  
    Yalnızca bir aboneliğiniz varsa bunu göremezsiniz.

    + **Azure'daki işlev uygulamasını seçin**: `+ Create new Function App` öğesini seçin.  
    ( `Advanced` Bu makalede kapsanmayan seçeneğini seçmeyin.)

    + **İşlev uygulaması için genel olarak benzersiz bir ad girin**: URL yolunda geçerli olan bir ad yazın. Yazdığınız ad, Azure Işlevlerinde benzersiz olduğundan emin olmak için onaylanır. 

    + **Çalışma zamanı seçin**: yerel olarak çalıştırdığınız Python sürümünü seçin. `python --version`Sürümünüzü denetlemek için komutunu kullanabilirsiniz.

    + **Yeni kaynaklar için bir konum seçin**: daha iyi performans için, size yakın bir [bölge](https://azure.microsoft.com/regions/) seçin.

    Uzantı, bildirim alanında Azure 'da oluşturulmakta olan ayrı kaynakların durumunu gösterir.

    :::image type="content" source="../../includes/media/functions-publish-project-vscode/resource-notification.png" alt-text="Azure kaynak oluşturma bildirimi":::

1. Tamamlandığında, aşağıdaki Azure kaynakları, işlev uygulamanızın adına göre adlar kullanılarak aboneliğinizde oluşturulur:

    [!INCLUDE [functions-vs-code-created-resources](../../includes/functions-vs-code-created-resources.md)]

    İşlev uygulamanız oluşturulduktan sonra bir bildirim görüntülenir ve dağıtım paketi uygulanır. 

    [!INCLUDE [functions-vs-code-create-tip](../../includes/functions-vs-code-create-tip.md)]

4. Oluşturduğunuz Azure kaynakları dahil olmak üzere oluşturma ve dağıtım sonuçlarını görüntülemek için bu bildirimde **çıktıyı görüntüle** ' yi seçin. Bildirimi kaçırırsanız, sağ alt köşedeki zil simgesini seçerek tekrar görüntüleyin.

    ![Tüm bildirimi oluştur](./media/functions-create-first-function-vs-code/function-create-notifications.png)

[!INCLUDE [functions-vs-code-run-remote](../../includes/functions-vs-code-run-remote.md)]

[!INCLUDE [functions-cleanup-resources-vs-code.md](../../includes/functions-cleanup-resources-vs-code.md)]

## <a name="next-steps"></a>Sonraki adımlar

Basit bir HTTP ile tetiklenen işlevi olan bir işlev uygulaması oluşturmak için [Visual Studio Code](functions-develop-vs-code.md?tabs=python) kullandınız. Sonraki makalede, Azure depolama 'ya bağlanarak bu işlevi genişletmelisiniz. Diğer Azure hizmetlerine bağlanma hakkında daha fazla bilgi edinmek için bkz. [Azure işlevleri 'nde var olan bir işleve bağlama ekleme](add-bindings-existing-function.md?tabs=python). 

> [!div class="nextstepaction"]
> [Azure depolama kuyruğuna bağlanma](functions-add-output-binding-storage-queue-vs-code.md?pivots=programming-language-python)

[Sorun mu yaşıyorsunuz? Bize bilgi verin.](https://aka.ms/python-functions-qs-survey)

[Azure Functions Core Tools]: functions-run-local.md
[Azure Functions extension for Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
