---
title: Kullanıcı tabanlı hizmet hesaplarının güvenliğini sağlama | Azure Active Directory
description: Şirket içi kullanıcı hesaplarının güvenliğini sağlamaya yönelik bir kılavuz.
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
ms.openlocfilehash: e484bdda33142024f2067649eaa67042fe7776f8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100417955"
---
# <a name="securing-user-based-service-accounts-in-active-directory"></a>Active Directory 'de Kullanıcı tabanlı hizmet hesaplarının güvenliğini sağlama

Şirket içi kullanıcı hesapları, Windows üzerinde çalışan hizmetleri güvenli hale getirmenin geleneksel yaklaşımıdır. Genel yönetilen hizmet hesapları (gMSAs) ve tek başına yönetilen hizmet hesapları (sMSAs) hizmetinize desteklenmiyorsa bu hesapları son çare olarak kullanın. Kullanılacak en iyi hesap türünü seçme hakkında bilgi için bkz. Şirket içi hizmet hesaplarına genel bakış. Ayrıca, hizmetinizi yönetilen kimlik veya hizmet ilkesi gibi bir Azure hizmet hesabı kullanmak üzere taşıyıp taşıyabileceğinizi araştırın. 

Şirket içi kullanıcı hesapları, hizmetler için bir güvenlik bağlamı sağlamak üzere oluşturulabilir ve hizmetlerin yerel ve ağ kaynaklarına erişmesi için gerekli ayrıcalıklara sahip olur. Diğer tüm Active Directory (AD) Kullanıcı hesapları gibi el ile parola yönetimine gerek duyar. Bu hesapların güvenliğini sağlamak için güçlü parola yönetimi süreçlerini gözlemlemek için hizmet ve etki alanı yöneticileri gereklidir.

Bir kullanıcı hesabını hizmet hesabı olarak kullanırken, yalnızca tek bir hizmet için kullanın. Bir hizmet hesabı olduğunu ve hizmet hesabını temizlediğinden emin olmak için bir şekilde adlandırın. 

## <a name="benefits-and-challenges"></a>Avantajlar ve sorunlar

Avantajlar

Şirket içi kullanıcı hesapları, hizmetleriyle kullanım için en çok yönlü hesap türüdür. Hizmet hesapları olarak kullanılan Kullanıcı hesapları, normal kullanıcı hesaplarını yöneten tüm ilkeler tarafından denetlenebilirler. Bu şekilde, bunları yalnızca bir MSA kullanamıyoruz kullanın. Ayrıca bir bilgisayar hesabının daha iyi bir seçenek olup olmadığını da değerlendirin. 

Şirket içi kullanıcı hesaplarıyla ilgili sorunlar

Aşağıdaki sorunlar, şirket içi kullanıcı hesaplarının kullanımıyla ilişkilidir.

| Zorluklar| Risk Azaltıcı Etkenler |
| - | - |
| Parola yönetimi, daha zayıf güvenlik ve hizmet kapalı kalma süresine neden olabilecek el ile gerçekleştirilen bir işlemdir.| Parola karmaşıklığı ve parola değişikliğinin güçlü bir parolayla düzenli güncelleştirmeler yapılmasını sağlayan sağlam bir işlem tarafından yönetildiğinden emin olun. <br> Hizmet üzerindeki bir parola güncelleştirmesiyle koordinat parolası değişikliği, hizmet kapalı kalma süresine neden olur. |
| Hizmet hesabı olarak davranan şirket içi kullanıcı hesaplarının tanımlanması zor olabilir.| Ortamınızda dağıtılan hizmet hesaplarının kayıtlarını belgeleyin ve saklayın. <br> Hesap adını ve bunlara erişim atandıkları kaynakları izleyin. <br> Hizmet hesabı olarak kullanılan tüm kullanıcı hesaplarına svc_ bir ön eki eklemeyi düşünün. |


## <a name="find-on-premises-user-accounts-used-as-service-accounts"></a>Hizmet hesabı olarak kullanılan şirket içi kullanıcı hesaplarını bulun

Şirket içi kullanıcı hesapları, diğer tüm AD Kullanıcı hesaplarında olduğu gibidir. Sonuç olarak, bir hizmet hesabı olarak tanımlayan bir kullanıcı hesabının tek bir özniteliği olmadığından, bu hesapların bulunması zor olabilir. 

Hizmet hesabı olarak kullanılan herhangi bir kullanıcı hesabı için kolayca tanımlanabilir bir adlandırma kuralı oluşturmanızı öneririz.

Örneğin, "Service-" önekini önek olarak ekleyin ve hizmeti "Service-HRDataConnector" olarak adlandırın.

Bu hizmet hesaplarını bulmak için aşağıdaki bazı göstergeleri kullanabilirsiniz, ancak bu, bu tür hesapların tümünü bulamamayabilir.

* Hesap, yetkilendirme için güvenilir.

* Hizmet sorumlusu adına sahip hesaplar.

* Parolası hiçbir zaman sona ermeyecek şekilde ayarlanmış olan hesaplar.

Hizmetler için oluşturulan şirket içi kullanıcı hesaplarını bulmak için aşağıdaki PowerShell komutlarını çalıştırabilirsiniz.

### <a name="find-accounts-trusted-for-delegation"></a>Yetkilendirme için Güvenilen hesapları bulma

```PowerShell

Get-ADObject -Filter {(msDS-AllowedToDelegateTo -like '*') -or (UserAccountControl -band 0x0080000) -or (UserAccountControl -band 0x1000000)} -prop samAccountName,msDS-AllowedToDelegateTo,servicePrincipalName,userAccountControl | select DistinguishedName,ObjectClass,samAccountName,servicePrincipalName, @{name='DelegationStatus';expression={if($_.UserAccountControl -band 0x80000){'AllServices'}else{'SpecificServices'}}}, @{name='AllowedProtocols';expression={if($_.UserAccountControl -band 0x1000000){'Any'}else{'Kerberos'}}}, @{name='DestinationServices';expression={$_.'msDS-AllowedToDelegateTo'}}

```

### <a name="find-accounts-with-service-principle-names"></a>Hizmet ilkesi adlarına sahip hesapları bulma

```PowerShell

Get-ADUser -Filter * -Properties servicePrincipalName | where {$_.servicePrincipalName -ne $null}

```

 

### <a name="find-accounts-with-passwords-set-to-never-expire"></a>Parolaların süre sonu olarak ayarlanmış olan hesapların bulun

```PowerShell

Get-ADUser -Filter * -Properties PasswordNeverExpires | where {$_.PasswordNeverExpires -eq $true}

```


Ayrıca gizli kaynaklara erişimi denetleyebilir ve denetim günlüklerini güvenlik bilgileri ve olay yönetimi (SıEM) sistemine arşivleyebilirsiniz. Azure Log Analytics veya Azure Sentinel gibi sistemleri kullanarak, ve hizmet hesaplarını arayabilir ve analiz edebilirsiniz.

## <a name="assess-security-of-on-premises-user-accounts"></a>Şirket içi kullanıcı hesaplarının güvenliğini değerlendirin

Aşağıdaki ölçütleri kullanarak hizmet hesabı olarak kullanılan şirket içi Kullanıcı hesaplarınızın güvenliğini değerlendirin:

* Parola yönetimi ilkesi nedir?

* Hesap herhangi bir ayrıcalıklı grubun üyesi mi?

* Hesap, önemli kaynaklara okuma/yazma erişimine sahip mi?

### <a name="mitigate-potential-security-issues"></a>Olası güvenlik sorunlarını azaltma

Aşağıdaki tabloda, şirket içi kullanıcı hesapları için olası güvenlik sorunları ve ilgili azaltmaları gösterilmektedir.

| Güvenlik sorunları| Risk Azaltıcı Etkenler |
| - | - |
| Parola yönetimi|* Parola karmaşıklığı ve parola değişikliğinin, güçlü parola gereksinimleriyle düzenli güncelleştirmeler yapılmasını sağlayan sağlam bir işlem tarafından yönetildiğinden emin olun. <br> * Hizmetin kapalı kalma süresini en aza indirmek için parola güncelleştirmesiyle koordinat parolasını değiştirin. |
| Hesap, ayrıcalıklı grupların bir üyesidir.| Grup üyeliklerini gözden geçirin. Hesabı ayrıcalıklı gruplardan kaldırın. Hesaba yalnızca kendi hizmetini çalıştırmak için gereken hakları ve izinleri verin (hizmet satıcısına başvurun). Örneğin, yerel olarak oturum açmayı reddedebiliyor veya etkileşimli oturum açmayı reddedebilirsiniz. |
| Hesabın hassas kaynaklara okuma/yazma erişimi vardır.| Hassas kaynaklara erişimi denetleyin. Analiz için Denetim günlüklerini SıEM (Azure Log Analytics veya Azure Sentinel) olarak arşivleyin. İstenmeyen bir erişim düzeyi algılanırsa kaynak izinlerini düzeltin. |


## <a name="move-to-more-secure-account-types"></a>Daha güvenli hesap türlerine gitme

Microsoft, müşterilerin Şirket içi kullanıcı hesaplarını hizmet hesapları olarak kullanmasını önermez. Bu hesap türünü kullanan herhangi bir hizmet için, bunun yerine bir gMSA veya sMSA kullanmak üzere yapılandırılıp yapılandırılmadığını değerlendirin.

Ayrıca, daha güvenli hizmet hesabı türlerinin kullanılabilmesi için hizmetin kendisinin Azure 'a taşınıp taşınamayacağını değerlendirin. 

## <a name="next-steps"></a>Sonraki adımlar
Hizmet hesaplarını güvenli hale getirmek için aşağıdaki makalelere bakın

* [Şirket içi hizmet hesaplarına giriş](service-accounts-on-premises.md)

* [Güvenli grup yönetilen hizmet hesapları](service-accounts-group-managed.md)

* [Güvenli tek başına yönetilen hizmet hesapları](service-accounts-standalone-managed.md)

* [Güvenli bilgisayar hesapları](service-accounts-computer.md)

* [Güvenli Kullanıcı hesapları](service-accounts-user-on-premises.md)

* [Şirket içi hizmet hesaplarını yönetir](service-accounts-govern-on-premises.md)

 
