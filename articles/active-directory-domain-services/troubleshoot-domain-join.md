---
title: Azure AD Domain Services etki alanına ekleme sorunlarını giderme | Microsoft Docs
description: Bir VM 'ye katılmayı veya bir uygulamayı Azure Active Directory Domain Services bağlamayı denediğinizde ve yönetilen etki alanına bağlanamadığınızda veya kimlik doğrulayabileceğinizden sık karşılaşılan sorunları nasıl giderebileceğinizi öğrenin.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 10/02/2019
ms.author: iainfou
ms.openlocfilehash: 1016fbc1478ec713d50a2f04bcc80d08288b03f3
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71827232"
---
# <a name="troubleshoot-domain-join-problems-with-an-azure-ad-domain-services-managed-domain"></a>Azure AD Domain Services yönetilen bir etki alanı ile etki alanı ekleme sorunlarını giderme

Bir sanal makineyi (VM) katılmayı veya bir uygulamayı Azure Active Directory Domain Services (AD DS) yönetilen bir etki alanına bağlamayı denediğinizde, yapabağlanamadığınızı bir hata alabilirsiniz. Etki alanına ekleme sorunlarını gidermek için, aşağıdaki noktalarda bir sorun olduğunu gözden geçirin:

* Bir kimlik doğrulama istemi almazsanız, VM veya uygulama Azure AD DS yönetilen etki alanına bağlanamaz.
    * [Etki alanına katılacak bağlantı sorunlarını](#connectivity-issues-for-domain-join)gidermeye başlayın.
* Kimlik doğrulaması sırasında bir hata alırsanız, Azure AD DS yönetilen etki alanı bağlantısı başarılı olur.
    * [Etki alanı birleşimi sırasında kimlik bilgileriyle ilgili sorunları](#credentials-related-issues-during-domain-join)gidermeye başlayın.

## <a name="connectivity-issues-for-domain-join"></a>Etki alanına katılacak bağlantı sorunları

VM Azure AD DS yönetilen etki alanını bulamazsa, genellikle bir ağ bağlantısı veya yapılandırma sorunu vardır. Sorunu bulmak ve çözmek için aşağıdaki sorun giderme adımlarını gözden geçirin:

1. VM 'nin aynı veya Azure AD DS için etkinleştirilmiş eşlenmiş bir sanal ağa bağlı olduğundan emin olun. Aksi takdirde, VM, katılabilmek için etki alanını bulup bağlanamaz.
    * VM aynı sanal ağa bağlı değilse, sanal ağ eşleme veya VPN bağlantısının *etkin* olduğunu veya trafiğin doğru şekilde akmasını sağlamak için *bağlı* olduğunu doğrulayın.
1. @No__t-0 gibi Azure AD DS yönetilen etki alanının etki alanı adını kullanarak etki alanına ping işlemi yapmayı deneyin.
    * Ping yanıtı başarısız olursa, Azure AD DS yönetilen etki alanınız için Portal 'daki Genel Bakış sayfasında (`ping 10.0.0.4` gibi) görüntülenecek etki alanının IP adreslerini ping yapmayı deneyin.
    * IP adresine başarılı bir şekilde ping işlemi yapabiliyorsanız, DNS yanlış yapılandırılmış olabilir. Sanal ağ için Azure AD DS yönetilen etki alanı DNS sunucularını yapılandırdığınızdan emin olun.
1. Sanal makinede DNS çözümleyici önbelleğini (`ipconfig /flushdns`) temizlemeye çalışın.

### <a name="network-security-group-nsg-configuration"></a>Ağ güvenlik grubu (NSG) yapılandırması

Azure AD DS yönetilen bir etki alanı oluşturduğunuzda, başarılı etki alanı işlemi için uygun kurallara sahip bir ağ güvenlik grubu da oluşturulur. Ek ağ güvenlik grubu kuralları düzenler veya oluşturursanız, Azure AD DS bağlantı ve kimlik doğrulama hizmetleri sağlamak için gerekli olan bağlantı noktalarını istemeden engelmeyebilirsiniz. Bu ağ güvenlik grubu kuralları, parola eşitleme, kullanıcıların oturum açamayacak veya etki alanına katılması sorunları gibi sorunların oluşmasına neden olabilir.

Bağlantı sorunlarıyla karşılaşmaya devam ederseniz, aşağıdaki sorun giderme adımlarını gözden geçirin:

1. Azure portal Azure AD DS yönetilen etki alanının sistem durumunu kontrol edin. *AADDS001*için bir uyarıınız varsa, bir ağ güvenlik grubu kuralı erişimi engelliyor demektir.
1. [Gerekli bağlantı noktalarını ve ağ güvenlik grubu kurallarını][network-ports]gözden geçirin. Bağlı olduğunuz VM veya sanal ağa hiçbir ağ güvenlik grubu kuralı, bu ağ bağlantı noktalarını engellemediğinizden emin olun.
1. Herhangi bir ağ güvenlik grubu yapılandırma sorunu çözümlendikten sonra, *AADDS001* uyarısı yaklaşık 2 saat içinde sistem durumu sayfasından kaldırılır. Ağ bağlantısı artık kullanılabilir olduğundan, VM 'yi yeniden birleştirmeyi deneyin.

## <a name="credentials-related-issues-during-domain-join"></a>Etki alanına ekleme sırasında kimlik bilgileri ile ilgili sorunlar

Azure AD DS yönetilen etki alanına katılması için kimlik bilgileri isteyen bir iletişim kutusu alırsanız, sanal makine Azure sanal ağını kullanarak etki alanına bağlanabilir. Etki alanına ekleme işlemi, etki alanına veya yetkilendirmede kimlik doğrulama işlemini, kimlik bilgileri tarafından sağlanan kimlik bilgilerini kullanarak tamamlamaya başarısız olur.

Kimlik bilgileriyle ilgili sorunları gidermek için aşağıdaki sorun giderme adımlarını gözden geçirin:

1. @No__t-0 gibi kimlik bilgilerini belirtmek için UPN biçimini kullanmayı deneyin. Azure AD 'de bu UPN 'nin doğru yapılandırıldığından emin olun.
    * Kiracınızda aynı UPN ön ekine sahip birden fazla kullanıcı varsa veya UPN önekiniz aşırı uzunsa, hesabınız için *sAMAccountName* otomatik olarak oluşturulabilir. Bu nedenle, hesabınız için *sAMAccountName* biçimi, şirket içi etki alanında beklediğiniz veya kullandığınız verilerden farklı olabilir.
1. VM 'Leri Azure AD DS tarafından yönetilen etki alanına katmak için *AAD DC yöneticileri* grubuna ait bir kullanıcı hesabının kimlik bilgilerini kullanmayı deneyin.
1. [Parola eşitlemesini][enable-password-sync] etkinleştirdiğinizden ve ilk parola eşitlemenin tamamlanabilmesi için yeterince uzun süre beklediğinizden emin olun.

## <a name="next-steps"></a>Sonraki adımlar

Etki alanı ekleme işleminin bir parçası olarak Active Directory süreçlerini daha ayrıntılı bir şekilde anlamak için bkz. [JOIN ve Authentication sorunları][join-authentication-issues].

VM 'nize Azure AD DS yönetilen etki alanına katılma sorunları yaşıyorsanız, [yardım bulun ve Azure Active Directory için bir destek bileti açın][azure-ad-support].

<!-- INTERNAL LINKS -->
[enable-password-sync]: tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds
[network-ports]: network-considerations.md#network-security-groups-and-required-ports
[azure-ad-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md

<!-- EXTERNAL LINKS -->
[join-authentication-issues]: /previous-versions/windows/it-pro/windows-2000-server/cc961817(v=technet.10)
