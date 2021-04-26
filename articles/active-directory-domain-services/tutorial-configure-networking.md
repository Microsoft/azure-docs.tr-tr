---
title: Öğretici-Azure AD Domain Services için sanal ağı yapılandırma | Microsoft Docs
description: Bu öğreticide, Azure portal kullanarak Azure Active Directory Domain Services yönetilen bir etki alanı için Azure sanal ağ alt ağı veya ağ eşlemesi oluşturma ve yapılandırma hakkında bilgi edineceksiniz.
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: ddb8f360304bdb41ae359f293af4d10b0afc6558
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96618425"
---
# <a name="tutorial-configure-virtual-networking-for-an-azure-active-directory-domain-services-managed-domain"></a>Öğretici: Azure Active Directory Domain Services yönetilen bir etki alanı için sanal ağ yapılandırma

Kullanıcılara ve uygulamalara bağlantı sağlamak için, bir Azure sanal ağ alt ağına bir Azure Active Directory Domain Services (Azure AD DS) yönetilen etki alanı dağıtılır. Bu sanal ağ alt ağı, yalnızca Azure platformu tarafından belirtilen yönetilen etki alanı kaynakları için kullanılmalıdır.

Kendi VM 'lerinizi ve uygulamalarınızı oluşturduğunuzda, bunlar aynı sanal ağ alt ağına dağıtılmamalıdır. Bunun yerine, uygulamalarınızı ayrı bir sanal ağ alt ağında veya Azure AD DS sanal ağı ile eşlenmiş ayrı bir sanal ağda oluşturmanız ve dağıtmanız gerekir.

Bu öğreticide, ayrılmış bir sanal ağ alt ağı oluşturma ve yapılandırma ya da Azure AD DS yönetilen etki alanının sanal ağına farklı bir ağın nasıl eşkullanılacağı gösterilir.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Etki alanına katılmış kaynaklara yönelik sanal ağ bağlantısı seçeneklerini Azure AD DS anlayın
> * Azure AD DS sanal ağında bir IP adresi aralığı ve ek alt ağ oluşturma
> * Sanal Ağ eşlemesini Azure AD DS ayrı bir ağa yapılandırın

Azure aboneliğiniz yoksa başlamadan önce [bir hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlayabilmeniz için aşağıdaki kaynaklar ve ayrıcalıklar gereklidir:

* Etkin bir Azure aboneliği.
    * Azure aboneliğiniz yoksa [bir hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Abonelikle ilişkili bir Azure Active Directory kiracısı, şirket içi bir dizinle veya yalnızca bulut diziniyle eşitlenir.
    * Gerekirse, [bir Azure Active Directory kiracı oluşturun][create-azure-ad-tenant] veya [bir Azure aboneliğini hesabınızla ilişkilendirin][associate-azure-ad-tenant].
* Azure AD DS 'yi yapılandırmak için Azure AD kiracınızda *genel yönetici* ayrıcalıklarına sahip olmanız gerekir.
* Gerekli Azure AD DS kaynaklarını oluşturmak için Azure aboneliğinizde *katılımcı* ayrıcalıklarına sahip olmanız gerekir.
* Azure AD kiracınızda etkinleştirilmiş ve yapılandırılmış Azure Active Directory Domain Services yönetilen bir etki alanı.
    * Gerekirse, ilk öğretici [Azure Active Directory Domain Services yönetilen bir etki alanı oluşturur ve yapılandırır][create-azure-ad-ds-instance].

## <a name="sign-in-to-the-azure-portal"></a>Azure portalında oturum açın

Bu öğreticide, Azure portal kullanarak yönetilen etki alanını oluşturup yapılandırırsınız. Başlamak için öncelikle [Azure Portal](https://portal.azure.com)oturum açın.

## <a name="application-workload-connectivity-options"></a>Uygulama iş yükü bağlantı seçenekleri

Önceki öğreticide, sanal ağ için bazı varsayılan yapılandırma seçeneklerini kullanan bir yönetilen etki alanı oluşturulmuştur. Bu varsayılan seçenekler bir Azure sanal ağı ve sanal ağ alt ağı oluşturmuştur. Yönetilen etki alanı hizmetlerini sağlayan Azure AD DS etki alanı denetleyicileri bu sanal ağ alt ağına bağlanır.

Yönetilen etki alanını kullanması gereken VM 'Ler oluşturup çalıştırdığınızda, ağ bağlantısının sağlanması gerekir. Bu ağ bağlantısı aşağıdaki yollarla belirtilebilir:

* Yönetilen etki alanının sanal ağında ek bir sanal ağ alt ağı oluşturun. Bu ek alt ağ, VM 'lerinizi oluşturduğunuz ve bağlayabileceğiniz yerdir.
    * VM 'Ler aynı sanal ağın bir parçası olduğundan, otomatik olarak ad çözümlemesi gerçekleştirebilir ve Azure AD DS etki alanı denetleyicileriyle iletişim kurabilir.
* Yönetilen etki alanının sanal ağından bir veya daha fazla ayrı sanal ağa Azure sanal ağ eşlemesi 'ni yapılandırın. Bu ayrı sanal ağlar, VM 'lerinizi oluşturduğunuz ve bağlayabileceğiniz yerdir.
    * Sanal Ağ eşlemesini yapılandırırken, DNS ayarlarını Azure AD DS etki alanı denetleyicilerine yeniden kullanmak için de yapılandırmanız gerekir.

Genellikle, bu ağ bağlantısı seçeneklerinden yalnızca birini kullanırsınız. Bu seçenek genellikle Azure kaynaklarınızı ayrı ayrı yönetmek istediğiniz şekilde olur.

* Azure AD DS ve bağlı VM 'Leri tek bir kaynak grubu olarak yönetmek istiyorsanız VM 'Ler için ek bir sanal ağ alt ağı oluşturabilirsiniz.
* Azure AD DS yönetimini ve ardından bağlı VM 'Leri ayırmak istiyorsanız sanal ağ eşlemesi kullanabilirsiniz.
    * Ayrıca, mevcut bir sanal ağa bağlı olan Azure ortamınızda var olan VM 'lere bağlantı sağlamak için sanal ağ eşlemesi kullanmayı da tercih edebilirsiniz.

Bu öğreticide, yalnızca bir sanal ağ bağlantısı seçeneğini yapılandırmanız gerekir.

Sanal ağın nasıl planlanacağı ve yapılandırılacağı hakkında daha fazla bilgi için bkz. [Azure Active Directory Domain Services için ağ değerlendirmeleri][network-considerations].

## <a name="create-a-virtual-network-subnet"></a>Sanal ağ alt ağı oluştur

Varsayılan olarak, yönetilen etki alanı ile oluşturulan Azure sanal ağı tek bir sanal ağ alt ağı içerir. Bu sanal ağ alt ağı, yönetilen etki alanı hizmetleri sağlamak için yalnızca Azure platformu tarafından kullanılmalıdır. Bu Azure sanal ağında kendi sanal makinelerinizi oluşturup kullanmak için ek bir alt ağ oluşturun.

VM 'Ler ve uygulama iş yükleri için bir sanal ağ alt ağı oluşturmak için aşağıdaki adımları izleyin:

1. Azure portal, yönetilen etki alanınız için *Myresourcegroup* gibi kaynak grubunu seçin. Kaynak listesinden, *aeklemesine-VNET* gibi varsayılan sanal ağı seçin.
1. Sanal ağ penceresinin sol menüsünde **Adres alanı**' nı seçin. Sanal ağ, varsayılan alt ağ tarafından kullanılan *10.0.2.0/24* tek bir adres alanı ile oluşturulur.

    Sanal ağa ek bir IP adresi aralığı ekleyin. Bu adres aralığının ve kullanılacak gerçek IP adresi aralığının boyutu, zaten dağıtılmış olan diğer ağ kaynaklarına bağlıdır. IP adresi aralığı, Azure veya şirket içi ortamınızda var olan tüm adres aralıklarıyla çakışmamalıdır. Alt ağa dağıtmayı düşündüğünüz VM sayısı için yeterince büyük olan IP adresi aralığını boyutlandırdığınızdan emin olun.

    Aşağıdaki örnekte, ek bir IP adresi aralığı *10.0.3.0/24* eklenmiştir. Hazırlanıyor, **Kaydet**' i seçin.

    ![Azure portal ek bir sanal ağ IP adresi aralığı ekleyin](./media/tutorial-configure-networking/add-vnet-address-range.png)

1. Sonra, sanal ağ penceresinin sol menüsünde **alt ağlar**' ı seçin, sonra da **+ alt** ağ ' i seçerek alt ağ ekleyin.
1. Alt ağ için *iş yükleri* gibi bir ad girin. Gerekirse, önceki adımlarda sanal ağ için yapılandırılan IP adresi aralığının bir alt kümesini kullanmak istiyorsanız **adres aralığını** güncelleştirin. Şimdilik, ağ güvenlik grubu, yol tablosu, hizmet uç noktaları gibi seçenekler için varsayılan değerleri bırakın.

    Aşağıdaki örnekte, *10.0.3.0/24* IP adresi aralığını kullanan *iş yükleri* adlı bir alt ağ oluşturulur:

    ![Azure portal ek bir sanal ağ alt ağı ekleme](./media/tutorial-configure-networking/add-vnet-subnet.png)

1. Hazırlık sırasında **Tamam**' ı seçin. Sanal ağ alt ağının oluşturulması birkaç dakika sürer.

Yönetilen etki alanını kullanması gereken bir VM oluşturduğunuzda, bu sanal ağ alt ağını seçtiğinizden emin olun. Varsayılan *aeklemeleri-alt ağında* VM oluşturmayın. Farklı bir sanal ağ seçerseniz, sanal ağ eşlemesini yapılandırmadığınız müddetçe, yönetilen etki alanına ulaşmak için ağ bağlantısı ve DNS çözümlemesi yoktur.

## <a name="configure-virtual-network-peering"></a>Sanal Ağ eşlemesini yapılandırma

VM 'Ler için mevcut bir Azure sanal ağınız olabilir veya yönetilen etki alanı sanal ağınızı ayrı tutmak isteyebilirsiniz. Yönetilen etki alanını kullanmak için, diğer sanal ağlardaki VM 'Lerin Azure AD DS etki alanı denetleyicileriyle iletişim kurmak için bir yol gerekir. Bu bağlantı, Azure sanal ağ eşlemesi kullanılarak sağlanmalıdır.

Azure sanal ağ eşlemesi ile, bir sanal özel ağ (VPN) cihazına gerek olmadan iki sanal ağ birbirine bağlanır. Ağ eşleme, sanal ağları hızlı bir şekilde bağlamanıza ve Azure ortamınızda trafik akışlarını tanımlamanıza olanak sağlar.

Eşleme hakkında daha fazla bilgi için bkz. [Azure sanal ağ eşlemesi genel bakış][peering-overview].

Bir sanal ağı yönetilen etki alanı sanal ağına aktarmak için, şu adımları izleyin:

1. Yönetilen etki alanınız için oluşturulan *aeklemeleri-VNET* adlı varsayılan sanal ağı seçin.
1. Sanal ağ penceresinin sol menüsünde, eşlemeler ' **i seçin.**
1. Bir eşleme oluşturmak için **+ Ekle**' yi seçin. Aşağıdaki örnekte, varsayılan *AI* 'Ler *myvnet* adlı bir sanal ağ ile eşlenmez. Aşağıdaki ayarları kendi değerlerinizle yapılandırın:

    * **Aeklemesine-VNET 'ten uzak sanal ağa eşleme adı**: *aeklemeleri-VNET-to-myvnet* gibi iki ağın açıklayıcı bir tanımlayıcısı
    * **Sanal ağ dağıtım türü**: *Kaynak Yöneticisi*
    * **Abonelik**: *Azure* gibi, eşdüzey yapmak istediğiniz sanal ağın aboneliği
    * **Sanal ağ**: eşdüzey yapmak istediğiniz sanal ağ- *myvnet* gibi
    * **Myvnet 'ten aeklemesine-VNET 'e eşlemenin adı**: *myvnet-to-aeklemeleri-VNET* gibi iki ağın açıklayıcı bir tanımlayıcısı

    ![Azure portal sanal ağ eşlemesini yapılandırma](./media/tutorial-configure-networking/create-peering.png)

    Ortamınız için özel gereksinimleriniz olmadığı takdirde, sanal ağ erişimi veya iletilen trafik için diğer varsayılan değerleri bırakın, ardından **Tamam**' ı seçin.

1. Hem Azure AD DS sanal ağı hem de seçtiğiniz sanal ağ üzerinde eşleme oluşturulması birkaç dakika sürer. ' I hazırsanız, aşağıdaki örnekte gösterildiği gibi, **eşleme durumu** *bağlı* olarak raporlar:

    ![Eşlenen ağlar Azure portal başarıyla bağlandı](./media/tutorial-configure-networking/connected-peering.png)

Eşlenen sanal ağdaki VM 'Lerin yönetilen etki alanını kullanabilmesi için DNS sunucularını doğru ad çözümlemesi için izin verecek şekilde yapılandırın.

### <a name="configure-dns-servers-in-the-peered-virtual-network"></a>Eşlenen sanal ağda DNS sunucularını yapılandırma

Eşlenen sanal ağdaki VM 'Ler ve uygulamalar için, yönetilen etki alanı ile başarılı bir şekilde konuşabilmek için DNS ayarları güncelleştirilmeleri gerekir. Azure AD DS etki alanı denetleyicilerinin IP adresleri, eşlenen sanal ağdaki DNS sunucuları olarak yapılandırılmalıdır. Etki alanı denetleyicilerini eşlenen sanal ağ için DNS sunucuları olarak yapılandırmanın iki yolu vardır:

* Azure sanal ağ DNS sunucularını Azure AD DS etki alanı denetleyicilerini kullanacak şekilde yapılandırın.
* Yönetilen etki alanına sorguları yönlendirmek için, eşlenen sanal ağda kullanımda olan DNS sunucusunu koşullu DNS iletmeyi kullanacak şekilde yapılandırın. Bu adımlar, kullanımda olan DNS sunucusuna bağlı olarak farklılık gösterir.

Bu öğreticide, Azure sanal ağ DNS sunucularını tüm sorguları Azure AD DS etki alanı denetleyicilerine yönlendirecek şekilde yapılandıralim.

1. Azure portal, eşlenen sanal ağın kaynak grubunu ( *Myresourcegroup* gibi) seçin. Kaynak listesinden, *Myvnet* gibi eşlenmiş sanal ağı seçin.
1. Sanal ağ penceresinin sol menüsünde **DNS sunucuları**' nı seçin.
1. Varsayılan olarak, bir sanal ağ yerleşik Azure tarafından sağlanmış DNS sunucularını kullanır. **Özel** DNS sunucuları kullanmayı seçin. Azure AD DS etki alanı denetleyicilerinin IP adreslerini (genellikle *10.0.2.4* ve *10.0.2.5*) girin. Portalda yönetilen etki alanının **genel bakış** PENCERESINDE bu IP adreslerini onaylayın.

    ![Sanal ağ DNS sunucularını Azure AD DS etki alanı denetleyicilerini kullanacak şekilde yapılandırma](./media/tutorial-configure-networking/custom-dns.png)

1. Hazırlanıyor, **Kaydet**' i seçin. Sanal ağ için DNS sunucularının güncelleştirilmesi birkaç dakika sürer.
1. Güncelleştirilmiş DNS ayarlarını sanal makinelere uygulamak için, eşlenen sanal ağa bağlı VM 'Leri yeniden başlatın.

Yönetilen etki alanını kullanması gereken bir VM oluşturduğunuzda, bu eşlenen sanal ağı seçtiğinizden emin olun. Farklı bir sanal ağ seçerseniz, yönetilen etki alanına ulaşmak için ağ bağlantısı ve DNS çözümlemesi yoktur.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
> * Etki alanına katılmış kaynaklara yönelik sanal ağ bağlantısı seçeneklerini Azure AD DS anlayın
> * Azure AD DS sanal ağında bir IP adresi aralığı ve ek alt ağ oluşturma
> * Sanal Ağ eşlemesini Azure AD DS ayrı bir ağa yapılandırın

Bu yönetilen etki alanını işlem içinde görmek için bir sanal makineyi oluşturun ve etki alanına ekleyin.

> [!div class="nextstepaction"]
> [Windows Server sanal makinesini yönetilen etki alanınıza katma](join-windows-vm.md)

<!-- INTERNAL LINKS --> 
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[peering-overview]: ../virtual-network/virtual-network-peering-overview.md
[network-considerations]: network-considerations.md
