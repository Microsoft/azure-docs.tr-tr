---
title: Python kullanarak Azure 'da ilk dayanıklı işlevinizi oluşturma
description: Visual Studio Code kullanarak Python 'da Azure dayanıklı Işlevi oluşturun ve yayımlayın.
author: anthonychu
ms.topic: quickstart
ms.date: 12/23/2020
ms.reviewer: azfuncdf, antchu
ms.openlocfilehash: cccb42041ece65ad4f245c28dd7c5cf356d5e63c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104775162"
---
# <a name="create-your-first-durable-function-in-python"></a>Python 'da ilk dayanıklı işlevinizi oluşturma

*Dayanıklı işlevler* , bir sunucusuz ortamda durum bilgisi olan işlevler yazmanıza olanak sağlayan bir [Azure işlevleri](../functions-overview.md) uzantısıdır. Uzantı sizin için durumu, denetim noktalarını ve yeniden başlatmaları yönetir.

Bu makalede, bir "Hello World" dayanıklı işlevini yerel olarak oluşturmak ve test etmek için Visual Studio Code Azure Işlevleri uzantısının nasıl kullanılacağını öğrenirsiniz.  Bu işlev, diğer işlevlere yönelik çağrıları düzenler ve birlikte zincirler. Ardından işlev kodunu Azure’da yayımlayacaksınız.

![Azure 'da dayanıklı işlevi çalıştırma](./media/quickstart-python-vscode/functions-vs-code-complete.png)

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlamak için:

* [Visual Studio Code](https://code.visualstudio.com/download)'i yükler.

* [Azure işlevleri](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) vs Code uzantısını yükler.

* [Azure Functions Core Tools](../functions-run-local.md)en son sürümüne sahip olduğunuzdan emin olun.

* Dayanıklı İşlevler bir Azure depolama hesabı gerektirir. Bir Azure aboneliğine ihtiyacınız vardır.

* [Python](https://www.python.org/) 'un 3,6, 3,7 veya 3,8 sürümünün yüklü olduğundan emin olun.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-your-local-project"></a><a name="create-an-azure-functions-project"></a>Yerel projenizi oluşturma 

Bu bölümde, yerel bir Azure Işlevleri projesi oluşturmak için Visual Studio Code kullanırsınız. 

1. Visual Studio Code, komut paletini açmak için F1 (veya <kbd>Ctrl/Cmd + SHIFT + P</kbd>) tuşuna basın. Komut paletinde, araması yapın ve seçin `Azure Functions: Create New Project...` .

    ![Create işlevi](media/quickstart-python-vscode/functions-create-project.png)

1. Projeniz için boş bir klasör konumu seçin ve **Seç**' i seçin.

1. İstemlerin ardından aşağıdaki bilgileri sağlayın:

    | İstem | Değer | Açıklama |
    | ------ | ----- | ----------- |
    | İşlev uygulaması projeniz için bir dil seçin | Python | Yerel bir Python Işlevleri projesi oluşturun. |
    | Sürüm seçin | Azure İşlevleri v3 | Bu seçeneği yalnızca temel araçlar yüklü değilse görürsünüz. Bu durumda, uygulamayı ilk kez çalıştırdığınızda çekirdek araçlar yüklenir. |
    | Python sürümü | Python 3,6, 3,7 veya 3,8 | VS Code, seçtiğiniz sürümde bir sanal ortam oluşturacak. |
    | Projenizin ilk işlevi için bir şablon seçin | Şimdilik atla | |
    | Projenizi nasıl açmak istediğinizi seçin | Geçerli pencerede aç | VS Code seçtiğiniz klasörde yeniden açılır. |

Gerekirse Azure Functions Core Tools Visual Studio Code yüklenir. Ayrıca, bir klasörde işlev uygulaması projesi oluşturur. Bu proje yapılandırma dosyalarında [host.js](../functions-host-json.md) ve [local.settings.js](../functions-run-local.md#local-settings-file) içerir.

Kök klasörde bir *requirements.txt* dosyası da oluşturulur. İşlev uygulamanızı çalıştırmak için gereken Python paketlerini belirtir.

## <a name="install-azure-functions-durable-from-pypi"></a>Pypı 'den Azure işlevleri 'ni dayanıklı bir şekilde yükler

Projeyi oluşturduğunuzda Azure Işlevleri VS Code uzantısı, seçtiğiniz Python sürümüne sahip bir sanal ortam otomatik olarak oluşturulur. Sanal ortamı bir terminalde etkinleştireceğinize ve Azure Işlevleri ve Dayanıklı İşlevler gereken bazı bağımlılıkları yükleyeceksiniz.

1. Düzenleyicide *requirements.txt* açın ve içeriğini aşağıdaki şekilde değiştirin:

    ```
    azure-functions
    azure-functions-durable
    ```

1. Düzenleyicinin tümleşik terminalini geçerli klasörde açın (<kbd>Ctrl + Shift + '</kbd>).

1. Tümleşik terminalde, geçerli klasörde sanal ortamı etkinleştirin:

    **Linux veya macOS**

    ```bash
    source .venv/bin/activate
    ```

    **Windows**

    ```powershell
    .venv\scripts\activate
    ```

    ![Sanal ortamı etkinleştir](media/quickstart-python-vscode/activate-venv.png)

1. Sanal ortamın etkinleştirildiği tümleşik terminalde, yeni tanımladığınız paketleri yüklemek için PIP kullanın:

    ```bash
    python -m pip install -r requirements.txt
    ```

## <a name="create-your-functions"></a>İşlevlerinizi oluşturma

Temel bir Dayanıklı İşlevler uygulaması üç işlev içerir:

* *Orchestrator işlevi* -diğer işlevleri düzenleyen bir iş akışını açıklar.
* *Etkinlik işlevi* -Orchestrator işlevi tarafından çağırılır, iş gerçekleştirir ve isteğe bağlı olarak bir değer döndürür.
* *Client işlevi* -bir Orchestrator işlevi Başlatan normal bir Azure işlevi. Bu örnek, HTTP ile tetiklenen bir işlev kullanır.

### <a name="orchestrator-function"></a>Orchestrator işlevi

Projenizde dayanıklı işlev kodu oluşturmak için bir şablon kullanın.

1. Komut paletinde, araması yapın ve seçin `Azure Functions: Create Function...` .

1. İstemlerin ardından aşağıdaki bilgileri sağlayın:

    | İstem | Değer | Açıklama |
    | ------ | ----- | ----------- |
    | İşleviniz için bir şablon seçin | Dayanıklı İşlevler Orchestrator | Dayanıklı İşlevler düzenleme oluşturma |
    | Bir işlev adı girin | Merhaba Orchestrator | Dayanıklı işlevinizin adı |

Etkinlik işlevlerini koordine etmek için bir Orchestrator eklediniz. Orchestrator işlevini görmek için *helloorchestrator/ \_ \_ init__. Kopyala* ' yı açın. Her çağrı, `context.call_activity` adlı bir etkinlik işlevini çağırır `Hello` .

Ardından, başvurulan `Hello` etkinlik işlevini eklersiniz.

### <a name="activity-function"></a>Activity işlevi

1. Komut paletinde, araması yapın ve seçin `Azure Functions: Create Function...` .

1. İstemlerin ardından aşağıdaki bilgileri sağlayın:

    | İstem | Değer | Açıklama |
    | ------ | ----- | ----------- |
    | İşleviniz için bir şablon seçin | Dayanıklı İşlevler etkinliği | Etkinlik işlevi oluşturma |
    | Bir işlev adı girin | Hello | Etkinlik işlevinizin adı |

`Hello`Orchestrator tarafından çağrılan etkinlik işlevini eklediniz. Giriş olarak bir ad alıp bir selamlama döndürdüğünden emin olmak için *Merhaba/ \_ \_ init__. Kopyala* ' yı açın. Bir etkinlik işlevi, veritabanı çağrısı yapma veya hesaplama gerçekleştirme gibi eylemleri gerçekleştirebileceğiniz yerdir.

Son olarak, Orchestration 'u Başlatan bir HTTP tetiklenen işlev eklersiniz.

### <a name="client-function-http-starter"></a>İstemci işlevi (HTTP başlangıç)

1. Komut paletinde, araması yapın ve seçin `Azure Functions: Create Function...` .

1. İstemlerin ardından aşağıdaki bilgileri sağlayın:

    | İstem | Değer | Açıklama |
    | ------ | ----- | ----------- |
    | İşleviniz için bir şablon seçin | HTTP Starter Dayanıklı İşlevler | HTTP başlangıç işlevi oluşturma |
    | Bir işlev adı girin | DurableFunctionsHttpStart | Etkinlik işlevinizin adı |
    | Yetkilendirme düzeyi | Anonim | Tanıtım amacıyla, işlevin kimlik doğrulaması olmadan çağrılmasına izin ver |

Orchestration Başlatan bir HTTP tetiklenen işlev eklediniz. Yeni bir düzenleme başlatmak için kullandığını görmek için *DurableFunctionsHttpStart/ \_ \_ init__. Kopyala* öğesini açın `client.start_new` . Ardından `client.create_check_status_response` , yeni Orchestration izlemek ve yönetmek için kullanılabilecek URL 'leri içeren BIR http yanıtı döndürmek için kullanır.

Artık yerel olarak çalıştırılabilecek ve Azure 'a dağılabilecek bir Dayanıklı İşlevler uygulaması var.

## <a name="test-the-function-locally"></a>İşlevi yerel olarak test etme

Azure İşlevleri Temel Araçları, Azure İşlevleri projenizi yerel geliştirme bilgisayarınızda çalıştırmanıza olanak sağlar. Yüklü değilse, Visual Studio Code bir işlevi ilk kez başlattığınızda bu araçları yüklemeniz istenir.

1. İşlevinizi test etmek için `Hello` etkinlik işlev kodunda (*Hello/ \_ \_ init__. Kopyala*) bir kesme noktası ayarlayın. `Debug: Start Debugging`İşlev uygulaması projesini başlatmak Için F5 tuşuna basın veya komut paletinden seçim yapın. Temel Araçlar’daki çıktı, **Terminal** panelinde görüntülenir.

    > [!NOTE]
    > Hata ayıklama hakkında daha fazla bilgi için [dayanıklı işlevler tanılamayı](durable-functions-diagnostics.md#debugging) inceleyin.

1. Dayanıklı İşlevler, çalıştırmak için bir Azure depolama hesabı gerektirir. VS Code bir depolama hesabı seçmenizi isterse **depolama hesabı seç**' i seçin.

    ![Depolama hesabı oluştur](media/quickstart-python-vscode/functions-select-storage.png)

1. İstemlerin ardından, Azure 'da yeni bir depolama hesabı oluşturmak için aşağıdaki bilgileri sağlayın.

    | İstem | Değer | Açıklama |
    | ------ | ----- | ----------- |
    | Abonelik seçme | *aboneliğinizin adı* | Azure aboneliğinizi seçin |
    | Bir depolama hesabı seçin | Yeni depolama hesabı oluşturma |  |
    | Yeni depolama hesabının adını girin | *benzersiz ad* | Oluşturulacak depolama hesabının adı |
    | Kaynak grubu seçme | *benzersiz ad* | Oluşturulacak kaynak grubunun adı |
    | Bir konum seçin | *geli* | Size yakın bir bölge seçin |

1. **Terminal** panelinde, HTTP ile tetiklenen işlevinizin URL uç noktasını kopyalayın.

    ![Azure yerel çıktısı](media/quickstart-python-vscode/functions-f5.png)

1. Tarayıcınızı veya [Postman](https://www.getpostman.com/) veya [kıvrık](https://curl.haxx.se/)gıbı bir aracı kullanarak URL uç noktasına bir http isteği gönderin. Son segmenti Orchestrator işlevinin adı () ile değiştirin `HelloOrchestrator` . URL şuna benzer olmalıdır `http://localhost:7071/api/orchestrators/HelloOrchestrator` .

   Yanıt, HTTP işlevinin ilk sonucudur ve dayanıklı düzenleme 'nin başarıyla başlatıldığını bilmenizi sağlar. Orchestration 'un nihai sonucu henüz değildir. Yanıt birkaç yararlı URL içerir. Şimdilik düzenleme durumunu sorgulayalım.

1. URL değerini kopyalayın `statusQueryGetUri` ve tarayıcının adres çubuğuna yapıştırın ve isteği yürütün. Alternatif olarak, GET isteğini vermek için Postman kullanmaya de devam edebilirsiniz.

   İstek, bu durum için düzenleme örneğini sorgular. Örneğin tamamlandığını gösteren ve dayanıklı işlevin çıkışlarını veya sonuçlarını içeren nihai bir yanıt almanız gerekir. Şöyle görünür: 

    ```json
    {
        "name": "HelloOrchestrator",
        "instanceId": "9a528a9e926f4b46b7d3deaa134b7e8a",
        "runtimeStatus": "Completed",
        "input": null,
        "customStatus": null,
        "output": [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ],
        "createdTime": "2020-03-18T21:54:49Z",
        "lastUpdatedTime": "2020-03-18T21:54:54Z"
    }
    ```

1. Hata ayıklamayı durdurmak için VS Code <kbd>Shift + F5</kbd> tuşlarına basın.

İşlevin yerel bilgisayarınızda düzgün çalıştığını doğruladıktan sonra, projeyi Azure'da yayımlamanın zamanı gelmiştir.

[!INCLUDE [functions-create-function-app-vs-code](../../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../../includes/functions-publish-project-vscode.md)]

## <a name="test-your-function-in-azure"></a>Azure'da işlevinizi test etme

1. **Çıktı** panelinden HTTP tetikleyicisinin URL’sini kopyalayın. HTTP ile tetiklenen işlevinizi çağıran URL şu biçimde olmalıdır: `http://<functionappname>.azurewebsites.net/api/orchestrators/HelloOrchestrator`

2. HTTP isteğinin yeni URL’sini tarayıcınızın adres çubuğuna yapıştırın. Yayımlanan uygulamayı kullanmadan önce aynı durum yanıtını almalısınız.

## <a name="next-steps"></a>Sonraki adımlar

Python dayanıklı bir işlev uygulaması oluşturmak ve yayımlamak için Visual Studio Code kullandınız.

> [!div class="nextstepaction"]
> [Ortak dayanıklı işlev desenleri hakkında bilgi edinin](durable-functions-overview.md#application-patterns)
