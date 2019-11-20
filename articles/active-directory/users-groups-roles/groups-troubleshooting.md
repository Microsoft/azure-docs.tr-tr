---
title: Dinamik grup üyelikleriyle ilgili sorunları giderin-Azure AD | Microsoft Docs
description: Azure Active Directory dinamik grup üyeliği için sorun giderme ipuçları
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6f685ac63e3b4a8cf466be4eb4561472fb084d49
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74026554"
---
# <a name="troubleshoot-and-resolve-groups-issues"></a>Sorun giderme ve grup sorunlarını çözme

## <a name="troubleshooting-group-creation-issues"></a>Grup oluşturma sorunlarını giderme

**Azure Portal güvenlik grubu oluşturmayı devre dışı ediyorum, ancak gruplar hala PowerShell aracılığıyla oluşturulabilir** Kullanıcı, yönetici olmayan kullanıcıların erişim panelinde veya Azure portal güvenlik grupları oluşturup oluşturamayacağını kontrol Azure portal denetimlerinde **Azure portalları ayarında güvenlik grupları oluşturabilir** . PowerShell aracılığıyla güvenlik grubu oluşturmayı denetlemez.

PowerShell 'de yönetici olmayan kullanıcılar için Grup oluşturmayı devre dışı bırakmak için:
1. Yönetici olmayan kullanıcıların grup oluşturmalarına izin verildiğini doğrulayın:
   

   ```powershell
   Get-MsolCompanyInformation | Format-List UsersPermissionToCreateGroupsEnabled
   ```

  
2. `UsersPermissionToCreateGroupsEnabled : True`döndürürse yönetici olmayan kullanıcılar grupları oluşturabilir. Bu özelliği devre dışı bırakmak için:
  

   ``` 
   Set-MsolCompanySettings -UsersPermissionToCreateGroupsEnabled $False
   ```

<br/>**PowerShell 'de dinamik bir grup oluşturmaya çalışırken izin verilen en fazla grup hatasını aldım**<br/>
PowerShell 'de, _dinamik grup ilkelerinin izin verilen en fazla grup sayısına ulaşıldığını_belirten bir ileti alırsanız, bu, kiracınızdaki dinamik gruplar için en yüksek sınıra ulaştığınız anlamına gelir. Her kiracı için en fazla dinamik grup sayısı 5.000 ' dir.

Yeni dinamik gruplar oluşturmak için önce var olan bazı dinamik grupları silmeniz gerekir. Sınırı artırmanın bir yolu yoktur.

## <a name="troubleshooting-dynamic-memberships-for-groups"></a>Gruplar için dinamik üyelik sorunlarını giderme

**Grupta bir kural yapılandırdım, ancak grupta hiçbir üyelik güncelleştirilmedi**<br/>
1. Kuraldaki Kullanıcı veya cihaz özniteliklerinin değerlerini doğrulayın. Kuralı karşılayan kullanıcılar olduğundan emin olun. Cihazlar için, eşitlenen özniteliklerin beklenen değerleri içerdiğinden emin olmak için cihaz özelliklerini denetleyin.<br/>
2. Tamamlanmamış olduğunu onaylamak için üyelik işleme durumunu kontrol edin. Grubun **genel bakış** sayfasında [Üyelik işleme durumunu](groups-create-rule.md#check-processing-status-for-a-rule) ve son güncelleştirilme tarihini kontrol edebilirsiniz.

Her şey iyi görünüyorsa, lütfen grubun doldurulması için bir süre bekleyin. Kiracınızın boyutuna bağlı olarak, ilk seferinde veya kural değişikliğinden sonra grubun doldurulması 24 saat kadar sürebilir.

**Bir kural yapılandırdım, ancak artık kuralın var olan üyeleri kaldırılıyor**<br/>Bu beklenen bir davranıştır. Bir kural etkinleştirildiğinde veya değiştirildiğinde grubun mevcut üyeleri kaldırılır. Kural değerlendirmesinden döndürülen kullanıcılar gruba üye olarak eklenir.

**Bir kural eklerken veya değiştirdiğimde, üyelik değişikliklerini anında görmem, neden değil?**<br/>Adanmış üyelik değerlendirmesi zaman uyumsuz bir arka plan işleminde düzenli aralıklarla yapılır. İşlemin ne kadar süreceği, dizininizdeki kullanıcı sayısına ve kuralın sonucu olarak oluşturulan grubun boyutuna göre belirlenir. Genellikle, az sayıda kullanıcısı olan dizinler grup üyeliği değişikliğini birkaç dakikadan kısa bir süre içinde görür. Çok sayıda kullanıcısı olan dizinler, doldurulması 30 dakika veya daha uzun sürebilir.

**Grubu şimdi işleme nasıl zorlayabilir?**<br/>
Şu anda, grubun talep üzerine işlenmek üzere otomatik olarak tetiklenmesi için bir yol yoktur. Ancak, üyelik kuralını, sonunda boşluk eklemek üzere güncelleştirerek yeniden işlemeyi el ile tetikleyebilirsiniz.  

**Bir kural işleme hatasıyla karşılaştım**<br/>Aşağıdaki tabloda, yaygın dinamik üyelik kuralı hataları ve nasıl düzelticekleri listelenmektedir.

| Kural ayrıştırıcı hatası | Hata kullanımı | Düzeltilen kullanım |
| --- | --- | --- |
| Hata: öznitelik desteklenmiyor. |(User. ınvalidproperty-EQ "Value") |(User. Department-EQ "değer")<br/><br/>Özniteliğin [desteklenen özellikler listesinde](groups-dynamic-membership.md#supported-properties)olduğundan emin olun. |
| Hata: Işleç öznitelik üzerinde desteklenmiyor. |(User. accountEnabled-true içerir) |(User. accountEnabled-EQ true)<br/><br/>Kullanılan işleç Özellik türü için desteklenmiyor (Bu örnekte-Contains, Boolean türü üzerinde kullanılamaz). Özellik türü için doğru işleçleri kullanın. |
| Hata: sorgu derleme hatası. | 1. (User. Department-EQ "Sales") (User. Department-EQ "pazarlama")<br>2. (User. userPrincipalName-Match "*@domain.ext") | 1. işleç eksik. Ve veya ya da iki JOIN yüklemleri kullanın<br>(User. Department-EQ "Sales")-veya (User. Department-EQ "pazarlama")<br>2.-Match ile kullanılan normal ifadede hata<br>(User. userPrincipalName-Match ". *@domain.ext")<br>ya da alternatif olarak: (User. userPrincipalName-Match "@domain.ext$") |

## <a name="next-steps"></a>Sonraki adımlar

Bu makalelerde Azure Active Directory ile ilgili ek bilgi sağlanmıştır.

* [Azure Active Directory grupları ile kaynaklara erişimi yönetme](../fundamentals/active-directory-manage-groups.md)
* [Azure Active Directory’de Uygulama Yönetimi](../manage-apps/what-is-application-management.md)
* [Azure Active Directory nedir?](../fundamentals/active-directory-whatis.md)
* [Şirket içi kimliklerinizi Azure Active Directory ile tümleştirme](../hybrid/whatis-hybrid-identity.md)