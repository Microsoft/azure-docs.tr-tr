---
title: Öğretici-API 'Leri Içeri aktarma ve yönetme-Azure API Management ve Visual Studio Code | Microsoft Docs
description: Bu öğreticide, API 'Leri içeri aktarmak, test etmek ve yönetmek için Visual Studio Code için Azure API Management uzantısını nasıl kullanacağınızı öğrenin.
ms.service: api-management
author: dlepow
ms.author: apimpm
ms.topic: tutorial
ms.date: 12/10/2020
ms.openlocfilehash: 0090d981e93cee12f2feaaf7d2c12f341564f6ec
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107482339"
---
# <a name="tutorial-use-the-api-management-extension-for-visual-studio-code-to-import-and-manage-apis"></a>Öğretici: API 'Leri içeri ve yönetmek için Visual Studio Code API Management uzantısını kullanma

Bu öğreticide, API Management ortak işlemler için Visual Studio Code API Management uzantısının nasıl kullanılacağını öğrenirsiniz. API 'Leri içeri aktarmak, güncelleştirmek, test etmek ve yönetmek için tanıdık Visual Studio Code ortamını kullanın.

Aşağıdakileri nasıl yapacağınızı öğrenirsiniz:

> [!div class="checklist"]
> * Bir API 'YI API Management içeri aktarma
> * API 'YI düzenleme
> * API Management ilkelerini uygula
> * API’yi test etme


:::image type="content" source="media/visual-studio-code-tutorial/tutorial-api-result.png" alt-text="API Management uzantılı API":::

Ek API Management özelliklerine giriş için [Azure Portal](import-and-publish.md)kullanarak API Management öğreticileri inceleyin.

## <a name="prerequisites"></a>Önkoşullar
- [Azure API Management terminolojisini](api-management-terminology.md) anlama
- Visual Studio Code için [Visual Studio Code](https://code.visualstudio.com/) ve en son [Azure API Management uzantısını](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-apimanagement&ssr=false#overview) yüklediğinizden emin olun
- [API Management örneği oluşturma](vscode-create-service-instance.md)

## <a name="import-an-api"></a>Bir API’yi içeri aktarma

Aşağıdaki örnek, JSON biçiminde bir Openapı belirtimini API Management içine aktarır. Microsoft, bu örnekte kullanılan arka uç API 'sini sağlar ve ' ı Azure 'da barındırır `https://conferenceapi.azurewebsites.net?format=json` .

1. Visual Studio Code, etkinlik çubuğundan Azure simgesini seçin.
1. Gezgin bölmesinde, oluşturduğunuz API Management örneğini genişletin.
1. **API 'ler**' e sağ tıklayın ve **Openapı bağlantısından içeri aktar**' ı seçin. 
1. İstendiğinde, aşağıdaki değerleri girin:
    1. JSON biçimindeki içerik için bir **Openapı bağlantısı** . Bu örnek için: *https://conferenceapi.azurewebsites.net?format=json* .
    Bu URL, örnek API 'yi uygulayan hizmettir. İstekleri bu adrese iletir API Management.
    1. API Management örneğinde benzersiz olan *demo-Conference-API* gibi bir **API adı**. Bu ad yalnızca harf, sayı ve kısa çizgi içerebilir. İlk ve son karakterlerin alfasayısal olması gerekir. Bu ad, API 'YI çağırmak için yol içinde kullanılır.

API başarıyla alındıktan sonra gezgin bölmesinde görünür ve kullanılabilir API işlemleri, **işlemler** düğümünün altında görünür.

:::image type="content" source="media/visual-studio-code-tutorial/tutorial-api-operations.png" alt-text="Gezgin bölmesinde içeri aktarılan API":::

## <a name="edit-the-api"></a>API 'YI düzenleme

Visual Studio Code ' de API 'YI düzenleyebilirsiniz. Örneğin, API 'ye erişmek için kullanılan **http** protokolünü kaldırmak için DÜZENLEYICI penceresindeki apı 'nın Kaynak Yöneticisi JSON açıklamasını düzenleyin. Sonra **Dosya**  >  **Kaydet**' i seçin.

:::image type="content" source="media/visual-studio-code-tutorial/import-demo-api.png" alt-text="JSON açıklamasını Düzenle":::

Openapı biçimini düzenlemek için, Gezgin bölmesinde API adına sağ tıklayın ve **Openapı 'Yi Düzenle**' yi seçin. Değişikliklerinizi yapın ve ardından **Dosya**  >  **Kaydet**' i seçin.

## <a name="apply-policies-to-the-api"></a>API 'ye ilke uygulama 

API Management, API 'leriniz için yapılandırabileceğiniz [ilkeler](api-management-policies.md) sağlar. İlkeler, bir API isteği veya yanıtı üzerinde sırayla yürütülen deyimlerin bir koleksiyonudur. İlkeler, API Management örneğindeki tüm API 'Ler için uygulanan genel olabilir veya belirli bir API veya API işlemi kapsamına eklenebilir.

Bu bölümde API yanıtını dönüştüren API 'nize bazı ortak giden ilkelerin nasıl uygulanacağı gösterilmektedir. Bu örnekteki ilkeler yanıt üst bilgilerini değiştirir ve yanıt gövdesinde görüntülenen özgün arka uç URL 'Lerini gizler.

1. Gezgin bölmesinde, içeri aktardığınız *demo-Conference-API* altında **ilke** ' yi seçin. İlke dosyası düzenleyici penceresinde açılır. Bu dosya, API 'deki tüm işlemler için ilkeleri yapılandırır. 

1. Dosyayı öğesinde aşağıdaki içerikle güncelleştirin `<outbound>` :
    ```html
    [...]
    <outbound>
        <set-header name="Custom" exists-action="override">
            <value>"My custom value"</value>
        </set-header>
        <set-header name="X-Powered-By" exists-action="delete" />
        <redirect-content-urls />
        <base />
    </outbound>
    [...]
    ```

    * İlk `set-header` ilke, tanıtım amacıyla özel bir yanıt üst bilgisi ekler.
    * İkinci ilke, varsa `set-header` **X destekli** üstbilgiyi siler. Bu üstbilgi, API arka ucunda kullanılan uygulama çerçevesini açığa çıkarabilirsiniz ve yayımcılar genellikle kaldırır.
    * `redirect-content-urls`API Management ağ geçidi aracılığıyla eşdeğer bağlantılara işaret ettikleri şekilde, yanıt gövdesindeki ilke yeniden yazar (maskeler) bağlantıları.
    
1. Dosyayı kaydedin. İstenirse, dosyayı buluta yüklemek için **karşıya yükle** ' yi seçin.

## <a name="test-the-api"></a>API’yi test etme

### <a name="get-the-subscription-key"></a>Abonelik anahtarını al

İçeri aktardığınız içeri aktarılan API 'yi ve uygulanan ilkeleri test etmek için API Management örneğiniz için bir abonelik anahtarına ihtiyacınız vardır.

1. Gezgin bölmesinde, API Management örneğinizin adına sağ tıklayın.
1. **Abonelik anahtarını Kopyala**' yı seçin.

    :::image type="content" source="media/visual-studio-code-tutorial/copy-subscription-key.png" alt-text="Abonelik anahtarını Kopyala":::

### <a name="test-an-api-operation"></a>API işlemini test etme

1. Gezgin bölmesinde, içeri aktardığınız *demo-Conference-API* altındaki **işlemler** düğümünü genişletin.
1. *Gethoparlörler* gibi bir işlem seçin ve ardından işlemi sağ tıklatın ve **test işlemi**' ni seçin.
1. Düzenleyici penceresinde, **OCP-apim-Subscription-Key**' ın yanındaki `{{SubscriptionKey}}` kopyaladığınız abonelik anahtarıyla değiştirin.
1. **İstek gönder**’i seçin. 

:::image type="content" source="media/visual-studio-code-tutorial/test-api.png" alt-text="Visual Studio Code API isteği gönder":::

İstek başarılı olduğunda, arka uç **200 Tamam** ve bazı veriler ile yanıt verir.

:::image type="content" source="media/visual-studio-code-tutorial/test-api-policies.png" alt-text="API test işlemi":::

Yanıtta aşağıdaki ayrıntılara dikkat edin:
* **Özel** üst bilgi yanıta eklenir.
* **X Ile güçlendirilmiştir** üst bilgi yanıtta görünmez.
* Bu durumda, API arka ucunun URL 'Leri API Management ağ geçidine yönlendirilir `https://apim-hello-world.azure-api.net/demo-conference-api` .

### <a name="trace-the-api-operation"></a>API işlemini izleme

API işleminde hata ayıklamanıza yardımcı olacak ayrıntılı izleme bilgileri için, **OCP-APIM-Trace-location**' ın yanında görüntülenen bağlantıyı seçin. 

Bu konumdaki JSON dosyası, istek yapıldıktan sonra herhangi bir sorunun nerede olduğunu belirleyebilmeniz için gelen, arka uç ve giden izleme bilgilerini içerir.

> [!TIP]
> API işlemlerini test ettiğinizde API Management uzantısı isteğe bağlı [ilke hata ayıklamasına](api-management-debug-policies.md) izin verir (Geliştirici hizmet katmanında kullanılabilir).

## <a name="clean-up-resources"></a>Kaynakları temizleme

Artık gerekli değilse, [API Management hizmetini](get-started-create-service-instance.md#clean-up-resources) ve kaynak grubunu silmek için sağ tıklayıp **portalda aç** ' ı seçerek API Management örneğini kaldırın.

Alternatif olarak, **API Management Sil** ' i seçerek yalnızca API Management örneğini silebilirsiniz (Bu işlem, kaynak grubunu silmez).

:::image type="content" source="media/visual-studio-code-tutorial/vscode-apim-delete.png" alt-text="VS Code API Management örneğini Sil":::

## <a name="next-steps"></a>Sonraki adımlar

Bu öğretici, API 'Leri içeri ve yönetmek için kullanabileceğiniz Visual Studio Code için API Management uzantısının çeşitli özelliklerini sunmuştur. Şunları öğrendiniz:

> [!div class="checklist"]
> * Bir API 'YI API Management içeri aktarma
> * API 'YI düzenleme
> * API Management ilkelerini uygula
> * API’yi test etme

API Management uzantısı, API 'lerinizle çalışmak için ek özellikler sağlar. Örneğin, (Geliştirici hizmet katmanında kullanılabilir) [ilkeler hata ayıklama](api-management-debug-policies.md) veya [adlandırılmış değerleri](api-management-howto-properties.md)oluşturma ve yönetme.
