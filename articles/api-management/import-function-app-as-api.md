---
title: Azure İşlev Uygulaması API Management API olarak içeri aktarma
titleSuffix: Azure API Management
description: Bu makalede bir Azure İşlev Uygulaması Azure API Management bir API 'ye nasıl içeri aktarılacağı gösterilmektedir.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 04/16/2021
ms.author: apimpm
ms.openlocfilehash: 8e8047fc6865bab8f4aac2315b269bf7526b1e42
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/18/2021
ms.locfileid: "107599388"
---
# <a name="import-an-azure-function-app-as-an-api-in-azure-api-management"></a>Azure İşlev Uygulamasını Azure API Management'da API olarak içeri aktarma

Azure API Management, Azure İşlev Uygulamalarının yeni API olarak içeri aktarılmasını veya var olan API'lere eklenmesini destekler. Bu işlemin ardından Azure İşlev Uygulamasında otomatik olarak bir konak anahtarı oluşturulur. Bu anahtar daha sonra Azure API Management'taki bir adlandırılmış değere atanır.

Bu makalede bir Azure İşlev Uygulaması Azure API Management API 'SI olarak içeri ve test etme adımları anlatılmaktadır. 

Şunları öğrenirsiniz:

> [!div class="checklist"]
> * Azure İşlev Uygulamalarını API olarak içeri aktarma
> * Azure İşlev Uygulamalarını API'lere ekleme
> * Yeni Azure İşlev Uygulaması ana bilgisayar anahtarını ve Azure API Management adlandırılmış değerini görüntüleme
> * Azure portalında API’yi test etme

## <a name="prerequisites"></a>Önkoşullar

* [Azure API Management örneği oluşturma](get-started-create-service-instance.md) hızlı başlangıcı ' nı doldurun.
* Aboneliğinizde bir Azure İşlevleri uygulaması bulunduğundan emin olun. Daha fazla bilgi için bkz. [Azure İşlev Uygulaması oluşturma](../azure-functions/functions-get-started.md). İşlevlerin HTTP tetikleyicisi ve Yetkilendirme düzeyi *anonim* veya *işlev* olarak ayarlanmış olmalıdır.

> [!NOTE]
> API 'lerinizi içeri aktarmak ve yönetmek için Visual Studio Code API Management uzantısını kullanabilirsiniz. Yüklemek ve başlamak için [API Management uzantısı öğreticisini](visual-studio-code-tutorial.md) izleyin.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="import-an-azure-function-app-as-a-new-api"></a><a name="add-new-api-from-azure-function-app"></a> Azure İşlev Uygulamalarını yeni API olarak içeri aktarma

Bir Azure İşlev Uygulamasından yeni API oluşturmak için aşağıdaki adımları uygulayın.

1. Azure portal API Management hizmetinize gidin ve menüden **API 'leri** seçin.

2. **Yeni API ekleyin** listesinde **İşlev Uygulaması**'nı seçin.

    :::image type="content" source="./media/import-function-app-as-api/add-01.png" alt-text="İşlev Uygulaması kutucuğunu gösteren ekran görüntüsü.":::

3. İçeri aktarılacak İşlevleri seçmek için **Gözat**'a tıklayın.

    :::image type="content" source="./media/import-function-app-as-api/add-02.png" alt-text="Tarama düğmesini vurgulayan ekran görüntüsü.":::

4. Kullanılabilir İşlev Uygulamaları listesinden seçim yapmak için **İşlev Uygulaması** bölümüne tıklayın.

    :::image type="content" source="./media/import-function-app-as-api/add-03.png" alt-text="İşlev Uygulaması bölümünü vurgulayan ekran görüntüsü.":::

5. İşlevlerini içeri aktarmak istediğiniz İşlev Uygulamasını bulun, uygulamaya tıklayın ve **Seç**'e basın.

    :::image type="content" source="./media/import-function-app-as-api/add-04.png" alt-text="Işlevleri içeri aktarmak istediğiniz İşlev Uygulaması ve seçim düğmesini vurgulayan ekran görüntüsü.":::

6. İçeri aktarmak istediğiniz İşlevleri seçin ve **Seç**'e tıklayın.
    * Yalnızca *anonim* veya *işlev* yetkilendirme düzeylerine sahip http tetikleyicisini temel alarak işlevleri içeri aktarabilirsiniz.

    :::image type="content" source="./media/import-function-app-as-api/add-05.png" alt-text="İçeri aktarılacak Işlevleri ve seçim düğmesini vurgulayan ekran görüntüsü.":::

7. **Tam** görünümüne geçiş yapın ve yeni API’nize **Ürün** atayın. 
8. Gerekirse, oluşturma sırasında diğer alanları belirtin veya daha sonra **Ayarlar** sekmesini kullanarak yapılandırın. 
    * Ayarlar, [Ilk API 'Nizi Içeri aktarma ve yayımlama](import-and-publish.md#import-and-publish-a-backend-api) öğreticisinde açıklanmıştır.

    >[!NOTE]
    > Ürünler, geliştirici portalı aracılığıyla geliştiricilere sunulan bir veya daha fazla API 'nin ilişkilerinden biridir. İlk olarak, geliştiricilerin API 'ye erişim sağlamak için bir ürüne abone olmaları gerekir. Abone olduktan sonra, söz konusu üründeki tüm API 'leri için bir abonelik anahtarı alırlar. API Management örneğinin Oluşturucusu olarak, bir yöneticiyseniz ve varsayılan olarak her ürüne abone olursunuz.
    >
    > Her bir API Management örneği iki varsayılan örnek ürünle birlikte gelir:
    > - **Başlangıç**
    > - **Sınırsız**

9. **Oluştur**’a tıklayın.

## <a name="append-azure-function-app-to-an-existing-api"></a><a name="append-azure-function-app-to-api"></a> Azure İşlev Uygulamalarını var olan API'lere ekleme

Azure İşlev Uygulamasını var olan bir API'ye eklemek için aşağıdaki adımları uygulayın.

1. **Azure API Management** hizmet örneğinizde soldaki menüden **API'ler** seçeneğini belirleyin.

2. Azure İşlev Uygulamasından içeri aktarmak istediğiniz bir API'yi seçin. **...** simgesine tıklayın ve bağlam menüsünden **İçeri Aktar**'ı seçin.

    :::image type="content" source="./media/import-function-app-as-api/append-function-api-1.png" alt-text="Içeri aktar menü seçeneğini vurgulayan ekran görüntüsü.":::

3. **İşlev Uygulaması** kutucuğuna tıklayın.

    :::image type="content" source="./media/import-function-app-as-api/append-function-api-2.png" alt-text="İşlev Uygulaması kutucuğunu vurgulayan ekran görüntüsü.":::

4. Açılır pencerede **Gözat**'a tıklayın.

    :::image type="content" source="./media/import-function-app-as-api/append-function-api-3.png" alt-text="Tarama düğmesini gösteren ekran görüntüsü.":::

5. Kullanılabilir İşlev Uygulamaları listesinden seçim yapmak için **İşlev Uygulaması** bölümüne tıklayın.

    :::image type="content" source="./media/import-function-app-as-api/add-03.png" alt-text="Işlev uygulamalarının listesini vurgulayan ekran görüntüsü.":::

6. İşlevlerini içeri aktarmak istediğiniz İşlev Uygulamasını bulun, uygulamaya tıklayın ve **Seç**'e basın.

    :::image type="content" source="./media/import-function-app-as-api/add-04.png" alt-text="İşlevleri içe aktarmak istediğiniz İşlev Uygulaması vurgulayan ekran görüntüsü.":::

7. İçeri aktarmak istediğiniz İşlevleri seçin ve **Seç**'e tıklayın.

    :::image type="content" source="./media/import-function-app-as-api/add-05.png" alt-text="İçe aktarmak istediğiniz işlevleri vurgulayan ekran görüntüsü.":::

8. **İçeri Aktar**’a tıklayın.

    :::image type="content" source="./media/import-function-app-as-api/append-function-api-4.png" alt-text="İşlev Uygulamasından ekleme":::

## <a name="authorization"></a><a name="authorization"></a> Yetkisi

Bir Azure İşlev Uygulaması içeri aktarıldığında aşağıdakiler otomatik olarak oluşturulur:

* İşlev Uygulaması içindeki ana bilgisayar anahtarı apim-{*Azure API Management hizmet örneğiniz* adı},
* Oluşturulan ana bilgisayar anahtarını içeren, Azure API Management örneği içindeki adlandırılmış değer {*azure işlev uygulaması örneğiniz* adı}-anahtar.

4 Nisan 2019 ' den sonra oluşturulan API 'Ler için, ana bilgisayar anahtarı API Management olan HTTP isteklerinde bir başlıktaki İşlev Uygulaması geçirilir. Eski API 'Ler, ana bilgisayar anahtarını [bir sorgu parametresi](../azure-functions/functions-bindings-http-webhook-trigger.md#api-key-authorization)olarak iletir. Bu davranışı, `PATCH Backend` işlev uygulaması Ilişkili *arka uç* varlığındaki [REST API çağrısı](/rest/api/apimanagement/2019-12-01/backend/update#backendcredentialscontract) aracılığıyla değiştirebilirsiniz.

> [!WARNING]
> Azure İşlev Uygulaması ana bilgisayar anahtarı değerini ya da Azure API Management adlandırılmış değeri kaldırmak veya değiştirmek, hizmetler arasındaki iletişimi bozacaktır. Değerler otomatik olarak eşitlenmez.
>
> Ana bilgisayar anahtarını döndürmeniz gerekirse Azure API Management'taki adlandırılmış değerin de değiştirildiğinden emin olun.

### <a name="access-azure-function-app-host-key"></a>Azure İşlev Uygulaması ana bilgisayar anahtarına erişim

1. Azure İşlev Uygulaması örneğinize gidin.

    :::image type="content" source="./media/import-function-app-as-api/keys-01.png" alt-text="Işlev uygulaması örneğinizi seçmeyi vurgulayan ekran görüntüsü.":::

2. Yan gezinti menüsünün **işlevler** bölümünde **uygulama anahtarları**' nı seçin.

    :::image type="content" source="./media/import-function-app-as-api/keys-02b.png" alt-text="Işlev uygulamaları ayarları seçeneğini vurgulayan ekran görüntüsü.":::

3. **Ana bilgisayar anahtarları** bölümünün altındaki anahtarları bulun.

    :::image type="content" source="./media/import-function-app-as-api/keys-03.png" alt-text="Ana bilgisayar anahtarları bölümünü vurgulayan ekran görüntüsü.":::

### <a name="access-the-named-value-in-azure-api-management"></a>Azure API Management'taki adlandırılmış değere erişim

Azure API Management örneğinize gidin ve soldaki menüden **Adlandırılmış değerler**'i seçin. Azure İşlev Uygulaması anahtarı burada depolanır.

:::image type="content" source="./media/import-function-app-as-api/api-named-value.png" alt-text="İşlev Uygulamasından ekleme":::

## <a name="test-the-new-api-in-the-azure-portal"></a><a name="test-in-azure-portal"></a> Azure portal yeni API 'YI test etme

İşlemleri doğrudan Azure portaldan çağırabilirsiniz. Azure portalı kullanarak bir API'nin işlemlerini kolayca görüntüleyebilir ve test edebilirsiniz.  

:::image type="content" source="./media/import-function-app-as-api/test-api.png" alt-text="Test yordamını vurgulayan ekran görüntüsü.":::

1. Bir önceki bölümde oluşturduğunuz API'yi seçin.

2. **Test** sekmesini seçin.

3. Test etmek istediğiniz işlemi seçin.

    * Sayfada sorgu parametreleri ve üst bilgiler için alanlar görüntülenir. 
    * Bu API ile ilişkili ürün abonelik anahtarı için üst bilgilerden biri "OCP-apim-Subscription-Key" dır. 
    * API Management örneğinin Oluşturucusu olarak, zaten bir Yöneticinizde, anahtar otomatik olarak doldurulur. 

4. **Gönder**’i seçin.

    * Sınama başarılı olduğunda, arka uç **200 Tamam** ve bazı veriler ile yanıt verir.

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Yayımlanan API’yi dönüştürme ve koruma](transform-api.md)
