---
title: Yönetilen kimliklerle kimlik doğrulama-Azure Logic Apps
description: Yönetilen bir kimlik kullanarak kimlik bilgileriyle veya gizli anahtarlarla oturum açmadan diğer Azure Active Directory kiracılardaki kaynaklara erişin
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
services: logic-apps
ms.service: logic-apps
ms.suite: integration
ms.topic: article
ms.date: 10/21/2019
ms.openlocfilehash: 2d1dbde2499dbe793a895f894e5ae83c36c54449
ms.sourcegitcommit: fa5ce8924930f56bcac17f6c2a359c1a5b9660c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/31/2019
ms.locfileid: "73200634"
---
# <a name="authenticate-access-to-azure-resources-by-using-managed-identities-in-azure-logic-apps"></a>Azure Logic Apps içindeki yönetilen kimlikleri kullanarak Azure kaynaklarına erişimi kimlik doğrulaması

Diğer Azure Active Directory (Azure AD) kiracılarındaki kaynaklara erişmek ve oturum açmadan kimliğinizi doğrulamak için mantıksal uygulamanız, sistem tarafından atanan [yönetilen kimliği](../active-directory/managed-identities-azure-resources/overview.md) (eski adıyla YÖNETILEN HIZMET KIMLIĞI veya MSI) kullanabilir, örneğin kimlik bilgileri veya gizli diziler. Azure bu kimliği sizin için yönetir ve gizli dizileri sağlamanız veya döndürmenize gerek olmadığından kimlik bilgilerinizin güvenliğini sağlamaya yardımcı olur. Bu makalede, mantıksal uygulamanızda sistem tarafından atanan yönetilen kimliğin nasıl ayarlanacağı ve kullanılacağı gösterilir. Şu anda yönetilen kimlikler yalnızca [belirli yerleşik Tetikleyiciler ve eylemlerle](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-to-outbound-calls)birlikte çalışarak, yönetilmeyen bağlayıcılar veya bağlantılar değildir.

Daha fazla bilgi için şu konulara bakın:

* [Yönetilen kimlikleri destekleyen Tetikleyiciler ve eylemler](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
* [Yönetilen kimliklerle Azure AD kimlik doğrulamasını destekleyen Azure hizmetleri](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)
* [Giden aramalarda desteklenen kimlik doğrulama türleri](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
* [Mantıksal uygulamalar için yönetilen kimlik sınırları](../logic-apps/logic-apps-limits-and-config.md#managed-identity)

## <a name="prerequisites"></a>Önkoşullar

* Bir Azure aboneliği veya aboneliğiniz yoksa [ücretsiz bir Azure hesabı için kaydolun](https://azure.microsoft.com/free/). Hem yönetilen kimlik hem de erişim istediğiniz hedef Azure kaynağının aynı Azure aboneliğini kullanması gerekir.

* Hedef kaynakla aynı Azure AD kiracısındaki yönetilen kimliklere roller atayabilecek [Azure AD yönetici izinleri](../active-directory/users-groups-roles/directory-assign-admin-roles.md) . Azure kaynağına bir yönetilen kimlik erişimi sağlamak için hedef kaynakta bu kimlik için bir rol eklemeniz gerekir.

* Erişmek istediğiniz hedef Azure kaynağı

* [Yönetilen kimlikleri destekleyen Tetikleyiciler ve eylemleri](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound) kullanan bir mantıksal uygulama

<a name="system-assigned"></a>

## <a name="enable-system-assigned-identity"></a>Sistem tarafından atanan kimliği etkinleştir

Kullanıcı tarafından atanan kimliklerin aksine, sistem tarafından atanan kimliği el ile oluşturmanız gerekmez. Mantıksal uygulamanızın sistem tarafından atanan kimliğini ayarlamak için kullanabileceğiniz seçenekler şunlardır:

* [Azure portalda](#azure-portal-system-logic-app)
* [Azure Resource Manager şablonları](#template-system-logic-app)
* [Azure PowerShell](../active-directory/managed-identities-azure-resources/howto-assign-access-powershell.md)
* [Azure CLI](../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md)

<a name="azure-portal-system-logic-app"></a>

### <a name="enable-system-assigned-identity-in-azure-portal"></a>Azure portal 'de sistem tarafından atanan kimliği etkinleştirme

1. [Azure Portal](https://portal.azure.com)mantıksal uygulama tasarımcısında mantıksal uygulamanızı açın.

1. Mantıksal uygulama menüsünde, **Ayarlar**altında, **kimlik** > **sistem atandı**' ı seçin. **Durum**' un altında **,**  > **Kaydet** > **Evet**' i seçin.

   ![Sistem tarafından atanan kimliği etkinleştirme](./media/create-managed-service-identity/turn-on-system-assigned-identity.png)

   Mantıksal uygulamanız artık Azure Active Directory kayıtlı olan ve bir nesne KIMLIĞIYLE temsil edilen sistem tarafından atanan kimliği kullanabilir.

   ![Sistem tarafından atanan kimliğin nesne KIMLIĞI](./media/create-managed-service-identity/object-id.png)

   | Özellik | Değer | Açıklama |
   |----------|-------|-------------|
   | **Nesne KIMLIĞI** | <*Identity-kaynak kimliği*> | Azure AD kiracınızdaki mantıksal uygulamanız için sistem tarafından atanan kimliği temsil eden bir genel benzersiz tanımlayıcı (GUID) |
   ||||

1. Şimdi [kaynağa kimlik erişimi veren adımları](#access-other-resources)izleyin.

<a name="template-system-logic-app"></a>

### <a name="enable-system-assigned-identity-in-azure-resource-manager-template"></a>Azure Resource Manager şablonunda sistem tarafından atanan kimliği etkinleştirme

Logic Apps gibi Azure kaynaklarını oluşturma ve dağıtmaya otomatik hale getirmek için [Azure Resource Manager şablonları](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)kullanabilirsiniz. Şablondaki mantıksal uygulamanız için sistem tarafından atanan yönetilen kimliği etkinleştirmek üzere, `identity` nesnesini ve `type` alt özelliğini şablondaki mantıksal uygulamanın kaynak tanımına ekleyin, örneğin:

```json
{
   "apiVersion": "2016-06-01",
   "type": "Microsoft.logic/workflows",
   "name": "[variables('logicappName')]",
   "location": "[resourceGroup().location]",
   "identity": {
      "type": "SystemAssigned"
   },
   "properties": {
      "definition": {
         "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
         "actions": {},
         "parameters": {},
         "triggers": {},
         "contentVersion": "1.0.0.0",
         "outputs": {}
   },
   "parameters": {},
   "dependsOn": []
}
```

Azure mantıksal uygulama kaynak tanımınızı oluşturduğunda, `identity` nesnesi bu ek özellikleri alır:

```json
"identity": {
   "type": "SystemAssigned",
   "principalId": "<principal-ID>",
   "tenantId": "<Azure-AD-tenant-ID>"
}
```

| Özellik (JSON) | Değer | Açıklama |
|-----------------|-------|-------------|
| `principalId` | <*asıl kimliği*> | Azure AD kiracısında mantıksal uygulamanızı temsil eden yönetilen kimliğin hizmet sorumlusu nesnesinin genel benzersiz tanıtıcısı (GUID). Bu GUID bazen bir "nesne KIMLIĞI" veya `objectID`olarak görünür. |
| `tenantId` | <*Azure-AD-KIRACı kimliği*> | Mantıksal uygulamanın artık bir üyesi olduğu Azure AD kiracısını temsil eden genel benzersiz tanımlayıcı (GUID). Azure AD kiracısı içinde hizmet sorumlusu, mantıksal uygulama örneğiyle aynı ada sahiptir. |
||||

<a name="access-other-resources"></a>

## <a name="give-identity-access-to-resources"></a>Kaynaklara kimlik erişimi verme

Mantıksal uygulamanız için yönetilen bir kimlik ayarladıktan sonra, [Bu kimliğe diğer Azure kaynaklarına erişim izni verebilirsiniz](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md). Bu kimliği, kimlik doğrulaması için kullanabilirsiniz.

1. [Azure Portal](https://portal.azure.com), yönetilen kimliğinizin erişimini istediğiniz Azure kaynağına gidin.

1. Kaynak menüsünde, bu kaynak için geçerli rol atamalarını gözden geçirebileceğiniz **rol atamalarını** > **ERIŞIM denetimi (IAM)** seçeneğini belirleyin. Araç çubuğunda **ekle** > **rol ataması Ekle**' yi seçin.

   !["Rol ataması Ekle" > "Ekle" yi seçin](./media/create-managed-service-identity/add-role-to-resource.png)

   > [!TIP]
   > **Rol ataması Ekle** seçeneği devre dışıysa, büyük olasılıkla izinlerinizin olması gerekmez. Kaynakları kaynaklar için yönetmenizi sağlayan izinler hakkında daha fazla bilgi için, [Azure Active Directory Içindeki yönetici rolü izinleri](../active-directory/users-groups-roles/directory-assign-admin-roles.md)bölümüne bakın.

1. **Rol ataması Ekle**' nin altında, kimliğinize hedef kaynağa gereken erişimi veren bir **rol** seçin.

   Bu konunun örneği için, kimliğiniz bir [Azure depolama kapsayıcısındaki bloba erişebilen bir role](../storage/common/storage-auth-aad.md#assign-rbac-roles-for-access-rights)ihtiyaç duyuyor:

   !["Depolama Blobu verileri katılımcısı" rolünü seçin](./media/create-managed-service-identity/assign-role.png)

1. **Erişim ata** kutusunda **Azure AD Kullanıcı, Grup veya hizmet sorumlusu**' nı seçin.

   ![Sistem tarafından atanan kimlik için erişimi seçin](./media/create-managed-service-identity/assign-access-system.png)

1. **Seç** kutusunda mantıksal uygulamanızı bulun ve seçin.

   ![Sistem tarafından atanan kimlik için mantıksal uygulama seçin](./media/create-managed-service-identity/add-permissions-select-logic-app.png)

1. İşiniz bittiğinde **Kaydet**'i seçin.

   Hedef kaynağın rol atamaları listesi artık seçili yönetilen kimliği ve rolü gösterir.

   ![Hedef kaynağa Yönetilen kimlikler ve roller eklendi](./media/create-managed-service-identity/added-roles-for-identities.png)

1. Şimdi, yönetilen kimlikleri destekleyen bir tetikleyici veya eylemde [kimlik doğrulamak için adımları](#authenticate-access-with-identity) izleyin.

<a name="authenticate-access-with-identity"></a>

## <a name="authenticate-access-with-managed-identity"></a>Yönetilen kimlikle erişimin kimliğini doğrulama

[Mantıksal uygulamanız için yönetilen kimliği etkinleştirdikten](#azure-portal-system-logic-app) ve [Bu kimliğe hedef kaynağa veya varlığa erişim izni](#access-other-resources)verdikten sonra, bu kimliği [yönetilen kimlikleri destekleyen Tetikleyiciler ve eylemler](logic-apps-securing-a-logic-app.md#managed-identity-authentication)' de kullanabilirsiniz.

> [!IMPORTANT]
> Sistem tarafından atanan kimliği kullanmak istediğiniz bir Azure işleviniz varsa, önce [Azure işlevleri için kimlik doğrulamasını etkinleştirin](../logic-apps/logic-apps-azure-functions.md#enable-authentication-for-azure-functions).

Bu adımlarda, Azure portal aracılığıyla yönetilen kimliğin bir tetikleyici veya eylemle nasıl kullanılacağı gösterilmektedir. Bir tetikleyici veya eylemin temel alınan JSON tanımında yönetilen kimliği belirtmek için bkz. [yönetilen kimlik kimlik doğrulaması](../logic-apps/logic-apps-securing-a-logic-app.md#managed-identity-authentication).

1. [Azure Portal](https://portal.azure.com)mantıksal uygulama tasarımcısında mantıksal uygulamanızı açın.

1. Henüz yapmadıysanız, [yönetilen kimlikleri destekleyen tetikleyiciyi veya eylemi](logic-apps-securing-a-logic-app.md#managed-identity-authentication)ekleyin.

   Örneğin, HTTP tetikleyicisi veya eylemi mantıksal uygulamanız için etkinleştirdiğiniz sistem tarafından atanan kimliği kullanabilir. Genel olarak, HTTP tetikleyicisi veya eylemi, erişmek istediğiniz kaynağı veya varlığı belirtmek için bu özellikleri kullanır:

   | Özellik | Gereklidir | Açıklama |
   |----------|----------|-------------|
   | **Yöntem** | Yes | Çalıştırmak istediğiniz işlem tarafından kullanılan HTTP yöntemi |
   | **KULLANıLMAMıŞSA** | Yes | Hedef Azure kaynağına veya varlığına erişmek için uç nokta URL 'SI. URI sözdizimi genellikle Azure kaynağı veya hizmeti için [kaynak kimliğini](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) içerir. |
   | **Üst Bilgiler** | Hayır | İçerik türü gibi giden isteğe dahil etmek veya istediğiniz tüm üst bilgi değerleri |
   | **Sorgular** | Hayır | Belirli bir işlemin parametresi ya da çalıştırmak istediğiniz işlem için API sürümü gibi isteğe dahil etmek istediğiniz veya isteğe dahil etmek istediğiniz sorgu parametreleri |
   | **Kimlik doğrulaması** | Yes | Hedef kaynağa veya varlığa erişimi doğrulamak için kullanılacak kimlik doğrulaması türü |
   ||||

   Belirli bir örnek olarak, [anlık görüntü blobu işlemini](https://docs.microsoft.com/rest/api/storageservices/snapshot-blob) , Azure depolama hesabındaki bir blob üzerinde daha önce Kimliğiniz için daha önce ayarladığınız bir blob üzerinde çalıştırmak istediğinizi varsayalım. Ancak, [Azure Blob depolama Bağlayıcısı](https://docs.microsoft.com/connectors/azureblob/) Şu anda bu işlemi sunmaz. Bunun yerine, [http eylemini](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action) veya başka bir [BLOB hizmeti REST API işlemini](https://docs.microsoft.com/rest/api/storageservices/operations-on-blobs)kullanarak bu işlemi çalıştırabilirsiniz.

   > [!IMPORTANT]
   > HTTP isteklerini ve yönetilen kimlikleri kullanarak güvenlik duvarlarının arkasındaki Azure depolama hesaplarına erişmek için, depolama hesabınızı [Güvenilen Microsoft hizmetlerinin erişimine izin veren özel durumla](../connectors/connectors-create-api-azureblobstorage.md#access-trusted-service)ayarladığınızdan emin olun.

   [Anlık görüntü blobu işlemini](https://docs.microsoft.com/rest/api/storageservices/snapshot-blob)ÇALıŞTıRMAK için http eylemi şu özellikleri belirtir:

   | Özellik | Gereklidir | Örnek değer | Açıklama |
   |----------|----------|---------------|-------------|
   | **Yöntem** | Yes | `PUT`| Anlık görüntü blobu işleminin kullandığı HTTP yöntemi |
   | **KULLANıLMAMıŞSA** | Yes | `https://{storage-account-name}.blob.core.windows.net/{blob-container-name}/{folder-name-if-any}/{blob-file-name-with-extension}` | Bu söz dizimini kullanan Azure genel (genel) ortamındaki bir Azure Blob depolama dosyasının kaynak KIMLIĞI |
   | **Üst Bilgiler** | Evet, Azure depolama için | `x-ms-blob-type` = `BlockBlob` <p>`x-ms-version` = `2019-02-02` | Azure depolama işlemleri için gereken `x-ms-blob-type` ve `x-ms-version` üst bilgi değerleri. <p><p>**Önemli**: giden http tetikleyicisinde ve Azure depolama için eylem isteklerinde, üst bilgi, çalıştırmak istediğiniz işlem için `x-ms-version` ÖZELLIĞINI ve API sürümünü gerektirir. <p>Daha fazla bilgi için şu konulara bakın: <p><p>- [istek üstbilgileri-anlık görüntü blobu](https://docs.microsoft.com/rest/api/storageservices/snapshot-blob#request) <br>[Azure depolama hizmetleri için - sürümü oluşturma](https://docs.microsoft.com/rest/api/storageservices/versioning-for-the-azure-storage-services#specifying-service-versions-in-requests) |
   | **Sorgular** | Evet, bu işlem için | `comp` = `snapshot` | Anlık görüntü blobu işleminin sorgu parametresi adı ve değeri. |
   | **Kimlik doğrulaması** | Yes | `Managed Identity` | Azure Blob 'a erişim kimlik doğrulaması için kullanılacak kimlik doğrulaması türü |
   |||||

   Bu özellik değerlerinin tümünü gösteren örnek HTTP eylemi aşağıda verilmiştir:

   ![Azure kaynağına erişmek için HTTP eylemi ekleme](./media/create-managed-service-identity/http-action-example.png)

   Tüm kullanılabilir Azure REST API işlemleri hakkında daha fazla bilgi için bkz. [azure REST API başvurusu](https://docs.microsoft.com/rest/api/azure/).

1. **Kimlik doğrulama** listesinden **yönetilen kimlik**' i seçin. [ **Kimlik doğrulama** özelliği desteklenir](logic-apps-securing-a-logic-app.md#add-authentication-outbound) , ancak gizliyse, **yeni parametre Ekle** listesini açın ve **kimlik doğrulaması**' nı seçin.

   > [!NOTE]
   > Tüm tetikleyiciler ve eylemler bir kimlik doğrulama türünü seçmenizi sağlar. Daha fazla bilgi için bkz. [giden çağrılara kimlik doğrulama ekleme](logic-apps-securing-a-logic-app.md#add-authentication-outbound).

   !["Kimlik doğrulaması" özelliğinde "yönetilen kimlik" i seçin](./media/create-managed-service-identity/select-managed-identity.png)

1. **Yönetilen kimlik**' i seçtikten sonra bazı Tetikleyiciler ve eylemler için **izleyici** özelliği görüntülenir. **Hedef kitle** özelliği desteklenir, ancak gizliyse, **yeni parametre Ekle** listesini açın ve **hedef kitle**' i seçin.

1. Hedef kaynak veya hizmet için hedef **kitle** DEĞERINI kaynak kimliği olarak ayarladığınızdan emin olun. Aksi takdirde, varsayılan olarak, **hedef kitle** özelliği, Azure Resource Manager kaynak kimliği olan `https://management.azure.com/` kaynak kimliğini kullanır.

   > [!IMPORTANT]
   > Hedef kaynak KIMLIĞININ, tüm gerekli eğik çizgiler de dahil olmak üzere, Azure Active Directory (AD) beklediği değerle *tam olarak eşleştiğinden* emin olun. Örneğin, tüm Azure Blob depolama hesaplarının kaynak KIMLIĞI, sonunda eğik çizgi gerektirir. Ancak, belirli bir depolama hesabının kaynak KIMLIĞI, sonunda eğik çizgi gerektirmez. [Azure AD 'yi destekleyen Azure hizmetleri için kaynak kimliklerini](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)denetleyin.

   Bu örnek, kimlik doğrulaması için kullanılan erişim belirteçlerinin tüm depolama hesapları için geçerli olması için **hedef kitle** özelliğini `https://storage.azure.com/` olarak ayarlar. Ancak, belirli bir depolama hesabı için `https://fabrikamstorageaccount.blob.core.windows.net`kök hizmeti URL 'sini de belirtebilirsiniz.

   !["Seyirci" özelliğinde hedef kaynak KIMLIĞINI belirtin](./media/create-managed-service-identity/specify-audience-url-target-resource.png)

   Azure depolama için Azure AD 'ye erişimi yetkilendirme hakkında daha fazla bilgi için şu konulara bakın:

   * [Azure Active Directory kullanarak Azure bloblarına ve kuyruklara erişim yetkisi verme](../storage/common/storage-auth-aad.md)
   * [Azure Active Directory ile Azure depolama 'ya erişim yetkisi verme](https://docs.microsoft.com/rest/api/storageservices/authorize-with-azure-active-directory#use-oauth-access-tokens-for-authentication)

<a name="remove-identity"></a>

## <a name="remove-system-assigned-identity"></a>Sistem tarafından atanan kimliği kaldır

Mantıksal uygulamanız için sistem tarafından atanan kimlik kullanımını durdurmak için şu seçeneklere sahipsiniz:

* [Azure portalda](#azure-portal-disable)
* [Azure Resource Manager şablonları](#template-disable)
* [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.resources/remove-azroleassignment)
* [Azure CLI](https://docs.microsoft.com/cli/azure/role/assignment?view=azure-cli-latest#az-role-assignment-delete)

Mantıksal uygulamanızı silerseniz Azure, yönetilen kimliği Azure AD 'den otomatik olarak kaldırır.

<a name="azure-portal-disable"></a>

### <a name="remove-system-assigned-identity-in-the-azure-portal"></a>Azure portal sistem tarafından atanan kimliği kaldırma

Azure portal, [mantıksal uygulamanızdan](#disable-identity-logic-app) sistem tarafından atanan kimliği ve kimliğin [hedef kaynağınızın](#disable-identity-target-resource)erişimini kaldırın.

<a name="disable-identity-logic-app"></a>

#### <a name="remove-system-assigned-identity-from-logic-app"></a>Mantıksal uygulamadan sistem tarafından atanan kimliği kaldırma

1. [Azure Portal](https://portal.azure.com)mantıksal uygulama tasarımcısında mantıksal uygulamanızı açın.

1. Mantıksal uygulama menüsünde, **Ayarlar**altında, **kimlik** > **sistem atandı**' ı seçin. **Durum**' un altında, **Tamam** >  > **Kaydet** **' i seçin** .

   ![Sistem tarafından atanan kimlik kullanımını durdur](./media/create-managed-service-identity/turn-off-system-assigned-identity.png)

<a name="disable-identity-target-resource"></a>

#### <a name="remove-identity-access-from-resources"></a>Kaynaklardan kimlik erişimini kaldırma

1. [Azure Portal](https://portal.azure.com), yönetilen bir kimlik için erişimi kaldırmak Istediğiniz hedef Azure kaynağına gidin.

1. Hedef kaynağın menüsünde, **erişim denetimi (IAM)** seçeneğini belirleyin. Araç çubuğu altında **rol atamaları**' nı seçin.

1. Roller listesinde, kaldırmak istediğiniz yönetilen kimlikleri seçin. Araç çubuğunda **Kaldır**' ı seçin.

   > [!TIP]
   > **Kaldır** seçeneği devre dışıysa, büyük olasılıkla izinlerinizin olması gerekmez. Kaynakları kaynaklar için yönetmenizi sağlayan izinler hakkında daha fazla bilgi için, [Azure Active Directory Içindeki yönetici rolü izinleri](../active-directory/users-groups-roles/directory-assign-admin-roles.md)bölümüne bakın.

Yönetilen kimlik artık kaldırılır ve artık hedef kaynağa erişemez.

<a name="template-disable"></a>

### <a name="disable-managed-identity-in-azure-resource-manager-template"></a>Azure Resource Manager şablonunda yönetilen kimliği devre dışı bırak

Mantıksal uygulamanın sistem tarafından yönetilen kimliğini bir Azure Resource Manager şablonu kullanarak etkinleştirdiyseniz, `identity` nesnesinin `type` alt özelliğini `None`olarak ayarlayın. Bu eylem Ayrıca, Azure AD 'den sistem tarafından yönetilen kimliğin asıl KIMLIĞINI de siler.

```json
"identity": {
   "type": "None"
}
```

## <a name="next-steps"></a>Sonraki adımlar

* [Azure Logic Apps 'da güvenli erişim ve veriler](../logic-apps/logic-apps-securing-a-logic-app.md)
