---
title: Azure AD Domain Services hakkında sık sorulan sorular | Microsoft Docs
description: Azure Active Directory Domain Services için yapılandırma, yönetim ve kullanılabilirlik hakkında sık sorulan sorulardan bazılarını okuyun ve anlayın
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 48731820-9e8c-4ec2-95e8-83dba1e58775
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 09/03/2019
ms.author: iainfou
ms.openlocfilehash: 61c9d887f1b193258161cd96ccfa3618e8371890
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74081016"
---
# <a name="frequently-asked-questions-faqs"></a>Sık sorulan sorular (SSS)

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
* [Konuk kullanıcıların dizinme Azure AD Domain Services kullanmasına davet edilebilir mi?](#can-guest-users-invited-to-my-directory-use-azure-ad-domain-services)
* [Mevcut bir Azure AD Domain Services yönetilen etki alanını farklı bir aboneliğe, kaynak grubuna, bölgeye veya sanal ağa taşıyabilir miyim?](#can-i-move-an-existing-azure-ad-domain-services-managed-domain-to-a-different-subscription-resource-group-region-or-virtual-network)

### <a name="can-i-create-multiple-managed-domains-for-a-single-azure-ad-directory"></a>Tek bir Azure AD dizini için birden çok yönetilen etki alanı oluşturabilir miyim?
Hayır. Tek bir Azure AD dizini için yalnızca Azure AD Domain Services tarafından hizmet verilen tek bir yönetilen etki alanı oluşturabilirsiniz.

### <a name="can-i-enable-azure-ad-domain-services-in-a-classic-virtual-network"></a>Azure AD Domain Services, klasik bir sanal ağda etkinleştirebilir miyim?
Klasik sanal ağlar Yeni dağıtımlar için desteklenmez. Klasik sanal ağlarda dağıtılan mevcut yönetilen etki alanları desteklenmeye devam eder.

### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-resource-manager-virtual-network"></a>Azure Resource Manager bir sanal ağda Azure AD Domain Services etkinleştirebilir miyim?
Evet. Azure AD Domain Services, Azure Resource Manager bir sanal ağda etkinleştirilebilir. Klasik Azure sanal ağları, yeni bir yönetilen etki alanı oluşturduğunuz sırada artık desteklenmez.

### <a name="can-i-migrate-my-existing-managed-domain-from-a-classic-virtual-network-to-a-resource-manager-virtual-network"></a>Mevcut yönetilen etki alanını klasik bir sanal ağdan Kaynak Yöneticisi sanal ağa geçirebilir miyim?
Evet, bu özellik önizleme aşamasındadır. Daha fazla bilgi için bkz. [Klasik sanal ağ modelinden Azure AD Domain Services geçirme Kaynak Yöneticisi (Önizleme)](migrate-from-classic-vnet.md).

### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-csp-cloud-solution-provider-subscription"></a>Azure CSP (bulut çözümü sağlayıcısı) aboneliğindeki Azure AD Domain Services etkinleştirebilir miyim?
Evet. Daha fazla bilgi için bkz. [Azure CSP aboneliklerinde Azure AD Domain Services etkinleştirme](csp.md).

### <a name="can-i-enable-azure-ad-domain-services-in-a-federated-azure-ad-directory-i-do-not-synchronize-password-hashes-to-azure-ad-can-i-enable-azure-ad-domain-services-for-this-directory"></a>Azure AD Domain Services federe bir Azure AD dizininde etkinleştirebilir miyim? Parola karmalarını Azure AD ile eşitleyemiyorum. Bu dizin için Azure AD Domain Services etkinleştirebilir miyim?
Hayır. NTLM veya Kerberos aracılığıyla kullanıcıların kimliğini doğrulamak için Azure AD Domain Services Kullanıcı hesaplarının parola karmalarını erişmesi gerekir. Bir Federasyon dizininde, parola karmaları Azure AD dizininde depolanmaz. Bu nedenle Azure AD Domain Services, bu Azure AD dizinleriyle çalışmaz.

### <a name="can-i-make-azure-ad-domain-services-available-in-multiple-virtual-networks-within-my-subscription"></a>Azure AD Domain Services Aboneliğimde birden çok sanal ağda kullanılabilir yapabilir miyim?
Hizmetin kendisi bu senaryoyu doğrudan desteklemez. Yönetilen etki alanınız tek seferde yalnızca bir sanal ağda kullanılabilir. Ancak, diğer sanal ağlara Azure AD Domain Services göstermek için birden çok sanal ağ arasında bağlantı yapılandırabilirsiniz. Daha fazla bilgi için bkz. VPN ağ geçitleri veya [sanal ağ eşlemesi](../virtual-network/virtual-network-peering-overview.md) [kullanarak Azure 'da sanal ağları bağlama](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md) .

### <a name="can-i-enable-azure-ad-domain-services-using-powershell"></a>PowerShell 'i kullanarak Azure AD Domain Services etkinleştirebilir miyim?
Evet. Daha fazla bilgi için bkz. [PowerShell kullanarak Azure AD Domain Services etkinleştirme](powershell-create-instance.md).

### <a name="can-i-enable-azure-ad-domain-services-using-a-resource-manager-template"></a>Kaynak Yöneticisi şablonu kullanarak Azure AD Domain Services etkinleştirebilir miyim?
Evet, Kaynak Yöneticisi şablonu kullanarak Azure AD Domain Services yönetilen bir etki alanı oluşturabilirsiniz. Şablon dağıtılmadan önce Azure portal veya Azure PowerShell kullanılarak yönetim için bir hizmet sorumlusu ve Azure AD grubu oluşturulmalıdır. Azure portal Azure AD Domain Services yönetilen bir etki alanı oluşturduğunuzda, şablonu ek dağıtımlar ile kullanılmak üzere dışarı aktarma seçeneği vardır. Ayrıca [GitHub şablonları örnek deposunda örnek bir şablon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-AAD-DomainServices)de vardır.

### <a name="can-i-add-domain-controllers-to-an-azure-ad-domain-services-managed-domain"></a>Azure AD Domain Services yönetilen bir etki alanına etki alanı denetleyicileri ekleyebilir miyim?
Hayır. Azure AD Domain Services tarafından belirtilen etki alanı, yönetilen bir etki alanıdır. Bu etki alanı için etki alanı denetleyicileri sağlamanız, yapılandırmanız veya başka bir şekilde yönetmeniz gerekmez. Bu yönetim etkinlikleri, Microsoft tarafından bir hizmet olarak sağlanır. Bu nedenle, yönetilen etki alanı için ek etki alanı denetleyicileri (salt okuma-yazma veya salt okuma) ekleyemezsiniz.

### <a name="can-guest-users-invited-to-my-directory-use-azure-ad-domain-services"></a>Konuk kullanıcıların dizinme Azure AD Domain Services kullanmasına davet edilebilir mi?
Hayır. Azure [AD B2B](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md) davet Işlemi KULLANıLARAK Azure AD dizininize davet edilen konuk kullanıcılar Azure AD Domain Services yönetilen etki alanınız ile eşitlenir. Ancak, bu kullanıcıların parolaları Azure AD dizininizde depolanmaz. Bu nedenle Azure AD Domain Services, bu kullanıcılar için NTLM ve Kerberos karmalarını yönetilen etki alanınız içinde eşitlememe yolu yoktur. Bu tür kullanıcılar oturum açamaz veya bilgisayarları yönetilen etki alanına katılamaz.

### <a name="can-i-move-an-existing-azure-ad-domain-services-managed-domain-to-a-different-subscription-resource-group-region-or-virtual-network"></a>Mevcut bir Azure AD Domain Services yönetilen etki alanını farklı bir aboneliğe, kaynak grubuna, bölgeye veya sanal ağa taşıyabilir miyim?
Hayır. Azure AD Domain Services yönetilen bir etki alanı oluşturduktan sonra, örneği farklı bir kaynak grubuna, sanal ağa, aboneliğe vb. taşıyamazsınız. Azure AD DS örneğini dağıtırken en uygun aboneliği, kaynak grubunu, bölgeyi ve sanal ağı seçin.

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

### <a name="can-i-connect-to-the-domain-controller-for-my-managed-domain-using-remote-desktop"></a>Yönetilen etki alanım için Uzak Masaüstü 'Nü kullanarak etki alanı denetleyicisine bağlanabilir miyim?
Hayır. Uzak Masaüstü 'Nü kullanarak yönetilen etki alanı için etki alanı denetleyicilerine bağlanma izniniz yok. *AAD DC yöneticileri* grubunun üyeleri, Active Directory Yönetim MERKEZI (ADAC) veya ad POWERSHELL gibi ad yönetim araçlarını kullanarak yönetilen etki alanını yönetebilir. Bu araçlar, yönetilen etki alanına katılmış bir Windows Server üzerinde *uzak sunucu yönetim araçları* özelliği kullanılarak yüklenir. Daha fazla bilgi için bkz. [Azure AD Domain Services yönetilen bir etki alanını yapılandırmak ve yönetmek için YÖNETIM VM oluşturma](tutorial-create-management-vm.md).

### <a name="ive-enabled-azure-ad-domain-services-what-user-account-do-i-use-to-domain-join-machines-to-this-domain"></a>Azure AD Domain Services etkinleştirdim. Makinelere bu etki alanına katılması için hangi kullanıcı hesabını kullanabilirim?
*AAD DC yöneticileri* yönetim grubunun üyeleri, etki alanına katılabilir makinelere sahip olabilir. Ayrıca, bu grubun üyelerine, etki alanına katılmış makinelere uzak masaüstü erişimi verilir.

### <a name="do-i-have-domain-administrator-privileges-for-the-managed-domain-provided-by-azure-ad-domain-services"></a>Azure AD Domain Services tarafından belirtilen yönetilen etki alanı için etki alanı yöneticisi ayrıcalıklarına sahip mıyım?
Hayır. Yönetilen etki alanında yönetim ayrıcalıkları verilmemiş. Etki alanı *Yöneticisi* ve *Kurumsal Yönetici* ayrıcalıkları, etki alanı içinde kullanabilmeniz için kullanılamaz. Şirket içi Active Directory etki alanı yöneticisinin veya kurumsal yönetici gruplarının üyelerine, yönetilen etki alanında etki alanı/kuruluş yöneticisi ayrıcalıkları de verilmez.

### <a name="can-i-modify-group-memberships-using-ldap-or-other-ad-administrative-tools-on-managed-domains"></a>Yönetilen etki alanlarında LDAP veya diğer AD Yönetim araçlarını kullanarak grup üyeliklerini değiştirebilir miyim?
Hayır. Grup üyelikleri, Azure AD Domain Services tarafından hizmet verilen etki alanlarında değiştirilemez. Aynı Kullanıcı öznitelikleri için de geçerlidir. Grup üyeliklerini veya Kullanıcı özniteliklerini Azure AD 'de ya da şirket içi etki alanında değiştirebilirsiniz. Değişiklikler Azure AD Domain Services otomatik olarak eşitlenir.

### <a name="how-long-does-it-take-for-changes-i-make-to-my-azure-ad-directory-to-be-visible-in-my-managed-domain"></a>Azure AD dizinimde, yönetilen etki alanım 'da görünür hale gelen değişiklikler için ne kadar sürer?
Azure AD Kullanıcı arabirimi veya PowerShell kullanılarak Azure AD dizininizde yapılan değişiklikler, yönetilen etki alanınız ile otomatik olarak eşitlenir. Bu eşitleme işlemi arka planda çalışır. Bu eşitlemenin tüm nesne değişikliklerini tamamlaması için tanımlı bir zaman aralığı yok.

### <a name="can-i-extend-the-schema-of-the-managed-domain-provided-by-azure-ad-domain-services"></a>Azure AD Domain Services tarafından belirtilen yönetilen etki alanının şemasını genişletebilir miyim?
Hayır. Şema, yönetilen etki alanı için Microsoft tarafından yönetilir. Şema uzantıları Azure AD Domain Services tarafından desteklenmez.

### <a name="can-i-modify-or-add-dns-records-in-my-managed-domain"></a>Yönetilen etki alanım 'da DNS kayıtlarını değiştirebilir veya ekleyebilir miyim?
Evet. *AAD DC Administrators* grubunun üyelerine, yönetilen etkı alanındaki DNS kayıtlarını değiştirmek Için *DNS yönetici* ayrıcalıkları verilir. Bu kullanıcılar, DNS yönetimi için, yönetilen etki alanına katılmış Windows Server çalıştıran bir makinede DNS Yöneticisi konsolunu kullanabilirler. DNS Yöneticisi konsolunu kullanmak için, sunucu üzerinde *uzak sunucu yönetim araçları* isteğe bağlı özelliğinin bir parçası olan *DNS sunucusu araçları*'nı yüklersiniz. Daha fazla bilgi için bkz. [Azure AD Domain Services yönetilen bir etki alanında DNS yönetme](manage-dns.md).

### <a name="what-is-the-password-lifetime-policy-on-a-managed-domain"></a>Yönetilen bir etki alanında parola ömrü ilkesi nedir?
Azure AD Domain Services yönetilen bir etki alanında varsayılan parola ömrü 90 gündür. Bu parola ömrü, Azure AD 'de yapılandırılan parola ömrü ile eşitlenmez. Bu nedenle, kullanıcıların parolalarının yönetilen etki alanında sona ereceği, ancak Azure AD 'de hala geçerli olduğu bir durum olabilir. Bu senaryolarda, kullanıcıların Azure AD 'de parolasını değiştirmesi ve yeni parolanın yönetilen etki alanınız ile eşitlenmesi gerekir. Ayrıca, Kullanıcı hesapları için *parola-sona erme tarihi* ve *Kullanıcı-değiştirme-oturum açma* öznitelikleri, yönetilen etki alanınız ile eşitlenmez.

### <a name="does-azure-ad-domain-services-provide-ad-account-lockout-protection"></a>Azure AD Domain Services AD hesabı kilitleme koruması sağlar mi?
Evet. Yönetilen etki alanında 2 dakika içinde 5 geçersiz parola denemesi, bir kullanıcı hesabının 30 dakika boyunca kilitlenmesine neden olur. 30 dakika sonra, Kullanıcı hesabı otomatik olarak kilidi açılır. Yönetilen etki alanında geçersiz parola denemeleri, Azure AD 'de Kullanıcı hesabını kilitlemez. Kullanıcı hesabı yalnızca Azure AD Domain Services yönetilen etki alanı içinde kilitlenir. Daha fazla bilgi için bkz. [yönetilen etki alanlarında parola ve hesap kilitleme ilkeleri](password-policy.md).

## <a name="billing-and-availability"></a>Faturalandırma ve kullanılabilirlik

* [Ücretli bir hizmet Azure AD Domain Services mı?](#is-azure-ad-domain-services-a-paid-service)
* [Hizmet için ücretsiz deneme sürümü var mı?](#is-there-a-free-trial-for-the-service)
* [Azure AD Domain Services yönetilen bir etki alanını duraklatabilir miyim?](#can-i-pause-an-azure-ad-domain-services-managed-domain)
* [Azure AD Domain Services bir DR olayı için başka bir bölgeye yük devreder miyim?](#can-i-pause-an-azure-ad-domain-services-managed-domain)
* [Enterprise Mobility Suite 'in (EMS) bir parçası olarak Azure AD Domain Services alabilir miyim? Azure AD Domain Services kullanmak için Azure AD Premium ihtiyacım var mı?](#can-i-failover-azure-ad-domain-services-to-another-region-for-a-dr-event)
* [Hizmet hangi Azure bölgelerini kullanabilir?](#can-i-get-azure-ad-domain-services-as-part-of-enterprise-mobility-suite-ems-do-i-need-azure-ad-premium-to-use-azure-ad-domain-services)

### <a name="is-azure-ad-domain-services-a-paid-service"></a>Ücretli bir hizmet Azure AD Domain Services mı?
Evet. Daha fazla bilgi edinmek için bkz. [fiyatlandırma sayfası](https://azure.microsoft.com/pricing/details/active-directory-ds/).

### <a name="is-there-a-free-trial-for-the-service"></a>Hizmet için ücretsiz deneme sürümü var mı?
Azure AD Domain Services, Azure için ücretsiz denemeye dahildir. [Azure 'un ücretsiz bir aylık deneme sürümüne](https://azure.microsoft.com/pricing/free-trial/)kaydolabilirsiniz.

### <a name="can-i-pause-an-azure-ad-domain-services-managed-domain"></a>Azure AD Domain Services yönetilen bir etki alanını duraklatabilir miyim?
Hayır. Azure AD Domain Services yönetilen bir etki alanını etkinleştirdikten sonra, yönetilen etki alanını silinceye kadar hizmet seçtiğiniz sanal ağınız içinde kullanılabilir. Hizmeti duraklatmanın bir yolu yoktur. Faturalandırma, yönetilen etki alanını silene kadar saatlik olarak devam eder.

### <a name="can-i-failover-azure-ad-domain-services-to-another-region-for-a-dr-event"></a>Azure AD Domain Services bir DR olayı için başka bir bölgeye yük devreder miyim?
Hayır. Azure AD Domain Services Şu anda coğrafi olarak yedekli bir dağıtım modeli sağlamıyor. Ida bir Azure bölgesindeki tek bir sanal ağla sınırlı değildir. Birden çok Azure bölgesini kullanmak istiyorsanız, Azure IaaS VM 'lerinde Active Directory Etki Alanı denetleyicilerinizi çalıştırmanız gerekir. Mimari kılavuzumuzu [burada](https://docs.microsoft.com/azure/architecture/reference-architectures/identity/adds-extend-domain)bulabilirsiniz.

### <a name="can-i-get-azure-ad-domain-services-as-part-of-enterprise-mobility-suite-ems-do-i-need-azure-ad-premium-to-use-azure-ad-domain-services"></a>Enterprise Mobility Suite 'in (EMS) bir parçası olarak Azure AD Domain Services alabilir miyim? Azure AD Domain Services kullanmak için Azure AD Premium ihtiyacım var mı?
Hayır. Azure AD Domain Services, Kullandıkça Öde Azure hizmetidir ve EMS 'nin bir parçası değildir. Azure AD Domain Services, tüm Azure AD sürümleriyle (ücretsiz ve Premium) kullanılabilir. Kullanıma bağlı olarak saatlik olarak faturalandırılırsınız.

### <a name="what-azure-regions-is-the-service-available-in"></a>Hizmet hangi Azure bölgelerini kullanabilir?
Azure AD Domain Services kullanılabildiği Azure bölgelerinin bir listesini görmek için [bölgeye göre Azure hizmetleri](https://azure.microsoft.com/regions/#services/) sayfasına bakın.

## <a name="troubleshooting"></a>Sorun giderme

Azure AD Domain Services yapılandırma veya yönetme ile ilgili sık karşılaşılan sorunların çözümleri için [sorun giderme kılavuzuna](troubleshoot.md) bakın.

## <a name="next-steps"></a>Sonraki adımlar

Azure AD Domain Services hakkında daha fazla bilgi edinmek için bkz. [Azure Active Directory Domain Services nedir?](overview.md).

Başlamak için bkz. [Azure Active Directory Domain Services örneği oluşturma ve yapılandırma](tutorial-create-instance.md).
