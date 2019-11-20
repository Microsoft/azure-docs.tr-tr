---
title: Azure API Management sayfa şablonları | Microsoft Docs
description: Azure API Management 'de bir dizi şablon kullanarak geliştirici portalı sayfalarının içeriğini özelleştirmeyi öğrenin.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: e57df269-1019-4b74-b74d-53155b809d59
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: ce56c406c884471c445b25343d5c42f9edcbe4c4
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73176547"
---
# <a name="page-templates-in-azure-api-management"></a>Azure API Management sayfa şablonları
Azure API Management, içeriğini yapılandıran bir dizi şablon kullanarak geliştirici portalı sayfalarının içeriğini özelleştirmenizi sağlar. [Dotsıvı](http://dotliquidmarkup.org/) Syntax ve seçtiğiniz düzenleyiciyi ( [Örneğin, tasarımcılar için dotlikit](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers)) ve sağlanan bir yerelleştirilmiş [dize kaynakları](api-management-template-resources.md#strings), [glif kaynakları](api-management-template-resources.md#glyphs)ve [sayfa denetimleri](api-management-page-controls.md)kümesini kullanarak, yapılandırmak için harika bir esneklik vardır Gördüğünüz sayfaların içeriği bu şablonları kullanarak sığdırar.  
  
 Bu bölümdeki şablonlar, geliştirici portalındaki oturum açma, kaydolma ve sayfa bulunamadı sayfalarının içeriğini özelleştirmenizi sağlar.  
  
-   [Oturum Aç](#SignIn)  
  
-   [Kaydolma](#SignUp)  
  
-   [Sayfa bulunamadı](#PageNotFound)  
  
> [!NOTE]
>  Örnek varsayılan şablonlar aşağıdaki belgelere dahil edilmiştir, ancak sürekli iyileştirmeler nedeniyle değişikliğe tabidir. Canlı varsayılan şablonları, istenen ayrı şablonlara giderek Geliştirici Portalında görüntüleyebilirsiniz. Şablonlarla çalışma hakkında daha fazla bilgi için bkz. [şablonları kullanarak API Management Geliştirici Portalını Özelleştirme](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/).  

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
##  <a name="SignIn"></a>Oturum Aç  
 **Oturum açma** şablonu, geliştirici portalındaki oturum açma sayfasını özelleştirmenize olanak sağlar.  
  
 ![Oturum açma sayfası](./media/api-management-page-templates/APIM-Sign-In-Page-Developer-Portal-Templates.png "APıM oturum açma sayfası geliştirici portalı şablonları")  
  
### <a name="default-template"></a>Varsayılan şablon  
  
```xml  
<h2 class="text-center">{% localized "SigninStrings|WebAuthenticationSigninTitle" %}</h2>  
{% if registrationEnabled == true %}  
<p class="text-center">{% localized "SigninStrings|WebAuthenticationNotAMember" %}</p>  
{% endif %}  
  
<div class="row center-block ap-idp-container">  
  <div class="col-md-6">  
    {% if registrationEnabled == true %}  
        <p>{% localized "SigninStrings|WebAuthenticationSigininWithPassword" %}</p>  
    <basic-SignIn></basic-SignIn>  
    {% endif %}  
  </div>  
  
    {% if registrationEnabled != true and providers.size == 0 %}  
        {% localized "ProviderInfoStrings|TextboxExternalIdentitiesDisabled" %}  
  {% else %}  
        {% if providers.size > 0 %}  
      <div class="col-md-6">  
            <div class="providers-list">  
                <p class="text-left">  
                {% if registrationEnabled == true %}  
                    {% localized "ProviderInfoStrings|TextboxExternalIdentitiesSigninInvitation" %}  
                {% else %}  
                    {% localized "ProviderInfoStrings|TextboxExternalIdentitiesSigninInvitationPrimary" %}  
                {% endif %}  
                </p>  
        <providers></providers>  
            </div>  
    </div>  
        {% endif %}  
    {% endif %}  
  
  {% if userRegistrationTermsEnabled == true %}  
    <div class="col-md-6">  
        <div id="terms" class="modal" role="dialog" tabindex="-1">  
            <div class="modal-dialog">  
                <div class="modal-content">  
                    <div class="modal-header">  
                        <h4 class="modal-title">{% localized "SigninResources|DialogHeadingTermsOfUse" %}</h4>  
                    </div>  
                    <div class="modal-body break-all">{{userRegistrationTerms}}</div>  
                    <div class="modal-footer">  
                        <button type="button" class="btn btn-default" data-dismiss="modal">{% localized "CommonStrings|ButtonLabelClose" %}</button>  
                    </div>  
                </div>  
            </div>  
        </div>  
        <p>{% localized "SigninResources|TextblockUserRegistrationTermsProvided" %}</p>  
    </div>  
    {% endif %}  
</div>  
```  
  
### <a name="controls"></a>Denetimler  
 Bu şablon aşağıdaki [sayfa denetimlerini](api-management-page-controls.md)kullanabilir.  
  
-   [temel-oturum açma](api-management-page-controls.md#basic-signin)  
  
-   [sağlayıcılarla](api-management-page-controls.md#providers)  
  
### <a name="data-model"></a>Veri modeli  
 [Kullanıcı oturum açma](api-management-template-data-model-reference.md#UseSignIn) varlığı.  
  
### <a name="sample-template-data"></a>Örnek şablon verileri  
  
```json  
{
    "Email": null,
    "Password": null,
    "ReturnUrl": null,
    "RememberMe": false,
    "RegistrationEnabled": true,
    "DelegationEnabled": false,
    "DelegationUrl": null,
    "SsoSignUpUrl": null,
    "AuxServiceUrl": "https://portal.azure.com/#resource/subscriptions/{subscription ID}/resourceGroups/Api-Default-West-US/providers/Microsoft.ApiManagement/service/contoso5",
    "Providers": [  
        {  
            "Properties": {  
                "AuthenticationType": "Aad",  
                "Caption": "Azure Active Directory"  
            },  
            "AuthenticationType": "Aad",  
            "Caption": "Azure Active Directory"  
        }  
        ],
    "UserRegistrationTerms": null,
    "UserRegistrationTermsEnabled": false
}
```  
  
##  <a name="SignUp"></a>Kaydol  
 **Kaydolma** şablonu, geliştirici portalındaki kaydolma sayfasını özelleştirmenize olanak sağlar.  
  
 ![Kaydolma sayfası](./media/api-management-page-templates/APIM-Sign-Up-Page-Developer-Portal-Templates.png "APıM kaydolma sayfası geliştirici portalı şablonları")  
  
### <a name="default-template"></a>Varsayılan şablon  
  
```xml  
<h2 class="text-center">{% localized "SignupStrings|PageTitleSignup" %}</h2>  
<p class="text-center">  
  {% localized "SignupStrings|WebAuthenticationAlreadyAMember" %} <a href="/signin">{% localized "SignupStrings|WebAuthenticationSigninNow" %}</a>  
</p>  
  
<div class="row center-block ap-idp-container">  
  <div class="col-md-6">  
    <p>{% localized "SignupStrings|WebAuthenticationCreateNewAccount" %}</p>  
    <sign-up></sign-up>  
  </div>  
</div>  
```  
  
### <a name="controls"></a>Denetimler  
 Bu şablon aşağıdaki [sayfa denetimlerini](api-management-page-controls.md)kullanabilir.  
  
-   [Kaydolma](api-management-page-controls.md#sign-up)  
  
### <a name="data-model"></a>Veri modeli  
 [Kullanıcı kaydolma](api-management-template-data-model-reference.md#UserSignUp) varlığı.  
  
### <a name="sample-template-data"></a>Örnek şablon verileri  
  
```json  
{  
    "PasswordConfirm": null,  
    "Password": null,  
    "PasswordVerdictLevel": 0,  
    "UserRegistrationTerms": null,  
    "UserRegistrationTermsOptions": 0,  
    "ConsentAccepted": false,  
    "Email": null,  
    "FirstName": null,  
    "LastName": null,  
    "UserData": null,  
    "NameIdentifier": null,  
    "ProviderName": null  
}  
```  
  
##  <a name="PageNotFound"></a>Sayfa bulunamadı  
 **Sayfa bulunamadı** şablonu, geliştirici portalındaki sayfa bulunamadı sayfasını özelleştirmenize olanak sağlar.  
  
 ![Bulunamadı sayfası](./media/api-management-page-templates/APIM-Not-Found-Page-Developer-Portal-Templates.png "APıM bulunamadı sayfası geliştirici portalı şablonları")  
  
### <a name="default-template"></a>Varsayılan şablon  
  
```xml  
<h2>{% localized "NotFoundStrings|PageTitleNotFound" %}</h2>  
  
<h3>{% localized "NotFoundStrings|TitlePotentialCause" %}</h3>  
<ul>  
  <li>{% localized "NotFoundStrings|TextblockPotentialCauseOldLink" %}</li>  
  <li>{% localized "NotFoundStrings|TextblockPotentialCauseMisspelledUrl" %}</li>  
</ul>  
  
<h3>{% localized "NotFoundStrings|TitlePotentialSolution" %}</h3>  
<ul>  
  <li>{% localized "NotFoundStrings|TextblockPotentialSolutionRetype" %}</li>  
  <li>  
    {% capture textPotentialSolutionStartOver %}{% localized "NotFoundStrings|TextblockPotentialSolutionStartOver" %}{% endcapture %}  
    {% capture homeLink %}<a href="/">{% localized "NotFoundStrings|LinkLabelHomePage" %}</a>{% endcapture %}  
    {% assign replaceString = '{0}' %}  
  
    {{ textPotentialSolutionStartOver | replace : replaceString, homeLink }}  
  </li>  
</ul>  
  
<p>  
  {% capture textReportProblem %}{% localized "NotFoundStrings|TextReportProblem" %}{% endcapture %}  
  {% capture emailLink %}<a href="mailto:apimgmt@microsoft.com" target="_self" title="API Management Support">{% localized "NotFoundStrings|LinkLabelSendUsEmail" %}</a>{% endcapture %}  
  {% assign replaceString = '{0}' %}  
  
  {{ textReportProblem | replace : replaceString, emailLink }}  
</p>  
```  
  
### <a name="controls"></a>Denetimler  
 Bu şablon, herhangi bir [sayfa denetimini](api-management-page-controls.md)kullanamaz.  
  
### <a name="data-model"></a>Veri modeli  
  
|Özellik|Tür|Açıklama|  
|--------------|----------|-----------------|  
|referenceCode|string|Bu sayfa bir iç hatanın sonucu olarak görüntüleniyorsa kod oluşturulur.|  
|Raporladı|string|Bu sayfa bir iç hatanın sonucu olarak görüntüleniyorsa kod oluşturulur.|  
|emailBody|string|Bu sayfa bir iç hata sonucu olarak görüntüleniyorsa, e-posta gövdesi oluşturulur.|  
|requestedUrl|string|Sayfa bulunamadığında istenen URL.|  
|Refertekrar URL 'si|string|İstenen URL 'nin başvuran URL 'SI.|  
  
### <a name="sample-template-data"></a>Örnek şablon verileri  
  
```json  
{  
    "referenceCode": null,  
    "errorCode": null,  
    "emailBody": null,  
    "requestedUrl": "https://contoso5.portal.azure-api.net:443/NotFoundPage?startEditTemplate=NotFoundPage",  
    "referrerUrl": "https://contoso5.portal.azure-api.net/signup?startEditTemplate=SignUpTemplate"  
}  
```

## <a name="next-steps"></a>Sonraki adımlar
Şablonlarla çalışma hakkında daha fazla bilgi için bkz. [şablonları kullanarak API Management Geliştirici Portalını Özelleştirme](api-management-developer-portal-templates.md).
