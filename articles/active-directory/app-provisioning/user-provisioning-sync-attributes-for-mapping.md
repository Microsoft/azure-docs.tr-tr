---
title: Eşleme için öznitelikleri Azure Active Directory eşitler
description: Azure Active Directory ve SaaS uygulamalarıyla Kullanıcı sağlamayı yapılandırırken, varsayılan olarak eşitlenmemiş kaynak özniteliklerini eklemek için dizin uzantısı özelliğini kullanın.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: troubleshooting
ms.date: 03/31/2021
ms.author: kenwith
ms.openlocfilehash: f7a2429161cebe867d844b4ca7aa08ec3613edcd
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107388219"
---
# <a name="syncing-extension-attributes-for-app-provisioning"></a>Uygulama sağlama için uzantı öznitelikleri eşitleniyor

Azure Active Directory (Azure AD), Azure AD 'den bir [SaaS uygulamasına](../saas-apps/tutorial-list.md)Kullanıcı hesapları sağlarken bir kullanıcı profili oluşturmak için gereken tüm verileri (öznitelikleri) içermelidir. Kullanıcı hazırlama için öznitelik eşlemelerini özelleştirirken, eşleme yapmak istediğiniz özniteliği **kaynak öznitelik** listesinde görünmediğini görebilirsiniz. Bu makalede eksik özniteliği nasıl ekleyeceğiniz gösterilir.

Yalnızca Azure AD 'deki kullanıcılar için [PowerShell veya Microsoft Graph kullanarak şema uzantıları oluşturabilirsiniz](#create-an-extension-attribute-on-a-cloud-only-user).

Şirket içi Active Directory kullanıcılar için, kullanıcıları Azure AD ile eşitlemeniz gerekir. [Azure AD Connect](../hybrid/whatis-azure-ad-connect.md)kullanarak kullanıcıları ve öznitelikleri eşitleyebilirsiniz. Azure AD Connect, bazı öznitelikleri otomatik olarak Azure AD 'ye eşitler, ancak tüm özniteliklere vermez. Ayrıca, varsayılan olarak eşitlenen bazı öznitelikler (SAMAccountName gibi) Azure AD Graph API kullanılarak sunulmayabilir. Bu durumlarda, [özniteliği Azure AD ile eşleştirmek için Azure AD Connect Directory uzantısı özelliğini kullanabilirsiniz](#create-an-extension-attribute-using-azure-ad-connect). Bu şekilde, öznitelik Azure AD Graph API ve Azure AD sağlama hizmeti tarafından görünür olur.

## <a name="create-an-extension-attribute-on-a-cloud-only-user"></a>Yalnızca bulut kullanıcısı üzerinde uzantı özniteliği oluşturma
Microsoft Graph ve PowerShell 'i kullanarak Azure AD 'deki kullanıcılar için Kullanıcı şemasını genişletebilirsiniz. Bu uzantı öznitelikleri çoğu durumda otomatik olarak keşfedilir.

1000 ' den fazla hizmet sorumlusu varsa, kaynak öznitelik listesinde eksik olan uzantıları bulabilirsiniz. Oluşturduğunuz bir öznitelik otomatik olarak görünmezse, özniteliğin oluşturulduğunu doğrulayın ve şemaya el ile ekleyin. Oluşturulduğunu doğrulamak için Microsoft Graph ve [grafik Gezgini](/graph/graph-explorer/graph-explorer-overview)' ni kullanın. Şemaya el ile eklemek için, bkz. [desteklenen özniteliklerin listesini düzenlemeyle](customize-application-attributes.md#editing-the-list-of-supported-attributes).

### <a name="create-an-extension-attribute-on-a-cloud-only-user-using-microsoft-graph"></a>Microsoft Graph kullanarak yalnızca bulutta bulunan bir kullanıcı üzerinde uzantı özniteliği oluşturma
[Microsoft Graph](/graph/overview)kullanarak Azure AD kullanıcıları şemasını genişletebilirsiniz. 

İlk olarak, üzerinde çalıştığınız uygulamanın KIMLIĞINI almak için kiracınızdaki uygulamaları listeleyin. Daha fazla bilgi için bkz. [extensionProperties 'ı listeleme](/graph/api/application-list-extensionproperty?view=graph-rest-1.0&tabs=http&preserve-view=true).

```json
GET https://graph.microsoft.com/v1.0/applications
```

Sonra, Extension özniteliğini oluşturun. Aşağıdaki **ID** özelliğini, önceki adımda alınan **kimlik** ile değiştirin. "AppID" değil, **"ID"** özniteliğini kullanmanız gerekir. Daha fazla bilgi edinmek için bkz. [extensionProperty oluşturma]/Graph/api/Application-Post-extensionproperty.exe. MD? View = Graph-Rest-1.0&sekmeler = http&Preserve-View = true).

```json
POST https://graph.microsoft.com/v1.0/applications/{id}/extensionProperties
Content-type: application/json

{
    "name": "extensionName",
    "dataType": "string",
    "targetObjects": [
        "User"
    ]
}
```

Önceki istek biçimiyle bir uzantı özniteliği oluşturdu `extension_appID_extensionName` . Artık bir kullanıcıyı bu uzantı özniteliğiyle güncelleştirebilirsiniz. Daha fazla bilgi için bkz. [Kullanıcı güncelleştirme](/graph/api/user-update?view=graph-rest-1.0&tabs=http&preserve-view=true).
```json
PATCH https://graph.microsoft.com/v1.0/users/{id}
Content-type: application/json

{
  "extension_inputAppId_extensionName": "extensionValue"
}
```
Son olarak, kullanıcının özniteliğini doğrulayın. Daha fazla bilgi için bkz. [Kullanıcı alma](/graph/api/user-get?view=graph-rest-1.0&tabs=http#example-3-users-request-using-select&preserve-view=true).

```json
GET https://graph.microsoft.com/v1.0/users/{id}?$select=displayName,extension_inputAppId_extensionName
```


### <a name="create-an-extension-attribute-on-a-cloud-only-user-using-powershell"></a>PowerShell kullanarak yalnızca bulutta yer alan bir kullanıcı için uzantı özniteliği oluşturma
PowerShell kullanarak özel bir uzantı oluşturun ve bir kullanıcıya bir değer atayın. 

```
#Connect to your Azure AD tenant   
Connect-AzureAD

#Create an application (you can instead use an existing application if you would like)
$App = New-AzureADApplication -DisplayName “test app name” -IdentifierUris https://testapp

#Create a service principal
New-AzureADServicePrincipal -AppId $App.AppId

#Create an extension property
New-AzureADApplicationExtensionProperty -ObjectId $App.ObjectId -Name “TestAttributeName” -DataType “String” -TargetObjects “User”

#List users in your tenant to determine the objectid for your user
Get-AzureADUser

#Set a value for the extension property on the user. Replace the objectid with the ID of the user and the extension name with the value from the previous step
Set-AzureADUserExtension -objectid 0ccf8df6-62f1-4175-9e55-73da9e742690 -ExtensionName “extension_6552753978624005a48638a778921fan3_TestAttributeName”

#Verify that the attribute was added correctly.
Get-AzureADUser -ObjectId 0ccf8df6-62f1-4175-9e55-73da9e742690 | Select -ExpandProperty ExtensionProperty

```

## <a name="create-an-extension-attribute-using-azure-ad-connect"></a>Azure AD Connect kullanarak uzantı özniteliği oluşturma

1. Azure AD Connect Sihirbazı 'nı açın, görevler ' i ve ardından **eşitleme seçeneklerini Özelleştir**' i seçin.

   ![Azure Active Directory Connect Sihirbazı ek görevler sayfası](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-customize.png)
 
2. Azure AD Genel Yöneticisi olarak oturum açın. 

3. **Isteğe bağlı özellikler** sayfasında, **dizin uzantısı öznitelik eşitleme**' yi seçin.
 
   ![Azure Active Directory Connect sihirbaz Isteğe bağlı özellikler sayfası](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extension-attribute-sync.png)

4. Azure AD 'ye genişletmek istediğiniz öznitelikleri seçin.
   > [!NOTE]
   > **Kullanılabilir öznitelikler** altında arama büyük/küçük harfe duyarlıdır.

   !["Dizin uzantıları" seçim sayfasını gösteren ekran görüntüsü](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extensions.png)

5. Azure AD Connect Sihirbazı 'nı tamamlayıp tam eşitleme döngüsünün çalışmasına izin verin. Cycle tamamlandığında, şema genişletilir ve yeni değerler, şirket içi AD ile Azure AD arasında eşitlenir.
 
6. Azure portal, [Kullanıcı özniteliği eşlemelerini düzenlemekte](customize-application-attributes.md)olduğunuz sırada, **kaynak öznitelik** listesi artık eklenen özniteliği biçiminde içerecektir `<attributename> (extension_<appID>_<attributename>)` . Özniteliği seçin ve sağlama için hedef uygulamayla eşleyin.

   ![Azure Active Directory Connect Sihirbazı Dizin uzantıları seçim sayfası](./media/user-provisioning-sync-attributes-for-mapping/attribute-mapping-extensions.png)

> [!NOTE]
> **ManagedBy** veya **DN/distinguishedName 'dir** gibi şirket içi ad 'den başvuru öznitelikleri sağlama özelliği bugün desteklenmez. Bu özelliği [Kullanıcı seste](https://feedback.azure.com/forums/169401-azure-active-directory)isteyebilirsiniz. 


## <a name="next-steps"></a>Sonraki adımlar

* [Sağlama için kimin kapsam içinde olduğunu tanımlama](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)
