---
title: Hizmet sorumlusunu Azure Analysis Services Yönetici rolüne Ekle | Microsoft Docs
description: Azure Analysis Services Server yönetici rolüne bir Automation hizmet sorumlusu eklemeyi öğrenin
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 0a3a86283c8ec9876fbec049a2a1a110eb1a80f3
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73573612"
---
# <a name="add-a-service-principal-to-the-server-administrator-role"></a>Sunucu Yöneticisi rolüne hizmet sorumlusu ekleme 

 Katılımsız PowerShell görevlerini otomatikleştirmek için bir hizmet sorumlusu, yönetilmekte olan Analysis Services sunucuda **Sunucu Yöneticisi** ayrıcalıklarına sahip olmalıdır. Bu makalede, bir Azure AS Server 'daki sunucu yöneticileri rolüne bir hizmet sorumlusu nasıl ekleyeceğiniz açıklanır.

## <a name="before-you-begin"></a>Başlamadan önce
Bu görevi tamamlamadan önce, Azure Active Directory kayıtlı bir hizmet sorumlusu olması gerekir.

[Hizmet sorumlusu oluşturma-Azure portal](../active-directory/develop/howto-create-service-principal-portal.md)   
[Hizmet sorumlusu oluşturma - PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

## <a name="required-permissions"></a>Gerekli izinler
Bu görevi gerçekleştirmek için Azure 'da sunucu [Yöneticisi](analysis-services-server-admins.md) izinlerine sahip olmanız gerekir. 

## <a name="add-service-principal-to-server-administrators-role"></a>Hizmet sorumlusunu sunucu yöneticileri rolüne Ekle

1. SSMS 'de, Azure 'u sunucunuza bağlayın.
2. **Sunucu özellikleri** > **güvenlik**' te, **Ekle**' ye tıklayın.
3. **Kullanıcı veya Grup Seç**' te, kayıtlı uygulamanızı ada göre arayın, öğesini seçin ve ardından **Ekle**' ye tıklayın.

    ![Hizmet sorumlusu hesabı ara](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-picker.png)

4. Hizmet sorumlusu hesap KIMLIĞINI doğrulayın ve ardından **Tamam**' a tıklayın.
    
    ![Hizmet sorumlusu hesabı ara](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-add.png)


> [!NOTE]
> Azure PowerShell cmdlet 'lerini kullanan sunucu işlemleri için Zamanlayıcı çalıştıran hizmet sorumlusu, [Azure rol tabanlı Access Control (RBAC)](../role-based-access-control/overview.md)içindeki kaynak için **sahip** rolüne de ait olmalıdır. 

## <a name="related-information"></a>İlgili bilgiler

* [PowerShell modülünü indir SQL Server](https://docs.microsoft.com/sql/ssms/download-sql-server-ps-module)   
* [SSMS 'yi indir](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)   


