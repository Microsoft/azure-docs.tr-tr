---
title: Azure API Management kimlik doğrulama ilkeleri | Microsoft Docs
description: Azure API Management 'de kullanıma sunulan kimlik doğrulama ilkeleri hakkında bilgi edinin.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 061702a7-3a78-472b-a54a-f3b1e332490d
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/27/2017
ms.author: apimpm
ms.openlocfilehash: 572d8c4b59622156e8b3aca4565bbc206367f6d4
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72514844"
---
# <a name="api-management-authentication-policies"></a>API Management kimlik doğrulama ilkeleri
Bu konu, aşağıdaki API Management ilkelerine yönelik bir başvuru sağlar. İlke ekleme ve yapılandırma hakkında daha fazla bilgi için bkz. [API Management ilkeleri](https://go.microsoft.com/fwlink/?LinkID=398186).

##  <a name="AuthenticationPolicies"></a>Kimlik doğrulama ilkeleri

-   Temel kimlik doğrulaması kullanarak arka uç hizmeti ile temel kimlik doğrulaması [Ile kimlik](api-management-authentication-policies.md#Basic) doğrulaması yapın.

-   [İstemci sertifikası Ile kimlik doğrulama](api-management-authentication-policies.md#ClientCertificate) -istemci sertifikaları kullanarak arka uç hizmetiyle kimlik doğrulaması yapın.

-   [Yönetilen kimlik Ile kimlik](api-management-authentication-policies.md#ManagedIdentity) doğrulama-API Management hizmeti için [yönetilen kimlikle](../active-directory/managed-identities-azure-resources/overview.md) kimlik doğrulaması yapın.

##  <a name="Basic"></a>Temel ile kimlik doğrulama
 Temel kimlik doğrulaması kullanarak arka uç hizmetiyle kimlik doğrulaması yapmak için `authentication-basic` ilkesini kullanın. Bu ilke, HTTP yetkilendirme üst bilgisini ilkede belirtilen kimlik bilgilerine karşılık gelen değere etkin bir şekilde ayarlar.

### <a name="policy-statement"></a>İlke ekstresi

```xml
<authentication-basic username="username" password="password" />
```

### <a name="example"></a>Örnek

```xml
<authentication-basic username="testuser" password="testpassword" />
```

### <a name="elements"></a>Öğeler

|Adı|Açıklama|Gereklidir|
|----------|-----------------|--------------|
|kimlik doğrulama-temel|Kök öğe.|Yes|

### <a name="attributes"></a>Öznitelikler

|Adı|Açıklama|Gereklidir|Varsayılan|
|----------|-----------------|--------------|-------------|
|kullanıcı adı|Temel kimlik bilgisinin Kullanıcı adını belirtir.|Yes|Yok|
|password|Temel kimlik bilgisinin parolasını belirtir.|Yes|Yok|

### <a name="usage"></a>Kullanım
 Bu ilke, aşağıdaki ilke [bölümlerinde](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) ve [kapsamlarda](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)kullanılabilir.

-   **İlke bölümleri:** gelen

-   **İlke kapsamları:** tüm kapsamlar

##  <a name="ClientCertificate"></a>İstemci sertifikası ile kimlik doğrulama
 İstemci sertifikası kullanarak bir arka uç hizmetiyle kimlik doğrulaması yapmak için `authentication-certificate` ilkesini kullanın. Sertifikanın öncelikle [API Management](https://go.microsoft.com/fwlink/?LinkID=511599) ve parmak izi ile tanımlanması gerekir.

### <a name="policy-statement"></a>İlke ekstresi

```xml
<authentication-certificate thumbprint="thumbprint" certificate-id="resource name"/>
```

### <a name="examples"></a>Örnekler

Bu örnekte istemci sertifikası, parmak izine göre tanımlanır.
```xml
<authentication-certificate thumbprint="CA06F56B258B7A0D4F2B05470939478651151984" />
```
Bu örnekte istemci sertifikası, kaynak adı ile tanımlanır.
```xml  
<authentication-certificate certificate-id="544fe9ddf3b8f30fb490d90f" />  
```  

### <a name="elements"></a>Öğeler  
  
|Adı|Açıklama|Gereklidir|  
|----------|-----------------|--------------|  
|kimlik doğrulama-sertifika|Kök öğe.|Yes|  
  
### <a name="attributes"></a>Öznitelikler  
  
|Adı|Açıklama|Gereklidir|Varsayılan|  
|----------|-----------------|--------------|-------------|  
|#c0|İstemci sertifikası için parmak izi.|@No__t_0 ya da `certificate-id` mevcut olmalıdır.|Yok|  
|sertifika kimliği|Sertifika kaynağı adı.|@No__t_0 ya da `certificate-id` mevcut olmalıdır.|Yok|  
  
### <a name="usage"></a>Kullanım  
 Bu ilke, aşağıdaki ilke [bölümlerinde](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) ve [kapsamlarda](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)kullanılabilir.  
  
-   **İlke bölümleri:** gelen  
  
-   **İlke kapsamları:** tüm kapsamlar  

##  <a name="ManagedIdentity"></a>Yönetilen kimlikle kimlik doğrulama  
 API Management hizmetinin yönetilen kimliğini kullanarak bir arka uç hizmetiyle kimlik doğrulaması yapmak için `authentication-managed-identity` ilkesini kullanın. Bu ilke temelde, belirtilen kaynağa erişmek için Azure Active Directory bir erişim belirteci almak üzere yönetilen kimliği kullanır. Belirteç başarıyla alındıktan sonra, ilke `Bearer` düzenini kullanarak `Authorization` üstbilgisindeki belirtecin değerini ayarlar.
  
### <a name="policy-statement"></a>İlke ekstresi  
  
```xml  
<authentication-managed-identity resource="resource" output-token-variable-name="token-variable" ignore-error="true|false"/>  
```  
  
### <a name="example"></a>Örnek  
#### <a name="use-managed-identity-to-authenticate-with-a-backend-service"></a>Bir arka uç hizmetiyle kimlik doğrulamak için yönetilen kimlik kullanma
```xml  
<authentication-managed-identity resource="https://graph.windows.net"/> 
```
```xml  
<authentication-managed-identity resource="https://management.azure.com/"/> <!--Azure Resource Manager-->
```
```xml  
<authentication-managed-identity resource="https://vault.azure.net"/> <!--Azure Key Vault-->
```
```xml  
<authentication-managed-identity resource="https://servicebus.azure.net/"/> <!--Azure Service Busr-->
```
```xml  
<authentication-managed-identity resource="https://storage.azure.com/"/> <!--Azure Blob Storage-->
```
```xml  
<authentication-managed-identity resource="https://database.windows.net/"/> <!--Azure SQL-->
```
  
#### <a name="use-managed-identity-in-send-request-policy"></a>Gönderme isteği ilkesinde yönetilen kimliği kullan
```xml  
<send-request mode="new" timeout="20" ignore-error="false">
    <set-url>https://example.com/</set-url>
    <set-method>GET</set-method>
    <authentication-managed-identity resource="ResourceID"/>
</send-request>
```

### <a name="elements"></a>Öğeler  
  
|Adı|Açıklama|Gereklidir|  
|----------|-----------------|--------------|  
|kimlik doğrulama-yönetilen-kimlik |Kök öğe.|Yes|  
  
### <a name="attributes"></a>Öznitelikler  
  
|Adı|Açıklama|Gereklidir|Varsayılan|  
|----------|-----------------|--------------|-------------|  
|Kaynak|dizisinde. Azure Active Directory içindeki hedef Web API 'sinin (güvenli kaynak) uygulama KIMLIĞI URI 'SI.|Yes|Yok|  
|çıkış-belirteç-değişken-adı|dizisinde. @No__t_0 nesne türü olarak belirteç değeri alacak bağlam değişkeninin adı. |Hayır|Yok|  
|yoksayma-hata|Boolean. @No__t_0 olarak ayarlanırsa, bir erişim belirteci alınmasa bile ilke ardışık düzeni yürütülmeye devam eder.|Hayır|yanlış|  
  
### <a name="usage"></a>Kullanım  
 Bu ilke, aşağıdaki ilke [bölümlerinde](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) ve [kapsamlarda](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)kullanılabilir.  
  
-   **İlke bölümleri:** gelen  
  
-   **İlke kapsamları:** tüm kapsamlar

## <a name="next-steps"></a>Sonraki adımlar
İlkelerle çalışma hakkında daha fazla bilgi için bkz.

+ [API Management ilkeler](api-management-howto-policies.md)
+ [API dönüştürme](transform-api.md)
+ İlke deyimlerinin ve ayarlarının tam listesi için [Ilke başvurusu](api-management-policy-reference.md)
+ [İlke örnekleri](policy-samples.md)
