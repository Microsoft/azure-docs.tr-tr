---
title: Azure Active Directory bir uygulamadan Kullanıcı veya grup atamalarını kaldırma | Microsoft Docs
description: Azure Active Directory içindeki bir kurumsal uygulamadan bir kullanıcının veya grubun erişim atamasını kaldırma
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: mimart
ms.reviewer: asteen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 380816283156969c47f45a9b47435688df91f4ca
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68381054"
---
# <a name="remove-a-user-or-group-assignment-from-an-enterprise-app-in-azure-active-directory"></a>Azure Active Directory içindeki bir kurumsal uygulamadan bir kullanıcı veya grup atamasını kaldırma

Bir kullanıcıyı veya grubu, Azure Active Directory (Azure AD) içinde kuruluş uygulamalarınızdan birine atanan erişimden kaldırmak çok kolaydır. Kurumsal uygulamayı yönetmek için uygun izinlere sahip olmanız gerekir. Ve, dizin için genel yönetici olmanız gerekir.

> [!NOTE]
> Microsoft uygulamaları (örneğin, Office 365 uygulamaları) için, PowerShell kullanarak kullanıcıları Kurumsal bir uygulamaya kaldırın.

## <a name="how-do-i-remove-a-user-or-group-assignment-to-an-enterprise-app-in-the-azure-portal"></a>Azure portal bir kuruluş uygulamasına kullanıcı veya grup atamasını Nasıl yaparım? kaldırmak istiyor musunuz?

1. Dizin için genel yönetici olan bir hesapla [Azure portalda](https://portal.azure.com) oturum açın.
1. **Tüm hizmetler**' i seçin, metin kutusuna **Azure Active Directory** girin ve ardından **ENTER**' u seçin.
1. **Azure Active Directory- *DirectoryName***  sayfasında (diğer bir deyişle, YÖNETTIĞINIZ dizin için Azure AD sayfası) **Kurumsal uygulamalar**' ı seçin.
1. **Kurumsal uygulamalar-tüm uygulamalar** sayfasında, yönetebileceğiniz uygulamaların bir listesini görürsünüz. Bir uygulama seçin.
1. ***Appname*** Genel Bakış sayfasında (diğer bir deyişle, başlığında Seçili uygulamanın adını taşıyan sayfa), **Kullanıcılar & Gruplar**' ı seçin.
1. ***Appname*** **-User & grubu ataması** sayfasında, daha fazla Kullanıcı veya gruptan birini seçin ve ardından **Kaldır** komutunu seçin. Komut isteminde kararlarınızı onaylayın.

## <a name="how-do-i-remove-a-user-or-group-assignment-to-an-enterprise-app-using-powershell"></a>PowerShell 'i kullanarak bir kullanıcı veya grup atamasını bir kurumsal uygulamaya Nasıl yaparım? kaldırmak istiyor musunuz?

1. Yükseltilmiş bir Windows PowerShell komut istemi açın.

   > [!NOTE]
   > AzureAD modülünü yüklemeniz gerekir (komutunu `Install-Module -Name AzureAD`kullanın). NuGet modülünü veya yeni Azure Active Directory v2 PowerShell modülünü yüklemek isteyip istemediğiniz sorulursa, Y yazın ve ENTER tuşuna basın.

1. ' `Connect-AzureAD` İ çalıştırın ve bir genel yönetici kullanıcı hesabıyla oturum açın.
1. Bir uygulamayı bir Kullanıcı ve rolü kaldırmak için aşağıdaki betiği kullanın:

    ```powershell
    # Store the proper parameters
    $user = get-azureaduser -ObjectId <objectId>
    $spo = Get-AzureADServicePrincipal -ObjectId <objectId>

    #Get the ID of role assignment 
    $assignments = Get-AzureADServiceAppRoleAssignment -ObjectId $spo.ObjectId | Where {$_.PrincipalDisplayName -eq $user.DisplayName}

    #if you run the following, it will show you what is assigned what
    $assignments | Select *

    #To remove the App role assignment run the following command.
    Remove-AzureADServiceAppRoleAssignment -ObjectId $spo.ObjectId -AppRoleAssignmentId $assignments[assignment #].ObjectId
    ```

## <a name="next-steps"></a>Sonraki adımlar

- [Tüm gruplarımı gör](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Kurumsal uygulamaya Kullanıcı veya Grup atama](assign-user-or-group-access-portal.md)
- [Kurumsal uygulama için Kullanıcı oturum açma işlemlerini devre dışı bırakma](disable-user-sign-in-portal.md)
- [Kurumsal uygulamanın adını veya logosunu değiştirme](change-name-or-logo-portal.md)
