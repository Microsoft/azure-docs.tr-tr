---
title: Visual Studio Code-Azure Işlevleri kullanarak C# işlevi oluşturma
description: C# işlevi oluşturmayı öğrenin ve ardından Visual Studio Code içindeki Azure Işlevleri uzantısını kullanarak yerel projeyi Azure Işlevleri 'nde sunucusuz barındırma için yayımlayın.
ms.topic: quickstart
ms.date: 11/03/2020
ms.custom: devx-track-csharp
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./create-first-function-vs-code-csharp-ieux
ms.openlocfilehash: ea0b66c49d6f37c6b8f7eaa7f667a63ab09155e0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104952699"
---
# <a name="quickstart-create-a-c-function-in-azure-using-visual-studio-code"></a>Hızlı başlangıç: Visual Studio Code kullanarak Azure 'da C# işlevi oluşturma

[!INCLUDE [functions-language-selector-quickstart-vs-code](../../includes/functions-language-selector-quickstart-vs-code.md)]

Bu makalede, HTTP isteklerine yanıt veren bir C# sınıf kitaplığı tabanlı işlev oluşturmak için Visual Studio Code kullanırsınız. Kodu yerel olarak test ettikten sonra, Azure Işlevlerinin sunucusuz ortamına dağıtırsınız.

Bu hızlı başlangıcı tamamlamak, Azure hesabınızda birkaç ABD Doları veya daha kısa bir ücret doğurur.

Bu makalenin [CLI tabanlı bir sürümü](create-first-function-cli-csharp.md) de vardır.

## <a name="configure-your-environment"></a>Ortamınızı yapılandırma

Başlamadan önce, aşağıdaki gereksinimlerin yerinde olduğundan emin olun:

+ Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) sürüm 3. x.

+ [Desteklenen platformlardan](https://code.visualstudio.com/docs/supporting/requirements#_platforms) birinde çalışan [Visual Studio Code](https://code.visualstudio.com/).

+ Visual Studio Code için [C# uzantısı](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).  

+ Visual Studio Code için [Azure İşlevleri uzantısı](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions).

## <a name="create-your-local-project"></a><a name="create-an-azure-functions-project"></a>Yerel projenizi oluşturma

Bu bölümde, C# ' de yerel bir Azure Işlevleri projesi oluşturmak için Visual Studio Code kullanırsınız. Bu makalenin ilerleyen bölümlerinde işlev kodunu Azure'da yayımlayacaksınız.

1. Etkinlik çubuğundan Azure simgesini seçtikten sonra **Azure: İşlevler** alanında **Yeni proje oluştur...** simgesini seçin.

    ![Yeni proje oluştur'u seçin](./media/functions-create-first-function-vs-code/create-new-project.png)

1. Projenizin çalışma alanı için bir dizin konumu seçtikten sonra **Seç**'i belirleyin.

    > [!NOTE]
    > Bu adımlar, bir çalışma alanının dışında tamamlanacak şekilde tasarlandı. Bu örnekte, bir çalışma alanının parçası olan bir proje klasörünü seçmeyin.

1. İstemlerde aşağıdaki bilgileri sağlayın:

    + **İşlev projeniz için bir dil seçin**: `C#` öğesini seçin.

    + **Projenizin ilk işlevi için bir şablon seçin**: `HTTP trigger` öğesini seçin.

    + **Bir işlev adı sağlayın**: `HttpExample` yazın.

    + **Bir ad alanı sağlayın**: `My.Functions` yazın.

    + **Yetkilendirme düzeyi**: `Anonymous` herhangi bir kişinin işlev uç noktanızı çağırmasını sağlayan öğesini seçin. Yetkilendirme düzeyi hakkında bilgi edinmek için bkz. [Yetkilendirme anahtarları](functions-bindings-http-webhook-trigger.md#authorization-keys).

    + **Projenizi nasıl açmak istediğinizi seçin**: `Add to workspace` öğesini seçin.

1. Bu bilgileri kullanarak, Visual Studio Code HTTP tetikleyicisiyle bir Azure Işlevleri projesi oluşturur. Yerel proje dosyalarını Gezgin içinde görüntüleyebilirsiniz. Oluşturulan dosyalar hakkında daha fazla bilgi edinmek için bkz. [oluşturulan proje dosyaları](functions-develop-vs-code.md#generated-project-files).

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

İşlevin yerel bilgisayarınızda düzgün çalıştığını doğruladıktan sonra, projeyi doğrudan Azure 'da yayımlamak için Visual Studio Code kullanma zamanı vardır.

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../includes/functions-publish-project-vscode.md)]

[!INCLUDE [functions-vs-code-run-remote](../../includes/functions-vs-code-run-remote.md)]

[!INCLUDE [functions-cleanup-resources-vs-code.md](../../includes/functions-cleanup-resources-vs-code.md)]

## <a name="next-steps"></a>Sonraki adımlar

Basit bir HTTP ile tetiklenen işlevi olan bir işlev uygulaması oluşturmak için [Visual Studio Code](functions-develop-vs-code.md?tabs=csharp) kullandınız. Sonraki makalede, Azure Cosmos DB veya Azure depolama birimine bağlanarak bu işlevi genişletmelisiniz. Diğer Azure hizmetlerine bağlanma hakkında daha fazla bilgi edinmek için bkz. [Azure işlevleri 'nde var olan bir işleve bağlama ekleme](add-bindings-existing-function.md?tabs=csharp). 

> [!div class="nextstepaction"]
> [Veritabanına Bağlan](functions-add-output-binding-cosmos-db-vs-code.md?pivots=programming-language-csharp)
> [!div class="nextstepaction"]
> [Azure depolama kuyruğuna bağlanma](functions-add-output-binding-storage-queue-vs-code.md?pivots=programming-language-csharp)

[Azure Functions Core Tools]: functions-run-local.md
[Azure Functions extension for Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
