---
title: Azure geçişi ile Azure 'a değerlendirme ve geçiş için VMware VM 'lerini hazırlayın | Microsoft Docs
description: Azure geçişi kullanılarak şirket içi VMware VM 'lerinin Azure 'a değerlendirmesi ve geçirilmesi için nasıl hazırlanılacağı açıklanmaktadır.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 10/23/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 4cc04e9ab0acdc9d0cdff77ed1de7bea1c1362d4
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73498475"
---
# <a name="prepare-vmware-vms-for-assessment-and-migration-to-azure"></a>VMware VM 'lerini değerlendirme ve Azure 'a geçiş için hazırlama

Bu makale, [Azure geçişi](migrate-services-overview.md)'ni kullanarak şirket Içi VMware VM 'lerinden Azure 'a değerlendirme ve/veya geçiş için hazırlık yapmanıza yardımcı olur.

[Azure geçişi](migrate-overview.md) , Microsoft Azure için uygulamaları, altyapıyı ve iş yüklerini keşfetmenize, değerlendirmenize ve geçirmenize yardımcı olan araçların merkezini sağlar. Hub, Azure geçiş araçları ve üçüncü taraf bağımsız yazılım satıcısı (ISV) tekliflerini içerir. 


Bu öğretici, VMware VM 'lerini nasıl değerlendirmekte ve geçirebileceğiniz gösteren bir serinin ilk ilkidir. Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Azure 'u Azure geçişi ile çalışacak şekilde hazırlayın.
> * VMware 'yi VM değerlendirmesi için hazırlayın.
> * VM geçişi için VMware 'yi hazırlayın.

> [!NOTE]
> Öğreticiler, bir senaryo için en basit dağıtım yolunu gösterir. Dağıtım ve hızlı bir kavram kanıtı olarak nasıl ayarlanacağını öğrenirsiniz. Öğreticiler mümkün olduğunca varsayılan seçenekleri kullanır ve tüm olası ayarları ve yolları göstermez. Ayrıntılı yönergeler için, VMware değerlendirmesi ve geçiş için nasıl yapılır konusunu gözden geçirin.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/pricing/free-trial/) oluşturun.


## <a name="prepare-azure"></a>Azure’u hazırlama

Bu izinlere ihtiyacınız vardır.

**Görev** | **İzinler** 
--- | --- | ---
**Azure geçişi projesi oluşturma** | Azure hesabınızın bir proje oluşturmak için izinleri olması gerekir. 
**Azure geçişi gereci kaydetme** | Azure geçişi, Azure geçişi sunucu değerlendirmesi ile VMware VM 'lerini değerlendirmek ve Azure geçişi sunucu geçişi ile VMware VM 'lerinin [aracısız geçişini](server-migrate-overview.md) çalıştırmak için basit bir Azure geçiş gereci kullanır. Bu gereç VM 'Leri bulur ve Azure geçişi 'ne VM meta verilerini ve performans verilerini gönderir.<br/><br/>Kayıt sırasında Azure geçişi, gereci benzersiz bir şekilde tanımlayan ve bu uygulamaları oluşturmak için gereken izinlere sahip iki Azure Active Directory (Azure AD) uygulaması oluşturur.<br/> -İlk uygulama Azure geçişi hizmet uç noktaları ile iletişim kurar.<br/> -İkinci uygulama, Azure AD uygulama bilgileri ve gereç yapılandırma ayarlarını depolamak için kayıt sırasında oluşturulan bir Azure Key Vault erişir.
**Key Vault oluşturma** | Azure geçişi, VMware VM 'lerini Azure geçişi sunucu geçişi ile geçirmek için, erişim anahtarlarını aboneliğinizdeki çoğaltma depolama hesabına yönetmek üzere bir Key Vault oluşturur. Kasayı oluşturmak için Azure geçişi projesinin bulunduğu kaynak grubunda rol atama izinlerine sahip olmanız gerekir.


### <a name="assign-permissions-to-create-project"></a>Proje oluşturmak için izin atama

1. Azure portal aboneliğini açın ve **erişim denetimi (IAM)** seçeneğini belirleyin.
2. **Erişimi denetle**' de ilgili hesabı bulun ve izinleri görüntülemek için tıklatın.
3. **Katkıda bulunan** veya **sahip** izinlerinizin olması gerekir.
    - Henüz ücretsiz bir Azure hesabı oluşturduysanız, aboneliğinizin sahibi olursunuz.
    - Abonelik sahibi değilseniz, rolü atamak için sahip ile çalışın.

### <a name="assign-permissions-to-register-the-appliance"></a>Gereci kaydetmek için izin atama

Gereci kaydettirmek için, Azure geçişi 'nin gereç kaydı sırasında Azure AD uygulamalarını oluşturmak üzere izinleri atarsınız. İzinler aşağıdaki yöntemlerden biri kullanılarak atanabilir:

- Kiracı/Genel yönetici, Kiracıdaki kullanıcılara Azure AD uygulamaları oluşturmak ve kaydolmak için izin verebilir.
- Kiracı/Genel yönetici, uygulama geliştirici rolünü (izinleri olan) hesaba atayabilir.

> [!NOTE]
> - Uygulamalar, yukarıda açıklananlar dışında başka bir abonelik üzerinde başka erişim izinlerine sahip değildir.
> - Yalnızca yeni bir gereç kaydettiğinizde bu izinlere ihtiyacınız vardır. Gereç kurulduktan sonra izinleri kaldırabilirsiniz. 


#### <a name="grant-account-permissions"></a>Hesap izinleri verme

Kiracı/Genel yönetici, izinleri aşağıdaki gibi verebilir

1. Azure AD 'de, kiracı/genel yönetici **Azure Active Directory** > **Kullanıcılar** > **Kullanıcı ayarları**' na gitmelidir.
2. Yönetici **uygulama kayıtları** **Evet**olarak ayarlanmalıdır. Bu, hassas olmayan bir varsayılan ayardır. [Daha fazla bilgi edinin](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).

    ![Azure AD izinleri](./media/tutorial-prepare-vmware/aad.png)



#### <a name="assign-application-developer-role"></a>Uygulama geliştirici rolü atama 

Kiracı/Genel yönetici, uygulama geliştirici rolünü bir hesaba atayabilir. [Daha fazla bilgi edinin](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal).

### <a name="assign-role-assignment-permissions"></a>Rol atama izinleri atama

Key Vault oluşturmak için Azure geçişi 'ni etkinleştirmek üzere rol atama izinlerini aşağıdaki gibi atayın:

1. Azure portal içindeki kaynak grubunda, **erişim denetimi (IAM)** seçeneğini belirleyin.
2. **Erişimi denetle**' de ilgili hesabı bulun ve izinleri görüntülemek için tıklatın.

    - Sunucu değerlendirmesini çalıştırmak için, **katkıda bulunan** izinleri yeterlidir.
    - Aracısız sunucu geçişini çalıştırmak için **sahip** (veya **katkıda bulunan** ve **Kullanıcı erişimi Yöneticisi**) izinlerinizin olması gerekir.

3. Gerekli izinleriniz yoksa, bunları kaynak grubu sahibinden isteyin. 



## <a name="prepare-for-vmware-vm-assessment"></a>VMware VM değerlendirmesi için hazırlanma

VMware VM değerlendirmesi için hazırlanmak üzere şunları yapmanız gerekir:

- **VMware ayarlarını doğrulayın**. Geçirmek istediğiniz vCenter Server ve VM 'Lerin gereksinimleri karşıladığından emin olun.
- **Bir değerlendirme hesabı ayarlayın**. Azure geçişi 'nin değerlendirmede VM 'Leri bulması için vCenter Server erişmesi gerekir. Azure geçişi erişimi için salt okuma hesabına sahip olmanız gerekir.
- **Gereç gereksinimlerini doğrulayın**. Değerlendirme için kullanılan Azure geçiş gereci için dağıtım gereksinimlerini doğrulayın.

### <a name="verify-vmware-settings"></a>VMware ayarlarını doğrulama

1. [Denetle](migrate-support-matrix-vmware.md#assessment-vcenter-server-requirements) Değerlendirme için VMware sunucusu gereksinimleri.
2. İhtiyacınız olan bağlantı noktalarının vCenter Server açık [olduğundan emin olun](migrate-support-matrix-vmware.md#assessment-port-requirements) .


### <a name="set-up-an-account-for-assessment"></a>Değerlendirme için bir hesap ayarlayın

Azure geçişi 'nin değerlendirme ve aracısız geçiş için VM 'Leri bulması için vCenter Server erişmesi gerekir. Yalnızca değerlendirme için, vCenter Server için salt okunurdur bir hesap ayarlayın.

### <a name="verify-appliance-settings-for-assessment"></a>Değerlendirme için gereç ayarlarını doğrulama

Gereci dağıtmadan önce gereç gereksinimlerini denetleyin.

1. Gereç gereksinimlerini ve sınırlamalarını [doğrulayın](migrate-support-matrix-vmware.md#assessment-appliance-requirements) .
2. URL tabanlı bir güvenlik duvarı proxy 'SI kullanıyorsanız, gerecin erişmesi gereken Azure URL 'Lerini [gözden geçirin](migrate-support-matrix-vmware.md#assessment-url-access-requirements) . Proxy 'nin, URL 'Leri ararken alınan CNAME kayıtlarını çözümlediği emin olun.
3. Bulma ve değerlendirme sırasında gerecin topladığı [performans verilerini](migrate-appliance.md#collected-performance-data-vmware)ve [meta verileri](migrate-appliance.md#collected-metadata-vmware) gözden geçirin.
4. Gereç tarafından erişilen bağlantı noktalarını [aklınızda edin](migrate-support-matrix-vmware.md#assessment-port-requirements) .
5. VCenter Server, hesabınızın bir OVA dosyası kullanarak VM oluşturma izinlerine sahip olduğundan emin olun. Azure geçişi gereci bir OVA dosyası kullanarak bir VMware VM 'si olarak dağıtırsınız. 

URL tabanlı bir güvenlik duvarı. Proxy kullanıyorsanız, gerekli [Azure URL 'lerine](migrate-support-matrix-vmware.md#assessment-url-access-requirements)erişime izin verin.




## <a name="prepare-for-agentless-vmware-migration"></a>Aracısız VMware geçişine hazırlanma

VMware VM 'lerinin aracısız geçişi için gereksinimleri gözden geçirin.

1. [İnceleme](migrate-support-matrix-vmware.md#agentless-migration-vmware-server-requirements) VMware sunucusu gereksinimleri.
2. Azure geçişi 'nin Azure geçişi sunucu geçişini kullanarak aracısız geçiş için vCenter Server erişebilmesi için [gerekli izinlere](migrate-support-matrix-vmware.md#agentless-migration-vcenter-server-permissions)sahip bir hesap ayarlayın.
3. Aracısız geçiş kullanarak Azure 'a geçirmek istediğiniz VMware VM 'lerinin gereksinimlerini [gözden geçirin](migrate-support-matrix-vmware.md#agentless-migration-vmware-vm-requirements) .
4. Aracısız geçiş için Azure geçişi gereci kullanma gereksinimlerini [gözden geçirin](migrate-support-matrix-vmware.md#agentless-migration-appliance-requirements) .
5. Azure geçişi gerecinin aracısız geçiş için ihtiyaç duyacağı [URL erişimi](migrate-support-matrix-vmware.md#agentless-migration-url-access-requirements) ve [bağlantı noktası erişimini](migrate-support-matrix-vmware.md#agentless-migration-port-requirements) unutmayın.


## <a name="prepare-for-agent-based-vmware-migration"></a>Aracı tabanlı VMware geçişine hazırlanma

VMware VM 'lerinin [aracı tabanlı geçişi](server-migrate-overview.md) için gereksinimleri gözden geçirin.

1. [İnceleme](migrate-support-matrix-vmware.md#agent-based-migration-vmware-server-requirements) VMware sunucusu gereksinimleri. 
2. [Gerekli izinlere](migrate-support-matrix-vmware.md#agent-based-migration-vcenter-server-permissions)sahip bir hesap ayarlayın. Azure geçişi, Azure geçişi sunucu geçişini kullanarak aracı tabanlı geçiş için vCenter Server erişebilir.
3. Geçirmek istediğiniz her sanal makineye Mobility hizmeti yüklemesi de dahil olmak üzere, Azure 'a geçiş yapmak istediğiniz VMware VM 'lerinin gereksinimlerini [gözden geçirin](migrate-support-matrix-vmware.md#agent-based-migration-vmware-vm-requirements) .
4. [URL erişimi](migrate-support-matrix-vmware.md#agent-based-migration-url-access-requirements).
5. Azure geçişi bileşenlerinin aracı tabanlı erişim için ihtiyacı olan [bağlantı noktası erişimini](migrate-support-matrix-vmware.md#agent-based-migration-port-requirements) gözden geçirin.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide şunları yaptınız:
 
> [!div class="checklist"] 
> * Azure izinlerini ayarlayın.
> * Değerlendirme ve geçiş için VMware hazırlandı.


Azure geçişi projesi ayarlamak ve Azure 'a geçiş için VMware VM 'lerini değerlendirmek için ikinci öğreticiye devam edin.

> [!div class="nextstepaction"] 
> [VMware VM 'lerini değerlendir](./tutorial-assess-vmware.md) 

