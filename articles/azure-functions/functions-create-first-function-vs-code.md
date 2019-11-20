---
title: Visual Studio Code’u kullanarak Azure’da ilk işlevinizi oluşturma
description: Visual Studio Code’daki Azure İşlevleri uzantısını kullanarak basit bir HTTP ile tetiklenen işlev oluşturun ve Azure’da yayımlayın.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: azure işlevleri, işlevler, olay işleme, işlem, sunucusuz mimari
ms.service: azure-functions
ms.topic: quickstart
ms.date: 06/25/2019
ms.author: glenga
ms.custom: mvc, devcenter
ms.openlocfilehash: fbde3c939a23e4023c0403c27433717a11762b08
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73622051"
---
# <a name="create-your-first-function-using-visual-studio-code"></a>Visual Studio Code kullanarak ilk işlevinizi oluşturma

Azure İşlevleri, öncelikle bir VM oluşturmak veya bir web uygulaması yayımlamak zorunda kalmadan kodunuzu [sunucusuz](https://azure.microsoft.com/solutions/serverless/) bir ortamda yürütmenize olanak tanır.

Bu makalede Microsoft Visual Studio Code’u kullanarak yerel bilgisayarınızda bir “merhaba dünya” işlevi oluşturup test etmek üzere [Visual Studio Code için Azure İşlevleri uzantısı] kullanmayı öğreneceksiniz. Ardından işlev kodunu Visual Studio Code ile Azure’da yayımlayacaksınız.

![Visual Studio projesinde Azure İşlevleri kodu](./media/functions-create-first-function-vs-code/functions-vscode-intro.png)

Uzantı şu anda, C#JavaScript, Java ve Python işlevlerini desteklemektedir. Bu makaledeki adımlar ve aşağıdaki makale yalnızca JavaScript ve C# işlevleri destekler. Visual Studio Code kullanarak Python işlevleri oluşturma ve yayımlama hakkında bilgi edinmek için bkz. [Python 'da sunucusuz Azure Işlevleri oluşturma ve Visual Studio Code ile dağıtma](/azure/python/tutorial-vs-code-serverless-python-01). PowerShell işlevleri oluşturmak ve yayımlamak için Visual Studio Code kullanmayı öğrenmek için bkz. [Azure 'da Ilk PowerShell işlevinizi oluşturma](functions-create-first-function-powershell.md). 

Uzantı şu an önizleme aşamasındadır. Daha fazla bilgi edinmek için [Visual Studio Code için Azure İşlevleri uzantısı] sayfasına bakın.

## <a name="prerequisites"></a>Ön koşullar

Bu hızlı başlangıcı tamamlamak için:

* [Desteklenen platformlardan](https://code.visualstudio.com/) birinde [Visual Studio Code](https://code.visualstudio.com/docs/supporting/requirements#_platforms)’u yükleyin.

* [Azure Functions Core Tools](functions-run-local.md#v2)sürüm 2. x ' i yükler.

* Seçtiğiniz dile özgü gereksinimleri yükleyin:

    | Dil | Gereksinim |
    | -------- | --------- |
    | **C#** | [C#uzantının](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)  |
    | **JavaScript** | [Node. js](https://nodejs.org/) <sup>*</sup> | 
 
    <sup>*</sup> Etkin LTS ve bakım LTS sürümleri (8.11.1 ve 10.14.1 önerilir).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [functions-install-vs-code-extension](../../includes/functions-install-vs-code-extension.md)]

[!INCLUDE [functions-create-function-app-vs-code](../../includes/functions-create-function-app-vs-code.md)]

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

İşlevin yerel bilgisayarınızda düzgün çalıştığını doğruladıktan sonra, projeyi Azure'da yayımlamanın zamanı gelmiştir.

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../includes/functions-publish-project-vscode.md)]

## <a name="run-the-function-in-azure"></a>İşlevi Azure 'da çalıştırma

1. **Çıktı** panelinden HTTP tetikleyicisinin URL’sini kopyalayın. Bu URL, `code` sorgu parametresine geçirilen işlev anahtarını içerir. Daha önce olduğu gibi, `?name=<yourname>` sorgu dizesini bu URL’nin sonuna ekleyip isteği yürütmeyi unutmayın.

    HTTP tarafından tetiklenen işlevinizi çağıran URL aşağıdaki biçimde olmalıdır:

        http://<functionappname>.azurewebsites.net/api/<functionname>?code=<function_key>&name=<yourname> 

1. HTTP isteğinin yeni URL’sini tarayıcınızın adres çubuğuna yapıştırın. İşlevin döndürdüğü uzak GET isteğine tarayıcıda verilen yanıt aşağıda gösterilmiştir: 

    ![Tarayıcıdaki işlev yanıtı](./media/functions-create-first-function-vs-code/functions-test-remote-browser.png)

## <a name="next-steps"></a>Sonraki adımlar

HTTP ile tetiklenen basit bir işlevi kullanarak bir işlev uygulaması oluşturmak için Visual Studio Code’u kullandınız. Sonraki makalede, bir çıkış bağlaması ekleyerek bu işlevi genişletmelisiniz. Bu bağlama, HTTP isteğinden dizeyi bir Azure kuyruk depolama kuyruğundaki bir iletiye yazar. Sonraki makalede ayrıca, oluşturduğunuz kaynak grubunu kaldırarak bu yeni Azure kaynaklarını temizleme yöntemi gösterilmektedir.

> [!div class="nextstepaction"]
> [İşleviniz için bir Azure depolama kuyruğu bağlama ekleme](functions-add-output-binding-storage-queue-vs-code.md)

[Azure Functions Core Tools]: functions-run-local.md
[Visual Studio Code için Azure İşlevleri uzantısı]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
