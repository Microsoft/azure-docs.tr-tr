---
title: Rol atamaları oluşturma ve yönetme-Azure dijital TWINS | Microsoft Docs
description: Azure dijital TWINS 'te rol atamaları oluşturma ve yönetme hakkında bilgi edinin.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/02/2019
ms.custom: seodec18
ms.openlocfilehash: 45ce22f208ee31b7202705eb4e42c38bedf09a8b
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74013989"
---
# <a name="create-and-manage-role-assignments-in-azure-digital-twins"></a>Azure dijital TWINS 'de rol atamaları oluşturma ve yönetme

Azure dijital TWINS, kaynaklara erişimi yönetmek için rol tabanlı erişim denetimi ([RBAC](./security-role-based-access-control.md)) kullanır.

## <a name="role-assignments-overview"></a>Rol atamalarına genel bakış

Her rol ataması aşağıdaki tanıma uyar:

```JSON
{
  "roleId": "00e00ad7-00d4-4007-853b-b9968ad000d1",
  "objectId": "be2c6daa-a3a0-0c0a-b0da-c000000fbc5f",
  "objectIdType": "ServicePrincipalId",
  "path": "/",
  "tenantId": "00f000bf-86f1-00aa-91ab-2d7cd000db47"
}
```

Aşağıdaki tabloda her bir öznitelik açıklanmaktadır:

| Öznitelik | Ad | Gerekli | Tür | Açıklama |
| --- | --- | --- | --- | --- |
| RoleID | Rol tanımı tanımlayıcısı | Yes | Dize | İstenen rol atamasının benzersiz KIMLIĞI. Aşağıdaki sistem API 'sini veya İnceleme tablosunu sorgulayarak rol tanımlarını ve bunların tanımlayıcılarını bulun. |
| objectId | Nesne tanımlayıcısı | Yes | Dize | Azure Active Directory KIMLIĞI, hizmet sorumlusu nesne KIMLIĞI veya etki alanı adı. Rol atamasının atandığı durum. Rol ataması, ilişkili türüne göre biçimlendirilmelidir. `DomainName` Objectıdtype için ObjectID `“@”` karakteriyle başlamalıdır. |
| Objectıdtype | Nesne tanımlayıcı türü | Yes | Dize | Kullanılan nesne tanımlayıcısı türü. Aşağıdaki **desteklenen Objectıdtypes** bölümüne bakın. |
| yol | Boşluk yolu | Yes | Dize | `Space` nesnesine yönelik tam erişim yolu. `/{Guid}/{Guid}` bunun bir örneğidir. Bir tanımlayıcının tüm grafik için rol ataması gerekiyorsa, `"/"`belirtin. Bu karakter kökü belirler, ancak kullanımı önerilmez. Her zaman en az ayrıcalık Ilkesini izleyin. |
| tenantId | Kiracı tanımlayıcısı | Değişir | Dize | Çoğu durumda, bir Azure Active Directory kiracı KIMLIĞI. `DeviceId` ve `TenantId` Objectıdtypes için izin verilmedi. `UserId` ve `ServicePrincipalId` Objectıdtypes için gereklidir. DomainName Objectıdtype için isteğe bağlı. |

### <a name="supported-role-definition-identifiers"></a>Desteklenen rol tanımı tanımlayıcıları

Her rol ataması, bir rol tanımını Azure dijital TWINS ortamınızdaki bir varlıkla ilişkilendirir.

[!INCLUDE [digital-twins-roles](../../includes/digital-twins-roles.md)]

### <a name="supported-object-identifier-types"></a>Desteklenen nesne tanımlayıcı türleri

Daha önce **Objectıdtype** özniteliği tanıtılmıştı.

[!INCLUDE [digital-twins-object-types](../../includes/digital-twins-object-id-types.md)]

## <a name="role-assignment-operations"></a>Rol atama işlemleri

Azure Digital TWINS, rol atamaları için tam *oluşturma*, *okuma*ve *silme* işlemlerini destekler. *Güncelleştirme* işlemleri, rol atamaları eklenerek, rol atamaları kaldırılarak veya rol atamalarının erişim Izni veren [uzamsal zeka grafik](./concepts-objectmodel-spatialgraph.md) düğümlerinin değiştirilerek işlenir.

[![rol atama uç noktaları](media/security-roles/role-assignments.png)](media/security-roles/role-assignments.png#lightbox)

Sağlanan Swagger başvuru belgeleri, kullanılabilir tüm API uç noktaları, istek işlemleri ve tanımlar hakkında daha fazla bilgi içerir.

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

### <a name="grant-permissions-to-your-service-principal"></a>Hizmet sorumlusuna izin verme

Hizmet sorumlusuna izin verilmesi, genellikle Azure dijital TWINS ile çalışırken yapmanız gereken ilk adımlardan biridir. Şunları gerektirir:

1. Azure [CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) veya [PowerShell](https://docs.microsoft.com/powershell/azure/)aracılığıyla Azure örneğiniz üzerinde oturum açma.
1. Hizmet sorumlusu bilgileriniz alınıyor.
1. Hizmet sorumlusuna istenen rolü atama.

Uygulama KIMLIĞINIZ Azure Active Directory ' de size sağlanır. Active Directory ' de bir Azure dijital TWINS yapılandırma ve sağlama hakkında daha fazla bilgi edinmek için [hızlı](./quickstart-view-occupancy-dotnet.md)başlangıcı okuyun.

Uygulama KIMLIĞI ' ne sahip olduktan sonra aşağıdaki komutlardan birini yürütün. Azure CLı 'de:

```azurecli
az login
az ad sp show --id <ApplicationId>
```

PowerShell 'de:

```powershell
Login-AzAccount
Get-AzADServicePrincipal -ApplicationId <ApplicationId>
```

**Yönetici** rolüne sahip bir Kullanıcı, URL 'ye kimliği DOĞRULANMıŞ BIR http post Isteği yaparak alan yöneticisi rolünü bir kullanıcıya atayabilir:

```plaintext
YOUR_MANAGEMENT_API_URL/roleassignments
```

Aşağıdaki JSON gövdesiyle:

```JSON
{
  "roleId": "98e44ad7-28d4-4007-853b-b9968ad132d1",
  "objectId": "YOUR_SERVICE_PRINCIPLE_OBJECT_ID",
  "objectIdType": "ServicePrincipalId",
  "path": "YOUR_PATH",
  "tenantId": "YOUR_TENANT_ID"
}
```

### <a name="retrieve-all-roles"></a>Tüm rolleri al

[![sistem rolleri](media/security-roles/system-api.png)](media/security-roles/system-api.png#lightbox)

Tüm kullanılabilir rolleri (rol tanımları) listelemek için, kimliği doğrulanmış bir HTTP GET isteği oluşturun:

```plaintext
YOUR_MANAGEMENT_API_URL/system/roles
```

Başarılı bir istek, atanabilecek her bir rol için girdilerle bir JSON dizisi döndürür:

```JSON
[
    {
        "id": "3cdfde07-bc16-40d9-bed3-66d49a8f52ae",
        "name": "DeviceAdministrator",
        "permissions": [
            {
                "notActions": [],
                "actions": [
                    "Read",
                    "Create",
                    "Update",
                    "Delete"
                ],
                "condition": "@Resource.Type Any_of {'Device', 'DeviceBlobMetadata', 'DeviceExtendedProperty', 'Sensor', 'SensorBlobMetadata', 'SensorExtendedProperty'} || ( @Resource.Type == 'ExtendedType' && (!Exists @Resource.Category || @Resource.Category Any_of { 'DeviceSubtype', 'DeviceType', 'DeviceBlobType', 'DeviceBlobSubtype', 'SensorBlobSubtype', 'SensorBlobType', 'SensorDataSubtype', 'SensorDataType', 'SensorDataUnitType', 'SensorPortType', 'SensorType' } ) )"
            },
            {
                "notActions": [],
                "actions": [
                    "Read"
                ],
                "condition": "@Resource.Type == 'Space' && @Resource.Category == 'WithoutSpecifiedRbacResourceTypes' || @Resource.Type Any_of {'ExtendedPropertyKey', 'SpaceExtendedProperty', 'SpaceBlobMetadata', 'SpaceResource', 'Matcher'}"
            }
        ],
        "accessControlPath": "/system",
        "friendlyPath": "/system",
        "accessControlType": "System"
    }
]
```

### <a name="check-a-specific-role-assignment"></a>Belirli bir rol atamasını denetleme

Belirli bir rol atamasını denetlemek için, kimliği doğrulanmış bir HTTP GET isteği oluşturun:

```plaintext
YOUR_MANAGEMENT_API_URL/roleassignments/check?userId=YOUR_USER_ID&path=YOUR_PATH&accessType=YOUR_ACCESS_TYPE&resourceType=YOUR_RESOURCE_TYPE
```

| **Parametre değeri** | **Gerekli** |  **Tür** |  **Açıklama** |
| --- | --- | --- | --- |
| YOUR_USER_ID |  True | Dize |   UserID Objectıdtype için ObjectID. |
| YOUR_PATH | True | Dize |   Erişimi denetlemek için seçilen yol. |
| YOUR_ACCESS_TYPE |  True | Dize |   *Okuma*, *oluşturma*, *güncelleştirme*veya *silme* |
| YOUR_RESOURCE_TYPE | True | Dize |  *Cihaz*, *deviceblobmetadata*, *deviceextendedproperty*, *extendedpropertykey*, *ExtendedType*, *uç nokta*, *keystore*, *Matcher*, *ontology*, *Report*, *roledefinition*, *algılayıcı*, *sensorextendedproperty*, *Space*, *spaceblobmetadata*, *spaceextendedproperty*, *SpaceResource*, *spaceroleatama*, *sistem*,  *UerDefinedFunction*, *User*, *Userblobmetadata*veya *userextendedproperty* |

Başarılı bir istek, belirtilen yol ve kaynak için erişim türünün kullanıcıya atanıp atanmadığını belirten bir Boole `true` veya `false` döndürür.

### <a name="get-role-assignments-by-path"></a>Yola göre rol atamaları al

Bir yolun tüm rol atamalarını almak için, kimliği doğrulanmış bir HTTP GET isteği oluşturun:

```plaintext
YOUR_MANAGEMENT_API_URL/roleassignments?path=YOUR_PATH
```

| Değer | Şununla değiştir |
| --- | --- |
| YOUR_PATH | Alanın tam yolu |

Başarılı bir istek, seçili **yol** parametresiyle ilişkili her bir rol atamasının bulunduğu bir JSON dizisi döndürür:

```JSON
[
    {
        "id": "0000c484-698e-46fd-a3fd-c12aa11e53a1",
        "roleId": "98e44ad7-28d4-4007-853b-b9968ad132d1",
        "objectId": "0de38846-1aa5-000c-a46d-ea3d8ca8ee5e",
        "objectIdType": "UserId",
        "path": "/"
    }
]
```

### <a name="revoke-a-permission"></a>Bir izni iptal etme

Bir alıcıdan bir izni iptal etmek için, kimliği doğrulanmış bir HTTP SILME isteği yaparak rol atamasını silin:

```plaintext
YOUR_MANAGEMENT_API_URL/roleassignments/YOUR_ROLE_ASSIGNMENT_ID
```

| Parametre | Şununla değiştir |
| --- | --- |
| *YOUR_ROLE_ASSIGNMENT_ID* | Kaldırılacak rol atamasının **kimliği** |

Başarılı bir SILME isteği, 204 yanıt durumu döndürür. Rol atamasının hala saklanıp saklanmadığını [denetleyerek](#check-a-specific-role-assignment) rol atamasının kaldırılmasını doğrulayın.

### <a name="create-a-role-assignment"></a>Rol ataması oluşturma

Rol ataması oluşturmak için, URL 'ye kimliği doğrulanmış bir HTTP POST isteği oluşturun:

```plaintext
YOUR_MANAGEMENT_API_URL/roleassignments
```

JSON gövdesinin aşağıdaki şemaya uygun olduğunu doğrulayın:

```JSON
{
  "roleId": "YOUR_ROLE_ID",
  "objectId": "YOUR_OBJECT_ID",
  "objectIdType": "YOUR_OBJECT_ID_TYPE",
  "path": "YOUR_PATH",
  "tenantId": "YOUR_TENANT_ID"
}
```

Başarılı bir istek, yeni oluşturulan rol atamasının **kimliğiyle** birlikte 201 yanıt durumu döndürür:

```JSON
"d92c7823-6e65-41d4-aaaa-f5b32e3f01b9"
```

## <a name="configuration-examples"></a>Yapılandırma örnekleri

Aşağıdaki örneklerde, yaygın olarak karşılaşılan birkaç rol atama senaryosunda JSON gövdesinin nasıl yapılandırılacağı gösterilmektedir.

* **Örnek**: bir kullanıcının kiracı alanının katında yönetici erişimi olması gerekir.

   ```JSON
   {
    "roleId": "98e44ad7-28d4-4007-853b-b9968ad132d1",
    "objectId" : " 0fc863aa-eb51-4704-a312-7d635d70e000",
    "objectIdType" : "UserId",
    "tenantId": " a0c20ae6-e830-4c60-993d-a00ce6032724",
    "path": "/ 000e349c-c0ea-43d4-93cf-6b00abd23a44/ d84e82e6-84d5-45a4-bd9d-006a000e3bab"
   }
   ```

* **Örnek**: bir uygulama, test senaryolarını bir cihaz ve algılayıcı çalıştırır.

   ```JSON
   {
    "roleId": "98e44ad7-28d4-0007-853b-b9968ad132d1",
    "objectId" : "cabf7aaa-af0b-41c5-000a-ce2f4c20000b",
    "objectIdType" : "ServicePrincipalId",
    "tenantId": " a0c20ae6-e000-4c60-993d-a91ce6000724",
    "path": "/"
   }
    ```

* **Örnek**: bir etki alanının parçası olan tüm kullanıcılar boşluklar, sensörler ve kullanıcılar için okuma erişimi alır. Bu erişim, ilgili ilgili nesnelerini içerir.

   ```JSON
   {
    "roleId": " b1ffdb77-c635-4e7e-ad25-948237d85b30",
    "objectId" : "@microsoft.com",
    "objectIdType" : "DomainName",
    "path": "/000e349c-c0ea-43d4-93cf-6b00abd23a00"
   }
   ```

## <a name="next-steps"></a>Sonraki adımlar

- Azure dijital TWINS rol tabanlı erişim denetimini gözden geçirmek için, [rol-temel erişim denetimi](./security-authenticating-apis.md)makalesini okuyun.

- Azure dijital TWINS API kimlik doğrulaması hakkında bilgi edinmek için [API kimlik doğrulamasını](./security-authenticating-apis.md)okuyun.
