---
title: Güvenlik işlemlerini tümleştirin ve yönetin-Azure Logic Apps & Microsoft Graph güvenliği
description: Microsoft Graph güvenlik & ile uygulamanızın tehdit korumasını, algılamayı ve yanıtını geliştirebilirsiniz Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: preetikr
ms.author: preetikr
ms.reviewer: klam, estfan, LADocs
ms.topic: article
ms.date: 01/30/2019
tags: connectors
ms.openlocfilehash: 845f57d84f49bdd964cc6f61790faff093f59466
ms.sourcegitcommit: d37991ce965b3ee3c4c7f685871f8bae5b56adfa
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72679088"
---
# <a name="improve-threat-protection-by-integrating-security-operations-with-microsoft-graph-security--azure-logic-apps"></a>Güvenlik işlemlerini Microsoft Graph güvenlik & tümleştirerek tehdit korumasını geliştirebilirsiniz Azure Logic Apps

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) ve [Microsoft Graph güvenlik](https://docs.microsoft.com/graph/security-concept-overview) Bağlayıcısı sayesinde, Microsoft güvenlik ürünleri, hizmetleri ve iş ortaklarının tümleştirilmesine yönelik otomatik iş akışları oluşturarak uygulamanızın tehditleri nasıl algıladığı, koruduğunu ve yanıtladığını artırabilirsiniz. Örneğin, uyarılar gibi Microsoft Graph güvenlik varlıklarını izleyen ve yöneten [Azure Güvenlik Merkezi oyun kitapları](../security-center/security-center-playbooks.md) oluşturabilirsiniz. Microsoft Graph güvenlik Bağlayıcısı tarafından desteklenen bazı senaryolar aşağıda verilmiştir:

* Sorguları veya uyarı KIMLIĞINE göre uyarıları alın. Örneğin, yüksek önem derecesi uyarıları içeren bir liste alabilirsiniz.
* Uyarıları güncelleştirin. Örneğin, uyarı atamalarını güncelleştirebilir, uyarılara yorum ekleyebilir veya uyarıları etiketleyebilir.
* [Uyarı abonelikleri (Web kancaları)](https://docs.microsoft.com/graph/api/resources/webhooks)oluşturarak uyarıların ne zaman oluşturulduğunu veya değiştirildiğini izler.
* Uyarı aboneliklerinizi yönetin. Örneğin, etkin abonelikler alabilir, bir aboneliğin süre sonu süresini genişletebilir veya abonelikleri silebilirsiniz.

Mantıksal uygulamanızın iş akışı, Microsoft Graph güvenlik bağlayıcısından yanıt alan eylemleri kullanabilir ve bu çıktıyı iş akışınızda diğer eylemler için kullanılabilir hale getirir. Ayrıca, iş akışınızda başka eylemlere de sahip olabilirsiniz Microsoft Graph güvenlik Bağlayıcısı eylemleriyle çıktıyı kullanın. Örneğin, Microsoft Graph güvenlik Bağlayıcısı aracılığıyla yüksek önem derecesine sahip uyarılar alırsanız, bu uyarıları Outlook bağlayıcısını kullanarak bir e-posta iletisiyle gönderebilirsiniz. 

Microsoft Graph güvenliği hakkında daha fazla bilgi edinmek için [Microsoft Graph güvenlik API 'sine genel bakış](https://aka.ms/graphsecuritydocs)bölümüne bakın. Logic Apps 'e yeni başladıysanız [ne Azure Logic Apps? ne olduğunu](../logic-apps/logic-apps-overview.md)gözden geçirin. Microsoft Flow veya PowerApps arıyorsanız bkz. [Flow nedir?](https://flow.microsoft.com/) veya [PowerApps nedir?](https://powerapps.microsoft.com/)

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği. Azure aboneliğiniz yoksa [ücretsiz bir Azure hesabı için kaydolun](https://azure.microsoft.com/free/). 

* Microsoft Graph güvenlik bağlayıcısını kullanmak için, [Microsoft Graph güvenlik kimlik doğrulama gereksinimlerinin](https://aka.ms/graphsecurityauth)bir parçası olan, *AÇıKÇA* Azure Active Directory (ad) Kiracı Yöneticisi onay vermiş olmanız gerekir. Bu onay, Microsoft Graph güvenlik bağlayıcısının uygulama KIMLIĞI ve adının yanı sıra [Azure Portal](https://portal.azure.com)de bulabileceğinizi gerektirir:

   | Özellik | Değer |
   |----------|-------|
   | **Uygulama adı** | `MicrosoftGraphSecurityConnector` |
   | **Uygulama KIMLIĞI** | `c4829704-0edc-4c3d-a347-7c4a67586f3c` |
   |||

   Bağlayıcıya izin vermek için, Azure AD kiracı yöneticiniz aşağıdaki adımları izleyebilir:

   * [Azure AD uygulamaları için kiracı yöneticisine Izin verin](../active-directory/develop/v2-permissions-and-consent.md).

   * Mantıksal uygulamanızın ilk çalıştırıldığı sırada, [uygulamanız uygulama onayı deneyimi](../active-directory/develop/application-consent-experience.md)aracılığıyla Azure AD kiracı yöneticinizden izin isteyebilir.
   
* [Mantıksal uygulamalar oluşturma](../logic-apps/quickstart-create-first-logic-app-workflow.md) hakkında temel bilgi

* Uyarılar gibi Microsoft Graph güvenlik varlıklarınıza erişmek istediğiniz mantıksal uygulama. Şu anda bu bağlayıcının tetikleyicisi yok. Bu nedenle, Microsoft Graph bir güvenlik eylemi kullanmak için, mantıksal uygulamanızı bir tetikleyici ile başlatın, örneğin **yineleme** tetikleyicisi.

## <a name="connect-to-microsoft-graph-security"></a>Microsoft Graph güvenliğine bağlanma 

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. [Azure Portal](https://portal.azure.com/)oturum açın ve daha önce açık değilse mantıksal uygulama Tasarımcısı 'nda mantıksal uygulamanızı açın.

1. Boş Logic Apps için, Microsoft Graph bir güvenlik eylemi eklemeden önce tetikleyiciyi ve istediğiniz diğer eylemleri ekleyin.

   -veya-

   Mevcut Logic Apps için, Microsoft Graph güvenlik eylemi eklemek istediğiniz son adım altında **yeni adım**' ı seçin.

   -veya-

   Adımlar arasında bir eylem eklemek için, işaretçinizi adımlar arasındaki oka taşıyın. 
   Görüntülenen artı işaretini (+) seçin ve **Eylem Ekle**' yi seçin.

1. Arama kutusuna filtreniz olarak "Microsoft Graph Security" yazın. Eylemler listesinden istediğiniz eylemi seçin.

1. Microsoft Graph güvenlik kimlik bilgilerinizle oturum açın.

1. Seçtiğiniz eyleminiz için gerekli ayrıntıları sağlayın ve mantıksal uygulamanızın iş akışını oluşturmaya devam edin.

## <a name="add-actions"></a>Eylem ekleme

Microsoft Graph güvenlik Bağlayıcısı ile kullanılabilen çeşitli eylemleri kullanma hakkında daha ayrıntılı bilgiler aşağıda verilmiştir.

### <a name="manage-alerts"></a>Uyarıları yönetme

En son sonuçları filtrelemek, sıralamak veya almak için *yalnızca* [Microsoft Graph tarafından desteklenen OData sorgu parametrelerini](https://docs.microsoft.com/graph/query-parameters)sağlayın. Tüm temel URL 'YI veya HTTP eylemini (örneğin, `https://graph.microsoft.com/v1.0/security/alerts` veya `GET` veya `PATCH` işlemi) *belirtmeyin* . Yüksek önem derecesine sahip bir liste istediğinizde, **uyarıları al** eyleminin parametrelerini gösteren belirli bir örnek aşağıda verilmiştir:

`Filter alerts value as Severity eq 'high'`

Bu bağlayıcı ile kullanabileceğiniz sorgular hakkında daha fazla bilgi için [Microsoft Graph güvenlik uyarıları başvuru belgelerine](https://docs.microsoft.com/graph/api/alert-list)bakın. Bu bağlayıcıyla ilgili gelişmiş deneyimler oluşturmak için bağlayıcının desteklediği [şema özellikleri uyarıları](https://docs.microsoft.com/graph/api/resources/alert) hakkında daha fazla bilgi edinin.

| Eylem | Açıklama |
|--------|-------------|
| **Uyarıları al** | Bir veya daha fazla [Uyarı özelliklerine](https://docs.microsoft.com/graph/api/resources/alert)göre filtrelenmiş uyarıları alın, örneğin: <p>`Provider eq 'Azure Security Center' or 'Palo Alto Networks'` | 
| **KIMLIĞE göre uyarı al** | Uyarı KIMLIğINE bağlı olarak belirli bir uyarı alın. | 
| **Uyarıyı Güncelleştir** | Uyarı KIMLIĞINE göre belirli bir uyarıyı güncelleştirin. <p>İsteğinize gerekli ve düzenlenebilir özellikleri geçirdiğinizden emin olmak için, [Uyarılar için düzenlenebilir özelliklere](https://docs.microsoft.com/graph/api/alert-update)bakın. Örneğin, araştırabilmeleri için bir güvenlik analiste uyarı atamak üzere uyarının **atanmış** özelliğini güncelleştirebilirsiniz. |
|||

### <a name="manage-alert-subscriptions"></a>Uyarı aboneliklerini yönetme

Microsoft Graph [*abonelikleri*](https://docs.microsoft.com/graph/api/resources/subscription)veya [*Web kancalarını*](https://docs.microsoft.com/graph/api/resources/webhooks)destekler. Abonelikleri almak, güncelleştirmek veya silmek için [Microsoft Graph tarafından desteklenen OData sorgu parametrelerini](https://docs.microsoft.com/graph/query-parameters) Microsoft Graph varlık yapısına sağlayın ve `security/alerts` ve ardından OData sorgusunu ekleyin. 
Temel URL 'yi *eklemeyin* , örneğin `https://graph.microsoft.com/v1.0`. Bunun yerine, şu örnekteki biçimi kullanın:

`security/alerts?$filter=status eq 'New'`

| Eylem | Açıklama |
|--------|-------------|
| **Abonelik oluşturma** | Herhangi bir değişiklik hakkında size bildirimde bulunan [bir abonelik oluşturun](https://docs.microsoft.com/graph/api/subscription-post-subscriptions) . İstediğiniz belirli uyarı türleri için bu aboneliği filtreleyebilirsiniz. Örneğin, size yüksek önem derecesi uyarıları bildiren bir abonelik oluşturabilirsiniz. |
| **Etkin abonelikleri al** | Süre [dolmamış abonelikler alın](https://docs.microsoft.com/graph/api/subscription-list). | 
| **Aboneliği Güncelleştir** | Abonelik KIMLIĞINI sağlayarak [bir aboneliği güncelleştirin](https://docs.microsoft.com/graph/api/subscription-update) . Örneğin, aboneliğinizi genişletmek için, aboneliğin `expirationDateTime` özelliğini güncelleştirebilirsiniz. | 
| **Aboneliği Sil** | Abonelik KIMLIĞINI sağlayarak [bir aboneliği silin](https://docs.microsoft.com/graph/api/subscription-delete) . | 
||| 

## <a name="connector-reference"></a>Bağlayıcı başvurusu

Bağlayıcının Openapı (eski adıyla Swagger) açıklaması tarafından tanımlanan Tetikleyiciler, Eylemler ve limitlerle ilgili teknik ayrıntılar için bağlayıcının [başvuru sayfasını](https://aka.ms/graphsecurityconnectorreference)gözden geçirin.

## <a name="get-support"></a>Destek alın

Sorularınız için [Azure Logic Apps forumunu](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps) ziyaret edin.
Özelliklerle ilgili fikirlerinizi göndermek veya gönderilmiş olanları oylamak için [Logic Apps kullanıcı geri bildirimi sitesini](https://aka.ms/logicapps-wish) ziyaret edin.

## <a name="next-steps"></a>Sonraki adımlar

Diğer [Logic Apps bağlayıcıları](../connectors/apis-list.md) hakkında bilgi edinin
