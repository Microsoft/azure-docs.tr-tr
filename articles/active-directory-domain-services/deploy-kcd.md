---
title: Azure AD Domain Services için Kerberos kısıtlanmış temsili | Microsoft Docs
description: Azure Active Directory Domain Services yönetilen bir etki alanında kaynak tabanlı Kerberos kısıtlı temsilcisini (KCD) etkinleştirmeyi öğrenin.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: iainfou
ms.openlocfilehash: 89bc690e5a8c8d24d7732dd4e12f70a9f1f368af
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/10/2019
ms.locfileid: "70842663"
---
# <a name="configure-kerberos-constrained-delegation-kcd-in-azure-active-directory-domain-services"></a>Azure Active Directory Domain Services 'de Kerberos kısıtlanmış temsilcisini (KCD) yapılandırma

Uygulamaları çalıştırırken, bu uygulamaların farklı bir kullanıcı bağlamında kaynaklara erişmesi için bir gereksinim olabilir. Active Directory Domain Services (AD DS), bu kullanım örneğini sağlayan *Kerberos temsili* adlı bir mekanizmayı destekler. Kerberos *kısıtlı* temsilcisi (KCD), bu mekanizmaya, kullanıcı bağlamında erişilebilen belirli kaynakları tanımlamak için oluşturulur. Azure Active Directory Domain Services (Azure AD DS) yönetilen etki alanları, geleneksel şirket içi AD DS ortamlarında daha güvenli bir şekilde kilitlidir, bu nedenle daha güvenli *kaynak tabanlı* bir KCD kullanın.

Bu makalede, Azure AD DS yönetilen bir etki alanında Kaynak-Savunma Kerberos kısıtlı temsilcisini yapılandırma açıklanmaktadır.

## <a name="prerequisites"></a>Önkoşullar

Bu makaleyi tamamlayabilmeniz için aşağıdaki kaynaklara ihtiyacınız vardır:

* Etkin bir Azure aboneliği.
    * Azure aboneliğiniz yoksa [bir hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Abonelikle ilişkili bir Azure Active Directory kiracısı, şirket içi bir dizinle veya yalnızca bulut diziniyle eşitlenir.
    * Gerekirse, [bir Azure Active Directory kiracı oluşturun][create-azure-ad-tenant] veya [bir Azure aboneliğini hesabınızla ilişkilendirin][associate-azure-ad-tenant].
* Azure AD kiracınızda etkinleştirilmiş ve yapılandırılmış Azure Active Directory Domain Services yönetilen bir etki alanı.
    * Gerekirse, [bir Azure Active Directory Domain Services örneği oluşturun ve yapılandırın][create-azure-ad-ds-instance].
* Azure AD DS yönetilen etki alanına katılmış bir Windows Server Yönetim sanal makinesi.
    * Gerekirse, [bir Windows Server VM 'si oluşturup yönetilen bir etki alanına katmak][create-join-windows-vm] için öğreticiyi doldurun ve [AD DS yönetim araçlarını][tutorial-create-management-vm]ekleyin.
* Azure AD kiracınızda *Azure AD DC Administrators* grubunun üyesi olan bir kullanıcı hesabı.

## <a name="kerberos-constrained-delegation-overview"></a>Kerberos kısıtlı temsilciye genel bakış

Kerberos temsili, bir hesabın kaynaklara erişmek için başka bir hesabın kimliğine bürünmesini sağlar. Örneğin, arka uç Web bileşenine erişen bir Web uygulaması, arka uç bağlantısı yaptığında kendisini farklı bir kullanıcı hesabı olarak taklit edebilir. Kimliğe bürünme hesabının erişebileceği kaynakları sınırmadığı için Kerberos temsili güvenli değildir.

Kerberos kısıtlanmış temsili (KCD), belirli bir sunucunun veya uygulamanın başka bir kimlik kimliğine bürünerek bağlanabileceği Hizmetleri veya kaynakları kısıtlar. Geleneksel KCD, bir hizmet için etki alanı hesabı yapılandırmak için etki alanı yöneticisi ayrıcalıklarına gerek duyar ve hesabı tek bir etki alanında çalışacak şekilde kısıtlar. Geleneksel KCD 'de de bazı sorunlar vardır. Örneğin, önceki işletim sistemlerinde, hizmet yöneticisinin sahip oldukları kaynak hizmetleri için hangi ön uç hizmetlerin temsilci olarak olduğunu bilmemiz için kullanışlı bir yolu yoktur. Kaynak hizmetine temsilci olabilecek herhangi bir ön uç hizmeti potansiyel bir saldırı noktasıdır. Kaynak hizmetleri için temsilci olarak yapılandırılmış bir ön uç hizmeti barındıran bir sunucunun güvenliği tehlikeye girerse, kaynak hizmetleri de tehlikeye girebilir.

Azure AD DS yönetilen bir etki alanında etki alanı yöneticisi ayrıcalıklarına sahip değilsiniz. Sonuç olarak, yönetilen bir etki alanında AD DS Azure 'da geleneksel hesap tabanlı KCD yapılandırılamaz. Kaynak tabanlı KCD, bunun yerine daha da güvenli bir şekilde kullanılabilir.

### <a name="resource-based-kcd"></a>Kaynak tabanlı KCD

Windows Server 2012 ve üzeri, hizmet yöneticilerine hizmeti için kısıtlanmış temsilciyi yapılandırma olanağı sağlar. Bu model kaynak tabanlı KCD olarak bilinir. Bu yaklaşımda, arka uç hizmet yöneticisi, belirli ön uç hizmetlerinin KCD kullanmasını sağlayabilir veya reddedebilir.

Kaynak tabanlı KCD, PowerShell kullanılarak yapılandırılır. Kimliğe bürünme hesabının bir bilgisayar hesabı mı yoksa bir kullanıcı hesabı/hizmet hesabı mı olduğuna bağlı olarak [set-ADComputer][Set-ADComputer] veya [set-aduser][Set-ADUser] cmdlet 'lerini kullanırsınız.

## <a name="configure-resource-based-kcd-for-a-computer-account"></a>Bilgisayar hesabı için kaynak tabanlı KCD 'YI yapılandırma

Bu senaryoda, *contoso-WebApp.contoso.com*adlı bilgisayarda çalışan bir Web uygulamasına sahip olduğunu varsayalım. Web uygulamasının, etki alanı kullanıcıları bağlamında *contoso-api.contoso.com* adlı bilgisayarda çalışan BIR Web API 'sine erişmesi gerekir. Bu senaryoyu yapılandırmak için aşağıdaki adımları izleyin:

1. [Özel BIR OU oluşturun](create-ou.md). Bu özel OU 'yu Azure AD DS yönetilen etki alanındaki kullanıcılara yönetmek için izinler atayabilirsiniz.
1. [Etki alanı-][create-join-windows-vm]hem Web uygulamasını çalıştıran hem de Web API 'sini çalıştıran sanal makineleri Azure AD DS tarafından yönetilen etki alanına ekleyin. Önceki adımda özel OU 'da bu bilgisayar hesaplarını oluşturun.

    > [!NOTE]
    > Web uygulaması ve Web API 'SI için bilgisayar hesapları, kaynak tabanlı KCD 'yi yapılandırma izninizin olduğu özel bir OU 'da olmalıdır. Yerleşik *AAD DC bilgisayarları* kapsayıcısında bir bilgisayar hesabı için kaynak tabanlı KCD 'yi yapılandıramazsınız.

1. Son olarak, [set-ADComputer][Set-ADComputer] PowerShell cmdlet 'ini kullanarak kaynak tabanlı KCD 'yi yapılandırın. Etki alanına katılmış Yönetim sanal makinenizde ve *Azure AD DC Yöneticiler* grubunun bir üyesi olan kullanıcı hesabı olarak oturum açmış olarak, aşağıdaki cmdlet 'leri çalıştırın. Gerektiğinde kendi bilgisayar adlarınızı sağlayın:
    
    ```powershell
    $ImpersonatingAccount = Get-ADComputer -Identity contoso-webapp.contoso.com
    Set-ADComputer contoso-api.contoso.com -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
    ```

## <a name="configure-resource-based-kcd-for-a-user-account"></a>Bir kullanıcı hesabı için kaynak tabanlı KCD 'YI yapılandırma

Bu senaryoda, *appsvc*adlı bir hizmet hesabı olarak çalışan bir Web uygulamanız olduğunu varsayalım. Web uygulamasının, etki alanı kullanıcıları bağlamında *backendsvc* adlı bir hizmet hesabı olarak çalışan BIR Web API 'sine erişmesi gerekir. Bu senaryoyu yapılandırmak için aşağıdaki adımları izleyin:

1. [Özel BIR OU oluşturun](create-ou.md). Bu özel OU 'yu Azure AD DS yönetilen etki alanındaki kullanıcılara yönetmek için izinler atayabilirsiniz.
1. [Etki alanı-][create-join-windows-vm] arka uç Web API 'si/kaynağını çalıştıran sanal makineleri Azure AD DS tarafından yönetilen etki alanına ekleyin. Kendi bilgisayar hesabını özel OU içinde oluşturun.
1. Web uygulamasını özel OU içinde çalıştırmak için kullanılan hizmet hesabını (örneğin, ' appsvc ') oluşturun.

    > [!NOTE]
    > Yine, Web API sanal makinesi için bilgisayar hesabı ve Web uygulaması için hizmet hesabı, kaynak tabanlı KCD 'yi yapılandırma izninizin olduğu özel bir OU 'da olmalıdır. Yerleşik *AAD DC Computers* veya *AAD DC Users* kapsayıcılarındaki hesaplar için kaynak tabanlı KCD 'yi yapılandıramazsınız. Bu ayrıca kaynak tabanlı KCD 'yi ayarlamak için Azure AD 'den eşitlenen Kullanıcı hesaplarını kullanamayacağı anlamına gelir. Azure AD DS 'de özel olarak oluşturulan hizmet hesaplarını oluşturmanız ve kullanmanız gerekir.

1. Son olarak, [set-ADUser][Set-ADUser] PowerShell cmdlet 'ini kullanarak kaynak tabanlı KCD 'yi yapılandırın. Etki alanına katılmış Yönetim sanal makinenizde ve *Azure AD DC Yöneticiler* grubunun bir üyesi olan kullanıcı hesabı olarak oturum açmış olarak, aşağıdaki cmdlet 'leri çalıştırın. Gerektiğinde kendi hizmet adlarınızı sağlayın:

    ```powershell
    $ImpersonatingAccount = Get-ADUser -Identity appsvc
    Set-ADUser backendsvc -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
    ```

## <a name="next-steps"></a>Sonraki adımlar

Active Directory Domain Services ' de temsilcinin nasıl çalıştığı hakkında daha fazla bilgi edinmek için bkz. [Kerberos kısıtlanmış temsilciye genel bakış][kcd-technet].

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[Set-ADComputer]: /powershell/module/addsadministration/set-adcomputer
[Set-ADUser]: /powershell/module/addsadministration/set-aduser

<!-- EXTERNAL LINKS -->
[kcd-technet]: https://technet.microsoft.com/library/jj553400.aspx
