---
title: Azure AD Domain Services için DNS 'i yönetme | Microsoft Docs
description: DNS sunucusu araçlarını yüklemeyi ve Azure Active Directory Domain Services yönetilen bir etki alanı için koşullu ileticiler oluşturmayı öğrenin.
author: justinha
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: afa6920a36a5a7218571239b36815004d8f2d450
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96619360"
---
# <a name="administer-dns-and-create-conditional-forwarders-in-an-azure-active-directory-domain-services-managed-domain"></a>Azure Active Directory Domain Services yönetilen bir etki alanında DNS 'yi yönetme ve koşullu ileticiler oluşturma

Azure Active Directory Domain Services (Azure AD DS) ' de, anahtar bileşen DNS (etki alanı adı çözümlemesi) olur. Azure AD DS, yönetilen etki alanı için ad çözümlemesi sağlayan bir DNS sunucusu içerir. Bu DNS sunucusu, hizmetin çalışmasına izin veren anahtar bileşenleri için yerleşik DNS kayıtları ve güncelleştirmeleri içerir.

Kendi uygulama ve hizmetlerinizi çalıştırırken, etki alanına katılmamış makineler için DNS kayıtları oluşturmanız, yük dengeleyiciler için sanal IP adresleri yapılandırmanız veya dış DNS ileticileri ayarlamanız gerekebilir. *AAD DC Administrators* grubuna ait kullanıcılara Azure AD DS yönetilen etkı alanında DNS yönetim ayrıcalıkları verilir ve özel DNS kayıtları oluşturabilir ve düzenleyebilir.

Karma ortamda, şirket içi AD DS ortamı gibi diğer DNS ad alanlarında yapılandırılan DNS bölgeleri ve kayıtları, yönetilen etki alanı ile eşitlenmez. Diğer DNS ad alanlarındaki adlandırılmış kaynakları çözümlemek için, ortamınızda var olan DNS sunucularını işaret eden koşullu ileticiler oluşturun ve kullanın.

Bu makalede, DNS konsolunu kullanarak Azure AD DS kayıtları yönetme ve koşullu ileticiler oluşturma işlemlerinin nasıl yapılacağı gösterilir.

## <a name="before-you-begin"></a>Başlamadan önce

Bu makaleyi tamamlayabilmeniz için aşağıdaki kaynaklar ve ayrıcalıklar gereklidir:

* Etkin bir Azure aboneliği.
    * Azure aboneliğiniz yoksa [bir hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Abonelikle ilişkili bir Azure Active Directory kiracısı, şirket içi bir dizinle veya yalnızca bulut diziniyle eşitlenir.
    * Gerekirse, [bir Azure Active Directory kiracı oluşturun][create-azure-ad-tenant] veya [bir Azure aboneliğini hesabınızla ilişkilendirin][associate-azure-ad-tenant].
* Azure AD kiracınızda etkinleştirilmiş ve yapılandırılmış Azure Active Directory Domain Services yönetilen bir etki alanı.
    * Gerekirse, [Azure Active Directory Domain Services yönetilen bir etki alanı oluşturmak ve yapılandırmak][create-azure-ad-ds-instance]için öğreticiyi doldurun.
* Azure AD DS sanal ağınızdan diğer DNS ad alanlarınızın barındırıldığı konuma bağlantı.
    * Bu bağlantı, bir [Azure ExpressRoute][expressroute] veya [Azure VPN Gateway][vpn-gateway] bağlantısı ile birlikte sağlanıyor.
* Yönetilen etki alanına katılmış bir Windows Server Yönetim sanal makinesi.
    * Gerekirse, [bir Windows Server VM oluşturma ve bunu yönetilen bir etki alanına katma][create-join-windows-vm]öğreticisini doldurun.
* Azure AD kiracınızda *Azure AD DC Administrators* grubunun üyesi olan bir kullanıcı hesabı.

## <a name="install-dns-server-tools"></a>DNS sunucusu araçları 'nı yükler

Yönetilen bir etki alanında DNS kayıtları oluşturmak ve değiştirmek için, DNS sunucusu araçları 'nı yüklemeniz gerekir. Bu araçlar, Windows Server 'da bir özellik olarak yüklenebilir. Windows istemcisine yönetimsel araçların nasıl yükleneceği hakkında daha fazla bilgi için bkz. Install [uzak sunucu yönetim araçları (RSAT)][install-rsat].

1. Yönetim sanal makinenizde oturum açın. Azure portal kullanarak bağlanma adımları için bkz. [Windows Server VM 'ye bağlanma][connect-windows-server-vm].
1. **Sunucu Yöneticisi** sanal makinede oturum açtığınızda varsayılan olarak açılmazsa **Başlat** menüsünü ve ardından **Sunucu Yöneticisi** öğesini seçin.
1. **Sunucu Yöneticisi** penceresinin *Pano* bölmesinde **rol ve Özellik Ekle**' yi seçin.
1. *Rol ve Özellik Ekleme Sihirbazı*' nın **başlamadan önce** sayfasında **İleri**' yi seçin.
1. *Yükleme türü* Için, **rol tabanlı veya özellik tabanlı yükleme** seçeneğini işaretli bırakın ve **İleri ' yi** seçin.
1. **Sunucu seçimi** sayfasında, sunucu havuzundan *MYVM.AADDSCONTOSO.com* gibi geçerli VM 'Yi seçin ve ardından **İleri**' yi seçin.
1. **Sunucu rolleri** sayfasında, **İleri**' ye tıklayın.
1. **Özellikler** sayfasında, **uzak sunucu yönetim araçları** düğümünü genişletin ve ardından **rol yönetim araçları** düğümünü genişletin. Rol yönetim araçları listesinden **DNS sunucusu araçları** özelliğini seçin.

    ![Kullanılabilir rol yönetim araçları listesinden DNS sunucusu araçlarını yüklemeyi seçin](./media/manage-dns/install-dns-tools.png)

1. **Onay** sayfasında, **yükler**' i seçin. DNS sunucusu araçları 'nı yüklemek bir dakika veya iki dakika sürebilir.
1. Özellik yüklemesi tamamlandığında, **rol ve özellik ekleme** Sihirbazı 'ndan çıkmak için **Kapat** ' ı seçin.

## <a name="open-the-dns-management-console-to-administer-dns"></a>DNS 'yi yönetmek için DNS Yönetim konsolunu açın

DNS sunucusu araçları yüklüyken, yönetilen etki alanında DNS kayıtlarını yönetebilirsiniz.

> [!NOTE]
> Yönetilen bir etki alanında DNS 'yi yönetmek için *AAD DC Administrators* grubunun üyesi olan bir kullanıcı hesabında oturum açmış olmanız gerekir.

1. Başlangıç ekranından **Yönetim Araçları**' nı seçin. Önceki bölümde yüklü olan **DNS** de dahil olmak üzere kullanılabilir yönetim araçlarının bir listesi gösterilir. DNS Yönetim konsolunu başlatmak için **DNS** ' yi seçin.
1. **DNS sunucusuna bağlan** iletişim kutusunda **aşağıdaki bilgisayarı** seçin ve *AADDSCONTOSO.com* gibi yönetilen etki alanının DNS etki alanı adını girin:

    ![DNS konsolunda yönetilen etki alanına bağlanma](./media/manage-dns/connect-dns-server.png)

1. DNS konsolu belirtilen yönetilen etki alanına bağlanır. Gerekli DNS girdilerinizi oluşturmak veya mevcut kayıtları gerektiği gibi düzenlemek için **Ileriye doğru arama bölgelerini** veya **geriye doğru arama bölgelerini** genişletin.

    ![DNS konsolu-etki alanını yönetme](./media/manage-dns/dns-manager.png)

> [!WARNING]
> Kayıtları DNS sunucusu araçları 'nı kullanarak yönetirken, Azure AD DS tarafından kullanılan yerleşik DNS kayıtlarını silmeyin veya değiştirdiğinizden emin olun. Yerleşik DNS kayıtları, etki alanı DNS kayıtları, ad sunucusu kayıtları ve DC konumu için kullanılan diğer kayıtları içerir. Bu kayıtları değiştirirseniz, etki alanı Hizmetleri sanal ağ üzerinde kesintiye uğrasayabilir.

## <a name="create-conditional-forwarders"></a>Koşullu ileticiler oluşturma

Azure AD DS DNS bölgesi yalnızca yönetilen etki alanının bölge ve kayıtlarını içermelidir. Diğer DNS ad alanlarında adlandırılmış kaynakları çözümlemek için yönetilen etki alanında ek bölgeler oluşturmayın. Bunun yerine, DNS sunucusuna bu kaynakların adreslerini çözümlemek üzere nereye gidececeklerini bildirmek için yönetilen etki alanında koşullu ileticiler kullanın.

Koşullu iletici, sorguları iletmek için *contoso.com* gıbı bir DNS etki alanı tanımlamanızı sağlayan bir DNS sunucusunda bulunan bir yapılandırma seçeneğidir. Bu etki alanındaki kayıtlar için sorguları çözümlemeye çalışan yerel DNS sunucusu yerine, DNS sorguları söz konusu etki alanı için yapılandırılan DNS 'ye iletilir. Bu yapılandırma, kaynakları yansıtmak için yönetilen etki alanında Yinelenen kayıtlarla yerel bir DNS bölgesi oluştururken doğru DNS kayıtlarının döndürüldüğünden emin olmanızı sağlar.

Yönetilen etki alanında bir koşullu iletici oluşturmak için aşağıdaki adımları izleyin:

1. *Aaddscontoso.com* gibi DNS diliminizi seçin.
1. **Koşullu ileticiler**' i seçin, ardından sağ seçip **yeni koşullu iletici ' ı seçin...**
1. *Contoso.com* gibi diğer **DNS etki** alanınızı girin ve aşağıdaki örnekte gösterildiği gibi bu ad alanı için DNS sunucularının IP adreslerini girin:

    ![DNS sunucusu için koşullu iletici ekleme ve yapılandırma](./media/manage-dns/create-conditional-forwarder.png)

1. **Bu koşullu ileticiyi Active Directory ' de depola** kutusunu işaretleyin ve aşağıdaki örnekte gösterildiği gibi *Bu etkı alanındaki tüm DNS sunucuları* için seçeneği belirleyin:

    ![DNS konsolu-bu etki alanındaki tüm DNS sunucularını seçin](./media/manage-dns/store-in-domain.png)

    > [!IMPORTANT]
    > Koşullu iletici *etki alanı* yerine *ormanda* depolanıyorsa, koşullu iletici başarısız olur.

1. Koşullu İleticisi oluşturmak için **Tamam**' ı seçin.

Yönetilen etki alanına bağlı VM 'lerden diğer ad alanlarındaki kaynakların ad çözümlemesi artık doğru şekilde çözümlenmelidir. Koşullu ileticide yapılandırılan DNS etki alanı sorguları ilgili DNS sunucularına geçirilir.

## <a name="next-steps"></a>Sonraki adımlar

DNS yönetimi hakkında daha fazla bilgi için [TechNet 'Teki DNS araçları makalesine](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc753579(v=ws.11))bakın.

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[expressroute]: ../expressroute/expressroute-introduction.md
[vpn-gateway]: ../vpn-gateway/vpn-gateway-about-vpngateways.md
[create-join-windows-vm]: join-windows-vm.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[connect-windows-server-vm]: join-windows-vm.md#connect-to-the-windows-server-vm

<!-- EXTERNAL LINKS -->
[install-rsat]: /windows-server/remote/remote-server-administration-tools#BKMK_Thresh