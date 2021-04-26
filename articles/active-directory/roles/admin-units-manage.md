---
title: Yönetim birimleri ekleme ve kaldırma-Azure Active Directory | Microsoft Docs
description: Azure Active Directory içindeki rol izinlerinin kapsamını kısıtlamak için yönetim birimlerini kullanın.
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
ms.openlocfilehash: 6fad9356d3379e76aa259d67711d18f14a4e266f
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107505284"
---
# <a name="manage-administrative-units-in-azure-active-directory"></a>Azure Active Directory Yönetim birimlerini yönetme

Azure Active Directory (Azure AD) ' da daha ayrıntılı yönetim denetimi için, bir veya daha fazla yönetim birimiyle sınırlı bir kapsama sahip bir Azure AD rolüne kullanıcı atayabilirsiniz.

## <a name="get-started"></a>başlarken

1. [Grafik Gezgini](https://aka.ms/ge)aracılığıyla aşağıdaki yönergelerden sorguları çalıştırmak için aşağıdakileri yapın:

    a. Azure portal Azure AD 'ye gidin. 
    
    b. Uygulamalar listesinde, **Graph Explorer**' ı seçin.
    
    c. **İzinler** bölmesinde, **Graph Explorer Için yönetici onayı ver**' i seçin.

    !["Graph Explorer için yönetici onayı verme" bağlantısını gösteren ekran görüntüsü.](./media/admin-units-manage/select-graph-explorer.png)


1. [Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureAD/)'i kullanın.

## <a name="add-an-administrative-unit"></a>Yönetim birimi ekleme

Azure portal veya PowerShell 'i kullanarak bir yönetim birimi ekleyebilirsiniz.

### <a name="use-the-azure-portal"></a>Azure portalını kullanma

1. Azure portal Azure AD 'ye gidin. Ardından, sol bölmede **yönetim birimleri**' ni seçin.

    ![Azure AD 'de "yönetim birimleri" bağlantısının ekran görüntüsü.](./media/admin-units-manage/nav-to-admin-units.png)

1. Bölmenin üst kısmındaki **Ekle** düğmesini seçin ve ardından **ad** kutusuna, yönetim biriminin adını girin. İsteğe bağlı olarak, yönetim biriminin bir açıklamasını ekleyin.

    ![Yönetim biriminin adını girmek için Ekle düğmesini ve ad kutusunu gösteren ekran görüntüsü.](./media/admin-units-manage/add-new-admin-unit.png)

1. Yönetim birimini sonlandırmak için mavi **Ekle** düğmesini seçin.

### <a name="use-powershell"></a>PowerShell kullanma

Aşağıdaki komutları çalıştırmayı denemeden önce [Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureAD/) 'i yükleyebilirsiniz:

```powershell
Connect-AzureAD
New-AzureADMSAdministrativeUnit -Description "West Coast region" -DisplayName "West Coast"
```

Gerektiğinde, tırnak işaretleri içine alınmış değerleri değiştirebilirsiniz.

### <a name="use-microsoft-graph"></a>Microsoft Graph kullan

İstek

```http
POST /administrativeUnits
```

Gövde

```http
{
  "displayName": "North America Operations",
  "description": "North America Operations administration"
}
```

## <a name="remove-an-administrative-unit"></a>Yönetim birimini kaldırma

Azure AD 'de, artık yönetici rolleri için bir kapsam birimi olarak ihtiyacınız olmayan bir yönetim birimini kaldırabilirsiniz.

### <a name="use-the-azure-portal"></a>Azure portalını kullanma

1. Azure portal **Azure AD**' a gidin ve ardından **yönetim birimleri**' ni seçin. 
1. Silinecek yönetim birimini seçin ve **Sil**' i seçin. 
1. Yönetim birimini silmek istediğinizi onaylamak için **Evet**' i seçin. Yönetim birimi silinir.

![Yönetim birimi silme düğmesi ve onay penceresinin ekran görüntüsü.](./media/admin-units-manage/select-admin-unit-to-delete.png)

### <a name="use-powershell"></a>PowerShell kullanma

```powershell
$adminUnitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'DeleteMe Admin Unit'"
Remove-AzureADMSAdministrativeUnit -Id $adminUnitObj.Id
```

Belirli bir ortam için gereken şekilde, tırnak işaretleri içine alınmış değerleri değiştirebilirsiniz.

### <a name="use-the-graph-api"></a>Graph API’sini kullanma

İstek

```http
DELETE /administrativeUnits/{admin-unit-id}
```

Gövde

```http
{}
```

## <a name="next-steps"></a>Sonraki adımlar

* [Yönetici birimindeki kullanıcıları yönetme](admin-units-add-manage-users.md)
* [Yönetim birimindeki grupları yönetme](admin-units-add-manage-groups.md)
