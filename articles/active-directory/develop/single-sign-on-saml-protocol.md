---
title: Azure çoklu oturum açma SAML Protokolü
titleSuffix: Microsoft identity platform
description: Bu makalede Azure Active Directory ' de tek Sign-On (SSO) SAML Protokolü açıklanmaktadır
services: active-directory
documentationcenter: .net
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 05/18/2020
ms.author: kenwith
ms.custom: aaddev
ms.reviewer: paulgarn
ms.openlocfilehash: 40bf202e0f14f18d817e4e918f8372ba3c0a4ad8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91950678"
---
# <a name="single-sign-on-saml-protocol"></a>Çoklu Oturum Açma SAML protokolü

Bu makalede, Azure Active Directory (Azure AD) tarafından tek Sign-On (SSO) için desteklenen SAML 2,0 kimlik doğrulama istekleri ve yanıtları ele alınmaktadır.

Aşağıdaki protokol diyagramında çoklu oturum açma sırası açıklanmaktadır. Bulut hizmeti (hizmet sağlayıcısı), `AuthnRequest` (kimlik doğrulama isteği) öğesini Azure AD 'ye (kimlik sağlayıcısı) geçirmek için BIR http yeniden yönlendirme bağlaması kullanır. Daha sonra Azure AD, bulut hizmetine bir öğe göndermek için HTTP POST bağlamasını kullanır `Response` .

![Tek Sign-On (SSO) Iş akışı](./media/single-sign-on-saml-protocol/active-directory-saml-single-sign-on-workflow.png)

> [!NOTE]
> Bu makalede çoklu oturum açma için SAML kullanımı ele alınmaktadır. Çoklu oturum açmayı işlemenin diğer yolları hakkında daha fazla bilgi için (örneğin, OpenID Connect veya tümleşik Windows kimlik doğrulaması kullanarak) bkz. [Azure Active Directory uygulamalarda çoklu oturum açma](../manage-apps/what-is-single-sign-on.md).

## <a name="authnrequest"></a>Authbir kimlik doğrulama

Bir kullanıcı kimlik doğrulaması istemek için, bulut Hizmetleri `AuthnRequest` Azure AD 'ye bir öğe gönderir. Örnek SAML 2,0 `AuthnRequest` Aşağıdaki örnekteki gibi görünebilir:

```
<samlp:AuthnRequest
xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
ID="id6c1c178c166d486687be4aaf5e482730"
Version="2.0" IssueInstant="2013-03-18T03:28:54.1839884Z"
xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
</samlp:AuthnRequest>
```

| Parametre | Tür | Description |
| --- | --- | --- |
| ID | Gerekli | Azure AD döndürülen yanıtın özniteliğini doldurmak için bu özniteliği kullanır `InResponseTo` . KIMLIK bir sayıyla başlamamalıdır, bu nedenle ortak bir strateji, bir GUID 'nin dize gösterimine "ID" gibi bir dizeyi eklemek için kullanılır. Örneğin, `id6c1c178c166d486687be4aaf5e482730` geçerli BIR kimliğidir. |
| Sürüm | Gerekli | Bu parametre **2,0** olarak ayarlanmalıdır. |
| IssueInstant | Gerekli | Bu, UTC değeri ve [gidiş dönüş biçimine ("o")](/dotnet/standard/base-types/standard-date-and-time-format-strings)sahip bir tarih saat dizesidir. Azure AD bu türde bir DateTime değeri bekler, ancak bu değeri değerlendirmez veya kullanmaz. |
| AssertionConsumerServiceURL | İsteğe Bağlı | Sağlanmışsa, bu parametrenin `RedirectUri` Azure AD 'deki bulut hizmeti ile eşleşmesi gerekir. |
| ForceAuthn | İsteğe Bağlı | Bu bir Boole değeridir. Doğru ise, kullanıcının Azure AD ile geçerli bir oturumu olsa bile yeniden kimlik doğrulaması yapması zorunlu olacaktır. |
| Ipassive | İsteğe Bağlı | Bu, Azure AD 'nin Kullanıcı etkileşimi olmadan kullanıcının kimlik doğrulaması yapması gerekip gerekmediğini, varsa oturum tanımlama bilgisini kullanmasını belirten bir Boole değeridir. Bu değer doğru ise, Azure AD oturum tanımlama bilgisini kullanarak kullanıcının kimliğini doğrulamaya çalışacaktır. |

`AuthnRequest`Onay, hedef, Assertionconsumerserviceındex, Attributeconsumerserviceındex ve ProviderName gibi diğer tüm öznitelikler **yok sayılır**.

Azure AD, `Conditions` içindeki öğesini de yoksayar `AuthnRequest` .

### <a name="issuer"></a>Veren

`Issuer`İçindeki öğesi, `AuthnRequest` Azure AD 'deki bulut hizmetindeki **ServicePrincipalNames adlarından** biriyle tam olarak eşleşmelidir. Genellikle, bu, uygulama kaydı sırasında belirtilen **uygulama KIMLIĞI URI** 'sine ayarlanır.

Öğesi içeren bir SAML alıntısı `Issuer` aşağıdaki örneğe benzer şekilde görünür:

```
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
```

### <a name="nameidpolicy"></a>Nameıdpolicy

Bu öğe, yanıtta belirli bir ad KIMLIĞI biçimi ister ve `AuthnRequest` Azure AD 'ye gönderilen öğelerde isteğe bağlıdır.

Bir `NameIdPolicy` öğe aşağıdaki örneğe benzer şekilde görünür:

```
<NameIDPolicy Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent"/>
```

`NameIDPolicy`Sağlanmışsa, isteğe bağlı özniteliğini dahil edebilirsiniz `Format` . `Format`Öznitelik aşağıdaki değerlerden yalnızca birine sahip olabilir; başka herhangi bir değer hata ile sonuçlanır.

* `urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`: Azure Active Directory, bir ikili tanımlayıcı olarak NameID talebini yayınlar.
* `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`: Azure Active Directory, e-posta adresi biçiminde NameID talebini yayınlar.
* `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified`: Bu değer, Azure Active Directory talep biçimini seçmesine izin verir. Azure Active Directory, NameID 'yi ikili bir tanımlayıcı olarak verir.
* `urn:oasis:names:tc:SAML:2.0:nameid-format:transient`: Azure Active Directory NameID talebini, geçerli SSO işlemi için benzersiz olan rastgele oluşturulmuş bir değer olarak verir. Bu, değerin geçici olduğu ve kimliği doğrulanmış kullanıcıyı tanımlamak için kullanılamayan anlamına gelir.

`SPNameQualifier`Belirtilmişse, Azure AD yanıta aynı şekilde dahil edilir `SPNameQualifier` .

Azure AD, `AllowCreate` özniteliğini yoksayar.

### <a name="requestauthncontext"></a>RequestAuthnContext
`RequestedAuthnContext`Öğesi, istenen kimlik doğrulama yöntemlerini belirtir. `AuthnRequest`Azure AD 'ye gönderilen öğelerde isteğe bağlıdır. Azure AD, gibi `AuthnContextClassRef` değerleri destekler `urn:oasis:names:tc:SAML:2.0:ac:classes:Password` .

### <a name="scoping"></a>Kapsamlar
`Scoping`Kimlik sağlayıcılarının bir listesini içeren öğesi, `AuthnRequest` Azure AD 'ye gönderilen öğelerde isteğe bağlıdır.

Sağlanmışsa, `ProxyCount` `IDPListOption` Desteklenmeyen özniteliği veya `RequesterID` öğesini eklemeyin.

### <a name="signature"></a>İmza
`Signature`Öğeleri içindeki bir öğesi `AuthnRequest` isteğe bağlıdır. İmza varsa, Azure AD imzalı kimlik doğrulama isteklerini doğrulamaz. İstek sahibi doğrulama, yalnızca kayıtlı onaylama tüketici hizmeti URL 'Lerine yanıt vererek sağlanır.

### <a name="subject"></a>Konu
Bir öğesi eklemeyin `Subject` . Azure AD, bir istek için konunun belirtilmesini desteklemez ve sağlandıysa bir hata döndürür.

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

`Response`Öğesi, yetkilendirme isteğinin sonucunu içerir. Azure AD, `ID` `Version` `IssueInstant` öğesi ve değerlerini ayarlar `Response` . Ayrıca, aşağıdaki öznitelikleri de ayarlar:

* `Destination`: Oturum açma başarıyla tamamlandığında, bu `RedirectUri` hizmet sağlayıcısına (bulut hizmeti) ayarlanır.
* `InResponseTo`: Bu, `ID` `AuthnRequest` yanıtı Başlatan öğenin özniteliğine ayarlanır.

### <a name="issuer"></a>Veren

Azure AD, `Issuer` öğesini `https://sts.windows.net/<TenantIDGUID>/` \<TenantIDGUID> Azure AD KIRACıSıNıN Kiracı kimliği olduğu konum olarak ayarlar.

Örneğin, veren öğesiyle bir yanıt aşağıdaki örneğe benzeyebilir:

```
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> https://sts.windows.net/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
```

### <a name="status"></a>Durum

`Status`Öğesi, oturum açma başarısını veya başarısızlığını alır. Bu, `StatusCode` bir kodu veya isteğin durumunu temsil eden iç içe geçmiş kodların kümesini içeren öğesini içerir. Ayrıca, `StatusMessage` oturum açma işlemi sırasında oluşturulan özel hata iletilerini içeren öğesini de içerir.

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

, Ve ' a ek olarak, `ID` `IssueInstant` `Version` Azure AD yanıtın öğesinde aşağıdaki öğeleri ayarlar `Assertion` .

#### <a name="issuer"></a>Veren

Bu, `https://sts.windows.net/<TenantIDGUID>/` \<TenantIDGUID> Azure AD kiracısının Kiracı kimliği olduğu yere ayarlanır.

```
<Issuer>https://sts.windows.net/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
```

#### <a name="signature"></a>İmza

Azure AD, başarılı bir oturum açmaya yanıt olarak onaylama işlemi imzalar. `Signature`Öğesi, bir bulut hizmetinin onay bütünlüğünü doğrulamak üzere kaynağın kimliğini doğrulamak için kullanabileceği dijital bir imza içerir.

Bu dijital imzayı oluşturmak için Azure AD, `IDPSSODescriptor` meta veri belgesinin öğesinde imzalama anahtarını kullanır.

```
<ds:Signature xmlns:ds="https://www.w3.org/2000/09/xmldsig#">
      digital_signature_here
    </ds:Signature>
```

#### <a name="subject"></a>Konu

Bu, onay içindeki deyimlerin konusu olan sorumluyu belirtir. `NameID`Kimliği doğrulanmış kullanıcıyı temsil eden bir öğesi içerir. `NameID`Değer, yalnızca belirtecin hedef kitlesi olan hizmet sağlayıcısına yönlendirilen hedeflenen bir tanıtıcıdır. Kalıcı, iptal edilebilir, ancak hiçbir şekilde yeniden atanmaz. Ayrıca, kullanıcı hakkındaki herhangi bir şeyi açığa çıkarmadığından ve öznitelik sorguları için bir tanımlayıcı olarak kullanılamaz.

`Method` `SubjectConfirmation` Öğesinin özniteliği her zaman olarak ayarlanır `urn:oasis:names:tc:SAML:2.0:cm:bearer` .

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

`NotBefore`Ve `NotOnOrAfter` öznitelikleri, onaylaması geçerli olduğu aralığı belirtir.

* Özniteliğin değeri, `NotBefore` öğenin özniteliği değerinden daha sonra eşittir veya biraz (saniyeden küçüktür) `IssueInstant` `Assertion` . Azure AD, kendisiyle bulut hizmeti (hizmet sağlayıcısı) arasında herhangi bir zaman farkı hesaba almaz ve bu saate herhangi bir arabellek eklemez.
* Özniteliğin değeri, `NotOnOrAfter` öznitelik değerinden daha sonra 70 dakikadır `NotBefore` .

#### <a name="audience"></a>Hedef kitle

Bu, amaçlanan bir izleyiciyi tanımlayan bir URI içerir. Azure AD, bu öğenin değerini `Issuer` , `AuthnRequest` oturum açmayı Başlatan öğesinin değerine ayarlar. Değeri değerlendirmek için `Audience` , `App ID URI` uygulama kaydı sırasında belirtilen öğesinin değerini kullanın.

```
<AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
</AudienceRestriction>
```

Değer gibi `Issuer` , `Audience` değer de Azure AD 'de bulut hizmetini temsil eden hizmet sorumlusu adlarından biriyle tam olarak eşleşmelidir. Ancak, `Issuer` öğesinin değeri BIR URI değeri değilse, `Audience` yanıttaki değer `Issuer` önekli değerdir `spn:` .

#### <a name="attributestatement"></a>AttributeStatement

Bu konu veya Kullanıcı hakkında talepler içerir. Aşağıdaki alıntıda örnek bir `AttributeStatement` öğe içeriyor. Üç nokta, öğesinin birden çok öznitelik ve öznitelik değeri içerebileceğini belirtir.

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

* **Ad talebi** - `Name` özniteliğin değeri ( `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` ), kimliği doğrulanmış kullanıcının Kullanıcı asıl adıdır, örneğin `testuser@managedtenant.com` .
* **Objectıdentifier Claim** - `ObjectIdentifier` özniteliğin değeri ( `http://schemas.microsoft.com/identity/claims/objectidentifier` ), `ObjectId` Azure AD 'de kimliği doğrulanmış kullanıcıyı temsil eden dizin nesnesidir. `ObjectId` , sabit, genel olarak benzersiz ve kimliği doğrulanmış kullanıcının güvenli tanımlayıcısını yeniden kullanır.

#### <a name="authnstatement"></a>Authndeyim

Bu öğe, onaylama konusunun belirli bir zamanda belirli bir anlama göre doğrulandığını onaylar.

* `AuthnInstant`Öznitelik, kullanıcının kimlik doğrulamasının Azure AD ile süresini belirtir.
* `AuthnContext`Öğesi, kullanıcının kimliğini doğrulamak için kullanılan kimlik doğrulaması bağlamını belirtir.

```
<AuthnStatement AuthnInstant="2013-03-18T07:33:56.000Z" SessionIndex="_bf9c623d-cc20-407a-9a59-c2d0aee84d12">
      <AuthnContext>
        <AuthnContextClassRef> urn:oasis:names:tc:SAML:2.0:ac:classes:Password</AuthnContextClassRef>
      </AuthnContext>
</AuthnStatement>
```
