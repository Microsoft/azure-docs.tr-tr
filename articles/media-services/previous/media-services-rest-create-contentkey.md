---
title: REST ile içerik anahtarları oluşturma | Microsoft Docs
description: Bu makalede, varlıklara güvenli erişim sağlayan içerik anahtarlarının nasıl oluşturulacağı gösterilmektedir.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 95e9322b-168e-4a9d-8d5d-d7c946103745
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: a9960ece05b7cab4ddd26fdac7516f4adbccf5b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103017265"
---
# <a name="create-content-keys-with-rest"></a>REST ile içerik anahtarları oluşturma

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector"]
> * [REST](media-services-rest-create-contentkey.md)
> * [.NET](media-services-dotnet-create-contentkey.md)
> 
> 

Media Services şifrelenmiş varlıklar sunmanızı sağlar. Bir **Contentkey** , **varlığınıza güvenli** erişim sağlar. 

Yeni bir varlık oluşturduğunuzda (örneğin, [dosyaları karşıya](media-services-rest-upload-files.md)yüklemeden önce) aşağıdaki şifreleme seçeneklerini belirtebilirsiniz: **storageencryptıon**, **CommonEncryptionProtected** veya **EnvelopeEncryptionProtected**. 

İstemcilerinize varlıklar sunışınızda, varlıkların aşağıdaki iki şifreleden biriyle [dinamik olarak şifrelenmesini](media-services-rest-configure-asset-delivery-policy.md) sağlayabilirsiniz: **DynamicEnvelopeEncryption** veya **dynamiccommonencryption**.

Şifrelenmiş varlıkların, **Contentkey** s ile ilişkilendirilmesi gerekir. Bu makalede bir içerik anahtarının nasıl oluşturulacağı açıklanır.

Aşağıda, şifrelenmesini istediğiniz varlıklarla ilişkilendirdiğiniz içerik anahtarlarının oluşturulması için genel adımlar verilmiştir. 

1. Rastgele bir 16 baytlık AES anahtarı (ortak ve zarf şifrelemesi için) veya 32 baytlık AES anahtarını (depolama şifrelemesi için) rastgele oluşturun. 
   
    Bu, varlığınızın içerik anahtarıdır. Bu, söz konusu varlıkla ilişkili tüm dosyaların şifre çözme sırasında aynı içerik anahtarını kullanması gerektiği anlamına gelir. 
2. İçerik anahtarınızı şifrelemek için kullanılması gereken doğru X. 509.952 sertifikasını almak için [Getprotectionkeyıd](/rest/api/media/operations/rest-api-functions#getprotectionkeyid) ve [getprotectionkey](/rest/api/media/operations/rest-api-functions#getprotectionkey) yöntemlerini çağırın.
3. İçerik anahtarınızı X. 509.440 sertifikasının ortak anahtarıyla şifreleyin. 
   
   Media Services .NET SDK, şifrelemeyi yaparken OAEP ile RSA kullanır.  [Encryptsymmetrickeydata işlevinde](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.FileEncryption/EncryptionUtils.cs)bir örnek görebilirsiniz.
4. Anahtar tanımlayıcısı ve içerik anahtarı kullanılarak hesaplanan bir sağlama toplamı değeri (PlayReady AES anahtar sağlama toplaması algoritması temelinde) oluşturun. Daha fazla bilgi için [burada](https://www.microsoft.com/playready/documents/)bulunan PlayReady üstbilgi nesne belgesinin "PlayReady AES Key checksum algoritması" bölümüne bakın.
   
    Aşağıdaki .NET örneği, anahtar tanımlayıcısının GUID bölümünü ve açık içerik anahtarını kullanarak sağlama toplamını hesaplar.

    ```console
    public static string CalculateChecksum(byte[] contentKey, Guid keyId)
     {
       byte[] array = null;
         using (AesCryptoServiceProvider aesCryptoServiceProvider = new AesCryptoServiceProvider())
         {
             aesCryptoServiceProvider.Mode = CipherMode.ECB;
             aesCryptoServiceProvider.Key = contentKey;
             aesCryptoServiceProvider.Padding = PaddingMode.None;
             ICryptoTransform cryptoTransform = aesCryptoServiceProvider.CreateEncryptor();
             array = new byte[16];
             cryptoTransform.TransformBlock(keyId.ToByteArray(), 0, 16, array, 0);
         }
         byte[] array2 = new byte[8];
         Array.Copy(array, array2, 8);
         return Convert.ToBase64String(array2);
     }
    ```

5. Önceki adımlarda aldığınız **Encryptedcontentkey** (base64 kodlu dizeye dönüştürülmüş), **protectionkeyıd**, **Protectionkeytype**, **Contentkeytype** ve **sağlama toplamı** değerleri ile içerik anahtarı oluşturun.
6. $Links işlemi aracılığıyla **Contentkey** varlığını **varlık** varlığınızla ilişkilendirin.

Bu makale, bir AES anahtarı oluşturmayı, anahtarı şifrelemeyi ve sağlama toplamını hesaplamayı göstermez. 

> [!NOTE]
> 
> Media Services varlıklara erişirken, HTTP isteklerinizin belirli üstbilgi alanlarını ve değerlerini ayarlamanız gerekir. Daha fazla bilgi için bkz. [Media Services REST API Geliştirme Için kurulum](media-services-rest-how-to-use.md).

## <a name="connect-to-media-services"></a>Media Services’e bağlanmak

AMS API 'sine bağlanma hakkında daha fazla bilgi için bkz. [Azure AD kimlik doğrulamasıyla Azure MEDIA SERVICES API 'Sine erişme](media-services-use-aad-auth-to-access-ams-api.md). 

## <a name="retrieve-the-protectionkeyid"></a>Protectionkeyıd 'yi alma
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
charset=utf-8
Server: Microsoft-IIS/8.5
request-id: 2b6aa7a4-3a09-4b08-b581-26b55667f817
x-ms-request-id: 2b6aa7a4-3a09-4b08-b581-26b55667f817
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 04 Feb 2015 02:42:52 GMT

$metadata#Edm.String","value":"7D9BB04D9D0A4A24800CADBFEF232689E048F69C"}
```

## <a name="retrieve-the-protectionkey-for-the-protectionkeyid"></a>Protectionkeyıd için ProtectionKey değerini alma
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

{"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Edm.String", "value":"MIIDSTCCAjGgAwIBAgIQqf92wku/HLJGCbMAU8GEnDANBgkqhkiG9w0BAQQFADAuMSwwKgYDVQQDEyN3YW1zYmx1cmVnMDAxZW5jcnlwdGFsbHNlY3JldHMtY2VydDAeFw0xMjA1MjkwNzAwMDBaFw0zMjA1MjkwNzAwMDBaMC4xLDAqBgNVBAMTI3dhbXNibHVyZWcwMDFlbmNyeXB0YWxsc2VjcmV0cy1jZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAzR0SEbXefvUjb9wCUfkEiKtGQ5Gc328qFPrhMjSo+YHe0AVviZ9YaxPPb0m1AaaRV4dqWpST2+JtDhLOmGpWmmA60tbATJDdmRzKi2eYAyhhE76MgJgL3myCQLP42jDusWXWSMabui3/tMDQs+zfi1sJ4Ch/lm5EvksYsu6o8sCv29VRwxfDLJPBy2NlbV4GbWz5Qxp2tAmHoROnfaRhwp6WIbquk69tEtu2U50CpPN2goLAqx2PpXAqA+prxCZYGTHqfmFJEKtZHhizVBTFPGS3ncfnQC9QIEwFbPw6E5PO5yNaB68radWsp5uvDg33G1i8IT39GstMW6zaaG7cNQIDAQABo2MwYTBfBgNVHQEEWDBWgBCOGT2hPhsvQioZimw8M+jOoTAwLjEsMCoGA1UEAxMjd2Ftc2JsdXJlZzAwMWVuY3J5cHRhbGxzZWNyZXRzLWNlcnSCEKn/dsJLvxyyRgmzAFPBhJwwDQYJKoZIhvcNAQEEBQADggEBABcrQPma2ekNS3Wc5wGXL/aHyQaQRwFGymnUJ+VR8jVUZaC/U/f6lR98eTlwycjVwRL7D15BfClGEHw66QdHejaViJCjbEIJJ3p2c9fzBKhjLhzB3VVNiLIaH6RSI1bMPd2eddSCqhDIn3VBN605GcYXMzhYp+YA6g9+YMNeS1b+LxX3fqixMQIxSHOLFZ1G/H2xfNawv0VikH3djNui3EKT1w/8aRkUv/AAV0b3rYkP/jA1I0CPn0XFk7STYoiJ3gJoKq9EMXhit+Iwfz0sMkfhWG12/XO+TAWqsK1ZxEjuC9OzrY7pFnNxs4Mu4S8iinehduSpY+9mDd3dHynNwT4="}
```

## <a name="create-the-contentkey"></a>ContentKey oluşturma
X. 509.952 sertifikasını aldıktan ve kendi ortak anahtarını, içerik anahtarınızı şifrelemek için kullandıktan sonra, bir **contentkey** varlığı oluşturun ve özellik değerlerini uygun şekilde ayarlayın.

İçerik anahtarını oluştururken ayarlamanız gereken değerlerden biri türdür. Aşağıdaki değerlerden birini seçin:

```console
public enum ContentKeyType
{
    /// <summary>
    /// Specifies a content key for common encryption.
    /// </summary>
    /// <remarks>This is the default value.</remarks>
    CommonEncryption = 0,

    /// <summary>
    /// Specifies a content key for storage encryption.
    /// </summary>
    StorageEncryption = 1,

    /// <summary>
    /// Specifies a content key for configuration encryption.
    /// </summary>
    ConfigurationEncryption = 2,

    /// <summary>
    /// Specifies a content key for Envelope encryption.  Only used internally.
    /// </summary>
    EnvelopeEncryption = 4
    }
```

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

## <a name="media-services-learning-paths"></a>Media Services’i öğrenme yolları
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Geribildirim gönderme
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
