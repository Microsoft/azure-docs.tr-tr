---
title: Azure AD Domain Services 'de Güvenli LDAP sorunlarını giderme | Microsoft Docs
description: Azure Active Directory Domain Services yönetilen bir etki alanı için Güvenli LDAP (LDAPS) sorunlarını giderme hakkında bilgi edinin
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 445c60da-e115-447b-841d-96739975bdf6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 09/19/2019
ms.author: iainfou
ms.openlocfilehash: 96aa463441c9e0f21e2ef1aa27c566b94e1e5f4f
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71257882"
---
# <a name="troubleshoot-secure-ldap-connectivity-issues-to-an-azure-active-directory-domain-services-managed-domain"></a>Azure Active Directory Domain Services yönetilen bir etki alanında güvenli LDAP bağlantısı sorunlarını giderme

Azure Active Directory Domain Services (Azure AD DS) ile iletişim kurmak için Basit Dizin Erişim Protokolü (LDAP) kullanan uygulamalar ve hizmetler, [GÜVENLI LDAP kullanacak şekilde yapılandırılabilir](tutorial-configure-ldaps.md). Güvenli LDAP 'nin düzgün çalışması için uygun bir sertifika ve gerekli ağ bağlantı noktalarının açık olması gerekir.

Bu makale, Azure AD DS 'da Güvenli LDAP erişimiyle ilgili sorunları gidermenize yardımcı olur.

## <a name="common-connection-issues"></a>Yaygın bağlantı sorunları

Güvenli LDAP kullanarak Azure AD DS yönetilen bir etki alanına bağlanmada sorun yaşıyorsanız, aşağıdaki sorun giderme adımlarını gözden geçirin. Her bir sorun giderme adımından sonra Azure AD DS yönetilen etki alanına yeniden bağlanmayı deneyin:

* Güvenli LDAP sertifikasının veren zincirinin istemcide güvenilir olması gerekir. Güven sağlamak için, kök sertifika yetkilisini (CA) istemcideki güvenilen kök sertifika deposuna ekleyebilirsiniz.
    * [Sertifikayı dışarı aktarıp istemci bilgisayarlara uyguladığınızdan][client-cert]emin olun.
* Yönetilen etki alanınız için Güvenli LDAP sertifikasının *Konu* veya *Konu ALTERNATIF adları* özniteliğinde DNS adına sahip olduğunu doğrulayın.
    * [GÜVENLI LDAP sertifika gereksinimlerini][certs-prereqs] gözden geçirin ve gerekirse bir değiştirme sertifikası oluşturun.
* *Ldp. exe* gibi LDAP ISTEMCISININ, IP adresi DEĞIL, DNS adı kullanarak Güvenli LDAP uç noktasına bağlandığını doğrulayın.
    * Azure AD DS tarafından yönetilen etki alanına uygulanan sertifika, hizmetin IP adreslerini değil, yalnızca DNS adlarını içermez.
* LDAP istemcisinin bağlandığı DNS adını denetleyin. Azure AD DS yönetilen etki alanında güvenli LDAP için genel IP adresine çözümlenmelidir.
    * DNS adı iç IP adresine çözümlenirse, DNS kaydını dış IP adresine çözülecek şekilde güncelleştirin.
* Dış bağlantı için ağ güvenlik grubu, internet 'ten gelen TCP bağlantı noktası 636 trafiğine izin veren bir kural içermelidir.
    * Sanal ağa doğrudan bağlı ancak dış bağlantılara sahip olmayan kaynaklardan Güvenli LDAP kullanarak Azure AD DS yönetilen etki alanına bağlanabiliyorsa, [GÜVENLI LDAP trafiğine izin veren bir ağ güvenlik grubu kuralı oluşturduğunuzdan][ldaps-nsg]emin olun.

## <a name="next-steps"></a>Sonraki adımlar

Hala sorun yaşıyorsanız, ek sorun giderme yardımı için [bir Azure destek isteği açın][azure-support] .

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[configure-ldaps]: tutorial-configure-ldaps.md
[certs-prereqs]: tutorial-configure-ldaps.md#create-a-certificate-for-secure-ldap
[client-cert]: tutorial-configure-ldaps.md#export-a-certificate-for-client-computers
[ldaps-nsg]: tutorial-configure-ldaps.md#lock-down-secure-ldap-access-over-the-internet
