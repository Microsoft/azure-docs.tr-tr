---
title: Blob dizini etiketleriyle Azure blob verilerini yönetme ve bulma (Önizleme)
description: Blob nesnelerini kategorilere ayırmak, yönetmek ve sorgulamak için blob dizini etiketlerini nasıl kullanacağınızı öğrenin.
author: twooley
ms.author: twooley
ms.date: 03/18/2021
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.reviewer: klaasl
ms.custom: references_regions
ms.openlocfilehash: 2188aaea0cf5a4616291d3fdad839aefb2dbc413
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106280709"
---
# <a name="manage-and-find-azure-blob-data-with-blob-index-tags-preview"></a>Blob dizini etiketleriyle Azure blob verilerini yönetme ve bulma (Önizleme)

Veri kümeleri daha büyükse, bir veri Sea içindeki belirli bir nesnenin bulunması zor olabilir. Blob dizin etiketleri, anahtar-değer dizin etiketi özniteliklerini kullanarak veri yönetimi ve bulma özellikleri sağlar. Nesneleri tek bir kapsayıcı içinde veya depolama hesabınızdaki tüm kapsayıcılar arasında kategorilere ayırıp bulabilirsiniz. Veri gereksinimleri değiştikçe nesneler, dizin etiketleri güncelleştirilerek dinamik olarak kategorize edilebilir. Nesneler, geçerli kapsayıcı kuruluşlarıyla yerinde kalabilir.

Blob dizini etiketleri şunları yapmanızı sağlar:

- Anahtar-değer dizin etiketlerini kullanarak bloblarınızı dinamik olarak kategorilere ayırın
- Tüm depolama hesabı genelinde belirli etiketli Blobları hızlıca bulun
- Dizin etiketlerinin değerlendirmesini temel alan blob API 'Leri için koşullu davranışları belirtin
- [BLOB yaşam döngüsü yönetimi](storage-lifecycle-management-concepts.md) gibi özelliklerde gelişmiş denetimler için Dizin etiketlerini kullanın

Depolama hesabınızda birçok farklı uygulama tarafından erişilen milyonlarca blob 'un bulunduğu bir senaryoyu düşünün. Tek bir projeden ilgili tüm verileri bulmak istiyorsunuz. Veriler farklı adlandırma kurallarıyla birden çok kapsayıcıya yayılabilecek kapsamda ne olduğundan emin değilsiniz. Ancak, uygulamalarınız tüm verileri, projelerini temel alarak, Etiketler ile karşıya yükler. Milyonlarca blob 'u aramak ve adları ve özellikleri karşılaştırmak yerine, `Project = Contoso` bulma ölçütleriniz olarak kullanabilirsiniz. Blob dizini, tüm depolama hesabınızdaki tüm kapsayıcıları filtreleyecek ve yalnızca 50 Blobları kümesini hızlı bir şekilde bulup döndürecek `Project = Contoso` .

> [!IMPORTANT]
> Blob dizini etiketleri şu anda **Önizleme** aşamasındadır ve tüm genel bölgelerde kullanılabilir. Beta, önizleme veya henüz genel kullanıma sunulmayan Azure özelliklerine uygulanan yasal koşullara yönelik [Microsoft Azure önizlemeleri Için ek kullanım koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 'na bakın.

Blob dizininin nasıl kullanılacağına ilişkin örneklere başlamak için bkz. [verileri yönetmek ve bulmak için blob dizini etiketlerini kullanma](storage-blob-index-how-to.md).

## <a name="blob-index-tags-and-data-management"></a>Blob dizin etiketleri ve veri yönetimi

Kapsayıcı ve BLOB adı önekleri tek boyutlu kategorileştirsahiptir. Blob dizin etiketleri, [BLOB veri türleri (blok, ekleme veya sayfa)](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)için çok boyutlu kategorilere izin verir. Çok boyutlu kategori oluşturma işlemi, Azure Blob depolama tarafından yerel olarak dizinlenir, böylece verilerinizi hızlıca bulabilirsiniz.

Depolama hesabınızda aşağıdaki beş blobu göz önünde bulundurun:

- *kapsayıcı1/transaction.csv*
- *container2/campaign.docx*
- *Fotoğraflar/bannerphoto.png*
- *arşivleri/tamamlandı/2019review.pdf*
- *günlük/2020/01/01/logfile.txt*

Bu Bloblar *kapsayıcı/sanal klasör/blob adı* öneki kullanılarak ayrılır. Bu beş blobda bir dizin etiketi özniteliğini, `Project = Contoso` geçerli ön ek kuruluşlarını koruyarak birlikte kategorilere ayırmak için ayarlayabilirsiniz. Dizin etiketlerini eklemek, dizini kullanarak verileri filtreleme ve bulma özelliğini ortaya çıkaran verileri taşıma gereksinimini ortadan kaldırır.

## <a name="setting-blob-index-tags"></a>Blob dizini etiketlerini ayarlama

Blob dizin etiketleri, depolama hesabınızda yeni veya mevcut nesnelere uygulanabilen anahtar-değer öznitelikleridir. [BLOB 'U koy](/rest/api/storageservices/put-blob), [PUT blok listesi](/rest/api/storageservices/put-block-list)veya [kopyalama blobu](/rest/api/storageservices/copy-blob) işlemlerini ve isteğe bağlı üstbilgiyi kullanarak karşıya yükleme işlemi sırasında dizin etiketlerini belirtebilirsiniz `x-ms-tags` . Depolama hesabınızda zaten bloblarınız varsa, bir biçimli XML belgesini istek gövdesinde Dizin etiketleriyle geçirerek [BLOB etiketlerini ayarla](/rest/api/storageservices/set-blob-tags) ' yı çağırın.

> [!IMPORTANT]
> Blob dizini etiketlerini ayarlama, [Depolama Blobu veri sahibi](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) tarafından ve BLOB 'un etiketlerine (SAS izni) erişim izni olan bir paylaşılan erişim imzasına sahip herkes tarafından gerçekleştirilebilir `t` .
>
> Ayrıca, izni olan RBAC kullanıcıları `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` Bu işlemi gerçekleştirebilir.

Veri işlemenin ne zaman tamamlandığını anlatmak için bloba tek bir etiket uygulayabilirsiniz.

> "processedDate" = ' 2020-01-01 '

Verileri daha açıklayıcı bir şekilde yapmak için bloba birden çok etiket uygulayabilirsiniz.

> "Proje" = ' contoso '  
> "Sınıflandırılmış" = ' true '  
> "Durum" = ' Işlenmemiş '  
> "Öncelik" = ' 01 '

Var olan dizin etiketi özniteliklerini değiştirmek için, var olan etiket özniteliklerini alın, Tag özniteliklerini değiştirin ve öğesini [BLOB etiketlerini ayarla](/rest/api/storageservices/set-blob-tags) işlemiyle değiştirin. Blob 'dan tüm dizin etiketlerini kaldırmak için, `Set Blob Tags` belirtilen etiket öznitelikleri olmadan işlemi çağırın. Blob dizin etiketleri blob veri içeriklerine bir alt kaynak olduğundan, `Set Blob Tags` temel alınan içerikleri değiştirmez ve Blobun son değiştirilme zamanı veya ETag öğesini değiştirmez. Tüm geçerli temel Bloblar ve önceki sürümler için dizin etiketleri oluşturabilir veya değiştirebilirsiniz. Ancak, anlık görüntüler veya geçici silinen bloblarda bulunan Etiketler değiştirilemez.

Aşağıdaki sınırlar blob dizin etiketleri için geçerlidir:

- Her blob en fazla 10 blob Dizin etiketine sahip olabilir
- Etiket anahtarları bir ve 128 karakter arasında olmalıdır
- Etiket değerleri sıfır ile 256 karakter arasında olmalıdır
- Etiket anahtarları ve değerleri büyük/küçük harfe duyarlıdır
- Etiket anahtarları ve değerleri yalnızca dize veri türlerini destekler. Tüm sayılar, tarihler, saatler veya özel karakterler dizeler olarak kaydedilir
- Etiket anahtarları ve değerleri aşağıdaki adlandırma kurallarına uymalıdır:
  - Alfasayısal karakterler:
    - **a** - **z** (küçük harfler)
    - **A** - **Z** (büyük harfler)
    - **0** - **9** (sayı)
  - Geçerli özel karakterler: boşluk, artı, eksi, nokta, iki nokta, eşittir, alt çizgi, eğik çizgi ( ` +-.:=_/` )

## <a name="getting-and-listing-blob-index-tags"></a>Blob dizini etiketlerini alma ve listeleme

Blob dizin etiketleri, blob verileriyle birlikte bir alt kaynak olarak depolanır ve temel alınan blob veri içeriğinden bağımsız olarak alınabilir. Tek bir Blobun blob dizin etiketleri, [BLOB etiketlerini al](/rest/api/storageservices/get-blob-tags) işlemiyle alınabilir. Parametreli [Blobları Listele](/rest/api/storageservices/list-blobs) işlemi, `include:tags` BLOB Dizin etiketleriyle birlikte bir kapsayıcı içindeki tüm Blobları da döndürür.

> [!IMPORTANT]
> Blob dizini etiketlerini alma ve listeleme, [Depolama Blobu veri sahibi](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) tarafından ve BLOB 'un etiketlerine (SAS izni) erişim izni olan bir paylaşılan erişim imzası olan herkese karşı gerçekleştirilebilir `t` .
>
> Ayrıca, izni olan RBAC kullanıcıları `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/read` Bu işlemi gerçekleştirebilir.

En az bir blob Dizin etiketine sahip tüm Bloblar için, `x-ms-tag-count` [liste Blobları](/rest/api/storageservices/list-blobs)' nda döndürülür, [blobu alır](/rest/api/storageservices/get-blob)ve Blobun Dizin etiketlerinin sayısını belirten [BLOB özellikleri al](/rest/api/storageservices/get-blob-properties) işlemleri.

## <a name="finding-data-using-blob-index-tags"></a>Blob dizini etiketlerini kullanarak veri bulma

Dizin oluşturma altyapısı, anahtar-değer öznitelerinizi çok boyutlu bir dizine sunar. Dizin etiketlerinizi ayarladıktan sonra blob üzerinde bulunur ve anında alınabilir. Blob dizini güncelleştirmelerden önce bu işlem biraz zaman alabilir. Blob dizini güncelleştirildikten sonra, BLOB depolama tarafından sunulan yerel sorgu ve bulma yeteneklerini kullanabilirsiniz.

[Etiketlere göre blob bulma](/rest/api/storageservices/find-blobs-by-tags) işlemi, dizin etiketleri belirli bir sorgu ifadesiyle eşleşen filtrelenmiş bir blob kümesi almanızı sağlar. `Find Blobs by Tags` , Depolama hesabınızdaki tüm kapsayıcılar üzerinde filtrelemeyi destekler veya filtrelemeyi yalnızca tek bir kapsayıcıya kapsamını tanımlayabilirsiniz. Tüm Dizin etiketi anahtarları ve değerleri dizeler olduğundan, ilişkisel işleçler bir lexicographic sıralaması kullanır.

> [!IMPORTANT]
> Blob dizini etiketlerini kullanarak veri bulma işlemi, [Depolama Blobu veri sahibi](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) tarafından ve BLOB 'ları etiketlere (SAS izni) göre bulma izni olan bir paylaşılan erişim imzasına sahip herkes tarafından gerçekleştirilebilir `f` .
>
> Ayrıca, izni olan RBAC kullanıcıları `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/filter/action` Bu işlemi gerçekleştirebilir.

Blob dizini filtrelemesi için aşağıdaki ölçütler geçerlidir:

- Etiket anahtarları çift tırnak içine alınmalıdır (")
- Etiket değerleri ve kapsayıcı adları tek tırnak (') içine alınmalıdır
- @ Karakterine yalnızca belirli bir kapsayıcı adı üzerinde filtreleme için izin verilir (örneğin, `@container = 'ContainerName'` )
- Filtreler, dizeler üzerinde lexıgrafgraphic sıralaması ile uygulanır
- Aynı anahtar üzerinde aynı taraflı aralıklı işlemler geçersiz (örneğin, `"Rank" > '10' AND "Rank" >= '15'` )
- Bir filtre ifadesi oluşturmak için REST kullanılırken, karakterlerin URI kodlamalı olması gerekir

Aşağıdaki tabloda, için tüm geçerli operatörler gösterilmektedir `Find Blobs by Tags` :

|  İşleç  |  Açıklama  | Örnek |
|------------|---------------|---------|
|     =      |     Eşittir     | `"Status" = 'In Progress'` |
|     >      |  Büyüktür | `"Date" > '2018-06-18'` |
|     >=     |  Büyüktür veya eşittir | `"Priority" >= '5'` |
|     <      |  Küçüktür   | `"Age" < '32'` |
|     <=     |  Küçüktür veya eşittir  | `"Company" <= 'Contoso'` |
|    AND     |  Mantıksal and  | `"Rank" >= '010' AND "Rank" < '100'` |
| @container | Belirli bir kapsayıcının kapsamı | `@container = 'videofiles' AND "status" = 'done'` |

> [!NOTE]
> Etiketler üzerinde ayarlama ve sorgulama yaparken naicografik sıralaması hakkında bilgi sahibi olun.
>
> - Sayılar mektuplardan önce sıralanır. Sayılar ilk basamağa göre sıralanır.
> - Büyük harfler küçük harflerden önce sıralanır.
> - Semboller standart değildir. Bazı semboller sayısal değerlerden önce sıralanır. Diğer semboller, harften önce veya sonra sıralanır.

## <a name="conditional-blob-operations-with-blob-index-tags"></a>Blob dizini etiketleriyle koşullu blob işlemleri

REST sürümleri 2019-10-10 ve üzeri sürümlerde, çoğu [BLOB hizmeti API 'leri](/rest/api/storageservices/operations-on-blobs) artık `x-ms-if-tags` , belirtilen blob dizini koşulu karşılandığında işlemin başarılı olacağı şekilde koşullu bir üst bilgiyi desteklemektedir. Koşul karşılanmazsa, alacaksınız `error 412: The condition specified using HTTP conditional header(s) is not met` .

`x-ms-if-tags`Üst bilgi, var olan DIĞER http koşullu üstbilgileri (IF-Match, If-None-Match vb.) ile birleştirilebilir. Bir istekte birden fazla koşullu başlık sağlanmışsa, işlemin başarılı olması için hepsi true olarak değerlendirilmelidir. Tüm koşullu üstbilgiler mantıksal ve ile etkili bir şekilde birleştirilir.

Aşağıdaki tabloda, koşullu işlemler için geçerli işleçler gösterilmektedir:

|  İşleç  |  Açıklama  | Örnek |
|------------|---------------|---------|
|     =      |     Eşittir     | `"Status" = 'In Progress'` |
|     <>     |   Eşit değildir   | `"Status" <> 'Done'` |
|     >      |  Büyüktür | `"Date" > '2018-06-18'` |
|     >=     |  Büyüktür veya eşittir | `"Priority" >= '5'` |
|     <      |  Küçüktür   | `"Age" < '32'` |
|     <=     |  Küçüktür veya eşittir  | `"Company" <= 'Contoso'` |
|    AND     |  Mantıksal and  | `"Rank" >= '010' AND "Rank" < '100'` |
|     VEYA     | Mantıksal or   | `"Status" = 'Done' OR "Priority" >= '05'` |

> [!NOTE]
> `x-ms-if-tags`BLOB işlemlerine yönelik koşullu üst bilgide izin verilen ancak işlem içinde mevcut olmayan iki ek işleç (eşit ve mantıksal or) vardır `Find Blobs by Tags` .

## <a name="platform-integrations-with-blob-index-tags"></a>Blob dizini etiketleriyle platform tümleştirmeleri

Blob dizin etiketleri yalnızca BLOB verilerinizi sınıflandırmanız, yönetmeniz ve aramanız, ayrıca [yaşam döngüsü yönetimi](storage-lifecycle-management-concepts.md)gibi diğer BLOB depolama özellikleriyle tümleştirme sağlamanıza yardımcı olur.

### <a name="lifecycle-management"></a>Yaşam döngüsü yönetimi

`blobIndexMatch`Yaşam döngüsü yönetiminde bir kural filtresi olarak kullanarak, verileri daha soğuk katmanlara taşıyabilir veya bloblarınıza uygulanan Dizin etiketlerine göre verileri silebilirsiniz. Kurallarınızda daha ayrıntılı olabilir ve yalnızca belirtilen Etiketler ölçütleriyle eşleşiyorsa Blobları taşıyabilir veya silebilirsiniz.

Etiketli verilere yönelik eylemler uygulamak için yaşam döngüsü kuralında bağımsız bir filtre kümesi olarak bir blob dizini eşleşmesi ayarlayabilirsiniz. Ya da daha belirli veri kümeleriyle eşleştirmek için hem ön eki hem de bir blob dizinini birleştirebilirsiniz. Yaşam döngüsü kuralında birden çok filtre belirtilmesi, mantıksal bir ve işlemi uygular. Eylem yalnızca *Tüm* filtre ölçütleri eşleşiyorsa geçerlidir.

Aşağıdaki örnek yaşam döngüsü yönetim kuralı, *videofler* adlı bir kapsayıcıda blok Blobları için geçerlidir. Kural, yalnızca veriler blob Dizin etiketi ölçütleriyle eşleşiyorsa depolamayı arşivlemek için Bloblar `"Status" == 'Processed' AND "Source" == 'RAW'` .

# <a name="portal"></a>[Portal](#tab/azure-portal)

![Azure portal 'de yaşam döngüsü yönetimi için blob dizin eşleştirme kuralı örneği](media/storage-blob-index-concepts/blob-index-lifecycle-management-example.png)

# <a name="json"></a>[JSON](#tab/json)

```json
{
    "rules": [
        {
            "enabled": true,
            "name": "ArchiveProcessedSourceVideos",
            "type": "Lifecycle",
            "definition": {
                "actions": {
                    "baseBlob": {
                        "tierToArchive": {
                            "daysAfterModificationGreaterThan": 0
                        }
                    }
                },
                "filters": {
                    "blobIndexMatch": [
                        {
                            "name": "Status",
                            "op": "==",
                            "value": "Processed"
                        },
                        {
                            "name": "Source",
                            "op": "==",
                            "value": "RAW"
                        }
                    ],
                    "blobTypes": [
                        "blockBlob"
                    ],
                    "prefixMatch": [
                        "videofiles/"
                    ]
                }
            }
        }
    ]
}
```

---

## <a name="permissions-and-authorization"></a>İzinler ve yetkilendirme

Aşağıdaki yaklaşımlardan birini kullanarak blob dizini etiketlerine erişim yetkisi verebilirsiniz:

- Azure Active Directory (Azure AD) güvenlik sorumlusu için izin vermek üzere Azure rol tabanlı erişim denetimi (Azure RBAC) kullanma. Azure AD 'yi üstün güvenlik ve kullanım kolaylığı için kullanın. Azure AD 'yi blob işlemleriyle kullanma hakkında daha fazla bilgi için bkz. [Azure Active Directory kullanarak bloblara ve kuyruklara erişim yetkisi verme](../common/storage-auth-aad.md).
- Blob dizinine erişim yetkisi vermek için paylaşılan erişim imzası (SAS) kullanma. Paylaşılan erişim imzaları hakkında daha fazla bilgi için bkz. [paylaşılan erişim imzaları (SAS) kullanarak Azure depolama kaynaklarına sınırlı erişim verme](../common/storage-sas-overview.md).
- Paylaşılan anahtarla işlemleri yetkilendirmek için hesap erişim anahtarlarını kullanma. Daha fazla bilgi için bkz. [paylaşılan anahtarla yetkilendirme](/rest/api/storageservices/authorize-with-shared-key).

Blob dizin etiketleri, blob verilerine bir alt kaynaktır. Blob 'ları okuma veya yazma için izinleri olan bir kullanıcının veya bir SAS belirtecinin blob Dizin etiketlerine erişimi olmayabilir.

### <a name="role-based-access-control"></a>Rol tabanlı erişim denetimi

[Azure ad kimliği](../common/storage-auth-aad.md) kullanan çağıranlar, blob dizini etiketlerinde çalışmak için aşağıdaki izinlere sahip olabilir.

| Blob Dizin etiketi işlemleri                                          | Azure RBAC eylemi                                                             |
|--------------------------------------------------------------------|-------------------------------------------------------------------------------|
| [Blob etiketlerini ayarla](/rest/api/storageservices/set-blob-tags)           | Microsoft. Storage/storageAccounts/blobServices/kapsayıcılar/blob/Etiketler/yazma    |
| [Blob etiketlerini al](/rest/api/storageservices/get-blob-tags)           | Microsoft. Storage/storageAccounts/blobServices/kapsayıcılar/blob/Etiketler/okuma     |
| [Blob 'Ları etiketlere göre bul](/rest/api/storageservices/find-blobs-by-tags) | Microsoft. Storage/storageAccounts/blobServices/kapsayıcılar/Bloblar/filtre/eylem |

Dizin etiketi işlemleri için, temel alınan blob verilerinden ayrı ek izinler gereklidir. [Depolama Blobu veri sahibi](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) rolüne, üç blob Dizin etiketi işlemi için izin verilir. [Depolama Blobu veri okuyucusuna](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader) yalnızca ve işlemler için izin verilir `Find Blobs by Tags` `Get Blob Tags` .

### <a name="sas-permissions"></a>SAS izinleri

[Paylaşılan erişim imzası (SAS)](../common/storage-sas-overview.md) kullanan çağıranlara, blob dizini etiketlerinde çalışmak için kapsamlı izinler verilebilir.

#### <a name="blob-sas"></a>Blob SAS

Blob Dizin etiketlerine erişime izin vermek için bir blob SAS içinde aşağıdaki izinler verilebilir. Blob okuma ve yazma izinleri tek başına Dizin etiketlerinin okunmasına veya yazılmasına izin vermek için yeterli değildir.

| İzin | URI simgesi | İzin verilen işlemler                |
|------------|------------|-----------------------------------|
| Dizin etiketleri |     t      | Blob için dizin etiketleri al ve ayarla |

#### <a name="container-sas"></a>Kapsayıcı SAS

Blob etiketlerinde filtrelemeye izin vermek için bir kapsayıcı SAS içinde aşağıdaki izinler verilebilir. `Blob List`İzin, blob 'ların Dizin etiketlerine göre filtrelemesine izin vermek için yeterli değil.

| İzin | URI simgesi | İzin verilen işlemler         |
|------------|------------|----------------------------|
| Dizin etiketleri |     f      | Dizin etiketleriyle blob bulma |

## <a name="choosing-between-metadata-and-blob-index-tags"></a>Meta veriler ve BLOB dizin etiketleri arasında seçim yapma

Blob dizini etiketleri ve meta veriler, bir blob kaynağıyla birlikte rastgele Kullanıcı tanımlı anahtar-değer özelliklerini depolamanıza olanak sağlar. Her ikisi de, Blobun içeriğini döndürmeden veya değiştirmeden doğrudan alınabilir ve ayarlanabilir. Hem Metadata hem de dizin etiketlerini kullanmak mümkündür.

Yalnızca dizin etiketleri otomatik olarak dizinlenir ve yerel BLOB depolama hizmeti tarafından aranabilir yapılır. Meta veriler yerel olarak dizinlenemez veya aranmaz. [Azure Search](../../search/search-blob-ai-integration.md)gibi ayrı bir hizmet kullanmanız gerekir. Blob dizini etiketlerinin, temel alınan blob verilerinden ayrı okuma, filtreleme ve yazma için ek izinleri vardır. Meta veriler blob ile aynı izinleri kullanır ve [Get blob](/rest/api/storageservices/get-blob) ve [Get blob özelliklerini al](/rest/api/storageservices/get-blob-properties) işlemleri tarafından http üstbilgileri olarak döndürülür. Blob dizin etiketleri, [Microsoft tarafından yönetilen bir anahtar](../common/storage-service-encryption.md)kullanılarak Rest 'te şifrelenir. Meta veriler, blob verileri için belirtilen şifreleme anahtarı kullanılarak Rest 'de şifrelenir.

Aşağıdaki tabloda meta veriler ve BLOB dizin etiketleri arasındaki farklılıklar özetlenmektedir:

|              |   Meta veri   |   Blob dizin etiketleri  |
|--------------|--------------|--------------------|
| **Sınırlar**      | Sayısal sınır yok, 8 KB toplam, büyük/küçük harfe duyarsız | blob başına en fazla 10 etiket, etiket başına 768 bayt, büyük/küçük harfe duyarlı |
| **Güncelleştirmeler**    | Arşiv katmanında izin verilmiyor, `Set Blob Metadata` var olan tüm meta verileri değiştirir, `Set Blob Metadata` Blobun son değiştirilme süresini değiştirir | Tüm erişim katmanları için izin verilen `Set Blob Tags` tüm mevcut etiketlerin yerini alır, `Set Blob Tags` Blobun son değiştirilme zamanını değiştirmez |
| **Depolama**     | Blob verileriyle birlikte depolanır | Blob verilerinin alt kaynağı |
| **Dizin oluşturma & sorgulama** | Azure Search gibi ayrı bir hizmet kullanılmalıdır | Blob depolamada yerleşik olan özellikleri dizinleme ve sorgulama |
| **Şifreleme** | Blob verileri için kullanılan aynı şifreleme anahtarıyla Rest 'de şifrelendi | Microsoft tarafından yönetilen bir şifreleme anahtarıyla Rest 'te şifrelendi |
| **Fiyatlandırma** | Meta verilerin boyutu bir blob için depolama maliyetlerine dahildir | Dizin etiketi başına sabit maliyet |
| **Üst bilgi yanıtı** | Ve içinde üst bilgi olarak döndürülen meta veriler `Get Blob``Get Blob Properties` | Veya tarafından döndürülen etiket `Get Blob` sayısı `Get Blob Properties` , yalnızca ve ile döndürülen Etiketler `Get Blob Tags``List Blobs` |
| **İzinler**  | Blob verilerine yönelik okuma veya yazma izinleri meta verilere genişletiliyor | Dizin etiketlerini okumak, filtrelemek veya yazmak için ek izinler gerekir |
| **Adlandırma** | Meta veri adlarının C# tanımlayıcıları için adlandırma kurallarına uyması gerekir | Blob dizin etiketleri, daha geniş bir alfasayısal karakter aralığını destekler |

## <a name="pricing"></a>Fiyatlandırma

Blob dizini fiyatlandırması genel önizlemede ve genel kullanıma yönelik değişikliğe tabidir. Bir depolama hesabı içindeki aylık ortalama Dizin etiketi sayısı üzerinden ücretlendirilirsiniz. Dizin oluşturma altyapısının maliyeti yoktur. `Set Blob Tags`, Ve için istekleri `Get Blob Tags` `Find Blobs by Tags` ilgili işlem türlerine göre ücretlendirilir. [Daha fazla bilgi için bkz. Blok Blobu fiyatlandırması](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="regional-availability-and-storage-account-support"></a>Bölgesel kullanılabilirlik ve depolama hesabı desteği

Blob dizin etiketleri yalnızca, hiyerarşik ad alanı (HNS) devre dışı olan Genel Amaçlı v2 (GPv2) hesaplarında kullanılabilir. Genel Amaçlı (GPV1) hesapları desteklenmez, ancak herhangi bir GPv1 hesabını bir GPv2 hesabına yükseltebilirsiniz.

Dizin etiketleri Premium Depolama hesaplarında desteklenmez. Depolama hesapları hakkında daha fazla bilgi için bkz. [Azure depolama hesabına genel bakış](../common/storage-account-overview.md).

Blob dizini etiketleri şu anda tüm genel bölgelerde kullanılabilir.

Başlamak için bkz. [verileri yönetmek ve bulmak için blob dizini etiketlerini kullanma](storage-blob-index-how-to.md).

> [!IMPORTANT]
> Depolama hesaplarınızda blob Dizin önizlemesi 'ni kullanabilmeniz için aboneliğinizi kaydetmeniz gerekir. Bu makalenin [koşullar ve bilinen sorunlar](#conditions-and-known-issues) bölümüne bakın.

### <a name="register-your-subscription-preview"></a>Aboneliğinizi kaydetme (Önizleme)

Blob dizini etiketleri yalnızca genel önizlemede olduğundan, özelliği kullanabilmeniz için aboneliğinizi kaydetmeniz gerekir. Bir istek göndermek için aşağıdaki PowerShell veya CLı komutlarını çalıştırın.

#### <a name="register-by-using-powershell"></a>PowerShell kullanarak kaydetme

```powershell
Register-AzProviderFeature -FeatureName BlobIndex -ProviderNamespace Microsoft.Storage
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

#### <a name="register-by-using-azure-cli"></a>Azure CLı kullanarak kaydolun

```azurecli
az feature register --namespace Microsoft.Storage --name BlobIndex
az provider register --namespace 'Microsoft.Storage'
```

## <a name="conditions-and-known-issues"></a>Koşullar ve bilinen sorunlar

Bu bölümde, blob Dizin etiketlerinin genel önizlemede bilinen sorunlar ve koşullar açıklanmaktadır. Bu özellik, davranış değişebilir, genel kullanıma (GA) ulaşıncaya kadar üretim iş yükleri için kullanılmamalıdır.

- Önizleme için, önizleme bölgelerinde depolama hesabınız için blob dizinini kullanabilmeniz için önce aboneliğinizi kaydetmeniz gerekir.
- Önizlemede yalnızca GPv2 hesapları desteklenir. Blob, blok Blobstorage ve HNS etkin DataLake Gen2 hesapları desteklenmez. GPv1 hesapları desteklenmez.
- Sayfa bloblarını Dizin etiketleriyle karşıya yüklemek etiketleri kalıcı hale almaz. Sayfa Blobu karşıya yükledikten sonra etiketleri ayarlayın.
- Filtreleme tek bir kapsayıcının kapsamına eklendiğinde, `@container` yalnızca filtre ifadesindeki tüm dizin etiketleri eşitlik denetimleri ise (anahtar = değer) geçirilebilir.
- Aralık işlecini koşulla birlikte kullanırken `AND` , yalnızca aynı dizin etiketi anahtar adını ( `"Age" > '013' AND "Age" < '100'` ) belirtebilirsiniz.
- Sürüm oluşturma ve blob dizini desteklenmiyor. Blob dizin etiketleri sürümler için korunur ancak blob dizin altyapısına aktarılmaz.
- Dizin etiketlerinin dizine alınmış olup olmadığı saptanmayan bir API yok.
- Hesap yük devretmesi desteklenmez. Blob dizini yük devretmeden sonra düzgün şekilde güncelleştirilemez.
- Yaşam döngüsü yönetimi yalnızca blob dizini eşleşmesi olan eşitlik denetimlerini destekler.
- `Copy Blob` Kaynak blobundan yeni hedef Blobun blob dizini etiketlerini kopyalamaz. Kopyalama işlemi sırasında hedef bloba uygulanmasını istediğiniz etiketleri belirtebilirsiniz.
- `Copy Blob` Hedef bloba uygulanan etiketleri olan başka bir depolama hesabından (zaman uyumsuz kopya), blob Dizin altyapısının blob ve filtre kümesindeki etiketlerini döndürmelerine neden olur. `Copy Blob`URL 'den (eşitleme kopyası) kullanın.
- Anlık görüntü oluşturulurken Etiketler kalıcıdır. Ancak, bir anlık görüntüyü yükseltmek desteklenmez ve boş bir etiket kümesine neden olabilir.

## <a name="faq"></a>SSS

**Blob dizini, bloblarım içindeki içeriği filtrelemenize ve sorgularıma yardımcı olur mu?**

Hayır, blob verilerinizde arama yapmanız gerekiyorsa sorgu hızlandırma veya Azure Arama 'yı kullanın.

**Dizin etiketi değerlerinde herhangi bir gereksinim var mı?**

Blob dizin etiketleri yalnızca dize veri türlerini destekler ve sorgulıgraf sıralaması ile sonuçları döndürür. Sayılar için sayıyı sıfır olarak doldurur. Tarihler ve saatler için ISO 8601 ile uyumlu bir biçim olarak depolayın.

**Blob dizin etiketleri ve ilişkili Etiketler Azure Resource Manager?**

Hayır, Kaynak Yöneticisi Etiketler, abonelikler, kaynak grupları ve depolama hesapları gibi denetim düzlemi kaynaklarını düzenlemenize yardımcı olur. Dizin etiketleri veri düzleminde blob yönetimi ve bulma sağlar.

## <a name="next-steps"></a>Sonraki adımlar

Blob dizininin nasıl kullanılacağına ilişkin bir örnek için bkz. [verileri yönetmek ve bulmak için blob dizini kullanma](storage-blob-index-how-to.md).

[Yaşam döngüsü yönetimi](storage-lifecycle-management-concepts.md) hakkında bilgi edinin ve blob dizini eşleştirme ile bir kural ayarlayın.
