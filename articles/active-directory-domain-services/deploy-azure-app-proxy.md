---
title: Azure AD Domain Services için Azure AD Uygulama Ara Sunucusu Dağıtma | Microsoft Docs
description: Azure Active Directory Domain Services yönetilen bir etki alanında Azure Active Directory Uygulama Ara Sunucusu dağıtarak ve yapılandırarak uzak çalışanlar için iç uygulamalara güvenli erişim sağlama hakkında bilgi edinin
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 11/6/2019
ms.author: iainfou
ms.openlocfilehash: c0fcb8c2c5f9afa7fabe2ffa63a715ec24aa4a26
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73720518"
---
# <a name="deploy-azure-ad-application-proxy-for-secure-access-to-internal-applications-in-an-azure-ad-domain-services-managed-domain"></a>Azure AD Domain Services yönetilen bir etki alanında iç uygulamalara güvenli erişim için Azure AD Uygulama Ara Sunucusu Dağıtma

Azure AD Domain Services (Azure AD DS) ile şirket içinde çalışan eski uygulamaları Azure 'a kopyalayıp öteye taşıyabilirsiniz. Azure Active Directory (AD) uygulama proxy 'Si daha sonra, Azure AD DS yönetilen bir etki alanının iç uygulamalarını güvenli bir şekilde yayımlayarak, internet üzerinden erişilebilmesi için uzak çalışanları desteklemenize yardımcı olur.

Azure AD Uygulama Ara Sunucusu yeni başladıysanız ve daha fazla bilgi edinmek istiyorsanız bkz. [iç uygulamalara güvenli uzaktan erişim sağlama](../active-directory/manage-apps/application-proxy.md).

Bu makalede, Azure AD DS yönetilen bir etki alanındaki uygulamalara güvenli erişim sağlamak için bir Azure AD Uygulama Ara Sunucusu Bağlayıcısı oluşturma ve yapılandırma gösterilmektedir.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Başlamadan önce

Bu makaleyi tamamlayabilmeniz için aşağıdaki kaynaklar ve ayrıcalıklar gereklidir:

* Etkin bir Azure aboneliği.
    * Azure aboneliğiniz yoksa [bir hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Abonelikle ilişkili bir Azure Active Directory kiracısı, şirket içi bir dizinle veya yalnızca bulut diziniyle eşitlenir.
    * Gerekirse, [bir Azure Active Directory kiracı oluşturun][create-azure-ad-tenant] veya [bir Azure aboneliğini hesabınızla ilişkilendirin][associate-azure-ad-tenant].
    * Azure AD Uygulama Ara Sunucusu kullanmak için bir **Azure AD Premium Lisansı** gerekir.
* Azure AD kiracınızda etkinleştirilmiş ve yapılandırılmış Azure Active Directory Domain Services yönetilen bir etki alanı.
    * Gerekirse, [bir Azure Active Directory Domain Services örneği oluşturun ve yapılandırın][create-azure-ad-ds-instance].

## <a name="create-a-domain-joined-windows-vm"></a>Etki alanına katılmış Windows VM oluşturma

Trafiği ortamınızda çalışan uygulamalara yönlendirmek için Azure AD Uygulama Ara Sunucusu Bağlayıcısı bileşenini yüklersiniz. Bu Azure AD Uygulama Ara Sunucusu Bağlayıcısı, Azure AD DS tarafından yönetilen etki alanına katılmış Windows Server sanal makinelerinde (VM) yüklü olmalıdır. Bazı uygulamalarda, her birinin bağlayıcının yüklü olduğu birden çok sunucu dağıtabilirsiniz. Bu dağıtım seçeneği, daha fazla kullanılabilirlik sağlar ve daha ağır kimlik doğrulama yüklerini işlemeye yardımcı olur.

Azure AD Uygulama Ara Sunucusu bağlayıcısını çalıştıran VM aynı veya Azure AD DS etkinleştirdiğiniz eşlenmiş bir sanal ağ üzerinde olmalıdır. Daha sonra uygulama proxy 'Si kullanarak yayımladığınız uygulamaları barındıran VM 'Lerin aynı Azure sanal ağına dağıtılması gerekir.

Azure AD Uygulama Ara Sunucusu Bağlayıcısı için bir sanal makine oluşturmak için aşağıdaki adımları izleyin:

1. [Özel BIR OU oluşturun](create-ou.md). Bu özel OU 'yu Azure AD DS yönetilen etki alanındaki kullanıcılara yönetmek için izinler atayabilirsiniz. Azure AD Uygulama Ara Sunucusu ve uygulamalarınızı çalıştıran VM 'Ler, varsayılan *AAD DC bilgisayarları* OU 'ya değil, özel OU 'nun bir parçası olmalıdır.
1. [Etki alanı-][create-join-windows-vm]Azure AD uygulama ara sunucusu bağlayıcısını çalıştıran bir sanal makineyi ve uygulamalarınızı çalıştıran Azure AD DS yönetilen etki alanına ekleyin. Önceki adımda özel OU 'da bu bilgisayar hesaplarını oluşturun.

## <a name="download-the-azure-ad-application-proxy-connector"></a>Azure AD Uygulama Ara Sunucusu bağlayıcısını indirin

Azure AD Uygulama Ara Sunucusu bağlayıcısını indirmek için aşağıdaki adımları gerçekleştirin. Yüklediğiniz kurulum dosyası, sonraki bölümde uygulama proxy 'Si sanal makinenize kopyalanır.

1. Azure AD 'de *Kurumsal Yönetici* izinlerine sahip bir kullanıcı hesabıyla [Azure Portal](https://portal.azure.com) oturum açın.
1. Portalın üst kısmındaki **Azure Active Directory** arayıp seçin, sonra **Kurumsal uygulamalar**' ı seçin.
1. Sol taraftaki menüden **uygulama proxy 'si** ' ni seçin. İlk bağlayıcınızı oluşturmak ve uygulama proxy 'Sini etkinleştirmek için **bağlayıcıyı indirmek**üzere bağlantıyı seçin.
1. İndirme sayfasında, lisans koşullarını ve gizlilik sözleşmesi 'ni kabul edin, ardından **terimleri kabul et & indir**' i seçin.

    ![Azure AD Uygulaması Proxy bağlayıcısını indirin](./media/app-proxy/download-app-proxy-connector.png)

## <a name="install-and-register-the-azure-ad-application-proxy-connector"></a>Azure AD Uygulama Ara Sunucusu bağlayıcısını yükleyip kaydetme

Azure AD Uygulama Ara Sunucusu Bağlayıcısı olarak kullanılmak üzere bir VM ile, şimdi Azure portal indirilen kurulum dosyasını kopyalayın ve çalıştırın.

1. Azure AD Uygulama Ara Sunucusu Bağlayıcısı kurulum dosyasını sanal makinenize kopyalayın.
1. *Aadapplicationproxyconnectorınstaller. exe*gibi kurulum dosyasını çalıştırın. Yazılım lisans koşulları 'nı kabul edin.
1. Yüklemesi sırasında, bağlayıcıyı Azure AD dizininize uygulama proxy 'Si ile kaydetmeniz istenir.
   * Azure AD dizininizde genel yönetici için kimlik bilgilerini sağlayın. Azure AD Genel yönetici kimlik bilgileri, portalda Azure kimlik bilgilerinizle farklı olabilir

        > [!NOTE]
        > Bağlayıcıyı kaydetmek için kullanılan genel yönetici hesabı, uygulama ara sunucusu hizmetini etkinleştirdiğiniz dizine ait olmalıdır.
        >
        > Örneğin, Azure AD etki alanı *contoso.com*ise, genel yönetici bu etki alanında `admin@contoso.com` veya geçerli bir diğer ad olmalıdır.

   * Bağlayıcıyı yüklediğiniz VM için Internet Explorer Artırılmış Güvenlik Yapılandırması açıksa, kayıt ekranı engellenebilir. Erişime izin vermek için, hata iletisindeki yönergeleri uygulayın veya yüklemenin işlemi sırasında Internet Explorer gelişmiş güvenliği ' ni kapatın.
   * Bağlayıcı kaydı başarısız olursa bkz. [uygulama proxy 'Si sorunlarını giderme](../active-directory/manage-apps/application-proxy-troubleshoot.md).
1. Kurulumun sonunda, giden ara sunucuya sahip ortamlar için bir Note gösterilir. Azure AD Uygulama Ara Sunucusu bağlayıcısını giden proxy üzerinden çalışacak şekilde yapılandırmak için, `C:\Program Files\Microsoft AAD App Proxy connector\ConfigureOutBoundProxy.ps1`gibi, belirtilen betiği çalıştırın.
1. Azure portal uygulama proxy 'si sayfasında, yeni bağlayıcı aşağıdaki örnekte gösterildiği gibi *etkin*durumuyla listelenir:

    ![Azure portal etkin olarak gösterilen yeni Azure AD Uygulama Ara Sunucusu Bağlayıcısı](./media/app-proxy/connected-app-proxy.png)

> [!NOTE]
> Azure AD Uygulama Ara Sunucusu kimlik doğrulaması yapan uygulamalar için yüksek kullanılabilirlik sağlamak üzere, bağlayıcıları birden çok VM 'ye yükleyebilirsiniz. Bağlayıcıyı Azure AD DS yönetilen etki alanına katılmış diğer sunuculara yüklemek için önceki bölümde listelenen adımları yineleyin.

## <a name="enable-resource-based-kerberos-constrained-delegation"></a>Kaynak tabanlı Kerberos kısıtlı temsilcisini etkinleştirin

Tümleşik Windows kimlik doğrulaması (ıWA) kullanarak uygulamalarınızda çoklu oturum açma özelliğini kullanmak istiyorsanız, Azure AD Uygulama Ara Sunucusu bağlayıcılarına kullanıcıları taklit etmek ve adına belirteç göndermek ve almak için izin verin. Bu izinleri vermek için, bağlayıcı için Kerberos kısıtlanmış temsilciyi (KCD) Azure AD DS yönetilen etki alanındaki kaynaklara erişecek şekilde yapılandırırsınız. Azure AD DS yönetilen bir etki alanında etki alanı yöneticisi ayrıcalıklarına sahip olmadığınız için, yönetilen bir etki alanında geleneksel hesap düzeyi KCD yapılandırılamaz. Bunun yerine, kaynak tabanlı KCD 'yi kullanın.

Daha fazla bilgi için, [Azure Active Directory Domain Services Içinde Kerberos kısıtlanmış temsilcisini (KCD) yapılandırma](deploy-kcd.md)konusuna bakın.

> [!NOTE]
> Aşağıdaki PowerShell cmdlet 'lerini çalıştırmak için Azure AD kiracınızda *Azure AD DC Administrators* grubunun üyesi olan bir kullanıcı hesabında oturum açmış olmanız gerekir.
>
> Uygulama proxy Bağlayıcısı sanal makinelerinizin ve uygulama VM 'lerinin bilgisayar hesapları, kaynak tabanlı KCD 'yi yapılandırma izninizin olduğu özel bir OU 'da olmalıdır. Yerleşik *AAD DC bilgisayarları* kapsayıcısında bir bilgisayar hesabı için kaynak tabanlı KCD 'yi yapılandıramazsınız.

Azure AD Uygulama Ara Sunucusu bağlayıcısının yüklü olduğu bilgisayarın ayarlarını almak için [Get-ADComputer][Get-ADComputer] komutunu kullanın. Etki alanına katılmış Yönetim sanal makinenizde ve *Azure AD DC Yöneticiler* grubunun bir üyesi olan kullanıcı hesabı olarak oturum açmış olarak, aşağıdaki cmdlet 'leri çalıştırın.

Aşağıdaki örnek, *appproxy.contoso.com*adlı bilgisayar hesabı hakkında bilgi alır. Önceki adımlarda yapılandırılan Azure AD Uygulama Ara Sunucusu sanal makinesi için kendi bilgisayar adınızı girin.

```powershell
$ImpersonatingAccount = Get-ADComputer -Identity appproxy.contoso.com
```

Azure AD Uygulama Ara Sunucusu arka plandaki uygulamaları çalıştıran her uygulama sunucusu için, kaynak tabanlı KCD 'yi yapılandırmak üzere [set-ADComputer][Set-ADComputer] PowerShell cmdlet 'ini kullanın. Aşağıdaki örnekte, Azure AD Uygulama Ara Sunucusu bağlayıcısının *appserver.contoso.com* bilgisayarı kullanma izinleri verilmiştir:

```powershell
Set-ADComputer appserver.contoso.com -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
```

Birden çok Azure AD Uygulama Ara Sunucusu Bağlayıcısı dağıtırsanız, her bağlayıcı örneği için kaynak tabanlı KCD 'yi yapılandırmanız gerekir.

## <a name="next-steps"></a>Sonraki adımlar

Azure AD Uygulama Ara Sunucusu ile tümleştirilmiş Azure AD DS ile, kullanıcıların erişebileceği uygulamalar yayımlayın. Daha fazla bilgi için bkz. [Azure AD uygulama ara sunucusu kullanarak uygulama yayımlama](../active-directory/manage-apps/application-proxy-publish-azure-portal.md).

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[azure-bastion]: ../bastion/bastion-create-host-portal.md
[Get-ADComputer]: /powershell/module/addsadministration/get-adcomputer
[Set-ADComputer]: /powershell/module/addsadministration/set-adcomputer
