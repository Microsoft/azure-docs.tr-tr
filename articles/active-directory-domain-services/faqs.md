---
title: Azure AD Domain Services hakkında sık sorulan sorular | Microsoft Docs
description: Azure Active Directory Domain Services için yapılandırma, yönetim ve kullanılabilirlik hakkında sık sorulan sorulardan bazılarını okuyun ve anlayın
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 48731820-9e8c-4ec2-95e8-83dba1e58775
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 02/09/2021
ms.author: justinha
ms.openlocfilehash: 32dcf2b387231d50796de0036388b53cab83bf72
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107749339"
---
# <a name="frequently-asked-questions-faqs-about-azure-active-directory-ad-domain-services"></a>Azure Active Directory (AD) etki alanı Hizmetleri hakkında sık sorulan sorular (SSS)

Bu sayfa Azure Active Directory Domain Services hakkında sık sorulan soruları yanıtlar.

## <a name="configuration"></a>Yapılandırma

* [Tek bir Azure AD dizini için birden çok yönetilen etki alanı oluşturabilir miyim?](#can-i-create-multiple-managed-domains-for-a-single-azure-ad-directory)
* [Azure AD Domain Services, klasik bir sanal ağda etkinleştirebilir miyim?](#can-i-enable-azure-ad-domain-services-in-a-classic-virtual-network)
* [Azure Resource Manager bir sanal ağda Azure AD Domain Services etkinleştirebilir miyim?](#can-i-enable-azure-ad-domain-services-in-an-azure-resource-manager-virtual-network)
* [Mevcut yönetilen etki alanını klasik bir sanal ağdan Kaynak Yöneticisi sanal ağa geçirebilir miyim?](#can-i-migrate-my-existing-managed-domain-from-a-classic-virtual-network-to-a-resource-manager-virtual-network)
* [Azure CSP (bulut çözümü sağlayıcısı) aboneliğindeki Azure AD Domain Services etkinleştirebilir miyim?](#can-i-enable-azure-ad-domain-services-in-an-azure-csp-cloud-solution-provider-subscription)
* [Azure AD Domain Services federe bir Azure AD dizininde etkinleştirebilir miyim? Parola karmalarını Azure AD ile eşitleyemiyorum. Bu dizin için Azure AD Domain Services etkinleştirebilir miyim?](#can-i-enable-azure-ad-domain-services-in-a-federated-azure-ad-directory-i-do-not-synchronize-password-hashes-to-azure-ad-can-i-enable-azure-ad-domain-services-for-this-directory)
* [Azure AD Domain Services Aboneliğimde birden çok sanal ağda kullanılabilir yapabilir miyim?](#can-i-make-azure-ad-domain-services-available-in-multiple-virtual-networks-within-my-subscription)
* [PowerShell 'i kullanarak Azure AD Domain Services etkinleştirebilir miyim?](#can-i-enable-azure-ad-domain-services-using-powershell)
* [Kaynak Yöneticisi şablonu kullanarak Azure AD Domain Services etkinleştirebilir miyim?](#can-i-enable-azure-ad-domain-services-using-a-resource-manager-template)
* [Azure AD Domain Services yönetilen bir etki alanına etki alanı denetleyicileri ekleyebilir miyim?](#can-i-add-domain-controllers-to-an-azure-ad-domain-services-managed-domain)
* [Konuk kullanıcılar Azure AD Domain Services dizinime davet edilebilir mi?](#can-guest-users-be-invited-to-my-directory-use-azure-ad-domain-services)
* [Mevcut bir Azure AD Domain Services yönetilen etki alanını farklı bir aboneliğe, kaynak grubuna, bölgeye veya sanal ağa taşıyabilir miyim?](#can-i-move-an-existing-azure-ad-domain-services-managed-domain-to-a-different-subscription-resource-group-region-or-virtual-network)
* [Var olan bir Azure AD Domain Services etki alanı adını yeniden adlandırabilir miyim?](#can-i-rename-an-existing-azure-ad-domain-services-domain-name)
* [Yüksek kullanılabilirlik seçenekleri dahil Azure AD Domain Services mi?](#does-azure-ad-domain-services-include-high-availability-options)

### <a name="can-i-create-multiple-managed-domains-for-a-single-azure-ad-directory"></a>Tek bir Azure AD dizini için birden çok yönetilen etki alanı oluşturabilir miyim?
Hayır. Tek bir Azure AD dizini için yalnızca Azure AD Domain Services tarafından hizmet verilen tek bir yönetilen etki alanı oluşturabilirsiniz.

### <a name="can-i-enable-azure-ad-domain-services-in-a-classic-virtual-network"></a>Azure AD Domain Services, klasik bir sanal ağda etkinleştirebilir miyim?
Klasik sanal ağlar Yeni dağıtımlar için desteklenmez. Klasik sanal ağlarda dağıtılan mevcut yönetilen etki alanları, 1 Mart 2023 ' de devre dışı bırakılana kadar desteklenmeye devam eder. Mevcut dağıtımlar için [Azure AD Domain Services, klasik sanal ağ modelinden Kaynak Yöneticisi](migrate-from-classic-vnet.md)taşıyabilirsiniz.

Daha fazla bilgi için bkz. [resmi kullanımdan kaldırma bildirimi](https://azure.microsoft.com/updates/we-are-retiring-azure-ad-domain-services-classic-vnet-support-on-march-1-2023/).

### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-resource-manager-virtual-network"></a>Azure Resource Manager bir sanal ağda Azure AD Domain Services etkinleştirebilir miyim?
Evet. Azure AD Domain Services, Azure Resource Manager bir sanal ağda etkinleştirilebilir. Yönetilen bir etki alanı oluşturduğunuzda klasik Azure sanal ağları artık kullanılamaz.

### <a name="can-i-migrate-my-existing-managed-domain-from-a-classic-virtual-network-to-a-resource-manager-virtual-network"></a>Mevcut yönetilen etki alanını klasik bir sanal ağdan Kaynak Yöneticisi sanal ağa geçirebilir miyim?
Evet. Daha fazla bilgi için bkz. [Klasik sanal ağ modelinden Azure AD Domain Services kaynak yöneticisi 'ye geçirme](migrate-from-classic-vnet.md).

### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-csp-cloud-solution-provider-subscription"></a>Azure CSP (bulut çözümü sağlayıcısı) aboneliğindeki Azure AD Domain Services etkinleştirebilir miyim?
Evet. Daha fazla bilgi için bkz. [Azure CSP aboneliklerinde Azure AD Domain Services etkinleştirme](csp.md).

### <a name="can-i-enable-azure-ad-domain-services-in-a-federated-azure-ad-directory-i-do-not-synchronize-password-hashes-to-azure-ad-can-i-enable-azure-ad-domain-services-for-this-directory"></a>Azure AD Domain Services federe bir Azure AD dizininde etkinleştirebilir miyim? Parola karmalarını Azure AD ile eşitleyemiyorum. Bu dizin için Azure AD Domain Services etkinleştirebilir miyim?
Hayır. NTLM veya Kerberos aracılığıyla kullanıcıların kimliğini doğrulamak için Azure AD Domain Services Kullanıcı hesaplarının parola karmalarını erişmesi gerekir. Bir Federasyon dizininde, parola karmaları Azure AD dizininde depolanmaz. Bu nedenle Azure AD Domain Services, bu Azure AD dizinleriyle çalışmaz.

Ancak, Parola karması eşitleme için Azure AD Connect kullanıyorsanız, Parola karması değerleri Azure AD 'de depolandığından Azure AD Domain Services kullanabilirsiniz.

### <a name="can-i-make-azure-ad-domain-services-available-in-multiple-virtual-networks-within-my-subscription"></a>Azure AD Domain Services Aboneliğimde birden çok sanal ağda kullanılabilir yapabilir miyim?
Hizmetin kendisi bu senaryoyu doğrudan desteklemez. Yönetilen etki alanınız tek seferde yalnızca bir sanal ağda kullanılabilir. Ancak, diğer sanal ağlara Azure AD Domain Services göstermek için birden çok sanal ağ arasında bağlantı yapılandırabilirsiniz. Daha fazla bilgi için bkz. VPN ağ geçitleri veya [sanal ağ eşlemesi](../virtual-network/virtual-network-peering-overview.md) [kullanarak Azure 'da sanal ağları bağlama](../vpn-gateway/vpn-gateway-howto-vnet-vnet-portal-classic.md) .

### <a name="can-i-enable-azure-ad-domain-services-using-powershell"></a>PowerShell 'i kullanarak Azure AD Domain Services etkinleştirebilir miyim?
Evet. Daha fazla bilgi için bkz. [PowerShell kullanarak Azure AD Domain Services etkinleştirme](powershell-create-instance.md).

### <a name="can-i-enable-azure-ad-domain-services-using-a-resource-manager-template"></a>Kaynak Yöneticisi şablonu kullanarak Azure AD Domain Services etkinleştirebilir miyim?
Evet, Kaynak Yöneticisi şablonu kullanarak Azure AD Domain Services yönetilen bir etki alanı oluşturabilirsiniz. Şablon dağıtılmadan önce Azure portal veya Azure PowerShell kullanılarak yönetim için bir hizmet sorumlusu ve Azure AD grubu oluşturulmalıdır. Daha fazla bilgi için, bkz. [Azure Resource Manager şablonu kullanarak Azure AD DS yönetilen etki alanı oluşturma](template-create-instance.md). Azure portal Azure AD Domain Services yönetilen bir etki alanı oluşturduğunuzda, şablonu ek dağıtımlar ile kullanılmak üzere dışarı aktarma seçeneği de vardır.

### <a name="can-i-add-domain-controllers-to-an-azure-ad-domain-services-managed-domain"></a>Azure AD Domain Services yönetilen bir etki alanına etki alanı denetleyicileri ekleyebilir miyim?
Hayır. Azure AD Domain Services tarafından belirtilen etki alanı, yönetilen bir etki alanıdır. Bu etki alanı için etki alanı denetleyicileri sağlamanız, yapılandırmanız veya başka bir şekilde yönetmeniz gerekmez. Bu yönetim etkinlikleri, Microsoft tarafından bir hizmet olarak sağlanır. Bu nedenle, yönetilen etki alanı için ek etki alanı denetleyicileri (salt okuma-yazma veya salt okuma) ekleyemezsiniz.

### <a name="can-guest-users-be-invited-to-my-directory-use-azure-ad-domain-services"></a>Konuk kullanıcılar Azure AD Domain Services dizinime davet edilebilir mi?
Hayır. Azure [AD B2B](../active-directory/external-identities/what-is-b2b.md) davet Işlemi KULLANıLARAK Azure AD dizininize davet edilen konuk kullanıcılar Azure AD Domain Services yönetilen etki alanınız ile eşitlenir. Ancak, bu kullanıcıların parolaları Azure AD dizininizde depolanmaz. Bu nedenle Azure AD Domain Services, bu kullanıcılar için NTLM ve Kerberos karmalarını yönetilen etki alanınız içinde eşitlememe yolu yoktur. Bu tür kullanıcılar oturum açamaz veya bilgisayarları yönetilen etki alanına katılamaz.

### <a name="can-i-move-an-existing-azure-ad-domain-services-managed-domain-to-a-different-subscription-resource-group-region-or-virtual-network"></a>Mevcut bir Azure AD Domain Services yönetilen etki alanını farklı bir aboneliğe, kaynak grubuna, bölgeye veya sanal ağa taşıyabilir miyim?
Hayır. Azure AD Domain Services yönetilen bir etki alanı oluşturduktan sonra, yönetilen etki alanını farklı bir kaynak grubuna, sanal ağa, aboneliğe vb. taşıyamazsınız. Yönetilen etki alanını dağıtırken en uygun aboneliği, kaynak grubunu, bölgeyi ve sanal ağı seçin.

### <a name="can-i-rename-an-existing-azure-ad-domain-services-domain-name"></a>Var olan bir Azure AD Domain Services etki alanı adını yeniden adlandırabilir miyim?
Hayır. Azure AD Domain Services yönetilen bir etki alanı oluşturduktan sonra DNS etki alanı adını değiştiremezsiniz. Yönetilen etki alanını oluştururken dikkatle DNS etki alanı adını seçin. DNS etki alanı adını seçerken dikkate alınması gereken [Azure AD Domain Services yönetilen bir etki alanı oluşturma ve yapılandırma hakkında bilgi için öğreticiye](tutorial-create-instance.md#create-a-managed-domain)bakın.

### <a name="does-azure-ad-domain-services-include-high-availability-options"></a>Yüksek kullanılabilirlik seçenekleri dahil Azure AD Domain Services mi?

Evet. Her Azure AD Domain Services yönetilen etki alanı iki etki alanı denetleyicisi içerir. Bu etki alanı denetleyicilerini yönetmeyin veya bunlara bağlanamazsınız, yönetilen hizmetin bir parçasıdır. Kullanılabilirlik Alanları destekleyen bir bölgeye Azure AD Domain Services dağıtırsanız, etki alanı denetleyicileri bölgeler arasında dağıtılır. Kullanılabilirlik Alanları desteklemeyen bölgelerde, etki alanı denetleyicileri kullanılabilirlik kümeleri arasında dağıtılır. Bu dağıtım üzerinde yapılandırma seçenekleriniz veya yönetim denetiminiz yok. Daha fazla bilgi için bkz. [Azure 'da sanal makineler Için kullanılabilirlik seçenekleri](../virtual-machines/availability.md).

## <a name="administration-and-operations"></a>Yönetim ve işlemler

* [Yönetilen etki alanım için Uzak Masaüstü 'Nü kullanarak etki alanı denetleyicisine bağlanabilir miyim?](#can-i-connect-to-the-domain-controller-for-my-managed-domain-using-remote-desktop)
* [Azure AD Domain Services etkinleştirdim. Makinelere bu etki alanına katılması için hangi kullanıcı hesabını kullanabilirim?](#ive-enabled-azure-ad-domain-services-what-user-account-do-i-use-to-domain-join-machines-to-this-domain)
* [Azure AD Domain Services tarafından belirtilen yönetilen etki alanı için etki alanı yöneticisi ayrıcalıklarına sahip mıyım?](#do-i-have-domain-administrator-privileges-for-the-managed-domain-provided-by-azure-ad-domain-services)
* [Yönetilen etki alanlarında LDAP veya diğer AD Yönetim araçlarını kullanarak grup üyeliklerini değiştirebilir miyim?](#can-i-modify-group-memberships-using-ldap-or-other-ad-administrative-tools-on-managed-domains)
* [Azure AD dizinimde, yönetilen etki alanım 'da görünür hale gelen değişiklikler için ne kadar sürer?](#how-long-does-it-take-for-changes-i-make-to-my-azure-ad-directory-to-be-visible-in-my-managed-domain)
* [Azure AD Domain Services tarafından belirtilen yönetilen etki alanının şemasını genişletebilir miyim?](#can-i-extend-the-schema-of-the-managed-domain-provided-by-azure-ad-domain-services)
* [Yönetilen etki alanım 'da DNS kayıtlarını değiştirebilir veya ekleyebilir miyim?](#can-i-modify-or-add-dns-records-in-my-managed-domain)
* [Yönetilen bir etki alanında parola ömrü ilkesi nedir?](#what-is-the-password-lifetime-policy-on-a-managed-domain)
* [Azure AD Domain Services AD hesabı kilitleme koruması sağlar mi?](#does-azure-ad-domain-services-provide-ad-account-lockout-protection)
* [Azure AD Domain Services içinde Dağıtılmış Dosya Sistemi (DFS) ve çoğaltmayı yapılandırabilir miyim?](#can-i-configure-distributed-file-system-and-replication-within-azure-ad-domain-services)
* [Windows güncelleştirmeleri Azure AD Domain Services nasıl uygulanır?](#how-are-windows-updates-applied-in-azure-ad-domain-services)

### <a name="can-i-connect-to-the-domain-controller-for-my-managed-domain-using-remote-desktop"></a>Yönetilen etki alanım için Uzak Masaüstü 'Nü kullanarak etki alanı denetleyicisine bağlanabilir miyim?
Hayır. Uzak Masaüstü 'Nü kullanarak yönetilen etki alanı için etki alanı denetleyicilerine bağlanma izniniz yok. *AAD DC yöneticileri* grubunun üyeleri, Active Directory Yönetim MERKEZI (ADAC) veya ad POWERSHELL gibi ad yönetim araçlarını kullanarak yönetilen etki alanını yönetebilir. Bu araçlar, yönetilen etki alanına katılmış bir Windows Server üzerinde *uzak sunucu yönetim araçları* özelliği kullanılarak yüklenir. Daha fazla bilgi için bkz. [Azure AD Domain Services yönetilen bir etki alanını yapılandırmak ve yönetmek için YÖNETIM VM oluşturma](tutorial-create-management-vm.md).

### <a name="ive-enabled-azure-ad-domain-services-what-user-account-do-i-use-to-domain-join-machines-to-this-domain"></a>Azure AD Domain Services etkinleştirdim. Makinelere bu etki alanına katılması için hangi kullanıcı hesabını kullanabilirim?
Yönetilen etki alanının parçası olan herhangi bir kullanıcı hesabı bir VM 'ye katılabilir. *AAD DC Administrators* grubunun üyelerine, yönetilen etki alanına katılmış makinelere uzak masaüstü erişimi verilir.

### <a name="do-i-have-domain-administrator-privileges-for-the-managed-domain-provided-by-azure-ad-domain-services"></a>Azure AD Domain Services tarafından belirtilen yönetilen etki alanı için etki alanı yöneticisi ayrıcalıklarına sahip mıyım?
Hayır. Yönetilen etki alanında yönetim ayrıcalıkları verilmemiş. Etki alanı *Yöneticisi* ve *Kurumsal Yönetici* ayrıcalıkları, etki alanı içinde kullanabilmeniz için kullanılamaz. Şirket içi Active Directory etki alanı yöneticisinin veya kurumsal yönetici gruplarının üyelerine, yönetilen etki alanında etki alanı/kuruluş yöneticisi ayrıcalıkları de verilmez.

### <a name="can-i-modify-group-memberships-using-ldap-or-other-ad-administrative-tools-on-managed-domains"></a>Yönetilen etki alanlarında LDAP veya diğer AD Yönetim araçlarını kullanarak grup üyeliklerini değiştirebilir miyim?
Azure AD Domain Services Azure Active Directory 'ten eşitlenen kullanıcılar ve gruplar, kaynak kaynağı Azure Active Directory değiştirilemiyor. Bu, kullanıcıları veya grupları AADDC kullanıcıları tarafından yönetilen kuruluş biriminden Özel bir kuruluş birimine taşımayı içerir. Yönetilen etki alanında kaynak olan herhangi bir kullanıcı veya grup değiştirilebilir.  

### <a name="how-long-does-it-take-for-changes-i-make-to-my-azure-ad-directory-to-be-visible-in-my-managed-domain"></a>Azure AD dizinimde, yönetilen etki alanım 'da görünür hale gelen değişiklikler için ne kadar sürer?
Azure AD Kullanıcı arabirimi veya PowerShell kullanılarak Azure AD dizininizde yapılan değişiklikler, yönetilen etki alanınız ile otomatik olarak eşitlenir. Bu eşitleme işlemi arka planda çalışır. Bu eşitlemenin tüm nesne değişikliklerini tamamlaması için tanımlı bir zaman aralığı yok.

### <a name="can-i-extend-the-schema-of-the-managed-domain-provided-by-azure-ad-domain-services"></a>Azure AD Domain Services tarafından belirtilen yönetilen etki alanının şemasını genişletebilir miyim?
Hayır. Şema, yönetilen etki alanı için Microsoft tarafından yönetilir. Şema uzantıları Azure AD Domain Services tarafından desteklenmez.

### <a name="can-i-modify-or-add-dns-records-in-my-managed-domain"></a>Yönetilen etki alanım 'da DNS kayıtlarını değiştirebilir veya ekleyebilir miyim?
Evet. *AAD DC Administrators* grubunun üyelerine, yönetilen etkı alanındaki DNS kayıtlarını değiştirmek Için *DNS yönetici* ayrıcalıkları verilir. Bu kullanıcılar, DNS yönetimi için yönetilen etki alanına katılmış Windows Server çalıştıran bir makinede DNS Yöneticisi konsolunu kullanabilirler. DNS Yöneticisi konsolunu kullanmak için, sunucu üzerinde *uzak sunucu yönetim araçları* isteğe bağlı özelliğinin bir parçası olan *DNS sunucusu araçları*'nı yükler. Daha fazla bilgi için bkz. [Azure AD Domain Services yönetilen bir etki alanında DNS yönetme](manage-dns.md).

### <a name="what-is-the-password-lifetime-policy-on-a-managed-domain"></a>Yönetilen bir etki alanında parola ömrü ilkesi nedir?
Azure AD Domain Services yönetilen bir etki alanında varsayılan parola ömrü 90 gündür. Bu parola ömrü, Azure AD 'de yapılandırılan parola ömrü ile eşitlenmez. Bu nedenle, kullanıcıların parolalarının yönetilen etki alanında sona ereceği, ancak Azure AD 'de hala geçerli olduğu bir durum olabilir. Bu senaryolarda, kullanıcıların Azure AD 'de parolasını değiştirmesi ve yeni parolanın yönetilen etki alanınız ile eşitlenmesi gerekir. Yönetilen bir etki alanında varsayılan parola ömrünü değiştirmek istiyorsanız, [özel parola ilkeleri oluşturup yapılandırabilirsiniz.](password-policy.md)

Ayrıca, *Disablepasswordexpiasyon* IÇIN Azure AD parola ilkesi, yönetilen bir etki alanıyla eşitlenir. *Disablepasswordexpiasyon* , Azure AD 'de bir kullanıcıya uygulandığında, yönetilen etki alanındaki eşitlenmiş kullanıcının *UserAccountControl* değeri *DONT_EXPIRE_PASSWORD* uygulanmış olur.

Kullanıcılar Azure AD 'de parolalarını sıfırlarken *Forcechangepasswordnextsignın = true* özniteliği uygulanır. Yönetilen bir etki alanı bu özniteliği Azure AD 'den eşitler. Yönetilen etki alanı, Azure AD 'den eşitlenen bir kullanıcı için *Forcechangepasswordnextsignın* değerini algıladığında, yönetilen etki alanındaki *pwdLastSet* özniteliği *0* olarak ayarlanır ve bu, şu anda ayarlanmış parolayı geçersiz kılar.

### <a name="does-azure-ad-domain-services-provide-ad-account-lockout-protection"></a>Azure AD Domain Services AD hesabı kilitleme koruması sağlar mi?
Evet. Yönetilen etki alanında 2 dakika içinde 5 geçersiz parola denemesi, bir kullanıcı hesabının 30 dakika boyunca kilitlenmesine neden olur. 30 dakika sonra, Kullanıcı hesabı otomatik olarak kilidi açılır. Yönetilen etki alanında geçersiz parola denemeleri, Azure AD 'de Kullanıcı hesabını kilitlemez. Kullanıcı hesabı yalnızca Azure AD Domain Services yönetilen etki alanı içinde kilitlenir. Daha fazla bilgi için bkz. [yönetilen etki alanlarında parola ve hesap kilitleme ilkeleri](password-policy.md).

### <a name="can-i-configure-distributed-file-system-and-replication-within-azure-ad-domain-services"></a>Azure AD Domain Services içinde Dağıtılmış Dosya Sistemi ve çoğaltmayı yapılandırabilir miyim?
Hayır. Azure AD Domain Services kullanılırken Dağıtılmış Dosya Sistemi (DFS) ve çoğaltma kullanılamaz.

### <a name="how-are-windows-updates-applied-in-azure-ad-domain-services"></a>Windows güncelleştirmeleri Azure AD Domain Services nasıl uygulanır?
Yönetilen bir etki alanındaki etki alanı denetleyicileri, gerekli Windows güncelleştirmelerini otomatik olarak uygular. Burada yapılandırmak veya yönetmek için bir şey yoktur. Windows güncelleştirmelerine giden trafiği engelleyen ağ güvenlik grubu kuralları oluşturmayın. Yönetilen etki alanına katılmış kendi VM 'leriniz için, gerekli tüm işletim sistemi ve uygulama güncelleştirmelerini yapılandırmadan ve uygulamaktan siz sorumlusunuz.

## <a name="billing-and-availability"></a>Faturalandırma ve kullanılabilirlik

* [Ücretli bir hizmet Azure AD Domain Services mı?](#is-azure-ad-domain-services-a-paid-service)
* [Hizmet için ücretsiz deneme sürümü var mı?](#is-there-a-free-trial-for-the-service)
* [Azure AD Domain Services yönetilen bir etki alanını duraklatabilir miyim?](#can-i-pause-an-azure-ad-domain-services-managed-domain)
* [Azure AD Domain Services yük devri bir DR olayı için başka bir bölgeye verebilir miyim?](#can-i-pause-an-azure-ad-domain-services-managed-domain)
* [Enterprise Mobility Suite 'in (EMS) bir parçası olarak Azure AD Domain Services alabilir miyim? Azure AD Domain Services kullanmak için Azure AD Premium ihtiyacım var mı?](#can-i-fail-over-azure-ad-domain-services-to-another-region-for-a-dr-event)
* [Hizmet hangi Azure bölgelerini kullanabilir?](#can-i-get-azure-ad-domain-services-as-part-of-enterprise-mobility-suite-ems-do-i-need-azure-ad-premium-to-use-azure-ad-domain-services)

### <a name="is-azure-ad-domain-services-a-paid-service"></a>Ücretli bir hizmet Azure AD Domain Services mı?
Evet. Daha fazla bilgi edinmek için bkz. [fiyatlandırma sayfası](https://azure.microsoft.com/pricing/details/active-directory-ds/).

### <a name="is-there-a-free-trial-for-the-service"></a>Hizmet için ücretsiz deneme sürümü var mı?
Azure AD Domain Services, Azure için ücretsiz denemeye dahildir. [Azure 'un ücretsiz bir aylık deneme sürümüne](https://azure.microsoft.com/pricing/free-trial/)kaydolabilirsiniz.

### <a name="can-i-pause-an-azure-ad-domain-services-managed-domain"></a>Azure AD Domain Services yönetilen bir etki alanını duraklatabilir miyim?
Hayır. Azure AD Domain Services yönetilen bir etki alanını etkinleştirdikten sonra, yönetilen etki alanını silinceye kadar hizmet seçtiğiniz sanal ağınız içinde kullanılabilir. Hizmeti duraklatmanın bir yolu yoktur. Faturalandırma, yönetilen etki alanını silene kadar saatlik olarak devam eder.

### <a name="can-i-fail-over-azure-ad-domain-services-to-another-region-for-a-dr-event"></a>Azure AD Domain Services yük devri bir DR olayı için başka bir bölgeye verebilir miyim?
Evet, yönetilen bir etki alanı için coğrafi dayanıklılık sağlamak üzere Azure AD DS 'yi destekleyen herhangi bir Azure bölgesindeki eşlenmiş bir sanal ağa ek bir [çoğaltma kümesi](tutorial-create-replica-set.md) oluşturabilirsiniz. Çoğaltma kümeleri, yönetilen etki alanı ile aynı ad alanını ve yapılandırmayı paylaşır.

### <a name="can-i-get-azure-ad-domain-services-as-part-of-enterprise-mobility-suite-ems-do-i-need-azure-ad-premium-to-use-azure-ad-domain-services"></a>Enterprise Mobility Suite 'in (EMS) bir parçası olarak Azure AD Domain Services alabilir miyim? Azure AD Domain Services kullanmak için Azure AD Premium ihtiyacım var mı?
Hayır. Azure AD Domain Services, Kullandıkça Öde Azure hizmetidir ve EMS 'nin bir parçası değildir. Azure AD Domain Services, tüm Azure AD sürümleriyle (ücretsiz ve Premium) kullanılabilir. Kullanıma bağlı olarak saatlik olarak faturalandırılırsınız.

### <a name="what-azure-regions-is-the-service-available-in"></a>Hizmet hangi Azure bölgelerini kullanabilir?
Azure AD Domain Services kullanılabildiği Azure bölgelerinin bir listesini görmek için [bölgeye göre Azure hizmetleri](https://azure.microsoft.com/regions/#services/) sayfasına bakın.

## <a name="troubleshooting"></a>Sorun giderme

Azure AD Domain Services yapılandırma veya yönetme ile ilgili sık karşılaşılan sorunların çözümleri için [sorun giderme kılavuzuna](troubleshoot.md) bakın.

## <a name="next-steps"></a>Sonraki adımlar

Azure AD Domain Services hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory Domain Services nedir?](overview.md).

Başlamak için bkz. [Azure Active Directory Domain Services yönetilen etki alanı oluşturma ve yapılandırma](tutorial-create-instance.md).
