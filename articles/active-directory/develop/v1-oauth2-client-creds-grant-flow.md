---
title: OAuth 2.0 kullanarak Azure AD hizmetinden hizmete kimlik doğrulama | Microsoft Docs
description: Bu makalede, OAuth 2.0 istemci kimlik bilgileri verme akışını kullanarak hizmet kimlik doğrulamasına hizmet uygulamak için HTTP iletilerinin nasıl kullanılacağı açıklanır.
services: active-directory
documentationcenter: .net
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: a7f939d9-532d-4b6d-b6d3-95520207965d
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/08/2017
ms.author: ryanwi
ms.reviewer: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1ac618b28fae7410a773012e390dcd6b3a63b966
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/06/2019
ms.locfileid: "68834773"
---
# <a name="service-to-service-calls-using-client-credentials-shared-secret-or-certificate"></a>İstemci kimlik bilgilerini kullanarak hizmet çağrıları (paylaşılan gizlilik veya sertifika)

[!INCLUDE [active-directory-develop-applies-v1](../../../includes/active-directory-develop-applies-v1.md)]

OAuth 2,0 Istemci kimlik bilgileri verme akışı, bir Web hizmetinin (*Gizli istemci*) bir kullanıcının kimliğine bürünmek yerine kendi kimlik bilgilerini kullanmasına izin verir, başka bir Web hizmetini çağırırken kimlik doğrulaması yapabilir. Bu senaryoda, istemci genellikle bir orta katman Web hizmeti, bir Daemon hizmeti veya Web sitesidir. Daha yüksek bir güvence düzeyi için Azure AD, çağıran hizmetin kimlik bilgileri olarak bir sertifika (paylaşılan gizlilik yerine) kullanmasına de olanak tanır.

## <a name="client-credentials-grant-flow-diagram"></a>İstemci kimlik bilgileri verme akış diyagramı
Aşağıdaki diyagramda, istemci kimlik bilgileri verme akışının Azure Active Directory (Azure AD) içinde nasıl çalıştığı açıklanmaktadır.

![OAuth 2.0 Istemci kimlik bilgileri verme akışı](./media/v1-oauth2-client-creds-grant-flow/active-directory-protocols-oauth-client-credentials-grant-flow.jpg)

1. İstemci uygulaması, Azure AD belirteç verme uç noktasında kimliğini doğrular ve erişim belirteci ister.
2. Azure AD belirteç verme uç noktası erişim belirtecini yayınlar.
3. Erişim belirteci, güvenli kaynağın kimliğini doğrulamak için kullanılır.
4. Güvenli kaynaktaki veriler istemci uygulamasına döndürülür.

## <a name="register-the-services-in-azure-ad"></a>Hizmetleri Azure AD 'ye kaydetme
Hem çağıran hizmeti hem de alma hizmetini Azure Active Directory (Azure AD) olarak kaydedin. Ayrıntılı yönergeler için bkz. [uygulamaları Azure Active Directory tümleştirme](quickstart-v1-integrate-apps-with-azure-ad.md).

## <a name="request-an-access-token"></a>Erişim belirteci isteme
Erişim belirteci istemek için, kiracıya özgü Azure AD uç noktasında bir HTTP GÖNDERISI kullanın.

```
https://login.microsoftonline.com/<tenant id>/oauth2/token
```

## <a name="service-to-service-access-token-request"></a>Hizmetten hizmete erişim belirteci isteği
İstemci uygulamanın, paylaşılan bir gizliliğe veya bir sertifikayla güvenli hale getirildiğine bağlı olarak iki durum vardır.

### <a name="first-case-access-token-request-with-a-shared-secret"></a>İlk durum: Paylaşılan gizli dizi ile belirteç isteğine erişim
Paylaşılan bir gizli dizi kullanılırken hizmetten hizmete erişim belirteci isteği aşağıdaki parametreleri içerir:

| Parametre |  | Açıklama |
| --- | --- | --- |
| grant_type |gerekli |İstenen izin türünü belirtir. Istemci kimlik bilgileri verme akışında, değerin **client_credentials**olması gerekir. |
| client_id |gerekli |Çağıran Web hizmetinin Azure AD istemci kimliğini belirtir. Çağıran uygulamanın istemci KIMLIĞINI bulmak için, [Azure portal](https://portal.azure.com) **Azure Active Directory**, **uygulama kayıtları**' ye tıklayın, uygulamaya tıklayın. Client_id, *uygulama kimliğidir* |
| client_secret |gerekli |Azure AD 'de çağıran Web hizmeti veya Daemon uygulaması için kayıtlı bir anahtar girin. Bir anahtar oluşturmak için, Azure portal **Azure Active Directory**, **uygulama kayıtları**' ne tıklayın, uygulama ' ya tıklayın, **Ayarlar**' a tıklayın, **anahtarlar**' a tıklayın ve bir anahtar ekleyin.  URL-bu parolayı sağlarken kodlayın. |
| resource |gerekli |Alıcı Web hizmetinin uygulama KIMLIĞI URI 'sini girin. Uygulama KIMLIĞI URI 'sini bulmak için, Azure portal **Azure Active Directory**, **uygulama kayıtları**, hizmet uygulamasına tıklayın ve ardından **Ayarlar** ve **Özellikler**' e tıklayın. |

#### <a name="example"></a>Örnek
Aşağıdaki http post, https://service.contoso.com/ Web hizmeti için bir [erişim belirteci](access-tokens.md) ister. , `client_id` Erişim belirtecini isteyen Web hizmetini tanımlar.

```
POST /contoso.com/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials&client_id=625bc9f6-3bf6-4b6d-94ba-e97cf07a22de&client_secret=qkDwDJlDfig2IpeuUZYKH1Wb8q1V0ju6sILxQQqhJ+s=&resource=https%3A%2F%2Fservice.contoso.com%2F
```

### <a name="second-case-access-token-request-with-a-certificate"></a>İkinci durum: Bir sertifikayla erişim belirteci isteği
Bir sertifikaya sahip hizmetten hizmete erişim belirteci isteği aşağıdaki parametreleri içerir:

| Parametre |  | Açıklama |
| --- | --- | --- |
| grant_type |gerekli |İstenen yanıt türünü belirtir. Istemci kimlik bilgileri verme akışında, değerin **client_credentials**olması gerekir. |
| client_id |gerekli |Çağıran Web hizmetinin Azure AD istemci kimliğini belirtir. Çağıran uygulamanın istemci KIMLIĞINI bulmak için, [Azure portal](https://portal.azure.com) **Azure Active Directory**, **uygulama kayıtları**' ye tıklayın, uygulamaya tıklayın. Client_id, *uygulama kimliğidir* |
| client_assertion_type |gerekli |Değer şu şekilde olmalıdır`urn:ietf:params:oauth:client-assertion-type:jwt-bearer` |
| client_assertion |gerekli | Uygulamanız için kimlik bilgileri olarak kaydettiğiniz sertifikayı oluşturmanız ve oturum açmanız için gereken bir onaylama (JSON Web Token). Sertifikanızı ve onaylama biçiminizi nasıl kaydedeceğinizi öğrenmek için [sertifika kimlik bilgileri](active-directory-certificate-credentials.md) hakkında bilgi edinin.|
| resource | gerekli |Alıcı Web hizmetinin uygulama KIMLIĞI URI 'sini girin. Uygulama KIMLIĞI URI 'sini bulmak için, Azure portal **Azure Active Directory**, **uygulama kayıtları**, hizmet uygulamasına tıklayın ve ardından **Ayarlar** ve **Özellikler**' e tıklayın. |

Client_secret parametresi iki parametre ile değiştirilmeleri dışında, parametrelerin paylaşılan gizliliğe göre neredeyse aynı olduğuna dikkat edin: bir client_assertion_type ve client_assertion.

#### <a name="example"></a>Örnek
Aşağıdaki http post, bir sertifikayla https://service.contoso.com/ Web hizmeti için bir erişim belirteci ister. , `client_id` Erişim belirtecini isteyen Web hizmetini tanımlar.

```
POST /<tenant_id>/oauth2/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

resource=https%3A%2F%contoso.onmicrosoft.com%2Ffc7664b4-cdd6-43e1-9365-c2e1c4e1b3bf&client_id=97e0a5b7-d745-40b6-94fe-5f77d35c6e05&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg&grant_type=client_credentials
```

### <a name="service-to-service-access-token-response"></a>Hizmetten hizmete erişim belirteci yanıtı

Başarılı bir yanıt, aşağıdaki parametrelerle bir JSON OAuth 2,0 yanıtı içerir:

| Parametre | Açıklama |
| --- | --- |
| access_token |İstenen erişim belirteci. Çağıran Web hizmeti, alıcı Web hizmetinde kimlik doğrulaması yapmak için bu belirteci kullanabilir. |
| token_type |Belirteç türü değerini gösterir. Azure AD 'nin desteklediği tek tür **taşıyıcı**. Taşıyıcı belirteçleri hakkında daha fazla bilgi için bkz [. OAuth 2,0 yetkilendirme çerçevesi: Taşıyıcı belirteç kullanımı (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). |
| expires_in |Erişim belirtecinin geçerli olduğu süre (saniye cinsinden). |
| expires_on |Erişim belirtecinin süre sonu. Tarih, 1970-01-01T0:0: 0Z UTC 'den sona erme zamanına kadar saniye sayısı olarak gösterilir. Bu değer, önbelleğe alınmış belirteçlerin ömrünü belirlemede kullanılır. |
| not_before |Erişim belirtecinin kullanılabilir hale geldiği zaman. Tarih,, belirteç için geçerlilik süresine kadar 1970-01-01T0:0: 0Z UTC 'den saniye sayısı olarak gösterilir.|
| resource |Alıcı Web hizmetinin uygulama KIMLIĞI URI 'SI. |

#### <a name="example-of-response"></a>Yanıt örneği
Aşağıdaki örnekte, bir Web hizmetine erişim belirteci isteğine yönelik bir başarı yanıtı gösterilmektedir.

```
{
"access_token":"eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJodHRwczovL3NlcnZpY2UuY29udG9zby5jb20vIiwiaXNzIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvN2ZlODE0NDctZGE1Ny00Mzg1LWJlY2ItNmRlNTdmMjE0NzdlLyIsImlhdCI6MTM4ODQ0ODI2NywibmJmIjoxMzg4NDQ4MjY3LCJleHAiOjEzODg0NTIxNjcsInZlciI6IjEuMCIsInRpZCI6IjdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZSIsIm9pZCI6ImE5OTE5MTYyLTkyMTctNDlkYS1hZTIyLWYxMTM3YzI1Y2RlYSIsInN1YiI6ImE5OTE5MTYyLTkyMTctNDlkYS1hZTIyLWYxMTM3YzI1Y2RlYSIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzdmZTgxNDQ3LWRhNTctNDM4NS1iZWNiLTZkZTU3ZjIxNDc3ZS8iLCJhcHBpZCI6ImQxN2QxNWJjLWM1NzYtNDFlNS05MjdmLWRiNWYzMGRkNThmMSIsImFwcGlkYWNyIjoiMSJ9.aqtfJ7G37CpKV901Vm9sGiQhde0WMg6luYJR4wuNR2ffaQsVPPpKirM5rbc6o5CmW1OtmaAIdwDcL6i9ZT9ooIIicSRrjCYMYWHX08ip-tj-uWUihGztI02xKdWiycItpWiHxapQm0a8Ti1CWRjJghORC1B1-fah_yWx6Cjuf4QE8xJcu-ZHX0pVZNPX22PHYV5Km-vPTq2HtIqdboKyZy3Y4y3geOrRIFElZYoqjqSv5q9Jgtj5ERsNQIjefpyxW3EwPtFqMcDm4ebiAEpoEWRN4QYOMxnC9OUBeG9oLA0lTfmhgHLAtvJogJcYFzwngTsVo6HznsvPWy7UP3MINA",
"token_type":"Bearer",
"expires_in":"3599",
"expires_on":"1388452167",
"resource":"https://service.contoso.com/"
}
```

## <a name="see-also"></a>Ayrıca bkz.
* [Azure AD 'de OAuth 2,0](v1-protocols-oauth-code.md)
* [C# ](https://github.com/Azure-Samples/active-directory-dotnet-daemon) Hizmetin [bir sertifikayla hizmete yönelik C# ](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential) bir paylaşılan gizli anahtar ve örnekle çağrı ile hizmete bakım örneği
