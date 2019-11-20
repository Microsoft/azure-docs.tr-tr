---
title: Kurumsal Güvenlik Paketi ile Azure HDInsight mimarisi
description: Kurumsal Güvenlik Paketi Azure HDInsight güvenliğini nasıl planlayacağınızı öğrenin.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: omidm
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/24/2019
ms.openlocfilehash: e7983c4da4803965dabaa6a471fbea8a2fba5229
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/09/2019
ms.locfileid: "70810951"
---
# <a name="use-enterprise-security-package-in-hdinsight"></a>HDInsight 'ta Kurumsal Güvenlik Paketi kullanma

Standart Azure HDInsight kümesi tek kullanıcılı bir kümedir. Büyük veri iş yükleri oluşturan daha küçük uygulama ekiplerine sahip çoğu şirket için uygundur. Her Kullanıcı isteğe bağlı bir küme oluşturabilir ve artık gerekli olmadığında yok edebilir. 

Birçok kuruluş, BT ekiplerinin kümeleri yönetmesi ve birden çok uygulama ekibinin kümeleri paylaştığı bir modele doğru taşınmıştır. Bu büyük kuruluşların Azure HDInsight 'taki her kümeye çok kullanıcılı erişim gereksinimi vardır.

HDInsight, yönetilen bir şekilde, popüler bir kimlik sağlayıcısını (Active Directory) kullanır. HDInsight 'ı [Azure Active Directory Domain Services (Azure AD DS)](../../active-directory-domain-services/overview.md)ile tümleştirerek, etki alanı kimlik bilgilerinizi kullanarak kümelere erişebilirsiniz. 

HDInsight 'taki sanal makineler (VM 'Ler), sizin belirttiğiniz etki alanına katılmış etki alanıdır. Bu nedenle, HDInsight üzerinde çalışan tüm hizmetler (Apache ambarı, Apache Hive sunucusu, Apache Ranger, Apache Spark Thrift Server ve diğerleri) kimliği doğrulanmış kullanıcı için sorunsuz bir şekilde çalışır. Yöneticiler daha sonra, kümedeki kaynaklar için rol tabanlı erişim denetimi sağlamak üzere Apache Ranger kullanarak güçlü yetkilendirme ilkeleri oluşturabilir.

## <a name="integrate-hdinsight-with-active-directory"></a>HDInsight’ı Active Directory ile tümleştirin

Açık kaynaklı Apache Hadoop, kimlik doğrulama ve güvenlik için Kerberos protokolüne bağımlıdır. Bu nedenle, Kurumsal Güvenlik Paketi (ESP) ile HDInsight kümesi düğümleri Azure AD DS tarafından yönetilen bir etki alanına katılır. Kerberos güvenliği, kümedeki Hadoop bileşenleri için yapılandırılır. 

Aşağıdaki öğeler otomatik olarak oluşturulur:

- Her Hadoop bileşeni için bir hizmet sorumlusu
- Etki alanına katılmış her makine için bir makine sorumlusu
- Bu hizmet ve makine sorumlularını depolamak için her küme için bir kuruluş birimi (OU)

Özetlemek gerekirse, şunları içeren bir ortam ayarlamanız gerekir:

- Active Directory etki alanı (Azure AD DS tarafından yönetilen). **Azure HDInsight ile çalışmak için etki alanı adının 39 karakter veya daha az olması gerekir.**
- Azure AD DS 'de Güvenli LDAP (LDAPS) etkin.
- Bunlar için ayrı sanal ağlar seçerseniz, HDInsight sanal ağından Azure AD DS sanal ağı 'na doğru ağ bağlantısı. HDInsight sanal ağının içindeki bir VM 'nin, sanal ağ eşlemesi aracılığıyla Azure AD DS bir görüş satırı olması gerekir. HDInsight ve Azure AD DS aynı sanal ağa dağıtılırsa, bağlantı otomatik olarak sağlanır ve başka bir eylem gerekmez.

## <a name="set-up-different-domain-controllers"></a>Farklı etki alanı denetleyicileri ayarlama
HDInsight Şu anda, kümenin Kerberos iletişimi için kullandığı ana etki alanı denetleyicisi olarak yalnızca Azure AD DS desteklemektedir. Ancak, bu tür bir kurulum HDInsight için Azure AD DS etkinleştirmeye neden olduğu sürece diğer karmaşık Active Directory kurulumları mümkündür.

### <a name="azure-active-directory-domain-services"></a>Azure Active Directory Domain Services
[Azure AD DS](../../active-directory-domain-services/overview.md) , Windows Server Active Directory ile tamamen uyumlu bir yönetilen etki alanı sağlar. Microsoft, yüksek oranda kullanılabilir (HA) bir kurulumda etki alanını yönetme, düzeltme eki uygulama ve izlemeye önem kazanır. Etki alanı denetleyicilerini koruma konusunda endişelenmeden kümenizi dağıtabilirsiniz. 

Kullanıcılar, gruplar ve parolalar Azure AD 'den eşitlenir. Azure AD örneğinden Azure AD DS tek yönlü eşitlemesi, kullanıcıların aynı kurumsal kimlik bilgilerini kullanarak kümede oturum açmasını sağlar. 

Daha fazla bilgi için bkz. [Azure AD DS kullanarak HDInsight KÜMELERINI ESP Ile yapılandırma](./apache-domain-joined-configure-using-azure-adds.md).

### <a name="on-premises-active-directory-or-active-directory-on-iaas-vms"></a>IaaS VM 'lerinde şirket içi Active Directory veya Active Directory

Etki alanınız için bir şirket içi Active Directory örneğiniz veya daha karmaşık Active Directory kurulumlarınız varsa, Azure AD Connect kullanarak bu kimlikleri Azure AD 'ye eşitleyebilirsiniz. Daha sonra, bu Active Directory kiracısında Azure AD DS 'yi etkinleştirebilirsiniz. 

Kerberos, parola karmalarını kullandığından, [Azure AD DS Parola karması eşitlemesini etkinleştirmeniz](../../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md)gerekir. 

Active Directory Federasyon Hizmetleri (AD FS) (AD FS) ile Federasyon kullanıyorsanız, parola karma eşitlemesini etkinleştirmeniz gerekir. (Önerilen bir kurulum için [Bu videoya](https://youtu.be/qQruArbu2Ew)bakın.) Parola karması eşitleme, AD FS altyapınızın başarısız olması durumunda olağanüstü durum kurtarma ile yardımcı olur ve ayrıca sızdırılan kimlik bilgisi koruma sağlamaya yardımcı olur. Daha fazla bilgi için bkz. [Azure AD Connect Sync ile parola karma eşitlemesini etkinleştirme](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md). 

Azure AD ve Azure AD DS olmadan yalnızca IaaS VM 'lerinde şirket içi Active Directory veya Active Directory kullanmak, HDInsight kümeleri için ESP ile desteklenen bir yapılandırma değildir.

Federasyon kullanılıyorsa ve parola karmaları doğru şekilde eşitlendiğinde, ancak kimlik doğrulama hatalarıyla karşılaşırsanız, PowerShell hizmet sorumlusu için bulut parolası kimlik doğrulamasının etkinleştirilip etkinleştirilmediğini denetleyin. Aksi takdirde, Azure AD kiracınız için bir [giriş bölgesi bulma (HRD) ilkesi](../../active-directory/manage-apps/configure-authentication-for-federated-users-portal.md) ayarlamanız gerekir. HRD ilkesini denetlemek ve ayarlamak için:

1. Önizleme [Azure AD PowerShell modülünü](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2)yükler.

   ```powershell
   Install-Module AzureAD
   ```

2. Genel yönetici (kiracı yöneticisi) kimlik bilgilerini kullanarak bağlanın.
   
   ```powershell
   Connect-AzureAD
   ```

3. Microsoft Azure PowerShell hizmet sorumlusunun zaten oluşturulup oluşturulmadıysa emin olun.

   ```powershell
   Get-AzureADServicePrincipal -SearchString "Microsoft Azure Powershell"
   ```

4. Yoksa, hizmet sorumlusu oluşturun.

   ```powershell
   $powershellSPN = New-AzureADServicePrincipal -AppId 1950a258-227b-4e31-a9cf-717495945fc2
   ```

5. İlkeyi oluşturun ve bu hizmet sorumlusu 'na ekleyin.

   ```powershell
    # Determine whether policy exists
    Get-AzureADPolicy | Where {$_.DisplayName -eq "EnableDirectAuth"}

    # Create if not exists
    $policy = New-AzureADPolicy `
        -Definition @('{"HomeRealmDiscoveryPolicy":{"AllowCloudPasswordValidation":true}}') `
        -DisplayName "EnableDirectAuth" `
        -Type "HomeRealmDiscoveryPolicy"

    # Determine whether a policy for the service principal exist
    Get-AzureADServicePrincipalPolicy `
        -Id $powershellSPN.ObjectId
    
    # Add a service principal policy if not exist
    Add-AzureADServicePrincipalPolicy `
        -Id $powershellSPN.ObjectId `
        -refObjectID $policy.ID
   ```

## <a name="next-steps"></a>Sonraki adımlar

* [HDInsight kümelerini ESP ile yapılandırma](apache-domain-joined-configure-using-azure-adds.md)
* [HDInsight kümeleri için ESP ile Apache Hive ilkeleri yapılandırma](apache-domain-joined-run-hive.md)
* [HDInsight kümelerini ESP ile yönetme](apache-domain-joined-manage.md) 
