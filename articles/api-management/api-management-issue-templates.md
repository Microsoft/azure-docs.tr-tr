---
title: Azure API Management 'da şablonlar verme | Microsoft Docs
description: Azure API Management 'de geliştirici portalındaki sorun sayfalarının içeriğini özelleştirmeyi öğrenin.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 47da4bb2-426e-4e53-8fa7-214ee2e3ab37
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: 1dac90053797caf66af79e458b9dbb95b682cd17
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73176646"
---
# <a name="issue-templates-in-azure-api-management"></a>Azure API Management 'daki şablonları verme
Azure API Management, içeriğini yapılandıran bir dizi şablon kullanarak geliştirici portalı sayfalarının içeriğini özelleştirmenizi sağlar. [Dotsıvı](http://dotliquidmarkup.org/) Syntax ve seçtiğiniz düzenleyiciyi ( [Örneğin, tasarımcılar için dotlikit](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers)) ve sağlanan bir yerelleştirilmiş [dize kaynakları](api-management-template-resources.md#strings), [glif kaynakları](api-management-template-resources.md#glyphs)ve [sayfa denetimleri](api-management-page-controls.md)kümesini kullanarak, yapılandırmak için harika bir esneklik vardır Gördüğünüz sayfaların içeriği bu şablonları kullanarak sığdırar.  
  
 Bu bölümdeki şablonlar, geliştirici portalındaki sorun sayfalarının içeriğini özelleştirmenizi sağlar.  
  
-   [Sorun listesi](#IssueList)  
  
> [!NOTE]
>  Örnek varsayılan şablonlar aşağıdaki belgelere dahil edilmiştir, ancak sürekli iyileştirmeler nedeniyle değişikliğe tabidir. Canlı varsayılan şablonları, istenen ayrı şablonlara giderek Geliştirici Portalında görüntüleyebilirsiniz. Şablonlarla çalışma hakkında daha fazla bilgi için bkz. [şablonları kullanarak API Management Geliştirici Portalını Özelleştirme](api-management-developer-portal-templates.md).  

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
##  <a name="IssueList"></a>Sorun listesi  
 **Sorun listesi** şablonu, geliştirici portalındaki sorun listesi sayfasının gövdesini özelleştirmenizi sağlar.  
  
 ![Sorun listesi geliştirici portalı](./media/api-management-issue-templates/APIM-Issue-List-Developer-Portal.png "APıM sorun listesi geliştirici portalı")  
  
### <a name="default-template"></a>Varsayılan şablon  
  
```xml
<div class="row">
  <div class="col-md-9">
    <h2>{% localized "IssuesStrings|WebIssuesIndexTitle" %}</h2>
  </div>
</div>
<div class="row">
  <div class="col-md-12">
    {% if issues.size > 0 %}
    <ul class="list-unstyled">
      {% capture reportedBy %}{% localized "IssuesStrings|WebIssuesStatusReportedBy" %}{% endcapture %}
      {% assign replaceString0 = '{0}' %}
      {% assign replaceString1 = '{1}' %}
      {% for issue in issues %}
      <li>
        <h3>
          <a href="/issues/{{issue.id}}">{{issue.title}}</a>
        </h3>
        <p>{{issue.description}}</p>
        <em>
          {% capture state %}{{issue.issueState}}{% endcapture %}
          {% capture devName %}{{issue.subscriptionDeveloperName}}{% endcapture %}
          {% capture str1 %}{{ reportedBy | replace : replaceString0, state }}{% endcapture %}
          {{ str1 | replace : replaceString1, devName }}
          <span class="UtcDateElement">{{ issue.reportedOn | date: "r" }}</span>
        </em>
      </li>
      {% endfor %}
    </ul>
    <paging-control></paging-control>
    {% else %}
    {% localized "CommonResources|NoItemsToDisplay" %}
    {% endif %}
    {% if canReportIssue %}
    <a class="btn btn-primary" id="createIssue" href="/Issues/Create">{% localized "IssuesStrings|WebIssuesReportIssueButton" %}</a>
    {% elsif isAuthenticated %}
    <hr />
    <p>{% localized "IssuesStrings|WebIssuesNoActiveSubscriptions" %}</p>
    {% else %}
    <hr />
    <p>
      {% capture signIntext %}{% localized "IssuesStrings|WebIssuesNotSignin" %}{% endcapture %}
      {% capture link %}<a href="/signin">{% localized "IssuesStrings|WebIssuesSignIn" %}</a>{% endcapture %}
      {{ signIntext | replace : replaceString0, link }}
    </p>
    {% endif %}
  </div>
</div>
```
  
### <a name="controls"></a>Denetimler  
 `Issue list` şablonunda aşağıdaki [sayfa denetimleri](api-management-page-controls.md)kullanılabilir.  
  
-   [sayfalama denetimi](api-management-page-controls.md#paging-control)  
  
### <a name="data-model"></a>Veri modeli  
  
|Özellik|Tür|Açıklama|  
|--------------|----------|-----------------|  
|`Issues`|[Sorun](api-management-template-data-model-reference.md#Issue) varlıkları koleksiyonu.|Geçerli kullanıcıya görünen sorunlar.|  
|`Paging`|[Sayfalama](api-management-template-data-model-reference.md#Paging) varlığı.|Uygulamalar koleksiyonu için sayfalama bilgileri.|  
|`IsAuthenticated`|boole|Geçerli kullanıcının Geliştirici Portalında oturum açmış olup olmadığı.|  
|`CanReportIssues`|boole|Geçerli kullanıcının bir sorunu dosyaya verme izinleri olup olmadığı.|  
|`Search`|string|Bu özellik kullanım dışıdır ve kullanılmamalıdır.|  
  
### <a name="sample-template-data"></a>Örnek şablon verileri  
  
```json
{
    "Issues": [
        {
            "Id": "5702b68bb16653124c8f9ba7",
            "ApiId": "570275f1b16653124c8f9ba3",
            "Title": "I couldn't figure out how to connect my application to the API",
            "Description": "I'm having trouble connecting my application to the backend API.",
            "SubscriptionDeveloperName": "Clayton",
            "IssueState": "Proposed",
            "ReportedOn": "2016-04-04T18:46:35.64",
            "Comments": null,
            "Attachments": null,
            "Services": null
        }
    ],
    "Paging": {
        "Page": 1,
        "PageSize": 10,
        "TotalItemCount": 1,
        "ShowAll": false,
        "PageCount": 1
    },
    "IsAuthenticated": true,
    "CanReportIssue": true,
    "Search": null
}
```

## <a name="next-steps"></a>Sonraki adımlar
Şablonlarla çalışma hakkında daha fazla bilgi için bkz. [şablonları kullanarak API Management Geliştirici Portalını Özelleştirme](api-management-developer-portal-templates.md).
