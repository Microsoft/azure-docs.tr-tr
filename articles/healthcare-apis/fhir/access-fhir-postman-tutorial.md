---
title: Azure 'da Postman FHıR sunucusu-FHıR için Azure API
description: Bu öğreticide, bir FHıR sunucusuna erişmek için Postman 'ı kullanmak için gereken adımları adım adım inceleyeceğiz. Postman, API 'Lere erişen uygulamalarda hata ayıklama için yararlıdır.
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: dseven
ms.author: matjazl
author: matjazl
ms.date: 03/26/2021
ms.openlocfilehash: 59847f745037acec47415489cdf61d119a7807af
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105936283"
---
# <a name="access-azure-api-for-fhir-with-postman"></a>Postman ile FHıR için Azure API 'sine erişme

İstemci uygulaması, [REST API](https://www.hl7.org/fhir/http.html)aracılığıyla fhır için Azure API 'sine erişebilir. İstek göndermek, yanıtları görüntülemek ve uygulamanızda hata ayıklamak için seçtiğiniz bir API test aracını kullanın. Bu öğreticide, [Postman](https://www.getpostman.com/)kullanarak fhır sunucusuna erişme adımlarında size yol göstereceğiz. 

## <a name="prerequisites"></a>Önkoşullar

- Azure 'da bir FHıR uç noktası. 

  FHIR (yönetilen hizmet) için Azure API 'yi dağıtmak üzere [Azure Portal](fhir-paas-portal-quickstart.md), [POWERSHELL](fhir-paas-powershell-quickstart.md)veya [Azure CLI](fhir-paas-cli-quickstart.md)kullanabilirsiniz.

- FHıR hizmetine erişmek için kayıtlı bir [Gizli istemci uygulaması](register-confidential-azure-ad-client-app.md) .
- Gizli istemci uygulamasına izin vermiş olursunuz, örneğin, FHıR hizmetine erişmek için "FHıR veri katılımcısı". Daha fazla bilgi için bkz. [FHıR Için Azure RBAC yapılandırma](./configure-azure-rbac.md).
- Postman yüklendi. 
    
  Postman hakkında daha fazla bilgi için bkz. [Postman](https://www.getpostman.com)'ı kullanmaya başlama.

## <a name="fhir-server-and-authentication-details"></a>FHıR sunucusu ve kimlik doğrulaması ayrıntıları

Postman 'ı kullanmak için aşağıdaki kimlik doğrulama parametreleri gereklidir:

- FHıR sunucu URL 'niz, örneğin `https://MYACCOUNT.azurehealthcareapis.com`

- `Authority`Fhır sunucunuzun kimlik sağlayıcısı, örneğin,`https://login.microsoftonline.com/{TENANT-ID}`

- `audience`Genellikle fhır sunucusunun URL 'sidir, örneğin `https://<FHIR-SERVER-NAME>.azurehealthcareapis.com` veya `https://azurehealthcareapis.com` .

- `client_id`Fhır hizmetine erişmek için kullanılan [Gizli istemci uygulamasının](register-confidential-azure-ad-client-app.md) veya uygulama kimliği.

- `client_secret`Gizli istemci uygulamasının veya uygulama gizli anahtarı.

Son olarak, bunun `https://www.getpostman.com/oauth2/callback` istemci uygulamanız için kayıtlı bir yanıt URL 'si olduğunu denetlemeniz gerekir.

## <a name="connect-to-fhir-server"></a>FHıR sunucusuna bağlan

Postman 'ı açın ve ardından bir istek yapmak için **Al** ' ı seçin `https://fhir-server-url/metadata` .

![Postman meta verileri yetenek beyanı](media/tutorial-postman/postman-metadata.png)

FHıR için Azure API 'nin meta veri URL 'SI `https://MYACCOUNT.azurehealthcareapis.com/metadata` . 

Bu örnekte, FHıR sunucu URL 'SI `https://fhirdocsmsft.azurewebsites.net` ve sunucunun yetenek beyanı ' nda bulunabilir `https://fhirdocsmsft.azurewebsites.net/metadata` . Bu uç noktaya kimlik doğrulaması olmadan erişilebilir.

Kısıtlanmış kaynaklara erişmeyi denerseniz, "kimlik doğrulama başarısız oldu" yanıtı oluşur.

![Kimlik doğrulama başarısız](media/tutorial-postman/postman-authentication-failed.png)

## <a name="obtaining-an-access-token"></a>Erişim belirteci alma
**Yeni Erişim Belirteci Al**’a tıklayın.

Geçerli bir erişim belirteci almak için, **Yetkilendirme** ' yi seçin ve **tür** açılan menüsünden **OAuth 2,0** ' ı seçin.

![OAuth 2,0 ayarla](media/tutorial-postman/postman-select-oauth2.png)

**Yeni Erişim Belirteci Al**’a tıklayın.

![Yeni erişim belirteci iste](media/tutorial-postman/postman-request-token.png)

**Yeni erişim belirteci al** iletişim kutusunda, aşağıdaki ayrıntıları girin:

| Alan                 | Örnek Değer                                                                                                   | Yorum                    |
|-----------------------|-----------------------------------------------------------------------------------------------------------------|----------------------------|
| Belirteç Adı            | MYTOKEN                                                                                                         | Seçtiğiniz bir ad          |
| Veriliş Türü            | Yetkilendirme kodu                                                                                              |                            |
| Geri Çağırma URL’si          | `https://www.getpostman.com/oauth2/callback`                                                                    |                            |
| Kimlik Doğrulama URL’si              | `https://login.microsoftonline.com/{TENANT-ID}/oauth2/authorize?resource=<audience>` | `audience``https://MYACCOUNT.azurehealthcareapis.com`fhır Için Azure API 'si içindir |
| Erişim Belirteci URL’si      | `https://login.microsoftonline.com/{TENANT ID}/oauth2/token`                                                    |                            |
| İstemci Kimliği             | `XXXXXXXX-XXX-XXXX-XXXX-XXXXXXXXXXXX`                                                                           | Uygulama Kimliği             |
| İstemci Gizli Anahtarı         | `XXXXXXXX`                                                                                                      | Gizli anahtar istemci anahtarı          |
| Kapsam | `<Leave Blank>` | Kapsam kullanılmıyor; Bu nedenle, boş bırakılabilir.  
| Durum                 | `1234`     | [Durum](https://learning.postman.com/docs/sending-requests/authorization/) , siteler arası istek sahteciliği engellemek için donuk bir değerdir. İsteğe bağlıdır ve ' 1234 ' gibi rastgele bir değer alabilir.                           |
| İstemci Kimlik Doğrulaması | İstemci kimlik bilgilerini gövdesinde gönder                                                                                 |                 

Azure Active Directory kimlik doğrulama akışı üzerinden kılavuzluk edecek **Istek belirtecini** seçin ve Postman 'ya bir belirteç döndürülür. Bir kimlik doğrulama hatası oluşursa, daha fazla ayrıntı için Postman konsoluna bakın. **Note**: şeritte **Görünüm**' ü ve ardından **Postman konsolunu göster**' i seçin. Postman konsolunun klavye kısayolu **alt-CTRL + C**' dir.

Döndürülen belirteç ekranını görüntülemek için aşağı kaydırın ve ardından **belirteci kullan**' ı seçin.

![Belirteç kullan](media/tutorial-postman/postman-use-token.png)

Yeni doldurulmuş belirteci görüntülemek için **erişim belirteci** alanına bakın. Kaynak aramasını yinelemek için **Gönder** `Patient` ' i seçerseniz bir **durum** `200 OK` döndürülür. Döndürülen bir durum, `200 OK` başarılı BIR http isteğini gösterir.

![200 TAMAM](media/tutorial-postman/postman-200-OK.png)

*Hasta arama* örneğinde, veritabanında arama sonucu boş olacak bir hastalar yok.

[JWT.MS](https://jwt.ms)gibi bir araç kullanarak erişim belirtecini inceleyebilirsiniz. İçerik örneği aşağıda gösterilmiştir.

```json
{
  "aud": "https://MYACCOUNT.azurehealthcareapis.com",
  "iss": "https://sts.windows.net/{TENANT-ID}/",
  "iat": 1545343803,
  "nbf": 1545343803,
  "exp": 1545347703,
  "acr": "1",
  "aio": "AUQAu/8JXXXXXXXXXdQxcxn1eis459j70Kf9DwcUjlKY3I2G/9aOnSbw==",
  "amr": [
    "pwd"
  ],
  "appid": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "oid": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "appidacr": "1",

  ...// Truncated
}
```

Sorun giderme durumlarında, doğru hedef kitleye ( `aud` talep) başlamak için iyi bir yerdir. Belirteciniz doğru sertifika verenden ( `iss` talep) ve doğru hedef kitleye ( `aud` talep) sahipse, ancak yıne de FHıR API 'sine erişemiyorsanız, Kullanıcı veya hizmet sorumlusu ( `oid` talep) fhır veri düzlemine erişemez. Kullanıcılara veri düzlemi rolleri atamak için [Azure rol tabanlı erişim denetimi (Azure RBAC)](configure-azure-rbac.md) kullanmanızı öneririz. Veri düzleiniz için harici, ikincil bir Azure Active Directory kiracısı kullanıyorsanız, [FHıR atamaları için yerel RBAC 'Yi yapılandırmanız](configure-local-rbac.md) gerekir.

[Azure CLI kullanarak fhır Için Azure API](get-healthcare-apis-access-token-cli.md)için bir belirteç almak da mümkündür. Azure CLı ile alınan bir belirteç kullanıyorsanız, yetkilendirme türü *taşıyıcı belirtecini* kullanmanız gerekir. Belirteci doğrudan içine yapıştırın.

## <a name="inserting-a-patient"></a>Hasta ekleme

Geçerli bir erişim belirteciyle, artık yeni bir hasta ekleyebilirsiniz. Postman 'da, **Gönder**' i seçerek yöntemi değiştirin ve sonra isteğin GÖVDESINE aşağıdaki JSON belgesini ekleyin.

[!code-json[](../samples/sample-patient.json)]

Hasta 'in başarıyla oluşturulduğunu belirlemek için **Gönder** ' i seçin.

![Hasta 'in başarıyla oluşturulduğunu gösteren ekran görüntüsü.](media/tutorial-postman/postman-patient-created.png)

Hasta aramasını yinelediğiniz takdirde hasta kaydını görmeniz gerekir.

![Hasta oluşturuldu](media/tutorial-postman/postman-patient-found.png)

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, Postman kullanarak FHıR için Azure API 'sine eriştiniz. FHıR özellikleri için Azure API 'SI hakkında daha fazla bilgi için bkz.
 
>[!div class="nextstepaction"]
>[Desteklenen özellikler](fhir-features-supported.md)
