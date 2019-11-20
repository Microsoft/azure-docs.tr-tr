---
title: Öğretici-Azure Active Directory Domain Services örneği oluşturma | Microsoft Docs
description: Bu öğreticide, Azure portal kullanarak bir Azure Active Directory Domain Services örneği oluşturma ve yapılandırma hakkında bilgi edineceksiniz.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 10/30/2019
ms.author: iainfou
ms.openlocfilehash: 2a1fbe8d47af8a2215b0d0a3d81fbe67a62d4755
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73474426"
---
# <a name="tutorial-create-and-configure-an-azure-active-directory-domain-services-instance"></a>Öğretici: Azure Active Directory Domain Services örneği oluşturma ve yapılandırma

Azure Active Directory Domain Services (Azure AD DS), Windows Server Active Directory ile tamamen uyumlu etki alanına katılması, Grup ilkesi, LDAP, Kerberos/NTLM kimlik doğrulaması gibi yönetilen etki alanı Hizmetleri sağlar. Etki alanı denetleyicilerini kendiniz dağıtmadan, yönetmeden ve düzeltme eki uygulamadan bu etki alanı hizmetlerini kullanırsınız. Azure AD DS, mevcut Azure AD kiracınızla tümleşir. Bu tümleştirme, kullanıcıların kurumsal kimlik bilgilerini kullanarak oturum açmasını sağlar ve kaynaklara erişimi güvenli hale getirmek için mevcut grupları ve Kullanıcı hesaplarını kullanabilirsiniz.

Ağ ve eşitleme için varsayılan yapılandırma seçeneklerini kullanarak yönetilen bir etki alanı oluşturabilir veya [Bu ayarları el ile tanımlayabilirsiniz][tutorial-create-instance-advanced]. Bu öğreticide, Azure portal kullanarak bir Azure AD DS örneği oluşturmak ve yapılandırmak için varsayılan seçeneklerin nasıl kullanılacağı gösterilmektedir.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Yönetilen bir etki alanı için DNS gereksinimlerini anlama
> * Azure AD DS örneği oluşturma
> * Parola karması eşitlemeyi etkinleştirme

Azure aboneliğiniz yoksa başlamadan önce [bir hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlayabilmeniz için aşağıdaki kaynaklar ve ayrıcalıklar gereklidir:

* Etkin bir Azure aboneliği.
    * Azure aboneliğiniz yoksa [bir hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Abonelikle ilişkili bir Azure Active Directory kiracısı, şirket içi bir dizinle veya yalnızca bulut diziniyle eşitlenir.
    * Gerekirse, [bir Azure Active Directory kiracı oluşturun][create-azure-ad-tenant] veya [bir Azure aboneliğini hesabınızla ilişkilendirin][associate-azure-ad-tenant].
* Azure AD DS 'yi etkinleştirmek için Azure AD kiracınızda *genel yönetici* ayrıcalıklarına sahip olmanız gerekir.
* Gerekli Azure AD DS kaynaklarını oluşturmak için Azure aboneliğinizde *katılımcı* ayrıcalıklarına sahip olmanız gerekir.

Azure AD DS için gerekli olmasa da, Azure AD kiracısı için [self servis parola sıfırlama (SSPR) yapılandırmanız][configure-sspr] önerilir. Kullanıcılar, SSPR olmadan parolalarını değiştirebilir, ancak SSPR, parolalarını unutmaları ve sıfırlanması gerekir.

> [!IMPORTANT]
> Azure AD DS yönetilen bir etki alanı oluşturduktan sonra, örneği farklı bir kaynak grubuna, sanal ağa, aboneliğe vb. taşıyamazsınız. Azure AD DS örneğini dağıtırken en uygun aboneliği, kaynak grubunu, bölgeyi ve sanal ağı seçin.

## <a name="sign-in-to-the-azure-portal"></a>Azure portalında oturum açın

Bu öğreticide, Azure portal kullanarak Azure AD DS örneğini oluşturup yapılandırırsınız. Başlamak için öncelikle [Azure Portal](https://portal.azure.com)oturum açın.

## <a name="create-an-instance"></a>Örnek oluşturma

**Etkinleştirme Azure AD Domain Services** Sihirbazı 'nı başlatmak için aşağıdaki adımları izleyin:

1. Azure portal menüsünde veya **giriş** sayfasında, **kaynak oluştur**' u seçin.
1. Arama çubuğuna *etki alanı Hizmetleri* girin, sonra arama önerilerindeki *Azure AD Domain Services* seçin.
1. Azure AD Domain Services sayfasında **Oluştur**' u seçin. **Etkinleştirme Azure AD Domain Services** Sihirbazı başlatılır.
1. Yönetilen etki alanını oluşturmak istediğiniz Azure **aboneliğini** seçin.
1. Yönetilen etki alanının ait olacağı **kaynak grubunu** seçin. **Yeni oluştur** veya var olan bir kaynak grubunu Seç seçeneğini belirleyin.

Azure AD DS örneği oluşturduğunuzda bir DNS adı belirlersiniz. Bu DNS adını seçerken bazı noktalar vardır:

* **Yerleşik etki alanı adı:** Varsayılan olarak, dizinin yerleşik etki alanı adı kullanılır (bir *. onmicrosoft.com* soneki). Yönetilen etki alanına internet üzerinden güvenli LDAP erişimini etkinleştirmek istiyorsanız, bu varsayılan etki alanı ile bağlantıyı güvenli hale getirmek için dijital bir sertifika oluşturamazsınız. Microsoft *. onmicrosoft.com* etki alanına sahip olduğundan, bir sertifika YETKILISI (CA) bir sertifika vermez.
* **Özel etki alanı adları:** En yaygın yaklaşım, genellikle zaten sahip olduğunuz ve yönlendirilebilir olan özel bir etki alanı adı belirtmektir. Yönlendirilebilir, özel bir etki alanı kullandığınızda, uygulamalarınızı desteklemek için gereken şekilde trafik doğru şekilde akabilir.
* **Yönlendirilemeyen etki alanı sonekleri:** Genellikle, *contoso. Local*gibi yönlendirilebilir olmayan bir etki alanı adı sonekini önlemenize tavsiye ederiz. *. Local* son eki yönlendirilebilir DEĞILDIR ve DNS çözümlenme sorunlarına neden olabilir.

> [!TIP]
> Özel bir etki alanı adı oluşturursanız, mevcut DNS ad alanları ile ilgilenin. Etki alanı adı için benzersiz bir ön ek eklemeniz önerilir. Örneğin, DNS kök adınız *contoso.com*ise, *corp.contoso.com* veya *DS.contoso.com*özel etki alanı adına sahip bir Azure AD DS yönetilen etki alanı oluşturun. Şirket içi AD DS ortamı olan bir karma ortamda, bu ön ekler zaten kullanımda olabilir. Azure AD DS için benzersiz bir ön ek kullanın.
>
> Azure AD DS yönetilen etki alanınız için kök DNS adını kullanabilirsiniz, ancak ortamınızdaki diğer hizmetler için bazı ek DNS kayıtları oluşturmanız gerekebilir. Örneğin, kök DNS adını kullanarak bir siteyi barındıran bir Web sunucusu çalıştırırsanız, ek DNS girişleri gerektiren adlandırma çakışmaları olabilir.
>
> Bu öğreticiler ve nasıl yapılır makalelerinde, *contoso.com* özel etki alanı kısa bir örnek olarak kullanılır. Tüm komutlarda, benzersiz bir ön ek içerebilen kendi etki alanı adınızı belirtin.
>
> Daha fazla bilgi için bkz. [etki alanı için bir adlandırma ön eki seçme][naming-prefix].

Aşağıdaki DNS adı kısıtlamaları da geçerlidir:

* **Etki alanı ön eki kısıtlamaları:** Ön eki 15 karakterden daha uzun olan yönetilen bir etki alanı oluşturamazsınız. Belirtilen etki alanı adınızın (örneğin, *contoso.com* etki alanı adında *contoso* ) ön ekinin 15 veya daha az karakter içermesi gerekir.
* **Ağ adı çakışmaları:** Yönetilen etki alanınız için DNS etki alanı adı, sanal ağda zaten mevcut olmamalıdır. Özellikle, bir ad çakışmasına yol açacak aşağıdaki senaryoları kontrol edin:
    * Azure sanal ağında aynı DNS etki alanı adına sahip bir Active Directory etki alanınız zaten varsa.
    * Yönetilen etki alanını etkinleştirmeyi planladığınız sanal ağın, şirket içi ağınızla bir VPN bağlantısı varsa. Bu senaryoda, şirket içi ağınızda aynı DNS etki alanı adına sahip bir etki alanı olmadığından emin olun.
    * Azure sanal ağında bu adı taşıyan mevcut bir Azure bulut hizmetiniz varsa.

Azure AD DS örneği oluşturmak için Azure portal *temel bilgiler* penceresindeki alanları doldurun:

1. Yönetilen etki alanınız için bir **DNS etki alanı adı** girin, önceki noktaları dikkate alarak.
1. Yönetilen etki alanının oluşturulması gereken Azure **konumunu** seçin. Kullanılabilirlik Alanları destekleyen bir bölge seçerseniz, Azure AD DS kaynakları daha fazla artıklık için bölgelere dağıtılır.

    Kullanılabilirlik Alanları, Azure bölgesi içinde fiziksel olarak benzersiz konumlardır. Her alan bağımsız güç, soğutma ve ağ bağlantısı ile donatılmış bir veya daha fazla veri merkezinden oluşur. Dayanıklılık sağlamak için, tüm etkin bölgelerde en az üç ayrı bölge vardır.

    Azure AD DS bölgeler arasında dağıtılacak şekilde yapılandırmanız için bir şey yoktur. Azure platformu, kaynakların bölge dağılımını otomatik olarak işler. Daha fazla bilgi edinmek ve bölge kullanılabilirliğini görmek için bkz. [Azure 'da kullanılabilirlik alanları nedir?][availability-zones]

    ![Azure AD Domain Services örneği için temel ayarları yapılandırma](./media/tutorial-create-instance/basics-window.png)

Azure AD DS yönetilen bir etki alanını hızlıca oluşturmak için, ek varsayılan yapılandırma seçeneklerini kabul etmek için **gözden geçir + oluştur** seçeneğini belirleyebilirsiniz. Bu oluşturma seçeneğini belirlediğinizde aşağıdaki varsayılanlar yapılandırılır:

* *10.0.1.0/24*IP adresi aralığını kullanan *aeklemeleri-VNET* adlı bir sanal ağ oluşturur.
* *10.0.1.0/24*IP adresi aralığını kullanarak *aeklemesine-subnet* adlı bir alt ağ oluşturur.
* Azure AD 'deki *Tüm* kullanıcıları Azure AD DS yönetilen etki alanına eşitler.

1. Bu varsayılan yapılandırma seçeneklerini kabul etmek için **gözden geçir + oluştur** ' u seçin.

## <a name="deploy-the-managed-domain"></a>Yönetilen etki alanını dağıtma

Sihirbazın **Özet** sayfasında, yönetilen etki alanının yapılandırma ayarlarını gözden geçirin. Değişiklik yapmak için sihirbazın herhangi bir adımına geri dönebilirsiniz. Azure AD DS yönetilen bir etki alanını bu yapılandırma seçeneklerini kullanarak tutarlı bir şekilde farklı bir Azure AD kiracısına yeniden dağıtmak için, **Otomasyon için bir şablon da indirebilirsiniz**.

1. Yönetilen etki alanını oluşturmak için **Oluştur**' u seçin. Azure AD DS yönetilen oluşturulduktan sonra DNS adı veya sanal ağ gibi belirli yapılandırma seçeneklerinin değiştirilemeytiğine ilişkin bir Note görüntülenir. Devam etmek için **Tamam**' ı seçin.
1. Yönetilen etki alanınızı sağlama işlemi bir saate kadar sürebilir. Portalda Azure AD DS dağıtımınızın ilerlemesini gösteren bir bildirim görüntülenir. Dağıtım için ayrıntılı ilerlemeyi görmek üzere bildirimi seçin.

    ![Dağıtımın Azure portal bildirim devam ediyor](./media/tutorial-create-instance/deployment-in-progress.png)

1. Bu sayfa, dizininizde yeni kaynakların oluşturulması dahil olmak üzere dağıtım işlemindeki güncelleştirmelerle yüklenir.
1. Kaynak grubunuzu ( *Myresourcegroup*gibi) seçin ve Azure kaynakları listesinden Azure AD DS örneğinizi seçin (örneğin, *contoso.com*). **Genel bakış** sekmesi, yönetilen etki alanının şu anda *dağıtmakta*olduğunu gösterir. Yönetilen etki alanını, tam olarak sağlanana kadar yapılandıramazsınız.

    ![Sağlama durumu sırasında etki alanı Hizmetleri durumu](./media/tutorial-create-instance/provisioning-in-progress.png)

1. Yönetilen etki alanı tam olarak sağlandığında **genel bakış** sekmesi, etki alanı durumunu *çalışıyor*olarak gösterir.

    ![Başarılı bir şekilde sağlandıktan sonra etki alanı Hizmetleri durumu](./media/tutorial-create-instance/successfully-provisioned.png)

Yönetilen etki alanı, Azure AD kiracınızla ilişkilendirilir. Azure AD DS, sağlama işlemi sırasında, Azure AD kiracısında *etki alanı denetleyicisi Hizmetleri* ve *AzureActiveDirectoryDomainControllerServices* adlı iki kurumsal uygulama oluşturur. Bu kurumsal uygulamaların, yönetilen etki alanınızı hizmetine yönelik olması gerekir. Bu uygulamaları silmeyin.

## <a name="update-dns-settings-for-the-azure-virtual-network"></a>Azure sanal ağı için DNS ayarlarını güncelleştirme

Azure AD DS başarıyla dağıtıldı, artık sanal ağı diğer bağlı VM 'Lerin ve uygulamaların yönetilen etki alanını kullanmasına izin verecek şekilde yapılandırın. Bu bağlantıyı sağlamak için, sanal ağınızın DNS sunucusu ayarlarını Azure AD DS 'nin dağıtıldığı iki IP adresini işaret etmek üzere güncelleştirin.

1. Yönetilen etki alanınız için **genel bakış** sekmesi bazı **gerekli yapılandırma adımlarını**gösterir. İlk yapılandırma adımı, sanal ağınızın DNS sunucusu ayarlarını güncelleştiryöneliktir. DNS ayarları doğru yapılandırıldıktan sonra bu adım artık gösterilmemektedir.

    Listelenen adresler, sanal ağda kullanılacak etki alanı denetleyicileridir. Bu örnekte, bu adresler *10.1.0.4* ve *10.1.0.5*. Daha sonra bu IP adreslerini **Özellikler** sekmesinde bulabilirsiniz.

    ![Sanal ağınız için DNS ayarlarını Azure AD Domain Services IP adresleriyle yapılandırın](./media/tutorial-create-instance/configure-dns.png)

1. Sanal ağın DNS sunucusu ayarlarını güncelleştirmek için **Yapılandır** düğmesini seçin. DNS ayarları, sanal ağınız için otomatik olarak yapılandırılır.

> [!TIP]
> Önceki adımlarda var olan bir sanal ağı seçtiyseniz, ağa bağlı tüm VM 'Ler, yeniden başlatmanın ardından yeni DNS ayarlarını alır. Azure portal, Azure PowerShell veya Azure CLı kullanarak VM 'Leri yeniden başlatabilirsiniz.

## <a name="enable-user-accounts-for-azure-ad-ds"></a>Azure AD DS için Kullanıcı hesaplarını etkinleştirme

Yönetilen etki alanındaki kullanıcıların kimliğini doğrulamak için Azure AD DS 'nin, NT LAN Manager (NTLM) ve Kerberos kimlik doğrulaması için uygun bir biçimde parola karmaları olması gerekir. Azure AD, kiracınız için Azure AD DS etkinleştirene kadar NTLM veya Kerberos kimlik doğrulaması için gereken biçimde parola karmaları oluşturmaz veya depolamaz. Azure AD, güvenlik nedenleriyle şifresiz metin biçiminde hiçbir parola kimlik bilgisi depolamaz. Bu nedenle, Azure AD kullanıcıların mevcut kimlik bilgilerini temel alarak bu NTLM veya Kerberos parola karmalarını otomatik olarak üretemiyor.

> [!NOTE]
> Uygun şekilde yapılandırıldıktan sonra, kullanılabilir parola karmaları Azure AD DS yönetilen etki alanında depolanır. Azure AD DS yönetilen etki alanını silerseniz, o noktada depolanan tüm parola karmaları da silinir. Azure AD 'de eşitlenmiş kimlik bilgileri, daha sonra Azure AD DS yönetilen bir etki alanı oluşturursanız yeniden kullanılamaz. parola karmalarını yeniden depolamak için Parola karması eşitlemesini yeniden yapılandırmanız gerekir. Daha önce etki alanına katılmış VM 'Ler veya kullanıcılar, parola karmalarını yeni Azure AD DS yönetilen etki alanında oluşturmak ve depolamak için Azure AD ihtiyaçlarına hemen kimlik doğrulaması yapamaz. Daha fazla bilgi için bkz. [Azure AD DS Için Parola karması eşitleme işlemi ve Azure AD Connect][password-hash-sync-process].

Bu parola karmalarını oluşturma ve depolama adımları, Azure AD 'de oluşturulan yalnızca bulutta yer alan Kullanıcı hesapları için ve Azure AD Connect kullanarak şirket içi dizininizden eşitlenen Kullanıcı hesaplarında farklıdır. Yalnızca bulutta yer alan bir kullanıcı hesabı, Azure portal veya Azure AD PowerShell cmdlet’leri kullanılarak Azure AD dizininizde oluşturulmuş bir hesaptır. Bu Kullanıcı hesapları şirket içi dizinden eşitlenmez. Bu öğreticide, yalnızca bir bulut kullanıcı hesabıyla çalışalım. Azure AD Connect kullanmak için gereken ek adımlar hakkında daha fazla bilgi için bkz. [Şirket ıçı ad 'nizden yönetilen etki alanınız ile eşitlenen Kullanıcı hesapları için parola karmalarını eşitleme][on-prem-sync].

> [!TIP]
> Azure AD kiracınızda, şirket içi AD 'nizden yalnızca bulutta bulunan kullanıcıların ve kullanıcıların bir birleşimi varsa, her iki adım kümesini de doldurmanız gerekir.

Yalnızca bulut Kullanıcı hesapları için, kullanıcıların Azure AD DS kullanabilmesi için parolalarını değiştirmesi gerekir. Bu parola değiştirme işlemi, Kerberos ve NTLM kimlik doğrulamasının parola karmalarının Azure AD 'de oluşturulmasına ve depolanmasına neden olur. Kiracıdaki Azure AD DS kullanması gereken tüm kullanıcılar için parolaların süresini bir sonraki oturum açma sırasında bir parola değişikliğine zorlar ya da onları el ile parolalarını değiştirmelerini isteyebilirsiniz. Bu öğreticide, Kullanıcı parolasını el ile değiştirelim.

Kullanıcının parolasını sıfırlayabilmesi için Azure AD kiracısı 'nin [self servis parola sıfırlama için yapılandırılmış][configure-sspr]olması gerekir.

Yalnızca bulutta bulunan bir kullanıcının parolasını değiştirmek için, kullanıcının aşağıdaki adımları tamamlaması gerekir:

1. [https://myapps.microsoft.com](https://myapps.microsoft.com)ADRESINDEKI Azure AD erişim paneli sayfasına gidin.
1. Sağ üst köşede adınızı seçin, sonra açılır menüden **profil** ' i seçin.

    ![Profil seçme](./media/tutorial-create-instance/select-profile.png)

1. **Profil** sayfasında, **Parolayı Değiştir**' i seçin.
1. **Parolayı Değiştir** sayfasında, mevcut (eski) parolanızı girip yeni bir parola girin ve onaylayın.
1. **Gönder**' i seçin.

Yeni parolanın Azure AD DS kullanılabilir olması ve yönetilen etki alanına katılmış bilgisayarlarda başarıyla oturum açması için parolanızı değiştirdikten birkaç dakika sürer.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
> * Yönetilen bir etki alanı için DNS gereksinimlerini anlama
> * Azure AD DS örneği oluşturma
> * Etki alanı yönetimine yönetici kullanıcılar ekleme
> * Azure AD DS kullanıcı hesaplarını etkinleştirme ve parola karmaları oluşturma

VM 'Leri etki alanına katmadan ve Azure AD DS yönetilen etki alanını kullanan uygulamalar dağıtmadan önce, uygulama iş yükleri için bir Azure sanal ağı yapılandırın.

> [!div class="nextstepaction"]
> [Yönetilen etki alanınızı kullanmak için uygulama iş yükleri için Azure sanal ağını yapılandırın](tutorial-configure-networking.md)

<!-- INTERNAL LINKS -->
[tutorial-create-instance-advanced]: tutorial-create-instance-advanced.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[network-considerations]: network-considerations.md
[create-dedicated-subnet]: ../virtual-network/virtual-network-manage-subnet.md#add-a-subnet
[scoped-sync]: scoped-synchronization.md
[on-prem-sync]: tutorial-configure-password-hash-sync.md
[configure-sspr]: ../active-directory/authentication/quickstart-sspr.md
[password-hash-sync-process]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md#password-hash-sync-process-for-azure-ad-domain-services
[availability-zones]: ../availability-zones/az-overview.md

<!-- EXTERNAL LINKS -->
[naming-prefix]: /windows-server/identity/ad-ds/plan/selecting-the-forest-root-domain#selecting-a-prefix
