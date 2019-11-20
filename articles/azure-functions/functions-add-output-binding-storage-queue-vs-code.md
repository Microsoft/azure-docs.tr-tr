---
title: Visual Studio Code kullanarak işlevleri Azure depolama 'ya bağlama
description: Visual Studio Code kullanarak işlevlerinizi bir Azure depolama kuyruğuna bağlamak için çıkış bağlamayı nasıl ekleyeceğinizi öğrenin.
author: ggailey777
ms.author: glenga
ms.date: 06/25/2019
ms.topic: quickstart
ms.service: azure-functions
manager: gwallace
ms.openlocfilehash: 951e48e591f490ea6321329352fd798fea58855d
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72329695"
---
# <a name="connect-functions-to-azure-storage-using-visual-studio-code"></a>Visual Studio Code kullanarak işlevleri Azure depolama 'ya bağlama

[!INCLUDE [functions-add-storage-binding-intro](../../includes/functions-add-storage-binding-intro.md)]

Bu makalede, [önceki hızlı başlangıç makalesinde](functions-create-first-function-vs-code.md) oluşturduğunuz Işlevi Azure Storage 'a bağlamak için Visual Studio Code nasıl kullanılacağı gösterilmektedir. Bu işleve eklediğiniz çıkış bağlaması, HTTP isteğinden verileri bir Azure kuyruk depolama kuyruğundaki bir iletiye yazar. 

Çoğu bağlamanın, bağlı hizmete erişmek için kullandığı depolanan bir bağlantı dizesi gerekir. Daha kolay hale getirmek için, işlev uygulamanız ile oluşturduğunuz depolama hesabını kullanırsınız. Bu hesap bağlantısı zaten `AzureWebJobsStorage` adlı bir uygulama ayarında depolanıyor.  

## <a name="prerequisites"></a>Önkoşullar

Bu makaleye başlamadan önce, aşağıdaki gereksinimleri karşılamanız gerekir:

* [Visual Studio Code Için Azure Storage uzantısını](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage)yükler.
* [Azure Depolama Gezgini](https://storageexplorer.com/)'i yükler. Depolama Gezgini, çıkış bağlamalarınız tarafından oluşturulan sıra iletilerini incelemek için kullanacağınız bir araçtır. Depolama Gezgini, macOS, Windows ve Linux tabanlı işletim sistemlerinde desteklenir.
* [.NET Core CLI araçları](https://docs.microsoft.com/dotnet/core/tools/?tabs=netcore2x) 'nı (C# yalnızca projeler) yükler.
* [Visual Studio Code hızlı başlangıcı ' nın 1. bölümündeki](functions-create-first-function-vs-code.md)adımları uygulayın. 

Bu makalede, Visual Studio Code Azure aboneliğinizde zaten oturum açmış olduğunuz varsayılmaktadır. Komut paletinden `Azure: Sign In` çalıştırarak oturum açabilirsiniz. 

## <a name="download-the-function-app-settings"></a>İşlev uygulaması ayarlarını indirin

[Önceki hızlı başlangıç makalesinde](functions-create-first-function-vs-code.md), Azure 'Da gerekli depolama hesabıyla birlikte bir işlev uygulaması oluşturdunuz. Bu hesabın bağlantı dizesi, Azure 'daki uygulama ayarlarında güvenli bir şekilde depolanır. Bu makalede, aynı hesaptaki bir depolama kuyruğuna iletiler yazarsınız. İşlevi yerel olarak çalıştırırken depolama hesabınıza bağlanmak için, uygulama ayarlarını yerel. Settings. JSON dosyasına indirmeniz gerekir. 

1. F1 tuşuna basarak komut paletini açın, sonra `Azure Functions: Download Remote Settings....` komutunu arayıp çalıştırın. 

1. Önceki makalede oluşturduğunuz işlev uygulamasını seçin. Var olan yerel ayarların üzerine yazmak için **Tümüne Evet** ' i seçin. 

    > [!IMPORTANT]  
    > Gizli dizi içerdiğinden, Local. Settings. JSON dosyası hiçbir şekilde yayımlanmaz ve kaynak denetiminden çıkarılır.

1. Depolama hesabı bağlantı dizesi değeri için anahtar olan `AzureWebJobsStorage` değerini kopyalayın. Çıkış bağlamasının beklendiği gibi çalıştığını doğrulamak için bu bağlantıyı kullanın.

## <a name="register-binding-extensions"></a>Bağlama uzantılarını kaydetme

Kuyruk depolama çıkış bağlaması kullandığınızdan, projeyi çalıştırmadan önce depolama bağlamaları uzantısının yüklü olması gerekir. 

# <a name="javascripttabnodejs"></a>[JavaScript](#tab/nodejs)

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

# <a name="ctabcsharp"></a>[C\#](#tab/csharp)

HTTP ve Zamanlayıcı Tetikleyicileri dışında, bağlamalar uzantı paketleri olarak uygulanır. Depolama uzantısı paketini projenize eklemek için Terminal penceresinde aşağıdaki [DotNet paket Ekle](/dotnet/core/tools/dotnet-add-package) komutunu çalıştırın.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
```
---
Şimdi, depolama çıkış bağlamasını projenize ekleyebilirsiniz.

## <a name="add-an-output-binding"></a>Çıktı bağlaması ekleme

Işlevlerde, her bağlama türü, function. json dosyasında tanımlanacak bir `direction`, `type` ve benzersiz bir `name` gerektirir. Bu öznitelikleri tanımlama yöntemi, işlev uygulamanızın diline bağlıdır.

# <a name="javascripttabnodejs"></a>[JavaScript](#tab/nodejs)

[!INCLUDE [functions-add-output-binding-json](../../includes/functions-add-output-binding-json.md)]

# <a name="ctabcsharp"></a>[C\#](#tab/csharp)

[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]

---

## <a name="add-code-that-uses-the-output-binding"></a>Çıkış bağlaması kullanan kod ekleme

Bağlama tanımlandıktan sonra, işlev imzasında bir öznitelik olarak erişmek için bağlamanın `name` ' ı kullanabilirsiniz. Bir çıkış bağlaması kullanarak kimlik doğrulaması, kuyruk başvurusu alma veya veri yazma için Azure depolama SDK kodunu kullanmanız gerekmez. Işlevler çalışma zamanı ve kuyruk çıkış bağlaması bu görevleri sizin için işler.

# <a name="javascripttabnodejs"></a>[JavaScript](#tab/nodejs)

[!INCLUDE [functions-add-output-binding-js](../../includes/functions-add-output-binding-js.md)]

# <a name="ctabcsharp"></a>[C\#](#tab/csharp)

[!INCLUDE [functions-add-storage-binding-csharp-library-code](../../includes/functions-add-storage-binding-csharp-library-code.md)]

---

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

Çıkış bağlaması ilk kullanıldığında, depolama hesabınızda Işlevler çalışma zamanına göre **outqueue** adlı yeni bir kuyruk oluşturulur. Depolama Gezgini, sıranın yeni iletiyle birlikte oluşturulduğunu doğrulamak için kullanacaksınız.

### <a name="connect-storage-explorer-to-your-account"></a>Depolama Gezgini’ni hesabınıza bağlama

Azure Depolama Gezgini zaten yüklediyseniz ve Azure hesabınıza bağladıysanız bu bölümü atlayın.

1. [Azure Depolama Gezgini] aracını çalıştırın, sol taraftaki Bağlan simgesini seçin ve **Hesap Ekle**' yi seçin.

    ![Microsoft Azure Depolama Gezgini bir Azure hesabı ekleyin](./media/functions-add-output-binding-storage-queue-vs-code/storage-explorer-add-account.png)

1. **Bağlan** iletişim kutusunda, **Azure hesabı ekle**' yi seçin, **Azure ortamınızı**seçin ve oturum aç ' ı seçin. **..** 

    ![Azure hesabınızda oturum açma](./media/functions-add-output-binding-storage-queue-vs-code/storage-explorer-connect-azure-account.png)

Hesabınızda başarıyla oturum açtıktan sonra hesabınızla ilişkili tüm Azure aboneliklerini görürsünüz.

### <a name="examine-the-output-queue"></a>Çıkış kuyruğunu inceleme

1. Visual Studio Code ' de, F1 tuşuna basarak komut paletini açın, sonra `Azure Storage: Open in Storage Explorer` komutunu arayıp çalıştırın ve depolama hesabınızın adını seçin. Depolama Hesabınız Azure Depolama Gezgini açılır.  

1. **Kuyruklar** düğümünü genişletin ve sonra **outqueue** adlı kuyruğu seçin. 

   Kuyruk, HTTP ile tetiklenen işlevi çalıştırdığınızda kuyruk çıkış bağlamasının oluşturduğu iletiyi içerir. İşlevi varsayılan `name` *Azure* değeri ile çağırdıysanız, kuyruk iletisi *İşleve geçirilen ad: Azure* şeklinde olur.

    ![Azure Depolama Gezgini gösterilen kuyruk iletisi](./media/functions-add-output-binding-storage-queue-vs-code/function-queue-storage-output-view-queue.png)

1. İşlevi yeniden çalıştırın, başka bir istek gönderin ve kuyrukta yeni bir ileti göründüğünü görürsünüz.  

Şimdi, güncelleştirilmiş işlev uygulamasını Azure 'da yeniden yayımlamanız zaman atalım.

## <a name="redeploy-and-verify-the-updated-app"></a>Güncelleştirilmiş uygulamayı yeniden dağıtın ve doğrulayın

1. Visual Studio Code ' de, F1 tuşuna basarak komut paletini açın. Komut paletinde, `Azure Functions: Deploy to function app...` ' ı arayıp seçin.

1. İlk makalede oluşturduğunuz işlev uygulamasını seçin. Projenizi aynı uygulamaya yeniden dağıtmakta olduğunuzdan, dosyaların üzerine yazma uyarısını kapatmak için **Dağıt** ' ı seçin.

1. Dağıtım tamamlandıktan sonra yeniden dağıtılan işlevi test etmek için kıvrımlı veya bir tarayıcı kullanabilirsiniz. Daha önce olduğu gibi, aşağıdaki örnekte olduğu gibi `&name=<yourname>` sorgu dizesini URL 'ye ekleyin:

    ```bash
    curl https://myfunctionapp.azurewebsites.net/api/httptrigger?code=cCr8sAxfBiow548FBDLS1....&name=<yourname>
    ```

1. Çıktı bağlamasının sırada yeni bir ileti oluşturmadığını doğrulamak için, [depolama sırasındaki iletiyi yeniden görüntüleyin](#examine-the-output-queue) .

## <a name="clean-up-resources"></a>Kaynakları temizleme

Azure’da *Kaynaklar*; işlev uygulamalarını, işlevleri, depolama hesaplarını ve benzeri öğeleri ifade eder. *Kaynak grupları* halinde gruplandırılırlar ve bir grubu silerek içindeki her şeyi silebilirsiniz.

Bu hızlı başlangıçları tamamlamak için kaynaklar oluşturdunuz. [Hesap durumunuza](https://azure.microsoft.com/account/) ve [hizmet fiyatlandırmanıza](https://azure.microsoft.com/pricing/) bağlı olarak size bu kaynakların ücretleri yansıtılabilir. Kaynaklara artık ihtiyacınız yoksa, şunları yaparak silebilirsiniz:

1. Visual Studio Code ' de, F1 tuşuna basarak komut paletini açın. Komut paletinde, `Azure Functions: Open in portal` ' ı arayıp seçin.

1. İşlev uygulamanızı seçin ve ENTER tuşuna basın. İşlev uygulaması sayfası [Azure Portal](https://portal.azure.com)açılır.

1. **Genel bakış** sekmesinde **kaynak grubu**altında adlandırılan bağlantıyı seçin.

    ![İşlev uygulaması sayfasından silinecek kaynak grubunu seçin.](./media/functions-add-output-binding-storage-queue-vs-code/functions-app-delete-resource-group.png)

1. **Kaynak grubu** sayfasında, dahil edilen kaynakların listesini gözden geçirin ve silmek istediğiniz kaynakların bunlar olduğunu doğrulayın.
 
1. **Kaynak grubunu sil**’i seçin ve yönergeleri izleyin.

   Silme işlemi birkaç dakika sürebilir. İşlem tamamlandığında, birkaç saniye boyunca bir bildirim görüntülenir. Bildirimi görüntülemek için sayfanın üst kısmındaki zil simgesini de seçebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

HTTP ile tetiklenen işlevinizi bir depolama kuyruğuna veri yazmak için güncelleştirdiniz. Işlevleri geliştirme hakkında daha fazla bilgi için bkz. [Visual Studio Code kullanarak Azure Işlevleri geliştirme](functions-develop-vs-code.md).

Sonra, işlev uygulamanız için Application Insights izlemeyi etkinleştirmelisiniz:

> [!div class="nextstepaction"]
> [Application Insights tümleştirmesini etkinleştirme](functions-monitoring.md#manually-connect-an-app-insights-resource)

[Azure Depolama Gezgini]: https://storageexplorer.com/
