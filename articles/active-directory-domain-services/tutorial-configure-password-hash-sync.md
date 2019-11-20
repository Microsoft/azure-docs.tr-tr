---
title: Azure AD Domain Services için parola karma eşitlemesini etkinleştir | Microsoft Docs
description: Bu öğreticide, Azure Active Directory Domain Services yönetilen bir etki alanına Azure AD Connect kullanarak parola karması eşitlemesini nasıl etkinleştireceğinizi öğrenin.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 10/30/2019
ms.author: iainfou
ms.openlocfilehash: 41e61376d12d447dd480a39ef7200db6af7cca89
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73172856"
---
# <a name="tutorial-enable-password-synchronization-in-azure-active-directory-domain-services-for-hybrid-environments"></a>Öğretici: karma ortamlarda Azure Active Directory Domain Services parola eşitlemeyi etkinleştirme

Karma ortamlarda, bir Azure Active Directory (Azure AD) kiracısı, Azure AD Connect kullanılarak şirket içi Active Directory Domain Services (AD DS) ortamıyla eşitlenmek üzere yapılandırılabilir. Varsayılan olarak Azure AD Connect, eski NT LAN Manager (NTLM) ve Azure Active Directory Domain Services için gereken Kerberos parola karmalarını (Azure AD DS) eşitlemez.

Azure AD DS 'yi şirket içi AD DS ortamından eşitlenen hesaplarla kullanmak için, NTLM ve Kerberos kimlik doğrulaması için gereken parola karmalarını eşitlemek üzere Azure AD Connect yapılandırmanız gerekir. Azure AD Connect yapılandırıldıktan sonra, şirket içi hesap oluşturma veya parola değiştirme olayı da eski parola karmalarını Azure AD ile eşitler.

Şirket içi AD DS ortamı olmayan salt bulut hesapları kullanıyorsanız, bu adımları gerçekleştirmeniz gerekmez.

Bu öğreticide şunları öğrenirsiniz:

> [!div class="checklist"]
> * Eski NTLM ve Kerberos parola karmalarının neden olması gerekir
> * Azure AD Connect için eski parola karması eşitlemesini yapılandırma

Azure aboneliğiniz yoksa başlamadan önce [bir hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlayabilmeniz için aşağıdaki kaynaklara ihtiyacınız vardır:

* Etkin bir Azure aboneliği.
    * Azure aboneliğiniz yoksa [bir hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Azure AD Connect kullanılarak şirket içi dizin ile eşitlenen aboneliğinizle ilişkili bir Azure Active Directory kiracısı.
    * Gerekirse, [bir Azure Active Directory kiracı oluşturun][create-azure-ad-tenant] veya [bir Azure aboneliğini hesabınızla ilişkilendirin][associate-azure-ad-tenant].
    * Gerekirse [Parola karması eşitlemesi için Azure AD Connect etkinleştirin][enable-azure-ad-connect].
* Azure AD kiracınızda etkinleştirilmiş ve yapılandırılmış Azure Active Directory Domain Services yönetilen bir etki alanı.
    * Gerekirse, [bir Azure Active Directory Domain Services örneği oluşturun ve yapılandırın][create-azure-ad-ds-instance].

## <a name="password-hash-synchronization-using-azure-ad-connect"></a>Azure AD Connect kullanarak parola karması eşitlemesi

Azure AD Connect, Kullanıcı hesapları ve gruplar gibi nesneleri şirket içi AD DS ortamından bir Azure AD kiracısına eşleştirmek için kullanılır. İşlem kapsamında, Parola karması eşitlemesi hesapların şirket içi AD DS ortamında ve Azure AD 'de aynı parolayı kullanmasını sağlar.

Yönetilen etki alanındaki kullanıcıların kimliğini doğrulamak için Azure AD DS, NTLM ve Kerberos kimlik doğrulaması için uygun bir biçimde parola karmaları gerektirir. Azure AD, kiracınız için Azure AD DS etkinleştirene kadar parola karmalarını NTLM veya Kerberos kimlik doğrulaması için gereken biçimde depolamaz. Azure AD, güvenlik nedenleriyle şifresiz metin biçiminde hiçbir parola kimlik bilgisi depolamaz. Bu nedenle, Azure AD kullanıcıların mevcut kimlik bilgilerini temel alarak bu NTLM veya Kerberos parola karmalarını otomatik olarak üretemiyor.

Azure AD Connect, Azure AD DS için gerekli NTLM veya Kerberos parola karmalarını eşitleyecek şekilde yapılandırılabilir. [Parola karması eşitlemesi için Azure AD Connect etkinleştirme][enable-azure-ad-connect]adımlarını tamamladığınızdan emin olun. Azure AD Connect var olan bir örneğiniz varsa, NTLM ve Kerberos için eski parola karmalarını eşitlediğinizden emin olmak için [en son sürüme indirin ve güncelleştirin][azure-ad-connect-download] . Bu işlevsellik Azure AD Connect erken sürümlerinde veya eski DirSync aracıyla kullanılamaz. Azure AD Connect Version *1.1.614.0* veya üzeri gereklidir.

## <a name="enable-synchronization-of-password-hashes"></a>Parola karmalarının eşitlenmesini etkinleştir

Azure AD Connect yüklendi ve Azure AD ile eşitlenecek şekilde yapılandırıldıysa, artık NTLM ve Kerberos için eski parola karması eşitlemesini yapılandırın. Gerekli ayarları yapılandırmak ve ardından Azure AD 'ye tam parola eşitlemeyi başlatmak için bir PowerShell betiği kullanılır. Azure AD Connect Parola karması eşitleme işlemi tamamlandığında, kullanıcılar, eski NTLM veya Kerberos parola karmaları kullanan Azure AD DS aracılığıyla uygulamalarda oturum açabilirler.

1. Azure AD Connect yüklü bilgisayarda, Başlat menüsünden, **Azure AD Connect > eşitleme hizmetini**açın.
1. **Bağlayıcılar** sekmesini seçin. Şirket içi AD DS ortamı ile Azure AD arasında eşitleme oluşturmak için kullanılan bağlantı bilgileri listelenir.

    **Tür** , Azure AD Bağlayıcısı için *Windows Azure Active Directory (Microsoft)* veya şirket içi AD DS Bağlayıcısı için *Active Directory Domain Services* gösterir. Bir sonraki adımda PowerShell betiğindeki kullanılacak bağlayıcı adlarını bir yere göz önünde alın.

    ![Bağlayıcı adlarını eşitlenmiş Service Manager listeleyin](media/tutorial-configure-password-hash-sync/service-sync-manager.png)

    Bu örnek ekran görüntüsünde aşağıdaki bağlayıcılar kullanılır:

    * Azure AD Bağlayıcısı adı *contoso.onmicrosoft.com-AAD*
    * Şirket içi AD DS Bağlayıcısı *OnPrem.contoso.com* olarak adlandırılmıştır

1. Aşağıdaki PowerShell betiğini kopyalayıp Azure AD Connect yüklü bilgisayara yapıştırın. Betik, eski parola karmalarını içeren tam bir parola eşitlemesini tetikler. `$azureadConnector` ve `$adConnector` değişkenlerini önceki adımdaki bağlayıcı adlarıyla güncelleştirin.

    Şirket içi hesap NTLM ve Kerberos parola karmalarını Azure AD ile eşleştirmek için bu betiği her bir AD ormanında çalıştırın.

    ```powershell
    # Define the Azure AD Connect connector names and import the required PowerShell module
    $azureadConnector = "<CASE SENSITIVE AZURE AD CONNECTOR NAME>"
    $adConnector = "<CASE SENSITIVE AD DS CONNECTOR NAME>"
    Import-Module "C:\Program Files\Microsoft Azure Active Directory Connect\AdSyncConfig\AdSyncConfig.psm1"

    # Create a new ForceFullPasswordSync configuration parameter object then
    # update the existing connector with this new configuration
    $c = Get-ADSyncConnector -Name $adConnector
    $p = New-Object Microsoft.IdentityManagement.PowerShell.ObjectModel.ConfigurationParameter "Microsoft.Synchronize.ForceFullPasswordSync", String, ConnectorGlobal, $null, $null, $null
    $p.Value = 1
    $c.GlobalParameters.Remove($p.Name)
    $c.GlobalParameters.Add($p)
    $c = Add-ADSyncConnector -Connector $c

    # Disable and re-enable Azure AD Connect to force a full password synchronization
    Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $false
    Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $true
    ```

    Hesap ve grup sayısı bakımından dizininizin boyutuna bağlı olarak, eski parola karmalarının Azure AD 'ye eşitlenmesi biraz zaman alabilir. Daha sonra, parolalar Azure AD ile eşitlendikten sonra Azure AD DS yönetilen etki alanı ile eşitlenir.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide şunları öğrendiniz:

> [!div class="checklist"]
> * Eski NTLM ve Kerberos parola karmalarının neden olması gerekir
> * Azure AD Connect için eski parola karması eşitlemesini yapılandırma

> [!div class="nextstepaction"]
> [Azure AD Domain Services yönetilen bir etki alanında eşitlemenin nasıl çalıştığını öğrenin](synchronization.md)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[enable-azure-ad-connect]: ../active-directory/hybrid/how-to-connect-install-express.md

<!-- EXTERNAL LINKS -->
[azure-ad-connect-download]: https://www.microsoft.com/download/details.aspx?id=47594
