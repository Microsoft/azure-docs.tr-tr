---
title: Azure geçişi sunucu değerlendirmesi ile fiziksel sunucuları değerlendirmeye hazırlama
description: Azure geçişi sunucu değerlendirmesi kullanılarak fiziksel sunucuların Azure 'a değerlendirmesi ve geçirilmesi için nasıl hazırlanılacağı açıklanmaktadır.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 11/07/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 075c60aed458be8a4bf45fafb2d06757862ff5ba
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158591"
---
# <a name="prepare-for-assessment-and-migration-of-physical-servers-to-azure"></a>Fiziksel sunucuların değerlendirmesi ve Azure 'a geçirilmesi için hazırlanma

Bu makalede, [Azure geçişi](migrate-services-overview.md)ile şirket içi fiziksel sunucu değerlendirmesi için nasıl hazırlanılacağı açıklanır.

[Azure geçişi](migrate-overview.md) , Microsoft Azure için uygulamaları, altyapıyı ve iş yüklerini keşfetmenize, değerlendirmenize ve geçirmenize yardımcı olan araçların merkezini sağlar. Hub, Azure geçiş araçları ve üçüncü taraf bağımsız yazılım satıcısı (ISV) tekliflerini içerir. 

Bu öğretici, Azure geçişi ile fiziksel sunucuları nasıl değerlendirmenizi gösteren bir serinin ilk ilkidir. Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Azure 'u hazırlayın. Azure hesabınız ve kaynaklarınızın Azure geçişi ile çalışması için izinleri ayarlayın.
> * Sunucu değerlendirmesi için şirket içi fiziksel sunucuları hazırlayın.


> [!NOTE]
> Öğreticiler, bir senaryo için en basit dağıtım yolunu gösterir, böylece bir kavram kanıtı hızlı bir şekilde ayarlayabilmenizi sağlayabilirsiniz. Öğreticiler mümkün olduğunca varsayılan seçenekleri kullanır ve tüm olası ayarları ve yolları göstermez. Ayrıntılı yönergeler için, fiziksel sunucu değerlendirmesi için nasıl yapılır konusunu gözden geçirin.


Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/pricing/free-trial/) oluşturun.


## <a name="prepare-azure"></a>Azure’u hazırlama

### <a name="azure-permissions"></a>Azure izinleri

Azure geçişi dağıtımı için izinleri ayarlamanız gerekir.

- Azure geçişi projesi oluşturmak için Azure hesabınız için izinler.
- Azure geçişi gerecini kaydetmek için hesabınızın izinleri. Gereç, Hyper-V bulma ve geçiş için kullanılır. Gereç kaydı sırasında Azure geçişi, gereci benzersiz bir şekilde tanımlayan iki Azure Active Directory (Azure AD) uygulaması oluşturur:
    - İlk uygulama Azure geçişi hizmet uç noktaları ile iletişim kurar.
    - İkinci uygulama, Azure AD uygulama bilgileri ve gereç yapılandırma ayarlarını depolamak için kayıt sırasında oluşturulan Azure Key Vault erişir.



### <a name="assign-permissions-to-create-project"></a>Proje oluşturmak için izin atama

Azure geçişi projesi oluşturma izniniz olup olmadığını denetleyin.

1. Azure portal aboneliğini açın ve **erişim denetimi (IAM)** seçeneğini belirleyin.
2. **Erişimi denetle**' de ilgili hesabı bulun ve izinleri görüntülemek için tıklatın.
3. **Katkıda bulunan** veya **sahip** izinlerinizin olması gerekir.
    - Henüz ücretsiz bir Azure hesabı oluşturduysanız, aboneliğinizin sahibi olursunuz.
    - Abonelik sahibi değilseniz, rolü atamak için sahip ile çalışın.


### <a name="assign-permissions-to-register-the-appliance"></a>Gereci kaydetmek için izin atama

Aşağıdaki yöntemlerden birini kullanarak, Gereç kaydı sırasında oluşturma sırasında Azure AD uygulamalarını oluşturmak için Azure geçişi izinleri atayabilirsiniz:

- Kiracı/Genel yönetici, Kiracıdaki kullanıcılara Azure AD uygulamaları oluşturmak ve kaydolmak için izin verebilir.
- Kiracı/Genel yönetici, uygulama geliştirici rolünü (izinleri olan) hesaba atayabilir.

Buna dikkat edin:

- Uygulamalar, yukarıda açıklananlar dışında başka bir abonelik üzerinde başka erişim izinlerine sahip değildir.
- Yalnızca yeni bir gereç kaydettiğinizde bu izinlere ihtiyacınız vardır. Gereç kurulduktan sonra izinleri kaldırabilirsiniz.


#### <a name="grant-account-permissions"></a>Hesap izinleri verme

Kiracı/Genel yönetici, izinleri aşağıdaki gibi verebilir:

1. Azure AD 'de, kiracı/genel yönetici **Azure Active Directory** > **Kullanıcılar** > **Kullanıcı ayarları**' na gitmelidir.
2. Yönetici **uygulama kayıtları** **Evet**olarak ayarlanmalıdır.

    ![Azure AD izinleri](./media/tutorial-prepare-hyper-v/aad.png)

> [!NOTE]
> Bu, hassas olmayan bir varsayılan ayardır. [Daha fazla bilgi edinin](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).

#### <a name="assign-application-developer-role"></a>Uygulama geliştirici rolü atama

Kiracı/Genel yönetici, uygulama geliştirici rolünü bir hesaba atayabilir. [Daha fazla bilgi edinin](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal).


## <a name="prepare-for-physical-server-assessment"></a>Fiziksel sunucu değerlendirmesi için hazırlanma

Fiziksel sunucu değerlendirmesi için hazırlanmak üzere, fiziksel sunucu ayarlarını doğrulamanız ve gereç dağıtımı için ayarları doğrulamanız gerekir:

### <a name="verify-physical-server-settings"></a>Fiziksel sunucu ayarlarını doğrulama

1. Sunucu değerlendirmesi için [fiziksel sunucu gereksinimlerini](migrate-support-matrix-physical.md#assessment-physical-server-requirements) doğrulayın.
2. Fiziksel sunucularda [gerekli bağlantı noktalarının](migrate-support-matrix-physical.md#assessment-port-requirements) açık olduğundan emin olun.


### <a name="verify-appliance-settings"></a>Gereç ayarlarını doğrulama

Sonraki öğreticide Azure geçişi gerecini ve değerlendirmeyi ayarlamadan önce, Gereç dağıtımına hazırlanın.

1. Gereç gereksinimlerini [doğrulayın](migrate-support-matrix-physical.md#assessment-appliance-requirements) .
2. Gerecin erişmesi gereken Azure URL 'Lerini [gözden geçirin](migrate-support-matrix-physical.md#assessment-appliance-url-access) .
3. Bulma ve değerlendirme sırasında gereç tarafından toplanacak verileri gözden geçirin.
4. Gereç [için bağlantı noktası](migrate-support-matrix-physical.md#assessment-port-requirements) erişim gereksinimleri.


### <a name="set-up-an-account-for-physical-server-discovery"></a>Fiziksel sunucu keşfi için bir hesap ayarlayın

Azure geçişi 'nin şirket içi sunucuları bulması için izinleri olması gerekir.

- **Windows:** Bulmaya dahil etmek istediğiniz tüm Windows sunucularında yerel bir kullanıcı hesabı ayarlayın. Kullanıcı hesabının aşağıdaki gruplara eklenmesi gerekir:-uzaktan yönetim kullanıcıları-performans Izleyicisi kullanıcıları-performans günlüğü kullanıcıları
- **Linux:** Bulunmasını istediğiniz Linux sunucularında bir kök hesabınız olması gerekir.


## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide şunları yaptınız:

> [!div class="checklist"]
> * Azure hesabı izinlerini ayarlayın.
> * Değerlendirme için fiziksel sunucular hazırlandı.

Azure geçişi projesi oluşturmak ve Azure 'a geçiş için fiziksel sunucuları değerlendirmek üzere bir sonraki öğreticiye devam edin

> [!div class="nextstepaction"]
> [Fiziksel sunucuları değerlendir](./tutorial-assess-physical.md)
