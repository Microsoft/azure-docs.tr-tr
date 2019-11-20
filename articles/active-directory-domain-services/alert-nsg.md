---
title: Azure AD DS ağ güvenlik grubu uyarılarını çözümleme | Microsoft Docs
description: Azure Active Directory Domain Services için ağ güvenlik grubu yapılandırma uyarılarını sorun giderme ve çözümleme hakkında bilgi edinin
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 95f970a7-5867-4108-a87e-471fa0910b8c
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 09/19/2019
ms.author: iainfou
ms.openlocfilehash: 959f1e3f25602938d769c574ea975c4bba9300e1
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71257996"
---
# <a name="known-issues-network-configuration-alerts-in-azure-active-directory-domain-services"></a>Bilinen sorunlar: Azure Active Directory Domain Services 'de ağ yapılandırması uyarıları

Uygulama ve hizmetlerin Azure Active Directory Domain Services (Azure AD DS) ile doğru iletişim kurmasına izin vermek için belirli ağ bağlantı noktalarının açık olması gerekir. Azure 'da ağ güvenlik gruplarını kullanarak trafik akışını kontrol edersiniz. Azure AD DS yönetilen bir etki alanının sistem durumu, gerekli ağ güvenlik grubu kuralları yerinde değilse bir uyarı gösterir.

Bu makale, ağ güvenlik grubu yapılandırma sorunları için genel uyarıları anlamanıza ve çözmenize yardımcı olur.

## <a name="alert-aadds104-network-error"></a>Uyarı AADDS104: Ağ hatası

### <a name="alert-message"></a>Uyarı iletisi

*Microsoft bu yönetilen etki alanı için etki alanı denetleyicilerine ulaşamıyor. Sanal ağınızda yapılandırılan bir ağ güvenlik grubu (NSG), yönetilen etki alanına erişimi engelliyorsa bu durum oluşabilir. Olası bir neden, internet 'ten gelen trafiği engelleyen Kullanıcı tanımlı bir yol olup olmalarıdır.*

Azure AD DS için ağ hatalarının en yaygın nedeni geçersiz ağ güvenlik grubu kuralları. Sanal ağ için ağ güvenlik grubu, belirli bağlantı noktalarına ve protokollere erişime izin vermelidir. Bu bağlantı noktaları engellenirse Azure platformu, yönetilen etki alanını izleyemez veya güncelleştiremez. Azure AD dizini ile Azure AD DS yönetilen etki alanı arasındaki eşitleme de etkilenir. Hizmette kesintiye uğramasını önlemek için varsayılan bağlantı noktalarını açık tutmanız gerekir.

## <a name="default-security-rules"></a>Varsayılan güvenlik kuralları

Aşağıdaki varsayılan gelen ve giden güvenlik kuralları, Azure AD DS yönetilen bir etki alanı için ağ güvenlik grubuna uygulanır. Bu kurallar Azure AD DS güvende tutar ve Azure platformunun yönetilen etki alanını izlemesine, yönetmesine ve güncelleştirmesine izin verir. Ayrıca, [GÜVENLI LDAP yapılandırırsanız][configure-ldaps]gelen trafiğe izin veren ek bir kuralınız olabilir.

### <a name="inbound-security-rules"></a>Gelen güvenlik kuralları

| Priority | Name | Port | Protocol | Kaynak | Destination | Action |
|----------|------|------|----------|--------|-------------|--------|
| 101      | AllowSyncWithAzureAD | 443 | TCP | AzureActiveDirectoryDomainServices | Any | Allow |
| 201      | AllowRD | 3389 | TCP | Corpnetgördünüz | Any | Allow |
| 301      | AllowPSRemoting | 5986| TCP | AzureActiveDirectoryDomainServices | Any | Allow |
| 65000    | Allvnetınbound | Any | Any | VirtualNetwork | VirtualNetwork | Allow |
| 65001    | AllowAzureLoadBalancerInBound | Any | Any | AzureLoadBalancer | Any | Allow |
| 65500    | DenyAllInBound | Any | Any | Any | Any | Reddet |

### <a name="outbound-security-rules"></a>Giden güvenlik kuralları

| Priority | Name | Port | Protocol | Kaynak | Destination | Action |
|----------|------|------|----------|--------|-------------|--------|
| 65000    | Allvnetoutbağlanmadı | Any | Any | VirtualNetwork | VirtualNetwork | Allow |
| 65001    | AllowAzureLoadBalancerOutBound | Any | Any |  Any | İnternet | Allow |
| 65500    | DenyAllOutBound | Any | Any | Any | Any | Reddet |

>[!NOTE]
> Azure AD DS sanal ağdan sınırsız giden erişime ihtiyaç duyuyor. Sanal ağ için giden erişimi kısıtlayan ek kurallar oluşturmanızı önermiyoruz.

## <a name="verify-and-edit-existing-security-rules"></a>Mevcut güvenlik kurallarını doğrulama ve düzenleme

Mevcut güvenlik kurallarını doğrulamak ve varsayılan bağlantı noktalarının açık olduğundan emin olmak için aşağıdaki adımları izleyin:

1. Azure portal **ağ güvenlik grupları**' nı arayıp seçin.
1. Yönetilen etki alanınız ile ilişkili ağ güvenlik grubunu (örneğin, *Aeklemesine-contoso.com-NSG*) seçin.
1. **Genel bakış** sayfasında, var olan gelen ve giden güvenlik kuralları gösterilir.

    Gelen ve giden kurallarını gözden geçirin ve önceki bölümde gerekli kuralların listesiyle karşılaştırın. Gerekirse, gerekli trafiği engelleyen tüm özel kuralları seçin ve silin. Gerekli kuralların herhangi biri eksikse, sonraki bölümde bir kural ekleyin.

    Gerekli trafiğe izin veren kuralları ekledikten veya sildikten sonra, Azure AD DS yönetilen etki alanının sistem durumu otomatik olarak iki saat içinde güncelleştirilir ve uyarıyı kaldırır.

### <a name="add-a-security-rule"></a>Güvenlik kuralı ekle

Eksik bir güvenlik kuralı eklemek için aşağıdaki adımları izleyin:

1. Azure portal **ağ güvenlik grupları**' nı arayıp seçin.
1. Yönetilen etki alanınız ile ilişkili ağ güvenlik grubunu (örneğin, *Aeklemesine-contoso.com-NSG*) seçin.
1. Sol bölmedeki **Ayarlar** ' ın altında, hangi kurala eklemek istediğinize bağlı olarak *gelen güvenlik kuralları* veya *giden güvenlik kuralları* ' na tıklayın.
1. **Ekle**' yi seçin, ardından bağlantı noktası, protokol, yön vb. temel alarak gerekli kuralı oluşturun. Hazırlık sırasında **Tamam**' ı seçin.

Güvenlik kuralının eklenmesi ve listede gösterilmesi birkaç dakika sürer.

## <a name="next-steps"></a>Sonraki adımlar

Hala sorun yaşıyorsanız, ek sorun giderme yardımı için [bir Azure destek isteği açın][azure-support] .

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[configure-ldaps]: tutorial-configure-ldaps.md
