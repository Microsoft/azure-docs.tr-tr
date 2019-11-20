---
title: Visual Studio’yu kullanarak Azure’da ilk işlevinizi oluşturma
description: Visual Studio kullanarak HTTP tarafından tetiklenen bir Azure İşlevi oluşturup yayımlayın.
author: ggailey777
manager: gwallace
keywords: azure işlevleri, işlevler, olay işleme, işlem, sunucusuz mimari
ms.assetid: 82db1177-2295-4e39-bd42-763f6082e796
ms.service: azure-functions
ms.topic: quickstart
ms.date: 07/19/2019
ms.author: glenga
ms.custom: mvc, devcenter, vs-azure, 23113853-34f2-4f
ms.openlocfilehash: 1d798be3a0cde39de208105c4b3d4458fa5b124c
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72329502"
---
# <a name="create-your-first-function-using-visual-studio"></a>Visual Studio’yu kullanarak ilk işlevinizi oluşturma

Azure İşlevleri, öncelikle bir VM oluşturmak veya bir web uygulaması yayımlamak zorunda kalmadan kodunuzu [sunucusuz](https://azure.microsoft.com/solutions/serverless/) bir ortamda yürütmenize olanak tanır.

Bu makalede, "Hello World" işlevini yerel olarak oluşturmak ve test etmek ve sonra Azure 'Da yayımlamak için Visual Studio 2019 ' i nasıl kullanacağınızı öğreneceksiniz. Bu hızlı başlangıç, Visual Studio 2019 için tasarlanmıştır. Visual Studio 2017 kullanarak bir Işlevler projesi oluştururken, önce [en son Azure işlevleri araçlarını](functions-develop-vs.md#check-your-tools-version)yüklemeniz gerekir.

![Tarayıcıdaki işlev localhost yanıtı](./media/functions-create-your-first-function-visual-studio/functions-create-your-first-function-visual-studio-browser-local-final.png)

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlayabilmeniz için öncelikle [Visual Studio 2019](https://azure.microsoft.com/downloads/)' i yüklemeniz gerekir. **Azure geliştirme** iş yükünün de yüklü olduğundan emin olun.

![Azure geliştirme iş yüküyle Visual Studio 'Yu yükler](media/functions-create-your-first-function-visual-studio/functions-vs-workloads.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-function-app-project"></a>İşlev uygulaması projesi oluşturma

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

Visual Studio, HTTP tetikleyici işlev türü için ortak kod içeren bir proje ve sınıf oluşturur. Yöntemdeki `FunctionName` özniteliği, varsayılan olarak `HttpTrigger` olan işlevin adını ayarlar. @No__t-0 özniteliği, işlevin bir HTTP isteği tarafından tetiklendiğini belirtir. Ortak kod, istek gövdesi veya sorgu dizesinde yer alan bir değeri içeren bir HTTP yanıtı gönderir.

Yöntemine uygun öznitelikleri uygulayarak giriş ve çıkış bağlamalarını kullanarak işlevinizin yeteneklerini genişletebilirsiniz. Daha fazla bilgi için, [Azure İşlevleri C# geliştirici başvurusu](functions-dotnet-class-library.md) konusunun [Tetikleyiciler ve bağlayıcılar](functions-dotnet-class-library.md#triggers-and-bindings) bölümüne göz atın.

İşlev projenizi ve HTTP ile tetiklenen işlev oluşturduğunuza göre, artık bunu yerel bilgisayarınızda test edebilirsiniz.

## <a name="run-the-function-locally"></a>İşlevi yerel olarak çalıştırma

Visual Studio, tüm Işlevler çalışma zamanını kullanarak işlevlerinizi yerel olarak test edebilmeniz için Azure Functions Core Tools ile tümleşir.  

[!INCLUDE [functions-run-function-test-local-vs](../../includes/functions-run-function-test-local-vs.md)]

İşlevin yerel bilgisayarınızda düzgün çalıştığını doğruladıktan sonra, projeyi Azure'da yayımlamanın zamanı gelmiştir.

## <a name="publish-the-project-to-azure"></a>Projeyi Azure'da yayımlama

Projenizi yayımlayabilmeniz için önce Azure aboneliğinizde bir işlev uygulamanızın olması gerekir. Visual Studio yayımlama, projenizi ilk kez yayımladığınızda sizin için bir işlev uygulaması oluşturur.

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

## <a name="test-your-function-in-azure"></a>Azure'da işlevinizi test etme

1. Yayımlama profili sayfasından işlev uygulamasının temel URL'sini kopyalayın. İşlevi yerel olarak test ederken kullandığınız URL’nin `localhost:port` kısmını, yeni temel URL ile değiştirin. Daha önce olduğu gibi, `?name=<YOUR_NAME>` sorgu dizesini bu URL’ye ekleyip isteği yürütmeyi unutmayın.

    HTTP tarafından tetiklenen işlevinizi çağıran URL aşağıdaki biçimde olmalıdır:

        http://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?name=<YOUR_NAME> 

2. HTTP isteğinin yeni URL’sini tarayıcınızın adres çubuğuna yapıştırın. İşlevin döndürdüğü uzak GET isteğine tarayıcıda verilen yanıt aşağıda gösterilmiştir:

    ![Tarayıcıdaki işlev yanıtı](./media/functions-create-your-first-function-visual-studio/functions-create-your-first-function-visual-studio-browser-azure.png)

## <a name="next-steps"></a>Sonraki adımlar

Azure 'da basit bir HTTP tetiklemeli işlevle bir C# işlev uygulaması oluşturmak ve yayımlamak Için Visual Studio 'yu kullandınız. .NET sınıf kitaplıkları olarak işlevleri geliştirme hakkında daha fazla bilgi için bkz. [Azure C# işlevleri geliştirici başvurusu](functions-dotnet-class-library.md).

> [!div class="nextstepaction"]
> [İşleviniz için bir Azure depolama kuyruğu bağlama ekleme](functions-add-output-binding-storage-queue-vs.md)
