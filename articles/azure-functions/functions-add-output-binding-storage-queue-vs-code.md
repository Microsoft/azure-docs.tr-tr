---
title: Azure Işlevlerini Visual Studio Code kullanarak Azure depolama 'ya bağlama
description: Visual Studio Code projenize bir çıkış bağlaması ekleyerek Azure Işlevlerini bir Azure depolama kuyruğuna bağlamayı öğrenin.
ms.date: 02/07/2020
ms.topic: quickstart
ms.custom: devx-track-python, devx-track-js
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: 96384d2c50e7d5b4b5b6e652d01c4a89cd519573
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99493417"
---
# <a name="connect-azure-functions-to-azure-storage-using-visual-studio-code"></a>Azure Işlevlerini Visual Studio Code kullanarak Azure depolama 'ya bağlama

[!INCLUDE [functions-add-storage-binding-intro](../../includes/functions-add-storage-binding-intro.md)]

Bu makalede, Azure Storage 'ı önceki hızlı başlangıç makalesinde oluşturduğunuz işleve bağlamak için Visual Studio Code nasıl kullanılacağı gösterilmektedir. Bu işleve eklediğiniz çıkış bağlaması, HTTP isteğinden verileri bir Azure kuyruk depolama kuyruğundaki bir iletiye yazar. 

Çoğu bağlamanın, bağlı hizmete erişmek için kullandığı depolanan bir bağlantı dizesi gerekir. Daha kolay hale getirmek için, işlev uygulamanız ile oluşturduğunuz depolama hesabını kullanırsınız. Bu hesap bağlantısı zaten adlı bir uygulama ayarında depolanıyor `AzureWebJobsStorage` .  

## <a name="configure-your-local-environment"></a>Yerel ortamınızı yapılandırma

Bu makaleye başlamadan önce, aşağıdaki gereksinimleri karşılamanız gerekir:

* [Visual Studio Code Için Azure Storage uzantısını](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage)yükler.

* [Azure Depolama Gezgini](https://storageexplorer.com/)'i yükler. Depolama Gezgini, çıkış bağlamalarınız tarafından oluşturulan sıra iletilerini incelemek için kullanacağınız bir araçtır. Depolama Gezgini, macOS, Windows ve Linux tabanlı işletim sistemlerinde desteklenir.

::: zone pivot="programming-language-csharp"
* [.NET Core CLI araçları](/dotnet/core/tools/?tabs=netcore2x)'nı yükler.
::: zone-end

::: zone pivot="programming-language-csharp"  
* [Visual Studio Code hızlı başlangıcı ' nın 1. bölümündeki](create-first-function-vs-code-csharp.md)adımları uygulayın. 
::: zone-end  
::: zone pivot="programming-language-javascript"  
* [Visual Studio Code hızlı başlangıcı ' nın 1. bölümündeki](create-first-function-vs-code-node.md)adımları uygulayın. 
::: zone-end   
::: zone pivot="programming-language-java"  
* [Visual Studio Code hızlı başlangıcı ' nın 1. bölümündeki](create-first-function-vs-code-java.md)adımları uygulayın. 
::: zone-end   
::: zone pivot="programming-language-typescript"  
* [Visual Studio Code hızlı başlangıcı ' nın 1. bölümündeki](create-first-function-vs-code-typescript.md)adımları uygulayın. 
::: zone-end   
::: zone pivot="programming-language-python"  
* [Visual Studio Code hızlı başlangıcı ' nın 1. bölümündeki](create-first-function-vs-code-python.md)adımları uygulayın. 
::: zone-end   
::: zone pivot="programming-language-powershell"  
* [Visual Studio Code hızlı başlangıcı ' nın 1. bölümündeki](create-first-function-vs-code-powershell.md)adımları uygulayın. 
::: zone-end   

Bu makalede, Visual Studio Code Azure aboneliğinizde zaten oturum açmış olduğunuz varsayılmaktadır. Komut paletinden komutunu çalıştırarak oturum açabilirsiniz `Azure: Sign In` . 

## <a name="download-the-function-app-settings"></a>İşlev uygulaması ayarlarını indirin

[Önceki hızlı başlangıç makalesinde](./create-first-function-vs-code-csharp.md), Azure 'Da gerekli depolama hesabıyla birlikte bir işlev uygulaması oluşturdunuz. Bu hesabın bağlantı dizesi, Azure 'daki uygulama ayarlarında güvenli bir şekilde depolanır. Bu makalede, aynı hesaptaki bir depolama kuyruğuna iletiler yazarsınız. İşlevi yerel olarak çalıştırırken depolama hesabınıza bağlanmak için, uygulama ayarlarını dosyaya local.settings.jsindirmeniz gerekir. 

1. F1 tuşuna basarak komut paletini açın, sonra komutunu arayıp çalıştırın `Azure Functions: Download Remote Settings....` . 

1. Önceki makalede oluşturduğunuz işlev uygulamasını seçin. Var olan yerel ayarların üzerine yazmak için **Tümüne Evet** ' i seçin. 

    > [!IMPORTANT]  
    > Gizli dizi içerdiğinden, dosyadaki local.settings.jshiçbir şekilde yayımlanmaz ve kaynak denetiminden çıkarılır.

1. `AzureWebJobsStorage`Depolama hesabı bağlantı dizesi değeri için anahtar olan değeri kopyalayın. Çıkış bağlamasının beklendiği gibi çalıştığını doğrulamak için bu bağlantıyı kullanın.

## <a name="register-binding-extensions"></a>Bağlama uzantılarını kaydetme

Kuyruk depolama çıkış bağlaması kullandığınızdan, projeyi çalıştırmadan önce depolama bağlamaları uzantısının yüklü olması gerekir. 

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell,programming-language-java"

Projeniz, önceden tanımlanmış bir uzantı paketleri kümesini otomatik olarak yüklediği [uzantı](functions-bindings-register.md#extension-bundles)paketlerini kullanmak üzere yapılandırılmıştır. 

Uzantı demeti kullanımı, projenin kökündeki host.jsaşağıdaki gibi görünen dosya üzerinde etkinleştirilir:

:::code language="json" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/host.json":::

::: zone-end

::: zone pivot="programming-language-csharp"

HTTP ve Zamanlayıcı Tetikleyicileri dışında, bağlamalar uzantı paketleri olarak uygulanır. Depolama uzantısı paketini projenize eklemek için Terminal penceresinde aşağıdaki [DotNet paket Ekle](/dotnet/core/tools/dotnet-add-package) komutunu çalıştırın.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage
```

::: zone-end

Şimdi, depolama çıkış bağlamasını projenize ekleyebilirsiniz.

## <a name="add-an-output-binding"></a>Çıktı bağlaması ekleme

Işlevlerde, her bağlama türü `direction` , `type` `name` dosyasında function.jstanımlanması için bir, ve benzersiz gerektirir. Bu öznitelikleri tanımlama yöntemi, işlev uygulamanızın diline bağlıdır.

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell"

[!INCLUDE [functions-add-output-binding-json](../../includes/functions-add-output-binding-json.md)]

::: zone-end

::: zone pivot="programming-language-csharp"

[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]

::: zone-end

::: zone pivot="programming-language-java"

[!INCLUDE [functions-add-output-binding-java](../../includes/functions-add-output-binding-java.md)]

::: zone-end

## <a name="add-code-that-uses-the-output-binding"></a>Çıkış bağlaması kullanan kod ekleme

Bağlama tanımlandıktan sonra, `name` işlev imzasında bir öznitelik olarak erişmek için bağlamayı kullanabilirsiniz. Bir çıkış bağlaması kullanarak kimlik doğrulaması, kuyruk başvurusu alma veya veri yazma için Azure depolama SDK kodunu kullanmanız gerekmez. Işlevler çalışma zamanı ve kuyruk çıkış bağlaması bu görevleri sizin için işler.

::: zone pivot="programming-language-javascript"  
[!INCLUDE [functions-add-output-binding-js](../../includes/functions-add-output-binding-js.md)]
::: zone-end  

::: zone pivot="programming-language-typescript"  
[!INCLUDE [functions-add-output-binding-ts](../../includes/functions-add-output-binding-ts.md)]
::: zone-end  

::: zone pivot="programming-language-powershell"

[!INCLUDE [functions-add-output-binding-powershell](../../includes/functions-add-output-binding-powershell.md)]

::: zone-end

::: zone pivot="programming-language-python"

[!INCLUDE [functions-add-output-binding-python](../../includes/functions-add-output-binding-python.md)]

::: zone-end

::: zone pivot="programming-language-csharp"  

[!INCLUDE [functions-add-storage-binding-csharp-library-code](../../includes/functions-add-storage-binding-csharp-library-code.md)]

::: zone-end  

::: zone pivot="programming-language-java"  

[!INCLUDE [functions-add-storage-binding-java-code](../../includes/functions-add-storage-binding-java-code.md)]

## <a name="update-the-tests"></a>Testleri güncelleştirme

[!INCLUDE [functions-add-output-binding-java-test](../../includes/functions-add-output-binding-java-test.md)]

::: zone-end  

## <a name="run-the-function-locally"></a>İşlevi yerel olarak çalıştırma

1. Önceki makalede olduğu gibi, uygulama projesi ve çekirdek araçları işlevine başlamak için <kbd>F5</kbd> ' e basın. 

1. Temel araçlar çalışırken **Azure: Functions** alanına gidin. **İşlevler** altında **Yerel proje**  >  **işlevleri**' ni genişletin. İşlevi sağ tıklatın (Mac üzerinde CTRL + tıklama) `HttpExample` ve **Şimdi işlevi Çalıştır...** seçeneğini belirleyin.

    :::image type="content" source="../../includes/media/functions-run-function-test-local-vs-code/execute-function-now.png" alt-text="İşlevi şimdi Visual Studio Code Çalıştır":::

1. **İstek gövdesi gir** bölümünde istek iletisi gövdesi değerini görürsünüz `{ "name": "Azure" }` . Bu istek iletisini işlevinizde göndermek için ENTER tuşuna basın.  
 
1. Bir yanıt döndürüldüğünde, temel araçları durdurmak için <kbd>CTRL + C</kbd> tuşlarına basın.

Depolama bağlantı dizesi kullandığınızdan, işleviniz yerel olarak çalışırken Azure Storage hesabına bağlanır. Çıkış bağlaması ilk kullanıldığında, depolama hesabınızda Işlevler çalışma zamanına göre **outqueue** adlı yeni bir kuyruk oluşturulur. Depolama Gezgini, sıranın yeni iletiyle birlikte oluşturulduğunu doğrulamak için kullanacaksınız.

### <a name="connect-storage-explorer-to-your-account"></a>Depolama Gezgini’ni hesabınıza bağlama

Azure Depolama Gezgini zaten yüklediyseniz ve Azure hesabınıza bağladıysanız bu bölümü atlayın.

1. [Azure Depolama Gezgini] aracını çalıştırın, sol taraftaki Bağlan simgesini seçin ve **Hesap Ekle**' yi seçin.

    ![Microsoft Azure Depolama Gezgini bir Azure hesabı ekleyin](./media/functions-add-output-binding-storage-queue-vs-code/storage-explorer-add-account.png)

1. **Bağlan** iletişim kutusunda, **Azure hesabı ekle**' yi seçin, **Azure ortamınızı** seçin ve oturum aç ' ı seçin. **..** 

    ![Azure hesabınızda oturum açma](./media/functions-add-output-binding-storage-queue-vs-code/storage-explorer-connect-azure-account.png)

Hesabınızda başarıyla oturum açtıktan sonra hesabınızla ilişkili tüm Azure aboneliklerini görürsünüz.

### <a name="examine-the-output-queue"></a>Çıkış kuyruğunu inceleme

1. Visual Studio Code ' de, F1 tuşuna basarak komut paletini açın, sonra komutu arayıp çalıştırın `Azure Storage: Open in Storage Explorer` ve depolama hesabınızın adını seçin. Depolama Hesabınız Azure Depolama Gezgini açılır.  

1. **Kuyruklar** düğümünü genişletin ve sonra **outqueue** adlı kuyruğu seçin. 

   Kuyruk, HTTP ile tetiklenen işlevi çalıştırdığınızda kuyruk çıkış bağlamasının oluşturduğu iletiyi içerir. İşlevi varsayılan `name`*Azure* değeri ile çağırdıysanız, kuyruk iletisi *İşleve geçirilen ad: Azure* şeklinde olur.

    ![Azure Depolama Gezgini gösterilen kuyruk iletisi](./media/functions-add-output-binding-storage-queue-vs-code/function-queue-storage-output-view-queue.png)

1. İşlevi yeniden çalıştırın, başka bir istek gönderin ve kuyrukta yeni bir ileti göründüğünü görürsünüz.  

Şimdi, güncelleştirilmiş işlev uygulamasını Azure 'da yeniden yayımlamanız zaman atalım.

## <a name="redeploy-and-verify-the-updated-app"></a>Güncelleştirilmiş uygulamayı yeniden dağıtın ve doğrulayın

1. Visual Studio Code ' de, F1 tuşuna basarak komut paletini açın. Komut paletinde, araması yapın ve seçin `Azure Functions: Deploy to function app...` .

1. İlk makalede oluşturduğunuz işlev uygulamasını seçin. Projenizi aynı uygulamaya yeniden **dağıtmakta** olduğunuzdan, dosyaların üzerine yazma uyarısını kapatmak için Dağıt ' ı seçin.

1. Dağıtım tamamlandıktan sonra, işlevi Azure 'da tetiklemek için **Şimdi Çalıştır...** özelliğini kullanabilirsiniz.

1. Çıktı bağlamasının sırada yeni bir ileti oluşturmadığını doğrulamak için, [depolama sırasındaki iletiyi yeniden görüntüleyin](#examine-the-output-queue) .

## <a name="clean-up-resources"></a>Kaynakları temizleme

Azure 'da, *kaynaklar* işlev uygulamalarına, işlevlere, depolama hesaplarına ve benzeri bir kaynağa başvurur. Bunlar *kaynak grupları* halinde gruplandırılır ve grubu silerek bir gruptaki her şeyi silebilirsiniz.

Bu hızlı başlangıçları tamamlamak için kaynaklar oluşturdunuz. [Hesap durumunuza](https://azure.microsoft.com/account/) ve [hizmet fiyatlandırmanıza](https://azure.microsoft.com/pricing/) bağlı olarak size bu kaynakların ücretleri yansıtılabilir. Kaynaklara artık ihtiyacınız yoksa, şunları yaparak silebilirsiniz:

[!INCLUDE [functions-cleanup-resources-vs-code-inner.md](../../includes/functions-cleanup-resources-vs-code-inner.md)]

## <a name="next-steps"></a>Sonraki adımlar

HTTP ile tetiklenen işlevinizi bir depolama kuyruğuna veri yazmak için güncelleştirdiniz. Artık Visual Studio Code kullanarak Işlevleri geliştirme hakkında daha fazla bilgi edinebilirsiniz:

+ [Visual Studio Code kullanarak Azure Işlevleri geliştirme](functions-develop-vs-code.md)

+ [Azure işlevleri Tetikleyicileri ve bağlamaları](functions-triggers-bindings.md).
::: zone pivot="programming-language-csharp"  
+ [C# ' de tamamen işlev projelerinin örnekleri](/samples/browse/?products=azure-functions&languages=csharp).

+ [Azure Işlevleri C# Geliştirici Başvurusu](functions-dotnet-class-library.md)  
::: zone-end 
::: zone pivot="programming-language-javascript"  
+ [JavaScript 'teki bütün işlev projelerinin örnekleri](/samples/browse/?products=azure-functions&languages=javascript).

+ [Azure Işlevleri JavaScript Geliştirici Kılavuzu](functions-reference-node.md)  
::: zone-end  
::: zone pivot="programming-language-java"  
+ [Java 'daki bütün işlev projelerinin örnekleri](/samples/browse/?products=azure-functions&languages=java).

+ [Azure Işlevleri Java geliştirici kılavuzu](functions-reference-java.md)  
::: zone-end  
::: zone pivot="programming-language-typescript"  
+ [TypeScript 'teki bütün işlev projelerinin örnekleri](/samples/browse/?products=azure-functions&languages=typescript).

+ [Azure Işlevleri TypeScript Geliştirici Kılavuzu](functions-reference-node.md#typescript)  
::: zone-end  
::: zone pivot="programming-language-python"  
+ [Python 'da tüm işlev projelerinin örnekleri](/samples/browse/?products=azure-functions&languages=python).

+ [Azure Işlevleri Python Geliştirici Kılavuzu](functions-reference-python.md)  
::: zone-end  
::: zone pivot="programming-language-powershell"  
+ [PowerShell 'de tüm işlev projelerinin örnekleri](/samples/browse/?products=azure-functions&languages=azurepowershell).

+ [Azure Işlevleri PowerShell Geliştirici Kılavuzu](functions-reference-powershell.md) 
::: zone-end