---
title: Azure Service Fabric-Kullanıcı tarafından atanan yönetilen kimlik ile uygulama dağıtma | Microsoft Docs
description: Bu makalede, Kullanıcı tarafından atanan yönetilen kimlik ile Service Fabric uygulamasının nasıl dağıtılacağı gösterilmektedir
services: service-fabric
author: athinanthny
ms.service: service-fabric
ms.topic: article
ms.date: 08/09/2019
ms.author: atsenthi
ms.openlocfilehash: 0cc1e51a4d5f9ad54866066a4247e1588da381a6
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71037497"
---
# <a name="deploy-service-fabric-application-with-a-user-assigned-managed-identity-preview"></a>Kullanıcı tarafından atanan yönetilen kimlikle Service Fabric uygulaması dağıtma (Önizleme)

Yönetilen kimliğe sahip bir Service Fabric uygulamasını dağıtmak için, uygulamanın, genellikle bir Azure Resource Manager şablonuyla Azure Resource Manager ile dağıtılması gerekir. Service Fabric uygulamasının Azure Resource Manager aracılığıyla nasıl dağıtılacağı hakkında daha fazla bilgi için bkz. [Uygulamaları ve hizmetleri Azure Resource Manager kaynakları olarak yönetme](service-fabric-application-arm-resource.md).

> [!NOTE] 
> 
> Azure kaynağı olarak dağıtılmayan uygulamalarda Yönetilen kimlikler **olamaz** . 
>
> Yönetilen kimlik ile uygulama dağıtımı Service Fabric API sürümü `"2019-06-01-preview"`ile desteklenir. Uygulama türü, uygulama türü sürümü ve hizmet kaynakları için aynı API sürümünü de kullanabilirsiniz.
>

## <a name="user-assigned-identity"></a>Kullanıcı tarafından atanan kimlik

Uygulamayı kullanıcı tarafından atanan kimlikle etkinleştirmek için önce **Identity** özelliğini **userassigned** türü ve başvurulan kullanıcı tarafından atanan kimliklerle birlikte uygulama kaynağına ekleyin. Ardından, Kullanıcı tarafından atanan kimliklerin her biri için bir kolay ad ile PrincipalId eşleme bir listesini içeren **uygulama** kaynağı için **Özellikler** bölümünün Içine bir **managedıdentities** bölümü ekleyin. Kullanıcı tarafından atanan kimlikler hakkında daha fazla bilgi için bkz. [Kullanıcı tarafından atanan yönetilen kimlik oluşturma, listeleme veya silme](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell).

### <a name="application-template"></a>Uygulama şablonu

Kullanıcı tarafından atanan kimlikle uygulamayı etkinleştirmek için, önce **kimlik** özelliğini **userassigned** ve başvurulan kullanıcı tarafından atanan kimliklerle birlikte uygulama kaynağına ekleyin ve ardındanKullanıcı tarafından atanan kimliklerin her biri Için PrincipalId eşlemesine kolay ad listesi içeren Özellikler bölümü.

    {
      "apiVersion": "2019-06-01-preview",
      "type": "Microsoft.ServiceFabric/clusters/applications",
      "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applicationTypes/', parameters('applicationTypeName'), '/versions/', parameters('applicationTypeVersion'))]",
        "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName'))]"
      ],
      "identity": {
        "type" : "userAssigned",
        "userAssignedIdentities": {
            "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName'))]": {}
        }
      },
      "properties": {
        "typeName": "[parameters('applicationTypeName')]",
        "typeVersion": "[parameters('applicationTypeVersion')]",
        "parameters": {
        },
        "managedIdentities": [
          {
            "name" : "[parameters('userAssignedIdentityName')]",
            "principalId" : "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName')), '2018-11-30').principalId]"
          }
        ]
      }
    }

Yukarıdaki örnekte, Kullanıcı tarafından atanan kimliğin kaynak adı, uygulama için yönetilen kimliğin kolay adı olarak kullanılır. Aşağıdaki örneklerde gerçek kolay ad "AdminUser" olduğu varsayılır.

### <a name="application-package"></a>Uygulama paketi

1. Azure Resource Manager şablonundaki `managedIdentities` bölümünde tanımlanan her bir kimlik için, **sorumlular** bölümünde uygulama bildiriminde `<ManagedIdentity>` bir etiket ekleyin. Özniteliğin, bölümünde`managedIdentities` tanımlanan `name`özellik `Name` ile eşleşmesi gerekir.

    **ApplicationManifest. xml**

    ```xml
      <Principals>
        <ManagedIdentities>
          <ManagedIdentity Name="AdminUser" />
        </ManagedIdentities>
      </Principals>
    ```

2. **Servicemanifestımport** bölümünde, yönetilen kimliği kullanan hizmet Için bir **ıdentitybindingpolicy** ekleyin. Bu ilke, `AdminUser` kimliği daha sonra hizmet bildirimine eklenmesi gereken hizmete özel bir kimlik adıyla eşler.

    **ApplicationManifest. xml**

    ```xml
      <ServiceManifestImport>
        <Policies>
          <IdentityBindingPolicy ServiceIdentityRef="WebAdmin" ApplicationIdentityRef="AdminUser" />
        </Policies>
      </ServiceManifestImport>
    ```

3. **Kaynak** bölümünün içine `ServiceIdentityRef` ,uygulamabildirimininiçindekiileeşleşenadıtaşıyanbirmanagedıdentityeklemekiçinhizmetbildiriminigüncelleştirin:`IdentityBindingPolicy`

    **ServiceManifest. xml**

    ```xml
      <Resources>
        ...
        <ManagedIdentities DefaultIdentity="WebAdmin">
          <ManagedIdentity Name="WebAdmin" />
        </ManagedIdentities>
      </Resources>
    ```

## <a name="next-steps"></a>Sonraki adımlar

* [Service Fabric uygulama kodunda yönetilen kimliği kullanma](how-to-managed-identity-service-fabric-app-code.md)
* [Diğer Azure kaynaklarına Service Fabric uygulama erişimi verme](how-to-grant-access-other-resources.md)
