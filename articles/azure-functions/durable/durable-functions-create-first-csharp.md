---
title: Kullanarak Azure 'da ilk dayanıklı işlevinizi oluşturmaC#
description: Visual Studio kullanarak bir Azure dayanıklı Işlevi oluşturun ve yayımlayın.
services: functions
documentationcenter: na
author: jeffhollan
manager: jeconnoc
keywords: azure işlevleri, işlevler, olay işleme, işlem, sunucusuz mimari
ms.service: azure-functions
ms.topic: quickstart
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 563412fbc5e8d9af3c399b1f75696053549143c4
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73615002"
---
# <a name="create-your-first-durable-function-in-c"></a>C\# ilk dayanıklı işlevinizi oluşturma

*Dayanıklı işlevler* , bir sunucusuz ortamda durum bilgisi olan işlevler yazmanıza olanak sağlayan bir [Azure işlevleri](../functions-overview.md) uzantısıdır. Uzantı sizin için durumu, denetim noktalarını ve yeniden başlatmaları yönetir.

[!INCLUDE [v1-note](../../../includes/functions-durable-v1-tutorial-note.md)]

Bu makalede, "Hello World" dayanıklı işlevini yerel olarak oluşturmak ve test etmek için Visual Studio 2019 ' i nasıl kullanacağınızı öğreneceksiniz.  Bu işlev, diğer işlevlere birlikte yapılan çağrıları düzenler ve zincirler. Ardından işlev kodunu Azure’da yayımlayacaksınız. Bu araçlar, Visual Studio 2019 ' de Azure geliştirme iş yükünün parçası olarak kullanılabilir.

![Azure 'da dayanıklı işlevi çalıştırma](./media/durable-functions-create-first-csharp/functions-vs-complete.png)

## <a name="prerequisites"></a>Ön koşullar

Bu öğreticiyi tamamlamak için:

* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/)' i yükler. **Azure geliştirme** iş yükünün de yüklü olduğundan emin olun. Visual Studio 2017 Ayrıca Dayanıklı İşlevler geliştirmeyi destekler, ancak UI ve adımlar farklılık gösterir.

* [Azure Storage öykünücüsü](../../storage/common/storage-use-emulator.md) 'nin yüklendiğini ve çalıştığını doğrulayın.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-function-app-project"></a>İşlev uygulaması projesi oluşturma

Azure Işlevleri şablonu, Azure 'da bir işlev uygulamasına yayımlanmakta olabilecek bir proje oluşturur. İşlev uygulaması, kaynakların yönetilmesi, dağıtılması ve paylaşılması için işlevleri bir mantıksal birim olarak gruplandırmanıza olanak tanır.

1. Visual Studio'da **Dosya** menüsünden  > Yeni**Proje**’yi seçin.

1. **Yeni Proje Ekle** iletişim kutusunda `functions`arayın, **Azure işlevleri** şablonunu seçin ve **İleri**' yi seçin. 

    ![Visual Studio'da işlev oluşturmaya yönelik yeni proje iletişim kutusu](./media/durable-functions-create-first-csharp/functions-vs-new-project.png)

1. Projeniz için bir **Proje adı** yazın ve **Tamam**' ı seçin. Proje adı bir C# ad alanı olarak geçerli olmalıdır, bu nedenle alt çizgi, kısa çizgi veya alfasayısal olmayan karakterler kullanmayın.

1. **Yeni bir Azure Işlevleri uygulaması oluştur**bölümünde, görüntüyü izleyen tabloda belirtilen ayarları kullanın.

    ![Visual Studio 'da yeni bir Azure Işlevleri uygulama iletişim kutusu oluşturma](./media/durable-functions-create-first-csharp/functions-vs-new-function.png)

    | Ayar      | Önerilen değer  | Açıklama                      |
    | ------------ |  ------- |----------------------------------------- |
    | **Sürüm** | Azure Işlevleri 2,0 <br />(.NET Core) | .NET Core 'u destekleyen Azure Işlevlerinin sürüm 2,0 çalışma zamanını kullanan bir işlev projesi oluşturur. Azure Işlevleri 1,0 .NET Framework destekler. Daha fazla bilgi için bkz. [Azure İşlevleri çalışma zamanı sürümünün hedefini belirleme](../functions-versions.md).   |
    | **Şablon** | Olmamalıdır | Boş bir işlev uygulaması oluşturur. |
    | **Depolama hesabı**  | Depolama Öykünücüsü | Dayanıklı işlev durumu yönetimi için bir depolama hesabı gereklidir. |

4. Boş bir işlev projesi oluşturmak için **Oluştur** ' u seçin. Bu proje, işlevlerinizi çalıştırmak için gereken temel yapılandırma dosyalarını içerir.

## <a name="add-functions-to-the-app"></a>Uygulamaya işlevler ekleyin

Aşağıdaki adımlar, projenizde dayanıklı işlev kodu oluşturmak için bir şablon kullanır.

1. Visual Studio 'da projeye sağ tıklayın ve > **yeni Azure Işlevi** **Ekle** ' yi seçin.

    ![Yeni işlev Ekle](./media/durable-functions-create-first-csharp/functions-vs-add-new-function.png)

1. Ekle menüsünde **Azure işlevinin** seçili olduğunu doğrulayın, C# dosyanız için bir ad yazın ve ardından **Ekle**' yi seçin.

1. **Dayanıklı işlevler Orchestration** şablonunu seçin ve ardından **Tamam** ' ı seçin.

    ![Dayanıklı şablon seçin](./media/durable-functions-create-first-csharp/functions-vs-select-template.png)  

> [!NOTE]
> Bu şablon şu anda uzantının eski bir 1. x sürümünü kullanarak dayanıklı bir işlev oluşturuyor. Daha yeni bir Dayanıklı İşlevler 2. x sürümüne yükseltme hakkında bilgi için [dayanıklı işlevler sürümler](durable-functions-versions.md) makalesine bakın.

Uygulamaya yeni bir dayanıklı işlev eklenir.  İçeriğini görüntülemek için yeni. cs dosyasını açın. Bu dayanıklı işlev, aşağıdaki yöntemlerle basit bir işlev zincirleme örneğidir:  

| Yöntem | Ifadelerini | Açıklama |
| -----  | ------------ | ----------- |
| **`RunOrchestrator`** | `<file-name>` | Dayanıklı düzenleme 'yi yönetir. Bu durumda, düzenleme başlar, bir liste oluşturur ve listeye yapılan üç işlev çağrısının sonucunu ekler.  Üç işlev çağrısı tamamlandığında, listeyi döndürür. |
| **`SayHello`** | `<file-name>_Hello` | İşlev bir merhaba döndürür. Düzenleyen iş mantığını içeren işlevdir. |
| **`HttpStart`** | `<file-name>_HttpStart` | Orchestration örneğini başlatan ve bir denetim durumu yanıtı döndüren [http ile tetiklenen bir işlev](../functions-bindings-http-webhook.md) . |

Artık işlev projenizi ve dayanıklı bir işlevi oluşturduğunuza göre, bunu yerel bilgisayarınızda test edebilirsiniz.

## <a name="test-the-function-locally"></a>İşlevi yerel olarak test etme

Azure İşlevleri Temel Araçları, Azure İşlevleri projenizi yerel geliştirme bilgisayarınızda çalıştırmanıza olanak sağlar. Visual Studio'da ilk kez bir işlev başlattığınızda bu araçları yüklemeniz istenir.

1. İşlevinizi test etmek için F5’e basın. İstenirse Visual Studio'dan gelen Azure İşlevleri Temel (CLI) araçlarını indirme ve yükleme isteğini kabul edin. Aracın HTTP isteklerini işleyebilmesi için bir güvenlik duvarı özel durumu etkinleştirmeniz de gerekebilir.

2. Azure İşlevleri çalışma zamanı çıktısından işlevinizin URL'sini kopyalayın.

    ![Azure yerel çalışma zamanı](./media/durable-functions-create-first-csharp/functions-vs-debugging.png)

3. HTTP isteğinin URL 'sini tarayıcınızın adres çubuğuna yapıştırın ve isteği yürütün. İşlevin döndürdüğü yerel GET isteğine tarayıcıda verilen yanıt aşağıda gösterilmiştir:

    ![Tarayıcıdaki işlev localhost yanıtı](./media/durable-functions-create-first-csharp/functions-vs-status.png)

    Yanıt, HTTP işlevinin ilk sonucudur ve dayanıklı düzenleme 'nin başarıyla başlatıldığını öğrenmemize olanak tanıyor.  Orchestration 'un nihai sonucu henüz değildir.  Yanıt birkaç yararlı URL içerir.  Şimdilik düzenleme durumunu sorgulayalım.

4. `statusQueryGetUri` URL değerini kopyalayın ve tarayıcının adres çubuğuna yapıştırın ve isteği yürütün.

    İstek, bu durum için düzenleme örneğini sorgular. Aşağıdakine benzer bir nihai yanıt almanız gerekir.  Bu çıktı, örneğin tamamlandığını gösterir ve dayanıklı işlevin çıkışlarını veya sonuçlarını içerir.

    ```json
    {
        "instanceId": "d495cb0ac10d4e13b22729c37e335190",
        "runtimeStatus": "Completed",
        "input": null,
        "customStatus": null,
        "output": [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ],
        "createdTime": "2019-11-02T07:07:40Z",
        "lastUpdatedTime": "2019-11-02T07:07:52Z"
    }
    ```

5. Hata ayıklamayı durdurmak için **Shift + F5** tuşuna basın.

İşlevin yerel bilgisayarınızda düzgün çalıştığını doğruladıktan sonra, projeyi Azure'da yayımlamanın zamanı gelmiştir.

## <a name="publish-the-project-to-azure"></a>Projeyi Azure'da yayımlama

Projenizi yayımlayabilmeniz için önce Azure aboneliğinizde bir işlev uygulamanızın olması gerekir. Visual Studio'dan bir işlev uygulaması oluşturabilirsiniz.

[!INCLUDE [Publish the project to Azure](../../../includes/functions-vstools-publish.md)]

## <a name="test-your-function-in-azure"></a>Azure'da işlevinizi test etme

1. Yayımlama profili sayfasından işlev uygulamasının temel URL'sini kopyalayın. İşlevi yerel olarak test ederken kullandığınız URL’nin `localhost:port` kısmını, yeni temel URL ile değiştirin.

    Dayanıklı işlev HTTP tetikleyicinizi çağıran URL aşağıdaki biçimde olmalıdır:

        http://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>_HttpStart

2. HTTP isteğinin yeni URL’sini tarayıcınızın adres çubuğuna yapıştırın. Yayımlanan uygulamayı kullanmadan önce aynı durum yanıtını almalısınız.

## <a name="next-steps"></a>Sonraki adımlar

Dayanıklı bir C# işlev uygulaması oluşturmak ve yayımlamak Için Visual Studio 'yu kullandınız.

> [!div class="nextstepaction"]
> [Ortak dayanıklı işlev desenleri hakkında bilgi edinin](durable-functions-overview.md#application-patterns)