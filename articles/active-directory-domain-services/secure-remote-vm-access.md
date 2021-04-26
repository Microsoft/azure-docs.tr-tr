---
title: Azure AD Domain Services 'de uzak VM erişiminin güvenliğini sağlama | Microsoft Docs
description: Ağ Ilkesi sunucusu (NPS) ve Azure AD Multi-Factor Authentication kullanarak VM 'lere uzaktan erişimi Azure Active Directory Domain Services yönetilen bir etki alanında Uzak Masaüstü Hizmetleri dağıtımıyla güvenli hale getirme hakkında bilgi edinin.
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: f0605cbd81d8131014a1f6a6bf30e3db0ce9aa90
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96618935"
---
# <a name="secure-remote-access-to-virtual-machines-in-azure-active-directory-domain-services"></a>Azure Active Directory Domain Services 'de sanal makinelere uzaktan erişimi güvenli hale getirme

Azure Active Directory Domain Services (Azure AD DS) tarafından yönetilen etki alanında çalışan sanal makinelere (VM 'Ler) uzaktan erişimi güvenli hale getirmek için Uzak Masaüstü Hizmetleri (RDS) ve ağ Ilkesi sunucusu (NPS) kullanabilirsiniz. Azure AD DS, kullanıcıların RDS ortamı üzerinden erişim istediklerinde kimliklerini doğrular. Gelişmiş güvenlik için, oturum açma olayları sırasında ek bir kimlik doğrulama istemi sağlamak üzere Azure AD Multi-Factor Authentication tümleştirebilirsiniz. Azure AD Multi-Factor Authentication, bu özelliği sağlamak üzere NPS uzantısını kullanır.

> [!IMPORTANT]
> Azure AD DS yönetilen bir etki alanında sanal makinelerinize güvenli bir şekilde bağlanmak için önerilen yol, sanal ağınızda sağladığınız tam platform tarafından yönetilen bir PaaS hizmeti olan Azure savunma 'yı kullanmaktır. Savunma ana bilgisayarı, doğrudan SSL üzerinden Azure portal sanal makinelerinize güvenli ve sorunsuz Uzak Masaüstü Protokolü (RDP) bağlantısı sağlar. Bir savunma ana bilgisayarı aracılığıyla bağlandığınızda, sanal makinelerinize genel bir IP adresi gerekmez ve TCP bağlantı noktası 3389 ' de RDP 'ye erişimi göstermek için ağ güvenlik grupları 'nı kullanmanız gerekmez.
>
> Desteklenen tüm bölgelerde Azure savunma kullanmanızı kesinlikle öneririz. Azure savunma kullanılabilirliği olmayan bölgelerde, Azure savunma kullanılabilir olana kadar bu makalede açıklanan adımları izleyin. Tüm gelen RDP trafiğine izin verilen Azure AD DS 'a katılmış VM 'lere genel IP adresleri atamaya dikkat edin.
>
> Daha fazla bilgi için bkz. Azure savunma nedir [?][bastion-overview].

Bu makalede, Azure AD DS 'de RDS 'yi yapılandırma ve isteğe bağlı olarak Azure AD Multi-Factor Authentication NPS uzantısını kullanma işlemlerinin nasıl yapılacağı gösterilir.

![Uzak Masaüstü Hizmetleri (RDS) genel bakış](./media/enable-network-policy-server/remote-desktop-services-overview.png)

## <a name="prerequisites"></a>Önkoşullar

Bu makaleyi tamamlayabilmeniz için aşağıdaki kaynaklara ihtiyacınız vardır:

* Etkin bir Azure aboneliği.
    * Azure aboneliğiniz yoksa [bir hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Abonelikle ilişkili bir Azure Active Directory kiracısı, şirket içi bir dizinle veya yalnızca bulut diziniyle eşitlenir.
    * Gerekirse, [bir Azure Active Directory kiracı oluşturun][create-azure-ad-tenant] veya [bir Azure aboneliğini hesabınızla ilişkilendirin][associate-azure-ad-tenant].
* Azure AD kiracınızda etkinleştirilmiş ve yapılandırılmış Azure Active Directory Domain Services yönetilen bir etki alanı.
    * Gerekirse, [Azure Active Directory Domain Services yönetilen bir etki alanı oluşturun ve yapılandırın][create-azure-ad-ds-instance].
* Azure Active Directory Domain Services sanal ağınızda oluşturulan bir *iş yükü* alt ağı.
    * Gerekirse, [Azure Active Directory Domain Services yönetilen bir etki alanı için sanal ağ yapılandırın][configure-azureadds-vnet].
* Azure AD kiracınızda *Azure AD DC Administrators* grubunun üyesi olan bir kullanıcı hesabı.

## <a name="deploy-and-configure-the-remote-desktop-environment"></a>Uzak masaüstü ortamını dağıtma ve yapılandırma

Başlamak için, Windows Server 2016 veya Windows Server 2019 çalıştıran en az iki Azure VM oluşturun. Uzak Masaüstü (RD) ortamınızın artıklığı ve yüksek kullanılabilirliği için, ek konakları daha sonra dengeleyebilir ve yük dengeleyebilirsiniz.

Önerilen bir RDS dağıtımı aşağıdaki iki VM 'yi içerir:

* *RDGVM01* -RD Bağlantı Aracısı sunucusunu, RD Web Erişimi sunucusunu ve RD Ağ Geçidi sunucusunu çalıştırır.
* *RDSHVM01* -RD Oturumu Ana Bilgisayarı sunucusunu çalıştırır.

VM 'Lerin Azure AD DS sanal ağınızın bir *iş yükü* alt ağına dağıtıldığından emin olun ve ardından VM 'leri yönetilen etki alanına katın. Daha fazla bilgi için bkz. [Windows Server VM oluşturma ve yönetilen bir etki alanına katma][tutorial-create-join-vm].

RD ortamı dağıtımı bir dizi adım içerir. Mevcut RD dağıtım kılavuzu, yönetilen bir etki alanında kullanılmak üzere belirli bir değişiklik yapılmadan kullanılabilir:

1. *Azure AD DC yöneticileri* grubunun bir parçası olan *contosoadmin* gibi bir hesapla RD ortamı için oluşturulan VM 'lerde oturum açın.
1. RDS oluşturmak ve yapılandırmak için, mevcut [Uzak Masaüstü ortamı dağıtım kılavuzunu][deploy-remote-desktop]kullanın. RD sunucusu bileşenlerini istediğiniz şekilde Azure sanal makinelerinize dağıtın.
    * Azure AD DS 'e özgü-RD lisansını yapılandırırken, dağıtım kılavuzunda belirtildiği şekilde **Kullanıcı başına** değil, **cihaz başına** moduna ayarlayın.
1. Bir Web tarayıcısı kullanarak erişim sağlamak istiyorsanız, [kullanıcılarınız Için Uzak Masaüstü Web istemcisini ayarlayın][rd-web-client].

Yönetilen etki alanına dağıtılan RD ile hizmeti, şirket içi AD DS etki alanı ile yaptığınız gibi yönetebilir ve kullanabilirsiniz.

## <a name="deploy-and-configure-nps-and-the-azure-ad-mfa-nps-extension"></a>NPS 'yi ve Azure AD MFA NPS uzantısını dağıtma ve yapılandırma

Kullanıcı oturum açma deneyiminin güvenliğini artırmak istiyorsanız, isteğe bağlı olarak RD ortamını Azure AD Multi-Factor Authentication ile tümleştirebilirsiniz. Bu yapılandırmayla, kullanıcılar kimlik doğrulamak için oturum açma sırasında ek bir istem alırlar.

Bu özelliği sağlamak için, ortamınızda Azure AD Multi-Factor Authentication NPS Uzantısı ile birlikte ek bir ağ Ilkesi sunucusu (NPS) yüklenir. Bu uzantı, çok faktörlü kimlik doğrulama istemlerinin durumunu istemek ve döndürmek için Azure AD ile tümleşir.

Kullanıcıların [Azure ad Multi-Factor Authentication kullanmak için kayıtlı][user-mfa-registration]olmaları gerekir, bu da ek Azure AD lisansları gerektirebilir.

Azure AD Multi-Factor Authentication ' de Azure AD DS uzak masaüstü ortamınızda bütünleştirmek için, bir NPS sunucusu oluşturun ve uzantıyı yüklemelisiniz:

1. Azure AD DS sanal ağınızdaki bir *iş yükü* alt ağına bağlı *NPSVM01* gibi ek bir Windows Server 2016 veya 2019 sanal makinesi oluşturun. VM 'yi yönetilen etki alanına ekleyin.
1. NPS VM 'de *contosoadmin* gıbı *Azure AD DC yöneticileri* grubunun bir parçası olan hesap olarak oturum açın.
1. **Sunucu Yöneticisi**, **rol ve Özellik Ekle**' yi seçin, ardından *Ağ İlkesi ve erişim Hizmetleri* rolünü yükler.
1. [Azure AD MFA NPS uzantısını yüklemek ve yapılandırmak][nps-extension]için mevcut nasıl yapılır makalesini kullanın.

NPS sunucusu ve Azure AD Multi-Factor Authentication NPS uzantısı yüklüyken, RD ortamıyla kullanılmak üzere yapılandırmak için bir sonraki bölümü doldurun.

## <a name="integrate-remote-desktop-gateway-and-azure-ad-multi-factor-authentication"></a>Uzak Masaüstü Ağ Geçidi ve Azure AD Multi-Factor Authentication tümleştirin

Azure AD Multi-Factor Authentication NPS uzantısını bütünleştirmek için, [ağ Ilkesi sunucusu (NPS) uzantısını ve Azure AD 'yi kullanarak Uzak Masaüstü Ağ Geçidi altyapınızı bütünleştirmek][azure-mfa-nps-integration]üzere mevcut nasıl yapılır makalesini kullanın.

Yönetilen bir etki alanı ile tümleştirme için aşağıdaki ek yapılandırma seçenekleri gereklidir:

1. [NPS sunucusunu Active Directory kaydetme][register-nps-ad]. Bu adım yönetilen bir etki alanında başarısız olur.
1. [Ağ ilkesini yapılandırmak için 4. adım][create-nps-policy]' da, **Kullanıcı hesabı Içeri arama özelliklerini yok saymak** için kutuyu işaretleyin.
1. NPS sunucusu ve Azure AD Multi-Factor Authentication NPS uzantısı için Windows Server 2019 kullanıyorsanız, güvenli kanalı NPS sunucusunun doğru iletişim kurmasına izin verecek şekilde güncelleştirmek için aşağıdaki komutu çalıştırın:

    ```powershell
    sc sidtype IAS unrestricted
    ```

Kullanıcılar artık Microsoft Authenticator uygulamasında bir SMS mesajı veya istem gibi oturum açtıklarında ek bir kimlik doğrulama faktörü istenir.

## <a name="next-steps"></a>Sonraki adımlar

Dağıtımınızın dayanıklılığını artırma hakkında daha fazla bilgi için bkz. [Uzak Masaüstü Hizmetleri-yüksek kullanılabilirlik][rds-high-availability].

Kullanıcı oturum açma güvenliğini sağlama hakkında daha fazla bilgi için bkz. [nasıl çalıştığını öğrenin: Azure AD Multi-Factor Authentication][concepts-mfa].

<!-- INTERNAL LINKS -->
[bastion-overview]: ../bastion/bastion-overview.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[configure-azureadds-vnet]: tutorial-configure-networking.md
[tutorial-create-join-vm]: join-windows-vm.md
[user-mfa-registration]: ../active-directory/authentication/howto-mfa-nps-extension.md#register-users-for-mfa
[nps-extension]: ../active-directory/authentication/howto-mfa-nps-extension.md
[azure-mfa-nps-integration]: ../active-directory/authentication/howto-mfa-nps-extension-rdg.md
[register-nps-ad]:../active-directory/authentication/howto-mfa-nps-extension-rdg.md#register-server-in-active-directory
[create-nps-policy]: ../active-directory/authentication/howto-mfa-nps-extension-rdg.md#configure-network-policy
[concepts-mfa]: ../active-directory/authentication/concept-mfa-howitworks.md

<!-- EXTERNAL LINKS -->
[deploy-remote-desktop]: /windows-server/remote/remote-desktop-services/rds-deploy-infrastructure
[rd-web-client]: /windows-server/remote/remote-desktop-services/clients/remote-desktop-web-client-admin
[rds-high-availability]: /windows-server/remote/remote-desktop-services/rds-plan-high-availability