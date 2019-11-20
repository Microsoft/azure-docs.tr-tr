---
title: CloudSimple tarafından Azure VMware çözümü-özel bulutta vCenter Identity kaynaklarını ayarlama
description: VMware yöneticilerinin vCenter 'a erişmesi için Active Directory kimlik doğrulaması için özel bulut vCenter ' ın nasıl ayarlanacağını açıklar.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 9d2986acc47087c267193eee43136e030abcc422
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/28/2019
ms.locfileid: "72990314"
---
# <a name="set-up-vcenter-identity-sources-to-use-active-directory"></a>Kullanılacak vCenter Identity kaynaklarını ayarlayın Active Directory

## <a name="about-vmware-vcenter-identity-sources"></a>VMware vCenter Identity kaynakları hakkında

VMware vCenter, vCenter 'a erişen kullanıcıların kimlik doğrulaması için farklı kimlik kaynaklarını destekler.  CloudSimple özel bulut vCenter, VMware yöneticilerinin vCenter 'a erişmesi için Active Directory kimlik doğrulaması yapacak şekilde ayarlanabilir. Kurulum tamamlandığında, **cloudowner** kullanıcısı kimlik kaynağından vCenter 'a kullanıcı ekleyebilir.  

Active Directory etki alanınızı ve etki alanı denetleyicilerinizi aşağıdaki yollarla ayarlayabilirsiniz:

* Şirket içinde çalışan etki alanı ve etki alanı denetleyicileri Active Directory
* Azure aboneliğinizde sanal makineler olarak çalışan etki alanı ve etki alanı denetleyicileri Active Directory
* Özel bulutunuzda çalışan yeni Active Directory etki alanı ve etki alanı denetleyicileri
* Azure Active Directory hizmeti

Bu kılavuzda, şirket içinde veya aboneliklerinizde sanal makineler çalıştıran etki alanı ve etki alanı denetleyicileri Active Directory ayarlama görevleri açıklanmaktadır.  Azure AD 'yi kimlik kaynağı olarak kullanmak istiyorsanız, kimlik kaynağını ayarlamayla ilgili ayrıntılı yönergeler için [CloudSimple özel bulutu 'Nda vCenter için kimlik sağlayıcısı olarak Azure AD 'Yi kullanma](azure-ad.md) bölümüne bakın.

[Bir kimlik kaynağı eklemeden](#add-an-identity-source-on-vcenter)önce [vCenter ayrıcalıklarınızı geçici olarak ilerletin](escalate-private-cloud-privileges.md).

> [!CAUTION]
> Yeni kullanıcılar yalnızca *bulut sahibi grubu*, *bulut-genel-küme-yönetici-grubu*, *bulut-genel-depolama-yönetici-grubu*, bulut-genel- *Ağ-Yönetici-Grup* veya *bulut-genel-VM-yönetici grubu*için eklenmelidir.  *Yöneticiler* grubuna eklenen kullanıcılar otomatik olarak kaldırılacaktır.  Yalnızca hizmet hesaplarının *Yöneticiler* grubuna eklenmesi gerekir.  


## <a name="identity-source-options"></a>Kimlik kaynağı seçenekleri

* [Çoklu oturum açma kimlik kaynağı olarak şirket içi Active Directory ekleme](#add-on-premises-active-directory-as-a-single-sign-on-identity-source)
* [Özel bulutta yeni Active Directory ayarlama](#set-up-new-active-directory-on-a-private-cloud)
* [Azure 'da Active Directory ayarlama](#set-up-active-directory-on-azure)

## <a name="add-on-premises-active-directory-as-a-single-sign-on-identity-source"></a>Çoklu oturum açma kimlik kaynağı olarak şirket Içi Active Directory ekleme

Şirket içi Active Directory çoklu oturum açma kimlik kaynağı olarak ayarlamak için şunlar gerekir:

* Şirket içi veri merkezinizden özel bulutunuz için [siteden sıteye VPN bağlantısı](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway) .
* VCenter ve platform hizmetleri denetleyicisine (PSC) Şirket içi DNS sunucusu IP 'si eklendi.

Active Directory etki alanınızı ayarlarken aşağıdaki tablodaki bilgileri kullanın.

| **Seçenek** | **Açıklama** |
|------------|-----------------|
| **Ad** | Kimlik kaynağının adı. |
| **Kullanıcılar için temel DN** | Kullanıcılar için temel ayırt edici ad. |
| **Etki alanı adı** | Etki alanının FDQN 'ı, örneğin, example.com. Bu metin kutusunda bir IP adresi sağlamaın. |
| **Etki alanı diğer adı** | Etki alanı NetBIOS adı. SSPI kimlik doğrulamaları kullanıyorsanız, Active Directory etki alanının NetBIOS adını kimlik kaynağının diğer adı olarak ekleyin. |
| **Gruplar için temel DN** | Gruplar için temel ayırt edici ad. |
| **Birincil sunucu URL 'SI** | Etki alanı için birincil etki alanı denetleyicisi LDAP sunucusu.<br><br> `ldap://hostname:port` veya `ldaps://hostname:port`biçimini kullanın. Bağlantı noktası genellikle LDAP bağlantıları için 389 ve LDAPS bağlantıları için 636 ' dir. Birden çok etki alanı denetleyicisi dağıtımı Active Directory için, bağlantı noktası genellikle LDAP için 3268 ve LDAPS için 3269 ' dir.<br><br>Birincil veya ikincil LDAP URL 'sinde `ldaps://` kullandığınızda Active Directory sunucusunun LDAPS uç noktası için güven kuran bir sertifika gerekir. |
| **İkincil sunucu URL 'SI** | Yük devretme için kullanılan ikincil etki alanı denetleyicisi LDAP sunucusunun adresi. |
| **Sertifika Seç** | Active Directory LDAP sunucunuz veya OpenLDAP sunucu kimlik kaynağı ile LDAPS kullanmak istiyorsanız, URL metin kutusuna `ldaps://` yazdıktan sonra Sertifika Seç düğmesi görünür. İkincil bir URL gerekli değildir. |
| **Kullanıcı Adı** | Etki alanındaki, kullanıcılar ve gruplar için temel DN 'ye yönelik en az salt okuma erişimi olan bir kullanıcının KIMLIĞI. |
| **Parola** | Kullanıcı adı tarafından belirtilen kullanıcının parolası. |

Önceki tabloda yer alan bilgilere sahip olduğunuzda, vCenter 'da çoklu oturum açma kimlik kaynağı olarak şirket içi Active Directory ekleyebilirsiniz.

> [!TIP]
> [VMware belgeleri sayfasında](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.psc.doc/GUID-B23B1360-8838-4FF2-B074-71643C4CB040.html)çoklu oturum açma kimlik kaynakları hakkında daha fazla bilgi bulabilirsiniz.

## <a name="set-up-new-active-directory-on-a-private-cloud"></a>Özel bulutta yeni Active Directory ayarlama

Özel bulutunuzda yeni bir Active Directory etki alanı ayarlayabilir ve onu çoklu oturum açma için bir kimlik kaynağı olarak kullanabilirsiniz.  Active Directory etki alanı, var olan bir Active Directory ormanının bir parçası olabilir veya bağımsız bir orman olarak ayarlanabilir.

### <a name="new-active-directory-forest-and-domain"></a>Yeni Active Directory orman ve etki alanı

Yeni bir Active Directory orman ve etki alanı ayarlamak için şunlar gerekir:

* Yeni Active Directory orman ve etki alanı için etki alanı denetleyicileri olarak kullanmak üzere Microsoft Windows Server çalıştıran bir veya daha fazla sanal makine.
* Ad çözümlemesi için DNS hizmetini çalıştıran bir veya daha fazla sanal makine.

Ayrıntılı adımlar için bkz. [Yeni bir Windows Server 2012 Active Directory ormanı yüklemeyi](https://docs.microsoft.com/windows-server/identity/ad-ds/deploy/install-a-new-windows-server-2012-active-directory-forest--level-200-) .

> [!TIP]
> Hizmetlerin yüksek kullanılabilirliği için, birden çok etki alanı denetleyicisi ve DNS sunucusu ayarlamayı öneririz.

Active Directory ormanı ve etki alanını ayarladıktan sonra, yeni Active Directory için [vCenter 'a bir kimlik kaynağı ekleyebilirsiniz](#add-an-identity-source-on-vcenter) .

### <a name="new-active-directory-domain-in-an-existing-active-directory-forest"></a>Mevcut bir Active Directory ormanında yeni Active Directory etki alanı

Mevcut bir Active Directory ormanında yeni bir Active Directory etki alanı kurmak için şunlar gerekir:

* Active Directory ormanınızın bulunduğu konumdan siteye VPN bağlantısı.
* Mevcut Active Directory ormanınızın adını çözümlemek için DNS sunucusu.

Ayrıntılı adımlar için bkz. [Yeni Windows Server 2012 Active Directory alt veya ağaç etki alanı yüklemesi](https://docs.microsoft.com/windows-server/identity/ad-ds/deploy/install-a-new-windows-server-2012-active-directory-child-or-tree-domain--level-200-) .

Active Directory etki alanını ayarladıktan sonra, yeni Active Directory için [vCenter 'a bir kimlik kaynağı ekleyebilirsiniz](#add-an-identity-source-on-vcenter) .

## <a name="set-up-active-directory-on-azure"></a>Azure 'da Active Directory ayarlama

Azure üzerinde çalışan Active Directory, şirket içinde çalışan Active Directory benzerdir.  VCenter 'da çoklu oturum açma kimlik kaynağı olarak Azure 'da çalışan Active Directory ayarlamak için, vCenter Server ve PSC 'nin Active Directory hizmetlerinin çalıştığı Azure sanal ağı ile ağ bağlantısı olması gerekir.  Azure sanal ağ bağlantısı kullanarak bu bağlantıyı, Active Directory hizmetlerinin CloudSimple özel bulutuna çalıştığı Azure sanal ağından [ExpressRoute kullanarak](azure-expressroute-connection.md) kurabilirsiniz.

Ağ bağlantısı kurulduktan sonra, kimlik kaynağı olarak eklemek için [Şirket içi Active Directory çoklu oturum açma kimlik kaynağı olarak ekleme](#add-on-premises-active-directory-as-a-single-sign-on-identity-source) bölümündeki adımları izleyin.  

## <a name="add-an-identity-source-on-vcenter"></a>VCenter 'a bir kimlik kaynağı ekleme

1. Özel bulutunuzda [ayrıcalıkları ilerletin](escalate-private-cloud-privileges.md) .

2. Özel bulutunuz için vCenter 'da oturum açın.

3. **Home > yönetimi**' ni seçin.

    ![Yönetim](media/OnPremAD01.png)

4. **Yapılandırma > çoklu oturum açma**seçeneğini belirleyin.

    ![Çoklu Oturum Açma](media/OnPremAD02.png)

5. **Kimlik kaynakları** sekmesini açın ve yeni bir kimlik kaynağı eklemek için **+** ' ye tıklayın.

    ![Kimlik kaynakları](media/OnPremAD03.png)

6. **LDAP sunucusu olarak Active Directory** seçin ve **İleri**' ye tıklayın.

    ![Active Directory](media/OnPremAD04.png)

7. Ortamınız için kimlik kaynak parametrelerini belirtin ve **İleri**' ye tıklayın.

    ![Active Directory](media/OnPremAD05.png)

8. Ayarları gözden geçirin ve **son**' a tıklayın.
