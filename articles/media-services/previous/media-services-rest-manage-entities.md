---
title: REST ile Media Services varlıkları yönetme | Microsoft Docs
description: Bu makalede REST API Media Services varlıkların nasıl yönetileceği gösterilmektedir.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 95262a32-0f2a-4286-b9e2-1a1ca6399b5b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: inhenkel
ms.openlocfilehash: 2821c4c2b13edaf5dfb34664c4b9f78ab56d98a7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103015565"
---
# <a name="managing-media-services-entities-with-rest"></a>REST ile Media Services varlıklarını yönetme

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [REST](media-services-rest-manage-entities.md)
> * [.NET](media-services-dotnet-manage-entities.md)
> 
> 

Microsoft Azure Media Services, OData v3 üzerinde oluşturulmuş REST tabanlı bir hizmettir. Varlıkları diğer bir OData hizmetinde olduğu gibi ekleyebilir, sorgulayabilir, güncelleştirebilir ve silebilirsiniz. Özel durumlar, uygun olduğunda çağrılacaktır. OData hakkında daha fazla bilgi için bkz. [Open Data Protocol documentation](https://www.odata.org/documentation/).

Bu konuda, Azure Media Services varlıkların REST ile nasıl yönetileceği gösterilmektedir.

>[!NOTE]
> 1 Nisan 2017’den itibaren, hesabınızdaki 90 günden eski olan tüm İş kayıtları, toplam kayıt sayısı üst kota sınırının altında olsa bile ilişkili Görev kayıtlarıyla birlikte otomatik olarak silinecektir. Örneğin, 1 Nisan 2017 ' de hesabınızda 31 Aralık 2016 ' den eski olan Iş kayıtları otomatik olarak silinir. İş/görev bilgilerini arşivlemek gerekirse, bu konuda açıklanan kodu kullanabilirsiniz.

## <a name="considerations"></a>Dikkat edilmesi gerekenler  

Media Services varlıklara erişirken, HTTP isteklerinizin belirli üstbilgi alanlarını ve değerlerini ayarlamanız gerekir. Daha fazla bilgi için bkz. [Media Services REST API Geliştirme Için kurulum](media-services-rest-how-to-use.md).

## <a name="connect-to-media-services"></a>Media Services’e bağlanmak

AMS API 'sine bağlanma hakkında daha fazla bilgi için bkz. [Azure AD kimlik doğrulamasıyla Azure MEDIA SERVICES API 'Sine erişme](media-services-use-aad-auth-to-access-ams-api.md). 

## <a name="adding-entities"></a>Varlık ekleme
Media Services içindeki her varlık, HTTP POST isteği aracılığıyla varlıklar gibi bir varlık kümesine eklenir.

Aşağıdaki örnek, bir AccessPolicy oluşturmayı gösterir.

```console
POST https://media.windows.net/API/AccessPolicies HTTP/1.1
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN> 
Host: media.windows.net
Content-Length: 74
Expect: 100-continue

{"Name": "DownloadPolicy", "DurationInMinutes" : "300", "Permissions" : 1}
```

## <a name="querying-entities"></a>Varlıkları sorgulama
Varlıkların sorgulanması ve listelenmesi basittir ve yalnızca GET HTTP isteği ve isteğe bağlı OData işlemleri içerir.
Aşağıdaki örnek, tüm MediaProcessor varlıklarının bir listesini alır.

```console
GET https://media.windows.net/API/MediaProcessors HTTP/1.1
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN> 
Host: media.windows.net
```

Ayrıca, belirli bir varlığı veya belirli bir varlıkla ilişkili tüm varlık kümelerini (örneğin, aşağıdaki örneklerde) alabilirsiniz:

```console
GET https://media.windows.net/API/JobTemplates('nb:jtid:UUID:e81192f5-576f-b247-b781-70a790c20e7c') HTTP/1.1
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN> 
Host: media.windows.net

GET https://media.windows.net/API/JobTemplates('nb:jtid:UUID:e81192f5-576f-b247-b781-70a790c20e7c')/TaskTemplates HTTP/1.1
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN> 
Host: media.windows.net
```

Aşağıdaki örnek yalnızca tüm Işlerin durum özelliğini döndürür.

```console
GET https://media.windows.net/API/Jobs?$select=State HTTP/1.1
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN> 
Host: media.windows.net
```

Aşağıdaki örnek, "SampleTemplate" adlı tüm JobTemplates ' i döndürür.

```console
GET https://media.windows.net/API/JobTemplates?$filter=startswith(Name,%20'SampleTemplate') HTTP/1.1
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN> 
Host: media.windows.net
```

> [!NOTE]
> $Expand işlem, LINQ hususları 'nda (WCF Veri Hizmetleri) açıklanan desteklenmeyen LINQ yöntemlerinde ve Media Services desteklenmez.
> 
> 

## <a name="enumerating-through-large-collections-of-entities"></a>Büyük varlık koleksiyonlarını sıralama
Varlıkları sorgularken, genel REST v2 sorgu sonuçlarını 1000 sonuçla sınırladığından, tek seferde döndürülen 1000 varlıkların bir sınırı vardır. Büyük varlık koleksiyonunu sıralamak için **Atla** ve **üst** ' i kullanın. 

Aşağıdaki örnek, ilk 2000 işi atlamak ve sonraki 1000 işi almak için **Skip** ve **top** 'ın nasıl kullanılacağını gösterir.  

```console
GET https://media.windows.net/api/Jobs()?$skip=2000&$top=1000 HTTP/1.1
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN>
Host: media.windows.net
```

## <a name="updating-entities"></a>Varlıklar güncelleştiriliyor
Varlık türüne ve içinde olduğu duruma bağlı olarak, bu varlıktaki özellikleri bir yama, PUT veya HTTP istekleri ile BIrLEŞTIr aracılığıyla güncelleştirebilirsiniz. Bu işlemler hakkında daha fazla bilgi için bkz. [Patch/put/Merge](/openspecs/windows_protocols/ms-odata/59d5abd3-7b12-490a-a0e2-9d9324b91893).

Aşağıdaki kod örneği bir varlık varlığındaki ad özelliğinin nasıl güncelleşbir olduğunu gösterir.

```console
MERGE https://media.windows.net/API/Assets('nb:cid:UUID:80782407-3f87-4e60-a43e-5e4454232f60') HTTP/1.1
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN>
Host: media.windows.net
Content-Length: 21
Expect: 100-continue

{"Name" : "NewName" }
```

## <a name="deleting-entities"></a>Varlıkları silme
Varlıklar, SILME HTTP isteği kullanılarak Media Services silinebilir. Varlığa bağlı olarak, varlıkları silmenin sırası önemli olabilir. Örneğin, varlıklar gibi varlıklar, varlığı silmeden önce söz konusu varlığa başvuran tüm Konumlandırıcı 'yı iptal etmeniz (veya silmeniz) gerekir.

Aşağıdaki örnek, blob depolamaya bir dosyayı karşıya yüklemek için kullanılan bir bulucunun nasıl silineceğini gösterir.

```console
DELETE https://media.windows.net/API/Locators('nb:lid:UUID:76dcc8e8-4230-463d-97b0-ce25c41b5c8d') HTTP/1.1
Content-Type: application/json;odata=verbose
Accept: application/json;odata=verbose
DataServiceVersion: 3.0
MaxDataServiceVersion: 3.0
x-ms-version: 2.19
Authorization: Bearer <ENCODED JWT TOKEN>
Host: media.windows.net
Content-Length: 0
```

## <a name="media-services-learning-paths"></a>Media Services’i öğrenme yolları
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Geribildirim gönderme
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
