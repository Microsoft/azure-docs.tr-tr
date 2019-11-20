---
title: Azure çoklu oturum kapatma SAML Protokolü | Microsoft Docs
description: Bu makalede Azure Active Directory içindeki çoklu oturum kapatma SAML Protokolü açıklanmaktadır
services: active-directory
documentationcenter: .net
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 0e4aa75d-d1ad-4bde-a94c-d8a41fb0abe6
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/19/2017
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: hirsin
ms.collection: M365-identity-device-management
ms.openlocfilehash: 66c509b1b901889241d6837611a2c373750fdb3a
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/06/2019
ms.locfileid: "68834782"
---
# <a name="single-sign-out-saml-protocol"></a>Çoklu oturum kapatma SAML Protokolü

Azure Active Directory (Azure AD), SAML 2,0 Web tarayıcısı çoklu oturum açma profilini destekler. Çoklu oturum açma 'nın düzgün çalışması için uygulama kaydı sırasında uygulamanın **LogoutURL 'Sinin** Azure AD 'ye açık olarak kaydedilmesi gerekir. Azure AD, oturumu kapattıktan sonra kullanıcıları yeniden yönlendirmek için LogoutURL 'yi kullanır.

Aşağıdaki diyagramda Azure AD çoklu oturum açma işleminin iş akışı gösterilmektedir.

![Azure AD çoklu oturum açma Iş akışı](./media/single-sign-out-saml-protocol/active-directory-saml-single-sign-out-workflow.png)

## <a name="logoutrequest"></a>LogoutRequest
Bulut hizmeti, bir oturumun `LogoutRequest` sonlandırıldığını göstermek için Azure AD 'ye bir ileti gönderir. Aşağıdaki alıntıda örnek `LogoutRequest` bir öğe gösterilmektedir.

```
<samlp:LogoutRequest xmlns="urn:oasis:names:tc:SAML:2.0:metadata" ID="idaa6ebe6839094fe4abc4ebd5281ec780" Version="2.0" IssueInstant="2013-03-28T07:10:49.6004822Z" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.workaad.com</Issuer>
  <NameID xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
</samlp:LogoutRequest>
```

### <a name="logoutrequest"></a>LogoutRequest
Azure AD 'ye gönderilen öğeaşağıdakiözniteliklerigerektirir:`LogoutRequest`

* `ID`-Bu, oturum kapatma isteğini tanımlar. Değeri bir sayıyla `ID` başlamamalıdır. Tipik uygulama, bir GUID 'nin dize gösterimine **ID** eklemek için kullanılır.
* `Version`-Bu öğenin değerini **2,0**olarak ayarlayın. Bu değer gereklidir.
* `IssueInstant`-Bu, koordinat `DateTime` evrensel saat (UTC) değeri ve [gidiş dönüş biçimi ("o")](https://msdn.microsoft.com/library/az4se3k1.aspx)içeren bir dizedir. Azure AD bu türden bir değer bekler, ancak bunu zorlamaz.

### <a name="issuer"></a>Sertifikayı Veren
İçindeki öğesi, `Issuer` Azure AD 'deki bulut hizmetindeki **ServicePrincipalNames adlarından** biriyle tam olarak eşleşmelidir. `LogoutRequest` Genellikle, bu, uygulama kaydı sırasında belirtilen **uygulama KIMLIĞI URI** 'sine ayarlanır.

### <a name="nameid"></a>NameID
`NameID` Öğesinin değeri, imzalanmakta olan kullanıcının `NameID` ile tam olarak eşleşmelidir.

## <a name="logoutresponse"></a>LogoutResponse
Azure AD, bir `LogoutResponse` `LogoutRequest` öğesine yanıt olarak bir öğesi gönderir. Aşağıdaki alıntıda bir örnek `LogoutResponse`gösterir.

```
<samlp:LogoutResponse ID="_f0961a83-d071-4be5-a18c-9ae7b22987a4" Version="2.0" IssueInstant="2013-03-18T08:49:24.405Z" InResponseTo="iddce91f96e56747b5ace6d2e2aa9d4f8c" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://sts.windows.net/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success" />
  </samlp:Status>
</samlp:LogoutResponse>
```

### <a name="logoutresponse"></a>LogoutResponse
Azure `ID`ad, `Version` öğesi`LogoutResponse` ve `IssueInstant` değerlerini ayarlar. Ayrıca, `InResponseTo` öğesini, `LogoutRequest` yanıtı kabul eden öğesinin `ID` özniteliğinin değerine ayarlar.

### <a name="issuer"></a>Sertifikayı Veren
Azure AD bu değeri, Azure `https://login.microsoftonline.com/<TenantIdGUID>/` ad \<kiracısının Kiracı kimliği olan TenantIdGUID > olarak ayarlar.

`Issuer` Öğesinin değerini değerlendirmek için uygulama kaydı sırasında belirtilen **uygulama kimliği URI** 'sinin değerini kullanın.

### <a name="status"></a>Durum
Azure AD, oturum `StatusCode` kapatma başarısını veya `Status` başarısızlığını göstermek için öğesindeki öğesini kullanır. Oturum kapatma denemesi başarısız olursa, `StatusCode` öğe özel hata iletileri de içerebilir.
