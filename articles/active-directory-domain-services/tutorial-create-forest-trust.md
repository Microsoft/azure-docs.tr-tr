---
title: Öğretici-Azure AD Domain Services bir orman güveni oluşturun | Microsoft Docs
description: Azure portal Azure AD Domain Services için bir şirket içi AD DS etki alanına tek yönlü bir giden orman oluşturmayı öğrenin
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 01/21/2021
ms.author: justinha
ms.openlocfilehash: e381c80dddc4484d541f5f81de6b5df712cff69b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98673476"
---
# <a name="tutorial-create-an-outbound-forest-trust-to-an-on-premises-domain-in-azure-active-directory-domain-services"></a>Öğretici: Azure Active Directory Domain Services ' de şirket içi etki alanına giden bir orman güveni oluşturma

Parola karmalarını eşitleyemeyen veya kullanıcıların akıllı kartlar kullanarak özel olarak oturum açarken ve parolasını bilmiyorsanız, Azure Active Directory Domain Services (Azure AD DS) bir kaynak ormanı kullanabilirsiniz. Kaynak ormanı, Azure AD DS bir veya daha fazla şirket içi AD DS ortamına tek yönlü bir giden güven kullanır. Bu güven ilişkisi, kullanıcıların, uygulamaların ve bilgisayarların Azure AD DS yönetilen etki alanındaki şirket içi bir etki alanında kimlik doğrulaması yapmasını sağlar. Bir kaynak ormanında, şirket içi parola karmaları hiçbir zaman eşitlenmez.

![Azure AD DS şirket içi AD DS orman güveninin diyagramı](./media/concepts-resource-forest/resource-forest-trust-relationship.png)

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Azure AD DS bağlantısını desteklemek için bir şirket içi AD DS ortamında DNS yapılandırma
> * Şirket içi AD DS ortamında tek yönlü bir gelen orman güveni oluşturma
> * Azure AD DS tek yönlü bir giden orman güveni oluşturun
> * Kimlik doğrulama ve kaynak erişimi için güven ilişkisini test edin ve doğrulayın

Azure aboneliğiniz yoksa başlamadan önce [bir hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlayabilmeniz için aşağıdaki kaynaklar ve ayrıcalıklar gereklidir:

* Etkin bir Azure aboneliği.
    * Azure aboneliğiniz yoksa [bir hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Abonelikle ilişkili bir Azure Active Directory kiracısı, şirket içi bir dizinle veya yalnızca bulut diziniyle eşitlenir.
    * Gerekirse, [bir Azure Active Directory kiracı oluşturun][create-azure-ad-tenant] veya [bir Azure aboneliğini hesabınızla ilişkilendirin][associate-azure-ad-tenant].
* Kaynak ormanı kullanılarak oluşturulan ve Azure AD kiracınızda yapılandırılmış Azure Active Directory Domain Services yönetilen bir etki alanı.
    * Gerekirse, [Azure Active Directory Domain Services yönetilen bir etki alanı oluşturun ve yapılandırın][create-azure-ad-ds-instance-advanced].
    
    > [!IMPORTANT]
    > *Kaynak* ormanı kullanarak yönetilen bir etki alanı oluşturduğunuzdan emin olun. Varsayılan seçenek bir *Kullanıcı* ormanı oluşturur. Şirket içi AD DS ortamlarında yalnızca kaynak ormanları için güvenler oluşturulabilir.
    >
    > Ayrıca, yönetilen etki alanınız için en az *Kurumsal* SKU kullanmanız gerekir. Gerekirse, [yönetilen bir etki alanı için SKU 'yu değiştirin][howto-change-sku].

## <a name="sign-in-to-the-azure-portal"></a>Azure portalında oturum açın

Bu öğreticide, Azure portal kullanarak giden orman güvenini Azure AD DS oluşturup yapılandırırsınız. Başlamak için öncelikle [Azure Portal](https://portal.azure.com)oturum açın.

## <a name="networking-considerations"></a>Ağ konusunda dikkat edilmesi gerekenler

Azure AD DS kaynak ormanı 'nı barındıran sanal ağın şirket içi Active Directory ağ bağlantısı olması gerekir. Ayrıca, uygulama ve hizmetlerin Azure AD DS kaynak ormanı 'nı barındıran sanal ağa ağ bağlantısı olması gerekir. Azure AD DS kaynak ormanına ağ bağlantısı her zaman açık ve kararlı olmalıdır, aksi takdirde kullanıcılar kimlik doğrulaması yapamaz veya kaynaklara erişemez.

Azure AD DS 'de bir orman güveni yapılandırmadan önce, Azure ile şirket içi ortam arasındaki ağ ağınızın aşağıdaki gereksinimleri karşıladığından emin olun:

* Özel IP adresleri kullanın. Dinamik IP adresi atamasıyla DHCP 'yi kullanmayın.
* Sanal ağ eşlemesi ve yönlendirmenin Azure ile şirket içi arasında başarıyla iletişim kurmasına izin vermek için çakışan IP adresi alanları kullanmaktan kaçının.
* Azure sanal ağı, bir [Azure siteden siteye (S2S) VPN][vpn-gateway] veya [ExpressRoute][expressroute] bağlantısı yapılandırmak için bir ağ geçidi alt ağına ihtiyaç duyuyor.
* Senaryonuzu desteklemek için yeterli IP adresi olan alt ağlar oluşturun.
* Azure AD DS 'nin kendi alt ağına sahip olduğundan emin olun, bu sanal ağ alt ağını uygulama VM 'Leri ve hizmetleriyle paylaşmayın.
* Eşlenen sanal ağlar geçişli DEĞILDIR.
    * Azure sanal ağ eşayarları, Azure AD DS kaynak ormanı güvenini şirket içi AD DS ortamında kullanmak istediğiniz tüm sanal ağlar arasında oluşturulmalıdır.
* Şirket içi Active Directory ormanınızla sürekli ağ bağlantısı sağlayın. İsteğe bağlı bağlantılar kullanmayın.
* Azure AD DS kaynak ormanınızın adı ve şirket içi Active Directory ormanınızın adı arasında sürekli ad çözümlemesi (DNS) olduğundan emin olun.

## <a name="configure-dns-in-the-on-premises-domain"></a>Şirket içi etki alanında DNS 'yi yapılandırma

Yönetilen etki alanını şirket içi ortamdan doğru bir şekilde çözümlemek için, mevcut DNS sunucularına ileticiler eklemeniz gerekebilir. Şirket içi ortamı yönetilen etki alanıyla iletişim kuracak şekilde yapılandırmadıysanız, şirket içi AD DS etki alanı için bir yönetim iş istasyonundan aşağıdaki adımları uygulayın:

1.   >  **Yönetim Araçları**  >  **DNS** Başlat ' ı seçin.
1. *MyAD01* gibi DNS sunucusunu sağ seçin ve ardından **Özellikler**' i seçin.
1. **İleticiler**' i seçin ve ardından başka ileticiler eklemek için **düzenleyin** .
1. *10.0.2.4* ve *10.0.2.5* gıbı yönetilen etki alanının IP adreslerini ekleyin.

## <a name="create-inbound-forest-trust-in-the-on-premises-domain"></a>Şirket içi etki alanında gelen orman güveni oluşturma

Şirket içi AD DS etki alanı, yönetilen etki alanı için gelen bir orman güveni gerektirir. Bu güvenin şirket içi AD DS etki alanında el ile oluşturulması gerekir, bu, Azure portal oluşturulamıyor.

Şirket içi AD DS etki alanında gelen güveni yapılandırmak için, şirket içi AD DS etki alanı için bir yönetim iş istasyonundan aşağıdaki adımları uygulayın:

1.   >    >  **Etki alanları ve Güvenleri Active Directory** yönetim araçlarını Başlat ' ı seçin.
1. *OnPrem.contoso.com* gibi etki alanına sağ tıklayın ve ardından **Özellikler**' i seçin.
1. **Güvenler** sekmesini ve ardından **yeni güven**' i seçin.
1. Azure AD DS etki alanı adı için *aaddscontoso.com* gibi bir ad girin ve ardından **İleri**' yi seçin.
1. Bir **orman güveni** oluşturma, sonra bir **yol oluşturma: gelen** güven seçeneğini belirleyin.
1. **Yalnızca bu etki alanı** için güven oluşturmayı seçin. Bir sonraki adımda, yönetilen etki alanı için Azure portal güveni oluşturursunuz.
1. **Orman genelinde kimlik doğrulaması** kullanmayı seçin, ardından bir güven parolasını girip onaylayın. Aynı parola, sonraki bölümde Azure portal de girilir.
1. Varsayılan seçeneklerde bir sonraki birkaç pencere arasında ilerleyin, ardından **Hayır, giden güveni onaylama** seçeneğini belirleyin.
1. **Son**'u seçin.

Bir ortam için orman güveni artık gerekmiyorsa, şirket içi etki alanından kaldırmak için aşağıdaki adımları izleyin:

1.   >    >  **Etki alanları ve Güvenleri Active Directory** yönetim araçlarını Başlat ' ı seçin.
1. *OnPrem.contoso.com* gibi etki alanına sağ tıklayın ve ardından **Özellikler**' i seçin.
1. **Güvenler** sekmesini ve ardından **Bu etki alanına güvenen etki alanlarını (gelen güvenler)** seçin, kaldırılacak güveni tıklatın ve ardından **Kaldır**' a tıklayın.
1. Güvenler sekmesinde, **Bu etki alanı tarafından güvenilen etki alanları (giden güvenler)** altında, kaldırılacak güveni ve sonra Kaldır ' a tıklayın.
1. Hayır ' a tıklayın **, güveni yalnızca yerel etki alanından kaldırın**.

## <a name="create-outbound-forest-trust-in-azure-ad-ds"></a>Azure AD DS giden orman güveni oluşturma

Yönetilen etki alanını çözümlemek için yapılandırılan şirket içi AD DS etki alanı ve oluşturulan bir gelen orman güveni ile, artık giden orman güvenini oluşturun. Bu giden orman güveni, şirket içi AD DS etki alanı ve yönetilen etki alanı arasındaki güven ilişkisini tamamlar.

Azure portal yönetilen etki alanı için giden güveni oluşturmak için aşağıdaki adımları izleyin:

1. Azure portal, **Azure AD Domain Services** arayıp seçin, sonra yönetilen etki alanınızı seçin (örneğin, *aaddscontoso.com*).
1. Yönetilen etki alanının sol tarafındaki menüden **Güvenler**' ı seçin ve ardından güven **Ekle** ' yi seçin.

   > [!NOTE]
   > **Güvenler** menü seçeneğini görmüyorsanız, *orman türü* için **Özellikler** ' in altına bakın. Yalnızca *kaynak* ormanları güven oluşturabilir. Orman türü *Kullanıcı* ise, güven oluşturamazsınız. Şu anda yönetilen bir etki alanının orman türünü değiştirmek mümkün değildir. Yönetilen etki alanını bir kaynak ormanı olarak silip yeniden oluşturmanız gerekir.

1. Güveninizi tanımlayan bir görünen ad girin, ardından *OnPrem.contoso.com* gibi şirket içi GÜVENILEN orman DNS adı.
1. Önceki bölümde yer alan şirket içi AD DS etki alanı için gelen orman güvenini yapılandırmak üzere kullanılan güven parolasını sağlayın.
1. *10.1.1.4* ve *10.1.1.5* gibi şirket içi AD DS etki alanı IÇIN en az iki DNS sunucusu sağlayın.
1. Hazırsanız, giden orman güvenini **kaydedin** .

    ![Azure portal giden orman güveni oluşturma](./media/tutorial-create-forest-trust/portal-create-outbound-trust.png)

Bir ortam için orman güveni artık gerekmiyorsa, Azure AD DS kaldırmak için aşağıdaki adımları izleyin:

1. Azure portal, **Azure AD Domain Services** arayıp seçin, sonra yönetilen etki alanınızı seçin (örneğin, *aaddscontoso.com*).
1. Yönetilen etki alanının sol tarafındaki menüden **Güvenler**' ı seçin, güveni seçin ve **Kaldır**' ı tıklatın.
1. Orman güvenini yapılandırmak için kullanılan güven parolasını sağlayın ve **Tamam**' a tıklayın.

## <a name="validate-resource-authentication"></a>Kaynak kimlik doğrulamasını doğrulama

Aşağıdaki yaygın senaryolar, orman güveninin kullanıcıları ve kaynaklara erişimi doğru şekilde doğruladığını doğrulamanızı sağlar:

* [Azure AD DS kaynak ormanında şirket içi kullanıcı kimlik doğrulaması](#on-premises-user-authentication-from-the-azure-ad-ds-resource-forest)
* [Şirket içi kullanıcı kullanarak Azure AD DS kaynak ormanındaki kaynaklara erişme](#access-resources-in-the-azure-ad-ds-resource-forest-using-on-premises-user)
    * [Dosya ve yazıcı paylaşımını etkinleştir](#enable-file-and-printer-sharing)
    * [Güvenlik grubu oluşturma ve üye ekleme](#create-a-security-group-and-add-members)
    * [Ormanlar arası erişim için bir dosya paylaşma oluşturma](#create-a-file-share-for-cross-forest-access)
    * [Ormanlar arası kimlik doğrulamasını bir kaynağa doğrulama](#validate-cross-forest-authentication-to-a-resource)

### <a name="on-premises-user-authentication-from-the-azure-ad-ds-resource-forest"></a>Azure AD DS kaynak ormanında şirket içi kullanıcı kimlik doğrulaması

Yönetilen etki alanına katılmış Windows Server sanal makinenizin olması gerekir. Şirket içi kullanıcılarınızın bir sanal makinede kimlik doğrulaması yapabilmeleri için bu sanal makineyi kullanın. Gerekirse, [bir Windows sanal makinesi oluşturun ve yönetilen etki alanına ekleyin][join-windows-vm].

1. [Azure ve azure](../bastion/bastion-overview.md) AD DS yönetici kimlik bilgilerinizle Azure AD DS kaynak ormanına katılmış WINDOWS Server VM 'sine bağlanın.
1. Bir komut istemi açın ve `whoami` Şu anda kimliği doğrulanmış kullanıcının ayırt edici adını göstermek için komutunu kullanın:

    ```console
    whoami /fqdn
    ```

1. `runas`Şirket içi etki alanından bir kullanıcı olarak kimlik doğrulaması yapmak için komutunu kullanın. Aşağıdaki komutta, yerine güvenilen Şirket `userUpn@trusteddomain.com` içi etki alanındaki bir kullanıcının UPN 'si ile değiştirin. Komut sizden kullanıcının parolasını ister:

    ```console
    Runas /u:userUpn@trusteddomain.com cmd.exe
    ```

1. Kimlik doğrulaması başarılı olursa yeni bir komut istemi açılır. Yeni komut isteminin başlığı içerir `running as userUpn@trusteddomain.com` .
1. `whoami /fqdn`Şirket içi Active Directory kimliği doğrulanmış kullanıcının ayırt edici adını görüntülemek için yeni komut isteminde kullanın.

### <a name="access-resources-in-the-azure-ad-ds-resource-forest-using-on-premises-user"></a>Şirket içi kullanıcı kullanarak Azure AD DS kaynak ormanındaki kaynaklara erişme

Azure AD DS kaynak ormanına katılmış Windows Server VM 'sini kullanarak, kullanıcıların şirket içi etki alanındaki kullanıcılarla şirket içi etki alanındaki bilgisayarlardan kimlik doğrulaması yaparken, kaynak ormanında barındırılan kaynaklara erişim izni olan senaryoyu test edebilirsiniz. Aşağıdaki örneklerde çeşitli yaygın senaryolar oluşturma ve test etme işlemlerinin nasıl yapılacağı gösterilmektedir.

#### <a name="enable-file-and-printer-sharing"></a>Dosya ve yazıcı paylaşımını etkinleştir

1. [Azure ve azure](../bastion/bastion-overview.md) AD DS yönetici kimlik bilgilerinizle Azure AD DS kaynak ormanına katılmış WINDOWS Server VM 'sine bağlanın.

1. **Windows ayarları**' nı açın ve **Ağ ve Paylaşım Merkezi**' ni arayıp seçin.
1. **Gelişmiş paylaşım ayarlarını değiştir** seçeneğini belirleyin.
1. **Etki alanı profili** altında **dosya ve yazıcı paylaşımını aç** ' ı seçin ve **değişiklikleri kaydedin**.
1. **Ağ ve Paylaşım Merkezi 'ni** kapatın.

#### <a name="create-a-security-group-and-add-members"></a>Güvenlik grubu oluşturma ve üye ekleme

1. **Active Directory Kullanıcıları ve Bilgisayarları**'nı açın.
1. Etki alanı adını sağ seçin, **Yeni**' yi seçin ve ardından **kuruluş birimi**' ni seçin.
1. Ad kutusuna *Localobjects* yazın ve ardından **Tamam**' ı seçin.
1. Gezinti bölmesinde **Localobjects** ' i seçin ve sağ tıklayın. **Yeni** ' yi ve ardından **Grup**' u seçin.
1. **Grup adı** kutusuna *fileserveraccess* yazın. **Grup kapsamı** Için **etki alanı yerel**' i seçin ve ardından **Tamam**' ı seçin.
1. İçerik bölmesinde, **Fileserveraccess**' e çift tıklayın. **Üyeler**' i seçin, **eklemeyi** seçin ve ardından **konumlar**' ı seçin.
1. **Konum** görünümünden şirket içi Active Directory seçin ve ardından **Tamam**' ı seçin.
1. **Seçilecek nesne adlarını girin** kutusunda *etki alanı kullanıcıları* yazın. **Adları denetle**' yi seçin, şirket içi Active Directory kimlik bilgilerini sağlayın ve ardından **Tamam**' ı seçin.

    > [!NOTE]
    > Güven ilişkisi yalnızca tek bir yol olduğundan kimlik bilgilerini sağlamanız gerekir. Bu, Azure AD DS yönetilen etki alanındaki kullanıcıların kaynaklara erişemeyeceği veya güvenilir (Şirket içi) etki alanındaki kullanıcıları veya grupları araymayacağı anlamına gelir.

1. Şirket içi Active Directory **etki alanı kullanıcıları** grubu, **fileserveraccess** grubunun bir üyesi olmalıdır. Grubu kaydetmek ve pencereyi kapatmak için **Tamam** ' ı seçin.

#### <a name="create-a-file-share-for-cross-forest-access"></a>Ormanlar arası erişim için bir dosya paylaşma oluşturma

1. Azure AD DS kaynak ormanına katılmış Windows Server VM 'de, bir klasör oluşturun ve *Crossforestshare* gibi bir ad sağlayın.
1. Klasörü sağ seçin ve **Özellikler**' i seçin.
1. **Güvenlik** sekmesini seçin ve ardından **Düzenle**' yi seçin.
1. *Çapraz Forestshare izinleri* Iletişim kutusunda **Ekle**' yi seçin.
1. **Seçilecek nesne adlarını girin** alanına *fileserveraccess* yazın ve ardından **Tamam**' ı seçin.
1. **Gruplar veya Kullanıcı adları** listesinden *fileserveraccess* ' i seçin. **FileServerAccess izinleri** listesinde, **değiştirme** ve **yazma** izinleri için *Izin ver* ' i seçin ve ardından **Tamam**' ı seçin.
1. **Paylaşım** sekmesini ve ardından **Gelişmiş paylaşım...** öğesini seçin.
1. **Bu klasörü paylaşma**' yı seçin, sonra da *çapraz Forestshare* gibi **Share adında** dosya paylaşımının hatırlayabileceğiniz bir adını girin.
1. **İzinler**'i seçin. **Herkes Için izinler** listesinde, **Değiştir** izni için **izin ver** ' i seçin.
1. **Tamam** ' ı iki kez seçin ve ardından **kapatın**.

#### <a name="validate-cross-forest-authentication-to-a-resource"></a>Ormanlar arası kimlik doğrulamasını bir kaynağa doğrulama

1. Şirket içi Active Directory bir kullanıcı hesabı kullanarak şirket içi Active Directory katılmış bir Windows bilgisayarda oturum açın.
1. **Windows Gezgini**'ni kullanarak, tam ana bilgisayar adını ve gibi bir paylaşma kullanarak oluşturduğunuz paylaşıma bağlanın `\\fs1.aaddscontoso.com\CrossforestShare` .
1. Yazma iznini doğrulamak için, klasörde sağ seçin, **Yeni**' yi seçin ve ardından **metin belgesi**' ni seçin. Varsayılan ad **Yeni metin belgesi ' ni** kullanın.

    Yazma izinleri doğru ayarlandıysa yeni bir metin belgesi oluşturulur. Aşağıdaki adımlar, dosyayı uygun şekilde açar, düzenleyebilir ve siler.
1. Oku iznini doğrulamak için **Yeni metin belgesi** açın.
1. Değiştirme iznini doğrulamak için, dosyaya metin ekleyin ve **Not defteri 'ni** kapatın. Değişiklikleri kaydetmeniz istendiğinde **Kaydet**' i seçin.
1. Silme iznini doğrulamak için **Yeni metin belgesi** ' ni sağ seçin ve **Sil**' i seçin. Dosya silmeyi onaylamak için **Evet** ' i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
> * Azure AD DS bağlantısını desteklemek için bir şirket içi AD DS ortamında DNS yapılandırma
> * Şirket içi AD DS ortamında tek yönlü bir gelen orman güveni oluşturma
> * Azure AD DS tek yönlü bir giden orman güveni oluşturun
> * Kimlik doğrulama ve kaynak erişimi için güven ilişkisini test edin ve doğrulayın

Azure AD DS orman türleri hakkında daha fazla kavramsal bilgi için bkz. [kaynak ormanları nelerdir?][concepts-forest] ve [orman güvenleri Azure AD DS nasıl çalışır?][concepts-trust]

<!-- INTERNAL LINKS -->
[concepts-forest]: concepts-resource-forest.md
[concepts-trust]: concepts-forest-trust.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance-advanced]: tutorial-create-instance-advanced.md
[howto-change-sku]: change-sku.md
[vpn-gateway]: ../vpn-gateway/vpn-gateway-about-vpngateways.md
[expressroute]: ../expressroute/expressroute-introduction.md
[join-windows-vm]: join-windows-vm.md