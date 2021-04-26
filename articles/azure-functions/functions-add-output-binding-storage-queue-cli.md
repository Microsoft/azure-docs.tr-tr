---
title: Komut satırı araçlarını kullanarak Azure Işlevlerini Azure depolama 'ya bağlama
description: Komut satırı projenize bir çıkış bağlaması ekleyerek Azure Işlevlerini bir Azure depolama kuyruğuna bağlamayı öğrenin.
ms.date: 02/07/2020
ms.topic: quickstart
ms.custom: devx-track-python
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: 6d0d559579e841553e04c26bf4dcc78934de3e95
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94920211"
---
# <a name="connect-azure-functions-to-azure-storage-using-command-line-tools"></a>Komut satırı araçlarını kullanarak Azure Işlevlerini Azure depolama 'ya bağlama

Bu makalede, bir Azure depolama kuyruğunu önceki hızlı başlangıç makalesinde oluşturduğunuz işlev ve depolama hesabıyla tümleştirmeniz gerekir. Bu tümleştirmeyi, HTTP isteğinden kuyruktaki bir iletiye veri yazan bir *Çıkış bağlaması* kullanarak elde edersiniz. Bu makalenin tamamlanması, önceki hızlı başlangıç için birkaç USD 'nin ötesinde ek maliyet yoktur. Bağlamalar hakkında daha fazla bilgi edinmek için bkz. [Azure işlevleri Tetikleyicileri ve bağlamaları kavramları](functions-triggers-bindings.md).

## <a name="configure-your-local-environment"></a>Yerel ortamınızı yapılandırma

::: zone pivot="programming-language-csharp"  
Başlamadan önce, [komut satırından hızlı başlangıç: Azure işlevleri projesi oluşturma](create-first-function-cli-csharp.md)makalesini doldurmanız gerekir. Bu makalenin sonunda kaynakları zaten temizlediğinizde, işlev uygulamasını ve ilgili kaynakları Azure 'da yeniden oluşturmak için adımlara tekrar gidin.  
::: zone-end  
::: zone pivot="programming-language-javascript"  
Başlamadan önce, [komut satırından hızlı başlangıç: Azure işlevleri projesi oluşturma](create-first-function-cli-node.md)makalesini doldurmanız gerekir. Bu makalenin sonunda kaynakları zaten temizlediğinizde, işlev uygulamasını ve ilgili kaynakları Azure 'da yeniden oluşturmak için adımlara tekrar gidin.  
::: zone-end   
::: zone pivot="programming-language-java"  
Başlamadan önce, [komut satırından hızlı başlangıç: Azure işlevleri projesi oluşturma](create-first-function-cli-java.md)makalesini doldurmanız gerekir. Bu makalenin sonunda kaynakları zaten temizlediğinizde, işlev uygulamasını ve ilgili kaynakları Azure 'da yeniden oluşturmak için adımlara tekrar gidin.  
::: zone-end   
::: zone pivot="programming-language-typescript"  
Başlamadan önce, [komut satırından hızlı başlangıç: Azure işlevleri projesi oluşturma](create-first-function-cli-typescript.md)makalesini doldurmanız gerekir. Bu makalenin sonunda kaynakları zaten temizlediğinizde, işlev uygulamasını ve ilgili kaynakları Azure 'da yeniden oluşturmak için adımlara tekrar gidin.  
::: zone-end   
::: zone pivot="programming-language-python"  
Başlamadan önce, [komut satırından hızlı başlangıç: Azure işlevleri projesi oluşturma](create-first-function-cli-python.md)makalesini doldurmanız gerekir. Bu makalenin sonunda kaynakları zaten temizlediğinizde, işlev uygulamasını ve ilgili kaynakları Azure 'da yeniden oluşturmak için adımlara tekrar gidin.  
::: zone-end   
::: zone pivot="programming-language-powershell"  
Başlamadan önce, [komut satırından hızlı başlangıç: Azure işlevleri projesi oluşturma](create-first-function-cli-powershell.md)makalesini doldurmanız gerekir. Bu makalenin sonunda kaynakları zaten temizlediğinizde, işlev uygulamasını ve ilgili kaynakları Azure 'da yeniden oluşturmak için adımlara tekrar gidin.  
::: zone-end   

[!INCLUDE [functions-cli-get-storage-connection](../../includes/functions-cli-get-storage-connection.md)]

[!INCLUDE [functions-register-storage-binding-extension-csharp](../../includes/functions-register-storage-binding-extension-csharp.md)]

[!INCLUDE [functions-add-output-binding-cli](../../includes/functions-add-output-binding-cli.md)]

::: zone pivot="programming-language-csharp"  
[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]  
::: zone-end  
::: zone pivot="programming-language-java" 
[!INCLUDE [functions-add-output-binding-java-cli](../../includes/functions-add-output-binding-java-cli.md)]
::: zone-end   

Bağlamaların ayrıntıları hakkında daha fazla bilgi için bkz. [Azure işlevleri Tetikleyicileri ve bağlamaları kavramları](functions-triggers-bindings.md) ve [sıra çıkış yapılandırması](functions-bindings-storage-queue-output.md#configuration).

## <a name="add-code-to-use-the-output-binding"></a>Çıkış bağlamayı kullanmak için kod ekleme

Sıra bağlaması tanımlı ile, artık işlevinizi, `msg` Çıkış parametresini alacak ve kuyruğa ileti yazacak şekilde güncelleştirebilirsiniz.

::: zone pivot="programming-language-python"     
[!INCLUDE [functions-add-output-binding-python](../../includes/functions-add-output-binding-python.md)]
::: zone-end  

::: zone pivot="programming-language-javascript"  
[!INCLUDE [functions-add-output-binding-js](../../includes/functions-add-output-binding-js.md)]
::: zone-end  

::: zone pivot="programming-language-typescript"  
[!INCLUDE [functions-add-output-binding-ts](../../includes/functions-add-output-binding-ts.md)]
::: zone-end  

::: zone pivot="programming-language-powershell"  
[!INCLUDE [functions-add-output-binding-powershell](../../includes/functions-add-output-binding-powershell.md)]  
::: zone-end

::: zone pivot="programming-language-csharp"  
[!INCLUDE [functions-add-storage-binding-csharp-library-code](../../includes/functions-add-storage-binding-csharp-library-code.md)]
::: zone-end 

::: zone pivot="programming-language-java"
[!INCLUDE [functions-add-output-binding-java-code](../../includes/functions-add-output-binding-java-code.md)]

[!INCLUDE [functions-add-output-binding-java-test-cli](../../includes/functions-add-output-binding-java-test-cli.md)]
::: zone-end

Kimlik doğrulaması, kuyruk başvurusu alma veya veri yazma için kod *yazmanıza gerek olmadığını* gözlemleyin. Tüm bu tümleştirme görevleri, Azure Işlevleri çalışma zamanı ve sıra çıkış bağlamasında kolayca işlenir.

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

[!INCLUDE [functions-extension-bundles-info](../../includes/functions-extension-bundles-info.md)]

## <a name="view-the-message-in-the-azure-storage-queue"></a>Azure depolama sırasındaki iletiyi görüntüleme

[!INCLUDE [functions-add-output-binding-view-queue-cli](../../includes/functions-add-output-binding-view-queue-cli.md)]

## <a name="redeploy-the-project-to-azure"></a>Projeyi Azure 'a yeniden dağıtın

İşlevin Azure depolama kuyruğuna bir ileti yazdığını doğruladığınıza göre, Azure üzerinde çalışan uç noktayı güncelleştirmek için projenizi yeniden dağıtabilirsiniz.

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell,programming-language-csharp" 
*Localfunctionsproj* klasöründe, [`func azure functionapp publish`](functions-run-local.md#project-file-deployment) uygulamayı yeniden dağıtmak için komutunu kullanın ve bunu `<APP_NAME>` uygulamanızın adıyla değiştirin.

```
func azure functionapp publish <APP_NAME>
```
::: zone-end  

::: zone pivot="programming-language-java" 

Yerel proje klasöründe, projenizi yeniden yayımlamak için aşağıdaki Maven komutunu kullanın:
```
mvn azure-functions:deploy
```
::: zone-end

## <a name="verify-in-azure"></a>Azure 'da doğrulama

1. Önceki hızlı başlangıçta olduğu gibi, yeniden dağıtılan işlevi sınamak için bir tarayıcı veya KıVRıMLı kullanın.

    # <a name="browser"></a>[Tarayıcı](#tab/browser)
    
    Yayımla komutunun çıktısında gösterilen tüm **ÇAĞıRMA URL** 'sini, sorgu parametresini ekleyerek bir tarayıcı adres çubuğuna kopyalayın `&name=Functions` . , İşlevi yerel olarak çalıştırdığınızda tarayıcı benzer bir çıktı görüntülemelidir.

    ![İşlevin çıktısı Azure üzerinde bir tarayıcıda çalışır](./media/functions-add-output-binding-storage-queue-cli/function-test-cloud-browser.png)

    # <a name="curl"></a>[curl](#tab/curl)
    
    [`curl`](https://curl.haxx.se/)Parametresini ekleyerek **Invoke URL 'si** ile çalıştırın `&name=Functions` . Komutun çıktısı, "Hello Functions" metni olmalıdır.
    
    ![İşlevin çıktısı, Azure 'da KıVRıMLı kullanılarak çalışır](./media/functions-add-output-binding-storage-queue-cli/function-test-cloud-curl.png)

    --- 

1. Depolama sırasını, önceki bölümde açıklandığı gibi bir kez inceleyerek sıraya yazılan yeni iletiyi içerdiğini doğrulayın.

## <a name="clean-up-resources"></a>Kaynakları temizleme

İşiniz bittiğinde, daha fazla maliyet ödemeden kaçınmak için kaynak grubunu ve içerdiği tüm kaynakları silmek için aşağıdaki komutu kullanın.

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```

## <a name="next-steps"></a>Sonraki adımlar

HTTP ile tetiklenen işlevinizi bir depolama kuyruğuna veri yazmak için güncelleştirdiniz. Artık çekirdek araçları ve Azure CLı kullanarak komut satırından Işlev geliştirme hakkında daha fazla bilgi edinebilirsiniz:

+ [Azure Functions Core Tools ile çalışma](functions-run-local.md)  

+ [Azure Işlevleri Tetikleyicileri ve bağlamaları](functions-triggers-bindings.md)

::: zone pivot="programming-language-csharp"  
+ [C# ' de tamamen işlev projelerinin örnekleri](/samples/browse/?products=azure-functions&languages=csharp).

+ [Azure Işlevleri C# Geliştirici Başvurusu](functions-dotnet-class-library.md)  

[previous-quickstart]: create-first-function-cli-csharp.md

::: zone-end 
::: zone pivot="programming-language-javascript"  
+ [JavaScript 'teki bütün işlev projelerinin örnekleri](/samples/browse/?products=azure-functions&languages=javascript).

+ [Azure Işlevleri JavaScript Geliştirici Kılavuzu](functions-reference-node.md)  

[previous-quickstart]: create-first-function-cli-javascript.md
::: zone-end  
::: zone pivot="programming-language-typescript"  
+ [TypeScript 'teki bütün işlev projelerinin örnekleri](/samples/browse/?products=azure-functions&languages=typescript).

+ [Azure Işlevleri TypeScript Geliştirici Kılavuzu](functions-reference-node.md#typescript)  

[previous-quickstart]: create-first-function-cli-typescript.md
::: zone-end  
::: zone pivot="programming-language-python"  
+ [Python 'da tüm işlev projelerinin örnekleri](/samples/browse/?products=azure-functions&languages=python).

+ [Azure Işlevleri Python Geliştirici Kılavuzu](functions-reference-python.md)  

[previous-quickstart]: create-first-function-cli-python.md
::: zone-end  
::: zone pivot="programming-language-powershell"  
+ [PowerShell 'de tüm işlev projelerinin örnekleri](/samples/browse/?products=azure-functions&languages=azurepowershell).

+ [Azure Işlevleri PowerShell Geliştirici Kılavuzu](functions-reference-powershell.md) 

[previous-quickstart]: create-first-function-cli-powershell.md
::: zone-end
