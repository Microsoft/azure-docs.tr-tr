---
title: Grup tarafından yönetilen hizmet hesaplarının güvenliğini sağlama | Azure Active Directory
description: Grup tarafından yönetilen hizmet hesapları bilgisayar hesaplarının güvenliğini sağlamaya yönelik bir kılavuz.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 2/15/2021
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: f6f91adf499c62fef56a30c5f2945ba7a90a0b29
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105640067"
---
# <a name="securing-group-managed-service-accounts"></a>Grup tarafından yönetilen hizmet hesaplarının güvenliğini sağlama

Grup tarafından yönetilen hizmet hesapları (gMSAs), hizmetlerin güvenliğini sağlamak için kullanılan yönetilen etki alanı hesaplarıdır. gMSAs, tek bir sunucuda veya bir ağ Load Balancer (NLB) veya bir Internet Information Services (IIS) sunucusunun arkasındaki sistemler gibi bir sunucu grubunda çalıştırılabilir. Hizmetlerinizi bir gMSA sorumlusu kullanacak şekilde yapılandırdıktan sonra, bu hesap için parola yönetimi Windows tarafından işlenir.

## <a name="benefits-of-using-gmsas"></a>GMSAs kullanmanın avantajları

gMSAs, yönetim yükünü azaltarak daha fazla güvenlik sunarak tek bir kimlik çözümü sunar:

* **Güçlü parolalar ayarlanıyor**. gMSAs 240 baytlık rastgele karmaşık parolalar kullanır. GMSA parolalarının karmaşıklığı ve uzunluğu, deneme yanılma veya sözlük saldırılarına karşı bir hizmetin güvenliğinin aşılmasına yardımcı olma olasılığını en aza indirir.

* **Parolalar düzenli olarak döngü** yapılıyor. Windows için gMSAs SHIFT parola yönetimi, parolayı her 30 günde bir değiştirir. Hizmet ve etki alanı yöneticilerinin, hizmet hesaplarının güvenliğini sağlamak için artık parola değişiklikleri zamanlaması veya hizmet kesintilerini yönetmesi gerekmez. 

* **Sunucu gruplarına dağıtımı destekleme**. Birden çok sunucuya gMSAs dağıtma özelliği, birden çok konağın aynı hizmeti çalıştırdığı yük dengeli çözümlerin desteklenmesi için izin verir. 

* **Basitleştirilmiş sunucu asıl adı (SPN) yönetimini destekleme**. PowerShell kullanarak, hesap oluşturma sırasında SPN 'YI ayarlayabilirsiniz. Bunlara ek olarak, otomatik SPN kayıtlarını destekleyen hizmetler, gMSA izinlerinin doğru ayarlanmış olması için gMSA 'ya karşı bu şekilde çalışabilir. 

## <a name="when-to-use-gmsas"></a>GMSAs ne zaman kullanılır?

Yük Devretme Kümelemesi gibi bir hizmet bu hizmeti desteklemiyorsa, şirket içi hizmetler için gMSA 'Ları tercih edilen hesap türü olarak kullanın.

> [!IMPORTANT]
> Üretime dağıtım yapmadan önce hizmeti gMSAs ile test etmeniz gerekir. Bunu yapmak için, bir test ortamı ayarlayın ve uygulamanın gMSA 'yı kullanabilmesi ve erişmesi gereken kaynaklara erişebilmesi gerekir. Daha fazla bilgi için bkz. [Grup tarafından yönetilen hizmet hesapları Için destek](/system-center/scom/support-group-managed-service-accounts).


Bir hizmet gMSAs kullanımını desteklemiyorsa, bir sonraki en iyi seçeneğiniz tek başına yönetilen hizmet hesabı (sMSA) kullanmaktır. sMSAs, gMSA ile aynı işlevselliği sağlar, ancak yalnızca tek bir sunucuda dağıtıma yöneliktir.

Hizmet tarafından bir gMSA veya sMSA kullanmıyorsanız, hizmetin standart bir kullanıcı hesabı olarak çalışacak şekilde yapılandırılması gerekir. Hesabı güvenli tutmak için güçlü parola yönetimi süreçlerini gözlemlemek için hizmet ve etki alanı yöneticileri gereklidir.

## <a name="assess-the-security-posture-of-gmsas"></a>GMSAs güvenlik duruşunu değerlendirin

gMSA 'Lar, sürekli parola yönetimi gerektiren standart kullanıcı hesaplarından doğal olarak daha güvenlidir. Ancak, genel güvenlik duruşunu göz önünde bulundurmanız durumunda gMSAs 'ın erişim kapsamını düşünmek önemlidir.

Aşağıdaki tabloda, gMSAs kullanımı için olası güvenlik sorunları ve azaltmaları gösterilmektedir.

| Güvenlik sorunları| Risk Azaltıcı Etkenler |
| - | - |
| gMSA, ayrıcalıklı grupların bir üyesidir. | Grup üyeliklerinizi gözden geçirin. Bunu yapmak için tüm grup üyeliklerini numaralandırmak üzere bir PowerShell betiği oluşturabilir ve ardından bir sonuç CSV dosyasını gMSA dosyalarınızın adlarına göre filtreleyebilirsiniz. <br>Ayrıcalıklı gruplardan gMSA 'yı kaldırın.<br> GMSA 'ya yalnızca kendi hizmetini çalıştırmak için gereken hakları ve izinleri verin (hizmet satıcınıza başvurun). 
| gMSA, hassas kaynaklara okuma/yazma erişimine sahiptir. | Hassas kaynaklara erişimi denetleyin. Denetim günlüklerini, analiz için Azure Log Analytics veya Azure Sentinel gibi bir SıEM 'ye arşivleyebilirsiniz. İstenmeyen erişim algılanırsa gereksiz kaynak izinlerini kaldırın. |


## <a name="find-gmsas"></a>GMSAs bulun

Kuruluşunuzda gMSAs zaten oluşturulmuş olabilir. Şu hesapları almak için aşağıdaki PowerShell cmdlet 'ini çalıştırın:

Etkin bir şekilde çalışmak için gMSA 'Ları yönetilen hizmet hesapları kuruluş biriminde (OU) olmalıdır.

  
![Yönetilen hizmet hesabı OU 'su ekran görüntüsü.](./media/securing-service-accounts/secure-gmsa-image-1.png)

Mevcut olmayan hizmet MSAs 'lerini bulmak için aşağıdaki komutlara bakın.

**GMSAs ve sMSAs dahil tüm hizmet hesaplarını bulmak için:**


```powershell

Get-ADServiceAccount -Filter *

# This PowerShell cmdlet will return all Managed Service Accounts (both gMSAs and sMSAs). An administrator can differentiate between the two by examining the ObjectClass attribute on returned accounts.

# For gMSA accounts, ObjectClass = msDS-GroupManagedServiceAccount

# For sMSA accounts, ObjectClass = msDS-ManagedServiceAccount

# To filter results to only gMSAs:

Get-ADServiceAccount –Filter * | where $_.ObjectClass -eq "msDS-GroupManagedServiceAccount"}
```

## <a name="manage-gmsas"></a>GMSAs 'yi yönetme

GMSAs 'yi yönetmek için aşağıdaki Active Directory PowerShell cmdlet 'lerini kullanabilirsiniz:

`Get-ADServiceAccount`

`Install-ADServiceAccount`

`New-ADServiceAccount`

`Remove-ADServiceAccount`

`Set-ADServiceAccount`

`Test-ADServiceAccount`

`Uninstall-ADServiceAccount`

> [!NOTE]
> Windows Server 2012 ile başlayarak, *-ADServiceAccount cmdlet 'leri varsayılan olarak gMSAs ile çalışır. Yukarıdaki cmdlet 'lerin kullanımı hakkında daha fazla bilgi için bkz. [**Grup tarafından yönetilen hizmet hesapları Ile çalışmaya**](/windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts)başlama.

## <a name="move-to-a-gmsa"></a>GMSA 'ya taşı
gMSAs, şirket içi gereksinimler için en güvenli hizmet hesabı türüdür. Bir öğesine taşıyabiliyorsanız, yapmanız gerekir. Ayrıca, hizmetlerinizi Azure 'a ve hizmet hesaplarınıza Azure Active Directory 'ye taşımayı düşünün.

1.  [KDS kök anahtarının ormanda dağıtıldığından](/windows-server/security/group-managed-service-accounts/create-the-key-distribution-services-kds-root-key)emin olun. Bu bir seferlik bir işlemdir.

2. [Yeni bir gMSA oluşturun](/windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts).

3. Hizmeti çalıştıran her konağa yeni gMSA 'yı yükler.
   > [!NOTE] 
   > Bir konakta gMSA oluşturma ve yükleme hakkında daha fazla bilgi için, hizmetinizi gMSA kullanacak şekilde yapılandırmadan önce bkz. [Grup yönetilen hizmet hesaplarıyla çalışmaya başlama](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj128431(v=ws.11))

 
4. Hizmet kimliğinizi gMSA olarak değiştirin ve boş bir parola belirtin.

5. Hizmetinizin yeni gMSA kimliği altında çalıştığını doğrulayın.

6. Eski hizmet hesabı kimliğini silin.

 

## <a name="next-steps"></a>Sonraki adımlar
Hizmet hesaplarını güvenli hale getirmek için aşağıdaki makalelere bakın

* [Şirket içi hizmet hesaplarına giriş](service-accounts-on-premises.md)

* [Güvenli grup yönetilen hizmet hesapları](service-accounts-group-managed.md)

* [Güvenli tek başına yönetilen hizmet hesapları](service-accounts-standalone-managed.md)

* [Güvenli bilgisayar hesapları](service-accounts-computer.md)

* [Güvenli Kullanıcı hesapları](service-accounts-user-on-premises.md)

* [Şirket içi hizmet hesaplarını yönetir](service-accounts-govern-on-premises.md)