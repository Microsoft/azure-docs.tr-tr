---
title: Visual Studio kullanarak işlevleri Azure depolama 'ya bağlama
description: Sınıf kitaplığı işlevlerinizi C# Visual Studio 'yu kullanarak bir Azure depolama kuyruğuna bağlamak için çıkış bağlamayı nasıl ekleyeceğinizi öğrenin.
author: ggailey777
ms.author: glenga
ms.date: 07/22/2019
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc
manager: gwallace
ms.openlocfilehash: 383401c1486bcbebc39b64d5794f8bdc660d2778
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72329625"
---
# <a name="connect-functions-to-azure-storage-using-visual-studio"></a>Visual Studio kullanarak işlevleri Azure depolama 'ya bağlama

[!INCLUDE [functions-add-storage-binding-intro](../../includes/functions-add-storage-binding-intro.md)]

Bu makalede, Visual Studio 'Yu kullanarak [önceki hızlı başlangıç makalesinde] oluşturduğunuz Işlevi Azure depolama 'ya nasıl bağlayabilmeniz gösterilmektedir. Bu işleve eklediğiniz çıkış bağlaması, HTTP isteğinden verileri bir Azure kuyruk depolama kuyruğundaki bir iletiye yazar. 

Çoğu bağlamanın, bağlı hizmete erişmek için kullandığı depolanan bir bağlantı dizesi gerekir. Daha kolay hale getirmek için, işlev uygulamanız ile oluşturduğunuz depolama hesabını kullanırsınız. Bu hesap bağlantısı zaten `AzureWebJobsStorage` adlı bir uygulama ayarında depolanıyor.  

## <a name="prerequisites"></a>Önkoşullar

Bu makaleye başlamadan önce şunları yapmanız gerekir: 

 - Tamamlandı [Visual Studio hızlı başlangıçın 1. bölümü] [./Functions-Create-First-Function-vs-Code.exe]. 

- Visual Studio 'dan Azure aboneliğinizde oturum açın.

## <a name="download-the-function-app-settings"></a>İşlev uygulaması ayarlarını indirin

[Önceki hızlı başlangıç makalesinde](functions-create-first-function-vs-code.md), Azure 'Da gerekli depolama hesabıyla birlikte bir işlev uygulaması oluşturdunuz. Bu hesabın bağlantı dizesi, Azure 'daki uygulama ayarlarında güvenli bir şekilde depolanır. Bu makalede, aynı hesaptaki bir depolama kuyruğuna iletiler yazarsınız. İşlevi yerel olarak çalıştırırken depolama hesabınıza bağlanmak için, uygulama ayarlarını *yerel. Settings. JSON* dosyasına indirmeniz gerekir. 

1. **Çözüm Gezgini**'nde projeye sağ tıklayın ve **Yayımla**'yı seçin. 

1. **Eylemler**' in altında **Azure App Service Ayarları Düzenle**' yi seçin. 

    ![Uygulama ayarlarını düzenleme](media/functions-add-output-binding-storage-queue-vs/edit-app-settings.png)

1. **AzureWebJobsStorage**altında, **uzak** dize değerini **Yerel**olarak kopyalayın ve ardından **Tamam**' ı seçin. 

Bağlantı için `AzureWebJobsStorage` ayarını kullanan depolama bağlaması artık yerel olarak çalışırken kuyruk depolamaya bağlanabilir.

## <a name="register-binding-extensions"></a>Bağlama uzantılarını kaydetme

Kuyruk depolama çıkış bağlaması kullandığınızdan, projeyi çalıştırmadan önce depolama bağlamaları uzantısının yüklü olması gerekir. HTTP ve Zamanlayıcı Tetikleyicileri dışında, bağlamalar uzantı paketleri olarak uygulanır. 

1. **Araçlar** menüsünde **NuGet Paket Yöneticisi** > **Paket Yöneticisi konsolu**' nu seçin. 

1. Konsolunda, depolama uzantılarını yüklemek için aşağıdaki [Install-Package](/nuget/tools/ps-ref-install-package) komutunu çalıştırın:

    ```Command
    Install-Package Microsoft.Azure.WebJobs.Extensions.Storage -Version 3.0.6
    ````

Şimdi, depolama çıkış bağlamasını projenize ekleyebilirsiniz.

## <a name="add-an-output-binding"></a>Çıktı bağlaması ekleme

[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]

## <a name="add-code-that-uses-the-output-binding"></a>Çıkış bağlaması kullanan kod ekleme

Bağlama tanımlandıktan sonra, işlev imzasında bir öznitelik olarak erişmek için bağlamanın `name` ' ı kullanabilirsiniz. Bir çıkış bağlaması kullanarak kimlik doğrulaması, kuyruk başvurusu alma veya veri yazma için Azure depolama SDK kodunu kullanmanız gerekmez. Işlevler çalışma zamanı ve kuyruk çıkış bağlaması bu görevleri sizin için işler.

[!INCLUDE [functions-add-storage-binding-csharp-library-code](../../includes/functions-add-storage-binding-csharp-library-code.md)]

## <a name="run-the-function-locally"></a>İşlevi yerel olarak çalıştırma

[!INCLUDE [functions-run-function-test-local-vs](../../includes/functions-run-function-test-local-vs.md)]

Çıkış bağlama ilk kullanıldığında, Işlevler çalışma zamanı tarafından depolama hesabınızda `outqueue` adlı yeni bir kuyruk oluşturulur. Kuyruğun yeni iletiyle birlikte oluşturulduğunu doğrulamak için Cloud Explorer 'ı kullanacaksınız.

## <a name="examine-the-output-queue"></a>Çıkış kuyruğunu inceleme

1. **Görünüm** menüsünde, Visual Studio ' da **bulut Gezgini**' ni seçin.

1. **Cloud Explorer**'da Azure aboneliğinizi ve **depolama hesaplarınızı**genişletin, sonra işleviniz tarafından kullanılan depolama hesabını genişletin. Depolama hesabı adını hatırlayamıyorsanız, *Local. Settings. JSON* dosyasında `AzureWebJobsStorage` bağlantı dizesi ayarını kontrol edin.  

1. **Kuyruklar** düğümünü genişletin ve ardından Visual Studio 'da sıranın içeriğini görüntülemek için **outqueue** adlı kuyruğa çift tıklayın. 

   Kuyruk, HTTP ile tetiklenen işlevi çalıştırdığınızda kuyruk çıkış bağlamasının oluşturduğu iletiyi içerir. İşlevi varsayılan `name` *Azure* değeri ile çağırdıysanız, kuyruk iletisi *İşleve geçirilen ad: Azure* şeklinde olur.

    ![Azure Depolama Gezgini gösterilen kuyruk iletisi](./media/functions-add-output-binding-storage-queue-vs-code/function-queue-storage-output-view-queue.png)

1. İşlevi yeniden çalıştırın, başka bir istek gönderin ve kuyrukta yeni bir ileti göründüğünü görürsünüz.  

Şimdi, güncelleştirilmiş işlev uygulamasını Azure 'da yeniden yayımlamanız zaman atalım.

## <a name="redeploy-and-verify-the-updated-app"></a>Güncelleştirilmiş uygulamayı yeniden dağıtın ve doğrulayın

1. **Çözüm Gezgini**, projeye sağ tıklayın ve **Yayımla**' yı seçin ve ardından projeyi Azure 'Da yeniden yayımlamak için **Yayımla** ' yı seçin.

1. Dağıtım tamamlandıktan sonra yeniden dağıtılan işlevi test etmek için tarayıcıyı kullanabilirsiniz. Daha önce olduğu gibi, URL 'ye `&name=<yourname>` sorgu dizesini ekleyin.

1. Çıktı bağlamasının sırada yeni bir ileti oluşturmadığını doğrulamak için, [depolama sırasındaki iletiyi yeniden görüntüleyin](#examine-the-output-queue) .

## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Sonraki adımlar

HTTP ile tetiklenen işlevinizi bir depolama kuyruğuna veri yazmak için güncelleştirdiniz. Işlevleri geliştirme hakkında daha fazla bilgi için bkz. [Visual Studio kullanarak Azure Işlevleri geliştirme](functions-develop-vs.md).

Sonra, işlev uygulamanız için Application Insights izlemeyi etkinleştirmelisiniz:

> [!div class="nextstepaction"]
> [Application Insights tümleştirmesini etkinleştirme](functions-monitoring.md#manually-connect-an-app-insights-resource)

[Azure Storage Explorer]: https://storageexplorer.com/
[önceki hızlı başlangıç makalesinde]: functions-create-your-first-function-visual-studio.md
