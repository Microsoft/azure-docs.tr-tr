---
title: Azure çoklu oturum açma SAML Protokolü | Microsoft Docs
description: Bu makalede Azure Active Directory içindeki çoklu oturum açma SAML Protokolü açıklanmaktadır
services: active-directory
documentationcenter: .net
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: ad8437f5-b887-41ff-bd77-779ddafc33fb
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
ms.openlocfilehash: cf512f802e0e4944e6ce949830719b87301adfc4
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/06/2019
ms.locfileid: "68834813"
---
# <a name="single-sign-on-saml-protocol"></a>Çoklu oturum açma SAML Protokolü

Bu makalede, Azure Active Directory (Azure AD) tarafından çoklu oturum açma için desteklenen SAML 2,0 kimlik doğrulama istekleri ve yanıtları ele alınmaktadır.

Aşağıdaki protokol diyagramında çoklu oturum açma sırası açıklanmaktadır. Bulut hizmeti (hizmet sağlayıcısı), `AuthnRequest` (kimlik doğrulama isteği) öğesini Azure AD 'ye (kimlik sağlayıcısı) geçirmek için bir http yeniden yönlendirme bağlaması kullanır. Daha sonra Azure AD, bulut hizmetine bir `Response` öğe göndermek için http post bağlamasını kullanır.

![Çoklu oturum açma Iş akışı](./media/single-sign-on-saml-protocol/active-directory-saml-single-sign-on-workflow.png)

## <a name="authnrequest"></a>Authbir kimlik doğrulama

Bir kullanıcı kimlik doğrulaması istemek için, bulut hizmetleri Azure `AuthnRequest` ad 'ye bir öğe gönderir. Örnek SAML 2,0 `AuthnRequest` aşağıdaki örnekteki gibi görünebilir:

```
<samlp:AuthnRequest
xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
ID="id6c1c178c166d486687be4aaf5e482730"
Version="2.0" IssueInstant="2013-03-18T03:28:54.1839884Z"
xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
</samlp:AuthnRequest>
```

| Parametre |  | Açıklama |
| --- | --- | --- |
| id | Gerekli | Azure AD döndürülen yanıtın `InResponseTo` özniteliğini doldurmak için bu özniteliği kullanır. KIMLIK bir sayıyla başlamamalıdır, bu nedenle ortak bir strateji, bir GUID 'nin dize gösterimine "ID" gibi bir dizeyi eklemek için kullanılır. Örneğin, `id6c1c178c166d486687be4aaf5e482730` geçerli bir kimliğidir. |
| Version | Gerekli | Bu parametre **2,0**olarak ayarlanmalıdır. |
| IssueInstant | Gerekli | Bu, UTC değeri ve [gidiş dönüş biçimine ("o")](https://msdn.microsoft.com/library/az4se3k1.aspx)sahip bir tarih saat dizesidir. Azure AD bu türde bir DateTime değeri bekler, ancak bu değeri değerlendirmez veya kullanmaz. |
| AssertionConsumerServiceUrl | İsteğe Bağlı | Sağlanmışsa, bu parametrenin Azure AD 'deki bulut `RedirectUri` hizmeti ile eşleşmesi gerekir. |
| ForceAuthn | İsteğe Bağlı | Bu bir Boole değeridir. Doğru ise, kullanıcının Azure AD ile geçerli bir oturumu olsa bile yeniden kimlik doğrulaması yapması zorunlu olacaktır. |
| Ipassive | İsteğe Bağlı | Bu, Azure AD 'nin Kullanıcı etkileşimi olmadan kullanıcının kimlik doğrulaması yapması gerekip gerekmediğini, varsa oturum tanımlama bilgisini kullanmasını belirten bir Boole değeridir. Bu değer doğru ise, Azure AD oturum tanımlama bilgisini kullanarak kullanıcının kimliğini doğrulamaya çalışacaktır. |

Onay, `AuthnRequest` hedef, assertionconsumerserviceındex, attributeconsumerserviceındex ve ProviderName gibi diğer tüm öznitelikler **yok sayılır**.

Azure AD, `Conditions` içindeki `AuthnRequest`öğesini de yoksayar.

### <a name="issuer"></a>Sertifikayı Veren

İçindeki öğesi, `Issuer` Azure AD 'deki bulut hizmetindeki **ServicePrincipalNames adlarından** biriyle tam olarak eşleşmelidir. `AuthnRequest` Genellikle, bu, uygulama kaydı sırasında belirtilen **uygulama KIMLIĞI URI** 'sine ayarlanır.

`Issuer` Öğesi içeren bir SAML alıntısı aşağıdaki örneğe benzer şekilde görünür:

```
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
```

### <a name="nameidpolicy"></a>Nameıdpolicy

Bu öğe, yanıtta belirli bir ad kimliği biçimi ister ve Azure AD 'ye gönderilen `AuthnRequest` öğelerde isteğe bağlıdır.

Bir `NameIdPolicy` öğe aşağıdaki örneğe benzer şekilde görünür:

```
<NameIDPolicy Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent"/>
```

Sağlanmışsa, isteğe bağlı `Format` özniteliğini dahil edebilirsiniz. `NameIDPolicy` `Format` Öznitelik aşağıdaki değerlerden yalnızca birine sahip olabilir; başka herhangi bir değer hata ile sonuçlanır.

* `urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`: Azure Active Directory, bir ikili tanımlayıcı olarak NameID talebini yayınlar.
* `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`: Azure Active Directory, e-posta adresi biçiminde NameID talebini yayınlar.
* `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified`: Bu değer, Azure Active Directory talep biçimini seçmesine izin verir. Azure Active Directory, NameID 'yi ikili bir tanımlayıcı olarak verir.
* `urn:oasis:names:tc:SAML:2.0:nameid-format:transient`: Azure Active Directory, NameID talebini geçerli SSO işlemine özgü rastgele oluşturulmuş bir değer olarak verir. Bu, değerin geçici olduğu ve kimliği doğrulanmış kullanıcıyı tanımlamak için kullanılamayan anlamına gelir.

Azure AD, `AllowCreate` özniteliğini yoksayar.

### <a name="requestauthncontext"></a>RequestAuthnContext
`RequestedAuthnContext` Öğesi, istenen kimlik doğrulama yöntemlerini belirtir. Azure AD 'ye gönderilen `AuthnRequest` öğelerde isteğe bağlıdır. Azure AD, `AuthnContextClassRef` gibi değerleri `urn:oasis:names:tc:SAML:2.0:ac:classes:Password`destekler.

### <a name="scoping"></a>Kapsamlar
Kimlik sağlayıcılarının bir listesini içeren `AuthnRequest` öğesi,AzureAD'yegönderilenöğelerdeisteğebağlıdır.`Scoping`

Sağlanmışsa, desteklenmeyen `ProxyCount` `IDPListOption` özniteliği veya `RequesterID` öğesini eklemeyin.

### <a name="signature"></a>İmza
Azure AD imzalı `Signature` kimlik doğrulama `AuthnRequest` isteklerini desteklemediği için öğelere öğe eklemeyin.

### <a name="subject"></a>Subject
Azure AD, `Subject` `AuthnRequest` öğelerin öğesini yoksayar.

## <a name="response"></a>Yanıt
İstenen bir oturum açma işlemi başarıyla tamamlandığında, Azure AD, bulut hizmetine bir yanıt gönderir. Başarılı bir oturum açma girişimine yanıt aşağıdaki örneğe benzer şekilde görünür:

```
<samlp:Response ID="_a4958bfd-e107-4e67-b06d-0d85ade2e76a" Version="2.0" IssueInstant="2013-03-18T07:38:15.144Z" Destination="https://contoso.com/identity/inboundsso.aspx" InResponseTo="id758d0ef385634593a77bdf7e632984b6" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <ds:Signature xmlns:ds="https://www.w3.org/2000/09/xmldsig#">
    ...
  </ds:Signature>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success" />
  </samlp:Status>
  <Assertion ID="_bf9c623d-cc20-407a-9a59-c2d0aee84d12" IssueInstant="2013-03-18T07:38:15.144Z" Version="2.0" xmlns="urn:oasis:names:tc:SAML:2.0:assertion">
    <Issuer>https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
    <ds:Signature xmlns:ds="https://www.w3.org/2000/09/xmldsig#">
      ...
    </ds:Signature>
    <Subject>
      <NameID>Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
      <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
        <SubjectConfirmationData InResponseTo="id758d0ef385634593a77bdf7e632984b6" NotOnOrAfter="2013-03-18T07:43:15.144Z" Recipient="https://contoso.com/identity/inboundsso.aspx" />
      </SubjectConfirmation>
    </Subject>
    <Conditions NotBefore="2013-03-18T07:38:15.128Z" NotOnOrAfter="2013-03-18T08:48:15.128Z">
      <AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
      </AudienceRestriction>
    </Conditions>
    <AttributeStatement>
      <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name">
        <AttributeValue>testuser@contoso.com</AttributeValue>
      </Attribute>
      <Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">
        <AttributeValue>3F2504E0-4F89-11D3-9A0C-0305E82C3301</AttributeValue>
      </Attribute>
      ...
    </AttributeStatement>
    <AuthnStatement AuthnInstant="2013-03-18T07:33:56.000Z" SessionIndex="_bf9c623d-cc20-407a-9a59-c2d0aee84d12">
      <AuthnContext>
        <AuthnContextClassRef> urn:oasis:names:tc:SAML:2.0:ac:classes:Password</AuthnContextClassRef>
      </AuthnContext>
    </AuthnStatement>
  </Assertion>
</samlp:Response>
```

### <a name="response"></a>Yanıt

`Response` Öğesi, yetkilendirme isteğinin sonucunu içerir. Azure `ID`ad, `Version` öğesi`Response` ve `IssueInstant` değerlerini ayarlar. Ayrıca, aşağıdaki öznitelikleri de ayarlar:

* `Destination`: Oturum açma başarıyla tamamlandığında, bu hizmet sağlayıcısına (bulut hizmeti) `RedirectUri` ayarlanır.
* `InResponseTo`: Bu, yanıtı başlatan `ID` `AuthnRequest` öğenin özniteliğine ayarlanır.

### <a name="issuer"></a>Sertifikayı Veren

Azure AD, `Issuer` `https://login.microsoftonline.com/<TenantIDGUID>/` öğesini TenantIDGUID > 'ın \<Azure AD kiracısının Kiracı kimliği olduğu yere ayarlar.

Örneğin, veren öğesiyle bir yanıt aşağıdaki örneğe benzeyebilir:

```
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
```

### <a name="status"></a>Durum

`Status` Öğesi, oturum açma başarısını veya başarısızlığını alır. Bu, bir `StatusCode` kodu veya isteğin durumunu temsil eden iç içe geçmiş kodların kümesini içeren öğesini içerir. Ayrıca, oturum açma `StatusMessage` işlemi sırasında oluşturulan özel hata iletilerini içeren öğesini de içerir.

<!-- TODO: Add an authentication protocol error reference -->

Aşağıdaki örnek, başarısız bir oturum açma girişimine bir SAML yanıtı örneğidir.

```
<samlp:Response ID="_f0961a83-d071-4be5-a18c-9ae7b22987a4" Version="2.0" IssueInstant="2013-03-18T08:49:24.405Z" InResponseTo="iddce91f96e56747b5ace6d2e2aa9d4f8c" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://sts.windows.net/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Requester">
      <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:RequestUnsupported" />
    </samlp:StatusCode>
    <samlp:StatusMessage>AADSTS75006: An error occurred while processing a SAML2 Authentication request. AADSTS90011: The SAML authentication request property 'NameIdentifierPolicy/SPNameQualifier' is not supported.
Trace ID: 66febed4-e737-49ff-ac23-464ba090d57c
Timestamp: 2013-03-18 08:49:24Z</samlp:StatusMessage>
  </samlp:Status>
```

### <a name="assertion"></a>Onaylama işlemi

, `ID` `IssueInstant` `Assertion` Ve 'aekolarak,AzureADyanıtınöğesindeaşağıdakiöğeleriayarlar.`Version`

#### <a name="issuer"></a>Sertifikayı Veren

Bu, Azure AD `https://sts.windows.net/<TenantIDGUID>/`kiracısının Kiracı kimliği olan \<TenantIDGUID > olarak ayarlanır.

```
<Issuer>https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
```

#### <a name="signature"></a>İmza

Azure AD, başarılı bir oturum açmaya yanıt olarak onaylama işlemi imzalar. `Signature` Öğesi, bir bulut hizmetinin onay bütünlüğünü doğrulamak üzere kaynağın kimliğini doğrulamak için kullanabileceği dijital bir imza içerir.

Bu dijital imzayı oluşturmak için Azure AD, meta veri belgesinin `IDPSSODescriptor` öğesinde imzalama anahtarını kullanır.

```
<ds:Signature xmlns:ds="https://www.w3.org/2000/09/xmldsig#">
      digital_signature_here
    </ds:Signature>
```

#### <a name="subject"></a>Subject

Bu, onay içindeki deyimlerin konusu olan sorumluyu belirtir. Kimliği doğrulanmış kullanıcıyı `NameID` temsil eden bir öğesi içerir. `NameID` Değer, yalnızca belirtecin hedef kitlesi olan hizmet sağlayıcısına yönlendirilen hedeflenen bir tanıtıcıdır. Kalıcı, iptal edilebilir, ancak hiçbir şekilde yeniden atanmaz. Ayrıca, kullanıcı hakkındaki herhangi bir şeyi açığa çıkarmadığından ve öznitelik sorguları için bir tanımlayıcı olarak kullanılamaz.

Öğesinin özniteliği her zaman olarak `urn:oasis:names:tc:SAML:2.0:cm:bearer`ayarlanır. `Method` `SubjectConfirmation`

```
<Subject>
      <NameID>Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
      <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
        <SubjectConfirmationData InResponseTo="id758d0ef385634593a77bdf7e632984b6" NotOnOrAfter="2013-03-18T07:43:15.144Z" Recipient="https://contoso.com/identity/inboundsso.aspx" />
      </SubjectConfirmation>
</Subject>
```

#### <a name="conditions"></a>Koşullar

Bu öğe, SAML onaylamaları için kabul edilebilir kullanımını tanımlayan koşulları belirtir.

```
<Conditions NotBefore="2013-03-18T07:38:15.128Z" NotOnOrAfter="2013-03-18T08:48:15.128Z">
      <AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
      </AudienceRestriction>
</Conditions>
```

`NotBefore` Ve`NotOnOrAfter` öznitelikleri, onaylaması geçerli olduğu aralığı belirtir.

* `NotBefore` Özniteliğin değeri, `Assertion` öğenin `IssueInstant` özniteliği değerinden daha sonra eşittir veya biraz (saniyeden küçüktür). Azure AD, kendisiyle bulut hizmeti (hizmet sağlayıcısı) arasında herhangi bir zaman farkı hesaba almaz ve bu saate herhangi bir arabellek eklemez.
* `NotOnOrAfter` Özniteliğin değeri, `NotBefore` öznitelik değerinden daha sonra 70 dakikadır.

#### <a name="audience"></a>Hedef kitle

Bu, amaçlanan bir izleyiciyi tanımlayan bir URI içerir. Azure AD, bu öğenin `Issuer` değerini, `AuthnRequest` oturum açmayı Başlatan öğesinin değerine ayarlar. `Audience` Değeri değerlendirmek için, uygulama kaydı sırasında belirtilen `App ID URI` öğesinin değerini kullanın.

```
<AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
</AudienceRestriction>
```

`Issuer` Değer gibi`Audience` , değer de Azure AD 'de bulut hizmetini temsil eden hizmet sorumlusu adlarından biriyle tam olarak eşleşmelidir. Ancak, `Issuer` öğesinin değeri bir URI değeri değilse `Audience` , yanıttaki `Issuer` değer önekli `spn:`değerdir.

#### <a name="attributestatement"></a>AttributeStatement

Bu konu veya Kullanıcı hakkında talepler içerir. Aşağıdaki alıntıda örnek `AttributeStatement` bir öğe içeriyor. Üç nokta, öğesinin birden çok öznitelik ve öznitelik değeri içerebileceğini belirtir.

```
<AttributeStatement>
      <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name">
        <AttributeValue>testuser@contoso.com</AttributeValue>
      </Attribute>
      <Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">
        <AttributeValue>3F2504E0-4F89-11D3-9A0C-0305E82C3301</AttributeValue>
      </Attribute>
      ...
</AttributeStatement>
```        

* **Ad talebi** - `Name` özniteliğin değeri (`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`), kimliği doğrulanmış kullanıcının Kullanıcı asıl adıdır, örneğin `testuser@managedtenant.com`.
* **Objectıdentifier Claim** - `ObjectIdentifier` özniteliğin değeri `ObjectId` (`http://schemas.microsoft.com/identity/claims/objectidentifier`), Azure AD 'de kimliği doğrulanmış kullanıcıyı temsil eden dizin nesnesidir. `ObjectId`, sabit, genel olarak benzersiz ve kimliği doğrulanmış kullanıcının güvenli tanımlayıcısını yeniden kullanır.

#### <a name="authnstatement"></a>Authndeyim

Bu öğe, onaylama konusunun belirli bir zamanda belirli bir anlama göre doğrulandığını onaylar.

* `AuthnInstant` Öznitelik, kullanıcının kimlik doğrulamasının Azure AD ile süresini belirtir.
* `AuthnContext` Öğesi, kullanıcının kimliğini doğrulamak için kullanılan kimlik doğrulaması bağlamını belirtir.

```
<AuthnStatement AuthnInstant="2013-03-18T07:33:56.000Z" SessionIndex="_bf9c623d-cc20-407a-9a59-c2d0aee84d12">
      <AuthnContext>
        <AuthnContextClassRef> urn:oasis:names:tc:SAML:2.0:ac:classes:Password</AuthnContextClassRef>
      </AuthnContext>
</AuthnStatement>
```
