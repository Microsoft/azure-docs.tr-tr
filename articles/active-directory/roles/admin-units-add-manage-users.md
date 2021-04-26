---
title: Yönetici birimine Kullanıcı ekleme, kaldırma ve listeleme-Azure Active Directory | Microsoft Docs
description: Azure Active Directory bir yönetim biriminde kullanıcıları ve rol izinlerini yönetme
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: roles
ms.workload: identity
ms.date: 11/04/2020
ms.author: rolyon
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 101f1452f547f195288e2d22bc516880100c7735
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107496835"
---
# <a name="add-and-manage-users-in-an-administrative-unit-in-azure-active-directory"></a>Azure Active Directory bir yönetim biriminde Kullanıcı ekleme ve yönetme

Azure Active Directory (Azure AD) içinde, daha ayrıntılı bir yönetim kapsamı denetimi için kullanıcıları bir yönetim birimine ekleyebilirsiniz.

Yönetim birimi yönetimi için PowerShell ve Microsoft Graph kullanmaya hazırlanmak için bkz. [kullanmaya başlayın](admin-units-manage.md#get-started).

## <a name="add-users-to-an-administrative-unit"></a>Yönetici birimine Kullanıcı ekleme

### <a name="use-the-azure-portal"></a>Azure portalını kullanma

Kullanıcıları, yönetim birimlerine ayrı ayrı veya toplu bir işlem olarak atayabilirsiniz.

- Kullanıcı profilinden bireysel kullanıcı atama:

   1. [Azure AD Yönetim merkezinde](https://portal.azure.com) ayrıcalıklı rol yöneticisi izinleriyle oturum açın.

   1. **Kullanıcıları** seçin ve ardından kullanıcının profilini açmak için bir yönetim birimine atanacak kullanıcıyı seçin.
   
   1. **Yönetim birimlerini** seçin. 
   
   1. Kullanıcıyı bir veya daha fazla yönetim birimine atamak için, **yönetim birimine ata** ' yı seçin ve sağ bölmede, kullanıcıyı atamak istediğiniz yönetim birimlerini seçin.

       ![Bir kullanıcıyı yönetim birimine atamaya yönelik "yönetim birimleri" bölmesinin ekran görüntüsü.](./media/admin-units-add-manage-users/assign-users-individually.png)

- Bir yönetim biriminden bireysel kullanıcı atama:

   1. [Azure AD Yönetim merkezinde](https://portal.azure.com) ayrıcalıklı rol yöneticisi izinleriyle oturum açın.
   1. **Yönetim birimleri**' ni seçin ve ardından kullanıcının atanacağı yönetim birimini seçin.
   1. **Tüm kullanıcılar**' ı seçin, **üye Ekle** ' yi seçin ve ardından **üye Ekle** bölmesinde, yönetim birimine atamak istediğiniz bir veya daha fazla kullanıcı seçin.

        ![Yönetici birimine Kullanıcı atamak için yönetim birimi "kullanıcılar" bölmesinin ekran görüntüsü.](./media/admin-units-add-manage-users/assign-to-admin-unit.png)

- Kullanıcıları toplu işlem olarak atama:

   1. [Azure AD Yönetim merkezinde](https://portal.azure.com) ayrıcalıklı rol yöneticisi izinleriyle oturum açın.

   1. **Yönetim birimlerini** seçin.

   1. Kullanıcıları eklemek istediğiniz yönetim birimini seçin.

   1. **Kullanıcılar**  >  **toplu etkinlikleri**  >  **toplu ekleme üyeleri**' ni seçin. Daha sonra, virgülle ayrılmış değerler (CSV) şablonunu indirebilir ve dosyayı düzenleyebilirsiniz. Biçim basittir ve her satıra tek bir Kullanıcı asıl adının eklenmesi gerekir. Dosya hazırlandıktan sonra uygun bir konuma kaydedin ve bu adımın bir parçası olarak karşıya yükleyin.

      ![Bir yönetim birimine toplu işlem olarak Kullanıcı atamaya yönelik "kullanıcılar" bölmesinin ekran görüntüsü.](./media/admin-units-add-manage-users/bulk-assign-to-admin-unit.png)

### <a name="use-powershell"></a>PowerShell kullanma

PowerShell 'de, `Add-AzureADAdministrativeUnitMember` kullanıcıyı yönetim birimine eklemek için aşağıdaki örnekteki cmdlet 'ini kullanın. Kullanıcı eklemek istediğiniz yönetim biriminin nesne KIMLIĞI ve eklemek istediğiniz kullanıcının nesne KIMLIĞI bağımsız değişken olarak alınır. Vurgulanan bölümü, belirli ortamınız için gereken şekilde değiştirin.

```powershell
$adminUnitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
$userObj = Get-AzureADUser -Filter "UserPrincipalName eq 'bill@example.onmicrosoft.com'"
Add-AzureADMSAdministrativeUnitMember -Id $adminUnitObj.Id -RefObjectId $userObj.ObjectId
```


### <a name="use-microsoft-graph"></a>Microsoft Graph kullan

Yer tutucusunu test bilgileriyle değiştirin ve aşağıdaki komutu çalıştırın:

İstek

```http
POST /administrativeUnits/{admin-unit-id}/members/$ref
```

Gövde

```http
{
  "@odata.id":"https://graph.microsoft.com/v1.0/users/{user-id}"
}
```

Örnek

```http
{
  "@odata.id":"https://graph.microsoft.com/v1.0/users/john@example.com"
}
```

## <a name="view-a-list-of-administrative-units-for-a-user"></a>Bir kullanıcı için yönetim birimlerinin bir listesini görüntüleme

### <a name="use-the-azure-portal"></a>Azure portalını kullanma

Azure portal, aşağıdakileri yaparak bir kullanıcının profilini açabilirsiniz:

1. **Azure AD**'ye gidin ve ardından **Kullanıcılar**' ı seçin.

1. Profilini görüntülemek istediğiniz kullanıcıyı seçin.

1. Kullanıcının atandığı yönetim birimlerinin listesini göstermek için **yönetim birimleri** ' ni seçin.

   ![Bir kullanıcının atandığı yönetim birimlerinin ekran görüntüsü.](./media/admin-units-add-manage-users/list-user-admin-units.png)

### <a name="use-powershell"></a>PowerShell kullanma

Şu komutu çalıştırın:

```powershell
Get-AzureADMSAdministrativeUnit | where { Get-AzureADMSAdministrativeUnitMember -Id $_.ObjectId | where {$_.RefObjectId -eq $userObjId} }
```

> [!NOTE]
> Varsayılan olarak, `Get-AzureADAdministrativeUnitMember` bir yönetim biriminin yalnızca 100 üyesini döndürür. Daha fazla üye almak için ekleyebilirsiniz `"-All $true"` .

### <a name="use-microsoft-graph"></a>Microsoft Graph kullan

Yer tutucusunu test bilgileriyle değiştirin ve aşağıdaki komutu çalıştırın:

```http
https://graph.microsoft.com/v1.0/users/{user-id}/memberOf/$/Microsoft.Graph.AdministrativeUnit
```

## <a name="remove-a-single-user-from-an-administrative-unit"></a>Yönetim biriminden tek bir kullanıcıyı kaldırma

### <a name="use-the-azure-portal"></a>Azure portalını kullanma

Bir kullanıcıyı, yönetim biriminden iki şekilde kaldırabilirsiniz: 

* Azure portal **Azure AD**' a gidin ve ardından **Kullanıcılar**' ı seçin. 
  1. Kullanıcının profilini açmak için kullanıcıyı seçin. 
  1. Kullanıcıyı kaldırmak istediğiniz yönetim birimini seçin ve ardından **Yönetim biriminden kaldır**' ı seçin.

     ![Kullanıcının profil bölmesinden bir yönetim biriminden bir kullanıcının nasıl kaldırılacağını gösteren ekran görüntüsü.](./media/admin-units-add-manage-users/user-remove-admin-units.png)

* Azure portal **Azure AD**' a gidin ve ardından **yönetim birimleri**' ni seçin.
  1. Kullanıcıyı kaldırmak istediğiniz yönetim birimini seçin. 
  1. Kullanıcıyı seçin ve ardından **üyeyi kaldır**' ı seçin.
  
     ![Yönetim birimi düzeyinde bir kullanıcının nasıl kaldırılacağını gösteren ekran görüntüsü.](./media/admin-units-add-manage-users/admin-units-remove-user.png)

### <a name="use-powershell"></a>PowerShell kullanma

Şu komutu çalıştırın:

```powershell
Remove-AzureADMSAdministrativeUnitMember -Id $adminUnitId -MemberId $memberUserObjId
```

### <a name="use-microsoft-graph"></a>Microsoft Graph kullan

Yer tutucuları test bilgileriyle değiştirin ve aşağıdaki komutu çalıştırın:

```http
https://graph.microsoft.com/v1.0/directory/administrativeUnits/{admin-unit-id}/members/{user-id}/$ref
```

## <a name="remove-multiple-users-as-a-bulk-operation"></a>Birden çok kullanıcıyı toplu işlem olarak kaldır

Bir yönetim biriminden birden çok kullanıcıyı kaldırmak için aşağıdakileri yapın:

1. Azure portal **Azure AD**'ye gidin.

1. **Yönetim birimleri**' ni seçin ve ardından kullanıcıları kaldırmak istediğiniz yönetim birimini seçin. 

1. **Üyeleri toplu kaldır**' ı seçin ve ardından kaldırmak istediğiniz kullanıcıları listelemek IÇIN kullanacağınız CSV şablonunu indirin.

   !["Kullanıcılar" bölmesinde "üyeleri toplu kaldırma" bağlantısını gösteren ekran görüntüsü.](./media/admin-units-add-manage-users/bulk-user-remove.png)

1. İndirilen CSV şablonunu ilgili Kullanıcı girişleriyle düzenleyin. Şablonun ilk iki satırını kaldırmayın. Her satıra bir Kullanıcı asıl adı (UPN) ekleyin.

   ![Bir yönetim biriminden toplu olarak kullanıcıları kaldırmak için düzenlenmiş bir CSV dosyasının ekran görüntüsü.](./media/admin-units-add-manage-users/bulk-user-entries.png)

1. Değişikliklerinizi kaydedin, dosyayı karşıya yükleyin ve ardından **Gönder**' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

- [Yönetim birimine rol atama](admin-units-assign-roles.md)
- [Yönetim birimine gruplar ekleme](admin-units-add-manage-groups.md)
