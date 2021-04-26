---
title: SharePoint Online Dizin oluşturucuyu yapılandırma (Önizleme)
titleSuffix: Azure Cognitive Search
description: Azure Bilişsel Arama 'de belge kitaplığı içeriğinin dizinini oluşturmayı otomatik hale getirmek için bir SharePoint Online Dizin Oluşturucu ayarlayın.
manager: luisca
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/01/2021
ms.openlocfilehash: 5888a7cc8aa58d1c6edab191e1243ebc60000fd6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105048876"
---
# <a name="how-to-configure-sharepoint-online-indexing-in-cognitive-search-preview"></a>Bilişsel Arama 'da SharePoint Online Dizin oluşturmayı yapılandırma (Önizleme)

> [!IMPORTANT] 
> SharePoint Online desteği şu anda **geçitli genel önizlemededir**. [Bu formu](https://aka.ms/azure-cognitive-search/indexer-preview)doldurarak geçitli önizlemeye erişim isteğinde bulabilirsiniz.
>
> Önizleme işlevselliği, bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yükleri için önerilmez. Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
> 
> [REST API sürüm 2020-06-30-önizleme](search-api-preview.md) bu özelliği sağlar. Şu anda portal veya SDK desteği yok.

> [!NOTE]
> SharePoint Online, belge düzeyinde Kullanıcı başına erişimi belirleyen ayrıntılı bir yetkilendirme modelini destekler. SharePoint Online Indexer bu izinleri arama dizinine çekmez ve Bilişsel Arama belge düzeyinde yetkilendirmeyi desteklemez. SharePoint Online 'dan bir arama hizmeti olarak bir belge dizine eklendiğinde, içerik, dizine okuma erişimi olan herkes tarafından kullanılabilir. Belge düzeyi izinlerine ihtiyacınız varsa, yetkisiz içeriğin sonuçlarını kırpmak için güvenlik filtrelerini araştırmanız gerekir. Daha fazla bilgi için bkz. [Active Directory kimlikleri kullanarak güvenlik kırpması](search-security-trimming-for-azure-search-with-aad.md).

Bu makalede, SharePoint Online belge kitaplıklarında saklanan belgeleri (PDF 'Ler, Microsoft Office belgeler ve diğer birçok yaygın biçim gibi) Azure Bilişsel Arama dizinine bağlamak için Azure Bilişsel Arama nasıl kullanılacağı açıklanır. İlk olarak, Dizin oluşturucuyu ayarlama ve yapılandırma hakkında temel bilgileri açıklar. Daha sonra, büyük olasılıkla karşılaştığınız davranış ve senaryolar hakkında daha ayrıntılı bir araştırma sunar.

## <a name="functionality"></a>İşlev
Azure Bilişsel Arama 'de bir Dizin Oluşturucu, veri kaynağından aranabilir verileri ve meta verileri çıkaran bir gezgin. SharePoint Online Indexer, SharePoint Online sitenize bağlanır ve bir veya daha fazla belge kitaplığındaki belgeleri dizinleyecek. Dizin Oluşturucu aşağıdaki işlevleri sağlar:
+ Bir veya daha fazla SharePoint Online belge kitaplığı 'ndan içerik dizini oluştur.
+ Azure Bilişsel Arama hizmetinizdeki aynı kiracıda yer alan SharePoint Online belge kitaplıklarından içerik dizini. Dizin Oluşturucu, Azure Bilişsel Arama hizmetinizin farklı kiracısındaki SharePoint siteleriyle çalışmaz. 
+ Dizin Oluşturucu, belge kitaplığındaki hangi içeriğin değiştirildiğini belirleyecek ve gelecekteki dizin oluşturma çalıştırmalarının yalnızca güncelleştirilmiş içeriğin dizinini belirleyen artımlı Dizin oluşturmayı destekleyecektir. Örneğin, ilk olarak Dizin Oluşturucu tarafından 5 PDF dizini oluşturulduysa 1 güncellenir, ardından Dizin Oluşturucu yeniden çalıştırılır, Dizin Oluşturucu yalnızca güncelleştirilmiş 1 PDF 'yi dizine alınır.
+ Metin ve normalleştirilmiş görüntüler, varsayılan olarak, dizini oluşturulmuş belgelerden ayıklanır. İsteğe bağlı olarak, daha fazla içerik zenginleştirme için işlem hattına bir beceri eklenebilir. Becerileri hakkında daha fazla bilgi [Azure bilişsel arama makalesinde beceri kavramlarını](cognitive-search-working-with-skillsets.md)bulabilirsiniz.

## <a name="supported-document-formats"></a>Desteklenen belge biçimleri

Azure Bilişsel Arama SharePoint Online Indexer, aşağıdaki belge biçimlerinden metin ayıklayabilir:

[!INCLUDE [search-document-data-sources](../../includes/search-blob-data-sources.md)]

## <a name="incremental-indexing-and-deletion-detection"></a>Artımlı dizin oluşturma ve silme algılaması
Varsayılan olarak, SharePoint Online Indexer artımlı dizinlemeyi destekler, bu da belge kitaplığındaki hangi içeriğin değiştirildiğini belirler ve gelecekteki dizin oluşturma çalıştırmaları üzerinde yalnızca güncelleştirilmiş içeriği dizine alır. Örneğin, ilk olarak Dizin Oluşturucu tarafından 5 Word belgesi dizine alınmışsa 1 güncellenir, ardından Dizin Oluşturucu yeniden çalıştırılır, Dizin Oluşturucu yalnızca güncelleştirilmiş 1 Word belgesini yeniden dizine alacak.

Silme algılaması de varsayılan olarak desteklenir. Bu, bir belge bir SharePoint Online belge kitaplığından silinirse, dizin oluşturucunun gelecekteki bir Dizin Oluşturucu çalışırken silmeyi algılayacağı ve belgeyi dizinden kaldırması anlamına gelir.

## <a name="setting-up-sharepoint-online-indexing"></a>SharePoint Online dizin oluşturma ayarlanıyor
SharePoint Online Dizin oluşturucuyu ayarlamak için Azure portal bazı eylemleri ve önizleme REST API kullanarak bazı eylemleri gerçekleştirmeniz gerekir. Bu önizleme SDK tarafından desteklenmiyor.

### <a name="step-1-enable-system-assigned-managed-identity"></a>1. Adım: sistem tarafından atanan yönetilen kimliği etkinleştirme
Bir sistem tarafından atanan yönetilen kimlik etkinleştirildiğinde Azure, arama hizmetiniz için Dizin Oluşturucu tarafından kullanılabilen bir kimlik oluşturur.

![Sistem tarafından atanan yönetilen kimliği etkinleştir](media/search-howto-index-sharepoint-online/enable-managed-identity.png "Sistem tarafından atanan yönetilen kimliği etkinleştir")

**Kaydet** ' i seçtikten sonra, arama hizmetinize atanmış BIR nesne kimliği görürsünüz.

![Sistem tarafından atanan yönetilen kimlik](media/search-howto-index-sharepoint-online/system-assigned-managed-identity.png "Sistem tarafından atanan yönetilen kimlik")

### <a name="step-2-create-an-aad-application"></a>2. Adım: AAD uygulaması oluşturma
SharePoint Online Indexer, kimlik doğrulaması için bu AAD uygulamasını kullanacaktır. 

1.  [Azure Portal](https://portal.azure.com/)gidin.

1.  Ana sayfanın sol tarafındaki menüyü açın ve **Azure Active Directory** **uygulama kayıtları** seçin. **+ Yeni kayıt**' ı seçin.
    1.  Uygulamanız için bir ad girin.
    2.  **Tek bir kiracı** seçin.
    3.  Yeniden yönlendirme URI 'SI gerekli değil.
    4.  **Kaydol** ' u seçin

1.  Sol taraftaki menüden **API izinleri** ' ni seçin, ardından **bir izin ekleyin** ve ardından **temsilci izinleri** **Microsoft Graph** . Aşağıdaki API izinlerini ekleyin: 
    1.  **Temsilci-dosyalar. Read. All** 
    2.  **Temsilci-siteler. Read. All** 
    3.  **Temsilci-User. Read**

    ![Temsil edilen API izinleri](media/search-howto-index-sharepoint-online/delegated-api-permissions.png "Temsil edilen API izinleri")

    Temsilci izinleri kullanmak, dizin oluşturucunun Kullanıcı bağlamındaki SharePoint sitesine erişebileceği anlamına gelir. Bu nedenle, Dizin oluşturucuyu çalıştırdığınızda, yalnızca oturum açan kullanıcının erişimi olan içeriğe erişebilir. Dizin Oluşturucu oluşturulurken veya tarih kaynağı güncelleştirilirken Kullanıcı oturumu açma gerçekleşir. Oturum açma adımı bu makalenin ilerleyen kısımlarında açıklanmıştır.

1.  **Kimlik doğrulama** sekmesini seçin. **Ortak istemci akışlarına Izin ver** ' i **Evet** olarak ayarlayıp **Kaydet**' i seçin.

1.  **+ Platform Ekle**' yi ve ardından **mobil ve Masaüstü uygulamaları**' nı seçin ve ardından `https://login.microsoftonline.com/common/oauth2/nativeclient` **Yapılandır**' ı işaretleyin.

    ![AAD uygulama kimlik doğrulama yapılandırması](media/search-howto-index-sharepoint-online/aad-app-authentication-configuration.png "AAD uygulama kimlik doğrulama yapılandırması")

1.  Yönetici onayı verin (yalnızca belirli kiracılar için gereklidir).

    Bazı kiracılar, bu temsil edilen API izinleri için yönetici onayı gerekli olacak şekilde kilitlenir. Böyle bir durum söz konusu olduğunda, Dizin oluşturucuyu oluşturmadan önce bu AAD uygulaması için yönetici yetkisi verme izniniz olması gerekir. 

    Her kiracı Bu gereksinime sahip olmadığı için öncelikle bu adımı atlıyoruz ve yönergelerle devam etmeniz önerilir. Dizin oluşturucuyu oluştururken yönetici onayı gerekli olduğunda, kimlik doğrulamasının kimlik doğrulamasını onaylaması gerektiğini söyleyen kimlik doğrulaması başarısız olur. Bu durumda, aşağıdaki düğmeyi kullanarak bir kiracı yöneticisinin izin vermesini sağlayabilirsiniz.

    ![AAD uygulaması yönetici izni verme](media/search-howto-index-sharepoint-online/aad-app-grant-admin-consent.png "AAD uygulaması yönetici izni verme")

<a name="create-data-source"></a>

### <a name="step-3-create-data-source"></a>3. Adım: veri kaynağı oluşturma
> [!IMPORTANT] 
> Bu bölümden başlayarak, geri kalan adımlar için Önizleme REST API kullanmanız gerekir. Azure Bilişsel Arama REST API hakkında bilginiz yoksa, bu [hızlı](search-get-started-rest.md)başlangıca göz atacağız.

Veri kaynağı, hangi verilerin dizine erişmesi gerektiğini, verilere erişmek için gereken kimlik bilgilerini ve verilerdeki değişiklikleri verimli bir şekilde belirlemek için ilkeleri (yeni, değiştirilmiş veya silinen satırlar) belirler. Aynı arama hizmetinde birden çok Dizin Oluşturucu tarafından bir veri kaynağı kullanılabilir.

SharePoint Dizin oluşturma için veri kaynağı aşağıdaki gerekli özelliklere sahip olmalıdır:
+ **ad** , arama hizmetinizin içindeki veri kaynağının benzersiz adıdır.
+ **tür** "SharePoint" olmalıdır. Bu, büyük/küçük harfe duyarlıdır.
+ **kimlik bilgileri** , SharePoint Online uç noktasını ve AAD uygulaması (ISTEMCI) kimliğini sağlar. Örnek bir SharePoint Online uç noktası `https://microsoft.sharepoint.com/teams/MySharePointSite` . SharePoint sitenizin ana sayfasına giderek ve URL 'YI tarayıcıdan kopyalayarak SharePoint Online uç noktasını edinebilirsiniz.
+ **kapsayıcı** hangi belge kitaplığının dizine alınacağı belirtir. Kapsayıcının oluşturulması hakkında daha fazla bilgi bu belgenin [hangi belgelerin dizine alınacağını denetlemek](#controlling-which-documents-are-indexed) bölümünde bulunabilir.

Bir veri kaynağı oluşturmak için:

```http
POST https://[service name].search.windows.net/datasources?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [admin key]

{
    "name" : "sharepoint-datasource",
    "type" : "sharepoint",
    "credentials" : { "connectionString" : "SharePointOnlineEndpoint=[SharePoint Online site url];ApplicationId=[AAD App ID]" },
    "container" : { "name" : "defaultSiteLibrary", "query" : null }
}
```

### <a name="step-4-create-an-index"></a>4. Adım: Dizin oluşturma
Dizin, bir belge, öznitelik ve arama deneyimini şekillendirip diğer yapıların alanlarını belirtir.

Belge kitaplığındaki belgelerden ayıklanan metni depolamak için aranabilir içerik alanıyla bir dizin oluşturma:

```http
POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "sharepoint-index",
    "fields": [
        { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
        { "name": "metadata_spo_item_name", "type": "Edm.String", "key": false, "searchable": true, "filterable": false, "sortable": false, "facetable": false },
        { "name": "metadata_spo_item_path", "type": "Edm.String", "key": false, "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "metadata_spo_item_content_type", "type": "Edm.String", "key": false, "searchable": false, "filterable": true, "sortable": false, "facetable": true },
        { "name": "metadata_spo_item_last_modified", "type": "Edm.DateTimeOffset", "key": false, "searchable": false, "filterable": false, "sortable": true, "facetable": false },
        { "name": "metadata_spo_item_size", "type": "Edm.Int64", "key": false, "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
    ]
}

```

Daha fazla bilgi için bkz. [Dizin oluşturma (REST API)](/rest/api/searchservice/create-index).

### <a name="step-5-create-an-indexer"></a>5. Adım: Dizin Oluşturucu oluşturma
Dizin Oluşturucu bir veri kaynağını hedef arama diziniyle bağlar ve veri yenilemeyi otomatikleştirmek için bir zamanlama sağlar. Dizin ve veri kaynağı oluşturulduktan sonra Dizin oluşturucuyu oluşturmaya hazırsınız demektir!

Bu bölümde, SharePoint sitesine erişimi olan kuruluş kimlik bilgilerinizle oturum açmanız istenir. Mümkünse, yeni bir kurumsal Kullanıcı hesabı oluşturmanızı ve bu yeni kullanıcıya dizin oluşturucunun sahip olmasını istediğiniz tam izinleri vermemiz önerilir.

Dizin oluşturucuyu oluşturmaya yönelik birkaç adım vardır:

1.  Dizin oluşturucuyu oluşturma girişimi için bir istek gönderin.

    ```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30-Preview
    Content-Type: application/json
    api-key: [admin key]
    
        {
          "name" : "sharepoint-indexer",
          "dataSourceName" : "sharepoint-datasource",
          "targetIndexName" : "sharepoint-index",
          "fieldMappings" : [
            { 
              "sourceFieldName" : "metadata_spo_site_library_item_id", 
              "targetFieldName" : "id", 
              "mappingFunction" : { 
                "name" : "base64Encode" 
              } 
            }
          ]
        }
    
    ```

1.  Dizin oluşturucuyu ilk kez oluştururken başarısız olur ve aşağıdaki hatayı görürsünüz. Hata iletisindeki bağlantıya gidin. Bağlantıya 10 dakika içinde gitmezseniz kodun süresi dolacaktır ve [veri kaynağını](#create-data-source)yeniden oluşturmanız gerekir.

    ```http
    {
        "error": {
            "code": "",
            "message": "Error with data source: To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code <CODE> to authenticate.  Please adjust your data source definition in order to proceed."
        }
    }
    ```

1.  Hata iletisinde belirtilen kodu girin.

    ![Cihaz kodunu girin](media/search-howto-index-sharepoint-online/enter-device-code.png "Cihaz kodunu girin")

1.  SharePoint Indexer, SharePoint içeriğine oturum açmış kullanıcı olarak erişir. Bu adım sırasında oturum açan kullanıcı oturum açmış kullanıcı olacaktır. Bu nedenle, dizin oluşturma istediğiniz belge kitaplığındaki bir belgeye erişimi olmayan bir kullanıcı hesabıyla oturum açarsanız, dizin oluşturucunun bu belgeye erişimi olmayacaktır.

    Mümkünse, yeni bir kullanıcı hesabı oluşturmanızı ve bu yeni kullanıcıya dizin oluşturucunun sahip olmasını istediğiniz tam izinleri vermeniz önerilir.

1.  İstenen izinleri onaylayın.

    ![API izinlerini Onayla](media/search-howto-index-sharepoint-online/aad-app-approve-api-permissions.png "API izinlerini Onayla")

1.  Dizin Oluşturucu oluşturma isteğini yeniden gönderin. Bu süre, isteğin başarılı olması gerekir. 

    ```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30-Preview
    Content-Type: application/json
    api-key: [admin key]
    
    {
        "name" : "sharepoint-indexer",
        "dataSourceName" : "sharepoint-datasource",
        "targetIndexName" : "sharepoint-index"
    }
    ```

### <a name="step-6-check-the-indexer-status"></a>6. Adım: Dizin Oluşturucu durumunu denetleme
Dizin Oluşturucu oluşturulduktan sonra, aşağıdaki isteği yaparak Dizin Oluşturucu durumunu kontrol edebilirsiniz.

```http
GET https://[service name].search.windows.net/indexers/sharepoint-indexer/status?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [admin key]
```

Dizin Oluşturucu durumu hakkında daha fazla bilgiye buradan [ulaşabilirsiniz: Dizin Oluşturucu durumunu Al](/rest/api/searchservice/get-indexer-status).

## <a name="updating-the-data-source"></a>Veri kaynağını güncelleştirme
Veri kaynağı nesnesinde güncelleştirme yoksa, Dizin Oluşturucu herhangi bir kullanıcı etkileşimi olmadan bir zamanlamaya göre çalıştırılabilir. Ancak, Azure Bilişsel Arama veri kaynağı nesnesi her güncelleştirildiği zaman, dizin oluşturucunun çalışması için yeniden oturum açmanız gerekir. Örneğin, veri kaynağı sorgusunu değiştirirseniz, `https://microsoft.com/devicelogin` ve yeni bir kod kullanarak yeniden oturum açmanız gerekir.

Veri kaynağı güncelleştirildikten sonra aşağıdaki adımları izleyin:

1.  Dizin oluşturucunun çalıştırılmasını el ile başlatın.

    ```http
    POST https://[service name].search.windows.net/indexers/sharepoint-indexer/run?api-version=2020-06-30-Preview  
    Content-Type: application/json
    api-key: [admin key]
    ```

    Dizin Oluşturucu çalıştırma isteği hakkında daha fazla bilgiye buradan ulaşabilirsiniz: [Dizin oluşturucuyu çalıştırın](/rest/api/searchservice/run-indexer).

1.  Dizin Oluşturucu durumunu denetleyin. Son Dizin Oluşturucu çalıştırmasında gidilecek bir hata varsa `https://microsoft.com/devicelogin` , bu sayfaya gidin ve yeni kodu sağlayın. 

    ```http
    GET https://[service name].search.windows.net/indexers/sharepoint-indexer/status?api-version=2020-06-30-Preview
    Content-Type: application/json
    api-key: [admin key]
    ```

    Dizin Oluşturucu durumu hakkında daha fazla bilgiye buradan [ulaşabilirsiniz: Dizin Oluşturucu durumunu Al](/rest/api/searchservice/get-indexer-status).

1.  Oturum aç

1.  Bir dizin oluşturucuyu el ile yeniden başlatın ve Dizin Oluşturucu durumunu denetleyin. Bu kez, dizin oluşturucunun çalışması başarıyla başlamalıdır.

## <a name="indexing-document-metadata"></a>Belge meta verilerini dizine alma
Dizin oluşturucuyu belge meta verilerini dizin olarak ayarladıysanız, dizin için aşağıdaki meta veriler kullanılır.

> [!NOTE]
> Özel meta veriler önizlemenin geçerli sürümüne dahil değildir.

| Tanımlayıcı | Tür | Description | 
| ------------- | -------------- | ----------- |
| metadata_spo_site_library_item_id | Edm.String | Site KIMLIĞI, kitaplık KIMLIĞI ve öğe KIMLIĞI, bir site için bir belge kitaplığında bir öğeyi benzersiz şekilde tanımlayan birleşim anahtarı. |
| metadata_spo_site_id | Edm.String | SharePoint Online sitesinin KIMLIĞI. |
| metadata_spo_library_id | Edm.String | Belge kitaplığının KIMLIĞI. |
| metadata_spo_item_id | Edm.String | Kitaplıktaki (belge) öğe KIMLIĞI. |
| metadata_spo_item_last_modified | Edm.DateTimeOffset | Öğenin son değiştirilme tarihi/saati (UTC). |
| metadata_spo_item_name | Edm.String | Öğenin adı. |
| metadata_spo_item_size | Edm.Int64 | Öğenin boyutu (bayt cinsinden). | 
| metadata_spo_item_content_type | Edm.String | Öğenin içerik türü. | 
| metadata_spo_item_extension | Edm.String | Öğenin uzantısı. |
| metadata_spo_item_weburi | Edm.String | Öğenin URI 'SI. |
| metadata_spo_item_path | Edm.String | Üst yolun ve öğe adının birleşimi. | 

SharePoint Online Indexer, her belge türüne özgü meta verileri de destekler. [Azure bilişsel arama 'de kullanılan içerik meta verileri özelliklerinde](search-blob-metadata-properties.md)daha fazla bilgi bulabilirsiniz.

<a name="controlling-which-documents-are-indexed"></a>

## <a name="controlling-which-documents-are-indexed"></a>Hangi belgelerin dizine alınacağını denetleme
Tek bir SharePoint Online Indexer, bir veya daha fazla belge kitaplığındaki içerikleri dizinedebilir. Dizinlemek istediğiniz belge kitaplıklarını göstermek için veri kaynağınızı oluştururken *kapsayıcı* parametresini kullanın. Veri kaynağı *kapsayıcısının* iki özelliği vardır: *ad* ve *sorgu*. 

### <a name="name"></a>Name
*Name* özelliği gereklidir ve üç değerden biri olmalıdır:
+ *defaultSiteLibrary*
    + Sitelerin varsayılan belge kitaplığındaki tüm içeriği dizine oluştur.
+   *allSiteLibraries*
    + Bir sitedeki tüm belge kitaplıklarının tüm içeriğini dizine koyun. Bu, belge kitaplıklarını bir alt siteden dizinetmez. Bu, ancak *sorguda* belirlenebilir.
+   *useQuery*
    + Yalnızca *sorguda* tanımlanan dizin içeriği.

### <a name="query"></a>Sorgu
*Sorgu* özelliği anahtar sözcük/değer çiftlerinden oluşur. Aşağıdakiler, kullanılabilecek anahtar sözcüklerdir. Değerler, site URL 'leri veya belge kitaplığı URL 'lardır.

> [!NOTE]
> Belirli bir anahtar sözcük için değeri almak üzere SharePoint Online 'ı bir tarayıcıda açmanızı öneririz. Bu, URL 'yi tarayıcıdan eklemek/dışlamak ve kopyalamak istediğiniz belge kitaplığına geziniyor. Bu, sorgudaki bir anahtar sözcükle kullanılacak değeri almanın en kolay yoludur.

| Sözcükle | Sorgu açıklaması | Örnek |
| ------------- | -------------- | ----------- |
| null | Null veya boş ise, kapsayıcı adına bağlı olarak varsayılan belge kitaplığını veya tüm belge kitaplıklarını dizine bırakın. | Varsayılan site kitaplığındaki tüm içeriği dizine oluştur: <br><br>  ``` "container" : { "name" : "defaultSiteLibrary", "query" : null } ``` |
| ıncludekütüphanariesınsıte | Bağlantı dizesinde tanımlı sitede bulunan tüm kitaplıkların içeriğini dizine oluştur. Bunlar sitenizin alt siteleriyle sınırlıdır <br><br> Bu anahtar sözcük için *sorgu* değeri sitenin veya alt sitenin URI 'si olmalıdır. | Tüm içeriği sitem içindeki tüm belge kitaplıklarından dizine oluştur. <br><br> ``` "container" : { "name" : "useQuery", "query" : "includeLibrariesInSite=https://mycompany.sharepoint.com/mysite" } ``` |
| ıncludelibrary | Bu kitaplıktan içerik dizini oluştur. <br><br> Bu anahtar sözcük için *sorgu* değeri aşağıdaki biçimlerden birinde olmalıdır: <br><br> Örnek 1: <br><br> *ıncludelibrary = [site veya alt site]/[belge kitaplığı]* <br><br> Örnek 2: <br><br> Tarayıcıdan kopyalanmış olan URI. | MyDocumentLibrary 'deki tüm içeriği dizine oluştur: <br><br> Örnek 1: <br><br> ``` "container" : { "name" : "useQuery", "query" : "includeLibrary=https://mycompany.sharepoint.com/mysite/MyDocumentLibrary" } ``` <br><br> Örnek 2: <br><br> ``` "container" : { "name" : "useQuery", "query" : "includeLibrary=https://mycompany.sharepoint.com/teams/mysite/MyDocumentLibrary/Forms/AllItems.aspx" } ``` |
| excludeLibrary |  Bu kitaplıktan içerik dizini kullanmayın. <br><br> Bu anahtar sözcük için *sorgu* değeri aşağıdaki biçimlerden birinde olmalıdır: <br><br> Örnek 1: <br><br> *excludeLibrary = [site veya alt site URI]/[belge kitaplığı]* <br><br> Örnek 2: <br><br> Tarayıcıdan kopyalanmış olan URI. | MyDocumentLibrary hariç tüm Kitaplıklarım arasından tüm içeriği dizine oluştur: <br><br> Örnek 1: <br><br> ``` "container" : { "name" : "useQuery", "query" : "includeLibrariesInSite=https://mysite.sharepoint.com/subsite1; excludeLibrary=https://mysite.sharepoint.com/subsite1/MyDocumentLibrary" } ``` <br><br> Örnek 2: <br><br> ``` "container" : { "name" : "useQuery", "query" : "includeLibrariesInSite=https://mycompany.sharepoint.com/teams/mysite; excludeLibrary=https://mycompany.sharepoint.com/teams/mysite/MyDocumentLibrary/Forms/AllItems.aspx" } ``` |

## <a name="index-by-file-type"></a>Dosya türüne göre dizin
Hangi belgelerin dizine alınacağını ve hangilerinin atlandığını denetleyebilirsiniz.

### <a name="include-documents-having-specific-file-extensions"></a>Belirli dosya uzantılarına sahip belgeleri dahil et
Dizin Oluşturucu yapılandırma parametresini kullanarak yalnızca belirttiğiniz dosya adı uzantılarına sahip belgeleri dizine alabilirsiniz `indexedFileNameExtensions` . Değer, dosya uzantılarının virgülle ayrılmış bir listesini içeren bir dizedir (önde gelen noktayla). Örneğin, yalnızca ' ı dizine eklemek için. PDF ve. DOCX belgeleri, bunu yapın:

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [admin key]

{
    ... other parts of indexer definition
    "parameters" : { "configuration" : { "indexedFileNameExtensions" : ".pdf,.docx" } }
}
```

### <a name="exclude-documents-having-specific-file-extensions"></a>Belirli dosya uzantılarına sahip belgeleri hariç tut
Yapılandırma parametresini kullanarak, belirli dosya adı uzantılarına sahip belgeleri dizin oluşturma işleminden dışlayabilirsiniz `excludedFileNameExtensions` . Değer, dosya uzantılarının virgülle ayrılmış bir listesini içeren bir dizedir (önde gelen noktayla). Örneğin, ile hariç tüm içerikleri dizine eklemek için. PNG ve. JPEG uzantıları, bunu yapın:

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [admin key]

{
    ... other parts of indexer definition
    "parameters" : { "configuration" : { "excludedFileNameExtensions" : ".png,.jpeg" } }
}
```

Hem hem `indexedFileNameExtensions` de `excludedFileNameExtensions` parametreleri varsa, Azure bilişsel arama önce `indexedFileNameExtensions` ' e, sonra da ' a bakar `excludedFileNameExtensions` . Bu, aynı dosya uzantısının her iki listede de mevcutsa, dizin oluşturma işleminin dışlanacağını belirtir.

## <a name="handling-errors"></a>Hataları işleme
Varsayılan olarak, SharePoint Online Indexer, desteklenmeyen içerik türüne (örneğin, bir görüntü) sahip bir belge ile karşılaştığında yanıt vermez. Tabii ki, `excludedFileNameExtensions` belirli içerik türlerini atlamak için parametresini kullanabilirsiniz. Ancak, önceden tüm olası içerik türlerini bilmeden belge dizinleyebilirsiniz. Desteklenmeyen bir içerik türüyle karşılaşıldığında dizine almaya devam etmek için `failOnUnsupportedContentType` yapılandırma parametresini false olarak ayarlayın:

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [admin key]

{
    ... other parts of indexer definition
    "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false } }
}
```

Bazı belgelerde Azure Bilişsel Arama, içerik türünü belirleyemiyor veya başka türlü desteklenen içerik türündeki bir belgeyi işleyemez. Bu hata modunu yoksaymak için `failOnUnprocessableDocument` yapılandırma parametresini false olarak ayarlayın:

```http
"parameters" : { "configuration" : { "failOnUnprocessableDocument" : false } }
```

Azure Bilişsel Arama, dizini oluşturulmuş belgelerin boyutunu sınırlandırır. Bu sınırlar [Azure bilişsel arama hizmet sınırları](./search-limits-quotas-capacity.md)bölümünde belgelenmiştir. Büyük boyutlu belgeler varsayılan olarak hata olarak değerlendirilir. Ancak, `indexStorageMetadataOnlyForOversizedDocuments` yapılandırma parametresini doğru olarak ayarlarsanız, büyük boyutlu belgelerin depolama meta verilerini dizine almaya devam edebilirsiniz:

```http
"parameters" : { "configuration" : { "indexStorageMetadataOnlyForOversizedDocuments" : true } }
```

Ayrıca, belgeler ayrıştırılırken veya bir dizine belge eklerken herhangi bir işlem noktasında hatalar meydana geliyorsa dizin oluşturmaya devam edebilirsiniz. Belirli sayıda hatayı yoksaymak için, `maxFailedItems` ve `maxFailedItemsPerBatch` yapılandırma parametrelerini istenen değerlere ayarlayın. Örnek:

```http
{
    ... other parts of indexer definition
    "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 10 }
}
```

## <a name="see-also"></a>Ayrıca bkz.
+ [Azure Bilişsel Arama'daki Dizin Oluşturucular](search-indexer-overview.md)
+ [Azure Bilişsel Arama 'de kullanılan içerik meta verileri özellikleri](search-blob-metadata-properties.md)