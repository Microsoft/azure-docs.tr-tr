---
title: Yönetim biriminde gruplar ekleme, kaldırma ve listeleme-Azure Active Directory | Microsoft Docs
description: Grupları ve rol izinlerini Azure Active Directory içindeki bir yönetim biriminde yönetin.
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: roles
ms.workload: identity
ms.date: 03/10/2021
ms.author: rolyon
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2ad8cce8375ecd670a481541a091e36aacb41240
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107505301"
---
# <a name="add-and-manage-groups-in-an-administrative-unit-in-azure-active-directory"></a>Azure Active Directory bir yönetim biriminde gruplar ekleme ve yönetme

Azure Active Directory (Azure AD) içinde, denetimin daha ayrıntılı yönetim kapsamı için yönetim birimine gruplar ekleyebilirsiniz.

Yönetim birimi yönetimi için PowerShell ve Microsoft Graph kullanmaya hazırlanmak için bkz. [kullanmaya başlayın](admin-units-manage.md#get-started).

## <a name="add-groups-to-an-administrative-unit"></a>Yönetim birimine gruplar ekleme

Azure portal, PowerShell veya Microsoft Graph kullanarak bir yönetim birimine gruplar ekleyebilirsiniz.

### <a name="use-the-azure-portal"></a>Azure portalını kullanma

Bir yönetim birimine yalnızca ayrı gruplar atayabilirsiniz. Grupları toplu işlem olarak atama seçeneği yoktur. Azure portal, yönetim birimine iki şekilde bir grup atayabilirsiniz:

* **Gruplar** bölmesinden:

  1. Azure portal **Azure AD**'ye gidin.
  1. **Gruplar**' ı seçin ve ardından yönetim birimine atamak istediğiniz grubu seçin. 
  1. Sol bölmede, grubun atandığı yönetim birimlerinin listesini göstermek için **yönetim birimleri** ' ni seçin. 

     !["Yönetim birimleri" bölmesindeki "yönetim birimine ata" bağlantısının ekran görüntüsü.](./media/admin-units-add-manage-groups/assign-to-group-1.png)

  1. **Yönetim birimine ata**' yı seçin.
  1. Sağ bölmede, yönetim birimini seçin.

* **Yönetim birimleri**  >  **tüm gruplar** bölmesinden:

  1. Azure portal **Azure AD**'ye gidin.
  
  1. Sol bölmede, **yönetim birimleri**' ni seçin ve ardından **tüm gruplar**' ı seçin. 
     Yönetim birimine zaten atanmış olan gruplar sağ bölmede görüntülenir. 

  1. **Gruplar** bölmesinde **Ekle**' yi seçin.
    Sağ bölmede Azure AD kuruluşunuzdaki tüm kullanılabilir gruplar listelenir. 

     ![Yönetim birimine Grup eklemek için "Ekle" düğmesinin ekran görüntüsü.](./media/admin-units-add-manage-groups/assign-to-admin-unit.png)

  1. Yönetim birimine atanacak bir veya daha fazla grup seçin ve ardından **Seç** düğmesini seçin.

### <a name="use-powershell"></a>PowerShell kullanma

Aşağıdaki örnekte, `Add-AzureADMSAdministrativeUnitMember` grubunu yönetim birimine eklemek için cmdlet 'ini kullanın. Yönetim biriminin nesne KIMLIĞI ve eklenecek grubun nesne KIMLIĞI bağımsız değişken olarak alınır. Vurgulanan bölümü, belirli ortamınız için gereken şekilde değiştirin.


```powershell
$adminUnitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
$GroupObj = Get-AzureADGroup -Filter "displayname eq 'TestGroup'"
Add-AzureADMSAdministrativeUnitMember -Id $adminUnitObj.Id -RefObjectId $GroupObj.ObjectId
```

### <a name="use-microsoft-graph"></a>Microsoft Graph kullan

Aşağıdaki komutları çalıştırın:

İstek

```http
POST /administrativeUnits/{admin-unit-id}/members/$ref
```

Gövde

```http
{
"@odata.id":"https://graph.microsoft.com/v1.0/groups/{group-id}"
}
```

Örnek

```http
{
"@odata.id":"https://graph.microsoft.com/v1.0/groups/871d21ab-6b4e-4d56-b257-ba27827628f3"
}
```

## <a name="view-a-list-of-groups-in-an-administrative-unit"></a>Yönetim birimindeki grupların listesini görüntüleme

### <a name="use-the-azure-portal"></a>Azure portalını kullanma

1. Azure portal **Azure AD**'ye gidin.

1. Sol bölmede, **yönetim birimleri**' ni seçin ve ardından gruplarını görüntülemek istediğiniz yönetim birimini seçin. Varsayılan olarak, **tüm kullanıcılar** sol bölmede seçilidir. 

1. Sol bölmede **gruplar**' ı seçin. Sağ bölmede, seçili yönetim biriminin üyesi olan grupların bir listesi görüntülenir.

   ![Yönetim birimindeki grupların listesini görüntüleyen "gruplar" bölmesinin ekran görüntüsü.](./media/admin-units-add-manage-groups/list-groups-in-admin-units.png)

### <a name="use-powershell"></a>PowerShell kullanma

Yönetim biriminin tüm üyelerinin listesini göstermek için aşağıdaki komutu çalıştırın: 

```powershell
$adminUnitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
Get-AzureADMSAdministrativeUnitMember -Id $adminUnitObj.Id
```

Yönetim biriminin üyesi olan tüm grupları göstermek için aşağıdaki kod parçacığını kullanın:

```powershell
foreach ($member in (Get-AzureADMSAdministrativeUnitMember -Id $adminUnitObj.Id)) 
{
if($member.ObjectType -eq "Group")
{
Get-AzureADGroup -ObjectId $member.ObjectId
}
}
```

### <a name="use-microsoft-graph"></a>Microsoft Graph kullan

Şu komutu çalıştırın:

İstek

```http
GET /directory/administrativeUnits/{admin-unit-id}/members/$/microsoft.graph.group
```

Gövde

```http
{}
```

## <a name="view-a-list-of-administrative-units-for-a-group"></a>Bir grup için yönetim birimlerinin bir listesini görüntüleme

### <a name="use-the-azure-portal"></a>Azure portalını kullanma

1. Azure portal **Azure AD**'ye gidin.

1. Sol bölmede, gruplar listesini göstermek için **gruplar** ' ı seçin.

1. Grubun profilini açmak için bir grup seçin. 

1. Sol bölmede, grubun üye olduğu tüm yönetim birimlerini listelemek için **yönetim birimleri** ' ni seçin.

   ![Bir grubun atandığı bir liste yönetim birimini görüntüleyen "yönetim birimleri" bölmesinin ekran görüntüsü.](./media/admin-units-add-manage-groups/list-group-au.png)

### <a name="use-powershell"></a>PowerShell kullanma

Şu komutu çalıştırın:

```powershell
Get-AzureADMSAdministrativeUnit | where { Get-AzureADMSAdministrativeUnitMember -ObjectId $_.ObjectId | where {$_.ObjectId -eq $groupObjId} }
```

### <a name="use-microsoft-graph"></a>Microsoft Graph kullan

Şu komutu çalıştırın:

```http
https://graph.microsoft.com/v1.0/groups/{group-id}/memberOf/$/Microsoft.Graph.AdministrativeUnit
```

## <a name="remove-a-group-from-an-administrative-unit"></a>Yönetim biriminden bir grubu kaldırma

### <a name="use-the-azure-portal"></a>Azure portalını kullanma

Bir grubu Azure portal bir yönetim biriminden iki şekilde kaldırabilirsiniz:

- Gruba genel bakış 'dan kaldırma:

  1. Azure portal **Azure AD**'ye gidin.
  1. Sol bölmede **gruplar**' ı seçin ve ardından Yönetim biriminden kaldırmak istediğiniz grubun profilini açın.
  1. Sol bölmede, grubun atandığı tüm yönetim birimlerini listelemek için **yönetim birimleri** ' ni seçin. 
  1. Grubu kaldırmak istediğiniz yönetim birimini seçin ve ardından **Yönetim biriminden kaldır**' ı seçin.

     ![Seçili yönetim birimine atanan grupların listesini görüntüleyen "yönetim birimleri" bölmesinin ekran görüntüsü.](./media/admin-units-add-manage-groups/group-au-remove.png)

- Yönetim biriminden kaldır:

  1. Azure portal **Azure AD**'ye gidin.
  1. Sol bölmede, **yönetim birimleri**' ni seçin ve ardından grubun atandığı yönetim birimini seçin.
  1. Sol bölmede, yönetim birimine atanan tüm grupları listelemek için **gruplar** ' ı seçin.
  1. Kaldırmak istediğiniz grubu seçin ve ardından **grupları kaldır**' ı seçin.

    ![Bir yönetim birimindeki grupların listesini görüntüleyen "gruplar" bölmesinin ekran görüntüsü.](./media/admin-units-add-manage-groups/list-groups-in-admin-units.png)

### <a name="use-powershell"></a>PowerShell kullanma

Şu komutu çalıştırın:

```powershell
Remove-AzureADMSAdministrativeUnitMember -ObjectId $adminUnitId -MemberId $memberGroupObjId
```

### <a name="use-microsoft-graph"></a>Microsoft Graph kullan

Şu komutu çalıştırın:

```http
https://graph.microsoft.com/v1.0/directory/administrativeUnits/{admin-unit-id}/members/{group-id}/$ref
```

## <a name="next-steps"></a>Sonraki adımlar

- [Yönetim birimine rol atama](admin-units-assign-roles.md)
- [Yönetici birimindeki kullanıcıları yönetme](admin-units-add-manage-users.md)
