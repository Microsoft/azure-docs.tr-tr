---
title: LinkedIn hesap bağlantıları için yönetici onayı-Azure AD | Microsoft Docs
description: Azure Active Directory 'de Microsoft uygulamalarında LinkedIn Tümleştirme hesabı bağlantılarının nasıl etkinleştirileceğini veya devre dışı bırakılacağını açıklar
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0bf65f69d9dcaf6de2236c98b56b58ec7e021099
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74025406"
---
# <a name="integrate-linkedin-account-connections-in-azure-active-directory"></a>Azure Active Directory LinkedIn hesabı bağlantılarını tümleştirin

Kuruluşunuzdaki kullanıcıların bazı Microsoft uygulamaları dahilinde LinkedIn bağlantılarına erişmesine izin verebilirsiniz. Kullanıcılar hesaplarını bağlamaya izin verene kadar veri paylaşılmaz. Kuruluşunuzu Azure Active Directory (Azure AD) [Yönetim Merkezi](https://aad.portal.azure.com)' nde tümleştirebilirsiniz.

> [!IMPORTANT]
> LinkedIn hesap bağlantıları ayarı şu anda Azure AD kuruluşları için kullanıma alınıyor. Kuruluşunuz tarafından alındığında, varsayılan olarak etkindir.
> 
> Larý
> * Bu ayar ABD Kamu, Microsoft Bulut Almanya veya Azure ve Office 365 ile Çin 'de 21Vianet tarafından işletilen Microsoft Bulut kullanan müşteriler için kullanılamaz.
> * Bu ayar, Almanya 'da sağlanan kiracılar için varsayılan olarak kapalıdır. Ayarın Microsoft Bulut Almanya kullanan müşteriler için kullanılamaz olduğunu unutmayın.
> * Bu ayar, Fransa 'da sağlanan kiracılar için varsayılan olarak kapalıdır.
>
> Kuruluşunuz için LinkedIn hesabı bağlantıları etkinleştirildikten sonra, Kullanıcı adına şirket verilerine erişen uygulamalara izin verdikten sonra hesap bağlantıları çalışır. Kullanıcı izin ayarı hakkında daha fazla bilgi için, bkz. [kullanıcının bir uygulamaya erişimini kaldırma](https://docs.microsoft.com/azure/active-directory/application-access-assignment-how-to-remove-assignment).

## <a name="enable-linkedin-account-connections-in-the-azure-portal"></a>Azure portal LinkedIn hesabı bağlantılarını etkinleştirin

Tüm kuruluşunuzdan yalnızca kuruluşunuzdaki seçili kullanıcılara kadar, yalnızca erişim sağlamak istediğiniz kullanıcılar için LinkedIn hesap bağlantılarını etkinleştirebilirsiniz.

1. Azure AD kuruluş için genel yönetici olan bir hesapla [Azure AD Yönetim merkezinde](https://aad.portal.azure.com/) oturum açın.
1. **Kullanıcıları**seçin.
1. **Kullanıcılar** dikey penceresinde **Kullanıcı ayarları**' nı seçin.
1. **LinkedIn hesap bağlantıları**altında, kullanıcıların bazı Microsoft uygulamalarında LinkedIn bağlantılarına erişmek için hesaplarını bağlanmasına izin verin. Kullanıcılar hesaplarını bağlamaya izin verene kadar veri paylaşılmaz.

    * Kuruluşunuzdaki tüm kullanıcılar için hizmeti etkinleştirmek üzere **Evet** ' i seçin
    * Hizmeti yalnızca kuruluşunuzdaki seçili kullanıcı grubu için etkinleştirmek üzere **Seçili grup** ' u seçin
    * Kuruluşunuzdaki tüm kullanıcıların onayını geri çekmek için **Hayır** ' ı seçin

    ![Kuruluştaki LinkedIn hesabı bağlantılarını tümleştirin](./media/linkedin-integration/linkedin-integration.png)

1. İşiniz bittiğinde, ayarlarınızı kaydetmek için **Kaydet** ' i seçin.

> [!Important]
> LinkedIn tümleştirmesi, hesaplarını bağlamayı kabul edene kadar kullanıcılarınız için tam olarak etkinleştirilmez. Kullanıcılarınız için hesap bağlantılarını etkinleştirdiğinizde hiçbir veri paylaşılmaz.

### <a name="assign-selected-users-with-a-group"></a>Seçili kullanıcıları bir grupla ata
Kullanıcı grubunu seçme seçeneğine sahip kullanıcıların listesini belirten ' selected ' seçeneğini değiştirdik. böylece, birçok bireysel kullanıcı yerine LinkedIn ve Microsoft hesaplarını tek bir grup için bağlama özelliğini etkinleştirebiliriz. Seçili bireysel kullanıcılar için LinkedIn hesabı bağlantıları etkinleştirilmemişse, herhangi bir şey yapmanız gerekmez. Seçili bireysel kullanıcılar için daha önce LinkedIn hesabı bağlantıları etkinleştirdiyseniz şunları yapmalısınız:

1. Bireysel kullanıcıların geçerli listesini al
1. Şu anda etkin olan bireysel kullanıcıları bir gruba taşı
1. Azure AD Yönetim Merkezi 'ndeki LinkedIn hesap bağlantıları ayarında seçili grup ' dan önceki grubu kullanın.

> [!NOTE]
> Şu anda seçili olan bireysel kullanıcılarınızı bir gruba taşımıyorsanız bile, Microsoft uygulamalarında LinkedIn bilgilerini görmeye devam edebilirler.

### <a name="get-the-current-list-of-selected-users"></a>Seçili kullanıcıların geçerli listesini al

1. Yönetici hesabınızla Microsoft 365 oturum açın.
1. [https://linkedinselectedusermigration.azurewebsites.net/](https://linkedinselectedusermigration.azurewebsites.net/) kısmına gidin. LinkedIn hesap bağlantıları için seçilen kullanıcıların listesini görürsünüz.
1. Listeyi bir CSV dosyasına dışarı aktarın.

### <a name="move-the-currently-selected-individual-users-to-a-group"></a>Şu anda seçili olan bireysel kullanıcıları bir gruba taşı

1. PowerShell’i başlatın
1. `Install-Module AzureAD` çalıştırarak Azure AD modülünü yüklemeyi
1. Şu betiği çalıştırın:

  ``` PowerShell
  $groupId = "GUID of the target group"
  
  $users = Get-Content 
  Path to the CSV file
  
  $i = 1
  foreach($user in $users} { Add-AzureADGroupMember -ObjectId $groupId -RefObjectId $user ; Write-Host $i Added $user ; $i++ ; Start-Sleep -Milliseconds 10 }
  ```

Grubu Azure AD Yönetim Merkezi 'ndeki LinkedIn hesap bağlantıları ayarında seçili grup olarak iki adımdan kullanmak için, bkz. [Azure Portal LinkedIn hesabı bağlantılarını etkinleştirme](#enable-linkedin-account-connections-in-the-azure-portal).

## <a name="use-group-policy-to-enable-linkedin-account-connections"></a>LinkedIn hesap bağlantılarını etkinleştirmek için grup ilkesi kullanma

1. [Office 2016 yönetim şablonu dosyalarını (ADMX/ADML)](https://www.microsoft.com/download/details.aspx?id=49030) indirin
1. **ADMX** dosyalarını ayıklayın ve bunları merkezi deponuza kopyalayın.
1. Grup ilkesi yönetimi 'ni açın.
1. Aşağıdaki ayarla bir grup ilkesi nesnesi oluşturun: **Kullanıcı yapılandırması** > **Yönetim Şablonları** > **Microsoft Office 2016** > **çeşitli** > , **Office uygulamalarında LinkedIn özelliklerini gösterir**.
1. **Etkin** veya **devre dışı**seçeneğini belirleyin.
  
   Durum | Etki
   ------ | ------
   **Etkin** | Office 2016 seçeneklerinde **Office uygulamalarında LinkedIn özelliklerini göster** ayarı etkinleştirilmiştir. Kuruluşunuzdaki kullanıcılar, Office 2016 uygulamalarında LinkedIn özelliklerini kullanabilir.
   **Devre dışı** | Office 2016 seçeneklerinde **LinkedIn özelliklerini Office uygulamalarında göster** ayarı devre dışıdır ve son kullanıcılar bu ayarı değiştiremezler. Kuruluşunuzdaki kullanıcılar, Office 2016 uygulamalarında LinkedIn özelliklerini kullanamaz.

Bu Grup ilkesi yalnızca yerel bir bilgisayar için Office 2016 uygulamalarını etkiler. Kullanıcılar Office 2016 uygulamalarında LinkedIn 'i devre dışı bıraklarsa Office 365 ' de LinkedIn özelliklerini görmeye devam edebilirler.

## <a name="next-steps"></a>Sonraki adımlar

* [LinkedIn için Kullanıcı onayı ve veri paylaşımı](linkedin-user-consent.md)

* [Microsoft uygulamalarınızdaki LinkedIn bilgileri ve özellikleri](https://go.microsoft.com/fwlink/?linkid=850740)

* [LinkedIn Yardım Merkezi](https://www.linkedin.com/help/linkedin)

* [Geçerli LinkedIn Tümleştirme ayarınızı Azure portal görüntüleyin](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/UserSettings)
