---
title: Öğrenciler için Azure başlangıç kullanarak bir Işlev oluşturma | Microsoft Docs
description: Öğrenciler için Azure başlangıç aboneliği içinden bir Azure Işlevi oluşturmayı öğrenin
Customer intent: As a student, I want to be able to create a HTTP triggered Function App within the Student Starter plan so that I can easily add APIs to any project.
services: functions
documentationcenter: na
author: alexkarcher-msft
manager: ggailey777
ms.assetid: ''
ms.service: azure-functions
ms.topic: quickstart
ms.date: 02/22/2019
ms.author: alkarche
ms.openlocfilehash: b4a143f14dc4e443570e8eca9ce6ce9e81b1d783
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70096634"
---
# <a name="create-a-function-using-azure-for-students-starter"></a>Öğrenciler için Azure başlangıç kullanarak bir Işlev oluşturma

Bu öğreticide, öğrenciler için Azure başlangıç aboneliğinde bir Hello World HTTP işlevi oluşturacağız. Ayrıca, bu abonelik türündeki Azure Işlevleri 'nde nelerin kullanılabildiğini de inceleyeceğiz.

*Öğrenciler için Microsoft Azure başlangıç* , ücretsiz olarak bulutta geliştirme yapmanız gereken Azure ürünlerini kullanmaya başlamanızı sağlar. [Bu teklif hakkında daha fazla bilgi edinin.](https://azure.microsoft.com/offers/ms-azr-0144p/)

Azure İşlevleri, öncelikle bir VM oluşturmak veya bir web uygulaması yayımlamak zorunda kalmadan kodunuzu [sunucusuz](https://azure.microsoft.com/solutions/serverless/) bir ortamda yürütmenize olanak tanır. [Burada Işlevler hakkında daha fazla bilgi edinin.](./functions-overview.md)

## <a name="create-a-function"></a>Işlev oluşturma

 Bu konu başlığında, Azure portal bir HTTP ile tetiklenen "Hello World" işlevi oluşturmak için Işlevleri nasıl kullanacağınızı öğrenin.

![Azure portalında işlev uygulaması oluşturma](./media/functions-create-student-starter/function-app-in-portal-editor.png)

## <a name="sign-in-to-azure"></a>Azure'da oturum açma

Azure hesabınızla Azure portalında <https://portal.azure.com> sayfasında oturum açın.

## <a name="create-a-function-app"></a>İşlev uygulaması oluşturma

İşlevlerinizin yürütülmesini barındıran bir işlev uygulamasına sahip olmanız gerekir. İşlev uygulaması, kaynakların daha kolay yönetilmesi, dağıtılması ve paylaşılması için işlevleri bir mantıksal birim olarak gruplandırmanıza olanak tanır. 

1. Azure portalının sol üst köşesinde bulunan **Yeni** düğmesine tıklayın, ardından **İşlem** > **İşlev Uygulaması** seçeneğini belirleyin.

    ![Azure portalında işlev uygulaması oluşturma](./media/functions-create-student-starter/function-app-create-flow.png)

2. Görüntünün altındaki tabloda belirtilen işlev uygulaması ayarlarını kullanın.

    <img src="./media/functions-create-student-starter/Function-create-start.png" width="315">

    | Ayar      | Önerilen değer  | Açıklama                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Uygulama adı** | Genel olarak benzersiz bir ad | Yeni işlev uygulamanızı tanımlayan ad. Geçerli karakterler: `a-z`, `0-9`, ve `-`.  | 
    | **Abonelik** | Aboneliğiniz | Bu yeni işlev uygulamasının oluşturulduğu abonelik. | 
    | **[Kaynak Grubu](../azure-resource-manager/resource-group-overview.md)** |  myResourceGroup | İşlev uygulamanızın oluşturulacağı yeni kaynak grubunun adı. |
   | **[Plan/konum App Service](./functions-scale.md)** | Yeni | İşlev uygulamanızın hangi bölgeye dağıtıldığını ve kaynaklarınızın yoğunluğunu denetleyen barındırma planı. Aynı plana dağıtılan birden çok Işlev uygulaması aynı tek bir ücretsiz örneği paylaşacaktır. Bu, öğrenci başlangıç planının kısıtlamasıdır. Tam barındırma seçenekleri [burada açıklanmıştır.](./functions-scale.md)|
    | **Çalışma zamanı yığını** | Tercih edilen dil | Tercih ettiğiniz işlev programlama dilini destekleyen bir çalışma zamanı seçin. C# ve F# için **.NET** işlevlerini seçin. |
    |**[Application Insights](./functions-monitoring.md)**| Enabled | Application Insights, işlev uygulamanızın günlüklerini depolamak ve analiz etmek için kullanılır. Application Insights destekleyen bir konum seçerseniz, varsayılan olarak etkindir. Application Insights, Application Insights dağıtmak üzere yakın bölgeyi el ile seçerek herhangi bir işlev için etkinleştirilebilir. Application Insights olmadan yalnızca canlı akış günlüklerini görüntüleyebileceksiniz.

3. Farklı bir konum seçmek için yukarıdaki **App Service planı/konumu** seçin

4. **Yeni oluştur** ' u seçin ve planınızı benzersiz bir ad verin.

5. Size en yakın konumu seçin. [Azure bölgelerinin tam haritasını buradan görebilirsiniz.](https://azure.microsoft.com/global-infrastructure/regions/) 

    <img src="./media/functions-create-student-starter/Create-ASP.png" width="800">

6. İşlev uygulamasını sağlamak ve dağıtmak için **Oluştur**'u seçin.

    <img src="./media/functions-create-student-starter/Function-create-end.png" width="315">

7. Portalın sağ üst köşesindeki Bildirim simgesini seçin ve **Dağıtım başarılı** iletisini bekleyin.

    ![Yeni işlev uygulaması ayarlarını tanımlama](./media/functions-create-student-starter/function-app-create-notification.png)

8. Yeni işlev uygulamanızı görüntülemek için **Kaynağa git**’i seçin.

Ardından, yeni işlev uygulamasında bir işlev oluşturun.

## <a name="create-function"></a>HTTP ile tetiklenen bir işlev oluşturma

1. Yeni işlev uygulamanızı genişletin ve **İşlevler**'in yanındaki **+** düğmesini, **Portalda**'yı ve **Devam**'ı seçin.

    ![İşlevler hızlı başlangıcı platform seçimi.](./media/functions-create-student-starter/function-app-quickstart-choose-portal.png)

1. **Web Kancası + API**'yi ve ardından **Oluştur**'u seçin.

    ![Azure portalındaki İşlevler hızlı başlangıcı.](./media/functions-create-student-starter/function-app-quickstart-node-webhook.png)

Dile özgü HTTP ile tetiklenen işlev şablonu kullanılarak bir işlev oluşturulur.

Artık bir HTTP isteği göndererek yeni işlevi çalıştırabilirsiniz.

## <a name="test-the-function"></a>İşlevi test etme

1. Yeni işlevinizde sağ üst kısımdaki **</> İşlev URL'sini al**'a tıklayın, **varsayılan (İşlev anahtarı)** seçeneğini belirleyin ve ardından **Kopyala**'ya tıklayın. 

    ![Azure portalından işlev URL’sini kopyalama](./media/functions-create-student-starter/function-app-develop-tab-testing.png)

2. İşlev URL'sini tarayıcınızın adres çubuğuna yapıştırın. `&name=<yourname>` sorgu dizesi değerini bu URL’nin sonuna ekleyin ve isteği yürütmek için klavyenizdeki `Enter` tuşuna basın. İşlev tarafından döndürülen yanıtın tarayıcıda gösterildiğini görürsünüz.  

    Aşağıdaki örnekte tarayıcıdaki yanıt gösterilmektedir:

    ![Tarayıcıdaki işlev yanıtı.](./media/functions-create-student-starter/function-app-browser-testing.png)

    İstek URL’si, işlevinize HTTP üzerinden erişmek için varsayılan olarak gerekli olan bir anahtar içerir.

3. İşleviniz çalıştığında, izleme bilgileri günlüklere yazılır. Önceki yürütme işleminden alınan izleme çıktısını görmek için, portalda işlevinize geri dönün ve ekranın altındaki oka tıklayarak **Günlükler**’i genişletin.

   ![Azure portalında İşlevler günlük görüntüleyicisi.](./media/functions-create-student-starter/function-view-logs.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="supported-features-in-azure-for-students-starter"></a>Öğrenciler için Azure başlangıç 'ta desteklenen özellikler

Öğrenci için Azure başlangıç bölümünde, Azure Işlevleri çalışma zamanı özelliklerinin çoğuna aşağıda listelenen birkaç anahtar sınırlaması ile erişebilirsiniz:

* HTTP tetikleyicisi desteklenen tek tetikleyici türüdür.
    * Tüm giriş ve çıkış bağlamaları desteklenir! [Tam listeye buradan bakın.](functions-triggers-bindings.md)
* Desteklenen Diller: 
    * C#(.NET Core 2)
    * JavaScript (node. js 8 & 10)
    * F#(.NET Core 2)
    * [Daha yüksek planlarda desteklenen dillere bakın](supported-languages.md)
* Windows desteklenen tek işletim sistemidir.
* Ölçek, her gün 60 dakika boyunca çalışan [bir ücretsiz katman örneğiyle](https://azure.microsoft.com/pricing/details/app-service/windows/) kısıtlıdır. HTTP trafiği alındığından ancak başka hiçbir işlem yapılmadığında 0 ' dan 1 ' e kadar örnek olarak ölçeklendirirsiniz.
* Yalnızca [2. x çalışma zamanı](functions-versions.md) desteklenir.
* Tüm geliştirici araçları, işlevleri düzenlenmek ve yayımlamak için desteklenir. Buna VS Code, Visual Studio, Azure CLı ve Azure portal dahildir. Portal dışında bir şey kullanmak isterseniz, önce portalda bir uygulama oluşturmanız ve ardından tercih ettiğiniz araçta bir dağıtım hedefi olarak bu uygulamayı seçmeniz gerekir.

## <a name="next-steps"></a>Sonraki adımlar

HTTP ile tetiklenen basit bir işlevi olan bir işlev uygulaması oluşturdunuz! Artık yerel araçları, daha fazla dili, izlemeyi ve tümleştirmeleri inceleyebilirsiniz.

 * [Visual Studio kullanarak ilk işlevinizi oluşturma](./functions-create-your-first-function-visual-studio.md)
 * [Visual Studio Code kullanarak ilk işlevinizi oluşturma](./functions-create-first-function-vs-code.md)
 * [Azure Işlevleri JavaScript Geliştirici Kılavuzu](./functions-reference-node.md)
 * [Azure Işlevleri 'ni kullanarak bir Azure SQL veritabanına bağlanma](./functions-scenario-database-table-cleanup.md)
 * [Azure IŞLEVLERI http bağlamaları hakkında daha fazla bilgi edinin](./functions-bindings-http-webhook.md).
 * [Azure Işlevlerinizi izleyin](./functions-monitoring.md)
