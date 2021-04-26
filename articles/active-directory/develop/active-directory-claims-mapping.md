---
title: Azure AD kiracı uygulaması taleplerini (PowerShell) özelleştirme
titleSuffix: Microsoft identity platform
description: Bu sayfada Azure Active Directory talep eşleştirmesi açıklanmaktadır.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: how-to
ms.date: 08/25/2020
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin, jeedes, luleon
ms.openlocfilehash: e77155f8a6efd3916ae90fcb562d688bb5b5126f
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/18/2021
ms.locfileid: "107598899"
---
# <a name="how-to-customize-claims-emitted-in-tokens-for-a-specific-app-in-a-tenant-preview"></a>Nasıl yapılır: bir Kiracıdaki belirli bir uygulama için belirteçlerde yayılan talepleri özelleştirme (Önizleme)

> [!NOTE]
> Bu özellik, bugün Portal üzerinden sunulan [talep özelleştirmelerinin](active-directory-saml-claims-customization.md) yerini alır ve yerini alır. Aynı uygulamada, bu belgede ayrıntılı grafik/PowerShell yöntemine ek olarak portalı kullanarak talepleri özelleştirirseniz, bu uygulama için verilen belirteçler portalda yapılandırmayı yoksayar. Bu belgede açıklanan yöntemler aracılığıyla yapılan yapılandırmaların portala yansıtılmayacak.

> [!NOTE]
> Bu özellik şu anda genel önizlemededir. Değişiklikleri geri almaya veya kaldırmaya hazırlıklı olun. Bu özellik, genel önizleme sırasında herhangi bir Azure Active Directory (Azure AD) aboneliğinde kullanılabilir. Ancak özellik genel kullanıma sunulduğunda, özelliğin bazı yönleri bir Azure AD Premium aboneliği gerektirebilir. Bu özellik, WS-beslenir, SAML, OAuth ve OpenID Connect protokolleri için talep eşleme ilkelerinin yapılandırılmasını destekler.

Bu özellik kiracı yöneticileri tarafından kiracısındaki belirli bir uygulama için belirteçlerde bulunan talepleri özelleştirmek üzere kullanılır. Talep eşleme ilkelerini kullanarak şunları yapabilirsiniz:

- Belirteçlere hangi taleplerin ekleneceğini seçin.
- Zaten mevcut olmayan talep türleri oluşturun.
- Belirli taleplerde yayılan verilerin kaynağını seçin veya değiştirin.

Bu makalede, [talep eşleme ilkesi türünü](reference-claims-mapping-policy-type.md)nasıl kullanacağınızı belirlemenize yardımcı olabilecek birkaç yaygın senaryo gösterilmektedir.

Bir talep eşleme ilkesi oluştururken, belirteçlerdeki Dizin şeması uzantısı özniteliğinden bir talep da oluşturabilirsiniz. Öğesinde *ID* yerine Extension özniteliği Için *extensionID* kullanın `ClaimsSchema` .  Uzantı öznitelikleri hakkında daha fazla bilgi için bkz. [Dizin şeması uzantısı özniteliklerini kullanma](active-directory-schema-extensions.md).

## <a name="prerequisites"></a>Önkoşullar

Aşağıdaki örneklerde, hizmet sorumluları için ilkeleri oluşturur, güncelleştirir, bağlar ve silebilirsiniz. Talep eşleme ilkeleri, yalnızca hizmet sorumlusu nesnelerine atanabilir. Azure AD 'de yeni başladıysanız, bu örneklere geçmeden önce [bir Azure AD kiracısı alma hakkında bilgi](quickstart-create-new-tenant.md) almanızı öneririz.

Başlamak için aşağıdaki adımları uygulayın:

1. En son [Azure AD PowerShell modülü genel önizleme sürümünü](https://www.powershellgallery.com/packages/AzureADPreview)indirin.
1. Azure AD yönetici hesabınızda oturum açmak için Connect komutunu çalıştırın. Her yeni oturumu başlattığınızda bu komutu çalıştırın.

   ``` powershell
   Connect-AzureAD -Confirm
   ```
1. Kuruluşunuzda oluşturulan tüm ilkeleri görmek için aşağıdaki komutu çalıştırın. İlkelerinizin beklenen şekilde oluşturulduğunu denetlemek için, aşağıdaki senaryolarda işlemlerden en çok işlem sonrasında bu komutu çalıştırmanızı öneririz.

   ``` powershell
   Get-AzureADPolicy
   ```

## <a name="omit-the-basic-claims-from-tokens"></a>Belirteçlerden temel talepler yok

Bu örnekte, bağlı hizmet sorumlularına verilen belirteçlerden [temel talep kümesini](reference-claims-mapping-policy-type.md#claim-sets) kaldıran bir ilke oluşturursunuz.

1. Talep eşleme ilkesi oluşturun. Belirli hizmet sorumlularına bağlı olan bu ilke, temel talep kümesini belirteçlerden kaldırır.
   1. İlkeyi oluşturmak için şu komutu çalıştırın:

      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"false"}}') -DisplayName "OmitBasicClaims" -Type "ClaimsMappingPolicy"
      ```
   2. Yeni ilkenize bakmak ve ilke ObjectID 'yi almak için aşağıdaki komutu çalıştırın:

      ``` powershell
      Get-AzureADPolicy
      ```
1. İlkeyi hizmet sorumlusuna atayın. Ayrıca hizmet sorumlunun ObjectID 'sini almanız gerekir.
   1. Tüm kuruluşunuzun hizmet sorumlularını görmek için [MICROSOFT Graph API 'sini sorgulayabilirsiniz](/graph/traverse-the-graph). Veya [Microsoft Graph Gezgini](https://developer.microsoft.com/graph/graph-explorer)' nde Azure AD hesabınızda oturum açın.
   2. Hizmet sorumlunuz ObjectID 'niz varsa, aşağıdaki komutu çalıştırın:

      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

## <a name="include-the-employeeid-and-tenantcountry-as-claims-in-tokens"></a>ÇalışanNo ve TenantCountry 'yi belirteçlere talep olarak ekleyin

Bu örnekte, EmployeeID ve TenantCountry ' ı bağlı hizmet sorumlularına verilen belirteçlere ekleyen bir ilke oluşturacaksınız. ÇalışanNo, hem SAML belirteçlerinde hem de JWTs 'de ad talep türü olarak yayınlanır. TenantCountry, hem SAML belirteçlerinde hem de JWTs 'de ülke/bölge talep türü olarak yayınlanır. Bu örnekte, belirteçlere temel talepler kümesini eklemeye devam ediyoruz.

1. Talep eşleme ilkesi oluşturun. Bu ilke, belirli hizmet sorumlularıyla bağlantılı olarak, ÇalışanNo ve TenantCountry taleplerini belirteçlere ekler.
   1. İlkeyi oluşturmak için aşağıdaki komutu çalıştırın:

      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema": [{"Source":"user","ID":"employeeid","SamlClaimType":"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/employeeid","JwtClaimType":"name"},{"Source":"company","ID":"tenantcountry","SamlClaimType":"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/country","JwtClaimType":"country"}]}}') -DisplayName "ExtraClaimsExample" -Type "ClaimsMappingPolicy"
      ```

   2. Yeni ilkenize bakmak ve ilke ObjectID 'yi almak için aşağıdaki komutu çalıştırın:

      ``` powershell
      Get-AzureADPolicy
      ```
1. İlkeyi hizmet sorumlusuna atayın. Ayrıca hizmet sorumlunun ObjectID 'sini almanız gerekir.
   1. Tüm kuruluşunuzun hizmet sorumlularını görmek için [MICROSOFT Graph API 'sini sorgulayabilirsiniz](/graph/traverse-the-graph). Veya [Microsoft Graph Gezgini](https://developer.microsoft.com/graph/graph-explorer)' nde Azure AD hesabınızda oturum açın.
   2. Hizmet sorumlunuz ObjectID 'niz varsa, aşağıdaki komutu çalıştırın:

      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

## <a name="use-a-claims-transformation-in-tokens"></a>Belirteçlerde talep dönüşümü kullanma

Bu örnekte, bağlantılı hizmet sorumlularına verilen JWTs 'e "JoinedData" özel talebi yayan bir ilke oluşturacaksınız. Bu talep, kullanıcı nesnesindeki extensionAttribute1 özniteliğinde depolanan verileri ". Sandbox" ile birleştirerek oluşturulmuş bir değer içerir. Bu örnekte, belirteçlerde ayarlanan temel talepler hariç tutuyoruz.

1. Talep eşleme ilkesi oluşturun. Bu ilke, belirli hizmet sorumlularıyla bağlantılı olarak, ÇalışanNo ve TenantCountry taleplerini belirteçlere ekler.
   1. İlkeyi oluşturmak için aşağıdaki komutu çalıştırın:

      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema":[{"Source":"user","ID":"extensionattribute1"},{"Source":"transformation","ID":"DataJoin","TransformationId":"JoinTheData","JwtClaimType":"JoinedData"}],"ClaimsTransformations":[{"ID":"JoinTheData","TransformationMethod":"Join","InputClaims":[{"ClaimTypeReferenceId":"extensionattribute1","TransformationClaimType":"string1"}], "InputParameters": [{"ID":"string2","Value":"sandbox"},{"ID":"separator","Value":"."}],"OutputClaims":[{"ClaimTypeReferenceId":"DataJoin","TransformationClaimType":"outputClaim"}]}]}}') -DisplayName "TransformClaimsExample" -Type "ClaimsMappingPolicy"
      ```

   2. Yeni ilkenize bakmak ve ilke ObjectID 'yi almak için aşağıdaki komutu çalıştırın:

      ``` powershell
      Get-AzureADPolicy
      ```
1. İlkeyi hizmet sorumlusuna atayın. Ayrıca hizmet sorumlunun ObjectID 'sini almanız gerekir.
   1. Tüm kuruluşunuzun hizmet sorumlularını görmek için [MICROSOFT Graph API 'sini sorgulayabilirsiniz](/graph/traverse-the-graph). Veya [Microsoft Graph Gezgini](https://developer.microsoft.com/graph/graph-explorer)' nde Azure AD hesabınızda oturum açın.
   2. Hizmet sorumlunuz ObjectID 'niz varsa, aşağıdaki komutu çalıştırın:

      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

## <a name="security-considerations"></a>Güvenlik konuları

Belirteçleri alan uygulamalar, talep değerlerinin Azure AD tarafından yetkili olarak verildiği ve üzerinde oynanamaz olgusuna bağımlıdır. Ancak, talepler eşleme ilkeleri aracılığıyla belirteç içeriğini değiştirdiğinizde bu varsayımlar artık doğru olmayabilir. Uygulamalar, kötü amaçlı aktörler tarafından oluşturulan talep eşleme ilkelerinden kendilerini korumak için, belirteçlerin talep eşleme ilkesi Oluşturucusu tarafından değiştirildiğini açıkça kabul etmelidir. Bu, aşağıdaki yollarla yapılabilir:

- Özel bir imzalama anahtarı yapılandırma
- Eşlenen talepleri kabul etmek için uygulama bildirimini güncelleştirin.
 
Bu olmadan, Azure AD bir [ `AADSTS50146` hata kodu](reference-aadsts-error-codes.md#aadsts-error-codes)döndürür.

### <a name="custom-signing-key"></a>Özel imzalama anahtarı

Hizmet sorumlusu nesnesine özel bir imzalama anahtarı eklemek için, [`New-AzureADApplicationKeyCredential`](/powerShell/module/Azuread/New-AzureADApplicationKeyCredential) uygulama nesneniz için bir sertifika anahtarı kimlik bilgisi oluşturmak üzere Azure PowerShell cmdlet 'ini kullanabilirsiniz.

Talep eşlemesi etkin olan uygulamalar `appid={client_id}` , kendi [OpenID Connect meta veri isteklerine](v2-protocols-oidc.md#fetch-the-openid-connect-metadata-document)ekleyerek belirteç imzalama anahtarlarını doğrulamalıdır. Aşağıda, kullanmanız gereken OpenID Connect meta veri belgesinin biçimi verilmiştir:

```
https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration?appid={client-id}
```

### <a name="update-the-application-manifest"></a>Uygulama bildirimini güncelleştirme

Alternatif olarak, `acceptMappedClaims` özelliğini `true` [uygulama bildiriminde](reference-app-manifest.md)olarak ayarlayabilirsiniz. Bu, [Apiapplication kaynak türünde](/graph/api/resources/apiapplication#properties)belgelendiği gibi, bir uygulamanın özel bir imzalama anahtarı belirtmeden talep eşlemeyi kullanmasına izin verir.

Bu, istenen belirteç kitlelerinin Azure AD kiracınızın doğrulanmış bir etki alanı adını kullanmasını gerektirir. Bu, `Application ID URI` Örneğin (uygulama bildiriminde ile temsil edilen) (örneğin, `identifierUris` `https://contoso.com/my-api` varsayılan kiracı adını kullanarak) olarak ayarlamanız gerektiği anlamına gelir `https://contoso.onmicrosoft.com/my-api` .

Doğrulanmış bir etki alanı kullanmıyorsanız, Azure AD `AADSTS501461` "Acceptmappedtalepler" iletisi ile bir hata kodu döndürür *. "Acceptmappedtalepler yalnızca uygulama GUID 'si ile eşleşen bir belirteç izleyicisinin veya kiracının doğrulanan etki alanlarında bulunan bir hedef kitle için desteklenir. Kaynak tanımlayıcıyı değiştirin ya da uygulamaya özgü bir imzalama anahtarı kullanın. "*

## <a name="next-steps"></a>Sonraki adımlar

- Daha fazla bilgi edinmek için [talep eşleme ilkesi türü](reference-claims-mapping-policy-type.md) başvuru makalesini okuyun.
- SAML belirtecinde verilen talepleri Azure portal aracılığıyla özelleştirmeyi öğrenmek için bkz. [nasıl yapılır: kurumsal uygulamalar IÇIN SAML belirtecinde verilen talepleri özelleştirme](active-directory-saml-claims-customization.md)
- Uzantı öznitelikleri hakkında daha fazla bilgi edinmek için bkz. [taleplerde Dizin şeması uzantısı özniteliklerini kullanma](active-directory-schema-extensions.md).
