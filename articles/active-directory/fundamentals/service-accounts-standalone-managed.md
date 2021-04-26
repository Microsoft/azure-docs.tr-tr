---
title: Tek başına yönetilen hizmet hesaplarının güvenliğini sağlama | Azure Active Directory
description: Tek başına yönetilen hizmet hesaplarının güvenliğini sağlamaya yönelik bir kılavuz.
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
ms.openlocfilehash: e2b3079407774c3d36fe5515b39e964018f9087e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102548861"
---
# <a name="securing-standalone-managed-service-accounts"></a>Tek başına yönetilen hizmet hesaplarının güvenliğini sağlama

Tek başına yönetilen hizmet hesapları (sMSAs), sunucuda çalışan bir veya daha fazla hizmetin güvenliğini sağlamak için kullanılan yönetilen etki alanı hesaplarıdır. Birden çok sunucu arasında yeniden kullanılamaz. sMSAs otomatik parola yönetimi, Basitleştirilmiş hizmet asıl adı (SPN) yönetimi ve diğer yöneticilere yönetimi için temsilci seçme yeteneği sağlar. 

Active Directory, sMSAs hizmeti çalıştıran belirli bir sunucuya bağlıdır. Bu hesapları, Microsoft Yönetim konsolunun Active Directory Kullanıcıları ve Bilgisayarları ek bileşeninde bulabilirsiniz.

![Yönetilen hizmet hesapları OU 'sunu gösteren Active Directory Kullanıcıları ve Bilgisayarları ek bileşeninin ekran görüntüsü.](./media/securing-service-accounts/secure-standalone-msa-image-1.png)

Yönetilen hizmet hesapları Windows Server 2008R2 Active Directory şeması ile tanıtılmıştı ve en düşük işletim sistemi düzeyi olan Windows Server 2008R2 gerektirir. 

## <a name="benefits-of-using-smsas"></a>SMSAs kullanmanın avantajları

sMSAs, hizmet hesapları olarak kullanılan kullanıcı hesaplarından daha fazla güvenlik sunar ve yönetim yükünü şu şekilde azaltır:

* Güçlü parolalar ayarlanıyor. sMSAs 240 baytlık rastgele oluşturulmuş karmaşık parolalar kullanır. SMSA parolalarının karmaşıklığı ve uzunluğu, deneme yanılma veya sözlük saldırılarına karşı bir hizmetin güvenliğinin aşılmasına yardımcı olma olasılığını en aza indirir.

* Parolalar düzenli olarak döngü yapılıyor. Windows, sMSA parolasını her 30 günde bir otomatik olarak değiştirir. Hizmet ve etki alanı yöneticilerinin parola değişikliklerini zamanlaması veya ilişkili kapalı kalma süresini yönetmesi gerekmez.

* SPN yönetimini basitleştirme. Etki alanı Işlev düzeyi (DFL) Windows Server 2008 R2 ise hizmet sorumlusu adları otomatik olarak güncelleştirilir. Örneğin, hizmet sorumlusu adı aşağıdaki senaryolarda otomatik olarak güncelleştirilir:

   * Konak bilgisayar hesabı yeniden adlandırıldı. 

   * Ana bilgisayarın DNS adı değiştirildi.

   * [PowerShell](/powershell/module/addsadministration/set-adserviceaccount) kullanarak ek bir Sam-AccountName veya DNS-hostname parametreleri ekleme veya kaldırma

## <a name="when-to-use-smsas"></a>SMSAs ne zaman kullanılır?

sMSAs, yönetim ve güvenlik görevlerini kolaylaştırabilir. Tek bir sunucuya dağıtılan bir veya daha fazla hizmet olduğunda sMSAs kullanın ve bir gMSA kullanamazsınız. 

> [!NOTE] 
> Birden fazla hizmet için sMSAs kullanabilirsiniz, ancak her bir hizmetin denetim amacıyla kendi kimliğine sahip olması önerilir. 

Yazılım Oluşturucusu bir MSA kullanıp kullanbil, bunu söyleyebilir, uygulamanızı test etmeniz gerekir. Bunu yapmak için, bir test ortamı oluşturun ve gerekli tüm kaynaklara erişebildiğinden emin olun. Bkz. adım adım yönergeler için [sMSA oluşturma ve yüklemeyi](/archive/blogs/askds/managed-service-accounts-understanding-implementing-best-practices-and-troubleshooting) öğrenin.

### <a name="assess-security-posture-of-smsas"></a>SMSAs güvenlik duruşunu değerlendir

sMSAs, daha önce standart kullanıcı hesaplarından daha güvenlidir ve bu sayede devam eden parola yönetimi gerekir. Ancak, sMSAs 'ın genel güvenlik duruşun bir parçası olarak erişim kapsamını göz önünde bulundurmanız önemlidir.

Aşağıdaki tabloda, SMSA 'Ların meydana getirdiği olası güvenlik sorunlarının nasıl azaltılacağını gösterilmektedir.

| Güvenlik sorunları| Risk Azaltıcı Etkenler |
| - | - |
| sMSA, ayrıcalıklı grupların bir üyesidir|Yükseltilmiş ayrıcalıklı gruplardan sMSA 'yı kaldırın (örneğin, Domain Admins). <br> En az ayrıcalıklı modeli kullanın ve sMSA 'Yı yalnızca kendi hizmetini çalıştırmak için gereken hakları ve izinleri verin. <br> Gerekli izinlerden emin değilseniz, Service Creator 'a başvurun. |
| sMSA, hassas kaynaklara okuma/yazma erişimine sahiptir.|Hassas kaynaklara erişimi denetleyin. Analiz için Denetim günlüklerini SıEM (Azure Log Analytics veya Azure Sentinel) olarak arşivleyin. <br> İstenmeyen bir erişim düzeyi algılanırsa kaynak izinlerini düzeltin. |
| Varsayılan olarak, sMSA parolasının geçiş sıklığı 30 gündür| Grup ilkesi, kurumsal güvenlik gereksinimlerine bağlı olarak süreyi ayarlamak için kullanılabilir. <br> * Parola süre sonu süresini aşağıdaki yolu kullanarak ayarlayabilirsiniz. <br>Bilgisayar Yapılandırması \ Windows ayarları \ güvenlik ayarları \ Options\Domain üye: maksimum makine hesabı parola yaşı |



### <a name="challenges-with-smsas"></a>SMSAs ile ilgili sorunlar

SMSAs ile ilişkili sorunlar şunlardır:

| Zorluklar| Risk Azaltıcı Etkenler |
| - | - |
| Tek bir sunucuda kullanılabilirler.| Hesabı sunucular arasında kullanmanız gerekiyorsa gMSAs kullanın. |
| Etki alanları arasında kullanılamaz.| Hesabı etki alanları genelinde kullanmanız gerekiyorsa gMSAs kullanın. |
| Tüm uygulamalar sMSAs 'yi desteklemez.| Mümkünse gMSAs kullanın. Uygulama Oluşturucu tarafından önerilen standart bir kullanıcı hesabı veya bilgisayar hesabı kullanmıyorsanız. |


## <a name="find-smsas"></a>SMSAs bulma

Herhangi bir etki alanı denetleyicisinde DSA. msc ' yi çalıştırın ve yönetilen hizmet hesapları kapsayıcısını genişleterek tüm SMSA 'Ları görüntüleyin. 

Aşağıdaki PowerShell komutu, Active Directory etki alanındaki tüm sMSAs ve gMSAs ' i döndürür. 

`Get-ADServiceAccount -Filter *`

Aşağıdaki komut Active Directory etki alanında yalnızca sMSAs döndürür.

`Get-ADServiceAccount -Filter * | where { $_.objectClass -eq "msDS-ManagedServiceAccount" }`

## <a name="manage-smsas"></a>SMSAs 'yi yönetme

SMSAs 'yi yönetmek için aşağıdaki Active Directory PowerShell cmdlet 'lerini kullanabilirsiniz:

`Get-ADServiceAccount`

` Install-ADServiceAccount`

` New-ADServiceAccount`

` Remove-ADServiceAccount`

`Set-ADServiceAccount`

`Test-ADServiceAccount`

`Ininstall-ADServiceAccount`

## <a name="move-to-smsas"></a>SMSAs 'ye taşı

Bir uygulama hizmeti, sMSA 'yı destekler, ancak şu anda güvenlik bağlamı için bir kullanıcı hesabı ya da bilgisayar hesabı kullanıyorsa, sunucuda [bir SMSA oluşturun ve](/archive/blogs/askds/managed-service-accounts-understanding-implementing-best-practices-and-troubleshooting) bu işlemi yapın. 

İdeal olarak, kaynakları Azure 'a taşıyın ve Azure yönetilen kimliklerini veya hizmet sorumlularını kullanın.

 

## <a name="next-steps"></a>Sonraki adımlar
Hizmet hesaplarını güvenli hale getirmek için aşağıdaki makalelere bakın

* [Şirket içi hizmet hesaplarına giriş](service-accounts-on-premises.md)

* [Güvenli grup yönetilen hizmet hesapları](service-accounts-group-managed.md)

* [Güvenli tek başına yönetilen hizmet hesapları](service-accounts-standalone-managed.md)

* [Güvenli bilgisayar hesapları](service-accounts-computer.md)

* [Güvenli Kullanıcı hesapları](service-accounts-user-on-premises.md)

* [Şirket içi hizmet hesaplarını yönetir](service-accounts-govern-on-premises.md)

