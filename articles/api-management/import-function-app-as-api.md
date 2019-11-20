---
title: Azure İşlev Uygulamalarını Azure API Management'a API olarak aktarma | Microsoft Docs
description: Bu öğreticide, bir Azure İşlev Uygulamasını Azure API Management'a API olarak nasıl aktaracağınız gösterilmektedir.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 06/28/2019
ms.author: apimpm
ms.openlocfilehash: 0c4a95669eea1b98baea5f9a866598e000c0923c
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74107854"
---
# <a name="import-an-azure-function-app-as-an-api-in-azure-api-management"></a>Azure İşlev Uygulamalarını Azure API Management'a API olarak aktarma

Azure API Management, Azure İşlev Uygulamalarının yeni API olarak içeri aktarılmasını veya var olan API'lere eklenmesini destekler. Bu işlemin ardından Azure İşlev Uygulamasında otomatik olarak bir ana bilgisayar anahtarı oluşturulur. Bu anahtar daha sonra Azure API Management'taki bir adlandırılmış değere atanır.

Bu makale, Azure İşlev Uygulamalarını Azure API Management'a API olarak aktarma konusunda yol göstermektedir. Ayrıca makalede test işlemi de açıklanmaktadır.

Şunları öğrenirsiniz:

> [!div class="checklist"]
> * Azure İşlev Uygulamalarını API olarak içeri aktarma
> * Azure İşlev Uygulamalarını API'lere ekleme
> * Yeni Azure İşlev Uygulaması ana bilgisayar anahtarını ve Azure API Management adlandırılmış değerini görüntüleme
> * Azure portalında API’yi test etme
> * API'yi geliştirici portalında test etme

## <a name="prerequisites"></a>Önkoşullar

* [Azure API Management örneği oluşturma](get-started-create-service-instance.md) hızlı başlangıcını tamamlayın.
* Aboneliğinizde bir Azure İşlevleri uygulaması bulunduğundan emin olun. Daha fazla bilgi için bkz. [Azure İşlev Uygulaması oluşturma](../azure-functions/functions-create-first-azure-function.md#create-a-function-app). Uygulamanın HTTP tetikleyicili İşlevler içermesi ve yetkilendirme düzeyinin *Anonim* veya *İşlev* olarak ayarlanması gerekir.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="add-new-api-from-azure-function-app"></a> Azure İşlev Uygulamalarını yeni API olarak içeri aktarma

Bir Azure İşlev Uygulamasından yeni API oluşturmak için aşağıdaki adımları uygulayın.

1. **Azure API Management** hizmet örneğinizde soldaki menüden **API'ler** seçeneğini belirleyin.

2. **Yeni API ekleyin** listesinde **İşlev Uygulaması**'nı seçin.

    ![İşlev Uygulamasından ekleme](./media/import-function-app-as-api/add-01.png)

3. İçeri aktarılacak İşlevleri seçmek için **Gözat**'a tıklayın.

    ![İşlev Uygulamasından ekleme](./media/import-function-app-as-api/add-02.png)

4. Kullanılabilir İşlev Uygulamaları listesinden seçim yapmak için **İşlev Uygulaması** bölümüne tıklayın.

    ![İşlev Uygulamasından ekleme](./media/import-function-app-as-api/add-03.png)

5. İşlevlerini içeri aktarmak istediğiniz İşlev Uygulamasını bulun, uygulamaya tıklayın ve **Seç**'e basın.

    ![İşlev Uygulamasından ekleme](./media/import-function-app-as-api/add-04.png)

6. İçeri aktarmak istediğiniz İşlevleri seçin ve **Seç**'e tıklayın.

    ![İşlev Uygulamasından ekleme](./media/import-function-app-as-api/add-05.png)

    > [!NOTE]
    > Yalnızca HTTP tetikleyicisine dayalı ve yetkilendirme düzeyi *Anonim* veya *İşlev* olarak ayarlanmış olan İşlevleri içeri aktarabilirsiniz.

7. **Tam** görünümüne geçiş yapın ve yeni API’nize **Ürün** atayın. Gerekirse, önceden doldurulmuş diğer alanları düzenleyin.

    ![İşlev Uygulamasından ekleme](./media/import-function-app-as-api/add-06.png)

8. **Oluştur**'a tıklayın.

## <a name="append-azure-function-app-to-api"></a> Azure İşlev Uygulamalarını var olan API'lere ekleme

Azure İşlev Uygulamasını var olan bir API'ye eklemek için aşağıdaki adımları uygulayın.

1. **Azure API Management** hizmet örneğinizde soldaki menüden **API'ler** seçeneğini belirleyin.

2. Azure İşlev Uygulamasından içeri aktarmak istediğiniz bir API'yi seçin. **...** simgesine tıklayın ve bağlam menüsünden **İçeri Aktar**'ı seçin.

    ![İşlev Uygulamasından ekleme](./media/import-function-app-as-api/append-01.png)

3. **İşlev Uygulaması** kutucuğuna tıklayın.

    ![İşlev Uygulamasından ekleme](./media/import-function-app-as-api/append-02.png)

4. Açılır pencerede **Gözat**'a tıklayın.

    ![İşlev Uygulamasından ekleme](./media/import-function-app-as-api/append-03.png)

5. Kullanılabilir İşlev Uygulamaları listesinden seçim yapmak için **İşlev Uygulaması** bölümüne tıklayın.

    ![İşlev Uygulamasından ekleme](./media/import-function-app-as-api/add-03.png)

6. İşlevlerini içeri aktarmak istediğiniz İşlev Uygulamasını bulun, uygulamaya tıklayın ve **Seç**'e basın.

    ![İşlev Uygulamasından ekleme](./media/import-function-app-as-api/add-04.png)

7. İçeri aktarmak istediğiniz İşlevleri seçin ve **Seç**'e tıklayın.

    ![İşlev Uygulamasından ekleme](./media/import-function-app-as-api/add-05.png)

8. **İçeri Aktar**’a tıklayın.

    ![İşlev Uygulamasından ekleme](./media/import-function-app-as-api/append-04.png)

## <a name="authorization"></a>Yetkisi

Bir Azure İşlev Uygulaması içeri aktarıldığında aşağıdakiler otomatik olarak oluşturulur:

* İşlev Uygulaması içindeki ana bilgisayar anahtarı apim-{*Azure API Management hizmet örneğiniz*adı},
* Oluşturulan ana bilgisayar anahtarını içeren, Azure API Management örneği içindeki adlandırılmış değer {*azure işlev uygulaması örneğiniz*adı}-anahtar.

4 Nisan 2019 ' den sonra oluşturulan API 'Ler için, ana bilgisayar anahtarı API Management olan HTTP isteklerinde bir başlıktaki İşlev Uygulaması geçirilir. Eski API 'Ler, ana bilgisayar anahtarını [bir sorgu parametresi](../azure-functions/functions-bindings-http-webhook.md#api-key-authorization)olarak iletir. Bu davranış, İşlev Uygulaması ilişkili *arka uç* varlığındaki `PATCH Backend` [REST API çağrısıyla](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/backend/update#backendcredentialscontract) değiştirilebilir.

> [!WARNING]
> Azure İşlev Uygulaması ana bilgisayar anahtarının veya Azure API Management adlandırılmış değerinin kaldırılması veya değiştirilmesi, hizmetler arasındaki iletişimin kopmasına neden olur. Değerler otomatik olarak eşitlenmez.
>
> Ana bilgisayar anahtarını döndürmeniz gerekirse Azure API Management'taki adlandırılmış değerin de değiştirildiğinden emin olun.

### <a name="access-azure-function-app-host-key"></a>Azure İşlev Uygulaması ana bilgisayar anahtarına erişim

1. Azure İşlev Uygulaması örneğinize gidin.

2. Genel bakıştan **İşlev Uygulaması ayarları**'nı seçin.

    ![İşlev Uygulamasından ekleme](./media/import-function-app-as-api/keys-02-a.png)

3. Anahtar, **Ana Bilgisayar Anahtarları** bölümünde bulunur.

    ![İşlev Uygulamasından ekleme](./media/import-function-app-as-api/keys-02-b.png)

### <a name="access-the-named-value-in-azure-api-management"></a>Azure API Management'taki adlandırılmış değere erişim

Azure API Management örneğinize gidin ve soldaki menüden **Adlandırılmış değerler**'i seçin. Azure İşlev Uygulaması anahtarı burada depolanır.

![İşlev Uygulamasından ekleme](./media/import-function-app-as-api/keys-01.png)

## <a name="test-in-azure-portal"></a>Azure portal yeni API 'YI test etme

İşlemleri doğrudan Azure portaldan çağırabilirsiniz. Azure portalı kullanarak bir API'nin işlemlerini kolayca görüntüleyebilir ve test edebilirsiniz.  

1. Bir önceki bölümde oluşturduğunuz API'yi seçin.

2. **Test** sekmesini seçin.

3. Bir işlem seçin.

    Sayfa, sorgu parametrelerinin ve üst bilgilerin alanlarını görüntüler. Bu API ile ilişkilendirilmiş ürünün abonelik anahtarı için, üst bilgilerden biri **Ocp-Apim-Subscription-Key** üst bilgisidir. API Management örneğini siz oluşturduysanız zaten bir yöneticisinizdir ve anahtar otomatik olarak doldurulur. 

4. **Gönder**’i seçin.

    Arka uç **200 OK** şeklinde ve bazı verilerle yanıt verir.

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Yayımlanan API’yi dönüştürme ve koruma](transform-api.md)
