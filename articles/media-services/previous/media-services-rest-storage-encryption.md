---
title: AMS REST API kullanarak içeriğinizi depolama şifreleme ile şifreleme
description: AMS REST API 'Leri kullanarak içeriğinizi depolama şifrelemesiyle nasıl şifreleyeceğinizi öğrenin.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: a0a79f3d-76a1-4994-9202-59b91a2230e0
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 885390d9246937247107128114e9242aa5e2dc01
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103015480"
---
# <a name="encrypting-your-content-with-storage-encryption"></a>Depolama şifrelemesiyle içeriğinizi şifreleme

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Bu öğreticiyi tamamlamak için bir Azure hesabınızın olması gerekir. Ayrıntılı bilgi için bkz. [Azure Ücretsiz Deneme Sürümü](https://azure.microsoft.com/pricing/free-trial/).   Media Services V2 'ye yeni özellik veya işlevsellik eklenmiyor >. <br/>[V3 Media Services](../latest/index.yml)en son sürüme göz atın. Ayrıca bkz. [v2 'den v3 'e geçiş kılavuzu](../latest/migrate-v-2-v-3-migration-introduction.md)
>

Bu makale, AMS depolama şifrelemesi hakkında genel bir bakış sunar ve depolama şifreli içeriğini karşıya yükleme işleminin nasıl yapılacağını gösterir:

* Bir içerik anahtarı oluşturun.
* Bir varlık oluşturun. Varlığı oluştururken AssetCreationOption öğesini StorageEncryption olarak ayarlayın.
  
     Şifrelenmiş varlıklar, içerik anahtarlarıyla ilişkilendirilir.
* İçerik anahtarını varlığa bağlayın.  
* AssetFile varlıklarında şifrelemeyle ilgili parametreleri ayarlayın.

## <a name="considerations"></a>Dikkat edilmesi gerekenler 

Depolama ile şifrelenmiş bir varlık sağlamak istiyorsanız, varlığın teslim ilkesini yapılandırmanız gerekir. Varlığınızın akışı için, akış sunucusu depolama şifrelemesini kaldırır ve belirtilen teslim ilkesini kullanarak içeriğinizi akışa çıkarır. Daha fazla bilgi için bkz. [varlık teslim Ilkelerini yapılandırma](media-services-rest-configure-asset-delivery-policy.md).

Media Services varlıklara erişirken, HTTP isteklerinizin belirli üstbilgi alanlarını ve değerlerini ayarlamanız gerekir. Daha fazla bilgi için bkz. [Media Services REST API Geliştirme Için kurulum](media-services-rest-how-to-use.md). 

### <a name="storage-side-encryption"></a>Depolama tarafı şifrelemesi

|Şifreleme seçeneği|Description|Media Services v2|Media Services v3|
|---|---|---|---|
|Media Services depolama şifrelemesi|AES-256 şifrelemesi, anahtar Media Services tarafından yönetiliyor|Desteklenen<sup>(1)</sup>|Desteklenmiyor<sup>(2)</sup>|
|[Bekleyen veriler için Depolama Hizmeti Şifrelemesi](../../storage/common/storage-service-encryption.md)|Azure depolama tarafından sunulan, Azure veya müşteri tarafından yönetilen anahtar olan sunucu tarafı şifrelemesi|Desteklenir|Desteklenir|
|[Depolama Client-Side şifrelemesi](../../storage/common/storage-client-side-encryption.md)|Azure depolama tarafından sunulan istemci tarafı şifreleme, Key Vault ' de müşteri tarafından yönetilen anahtar|Desteklenmez|Desteklenmez|

<sup>1</sup> Media Services, şifresiz ve herhangi bir şifreleme formu olmadan içerik işlemeyi desteklese de, bunu yapmanız önerilmez.

<sup>2</sup> Media Services v3 'de, depolama ŞIFRELEMESI (AES-256 şifrelemesi) yalnızca varlıklarınız Media Services V2 ile oluşturulduysa geriye dönük uyumluluk için desteklenir. V3, mevcut depolama şifrelenmiş varlıklarla çalışır, ancak yenilerini oluşturulmasına izin vermez.

## <a name="connect-to-media-services"></a>Media Services’e bağlanmak

AMS API 'sine bağlanma hakkında daha fazla bilgi için bkz. [Azure AD kimlik doğrulamasıyla Azure MEDIA SERVICES API 'Sine erişme](media-services-use-aad-auth-to-access-ams-api.md). 

## <a name="storage-encryption-overview"></a>Depolama şifrelemeye genel bakış
AMS depolama şifrelemesi, tüm dosyanın **AES-Mrk** modu şifrelemesini uygular.  AES-Mrk modu, doldurma gerekmeden rastgele uzunluktaki verileri şifreleyebilen bir blok şifredir. Bu işlem, AES algoritması ile bir sayaç bloğunu şifreleyerek ve sonra şifrelenecek veya şifresinin çözülmesi gereken verilerle AES çıkışı XOR ile çalışır.  Kullanılan sayaç bloğu, ınitializationvector değeri sayaç değerinin 0 ile 7 arasında ve sayaç değerinin 8 ile 15 ' i arasında bir değer sıfıra ayarlandığında oluşturulur. 16 baytlık sayaç bloğunun 3 ile 15 arasında (yani en az sayıda bayt), işlenen her veri bloğu için bir tane tarafından arttırılan ve ağ bayt düzeninde tutulan basit bir 64 bitlik işaretsiz tamsayı olarak kullanılır. Bu tamsayı en büyük değere (0xFFFFFFFFFFFFFFFF) ulaşırsa, bu süreyi arttırın, sayacın diğer 64 bitlerini etkilemeden blok sayacını sıfıra (bayt 8 ' e kadar) sıfırlar (yani, bayt 0 ile 7 arasında).   AES-Mrk modu şifrelemenin güvenliğini sağlamak için her bir içerik anahtarı için belirli bir anahtar tanımlayıcısı için ınitializationvector değeri her dosya için benzersiz olacaktır ve dosyalar 2 ^ 64 blok uzunluğunda olacaktır.  Bu benzersiz değer, bir sayaç değerinin belirli bir anahtarla hiçbir şekilde yeniden kullanılmamasını sağlamaktır. MRK modu hakkında daha fazla bilgi için bkz. [Bu wiki sayfası](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#CTR) (wiki makalesi "ınitializationvector" yerine "nonce" terimini kullanır).

AES-256 bit şifrelemesini kullanarak açık içeriğinizi yerel olarak şifrelemek için **depolama şifrelemeyi** kullanın ve ardından, geri kalanı şifreli olarak depolandığı Azure depolama 'ya yükleyin. Depolama şifrelemesi ile korunan varlıklar, kodlama öncesinde otomatik olarak şifrelenmez ve şifreli bir dosya sistemine yerleştirilir ve yeni bir çıkış varlığı olarak geri yüklenmeden önce isteğe bağlı olarak yeniden şifrelenir. Depolama şifrelemesi için birincil kullanım örneği, yüksek kaliteli giriş medya dosyalarınızı diskte bekleyen bir şekilde güçlü şifreleme ile güvenli hale getirmek istediğinizde kullanılır.

Depolama şifrelenmiş bir varlık teslim etmek için varlığın teslim ilkesini yapılandırmanız gerekir, böylece içeriğinizi nasıl teslim etmek istediğinizi bilir Media Services. Varlığınızın akışı silinmeden önce, akış sunucusu depolama şifrelemesini kaldırır ve belirtilen teslim ilkesini (örneğin, AES, ortak şifreleme veya şifreleme olmadan) kullanarak içeriğinizi akışa alabilir.

## <a name="create-contentkeys-used-for-encryption"></a>Şifreleme için kullanılan ContentKeys oluşturma
Şifrelenmiş varlıklar, depolama şifreleme anahtarlarıyla ilişkilendirilir. Varlık dosyalarını oluşturmadan önce şifreleme için kullanılacak içerik anahtarını oluşturun. Bu bölümde, içerik anahtarının nasıl oluşturulacağı açıklanmaktadır.

Aşağıda, şifrelenmesini istediğiniz varlıklarla ilişkilendirdiğiniz içerik anahtarlarının oluşturulması için genel adımlar verilmiştir. 

1. Depolama şifrelemesi için rastgele bir 32 baytlık AES anahtarı oluşturun. 
   
    32 baytlık AES anahtarı, varlığınızın içerik anahtarıdır. Bu, söz konusu varlıkla ilişkili tüm dosyaların şifre çözme sırasında aynı içerik anahtarını kullanması gerektiği anlamına gelir. 
2. İçerik anahtarınızı şifrelemek için kullanılması gereken doğru X. 509.952 sertifikasını almak için [Getprotectionkeyıd](/rest/api/media/operations/rest-api-functions#getprotectionkeyid) ve [getprotectionkey](/rest/api/media/operations/rest-api-functions#getprotectionkey) yöntemlerini çağırın.
3. İçerik anahtarınızı X. 509.440 sertifikasının ortak anahtarıyla şifreleyin. 
   
   Media Services .NET SDK, şifrelemeyi yaparken OAEP ile RSA kullanır.  Bir .NET örneğini [Encryptsymmetrickeydata işlevinde](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.FileEncryption/EncryptionUtils.cs)görebilirsiniz.
4. Anahtar tanımlayıcısı ve içerik anahtarı kullanılarak hesaplanan bir sağlama toplamı değeri oluşturun. Aşağıdaki .NET örneği, anahtar tanımlayıcısının GUID bölümünü ve açık içerik anahtarını kullanarak sağlama toplamını hesaplar.

    ```csharp
    public static string CalculateChecksum(byte[] contentKey, Guid keyId)
    {
        const int ChecksumLength = 8;
        const int KeyIdLength = 16;

        byte[] encryptedKeyId = null;

        // Checksum is computed by AES-ECB encrypting the KID
        // with the content key.
        using (AesCryptoServiceProvider rijndael = new AesCryptoServiceProvider())
        {
            rijndael.Mode = CipherMode.ECB;
            rijndael.Key = contentKey;
            rijndael.Padding = PaddingMode.None;

            ICryptoTransform encryptor = rijndael.CreateEncryptor();
            encryptedKeyId = new byte[KeyIdLength];
            encryptor.TransformBlock(keyId.ToByteArray(), 0, KeyIdLength, encryptedKeyId, 0);
        }

        byte[] retVal = new byte[ChecksumLength];
        Array.Copy(encryptedKeyId, retVal, ChecksumLength);

        return Convert.ToBase64String(retVal);
    }
    ```

5. Önceki adımlarda aldığınız **Encryptedcontentkey** (base64 kodlu dizeye dönüştürülmüş), **protectionkeyıd**, **Protectionkeytype**, **Contentkeytype** ve **sağlama toplamı** değerleri ile içerik anahtarı oluşturun.

    Depolama şifrelemesi için, aşağıdaki özellikler istek gövdesine eklenmelidir.

    İstek gövdesi özelliği    | Description
    ---|---
    Id | ContentKey KIMLIĞI, "NB: KID: UUID:" biçiminde şu biçim kullanılarak oluşturulur \<NEW GUID> .
    ContentKeyType | İçerik anahtar türü, anahtarı tanımlayan bir tamsayıdır. Depolama şifreleme biçimi için değer 1 ' dir.
    EncryptedContentKey | 256 bitlik (32 bayt) bir değer olan yeni bir içerik anahtarı değeri oluşturacağız. Anahtar, Getprotectionkeyıd ve GetProtectionKey yöntemleri için bir HTTP GET isteği yürüterek Microsoft Azure Media Services elde ettiğimiz depolama şifreleme X. 509.440 sertifikası kullanılarak şifrelenir. Örnek olarak, aşağıdaki .NET koduna bakın: [burada](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.FileEncryption/EncryptionUtils.cs)tanımlanan **Encryptsymmetrickeydata** yöntemi.
    Protectionkeyıd | Bu, içerik anahtarımızı şifrelemek için kullanılan depolama şifrelemesi X. 509.440 sertifikası için koruma anahtarı KIMLIĞIDIR.
    ProtectionKeyType | Bu, içerik anahtarını şifrelemek için kullanılan koruma anahtarı için şifreleme türüdür. Bu değer, örneğimiz için StorageEncryption (1).
    Sağlama |İçerik anahtarı için MD5 hesaplanan sağlama toplamı. İçerik KIMLIĞI içerik anahtarıyla şifrelenerek hesaplanır. Örnek kod, sağlama toplamı nasıl hesaplanacağını gösterir.


### <a name="retrieve-the-protectionkeyid"></a>Protectionkeyıd 'yi alma
Aşağıdaki örnek, içerik anahtarınızı şifrelerken kullanmanız gereken sertifika için bir sertifika parmak izi olan Protectionkeyıd 'nin nasıl alınacağını gösterir. Makinenizde uygun sertifikaya zaten sahip olduğunuzdan emin olmak için bu adımı gerçekleştirin.

İstek:

```console
GET https://media.windows.net/api/GetProtectionKeyId?contentKeyType=0 HTTP/1.1
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
User-Agent: Microsoft ADO.NET Data Services
Authorization: Bearer <ENCODED JWT TOKEN>
x-ms-version: 2.19
Host: media.windows.net
```

Yanıt:

```console
HTTP/1.1 200 OK
Cache-Control: no-cache
Content-Length: 139
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Server: Microsoft-IIS/8.5
request-id: 2b6aa7a4-3a09-4b08-b581-26b55667f817
x-ms-request-id: 2b6aa7a4-3a09-4b08-b581-26b55667f817
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 04 Feb 2015 02:42:52 GMT

{"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Edm.String","value":"7D9BB04D9D0A4A24800CADBFEF232689E048F69C"}
```

### <a name="retrieve-the-protectionkey-for-the-protectionkeyid"></a>Protectionkeyıd için ProtectionKey değerini alma
Aşağıdaki örnek, önceki adımda aldığınız Protectionkeyıd kullanarak X. 509.440 sertifikasının nasıl alınacağını gösterir.

İstek:

```console
GET https://media.windows.net/api/GetProtectionKey?ProtectionKeyId='7D9BB04D9D0A4A24800CADBFEF232689E048F69C' HTTP/1.1
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
User-Agent: Microsoft ADO.NET Data Services
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: 78d1247a-58d7-40e5-96cc-70ff0dfa7382
Host: media.windows.net
```

Yanıt:

```console
HTTP/1.1 200 OK
Cache-Control: no-cache
Content-Length: 1227
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Server: Microsoft-IIS/8.5
x-ms-client-request-id: 78d1247a-58d7-40e5-96cc-70ff0dfa7382
request-id: 1523e8f3-8ed2-40fe-8a9a-5d81eb572cc8
x-ms-request-id: 1523e8f3-8ed2-40fe-8a9a-5d81eb572cc8
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Thu, 05 Feb 2015 07:52:30 GMT

{"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Edm.String",
"value":"MIIDSTCCAjGgAwIBAgIQqf92wku/HLJGCbMAU8GEnDANBgkqhkiG9w0BAQQFADAuMSwwKgYDVQQDEyN3YW1zYmx1cmVnMDAxZW5jcnlwdGFsbHNlY3JldHMtY2VydDAeFw0xMjA1MjkwNzAwMDBaFw0zMjA1MjkwNzAwMDBaMC4xLDAqBgNVBAMTI3dhbXNibHVyZWcwMDFlbmNyeXB0YWxsc2VjcmV0cy1jZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAzR0SEbXefvUjb9wCUfkEiKtGQ5Gc328qFPrhMjSo+YHe0AVviZ9YaxPPb0m1AaaRV4dqWpST2+JtDhLOmGpWmmA60tbATJDdmRzKi2eYAyhhE76MgJgL3myCQLP42jDusWXWSMabui3/tMDQs+zfi1sJ4Ch/lm5EvksYsu6o8sCv29VRwxfDLJPBy2NlbV4GbWz5Qxp2tAmHoROnfaRhwp6WIbquk69tEtu2U50CpPN2goLAqx2PpXAqA+prxCZYGTHqfmFJEKtZHhizVBTFPGS3ncfnQC9QIEwFbPw6E5PO5yNaB68radWsp5uvDg33G1i8IT39GstMW6zaaG7cNQIDAQABo2MwYTBfBgNVHQEEWDBWgBCOGT2hPhsvQioZimw8M+jOoTAwLjEsMCoGA1UEAxMjd2Ftc2JsdXJlZzAwMWVuY3J5cHRhbGxzZWNyZXRzLWNlcnSCEKn/dsJLvxyyRgmzAFPBhJwwDQYJKoZIhvcNAQEEBQADggEBABcrQPma2ekNS3Wc5wGXL/aHyQaQRwFGymnUJ+VR8jVUZaC/U/f6lR98eTlwycjVwRL7D15BfClGEHw66QdHejaViJCjbEIJJ3p2c9fzBKhjLhzB3VVNiLIaH6RSI1bMPd2eddSCqhDIn3VBN605GcYXMzhYp+YA6g9+YMNeS1b+LxX3fqixMQIxSHOLFZ1G/H2xfNawv0VikH3djNui3EKT1w/8aRkUv/AAV0b3rYkP/jA1I0CPn0XFk7STYoiJ3gJoKq9EMXhit+Iwfz0sMkfhWG12/XO+TAWqsK1ZxEjuC9OzrY7pFnNxs4Mu4S8iinehduSpY+9mDd3dHynNwT4="}
```

### <a name="create-the-content-key"></a>İçerik anahtarını oluşturma
X. 509.952 sertifikasını aldıktan ve kendi ortak anahtarını, içerik anahtarınızı şifrelemek için kullandıktan sonra, bir **contentkey** varlığı oluşturun ve özellik değerlerini uygun şekilde ayarlayın.

İçerik anahtarını oluştururken ayarlamanız gereken değerlerden biri türdür. Depolama şifrelemesi kullanılırken, değer ' 1 ' olarak ayarlanmalıdır. 

Aşağıdaki örnek, depolama şifrelemesi için bir **Contentkeytype** kümesi ("1") ve **Protectionkeytype** 'ın X. 509.440 sertifika parmak izi olduğunu göstermek için "0" olarak ayarlanmış bir **contentkey** oluşturmayı gösterir.  

İstek

```console
POST https://media.windows.net/api/ContentKeys HTTP/1.1
Content-Type: application/json
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
User-Agent: Microsoft ADO.NET Data Services
Authorization: Bearer <ENCODED JWT TOKEN>
x-ms-version: 2.19
Host: media.windows.net
{
"Name":"ContentKey",
"ProtectionKeyId":"7D9BB04D9D0A4A24800CADBFEF232689E048F69C", 
"ContentKeyType":"1", 
"ProtectionKeyType":"0",
"EncryptedContentKey":"your encrypted content key",
"Checksum":"calculated checksum"
}
```

Yanıt:

```console
HTTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 777
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Location: https://media.windows.net/api/ContentKeys('nb%3Akid%3AUUID%3A9c8ea9c6-52bd-4232-8a43-8e43d8564a99')
Server: Microsoft-IIS/8.5
request-id: 76e85e0f-5cf1-44cb-b689-b3455888682c
x-ms-request-id: 76e85e0f-5cf1-44cb-b689-b3455888682c
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 04 Feb 2015 02:37:46 GMT

{"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#ContentKeys/@Element",
"Id":"nb:kid:UUID:9c8ea9c6-52bd-4232-8a43-8e43d8564a99","Created":"2015-02-04T02:37:46.9684379Z",
"LastModified":"2015-02-04T02:37:46.9684379Z",
"ContentKeyType":1,
"EncryptedContentKey":"your encrypted content key",
"Name":"ContentKey",
"ProtectionKeyId":"7D9BB04D9D0A4A24800CADBFEF232689E048F69C",
"ProtectionKeyType":0,
"Checksum":"calculated checksum"}
```

## <a name="create-an-asset"></a>Varlık oluşturma
Aşağıdaki örnek, bir varlığın nasıl oluşturulacağını göstermektedir.

**HTTP İsteği**

```console
POST https://media.windows.net/api/Assets HTTP/1.1
Content-Type: application/json
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN>
x-ms-version: 2.19
Host: media.windows.net

{"Name":"BigBuckBunny" "Options":1}
```

**HTTP yanıtı**

Başarılı olursa, aşağıdaki yanıt döndürülür:

```console
HTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 452
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Assets('nb%3Acid%3AUUID%3A9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1')
Server: Microsoft-IIS/8.5
x-ms-client-request-id: c59de965-bc89-4295-9a57-75d897e5221e
request-id: e98be122-ae09-473a-8072-0ccd234a0657
x-ms-request-id: e98be122-ae09-473a-8072-0ccd234a0657
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Sun, 18 Jan 2015 22:06:40 GMT
{  
   "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Assets/@Element",
   "Id":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
   "State":0,
   "Created":"2015-01-18T22:06:40.6010903Z",
   "LastModified":"2015-01-18T22:06:40.6010903Z",
   "AlternateId":null,
   "Name":"BigBuckBunny.mp4",
   "Options":1,
   "Uri":"https://storagetestaccount001.blob.core.windows.net/asset-9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
   "StorageAccountName":"storagetestaccount001"
}
```

## <a name="associate-the-contentkey-with-an-asset"></a>ContentKey 'i bir varlıkla ilişkilendir
ContentKey oluşturduktan sonra, aşağıdaki örnekte gösterildiği gibi $links işlemini kullanarak varlığınızla ilişkilendirin:

İstek:

```console
POST https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Afbd7ce05-1087-401b-aaae-29f16383c801')/$links/ContentKeys HTTP/1.1
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Content-Type: application/json
Authorization: Bearer <ENCODED JWT TOKEN>
x-ms-version: 2.19
Host: media.windows.net

{"uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeys('nb%3Akid%3AUUID%3A01e6ea36-2285-4562-91f1-82c45736047c')"}
```

Yanıt:

```console
HTTP/1.1 204 No Content 
```

## <a name="create-an-assetfile"></a>Assetdosyası oluşturma
[Assetfile](/rest/api/media/operations/assetfile) varlığı bir blob kapsayıcısında depolanan bir videoyu veya ses dosyasını temsil eder. Bir varlık dosyası her zaman bir varlıkla ilişkilendirilir ve bir varlık bir veya daha fazla varlık dosyası içerebilir. Bir varlık dosya nesnesi bir blob kapsayıcısındaki dijital dosyayla ilişkilendirilmediğinde Media Services kodlayıcı görevi başarısız olur.

**Assetfile** örneği ve gerçek medya dosyası iki ayrı nesne. Maldosya örneği medya dosyası hakkındaki meta verileri içerir, ancak medya dosyası gerçek medya içeriğini içerir.

Dijital medya dosyanızı bir blob kapsayıcısına yükledikten sonra, Assetdosyasını medya dosyanız (Bu makalede gösterilmez) hakkındaki bilgilerle güncelleştirmek için **merge** http isteğini kullanırsınız. 

**HTTP İsteği**

```console
POST https://media.windows.net/api/Files HTTP/1.1
Content-Type: application/json
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN>
x-ms-version: 2.19
Host: media.windows.net
Content-Length: 164

{  
   "IsEncrypted":"true",
   "EncryptionScheme" : "StorageEncryption", 
   "EncryptionVersion" : "1.0",       
   "EncryptionKeyId" : "nb:kid:UUID:32e6efaf-5fba-4538-b115-9d1cefe43510",
   "InitializationVector" : "397304628502661816</d:InitializationVector",
   "Options":0,
   "IsPrimary":"false",
   "MimeType":"video/mp4",
   "Name":"BigBuckBunny.mp4",
   "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1"
}
```

**HTTP yanıtı**

```console
HTTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 535
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Files('nb%3Acid%3AUUID%3Af13a0137-0a62-9d4c-b3b9-ca944b5142c5')
Server: Microsoft-IIS/8.5
request-id: 98a30e2d-f379-4495-988e-0b79edc9b80e
x-ms-request-id: 98a30e2d-f379-4495-988e-0b79edc9b80e
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Mon, 19 Jan 2015 00:34:07 GMT

{  
   "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Files/@Element",
   "Id":"nb:cid:UUID:f13a0137-0a62-9d4c-b3b9-ca944b5142c5",
   "Name":"BigBuckBunny.mp4",
   "ContentFileSize":"0",
   "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
   "EncryptionVersion": "1.0",
   "EncryptionScheme": "StorageEncryption",
   "IsEncrypted":true,
   "EncryptionKeyId":"nb:kid:UUID:32e6efaf-5fba-4538-b115-9d1cefe43510",
   "InitializationVector":"397304628502661816</d:InitializationVector",
   "IsPrimary":false,
   "LastModified":"2015-01-19T00:34:08.1934137Z",
   "Created":"2015-01-19T00:34:08.1934137Z",
   "MimeType":"video/mp4",
   "ContentChecksum":null
}
```
