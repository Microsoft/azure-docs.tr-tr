---
title: SharePoint dosyaları-Soru-Cevap Oluşturma
titleSuffix: Azure Cognitive Services
description: Bilgi bankasını, Active Directory ile güvenliği sağlanabileceği sorular ve yanıtlar ile zenginleştirerek bilgi bankalarınıza güvenli SharePoint veri kaynakları ekleyin.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: diberry
ms.openlocfilehash: da73f42c17a3688e7f1f464ec4a3bbe77cbc9229
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/12/2019
ms.locfileid: "68955202"
---
# <a name="add-a-secured-sharepoint-data-source-to-your-knowledge-base"></a>Bilgi tabanınız için güvenli bir SharePoint veri kaynağı ekleme

Bilgi bankasını, Active Directory ile güvenliği sağlanabileceği sorular ve yanıtlar ile zenginleştirerek bilgi bankalarınıza güvenli SharePoint veri kaynakları ekleyin. 

Bilgi tabanınızı Soru-Cevap Oluşturma Yöneticisi olarak güvenli bir SharePoint belgesi eklediğinizde Soru-Cevap Oluşturma için Active Directory izin istemeniz gerekir. Bu izin Active Directory yöneticisinden SharePoint 'e erişim için Soru-Cevap Oluşturma verildiğinde, yeniden verilmesi gerekmez. Bilgi Bankası 'na eklenen her bir sonraki belge, aynı SharePoint kaynağıdır ise yetkilendirmeye ihtiyaç duymayacak. 

Soru-Cevap Oluşturma Bilgi Bankası Yöneticisi Active Directory yöneticisi değilse, bu işlemi tamamlaması için Active Directory Manager ile iletişim kurması gerekir.

## <a name="add-supported-file-types-to-knowledge-base"></a>Bilgi Bankası 'nda desteklenen dosya türlerini ekleme

Soru-Cevap Oluşturma tarafından desteklenen tüm [dosya türlerini](../Concepts/data-sources-supported.md) , bir SharePoint sitesinden bilgi tabanınızdan ekleyebilirsiniz. Dosya kaynağının güvenliği varsa, [izin](#permissions) vermeniz gerekebilir.

1. SharePoint sitesindeki kitaplıktan, dosyanın üç nokta menüsünü `...`seçin.
1. Dosyanın URL 'sini kopyalayın.

   ![Dosyanın üç nokta menüsünü seçip URL 'yi kopyalayarak SharePoint dosya URL 'sini alın.](../media/add-sharepoint-datasources/get-sharepoint-file-url.png)

1. Soru-Cevap Oluşturma portalında, **Ayarlar** sayfasında, Bilgi Bankası 'na [URL 'yi ekleyin](edit-knowledge-base.md#add-datasource) . 

### <a name="images-with-sharepoint-files"></a>SharePoint dosyaları içeren görüntüler

Dosyalar görüntüleri içeriyorsa, bunlar ayıklanmaz. Dosya QnA çiftlerine ayıklandıktan sonra, Soru-Cevap Oluşturma portalından görüntüyü ekleyebilirsiniz.

Aşağıdaki markaşağı sözdizimi ile görüntüyü ekleyin: 

```markdown
![Explanation or description of image](URL of public image)
```

Köşeli parantezdeki `[]`metin, görüntüyü açıklar. Parantez `()`içindeki URL, resmin doğrudan bağlantıdır. 

Etkileşimli test panelinde QnA çiftini test ettiğinizde, Soru-Cevap Oluşturma portalında, görüntü, markı metni yerine görüntülenir. Bu, görüntünün istemci uygulamanızdan herkese açık bir şekilde alındığını doğrular.

## <a name="permissions"></a>İzinler

Bir SharePoint sunucusundan güvenli bir dosya bir bilgi tabanına eklendiğinde izin verilmesi gerçekleşir. SharePoint 'in nasıl ayarlandığına ve dosyayı ekleyen kişinin izinlerine bağlı olarak, bunun gerekebilir:

* ek adım yok-dosyayı ekleyen kişi gereken tüm izinlere sahiptir.
* hem [Bilgi Bankası Yöneticisi](#knowledge-base-manager-add-sharepoint-data-source-in-qna-maker-portal) hem de [Active Directory Yöneticisi](#active-directory-manager-grant-file-read-access-to-qna-maker)ile adımlar.

Aşağıda listelenen adımlara bakın. 

### <a name="knowledge-base-manager-add-sharepoint-data-source-in-qna-maker-portal"></a>Bilgi Bankası Yöneticisi: Soru-Cevap Oluşturma portalında SharePoint veri kaynağı ekleme

**Soru-cevap oluşturma Yöneticisi** bir bilgi tabanına güvenli bir SharePoint belgesi eklediğinde, Bilgi Bankası yöneticisi, Active Directory yöneticisinin tamamlaması gereken bir izin isteği başlatır.

İstek bir Active Directory hesabının kimliğini doğrulamak için bir açılır pencere ile başlar. 

![Kullanıcı hesabının kimliğini doğrulama](../media/add-sharepoint-datasources/authenticate-user-account.png)

Soru-Cevap Oluşturma Yöneticisi hesabı seçtikten sonra, Azure Active Directory Yöneticisi SharePoint kaynağına Soru-Cevap Oluşturma uygulamanın (Soru-Cevap Oluşturma Manager değil) erişimine izin vermek gerektiğini belirten bir bildirim alır. Azure Active Directory yöneticisinin bu kaynaktaki her belgeyi değil, her SharePoint kaynağı için bunu yapması gerekir. 

### <a name="active-directory-manager-grant-file-read-access-to-qna-maker"></a>Active Directory Yöneticisi: Soru-Cevap Oluşturma dosya okuma erişimi verme

Active Directory Yöneticisi (Soru-Cevap Oluşturma Yöneticisi değil), Soru-Cevap Oluşturma Portal SharePoint Enterprise uygulamasına dosya okuma izinleri sağlamak üzere [Bu bağlantıyı](https://login.microsoftonline.com/common/oauth2/v2.0/authorize?response_type=id_token&scope=Files.Read%20Files.Read.All%20Sites.Read.All%20User.Read%20User.ReadBasic.All%20profile%20openid%20email&client_id=c2c11949-e9bb-4035-bda8-59542eb907a6&redirect_uri=https%3A%2F%2Fwww.qnamaker.ai%3A%2FCreate&state=68) seçerek SharePoint kaynağına erişmek için soru-cevap oluşturma erişim vermelidir. 

![Azure Active Directory Yöneticisi izin etkileşimli olarak izin verir](../media/add-sharepoint-datasources/aad-manager-grants-permission-interactively.png)

<!--
The Active Directory manager must grant QnA Maker access either by application name, `QnAMakerPortalSharePoint`, or by application ID, `c2c11949-e9bb-4035-bda8-59542eb907a6`. 
-->
<!--
### Grant access from the interactive pop-up window 

The Active Directory manager will get a pop-up window requesting permissions to the `QnAMakerPortalSharePoint` app. The pop-up window includes the QnA Maker Manager email address that initiated the request, an `App Info` link to learn more about **QnAMakerPortalSharePoint**, and a list of permissions requested. Select **Accept** to provide those permissions. 

![Azure Active Directory manager grants permission interactively](../media/add-sharepoint-datasources/aad-manager-grants-permission-interactively.png)
-->
<!--

### Grant access from the App Registrations list

1. The Active Directory manager signs in to the Azure portal and opens **[App registrations list](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ApplicationsListBlade)**. 

1. Search for and select the **QnAMakerPortalSharePoint** app. Change the second filter box from **My apps** to **All apps**. The app information will open on the right side.

    ![Select QnA Maker app in App registrations list](../media/add-sharepoint-datasources/select-qna-maker-app-in-app-registrations.png)

1. Select **Settings**.

    [![Select Settings in the right-side blade](../media/add-sharepoint-datasources/select-settings-for-qna-maker-app-registration.png)](../media/add-sharepoint-datasources/select-settings-for-qna-maker-app-registration.png#lightbox)

1. Under **API access**, select **Required permissions**. 

    ![Select 'Settings', then under 'API access', select 'Required permission'](../media/add-sharepoint-datasources/select-required-permissions-in-settings-blade.png)

1. Do not change any settings in the **Enable Access** window. Select **Grant Permission**. 

    [![Under 'Grant Permission', select 'Yes'](../media/add-sharepoint-datasources/grant-app-required-permissions.png)](../media/add-sharepoint-datasources/grant-app-required-permissions.png#lightbox)

1. Select **YES** in the pop-up confirmation windows. 

    ![Grant required permissions](../media/add-sharepoint-datasources/grant-required-permissions.png)
-->
### <a name="grant-access-from-the-azure-active-directory-admin-center"></a>Azure Active Directory Yönetim merkezinden erişim izni verme

1. Active Directory Yöneticisi Azure portal oturum açar ve **[Kurumsal uygulamaları](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps)** açar. 

1. `QnAMakerPortalSharePoint` Soru-cevap oluşturma seçin uygulamasını arayın. 

    [![Kurumsal uygulamalar listesinde QnAMakerPortalSharePoint araması yapın](../media/add-sharepoint-datasources/search-enterprise-apps-for-qna-maker.png)](../media/add-sharepoint-datasources/search-enterprise-apps-for-qna-maker.png#lightbox)

1. **Güvenlik**altında **izinler**' e gidin. **Kuruluş için yönetici onayı ver**' i seçin. 

    [![Active Directory Yöneticisi için kimliği doğrulanmış kullanıcı seçin](../media/add-sharepoint-datasources/grant-aad-permissions-to-enterprise-app.png)](../media/add-sharepoint-datasources/grant-aad-permissions-to-enterprise-app.png#lightbox)

1. Active Directory izin verme izinleri olan bir oturum açma hesabı seçin. 


  
<!--

## Add SharePoint data source with APIs

You need to get the SharePoint file's URI before adding it to QnA Maker. 

## Get SharePoint File URI

Use the following steps to transform the SharePoint URL into a sharing token.

1. Encode the URL using [base64](https://en.wikipedia.org/wiki/Base64). 

1. Convert the base64-encoded result to an unpadded base64url format with the following character changes. 

    * Remove the equal character, `=` from the end of the value. 
    * Replace `/` with `_`. 
    * Replace `+` with `-`. 
    * Append `u!` to be beginning of the string. 

1. Sign in to Graph explorer and run the following query, where `sharedURL` is ...:

    ```
    https://graph.microsoft.com/v1.0/shares/<sharedURL>/driveitem
    ```

    Get the **@microsoft.graph.downloadUrl** and use this as `fileuri` in the QnA Maker APIs.

### Add or update a SharePoint File URI to your knowledge base

Use the **@microsoft.graph.downloadUrl** from the previous section as the `fileuri` in the QnA Maker API for [adding a knowledge base](https://go.microsoft.com/fwlink/?linkid=2092179) or [updating a knowledge base](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update). The following fields are mandatory: name, fileuri, filename, source.

```
{
    "name": "Knowledge base name",
    "files": [
        {
            "fileUri": "<@microsoft.graph.downloadURL>",
            "fileName": "filename.xlsx",
            "source": "<SharePoint link>"
        }
    ],
    "urls": [],
    "users": [],
    "hostUrl": "",
    "qnaList": []
}
```



## Remove QnA Maker app from SharePoint authorization

1. Use the steps in the previous section to find the Qna Maker app in the Active Directory admin center. 
1. When you select the **QnAMakerPortalSharePoint**, select **Overview**. 
1. Select **Delete** to remove permissions. 

-->

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Bilgi bankasında işbirliği yapın](collaborate-knowledge-base.md)
