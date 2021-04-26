---
title: Azure AD Domain Services 'daki ortak uyarılar ve çözümler | Microsoft Docs
description: Azure Active Directory Domain Services sistem durumunun bir parçası olarak oluşturulan yaygın uyarıların nasıl çözümleneceğini öğrenin
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 54319292-6aa0-4a08-846b-e3c53ecca483
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: 4caf804a274956556d6e9ca396c8f08594b11a87
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101092880"
---
# <a name="known-issues-common-alerts-and-resolutions-in-azure-active-directory-domain-services"></a>Bilinen sorunlar: Azure Active Directory Domain Services Genel uyarılar ve çözümler

Azure Active Directory Domain Services (Azure AD DS) uygulamalar için kimlik ve kimlik doğrulamanın merkezi bir parçası olarak bazı sorunlar oluşur. Sorunlarla karşılaşırsanız, yeniden çalışan işlemleri almanıza yardımcı olmak için bazı yaygın uyarılar ve ilgili sorun giderme adımları vardır. İstediğiniz zaman, ek sorun giderme yardımı için [bir Azure destek isteği de açabilirsiniz][azure-support] .

Bu makalede, Azure AD DS 'daki genel uyarılarla ilgili sorun giderme bilgileri sağlanmaktadır.

## <a name="aadds100-missing-directory"></a>AADDS100: eksik dizin

### <a name="alert-message"></a>Uyarı iletisi

*Yönetilen etki alanınız ile ilişkili Azure AD dizini silinmiş olabilir. Yönetilen etki alanı artık desteklenen bir yapılandırmada değil. Microsoft, yönetilen etki alanınızı izleyemez, yönetemez, düzeltme eki ve eşitleyemiyor.*

### <a name="resolution"></a>Çözüm

Bu hata genellikle, bir Azure aboneliği yeni bir Azure AD dizinine taşındığında ve Azure AD DS ile ilişkili eski Azure AD dizini silindiğinde oluşur.

Bu hata kurtarılamaz. Uyarıyı çözmek için, [mevcut yönetilen etki alanınızı silin](delete-aadds.md) ve yeni dizininizde yeniden oluşturun. Yönetilen etki alanını silme konusunda sorun yaşıyorsanız, ek sorun giderme yardımı için [bir Azure destek isteği açın][azure-support] .

## <a name="aadds101-azure-ad-b2c-is-running-in-this-directory"></a>AADDS101: Azure AD B2C bu dizinde çalışıyor

### <a name="alert-message"></a>Uyarı iletisi

*Azure AD Domain Services bir Azure AD B2C dizininde etkinleştirilemez.*

### <a name="resolution"></a>Çözüm

Azure AD DS, bir Azure AD diziniyle otomatik olarak eşitlenir. Azure AD dizini B2C için yapılandırılmışsa Azure AD DS dağıtılamıyor ve eşitlenemez.

Azure AD DS kullanmak için, aşağıdaki adımları kullanarak yönetilen etki alanınızı Azure AD B2C olmayan bir dizinde yeniden oluşturmanız gerekir:

1. [Yönetilen etki alanını](delete-aadds.md) mevcut Azure AD dizininden silin.
1. Azure AD B2C bir dizin olmayan yeni bir Azure AD dizini oluşturun.
1. [Değiştirme yönetilen etki alanı oluşturun](tutorial-create-instance.md).

Yönetilen etki alanının sistem durumu otomatik olarak iki saat içinde güncelleştirilir ve uyarıyı kaldırır.

## <a name="aadds103-address-is-in-a-public-ip-range"></a>AADDS103: adres genel bir IP aralığında

### <a name="alert-message"></a>Uyarı iletisi

*Azure AD Domain Services etkinleştirdiğiniz sanal ağın IP adresi aralığı ortak bir IP aralığında. Azure AD Domain Services, özel bir IP adresi aralığına sahip bir sanal ağda etkinleştirilmelidir. Bu yapılandırma, Microsoft 'un yönetilen etki alanınızı izleme, yönetme, düzeltme eki uygulama ve eşitlemeye yönelik yeteneğini etkiler.*

### <a name="resolution"></a>Çözüm

Başlamadan önce, [özel IP v4 adres alanlarını](https://en.wikipedia.org/wiki/Private_network#Private_IPv4_address_spaces)anladığınızdan emin olun.

Sanal ağ içinde VM 'Ler, alt ağ için yapılandırılan aynı IP adresi aralığındaki Azure kaynaklarına istek yapabilir. Bir alt ağ için genel bir IP adresi aralığı yapılandırırsanız, bir sanal ağ içinde yönlendirilen istekler amaçlanan Web kaynaklarına ulaşmayabilir. Bu yapılandırma, Azure AD DS ile öngörülemeyen hatalara neden olabilir.

> [!NOTE]
> Sanal ağınızda yapılandırılmış Internet 'teki IP adresi aralığına sahipseniz, bu uyarı yoksayılabilir. Ancak, Azure AD Domain Services öngörülemeyen hatalara yol açacağından, bu yapılandırmayla [SLA](https://azure.microsoft.com/support/legal/sla/active-directory-ds/v1_0/) 'ya kaydedilemez.

Bu uyarıyı çözmek için, mevcut yönetilen etki alanınızı silin ve özel bir IP adresi aralığına sahip bir sanal ağda yeniden oluşturun. Bu işlem, yönetilen etki alanı kullanılamadığından ve OU veya hizmet hesapları gibi oluşturduğunuz özel kaynaklar kaybolduğu için kesintiye uğramış.

1. [Yönetilen etki alanını](delete-aadds.md) dizininizden silin.
1. Sanal ağ IP adresi aralığını güncelleştirmek için Azure portal *sanal ağ* araması yapın ve seçin. Hatalı bir genel IP adresi aralığı kümesine sahip Azure AD DS sanal ağını seçin.
1. **Ayarlar** altında *Adres alanı*' nı seçin.
1. Adres aralığını, mevcut adres aralığını seçip düzenleyerek veya ek bir adres aralığı ekleyerek güncelleştirin. Yeni IP adresi aralığının özel bir IP aralığında olduğundan emin olun. Hazırlanıyor, değişiklikleri **kaydedin** .
1. Sol taraftaki gezinmede **alt ağları** seçin.
1. Düzenlemek istediğiniz alt ağı seçin veya ek bir alt ağ oluşturun.
1. Özel bir IP adresi aralığı güncelleştirin veya belirtin, sonra değişikliklerinizi **kaydedin** .
1. [Değiştirme yönetilen etki alanı oluşturun](tutorial-create-instance.md). Güncelleştirilmiş sanal ağ alt ağını özel bir IP adresi aralığıyla seçtiğinizden emin olun.

Yönetilen etki alanının sistem durumu otomatik olarak iki saat içinde güncelleştirilir ve uyarıyı kaldırır.

## <a name="aadds106-your-azure-subscription-is-not-found"></a>AADDS106: Azure aboneliğiniz bulunamadı

### <a name="alert-message"></a>Uyarı iletisi

*Yönetilen etki alanınız ile ilişkili Azure aboneliğiniz silindi.  Azure AD Domain Services düzgün şekilde çalışmaya devam etmek için etkin bir abonelik gerektirir.*

### <a name="resolution"></a>Çözüm

Azure AD DS, etkin bir abonelik gerektirir ve farklı bir aboneliğe taşınamaz. Yönetilen etki alanının ilişkilendirildiği Azure aboneliği silinirse, bir Azure aboneliği ve yönetilen etki alanı oluşturmanız gerekir.

1. [Bir Azure aboneliği oluşturun](../cost-management-billing/manage/create-subscription.md).
1. [Yönetilen etki alanını](delete-aadds.md) mevcut Azure AD dizininden silin.
1. [Değiştirme yönetilen etki alanı oluşturun](tutorial-create-instance.md).

## <a name="aadds107-your-azure-subscription-is-disabled"></a>AADDS107: Azure aboneliğiniz devre dışı bırakıldı

### <a name="alert-message"></a>Uyarı iletisi

*Yönetilen etki alanınız ile ilişkili Azure aboneliğiniz etkin değil.  Azure AD Domain Services düzgün şekilde çalışmaya devam etmek için etkin bir abonelik gerektirir.*

### <a name="resolution"></a>Çözüm

Azure AD DS, etkin bir abonelik gerektirir. Yönetilen etki alanının ilişkili olduğu Azure aboneliği etkin değilse, aboneliği yeniden etkinleştirmek için yenilemeniz gerekir.

1. [Azure aboneliğinizi yenileyin](../cost-management-billing/manage/subscription-disabled.md).
2. Abonelik yenilendiğinde bir Azure AD DS bildirimi, yönetilen etki alanını yeniden etkinleştirmenizi sağlar.

Yönetilen etki alanı yeniden etkinleştirildiğinde, yönetilen etki alanının sistem durumu otomatik olarak iki saat içinde güncelleştirilir ve uyarıyı kaldırır.

## <a name="aadds108-subscription-moved-directories"></a>AADDS108: abonelik taşınan dizinler

### <a name="alert-message"></a>Uyarı iletisi

*Azure AD Domain Services tarafından kullanılan abonelik başka bir dizine taşındı. Azure AD Domain Services düzgün şekilde çalışması için aynı dizinde etkin bir aboneliğe sahip olması gerekir.*

### <a name="resolution"></a>Çözüm

Azure AD DS, etkin bir abonelik gerektirir ve farklı bir aboneliğe taşınamaz. Yönetilen etki alanının ilişkili olduğu Azure aboneliği taşınırsa, aboneliği önceki dizine taşıyın veya mevcut dizinden [yönetilen etki alanınızı silin](delete-aadds.md) ve [Seçili abonelikte bir değiştirme yönetilen etki alanı oluşturun](tutorial-create-instance.md).

## <a name="aadds109-resources-for-your-managed-domain-cannot-be-found"></a>AADDS109: yönetilen etki alanınız için kaynaklar bulunamıyor

### <a name="alert-message"></a>Uyarı iletisi

*Yönetilen etki alanınız için kullanılan bir kaynak silindi. Azure AD Domain Services düzgün çalışması için bu kaynak gereklidir.*

### <a name="resolution"></a>Çözüm

Azure AD DS, genel IP adresleri, sanal ağ arabirimleri ve yük dengeleyici gibi düzgün çalışması için ek kaynaklar oluşturur. Bu kaynaklardan herhangi biri silinirse, yönetilen etki alanı desteklenmeyen bir durumda olur ve etki alanının yönetilmesini önler. Bu kaynaklarla ilgili daha fazla bilgi için bkz. [Azure AD DS tarafından kullanılan ağ kaynakları](network-considerations.md#network-resources-used-by-azure-ad-ds).

Bu uyarı, gerekli kaynaklardan biri silindiğinde oluşturulur. Kaynak 4 saatten daha önce silinmişse, Azure platformunun silinen kaynağı otomatik olarak yeniden oluşturalım olasılığı vardır. Aşağıdaki adımlarda, kaynak silme işleminin sistem durumu ve zaman damgasının nasıl kontrol yapılacağı ana hatlarıyla verilmiştir:

1. Azure portal **etki alanı Hizmetleri**' ni arayıp seçin. *Aaddscontoso.com* gibi yönetilen etki alanınızı seçin.
1. Sol taraftaki gezinmede **sistem durumu**' nu seçin.
1. Sistem durumu sayfasında KIMLIĞI *AADDS109* olan uyarıyı seçin.
1. Uyarının ilk bulduğu zamana yönelik bir zaman damgası vardır. Bu zaman damgası 4 saatten daha önce, Azure platformu kaynağı otomatik olarak yeniden oluşturabilir ve uyarıyı kendisi çözebilir.

    Uyarı 4 saatten eski olursa, yönetilen etki alanı kurtarılamaz durumda olur. [Yönetilen etki alanını silin](delete-aadds.md) ve ardından [Yeni bir yönetilen etki alanı oluşturun](tutorial-create-instance.md).

## <a name="aadds110-the-subnet-associated-with-your-managed-domain-is-full"></a>AADDS110: yönetilen etki alanınız ile ilişkili alt ağ dolu

### <a name="alert-message"></a>Uyarı iletisi

*Azure AD Domain Services dağıtımı için seçilen alt ağ dolu ve oluşturulması gereken ek etki alanı denetleyicisi için alan yok.*

### <a name="resolution"></a>Çözüm

Azure AD DS için sanal ağ alt ağı, otomatik olarak oluşturulan kaynaklar için yeterli IP adresine sahip olmalıdır. Bu IP adres alanı, bir bakım olayı varsa, yerine konacak kaynakları oluşturma gereksinimini içerir. Kullanılabilir IP adreslerinin tükenme riskini en aza indirmek için, kendi VM 'leriniz gibi ek kaynakları, yönetilen etki alanı ile aynı sanal ağ alt ağına dağıtmayın.

Bu hata kurtarılamaz. Uyarıyı çözmek için, [mevcut yönetilen etki alanınızı silin](delete-aadds.md) ve yeniden oluşturun. Yönetilen etki alanını silme konusunda sorun yaşıyorsanız, ek sorun giderme yardımı için [bir Azure destek isteği açın][azure-support] .

## <a name="aadds111-service-principal-unauthorized"></a>AADDS111: hizmet sorumlusu yetkilendirilmemiş

### <a name="alert-message"></a>Uyarı iletisi

*Azure AD Domain Services, etki alanına hizmet vermek için kullandığı bir hizmet sorumlusu, Azure aboneliğindeki kaynakları yönetme yetkisine sahip değildir. Hizmet sorumlusu, yönetilen etki alanınızı hizmetine hizmet vermek için izinleri kazanmak için gereklidir.*

### <a name="resolution"></a>Çözüm

Yönetilen bir etki alanının kaynaklarını yönetmek ve oluşturmak için otomatik olarak oluşturulan bazı hizmet sorumluları kullanılır. Bu hizmet sorumlularından birine yönelik erişim izinleri değiştirilirse etki alanı kaynakları doğru bir şekilde yönetemez. Aşağıdaki adımlarda, bir hizmet sorumlusu için erişim izinlerini nasıl anlayacağınızı ve daha sonra sağlayacağınızı gösterilmektedir:

1. [Azure rol tabanlı erişim denetimi ve Azure Portal uygulamalara erişim verme](../role-based-access-control/role-assignments-portal.md)hakkında bilgi edinin.
2. KIMLIĞI *abba844e-bc0e-44b0-947a-dc74e5d09022* olan hizmet sorumlusunun sahip olduğu erişimi gözden geçirin ve daha önceki bir tarihte reddedilen erişimi verin.

## <a name="aadds112-not-enough-ip-address-in-the-managed-domain"></a>AADDS112: yönetilen etki alanında yeterli IP adresi yok

### <a name="alert-message"></a>Uyarı iletisi

*Bu etki alanındaki sanal ağın alt ağının yeterli IP adresi bulunmayabilir. Azure AD Domain Services, üzerinde etkin olduğu alt ağ içinde en az iki kullanılabilir IP adresi gerektirir. Alt ağ içinde en az 3-5 yedek IP adresi olmasını öneririz. Bu durum alt ağ içinde başka sanal makineler dağıtılmışsa, bu nedenle kullanılabilir IP adresi sayısını veya alt ağdaki kullanılabilir IP adresleri sayısında bir kısıtlama varsa oluşmuş olabilir.*

### <a name="resolution"></a>Çözüm

Azure AD DS için sanal ağ alt ağı, otomatik olarak oluşturulan kaynaklar için yeterli IP adresine ihtiyaç duyuyor. Bu IP adres alanı, bir bakım olayı varsa, yerine konacak kaynakları oluşturma gereksinimini içerir. Kullanılabilir IP adreslerinin tükenme riskini en aza indirmek için, kendi VM 'leriniz gibi ek kaynakları, yönetilen etki alanı ile aynı sanal ağ alt ağına dağıtmayın.

Bu uyarıyı çözmek için, mevcut yönetilen etki alanınızı silin ve yeterince büyük bir IP adresi aralığına sahip bir sanal ağda yeniden oluşturun. Bu işlem, yönetilen etki alanı kullanılamadığından ve OU veya hizmet hesapları gibi oluşturduğunuz özel kaynaklar kaybolduğu için kesintiye uğramış.

1. [Yönetilen etki alanını](delete-aadds.md) dizininizden silin.
1. Sanal ağ IP adresi aralığını güncelleştirmek için Azure portal *sanal ağ* araması yapın ve seçin. Küçük IP adresi aralığına sahip yönetilen etki alanı için sanal ağı seçin.
1. **Ayarlar** altında *Adres alanı*' nı seçin.
1. Adres aralığını, mevcut adres aralığını seçip düzenleyerek veya ek bir adres aralığı ekleyerek güncelleştirin. Yeni IP adresi aralığının, yönetilen etki alanının alt ağ aralığı için yeterince büyük olduğundan emin olun. Hazırlanıyor, değişiklikleri **kaydedin** .
1. Sol taraftaki gezinmede **alt ağları** seçin.
1. Düzenlemek istediğiniz alt ağı seçin veya ek bir alt ağ oluşturun.
1. Yeterince büyük bir IP adresi aralığı güncelleştirin veya belirtin, sonra değişikliklerinizi **kaydedin** .
1. [Değiştirme yönetilen etki alanı oluşturun](tutorial-create-instance.md). Güncelleştirilmiş sanal ağ alt ağını, yeterince büyük bir IP adresi aralığı ile seçtiğinizden emin olun.

Yönetilen etki alanının sistem durumu otomatik olarak iki saat içinde güncelleştirilir ve uyarıyı kaldırır.

## <a name="aadds113-resources-are-unrecoverable"></a>AADDS113: kaynaklar kurtarılamaz

### <a name="alert-message"></a>Uyarı iletisi

*Azure AD Domain Services tarafından kullanılan kaynaklar beklenmeyen bir durumda algılandı ve kurtarılamıyor.*

### <a name="resolution"></a>Çözüm

Bu hata kurtarılamaz. Uyarıyı çözmek için, [mevcut yönetilen etki alanınızı silin](delete-aadds.md) ve yeniden oluşturun. Yönetilen etki alanını silme konusunda sorun yaşıyorsanız, ek sorun giderme yardımı için [bir Azure destek isteği açın][azure-support] .

## <a name="aadds114-subnet-invalid"></a>AADDS114: alt ağ geçersiz

### <a name="alert-message"></a>Uyarı iletisi

*Azure AD Domain Services dağıtımı için seçilen alt ağ geçersiz ve kullanılamaz.*

### <a name="resolution"></a>Çözüm

Bu hata kurtarılamaz. Uyarıyı çözmek için, [mevcut yönetilen etki alanınızı silin](delete-aadds.md) ve yeniden oluşturun. Yönetilen etki alanını silme konusunda sorun yaşıyorsanız, ek sorun giderme yardımı için [bir Azure destek isteği açın][azure-support] .

## <a name="aadds115-resources-are-locked"></a>AADDS115: kaynaklar kilitli

### <a name="alert-message"></a>Uyarı iletisi

*Hedef kapsam kilitlendiğinden, yönetilen etki alanı tarafından kullanılan bir veya daha fazla ağ kaynağı üzerinde çalışılamıyor.*

### <a name="resolution"></a>Çözüm

Değişiklik veya silme işlemini engellemek için, kaynak kilitleri Azure kaynaklarına uygulanabilir. Azure AD DS yönetilen bir hizmet olduğundan, Azure platformunun yapılandırma değişikliği yapma yeteneği olması gerekir. Azure AD DS bileşenlerinden bazılarına bir kaynak kilidi uygulanırsa, Azure platformu yönetim görevlerini gerçekleştiremez.

Azure AD DS bileşenlerinde kaynak kilitlerini denetlemek ve kaldırmak için aşağıdaki adımları izleyin:

1. Kaynak grubunuzda, sanal ağ, ağ arabirimi veya genel IP adresi gibi yönetilen etki alanının ağ bileşenlerinin her biri için Azure portal işlem günlüklerini denetleyin. Bu işlem günlükleri bir işlemin neden başarısız olduğunu ve bir kaynak kilidinin uygulandığını göstermelidir.
1. Bir kilidin uygulandığı kaynağı seçin ve ardından **kilitler altında kilitleri** seçin ve kilidi kaldırın.

## <a name="aadds116-resources-are-unusable"></a>AADDS116: kaynaklar kullanılamaz

### <a name="alert-message"></a>Uyarı iletisi

*Yönetilen etki alanı tarafından kullanılan bir veya daha fazla ağ kaynağı, ilke kısıtlaması nedeniyle üzerinde çalıştırılamaz.*

### <a name="resolution"></a>Çözüm

İlkeler, hangi yapılandırma eylemlerine izin verileceğini denetleyen Azure kaynaklarına ve kaynak gruplarına uygulanır. Azure AD DS yönetilen bir hizmet olduğundan, Azure platformunun yapılandırma değişikliği yapma yeteneği olması gerekir. Azure AD DS bileşenlerinden bazılarına bir ilke uygulanırsa, Azure platformu yönetim görevlerini gerçekleştiremeyebilir.

Uygulanan ilkeleri Azure AD DS bileşenlerinde denetlemek ve güncelleştirmek için aşağıdaki adımları izleyin:

1. Kaynak grubunuzda, sanal ağ, NIC veya genel IP adresi gibi, yönetilen etki alanının ağ bileşenlerinin her biri için Azure portal işlem günlüklerini denetleyin. Bu işlem günlükleri bir işlemin neden başarısız olduğunu ve kısıtlayıcı bir ilkenin uygulandığını göstermelidir.
1. Bir ilkenin uygulandığı kaynağı seçin ve ardından **ilkeler** altında ilkeyi daha az kısıtlayıcı olacak şekilde seçin ve düzenleyin.

## <a name="aadds500-synchronization-has-not-completed-in-a-while"></a>AADDS500: eşitleme bir süre içinde tamamlanmadı

### <a name="alert-message"></a>Uyarı iletisi

*Yönetilen etki alanı, [Date] tarihinde Azure AD ile en son eşitlendi. Kullanıcılar yönetilen etki alanında oturum açamıyor olabilir veya grup üyelikleri Azure AD ile eşitlenmemiş olabilir.*

### <a name="resolution"></a>Çözüm

Yönetilen etki alanının yapılandırmasındaki sorunları belirten tüm uyarılar için [Azure AD DS sistem durumunu denetleyin](check-health.md) . Ağ yapılandırması ile ilgili sorunlar Azure AD ile eşitlemeyi engelleyebilir. Bir yapılandırma sorununu belirten uyarıları çözebiliyorsanız, iki saat bekleyip eşitlemenin başarıyla tamamlanıp tamamlanmadığını görmek için yeniden denetleyin.

Aşağıdaki yaygın nedenler, yönetilen bir etki alanında eşitlemenin durdurulmasına neden olur:

* Gerekli ağ bağlantısı engellendi. Azure sanal ağını sorunlar ve gerekli olanları denetleme hakkında daha fazla bilgi edinmek için bkz. [ağ güvenlik grupları](alert-nsg.md) ve [Azure AD DS için ağ gereksinimleri](network-considerations.md)sorunlarını giderme.
*  Yönetilen etki alanı dağıtıldığında parola eşitleme ayarlanmadı veya başarıyla tamamlanmadı. Şirket içinde [yalnızca bulutta bulunan kullanıcılar](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) veya [karma kullanıcılar](tutorial-configure-password-hash-sync.md)için parola eşitlemeyi ayarlayabilirsiniz.

## <a name="aadds501-a-backup-has-not-been-taken-in-a-while"></a>AADDS501: bir yedekleme sırasında bir yedekleme yapılmadı

### <a name="alert-message"></a>Uyarı iletisi

*Yönetilen etki alanı, [Date] tarihinde en son yedeklendi.*

### <a name="resolution"></a>Çözüm

Yönetilen etki alanının yapılandırmasındaki sorunları belirten uyarılar için [Azure AD DS sistem durumunu kontrol edin](check-health.md) . Ağ yapılandırmasındaki sorunlar, Azure platformunun yedeklemeleri başarıyla ele almayı engelleyebilir. Bir yapılandırma sorununu belirten uyarıları çözebiliyorsanız, iki saat bekleyip eşitlemenin başarıyla tamamlanıp tamamlanmadığını görmek için yeniden denetleyin.

## <a name="aadds503-suspension-due-to-disabled-subscription"></a>AADDS503: devre dışı bırakılmış abonelik nedeniyle askıya alma

### <a name="alert-message"></a>Uyarı iletisi

*Etki alanıyla ilişkili Azure aboneliği etkin olmadığından, yönetilen etki alanı askıya alındı.*

### <a name="resolution"></a>Çözüm

> [!WARNING]
> Yönetilen bir etki alanı uzun bir süre askıya alınırsa, silinmekte olan bir tehlike vardır. Askıya alma nedenini mümkün olduğunca hızlı bir şekilde çözün. Daha fazla bilgi için bkz. [Azure AD DS için askıya alınmış durumları anlama](suspension.md).

Azure AD DS, etkin bir abonelik gerektirir. Yönetilen etki alanının ilişkili olduğu Azure aboneliği etkin değilse, aboneliği yeniden etkinleştirmek için yenilemeniz gerekir.

1. [Azure aboneliğinizi yenileyin](../cost-management-billing/manage/subscription-disabled.md).
2. Abonelik yenilendiğinde bir Azure AD DS bildirimi, yönetilen etki alanını yeniden etkinleştirmenizi sağlar.

Yönetilen etki alanı yeniden etkinleştirildiğinde, yönetilen etki alanının sistem durumu otomatik olarak iki saat içinde güncelleştirilir ve uyarıyı kaldırır.

## <a name="aadds504-suspension-due-to-an-invalid-configuration"></a>AADDS504: geçersiz bir yapılandırma nedeniyle askıya alma

### <a name="alert-message"></a>Uyarı iletisi

*Yönetilen etki alanı geçersiz bir yapılandırma nedeniyle askıya alındı. Hizmet, yönetilen etki alanı için etki alanı denetleyicilerini uzun bir süre yönetmedi, düzeltme eki veya güncelleştirme yapamadı.*

### <a name="resolution"></a>Çözüm

> [!WARNING]
> Yönetilen bir etki alanı uzun bir süre askıya alınırsa, silinmekte olan bir tehlike vardır. Askıya alma nedenini mümkün olduğunca hızlı bir şekilde çözün. Daha fazla bilgi için bkz. [Azure AD DS için askıya alınmış durumları anlama](suspension.md).

Yönetilen etki alanının yapılandırmasındaki sorunları belirten uyarılar için [Azure AD DS sistem durumunu kontrol edin](check-health.md) . Bir yapılandırma sorununu belirten uyarıları çözebiliyorsanız, iki saat bekleyip eşitlemenin tamamlanıp tamamlanmadığını görmek için yeniden denetleyin. Hazırlandığınızda, yönetilen etki alanını yeniden etkinleştirmek için [bir Azure destek isteği açın][azure-support] .

## <a name="next-steps"></a>Sonraki adımlar

Hala sorun yaşıyorsanız, ek sorun giderme yardımı için [bir Azure destek isteği açın][azure-support] .

<!-- INTERNAL LINKS -->
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md