---
title: Azure AD Domain Services için DNS 'i yönetme | Microsoft Docs
description: Azure Active Directory Domain Services yönetilen bir etki alanı için DNS 'yi yönetmek üzere DNS sunucusu araçlarını yüklemeyi öğrenin.
author: iainfoulds
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: iainfou
ms.openlocfilehash: c225be5a1123c89d8a470a8dea48b3c57eb893b5
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73474576"
---
# <a name="administer-dns-in-an-azure-ad-domain-services-managed-domain"></a>Azure AD Domain Services yönetilen bir etki alanında DNS 'yi yönetme

Azure Active Directory Domain Services (Azure AD DS) ' de, anahtar bileşen DNS (etki alanı adı çözümlemesi) olur. Azure AD DS, yönetilen etki alanı için ad çözümlemesi sağlayan bir DNS sunucusu içerir. Bu DNS sunucusu, hizmetin çalışmasına izin veren anahtar bileşenleri için yerleşik DNS kayıtları ve güncelleştirmeleri içerir.

Kendi uygulama ve hizmetlerinizi çalıştırırken, etki alanına katılmamış makineler için DNS kayıtları oluşturmanız, yük dengeleyiciler için sanal IP adresleri yapılandırmanız veya dış DNS ileticileri ayarlamanız gerekebilir. *AAD DC Administrators* grubuna ait kullanıcılara Azure AD DS yönetilen etkı alanında DNS yönetim ayrıcalıkları verilir ve özel DNS kayıtları oluşturabilir ve düzenleyebilir.

Karma ortamda, şirket içi AD DS ortamında yapılandırılan DNS bölgeleri ve kayıtları Azure AD DS ile eşitlenmez. Kendi DNS girdilerinizi tanımlamak ve kullanmak için Azure AD DS DNS sunucusunda kayıtlar oluşturun veya ortamınızda var olan DNS sunucularını işaret eden koşullu ileticileri kullanın.

Bu makalede, DNS sunucusunu nasıl yükleyeceğiniz ve Azure AD DS kayıtları yönetmek için DNS konsolunu nasıl kullanacağınız gösterilmektedir.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Başlamadan önce

Bu makaleyi tamamlayabilmeniz için aşağıdaki kaynaklar ve ayrıcalıklar gereklidir:

* Etkin bir Azure aboneliği.
    * Azure aboneliğiniz yoksa [bir hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Abonelikle ilişkili bir Azure Active Directory kiracısı, şirket içi bir dizinle veya yalnızca bulut diziniyle eşitlenir.
    * Gerekirse, [bir Azure Active Directory kiracı oluşturun][create-azure-ad-tenant] veya [bir Azure aboneliğini hesabınızla ilişkilendirin][associate-azure-ad-tenant].
* Azure AD kiracınızda etkinleştirilmiş ve yapılandırılmış Azure Active Directory Domain Services yönetilen bir etki alanı.
    * Gerekirse, [Azure Active Directory Domain Services bir örnek oluşturmak ve yapılandırmak][create-azure-ad-ds-instance]için öğreticiyi doldurun.
* Azure AD DS yönetilen etki alanına katılmış bir Windows Server Yönetim sanal makinesi.
    * Gerekirse, [bir Windows Server VM oluşturma ve bunu yönetilen bir etki alanına katma][create-join-windows-vm]öğreticisini doldurun.
* Azure AD kiracınızda *Azure AD DC Administrators* grubunun üyesi olan bir kullanıcı hesabı.

## <a name="install-dns-server-tools"></a>DNS sunucusu araçları 'nı yükler

Azure AD DS 'de DNS kayıtları oluşturmak ve değiştirmek için, DNS sunucusu araçları 'nı yüklemeniz gerekir. Bu araçlar, Windows Server 'da bir özellik olarak yüklenebilir. Windows istemcisine yönetimsel araçların nasıl yükleneceği hakkında daha fazla bilgi için bkz. Install [uzak sunucu yönetim araçları (RSAT)][install-rsat].

1. Yönetim sanal makinenizde oturum açın. Azure portal kullanarak bağlanma adımları için bkz. [Windows Server VM 'ye bağlanma][connect-windows-server-vm].
1. **Sunucu Yöneticisi** sanal makinede oturum açtığınızda varsayılan olarak açılmazsa **Başlat** menüsünü ve ardından **Sunucu Yöneticisi**öğesini seçin.
1. **Sunucu Yöneticisi** penceresinin *Pano* bölmesinde **rol ve Özellik Ekle**' yi seçin.
1. *Rol ve Özellik Ekleme Sihirbazı*' nın **başlamadan önce** sayfasında **İleri**' yi seçin.
1. *Yükleme türü*Için, **rol tabanlı veya özellik tabanlı yükleme** seçeneğini işaretli bırakın ve **İleri ' yi**seçin.
1. **Sunucu seçimi** sayfasında, sunucu havuzundan *MYVM.contoso.com*gibi geçerli VM 'Yi seçin ve ardından **İleri**' yi seçin.
1. **Sunucu rolleri** sayfasında, **İleri**' ye tıklayın.
1. **Özellikler** sayfasında, **uzak sunucu yönetim araçları** düğümünü genişletin ve ardından **rol yönetim araçları** düğümünü genişletin. Rol yönetim araçları listesinden **DNS sunucusu araçları** özelliğini seçin.

    ![Kullanılabilir rol yönetim araçları listesinden DNS sunucusu araçlarını yüklemeyi seçin](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-dns-tools.png)

1. **Onay** sayfasında, **yükler**' i seçin. Grup ilkesi yönetim araçlarını yüklemek bir dakika veya iki dakika sürebilir.
1. Özellik yüklemesi tamamlandığında, **rol ve özellik ekleme** Sihirbazı 'ndan çıkmak için **Kapat** ' ı seçin.

## <a name="open-the-dns-management-console-to-administer-dns"></a>DNS 'yi yönetmek için DNS Yönetim konsolunu açın

DNS sunucusu araçları yüklüyken Azure AD DS yönetilen etki alanında DNS kayıtlarını yönetebilirsiniz.

> [!NOTE]
> Azure AD DS yönetilen bir etki alanında DNS 'yi yönetmek için *AAD DC Administrators* grubunun üyesi olan bir kullanıcı hesabında oturum açmış olmanız gerekir.

1. Başlangıç ekranından **Yönetim Araçları**' nı seçin. Önceki bölümde yüklü olan **DNS** de dahil olmak üzere kullanılabilir yönetim araçlarının bir listesi gösterilir. DNS Yönetim konsolunu başlatmak için **DNS** ' yi seçin.
1. **DNS sunucusuna bağlan** iletişim kutusunda **aşağıdaki bilgisayarı**seçin ve *contoso.com*gibi yönetilen etki alanının DNS etki alanı adını girin:

    ![DNS konsolundaki Azure AD DS yönetilen etki alanına bağlanma](./media/active-directory-domain-services-admin-guide/dns-console-connect-to-domain.png)

1. DNS konsolu, belirtilen Azure AD DS yönetilen etki alanına bağlanır. Gerekli DNS girdilerinizi oluşturmak veya mevcut kayıtları gerektiği gibi düzenlemek için **Ileriye doğru arama bölgelerini** veya **geriye doğru arama bölgelerini** genişletin.

    ![DNS konsolu-etki alanını yönetme](./media/active-directory-domain-services-admin-guide/dns-console-managed-domain.png)

> [!WARNING]
> Kayıtları DNS sunucusu araçları 'nı kullanarak yönetirken, Azure AD DS tarafından kullanılan yerleşik DNS kayıtlarını silmeyin veya değiştirdiğinizden emin olun. Yerleşik DNS kayıtları, etki alanı DNS kayıtları, ad sunucusu kayıtları ve DC konumu için kullanılan diğer kayıtları içerir. Bu kayıtları değiştirirseniz, etki alanı Hizmetleri sanal ağ üzerinde kesintiye uğrasayabilir.

## <a name="next-steps"></a>Sonraki adımlar

DNS yönetimi hakkında daha fazla bilgi için [TechNet 'Teki DNS araçları makalesine](https://technet.microsoft.com/library/cc753579.aspx)bakın.

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[connect-windows-server-vm]: join-windows-vm.md#connect-to-the-windows-server-vm

<!-- EXTERNAL LINKS -->
[install-rsat]: /windows-server/remote/remote-server-administration-tools#BKMK_Thresh
