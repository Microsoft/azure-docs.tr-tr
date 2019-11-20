---
title: Öğretici-Azure Active Directory Domain Services için bir yönetim sanal makinesi oluşturma | Microsoft Docs
description: Bu öğreticide, Azure Active Directory Domain Services örneğini yönetmek için kullandığınız bir Windows sanal makinesini nasıl oluşturacağınızı ve yapılandıracağınızı öğreneceksiniz.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 10/30/2019
ms.author: iainfou
ms.openlocfilehash: 2aec10ab07b78aaacf34340b268f9b7dfbe69eb5
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73172346"
---
# <a name="tutorial-create-a-management-vm-to-configure-and-administer-an-azure-active-directory-domain-services-managed-domain"></a>Öğretici: Azure Active Directory Domain Services yönetilen bir etki alanını yapılandırmak ve yönetmek için bir yönetim sanal makinesi oluşturma

Azure Active Directory Domain Services (AD DS), Windows Server Active Directory ile tamamen uyumlu etki alanına katılması, Grup ilkesi, LDAP ve Kerberos/NTLM kimlik doğrulaması gibi yönetilen etki alanı Hizmetleri sağlar. Bu yönetilen etki alanını, şirket içi Active Directory Domain Services etki alanı ile aynı Uzak Sunucu Yönetim Araçları (RSAT) kullanarak yönetebilirsiniz. Azure AD DS yönetilen bir hizmet olduğundan, etki alanı denetleyicilerine bağlanmak için Uzak Masaüstü Protokolü (RDP) kullanma gibi, gerçekleştiremeyecek bazı yönetim görevleri vardır.

Bu öğreticide, Azure 'da bir Windows Server VM oluşturma ve Azure AD DS yönetilen bir etki alanını yönetmek için gerekli araçları nasıl yükleyeceğiniz gösterilmektedir.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Azure AD DS yönetilen bir etki alanında kullanılabilir yönetim görevlerini anlayın
> * Windows Server VM 'ye Active Directory Yönetim Araçları 'nı yükler
> * Ortak görevleri gerçekleştirmek için Active Directory Yönetim Merkezi kullanın

Azure aboneliğiniz yoksa başlamadan önce [bir hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlayabilmeniz için aşağıdaki kaynaklar ve ayrıcalıklar gereklidir:

* Etkin bir Azure aboneliği.
    * Azure aboneliğiniz yoksa [bir hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Abonelikle ilişkili bir Azure Active Directory kiracısı, şirket içi bir dizinle veya yalnızca bulut diziniyle eşitlenir.
    * Gerekirse, [bir Azure Active Directory kiracı oluşturun][create-azure-ad-tenant] veya [bir Azure aboneliğini hesabınızla ilişkilendirin][associate-azure-ad-tenant].
* Azure AD kiracınızda etkinleştirilmiş ve yapılandırılmış Azure Active Directory Domain Services yönetilen bir etki alanı.
    * Gerekirse, ilk öğretici [bir Azure Active Directory Domain Services örneği oluşturur ve yapılandırır][create-azure-ad-ds-instance].
* Azure AD DS yönetilen etki alanına katılmış bir Windows Server VM 'si.
    * Gerekirse, önceki öğretici [bir Windows Server VM 'si oluşturur ve bunu yönetilen bir etki alanına birleştirir][create-join-windows-vm].
* Azure AD kiracınızda *Azure AD DC Administrators* grubunun üyesi olan bir kullanıcı hesabı.

## <a name="sign-in-to-the-azure-portal"></a>Azure portalında oturum açın

Bu öğreticide, Azure portal kullanarak bir yönetim sanal makinesi oluşturup yapılandırırsınız. Başlamak için öncelikle [Azure Portal](https://portal.azure.com)oturum açın.

## <a name="available-administrative-tasks-in-azure-ad-ds"></a>Azure AD DS 'de kullanılabilir yönetim görevleri

Azure AD DS, kullanıcılarınız, uygulamalarınız ve hizmetleriniz tarafından kullanılacak yönetilen bir etki alanı sağlar. Bu yaklaşım, yapabileceğiniz bazı kullanılabilir yönetim görevlerini ve yönetilen etki alanı içinde sahip olduğunuz ayrıcalıkları değiştirir. Bu görevler ve izinler, düzenli bir şirket içi Active Directory Domain Services ortamıyla deneyiminizden farklı olabilir. Ayrıca, Uzak Masaüstü kullanarak Azure AD DS yönetilen etki alanındaki etki alanı denetleyicilerine bağlanamazsınız.

### <a name="administrative-tasks-you-can-perform-on-an-azure-ad-ds-managed-domain"></a>Azure AD DS yönetilen bir etki alanında gerçekleştirebileceğiniz yönetim görevleri

*AAD DC Administrators* grubunun üyelerine Azure AD DS yönetilen etki alanı üzerinde, bunların gibi görevleri gerçekleştirmesine olanak veren ayrıcalıklar verilir:

* Makineleri yönetilen etki alanına ekleyin.
* Yönetilen etki alanındaki *Aaddc bilgisayarları* ve *Aaddc kullanıcıları* kapsayıcıları için yerleşik Grup İlkesi nesnesini (GPO) yapılandırın.
* Yönetilen etki alanında DNS’yi yönetin.
* Yönetilen etki alanında özel kuruluş birimleri (OU) oluşturun ve yönetin.
* Yönetilen etki alanına katılan bilgisayarlara yönetici erişimi edinin.

### <a name="administrative-privileges-you-dont-have-on-an-azure-ad-ds-managed-domain"></a>Azure AD DS yönetilen bir etki alanında bulunmayan yönetim ayrıcalıkları

Azure AD DS yönetilen etki alanı kilitlidir, bu nedenle etki alanında belirli yönetim görevlerini yapmak için ayrıcalıklarınız yok. Aşağıdaki örneklerden bazıları yapamıyorum görevlerdir:

* Yönetilen etki alanının şemasını genişletin.
* Uzak Masaüstü 'Nü kullanarak yönetilen etki alanı için etki alanı denetleyicilerine bağlanın.
* Yönetilen etki alanına etki alanı denetleyicileri ekleyin.
* Yönetilen etki alanı için *etki alanı yöneticisi* veya *Kuruluş Yöneticisi* ayrıcalıklarınız yok.

## <a name="sign-in-to-the-windows-server-vm"></a>Windows Server VM 'de oturum açma

Önceki öğreticide, bir Windows Server VM oluşturulup Azure AD DS yönetilen etki alanına katıldı. Yönetim araçlarını yüklemek için bu VM 'yi kullanalım. Gerekirse, [bir Windows Server VM 'si oluşturup yönetilen bir etki alanına katmak için öğreticideki adımları izleyin][create-join-windows-vm].

> [!NOTE]
> Bu öğreticide Azure AD DS yönetilen etki alanına katılmış bir Windows Server VM 'si kullanırsınız. Yönetilen etki alanına katılmış Windows 10 gibi bir Windows istemcisi de kullanabilirsiniz.
>
> Windows istemcisine yönetimsel araçların nasıl yükleneceği hakkında daha fazla bilgi için bkz. [ınstall uzak sunucu yönetim araçları (RSAT)](https://social.technet.microsoft.com/wiki/contents/articles/2202.remote-server-administration-tools-rsat-for-windows-client-and-windows-server-dsforum2wiki.aspx)

Başlamak için Windows Server VM 'sine aşağıdaki şekilde bağlanın:

1. Azure portal sol taraftaki **kaynak grupları** ' nı seçin. SANAL makinenizin oluşturulduğu kaynak grubunu ( *Myresourcegroup*gibi) seçin ve ardından VM 'yi seçin ( *myvm*gibi).
1. VM 'nin **genel bakış** penceresinde **Bağlan**' ı seçin.

    ![Azure portal Windows sanal makinesine bağlanma](./media/tutorial-create-management-vm/connect-vm.png)

    Yalnızca SSL üzerinden Azure portal erişim sağlamak için [bir Azure savunma ana bilgisayarı oluşturup kullanabilirsiniz (Şu anda önizleme aşamasında)][azure-bastion] .

1. *RDP dosyasını indirme*seçeneğini belirleyin. Bu RDP dosyasını Web tarayıcınıza kaydedin.
1. VM'nize bağlanmak için indirilen RDP dosyasını açın. İstendiğinde **Bağlan**’ı seçin.
1. *Azure AD DC Administrators* grubunun parçası olan bir kullanıcının kimlik bilgilerini ( *contoso\dee* gibi) girin
1. Oturum açma işlemi sırasında bir sertifika uyarısı görürseniz, **Evet** ' i seçin veya bağlanmaya **devam edin** .

## <a name="install-active-directory-administrative-tools"></a>Active Directory Yönetim Araçları 'nı yükler

Azure AD DS yönetilen etki alanları, Active Directory Yönetim Merkezi (ADAC) veya AD PowerShell gibi şirket içi AD DS ortamlarla aynı yönetim araçları kullanılarak yönetilir. Bu araçlar Windows Server ve istemci bilgisayarlarda Uzak Sunucu Yönetim Araçları (RSAT) özelliğinin bir parçası olarak yüklenebilir. *AAD DC yöneticileri* grubunun üyeleri, yönetilen etki alanına katılmış bir BILGISAYARDAN bu ad yönetim araçlarını kullanarak Azure AD DS yönetilen etki alanlarını uzaktan yönetebilir.

Etki alanına katılmış bir VM 'ye Active Directory Yönetim Araçları 'nı yüklemek için aşağıdaki adımları izleyin:

1. **Sunucu Yöneticisi** sanal makinede oturum açtığınızda varsayılan olarak açılmazsa **Başlat** menüsünü ve ardından **Sunucu Yöneticisi**öğesini seçin.
1. **Sunucu Yöneticisi** penceresinin *Pano* bölmesinde **rol ve Özellik Ekle**' yi seçin.
1. *Rol ve Özellik Ekleme Sihirbazı*' nın **başlamadan önce** sayfasında **İleri**' yi seçin.
1. *Yükleme türü*Için, **rol tabanlı veya özellik tabanlı yükleme** seçeneğini işaretli bırakın ve **İleri ' yi**seçin.
1. **Sunucu seçimi** sayfasında, sunucu havuzundan *MYVM.contoso.com*gibi geçerli VM 'Yi seçin ve ardından **İleri**' yi seçin.
1. **Sunucu rolleri** sayfasında, **İleri**' ye tıklayın.
1. **Özellikler** sayfasında, **uzak sunucu yönetim araçları** düğümünü genişletin ve ardından **rol yönetim araçları** düğümünü genişletin.

    Rol yönetim araçları listesinden **AD DS ve AD LDS Araçları** özelliğini seçin ve ardından **İleri**' yi seçin.

    ![Özellikler sayfasından ' AD DS ve AD LDS Araçları ' ' nı yükler](./media/tutorial-create-management-vm/install-features.png)

1. **Onay** sayfasında, **yükler**' i seçin. Yönetim araçlarının yüklenmesi bir veya iki dakika sürebilir.
1. Özellik yüklemesi tamamlandığında, **rol ve özellik ekleme** Sihirbazı 'ndan çıkmak için **Kapat** ' ı seçin.

## <a name="use-active-directory-administrative-tools"></a>Active Directory yönetim araçlarını kullanma

Yönetim Araçları yüklüyken Azure AD DS yönetilen etki alanını yönetmek için bunları nasıl kullanacağınızı görelim. Sanal makinede *AAD DC Administrators* grubunun üyesi olan bir kullanıcı hesabıyla oturum açtığınızdan emin olun.

1. **Başlat** menüsünde **Windows Yönetim Araçları**' nı seçin. Önceki adımda yüklenen AD Yönetim Araçları listelenir.

    ![Sunucuda yüklü yönetim araçlarının listesi](./media/tutorial-create-management-vm/list-admin-tools.png)

1. **Active Directory Yönetim Merkezi**seçin.
1. Azure AD DS yönetilen etki alanını araştırmak için sol bölmedeki *contoso.com*gibi etki alanı adını seçin. *Aaddc bilgisayarları* ve *Aaddc kullanıcıları* adlı iki kapsayıcı listenin en üstünde bulunur.

    ![Azure AD DS yönetilen etki alanının kullanılabilir kapsayıcılar bölümünü listeleyin](./media/tutorial-create-management-vm/active-directory-administrative-center.png)

1. Azure AD DS yönetilen etki alanına ait olan kullanıcıları ve grupları görmek için **Aaddc kullanıcıları** kapsayıcısını seçin. Azure AD kiracınızdaki Kullanıcı hesapları ve grupları bu kapsayıcıda listelenmiştir.

    Aşağıdaki örnek çıktıda, *contoso Yöneticisi* adlı bir kullanıcı hesabı ve *AAD DC yöneticileri* için bir grup bu kapsayıcıda gösterilmiştir.

    ![Active Directory Yönetim Merkezi Azure AD DS etki alanı kullanıcılarının listesini görüntüleyin](./media/tutorial-create-management-vm/list-azure-ad-users.png)

1. Azure AD DS yönetilen etki alanına katılmış bilgisayarları görmek için **Aaddc bilgisayarları** kapsayıcısını seçin. Geçerli sanal makine için *Myvm*gibi bir giriş listelenir. Azure AD DS tarafından yönetilen etki alanına katılmış tüm bilgisayarlar için bilgisayar hesapları, bu *Aaddc bilgisayarları* kapsayıcısında depolanır.

Kullanıcı hesabı parolasını sıfırlama veya grup üyeliğini yönetme gibi genel Active Directory Yönetim Merkezi eylemler kullanılabilir. Bu eylemler yalnızca Azure AD DS yönetilen etki alanında doğrudan oluşturulan kullanıcılar ve gruplar için çalışır. Kimlik bilgileri yalnızca Azure AD *'Den* Azure AD DS eşitlenir. Azure AD DS 'den Azure AD 'ye geri yazma yok. Azure AD 'den eşitlenen kullanıcılar için parolaları veya yönetilen grup üyeliklerini değiştiremezsiniz ve bu değişiklikleri geri eşitlenmiş olacak şekilde değiştirebilirsiniz.

Ayrıca, yönetim araçlarının bir parçası olarak yüklenen *Windows PowerShell için Active Directory modülünü*, Azure AD DS yönetilen etki alanında sık kullanılan eylemleri yönetmek için de kullanabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
> * Azure AD DS yönetilen bir etki alanında kullanılabilir yönetim görevlerini anlayın
> * Windows Server VM 'ye Active Directory Yönetim Araçları 'nı yükler
> * Ortak görevleri gerçekleştirmek için Active Directory Yönetim Merkezi kullanın

Azure AD DS yönetilen etki alanı ile güvenli bir şekilde etkileşimde bulunmak için güvenli basit Dizin Erişimi Protokolü 'Nü (LDAPS) etkinleştirin.

> [!div class="nextstepaction"]
> [Yönetilen etki alanınız için Güvenli LDAP yapılandırma](tutorial-configure-ldaps.md)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[azure-bastion]: ../bastion/bastion-create-host-portal.md
