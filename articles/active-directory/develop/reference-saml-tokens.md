---
title: Azure AD tarafından desteklenen farklı belirteç ve talep türleri hakkında bilgi edinin | Microsoft Docs
description: Azure Active Directory (AAD) tarafından verilen SAML 2,0 ve JSON Web belirteçleri (JWT) belirteçlerinde talepleri anlamak ve değerlendirmek için bir kılavuz
documentationcenter: na
author: rwike77
services: active-directory
manager: CelesteDG
editor: ''
ms.assetid: 166aa18e-1746-4c5e-b382-68338af921e2
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/22/2018
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: af8f1e9150efca4441356a80c263a58fafefcfaf
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/06/2019
ms.locfileid: "68834847"
---
# <a name="azure-ad-saml-token-reference"></a>Azure AD SAML belirteci başvurusu

Azure Active Directory (Azure AD), her kimlik doğrulama akışının işlenmesinde birkaç tür güvenlik belirteci yayar. Bu belge, her bir belirteç türünün biçimini, güvenlik özelliklerini ve içeriğini açıklar.

## <a name="claims-in-saml-tokens"></a>SAML belirteçlerinde talepler

> [!div class="mx-codeBreakAll"]
> | Ad | Denk JWT talebi | Açıklama | Örnek |
> | --- | --- | --- | ------------|
> |Hedef kitle | `aud` |Belirtecin amaçlanan alıcısı. Belirteci alan uygulamanın, hedef kitle değerinin doğru olduğunu ve farklı bir hedef kitle için tasarlanan belirteçleri reddetmesini doğrulaması gerekir. | `<AudienceRestriction>`<br>`<Audience>`<br>`https://contoso.com`<br>`</Audience>`<br>`</AudienceRestriction>`  |
> | Kimlik Doğrulaması Anı | |Kimlik doğrulamasının gerçekleştiği tarihi ve saati kaydeder. | `<AuthnStatement AuthnInstant="2011-12-29T05:35:22.000Z">` | 
> |Kimlik Doğrulama Yöntemi | `amr` |Belirtecin konusunun nasıl doğrulandığını tanımlar. | `<AuthnContextClassRef>`<br>`http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod/password`<br>`</AuthnContextClassRef>` |
> |Ad | `given_name` |Azure AD Kullanıcı nesnesinde ayarlandığı gibi, kullanıcının ilk veya "verilen" adını sağlar. | `<Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname">`<br>`<AttributeValue>Frank<AttributeValue>`  |
> |Gruplar | `groups` |Konunun grup üyeliklerini temsil eden nesne kimliklerini sağlar. Bu değerler benzersizdir (bkz. nesne KIMLIĞI) ve bir kaynağa erişmek için Yetkilendirmeyi zorlama gibi erişimi yönetmek için güvenli bir şekilde kullanılabilir. Gruplar talebine dahil olan gruplar uygulama bildiriminin "Groupmembershipclaim" özelliği aracılığıyla uygulama başına temelinde yapılandırılır. Null değeri tüm grupları dışlayacak, "SecurityGroup" değeri yalnızca Active Directory güvenlik grubu üyeliklerini içerir ve "All" değeri hem güvenlik gruplarını hem de Office 365 dağıtım listelerini içerir. <br><br> **Notlar**: <br> Kullanıcının sayısı bir sınırı (SAML için 150 200, JWT için) üzerine gittiğinde, fazla kullanım talebi, Kullanıcı için Grup listesini içeren grafik uç noktasına işaret eden talep kaynaklarını da eklenecektir. 'ndaki. | `<Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/groups">`<br>`<AttributeValue>07dd8a60-bf6d-4e17-8844-230b77145381</AttributeValue>` |
> | Gruplar fazla kullanım göstergesi | `groups:src1` | Sınırlı olmayan Belirteç istekleri (yukarıya bakın `hasgroups` ), ancak belirteç için hala çok büyük olması için, kullanıcının tam gruplar listesine bir bağlantı dahil edilir. SAML için bu, `groups` talebin yerine yeni bir talep olarak eklenir. | `<Attribute Name=" http://schemas.microsoft.com/claims/groups.link">`<br>`<AttributeValue>https://graph.windows.net/{tenantID}/users/{userID}/getMemberObjects<AttributeValue>` |
> |Kimlik Sağlayıcı | `idp` |Belirtecin öznesinin kimliğini doğrulayan kimlik sağlayıcısını kaydeder. Bu değer, Kullanıcı hesabı verenden farklı bir kiracıda olmadığı takdirde veren talebinin değeriyle aynıdır. | `<Attribute Name=" http://schemas.microsoft.com/identity/claims/identityprovider">`<br>`<AttributeValue>https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/<AttributeValue>` |
> |Issuedat | `iat` |Belirtecin verildiği saati depolar. Genellikle belirtecin yeniliği ölçmek için kullanılır. | `<Assertion ID="_d5ec7a9b-8d8f-4b44-8c94-9812612142be" IssueInstant="2014-01-06T20:20:23.085Z" Version="2.0" xmlns="urn:oasis:names:tc:SAML:2.0:assertion">` |
> |Sertifikayı Veren | `iss` |Belirteci oluşturan ve döndüren güvenlik belirteci hizmetini (STS) belirler. Azure AD 'nin döndürdüğü belirteçlerde veren sts.windows.net. Verenin talep değerindeki GUID, Azure AD dizininin kiracı KIMLIĞIDIR. Kiracı KIMLIĞI, dizinin sabit ve güvenilir tanıtıcısıdır. | `<Issuer>https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/</Issuer>` |
> |Soyadı | `family_name` |Kullanıcının Azure AD Kullanıcı nesnesinde tanımlanan soyadı, soyadı veya aile adını sağlar. | `<Attribute Name=" http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname">`<br>`<AttributeValue>Miller<AttributeValue>` |
> |Ad | `unique_name` |Belirtecin konusunu tanımlayan ve okunabilir bir değer sunar. Bu değerin kiracı içinde benzersiz olması garanti edilmez ve yalnızca görüntüleme amacıyla kullanılmak üzere tasarlanmıştır. | `<Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name">`<br>`<AttributeValue>frankm@contoso.com<AttributeValue>`|
> |Nesne kimliği | `oid` |Azure AD 'de bir nesnenin benzersiz tanımlayıcısını içerir. Bu değer sabittir ve yeniden atanamaz veya tekrar kullanılamaz. Azure AD 'de sorgulardaki bir nesneyi tanımlamak için nesne KIMLIĞINI kullanın. | `<Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">`<br>`<AttributeValue>528b2ac2-aa9c-45e1-88d4-959b53bc7dd0<AttributeValue>` |
> |Roller | `roles` |Konunun hem doğrudan hem de dolaylı olarak grup üyeliği aracılığıyla verildiğini ve rol tabanlı erişim denetimini zorlamak için kullanılabilir olan tüm uygulama rollerini temsil eder. Uygulama rolleri uygulama bildiriminin `appRoles` özelliği aracılığıyla uygulama başına temelinde tanımlanır. Her uygulama rolünün özelliği, roller talebinde görüntülenen değerdir. `value` | `<Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/role">`|
> |Subject | `sub` |Uygulamanın kullanıcı gibi, belirtecin hangi ana öğe için onay verdiğini belirtir. Bu değer sabittir ve yeniden atanamaz veya yeniden kullanılamaz, bu nedenle yetkilendirme denetimlerini güvenli bir şekilde gerçekleştirmek için kullanılabilir. Konu her zaman Azure AD sorunları belirteçlerinde bulunduğundan, bu değeri genel amaçlı yetkilendirme sisteminde kullanmanızı öneririz. <br> `SubjectConfirmation`bir talep değil. Belirtecin konusunun nasıl doğrulandığını açıklar. `Bearer`konunun belirtecin elinde olduğunu teyit eden anlamına gelir. | `<Subject>`<br>`<NameID>S40rgb3XjhFTv6EQTETkEzcgVmToHKRkZUIsJlmLdVc</NameID>`<br>`<SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer" />`<br>`</Subject>`|
> |Kiracı Kimliği | `tid` |Belirteci veren dizin kiracısını tanımlayan, sabit ve yeniden kullanılabilir olmayan bir tanımlayıcı. Bu değeri, çok kiracılı bir uygulamadaki kiracıya özgü dizin kaynaklarına erişmek için kullanabilirsiniz. Örneğin, Graph API çağrısında kiracıyı tanımlamak için bu değeri kullanabilirsiniz. | `<Attribute Name="http://schemas.microsoft.com/identity/claims/tenantid">`<br>`<AttributeValue>cbb1a5ac-f33b-45fa-9bf5-f37db0fed422<AttributeValue>`|
> |Belirteç Ömrü | `nbf`, `exp` |Belirtecin geçerli olduğu zaman aralığını tanımlar. Belirteci doğrulayan hizmet geçerli tarihin belirteç ömrü içinde olduğunu doğrulamalıdır, aksi takdirde belirteci reddetmelidir. Hizmet, belirteç yaşam süresi aralığının, Azure AD ile hizmet arasında saat ("zaman sapması") arasında herhangi bir farklılık hesaba göre hesap yapmasına izin verebilir. | `<Conditions`<br>`NotBefore="2013-03-18T21:32:51.261Z"`<br>`NotOnOrAfter="2013-03-18T22:32:51.261Z"`<br>`>` <br>|

## <a name="sample-saml-token"></a>Örnek SAML belirteci

Bu, tipik bir SAML belirtecinin örneğidir.

    <?xml version="1.0" encoding="UTF-8"?>
    <t:RequestSecurityTokenResponse xmlns:t="http://schemas.xmlsoap.org/ws/2005/02/trust">
      <t:Lifetime>
        <wsu:Created xmlns:wsu="https://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-utility-1.0.xsd">2014-12-24T05:15:47.060Z</wsu:Created>
        <wsu:Expires xmlns:wsu="https://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-utility-1.0.xsd">2014-12-24T06:15:47.060Z</wsu:Expires>
      </t:Lifetime>
      <wsp:AppliesTo xmlns:wsp="http://schemas.xmlsoap.org/ws/2004/09/policy">
        <EndpointReference xmlns="https://www.w3.org/2005/08/addressing">
          <Address>https://contoso.onmicrosoft.com/MyWebApp</Address>
        </EndpointReference>
      </wsp:AppliesTo>
      <t:RequestedSecurityToken>
        <Assertion xmlns="urn:oasis:names:tc:SAML:2.0:assertion" ID="_3ef08993-846b-41de-99df-b7f3ff77671b" IssueInstant="2014-12-24T05:20:47.060Z" Version="2.0">
          <Issuer>https://sts.windows.net/b9411234-09af-49c2-b0c3-653adc1f376e/</Issuer>
          <ds:Signature xmlns:ds="https://www.w3.org/2000/09/xmldsig#">
            <ds:SignedInfo>
              <ds:CanonicalizationMethod Algorithm="https://www.w3.org/2001/10/xml-exc-c14n#" />
              <ds:SignatureMethod Algorithm="https://www.w3.org/2001/04/xmldsig-more#rsa-sha256" />
              <ds:Reference URI="#_3ef08993-846b-41de-99df-b7f3ff77671b">
                <ds:Transforms>
                  <ds:Transform Algorithm="https://www.w3.org/2000/09/xmldsig#enveloped-signature" />
                  <ds:Transform Algorithm="https://www.w3.org/2001/10/xml-exc-c14n#" />
                </ds:Transforms>
                <ds:DigestMethod Algorithm="https://www.w3.org/2001/04/xmlenc#sha256" />
                <ds:DigestValue>cV1J580U1pD24hEyGuAxrbtgROVyghCqI32UkER/nDY=</ds:DigestValue>
              </ds:Reference>
            </ds:SignedInfo>
            <ds:SignatureValue>j+zPf6mti8Rq4Kyw2NU2nnu0pbJU1z5bR/zDaKaO7FCTdmjUzAvIVfF8pspVR6CbzcYM3HOAmLhuWmBkAAk6qQUBmKsw+XlmF/pB/ivJFdgZSLrtlBs1P/WBV3t04x6fRW4FcIDzh8KhctzJZfS5wGCfYw95er7WJxJi0nU41d7j5HRDidBoXgP755jQu2ZER7wOYZr6ff+ha+/Aj3UMw+8ZtC+WCJC3yyENHDAnp2RfgdElJal68enn668fk8pBDjKDGzbNBO6qBgFPaBT65YvE/tkEmrUxdWkmUKv3y7JWzUYNMD9oUlut93UTyTAIGOs5fvP9ZfK2vNeMVJW7Xg==</ds:SignatureValue>
            <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
              <X509Data>
                <X509Certificate>MIIDPjCCAabcAwIBAgIQsRiM0jheFZhKk49YD0SK1TAJBgUrDgMCHQUAMC0xKzApBgNVBAMTImFjY291bnRzLmFjY2Vzc2NvbnRyb2wud2luZG93cy5uZXQwHhcNMTQwMTAxMDcwMDAwWhcNMTYwMTAxMDcwMDAwWjAtMSswKQYDVQQDEyJhY2NvdW50cy5hY2Nlc3Njb250cm9sLndpbmRvd3MubmV0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAkSCWg6q9iYxvJE2NIhSyOiKvqoWCO2GFipgH0sTSAs5FalHQosk9ZNTztX0ywS/AHsBeQPqYygfYVJL6/EgzVuwRk5txr9e3n1uml94fLyq/AXbwo9yAduf4dCHTP8CWR1dnDR+Qnz/4PYlWVEuuHHONOw/blbfdMjhY+C/BYM2E3pRxbohBb3x//CfueV7ddz2LYiH3wjz0QS/7kjPiNCsXcNyKQEOTkbHFi3mu0u13SQwNddhcynd/GTgWN8A+6SN1r4hzpjFKFLbZnBt77ACSiYx+IHK4Mp+NaVEi5wQtSsjQtI++XsokxRDqYLwus1I1SihgbV/STTg5enufuwIDAQABo2IwYDBeBgNVHQEEVzBVgBDLebM6bK3BjWGqIBrBNFeNoS8wLTErMCkGA1UEAxMiYWNjb3VudHMuYWNjZXNzY29udHJvbC53aW5kb3dzLm5ldIIQsRiM0jheFZhKk49YD0SK1TAJBgUrDgMCHQUAA4IBAQCJ4JApryF77EKC4zF5bUaBLQHQ1PNtA1uMDbdNVGKCmSp8M65b8h0NwlIjGGGy/unK8P6jWFdm5IlZ0YPTOgzcRZguXDPj7ajyvlVEQ2K2ICvTYiRQqrOhEhZMSSZsTKXFVwNfW6ADDkN3bvVOVbtpty+nBY5UqnI7xbcoHLZ4wYD251uj5+lo13YLnsVrmQ16NCBYq2nQFNPuNJw6t3XUbwBHXpF46aLT1/eGf/7Xx6iy8yPJX4DyrpFTutDz882RWofGEO5t4Cw+zZg70dJ/hH/ODYRMorfXEW+8uKmXMKmX2wyxMKvfiPbTy5LmAU8Jvjs2tLg4rOBcXWLAIarZ</X509Certificate>
              </X509Data>
            </KeyInfo>
          </ds:Signature>
          <Subject>
            <NameID Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent">m_H3naDei2LNxUmEcWd0BZlNi_jVET1pMLR6iQSuYmo</NameID>
            <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer" />
          </Subject>
          <Conditions NotBefore="2014-12-24T05:15:47.060Z" NotOnOrAfter="2014-12-24T06:15:47.060Z">
            <AudienceRestriction>
              <Audience>https://contoso.onmicrosoft.com/MyWebApp</Audience>
            </AudienceRestriction>
          </Conditions>
          <AttributeStatement>
            <Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">
              <AttributeValue>a1addde8-e4f9-4571-ad93-3059e3750d23</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.microsoft.com/identity/claims/tenantid">
              <AttributeValue>b9411234-09af-49c2-b0c3-653adc1f376e</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name">
              <AttributeValue>sample.admin@contoso.onmicrosoft.com</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname">
              <AttributeValue>Admin</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname">
              <AttributeValue>Sample</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.microsoft.com/ws/2008/06/identity/claims/groups">
              <AttributeValue>5581e43f-6096-41d4-8ffa-04e560bab39d</AttributeValue>
              <AttributeValue>07dd8a89-bf6d-4e81-8844-230b77145381</AttributeValue>
              <AttributeValue>0e129f4g-6b0a-4944-982d-f776000632af</AttributeValue>
              <AttributeValue>3ee07328-52ef-4739-a89b-109708c22fb5</AttributeValue>
              <AttributeValue>329k14b3-1851-4b94-947f-9a4dacb595f4</AttributeValue>
              <AttributeValue>6e32c650-9b0a-4491-b429-6c60d2ca9a42</AttributeValue>
              <AttributeValue>f3a169a7-9a58-4e8f-9d47-b70029v07424</AttributeValue>
              <AttributeValue>8e2c86b2-b1ad-476d-9574-544d155aa6ff</AttributeValue>
              <AttributeValue>1bf80264-ff24-4866-b22c-6212e5b9a847</AttributeValue>
              <AttributeValue>4075f9c3-072d-4c32-b542-03e6bc678f3e</AttributeValue>
              <AttributeValue>76f80527-f2cd-46f4-8c52-8jvd8bc749b1</AttributeValue>
              <AttributeValue>0ba31460-44d0-42b5-b90c-47b3fcc48e35</AttributeValue>
              <AttributeValue>edd41703-8652-4948-94a7-2d917bba7667</AttributeValue>
            </Attribute>
            <Attribute Name="http://schemas.microsoft.com/identity/claims/identityprovider">
              <AttributeValue>https://sts.windows.net/b9411234-09af-49c2-b0c3-653adc1f376e/</AttributeValue>
            </Attribute>
          </AttributeStatement>
          <AuthnStatement AuthnInstant="2014-12-23T18:51:11.000Z">
            <AuthnContext>
              <AuthnContextClassRef>urn:oasis:names:tc:SAML:2.0:ac:classes:Password</AuthnContextClassRef>
            </AuthnContext>
          </AuthnStatement>
        </Assertion>
      </t:RequestedSecurityToken>
      <t:RequestedAttachedReference>
        <SecurityTokenReference xmlns="https://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-secext-1.0.xsd" xmlns:d3p1="https://docs.oasis-open.org/wss/oasis-wss-wssecurity-secext-1.1.xsd" d3p1:TokenType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0">
          <KeyIdentifier ValueType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLID">_3ef08993-846b-41de-99df-b7f3ff77671b</KeyIdentifier>
        </SecurityTokenReference>
      </t:RequestedAttachedReference>
      <t:RequestedUnattachedReference>
        <SecurityTokenReference xmlns="https://docs.oasis-open.org/wss/2004/01/oasis-200401-wss-wssecurity-secext-1.0.xsd" xmlns:d3p1="https://docs.oasis-open.org/wss/oasis-wss-wssecurity-secext-1.1.xsd" d3p1:TokenType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0">
          <KeyIdentifier ValueType="http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLID">_3ef08993-846b-41de-99df-b7f3ff77671b</KeyIdentifier>
        </SecurityTokenReference>
      </t:RequestedUnattachedReference>
      <t:TokenType>http://docs.oasis-open.org/wss/oasis-wss-saml-token-profile-1.1#SAMLV2.0</t:TokenType>
      <t:RequestType>http://schemas.xmlsoap.org/ws/2005/02/trust/Issue</t:RequestType>
      <t:KeyType>http://schemas.xmlsoap.org/ws/2005/05/identity/NoProofKey</t:KeyType>
    </t:RequestSecurityTokenResponse>

## <a name="related-content"></a>İlgili içerik
* Belirteç yaşam süresi ilkesini Azure AD Graph API aracılığıyla yönetme hakkında daha fazla bilgi edinmek için bkz. Azure AD Graph [ilkesi işlemleri](https://msdn.microsoft.com/library/azure/ad/graph/api/policy-operations) ve [ilke varlığı](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#policy-entity).
* Örnekler de dahil olmak üzere PowerShell cmdlet 'leri aracılığıyla ilkeleri yönetme hakkında daha fazla bilgi ve örnek için bkz. [Azure AD 'de yapılandırılabilir belirteç ömürleri](active-directory-configurable-token-lifetimes.md). 
* Uygulamanıza yönelik belirteçlere [özel ve isteğe bağlı talepler](active-directory-optional-claims.md) ekleyin.
* [SAML Ile çoklu oturum açma (SSO)](single-sign-on-saml-protocol.md)kullanın.
* [Azure çoklu oturum kapatma SAML protokolünü](single-sign-out-saml-protocol.md) kullanma
