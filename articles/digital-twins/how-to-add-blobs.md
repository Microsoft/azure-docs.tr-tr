---
title: Nesnelere blob ekleme-Azure dijital TWINS | Microsoft Docs
description: Azure dijital TWINS 'te kullanıcılara, cihazlara ve boşluklara blob ekleme hakkında bilgi edinin.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 11/13/2019
ms.custom: seodec18
ms.openlocfilehash: 6ab9d0ae07978e69bebb0fc24c8965cce971cfd5
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74082324"
---
# <a name="add-blobs-to-objects-in-azure-digital-twins"></a>Azure dijital TWINS 'de nesnelere blob ekleme

Blob 'lar, Resimler ve Günlükler gibi ortak dosya türlerinin yapılandırılmamış temsilleridir. Blob 'lar bir MIME türünü (örneğin, "image/jpeg") ve meta verileri (ad, açıklama, tür vb.) kullanarak temsil ettikleri veri türlerini izler.

Azure dijital TWINS, cihaz, boşluk ve kullanıcılara Blobları eklemeyi destekler. Blob 'lar, bir Kullanıcı, bir cihaz fotoğrafı, video, harita, bir bellenim zip, JSON verileri, günlük vb. için bir profil resmini temsil edebilir.

[!INCLUDE [Digital Twins Management API familiarity](../../includes/digital-twins-familiarity.md)]

## <a name="uploading-blobs-overview"></a>Bloblara genel bakış yükleme

Blob 'ları belirli uç noktalara ve ilgili işlevlere yüklemek için çok parçalı istekleri kullanabilirsiniz.

[!INCLUDE [Digital Twins multipart requests](../../includes/digital-twins-multipart.md)]

### <a name="blob-metadata"></a>Blob meta verileri

**Content-Type** ve **Content-Disposition**özelliklerine ek olarak, Azure dijital TWINS blob çok parçalı istekleri doğru JSON gövdesini belirtmelidir. Hangi JSON gövdesinin gönderileceği, gerçekleştirilen HTTP istek işlem türüne bağlıdır.

Dört adet ana JSON şemaları şunlardır:

[![JSON şemaları](media/how-to-add-blobs/blob-models-img.png)](media/how-to-add-blobs/blob-models-img.png#lightbox)

JSON blob meta verileri aşağıdaki modele uyar:

```JSON
{
    "parentId": "00000000-0000-0000-0000-000000000000",
    "name": "My First Blob",
    "type": "Map",
    "subtype": "GenericMap",
    "description": "A well chosen description",
    "sharing": "None"
  }
```

| Öznitelik | Tür | Açıklama |
| --- | --- | --- |
| **parentId** | Dize | Blobun ile ilişkilendirilecek üst varlık (boşluklar, cihazlar veya kullanıcılar) |
| **name** |Dize | Blob için insan dostu bir ad |
| **type** | Dize | Blob türü- *Type* ve *TypeId* kullanılamaz  |
| **typeId** | Tamsayı | Blob türü KIMLIĞI- *Type* ve *TypeId* kullanılamaz |
| **subtype** | Dize | Blob alt türü- *Subtype* ve *subtypeıd* kullanılamaz |
| **subtypeId** | Tamsayı | Blob 'un alt tür KIMLIĞI- *alt tür* ve *alt TypeId* kullanamaz |
| **description** | Dize | Blobun özelleştirilmiş açıklaması |
| **sharing** | Dize | Blobun paylaşılıp paylaşılamayacağını-enum [`None`, `Tree`, `Global`] |

Blob meta verileri her zaman **Içerik türü** `application/json` olan ilk öbek olarak veya bir `.json` dosyası olarak sağlanır. Dosya verileri ikinci öbekte sağlanır ve desteklenen herhangi bir MIME türünde olabilir.

Swagger belgelerinde, bu model şemaları tam ayrıntılı olarak açıklanmaktadır.

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

[Swagger 'Yi nasıl kullanacağınızı](./how-to-use-swagger.md)okuyarak başvuru belgelerini kullanma hakkında bilgi edinin.

### <a name="blobs-response-data"></a>Blob yanıt verileri

Ayrı olarak döndürülen blob 'lar aşağıdaki JSON şemasına uygundur:

```JSON
{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "string",
  "parentId": "00000000-0000-0000-0000-000000000000",
  "type": "string",
  "subtype": "string",
  "typeId": 0,
  "subtypeId": 0,
  "sharing": "None",
  "description": "string",
  "contentInfos": [
    {
      "type": "string",
      "sizeBytes": 0,
      "mD5": "string",
      "version": "string",
      "lastModifiedUtc": "2019-01-12T00:58:08.689Z",
      "metadata": {
        "additionalProp1": "string",
        "additionalProp2": "string",
        "additionalProp3": "string"
      }
    }
  ],
  "fullName": "string",
  "spacePaths": [
    "string"
  ]
}
```

| Öznitelik | Tür | Açıklama |
| --- | --- | --- |
| **id** | Dize | Blob için benzersiz tanımlayıcı |
| **name** |Dize | Blob için insan dostu bir ad |
| **parentId** | Dize | Blobun ile ilişkilendirilecek üst varlık (boşluklar, cihazlar veya kullanıcılar) |
| **type** | Dize | Blob türü- *Type* ve *TypeId* kullanılamaz  |
| **typeId** | Tamsayı | Blob türü KIMLIĞI- *Type* ve *TypeId* kullanılamaz |
| **subtype** | Dize | Blob alt türü- *Subtype* ve *subtypeıd* kullanılamaz |
| **subtypeId** | Tamsayı | Blob 'un alt tür KIMLIĞI- *alt tür* ve *alt TypeId* kullanamaz |
| **sharing** | Dize | Blobun paylaşılıp paylaşılamayacağını-enum [`None`, `Tree`, `Global`] |
| **description** | Dize | Blobun özelleştirilmiş açıklaması |
| **Contentınfos** | Dizi | Sürüm dahil yapılandırılmamış meta veri bilgilerini belirtir |
| **fullName** | Dize | Blobun tam adı |
| **spacePaths** | Dize | Boşluk yolu |

Blob meta verileri her zaman **Içerik türü** `application/json` olan ilk öbek olarak veya bir `.json` dosyası olarak sağlanır. Dosya verileri ikinci öbekte sağlanır ve desteklenen herhangi bir MIME türünde olabilir.

### <a name="blob-multipart-request-examples"></a>Blob çok parçalı istek örnekleri

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

Bir metin dosyasını blob olarak karşıya yüklemek ve bir boşlukla ilişkilendirmek için, kimliği doğrulanmış bir HTTP POST isteği oluşturun:

```plaintext
YOUR_MANAGEMENT_API_URL/spaces/blobs
```

Aşağıdaki gövdesi:

```plaintext
--USER_DEFINED_BOUNDARY
Content-Type: application/json; charset=utf-8
Content-Disposition: form-data; name="metadata"

{
  "ParentId": "54213cf5-285f-e611-80c3-000d3a320e1e",
  "Name": "My First Blob",
  "Type": "Map",
  "SubType": "GenericMap",
  "Description": "A well chosen description",
  "Sharing": "None"
}
--USER_DEFINED_BOUNDARY
Content-Disposition: form-data; name="contents"; filename="myblob.txt"
Content-Type: text/plain

This is my blob content. In this case, some text, but I could also be uploading a picture, an JSON file, a firmware zip, etc.

--USER_DEFINED_BOUNDARY--
```

| Değer | Şununla değiştir |
| --- | --- |
| USER_DEFINED_BOUNDARY | Çok parçalı bir içerik sınır adı |

Aşağıdaki kod, [MultipartFormDataContent](https://docs.microsoft.com/dotnet/api/system.net.http.multipartformdatacontent)sınıfı kullanılarak aynı blob yüklemesinin .net uygulamasıdır:

```csharp
//Supply your metadata in a suitable format
var multipartContent = new MultipartFormDataContent("USER_DEFINED_BOUNDARY");

var metadataContent = new StringContent(JsonConvert.SerializeObject(metaData), Encoding.UTF8, "application/json");
metadataContent.Headers.ContentType = MediaTypeHeaderValue.Parse("application/json; charset=utf-8");
multipartContent.Add(metadataContent, "metadata");

//MY_BLOB.txt is the String representation of your text file
var fileContents = new StringContent("MY_BLOB.txt");
fileContents.Headers.ContentType = MediaTypeHeaderValue.Parse("text/plain");
multipartContent.Add(fileContents, "contents");

var response = await httpClient.PostAsync("spaces/blobs", multipartContent);
```

Son olarak [, kullanıcılar](https://curl.haxx.se/) çok parçalı form isteklerini aynı şekilde yapabilirler:

```bash
curl -X POST "YOUR_MANAGEMENT_API_URL/spaces/blobs" \
 -H "Authorization: Bearer YOUR_TOKEN" \
 -H "Accept: application/json" \
 -H "Content-Type: multipart/form-data" \
 -F "meta={\"ParentId\":\"YOUR_SPACE_ID\",\"Name\":\"My CURL Blob\",\"Type\":\"Map\",\"SubType\":\"GenericMap\",\"Description\":\"A well chosen description\",\"Sharing\":\"None\"};type=application/json" \
 -F "text=PATH_TO_FILE;type=text/plain"
```

| Değer | Şununla değiştir |
| --- | --- |
| YOUR_TOKEN | Geçerli OAuth 2,0 belirteciniz |
| YOUR_SPACE_ID | Blobun ilişkilendirileceği alanın KIMLIĞI |
| PATH_TO_FILE | Metin dosyanızın yolu |

[![kıvrımlı örneği](media/how-to-add-blobs/curl-img.png)](media/how-to-add-blobs/curl-img.png#lightbox)

Başarılı bir GÖNDERI, yeni Blobun KIMLIĞINI döndürür.

## <a name="api-endpoints"></a>API uç noktaları

Aşağıdaki bölümlerde, blob ile ilgili temel API uç noktaları ve işlevleri açıklanır.

### <a name="devices"></a>Cihazlar

Blob 'ları cihazlara ekleyebilirsiniz. Aşağıdaki görüntüde, yönetim API 'leriniz için Swagger başvuru belgeleri gösterilmektedir. Blob tüketimi için cihazla ilgili API uç noktalarını ve bunlara geçirilecek gerekli yol parametrelerini belirtir.

[Cihaz bloblarını ![](media/how-to-add-blobs/blobs-device-api-img.png)](media/how-to-add-blobs/blobs-device-api-img.png#lightbox)

Örneğin, bir blobu güncelleştirmek veya oluşturmak ve blobu bir cihaza eklemek için, kimliği doğrulanmış bir HTTP PATCH isteği oluşturun:

```plaintext
YOUR_MANAGEMENT_API_URL/devices/blobs/YOUR_BLOB_ID
```

| Parametre | Şununla değiştir |
| --- | --- |
| *YOUR_BLOB_ID* | İstenen blob KIMLIĞI |

Başarılı istekler, [daha önce açıklandığı](#blobs-response-data)gıbı bir JSON nesnesi döndürür.

### <a name="spaces"></a>Oluşamaz

Ayrıca boşluklara blob 'lar ekleyebilirsiniz. Aşağıdaki görüntüde Blobların işlenmesinden sorumlu tüm alan API uç noktaları listelenmektedir. Ayrıca, bu uç noktalara geçirilecek tüm yol parametrelerini listeler.

[![alanı blob 'ları](media/how-to-add-blobs/blobs-space-api-img.png)](media/how-to-add-blobs/blobs-space-api-img.png#lightbox)

Örneğin, bir alana eklenmiş bir blob döndürmek için, kimliği doğrulanmış bir HTTP GET isteği oluşturun:

```plaintext
YOUR_MANAGEMENT_API_URL/spaces/blobs/YOUR_BLOB_ID
```

| Parametre | Şununla değiştir |
| --- | --- |
| *YOUR_BLOB_ID* | İstenen blob KIMLIĞI |

Başarılı istekler, [daha önce açıklandığı](#blobs-response-data)gıbı bir JSON nesnesi döndürür.

Aynı uç noktaya yönelik bir yama isteği meta veri açıklamalarını güncelleştirir ve BLOB sürümlerini oluşturur. HTTP isteği, gerekli meta ve çok parçalı form verileriyle birlikte PATCH yöntemi aracılığıyla yapılır.

### <a name="users"></a>Kullanıcılar

Blob 'ları Kullanıcı modellerine ekleyebilirsiniz (örneğin, bir profil resmini ilişkilendirmek için). Aşağıdaki görüntüde, ilgili Kullanıcı API uç noktaları ve `id`gibi gerekli yol parametreleri gösterilmektedir:

[![Kullanıcı blob 'ları](media/how-to-add-blobs/blobs-users-api-img.png)](media/how-to-add-blobs/blobs-users-api-img.png#lightbox)

Örneğin, bir kullanıcıya eklenmiş bir blobu getirmek için, gerekli form verileriyle birlikte kimliği doğrulanmış bir HTTP GET isteği oluşturun:

```plaintext
YOUR_MANAGEMENT_API_URL/users/blobs/YOUR_BLOB_ID
```

| Parametre | Şununla değiştir |
| --- | --- |
| *YOUR_BLOB_ID* | İstenen blob KIMLIĞI |

Başarılı istekler, [daha önce açıklandığı](#blobs-response-data)gıbı bir JSON nesnesi döndürür.

## <a name="common-errors"></a>Sık karşılaşılan hatalar

Yaygın bir hata, doğru başlık bilgilerini sağlamayı içerir:

```JSON
{
    "error": {
        "code": "400.600.000.000",
        "message": "Invalid media type in first section."
    }
}
```

Bu hatayı çözmek için, genel isteğin uygun bir **Content-Type** üst bilgisine sahip olduğunu doğrulayın:

* `multipart/mixed`
* `multipart/form-data`

Ayrıca, her bir çok parçalı öbekin gerektiği şekilde karşılık gelen bir **Içerik türüne** sahip olduğunu doğrulayın.

## <a name="next-steps"></a>Sonraki adımlar

- Azure dijital TWINS için Swagger başvuru belgeleri hakkında daha fazla bilgi edinmek için bkz. [Azure dijital TWINS Swagger kullanımı](how-to-use-swagger.md).

- Blob 'ları Postman aracılığıyla karşıya yüklemek için [Postman yapılandırma](./how-to-configure-postman.md)makalesini okuyun.