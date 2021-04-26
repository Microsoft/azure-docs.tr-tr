---
title: Visual Studio Code-Azure Işlevleri kullanarak bir Java işlevi oluşturma
description: Java işlevi oluşturmayı öğrenin ve ardından Visual Studio Code içindeki Azure Işlevleri uzantısını kullanarak yerel projeyi Azure Işlevleri 'nde sunucusuz barındırma için yayımlayın.
ms.topic: quickstart
ms.date: 11/03/2020
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./create-first-function-vs-code-java-uiex
ms.openlocfilehash: 10ac677fb65428e8b8855c43b319bbebe7bbbb71
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101701441"
---
# <a name="quickstart-create-a-java-function-in-azure-using-visual-studio-code"></a>Hızlı başlangıç: Visual Studio Code kullanarak Azure 'da Java işlevi oluşturma

> [!div class="op_single_selector" title1="İşlev dilinizi seçin: "]
> - [Java](create-first-function-vs-code-java.md)
> - [Python](create-first-function-vs-code-python.md)
> - [C#](create-first-function-vs-code-csharp.md)
> - [JavaScript](create-first-function-vs-code-node.md)
> - [PowerShell](create-first-function-vs-code-powershell.md)
> - [TypeScript](create-first-function-vs-code-typescript.md)
> - [Diğer (Go/Rust)](create-first-function-vs-code-other.md)

Bu makalede, HTTP isteklerine yanıt veren bir Java işlevi oluşturmak için Visual Studio Code kullanırsınız. Kodu yerel olarak test ettikten sonra, Azure Işlevlerinin sunucusuz ortamına dağıtırsınız.

Bu hızlı başlangıcı tamamlamak, Azure hesabınızda birkaç ABD Doları veya daha kısa bir ücret doğurur.

> [!NOTE]
> Tercih ettiğiniz geliştirme aracınız Visual Studio Code, [Maven](create-first-function-cli-java.md), [Gradle](./functions-create-first-java-gradle.md) ve [IntelliJ fikrini](/azure/developer/java/toolkit-for-intellij/quickstart-functions)kullanarak Java geliştiricileri için benzer öğreticilerimize göz atın.

## <a name="configure-your-environment"></a>Ortamınızı yapılandırma

Başlamadan önce, aşağıdaki gereksinimlerin yerinde olduğundan emin olun:

+ Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [Java geliştirici seti](/azure/developer/java/fundamentals/java-jdk-long-term-support), sürüm 8 veya 11.

+ [Apache Maven](https://maven.apache.org), sürüm 3,0 veya üzeri.

+ [Desteklenen platformlardan](https://code.visualstudio.com/docs/supporting/requirements#_platforms) birinde çalışan [Visual Studio Code](https://code.visualstudio.com/).

+ [Java Uzantı paketi](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack)  

+ Visual Studio Code için [Azure İşlevleri uzantısı](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions). 

## <a name="create-your-local-project"></a><a name="create-an-azure-functions-project"></a>Yerel projenizi oluşturma

Bu bölümde, Java 'da yerel bir Azure Işlevleri projesi oluşturmak için Visual Studio Code kullanırsınız. Bu makalenin ilerleyen bölümlerinde işlev kodunu Azure'da yayımlayacaksınız. 

1. Etkinlik çubuğundan Azure simgesini seçtikten sonra **Azure: İşlevler** alanında **Yeni proje oluştur...** simgesini seçin.

    ![Yeni proje oluştur'u seçin](./media/functions-create-first-function-vs-code/create-new-project.png)

1. Projenizin çalışma alanı için bir dizin konumu seçtikten sonra **Seç**'i belirleyin.

    > [!NOTE]
    > Bu adımlar, bir çalışma alanının dışında tamamlanacak şekilde tasarlandı. Bu örnekte, bir çalışma alanının parçası olan bir proje klasörünü seçmeyin.

1. İstemlerde aşağıdaki bilgileri sağlayın:

    + **İşlev projeniz için bir dil seçin**: `Java` öğesini seçin.

    + **Java sürümü seçin**: `Java 8` `Java 11` Işlevlerinizin Azure 'da çalıştığı Java sürümünü seçin veya seçin. Yerel olarak doğruladığınızı bir Java sürümü seçin.

    + **Bir grup kimliği sağlayın**: Seç `com.function` .

    + **YAPıT kimliği sağlayın**: seçin `myFunction` .

    + **Bir sürüm belirtin**: Seç `1.0-SNAPSHOT` .

    + **Bir paket adı belirtin**: Seç `com.function` .

    + **Bir uygulama adı belirtin**: Seç `myFunction-12345` .

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

Basit bir HTTP ile tetiklenen işlevi olan bir işlev uygulaması oluşturmak için [Visual Studio Code](functions-develop-vs-code.md?tabs=java) kullandınız. Sonraki makalede, Azure depolama 'ya bağlanarak bu işlevi genişletmelisiniz. Diğer Azure hizmetlerine bağlanma hakkında daha fazla bilgi edinmek için bkz. [Azure işlevleri 'nde var olan bir işleve bağlama ekleme](add-bindings-existing-function.md?tabs=java). 

> [!div class="nextstepaction"]
> [Azure depolama kuyruğuna bağlanma](functions-add-output-binding-storage-queue-vs-code.md?pivots=programming-language-java)

[Azure Functions Core Tools]: functions-run-local.md
[Azure Functions extension for Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
