---
title: Azure çoklu oturum kapatma SAML Protokolü
description: Bu makalede, Azure Active Directory içindeki tek Sign-Out SAML Protokolü açıklanmaktadır
services: active-directory
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 03/22/2021
ms.author: kenwith
ms.custom: aaddev
ms.reviewer: paulgarn
ms.openlocfilehash: 040e49ee870704107e1d4118f1484119d98a9ebf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104781220"
---
# <a name="single-sign-out-saml-protocol"></a>Tek Sign-Out SAML Protokolü

Azure Active Directory (Azure AD), SAML 2,0 Web tarayıcısı çoklu oturum açma profilini destekler. Çoklu oturum açma 'nın düzgün çalışması için uygulama kaydı sırasında uygulamanın **LogoutURL 'Sinin** Azure AD 'ye açık olarak kaydedilmesi gerekir. Uygulama [Azure Uygulama Galerisine eklenirse](v2-howto-app-gallery-listing.md) , bu değer varsayılan olarak ayarlanabilir. Aksi takdirde, değer belirlenmesi ve uygulamayı Azure AD kiracısına ekleyen kişi tarafından ayarlanması gerekir. Azure AD, oturumu kapattıktan sonra kullanıcıları yeniden yönlendirmek için LogoutURL 'yi kullanır. 

Azure AD yeniden yönlendirme bağlamasını (HTTP GET), HTTP SONRASı bağlamayı destekler.

Aşağıdaki diyagramda Azure AD çoklu oturum açma işleminin iş akışı gösterilmektedir.

![Azure AD çoklu oturum açma Iş akışı](./media/single-sign-out-saml-protocol/active-directory-saml-single-sign-out-workflow.png)

## <a name="logoutrequest"></a>LogoutRequest
Bulut hizmeti, `LogoutRequest` bir oturumun sonlandırıldığını göstermek Için Azure AD 'ye bir ileti gönderir. Aşağıdaki alıntıda örnek bir `LogoutRequest` öğe gösterilmektedir.

```
<samlp:LogoutRequest xmlns="urn:oasis:names:tc:SAML:2.0:metadata" ID="idaa6ebe6839094fe4abc4ebd5281ec780" Version="2.0" IssueInstant="2013-03-28T07:10:49.6004822Z" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.workaad.com</Issuer>
  <NameID xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
</samlp:LogoutRequest>
```

### <a name="logoutrequest"></a>LogoutRequest
`LogoutRequest`Azure AD 'ye gönderilen öğe aşağıdaki öznitelikleri gerektirir:

* `ID` -Bu, oturum kapatma isteğini tanımlar. Değeri `ID` bir sayıyla başlamamalıdır. Tipik uygulama, bir GUID 'nin dize gösterimine **ID** eklemek için kullanılır.
* `Version` -Bu öğenin değerini **2,0** olarak ayarlayın. Bu değer gereklidir.
* `IssueInstant` -Bu, `DateTime` koordinat evrensel saat (UTC) değeri ve [gidiş dönüş biçimi ("o")](/dotnet/standard/base-types/standard-date-and-time-format-strings)içeren bir dizedir. Azure AD bu türden bir değer bekler, ancak bunu zorlamaz.

### <a name="issuer"></a>Veren
`Issuer`İçindeki öğesi, `LogoutRequest` Azure AD 'deki bulut hizmetindeki **ServicePrincipalNames adlarından** biriyle tam olarak eşleşmelidir. Genellikle, bu, uygulama kaydı sırasında belirtilen **uygulama KIMLIĞI URI** 'sine ayarlanır.

### <a name="nameid"></a>NameID
Öğesinin değeri, `NameID` `NameID` imzalanmakta olan kullanıcının ile tam olarak eşleşmelidir.

## <a name="logoutresponse"></a>LogoutResponse
Azure AD, bir `LogoutResponse` öğesine yanıt olarak bir `LogoutRequest` öğesi gönderir. Aşağıdaki alıntıda bir örnek gösterir `LogoutResponse` .

```
<samlp:LogoutResponse ID="_f0961a83-d071-4be5-a18c-9ae7b22987a4" Version="2.0" IssueInstant="2013-03-18T08:49:24.405Z" InResponseTo="iddce91f96e56747b5ace6d2e2aa9d4f8c" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://sts.windows.net/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success" />
  </samlp:Status>
</samlp:LogoutResponse>
```

### <a name="logoutresponse"></a>LogoutResponse
Azure AD, `ID` `Version` `IssueInstant` öğesi ve değerlerini ayarlar `LogoutResponse` . Ayrıca, `InResponseTo` öğesini `ID` , `LogoutRequest` yanıtı kabul eden öğesinin özniteliğinin değerine ayarlar.

### <a name="issuer"></a>Veren
Azure AD bu değeri, `https://login.microsoftonline.com/<TenantIdGUID>/` \<TenantIdGUID> Azure AD kiracısının Kiracı kimliği olduğu yere ayarlar.

Öğesinin değerini değerlendirmek için `Issuer` uygulama kaydı sırasında belirtilen **uygulama kimliği URI** 'sinin değerini kullanın.

### <a name="status"></a>Durum
Azure AD, `StatusCode` `Status` oturum kapatma başarısını veya başarısızlığını göstermek için öğesindeki öğesini kullanır. Oturum kapatma denemesi başarısız olursa, `StatusCode` öğe özel hata iletileri de içerebilir.
