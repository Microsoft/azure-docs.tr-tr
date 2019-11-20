---
title: Azure AD Federasyon meta verileri | Microsoft Docs
description: Bu makalede, Azure Active Directory belirteçleri kabul eden hizmetler için Azure Active Directory yayımladığı Federasyon meta verileri belgesi açıklanmaktadır.
services: active-directory
documentationcenter: .net
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: c2d5f80b-aa74-452c-955b-d8eb3ed62652
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/07/2017
ms.author: ryanwi
ms.reviewer: hirsin, dastrock
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: fefaf618ff29cc2186dc555eb6f452223f4cd097
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/06/2019
ms.locfileid: "68835138"
---
# <a name="federation-metadata"></a>Federasyon meta verileri
Azure Active Directory (Azure AD), Azure AD sorunlarının güvenlik belirteçlerini kabul edecek şekilde yapılandırılmış hizmetler için bir Federasyon meta veri belgesi yayımlar. Federasyon meta veri belgesi biçimi, [OASSıS Security assertion Markup Language (SAML) v 2.0 Için meta verileri](https://docs.oasis-open.org/security/saml/v2.0/saml-metadata-2.0-os.pdf)genişleten [Web HIZMETLERI Federasyonu Language (WS-federation) sürüm 1,2](https://docs.oasis-open.org/wsfed/federation/v1.2/os/ws-federation-1.2-spec-os.html)' de açıklanmaktadır.

## <a name="tenant-specific-and-tenant-independent-metadata-endpoints"></a>Kiracıya özgü ve kiracıdan bağımsız meta veri uç noktaları
Azure AD, kiracıya özgü ve kiracıdan bağımsız uç noktaları yayımlar.

Kiracıya özgü uç noktalar belirli bir kiracı için tasarlanmıştır. Kiracıya özgü Federasyon meta verileri, kiracıya özgü veren ve uç nokta bilgileri de dahil olmak üzere kiracı hakkında bilgiler içerir. Tek bir kiracıya erişimi kısıtlayan uygulamalar kiracıya özgü uç noktalar kullanır.

Kiracıdan bağımsız uç noktalar, tüm Azure AD kiracılarında ortak olan bilgileri sağlar. Bu bilgiler, *login.microsoftonline.com* adresinde barındırılan kiracılar için geçerlidir ve kiracılar arasında paylaşılır. Kiracıdan bağımsız uç noktalar, çok kiracılı uygulamalar için önerilir, çünkü belirli bir kiracı ile ilişkilendirilmez.

## <a name="federation-metadata-endpoints"></a>Federasyon meta verileri uç noktaları
Azure AD, Federasyon meta verilerini `https://login.microsoftonline.com/<TenantDomainName>/FederationMetadata/2007-06/FederationMetadata.xml`konumunda yayımlar.

**Kiracıya özgü uç noktalar** `TenantDomainName` için aşağıdaki türlerden biri olabilir:

* Azure AD kiracısının kayıtlı bir etki alanı adı, örneğin: `contoso.onmicrosoft.com`.
* Etki alanının `72f988bf-86f1-41af-91ab-2d7cd011db45`sabit Kiracı kimliği (gibi).

**Kiracıdan bağımsız uç noktalar** `TenantDomainName` `common`için,. Bu belgede yalnızca login.microsoftonline.com adresinde barındırılan tüm Azure AD kiracılarında ortak olan Federasyon meta veri öğeleri listelenir.

Örneğin, kiracıya özgü bir uç nokta `https://login.microsoftonline.com/contoso.onmicrosoft.com/FederationMetadata/2007-06/FederationMetadata.xml`olabilir. Kiracıdan bağımsız uç nokta [https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml](https://login.microsoftonline.com/common/FederationMetadata/2007-06/FederationMetadata.xml). Bu URL 'YI bir tarayıcıya yazarak, Federasyon meta veri belgesini görüntüleyebilirsiniz.

## <a name="contents-of-federation-metadata"></a>Federasyon meta verilerinin içeriği
Aşağıdaki bölümde, Azure AD tarafından verilen belirteçleri kullanan hizmetler için gereken bilgiler sağlanmaktadır.

### <a name="entity-id"></a>Varlık Kimliği
`EntityDescriptor` Öğesi bir`EntityID` özniteliği içerir. `EntityID` Özniteliğin değeri veren, diğer bir deyişle, belirteci veren güvenlik belirteci hizmeti (STS) öğesini temsil eder. Bir belirteç aldığınızda veren 'nin doğrulanması önemlidir.

Aşağıdaki meta veriler, bir `EntityDescriptor` `EntityID` öğeye sahip örnek bir kiracıya özgü öğe gösterir.

```
<EntityDescriptor
xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
ID="_b827a749-cfcb-46b3-ab8b-9f6d14a1294b"
entityID="https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db45/">
```
Kiracıya özgü `EntityID` bir değer oluşturmak için kiracının bağımsız uç noktasındaki Kiracı kimliğini kiracı Kimliğiniz ile değiştirebilirsiniz. Sonuç değeri, belirteç verenle aynı olacaktır. Strateji, çok kiracılı bir uygulamanın belirli bir kiracı için sertifikayı doğrulamasını sağlar.

Aşağıdaki meta veriler, kiracıdan bağımsız `EntityID` örnek bir öğe gösterir. Lütfen bir yer tutucu değil `{tenant}` değişmez değer olduğunu unutmayın.

```
<EntityDescriptor
xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
ID="="_0e5bd9d0-49ef-4258-bc15-21ce143b61bd"
entityID="https://sts.windows.net/{tenant}/">
```

### <a name="token-signing-certificates"></a>Belirteç imzalama sertifikaları
Hizmet bir Azure AD kiracısı tarafından verilen bir belirteç aldığında, belirtecin imzası, Federasyon meta veri belgesinde yayınlanan bir imzalama anahtarıyla doğrulanıp doğrulanması gerekir. Federasyon meta verileri, kiracılar tarafından belirteç imzalama için kullanılan sertifikaların genel bölümünü içerir. Sertifika ham baytları `KeyDescriptor` öğesinde görüntülenir. Belirteç imzalama sertifikası yalnızca `use` `signing`özniteliğin değeri olduğunda imzalama için geçerlidir.

Azure AD tarafından yayımlanan bir Federasyon meta veri belgesi, imzalama sertifikasını güncelleştirmek için Azure AD hazırlanırken olduğu gibi birden çok imzalama anahtarına sahip olabilir. Federasyon meta veri belgesi birden fazla sertifika içerdiğinde, belirteçleri doğrulayan bir hizmet belgedeki tüm sertifikaları desteklemelidir.

Aşağıdaki meta veriler, imzalama anahtarı `KeyDescriptor` ile örnek bir öğe gösterir.

```
<KeyDescriptor use="signing">
<KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
<X509Data>
<X509Certificate>
MIIDPjCCAiqgAwIBAgIQVWmXY/+9RqFA/OG9kFulHDAJBgUrDgMCHQUAMC0xKzApBgNVBAMTImFjY291bnRzLmFjY2Vzc2NvbnRyb2wud2luZG93cy5uZXQwHhcNMTIwNjA3MDcwMDAwWhcNMTQwNjA3MDcwMDAwWjAtMSswKQYDVQQDEyJhY2NvdW50cy5hY2Nlc3Njb250cm9sLndpbmRvd3MubmV0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEArCz8Sn3GGXmikH2MdTeGY1D711EORX/lVXpr+ecGgqfUWF8MPB07XkYuJ54DAuYT318+2XrzMjOtqkT94VkXmxv6dFGhG8YZ8vNMPd4tdj9c0lpvWQdqXtL1TlFRpD/P6UMEigfN0c9oWDg9U7Ilymgei0UXtf1gtcQbc5sSQU0S4vr9YJp2gLFIGK11Iqg4XSGdcI0QWLLkkC6cBukhVnd6BCYbLjTYy3fNs4DzNdemJlxGl8sLexFytBF6YApvSdus3nFXaMCtBGx16HzkK9ne3lobAwL2o79bP4imEGqg+ibvyNmbrwFGnQrBc1jTF9LyQX9q+louxVfHs6ZiVwIDAQABo2IwYDBeBgNVHQEEVzBVgBCxDDsLd8xkfOLKm4Q/SzjtoS8wLTErMCkGA1UEAxMiYWNjb3VudHMuYWNjZXNzY29udHJvbC53aW5kb3dzLm5ldIIQVWmXY/+9RqFA/OG9kFulHDAJBgUrDgMCHQUAA4IBAQAkJtxxm/ErgySlNk69+1odTMP8Oy6L0H17z7XGG3w4TqvTUSWaxD4hSFJ0e7mHLQLQD7oV/erACXwSZn2pMoZ89MBDjOMQA+e6QzGB7jmSzPTNmQgMLA8fWCfqPrz6zgH+1F1gNp8hJY57kfeVPBiyjuBmlTEBsBlzolY9dd/55qqfQk6cgSeCbHCy/RU/iep0+UsRMlSgPNNmqhj5gmN2AFVCN96zF694LwuPae5CeR2ZcVknexOWHYjFM0MgUSw0ubnGl0h9AJgGyhvNGcjQqu9vd1xkupFgaN+f7P3p3EVN5csBg5H94jEcQZT7EKeTiZ6bTrpDAnrr8tDCy8ng
</X509Certificate>
</X509Data>
</KeyInfo>
</KeyDescriptor>
  ```

`KeyDescriptor` Öğesi, Federasyon meta veri belgesinde iki yerde, WS-Federation 'e özgü bölümünde ve SAML 'ye özgü bölümünde görüntülenir. Her iki bölümde de yayımlanan sertifikalar aynı olacaktır.

WS-Federation 'a özgü bölümünde, bir WS-Federasyon meta veri okuyucusu, `RoleDescriptor` `SecurityTokenServiceType` türü olan bir öğeden sertifikaları okur.

Aşağıdaki meta veriler örnek `RoleDescriptor` bir öğe gösterir.

```
<RoleDescriptor xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns:fed="https://docs.oasis-open.org/wsfed/federation/200706" xsi:type="fed:SecurityTokenServiceType"protocolSupportEnumeration="https://docs.oasis-open.org/wsfed/federation/200706">
```

SAML 'ye özgü bölümünde, bir WS-Federasyon meta veri okuyucusu bir `IDPSSODescriptor` öğeden sertifikaları okur.

Aşağıdaki meta veriler örnek `IDPSSODescriptor` bir öğe gösterir.

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
```
Kiracıya özgü ve kiracıdan bağımsız sertifikaların biçiminde farklılık yoktur.

### <a name="ws-federation-endpoint-url"></a>WS-Federation Endpoint URL 'SI
Federasyon meta verileri, Azure AD 'nin, WS-Federation protokolünde çoklu oturum açma ve çoklu oturum kapatma için kullandığı URL 'YI içerir. Bu uç nokta `PassiveRequestorEndpoint` öğesinde görüntülenir.

Aşağıdaki meta veriler, kiracıya `PassiveRequestorEndpoint` özgü uç nokta için örnek bir öğe gösterir.

```
<fed:PassiveRequestorEndpoint>
<EndpointReference xmlns="https://www.w3.org/2005/08/addressing">
<Address>
https://login.microsoftonline.com/72f988bf-86f1-41af-91ab-2d7cd011db45/wsfed
</Address>
</EndpointReference>
</fed:PassiveRequestorEndpoint>
```
Kiracıdan bağımsız uç nokta için, aşağıdaki örnekte gösterildiği gibi WS-Federation URL 'SI WS-Federation uç noktasında görüntülenir.

```
<fed:PassiveRequestorEndpoint>
<EndpointReference xmlns="https://www.w3.org/2005/08/addressing">
<Address>
https://login.microsoftonline.com/common/wsfed
</Address>
</EndpointReference>
</fed:PassiveRequestorEndpoint>
```

### <a name="saml-protocol-endpoint-url"></a>SAML protokol uç noktası URL 'SI
Federasyon meta verileri, Azure AD 'nin SAML 2,0 protokolünde çoklu oturum açma ve çoklu oturum kapatma için kullandığı URL 'YI içerir. Bu uç noktalar `IDPSSODescriptor` öğesinde görüntülenir.

Oturum açma ve oturum kapatma URL 'leri `SingleSignOnService` ve `SingleLogoutService` öğelerinde görüntülenir.

Aşağıdaki meta veriler, kiracıya `PassiveResistorEndpoint` özgü uç nokta için bir örnek gösterir.

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
…
    <SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/contoso.onmicrosoft.com/saml2" />
    <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/contoso.onmicrosoft.com /saml2" />
  </IDPSSODescriptor>
```

Benzer şekilde, ortak SAML 2,0 protokol uç noktaları, aşağıdaki örnekte gösterildiği gibi kiracıya bağımsız Federasyon meta verilerinde yayımlanır.

```
<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol">
…
    <SingleLogoutService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/common/saml2" />
    <SingleSignOnService Binding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect" Location="https://login.microsoftonline.com/common/saml2" />
  </IDPSSODescriptor>
```
