---
title: Windows sanal masaüstünden el ile geçiş (klasik)-Azure
description: Windows sanal masaüstünden (klasik) Windows sanal masaüstüne el ile geçiş yapma.
author: Heidilohr
ms.topic: how-to
ms.date: 09/11/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 350b7306b6d39b42fbfebcc6566762df305b11b4
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106445989"
---
# <a name="migrate-manually-from-windows-virtual-desktop-classic"></a>Windows sanal masaüstünden el ile geçiş (klasik)

Windows sanal masaüstü (klasik), hizmet ortamını PowerShell cmdlet 'leri, REST API 'Ler ve hizmet nesneleriyle oluşturur. Windows sanal masaüstü hizmeti ortamındaki bir "nesne", Windows sanal masaüstü 'Nün oluşturduğu bir şeydir. Hizmet nesneleri kiracılar, konak havuzları, uygulama grupları ve oturum Konakları içerir.

Ancak, Windows sanal masaüstü (klasik) Azure ile tümleştirilmiştir. Azure tümleştirmesi olmadan oluşturduğunuz tüm nesneler, Azure aboneliğinize bağlı olmadıklarından Azure portal tarafından otomatik olarak yönetilmez.

Windows sanal masaüstü 'nün son önemli güncelleştirmesi, hizmette tam Azure tümleştirilmesine doğru bir kaydırma işareti işaretler. Windows sanal masaüstü 'nde oluşturduğunuz nesneler, Azure portal tarafından otomatik olarak yönetilir.

Bu makalede, Windows sanal masaüstü 'nün en son sürümüne geçiş yapmayı düşünmeniz gerektiğini açıklayacağız. Bundan sonra Windows sanal masaüstünden (klasik) Windows sanal masaüstü 'nün en son güncelleştirmesine nasıl el ile geçiş yapılacağını söylüreceğiz.

## <a name="why-migrate"></a>Neden geçirilir?

Büyük güncelleştirmeler, özellikle de el ile yapmanız kolay olabilir. Ancak, otomatik olarak geçiremeyeceğiniz bazı nedenler vardır:

- Klasik sürümle yapılan mevcut hizmet nesnelerinde Azure 'da hiçbir temsili yok. Kapsamları Windows sanal masaüstü hizmetinin ötesine genişlemez.
- En son güncelleştirme ile hizmetin uygulama KIMLIĞI, uygulamalar için Windows sanal masaüstü (klasik) gibi bir izin onayını kaldırmak üzere değiştirilmiştir. Yeni uygulama KIMLIĞIYLE kimlik doğrulamasından geçirilmedikleri takdirde, Windows sanal masaüstü ile yeni Azure nesneleri oluşturamazsınız.

Zahmetsiz, klasik sürümden uzaklaşmaya hala önem taşımaktadır. Geçiş yaptıktan sonra şunları yapabilirsiniz:

- Windows sanal masaüstünü Azure portal aracılığıyla yönetin.
- Uygulama gruplarına Azure Active Directory (AD) Kullanıcı grupları atayın.
- Dağıtımınızın sorunlarını gidermek için geliştirilmiş Log Analytics özelliğini kullanın.
- Yönetim erişimini yönetmek için Azure yerel rol tabanlı erişim denetimi (Azure RBAC) kullanın.

## <a name="when-should-i-migrate"></a>Ne zaman geçiş yapmam gerekir?

Geçiş yapmanız gerekiyorsa kendinize sormanız durumunda dağıtımınızın güncel ve gelecekteki durumuna da sahip olmanız gerekir.

Özellikle de el ile geçiş yapmanızı önerdiğimiz birkaç senaryo vardır:

- Az sayıda kullanıcıyla bir test ana bilgisayar havuzu kurulumuna sahipsiniz.
- Az sayıda kullanıcı içeren bir üretim ana bilgisayar havuzu kurulumuna sahipsiniz, ancak sonunda yüzlerce kullanıcıya kadar bir işlem yapın.
- Kolayca çoğaltılabilen basit bir kuruluma sahipsiniz. Örneğin, VM 'leriniz bir galeri görüntüsü kullanıyorsa.

> [!IMPORTANT]
> Çok sayıda kullanıcının kararlı hale getirimek için uzun süre geçen gelişmiş bir yapılandırma kullanıyorsanız, el ile geçiş yapmanız önerilmez.

## <a name="prepare-for-migration"></a>Geçiş için hazırlanma

Başlamadan önce ortamınızın geçişe hazırlandığına emin olmanız gerekir.

Geçiş işlemini başlatmak için gerekenler aşağıda verilmiştir:

- Yeni Azure hizmeti nesneleri oluşturacağınız bir Azure aboneliği.
- Aşağıdaki rollere atandığınızdan emin olun:
    
    - Katılımcı
    - Kullanıcı Erişimi Yöneticisi
    
    Katılımcı rolü aboneliğinizde Azure nesneleri oluşturmanıza olanak sağlar ve Kullanıcı erişimi Yöneticisi rolü, uygulama gruplarına kullanıcı atamanıza olanak tanır.

## <a name="how-to-migrate-manually"></a>El ile geçiş

Geçiş işlemi için hazırlandığınıza göre, gerçekten geçiş yapmaya zaman atalım.

Windows sanal masaüstünden (klasik) Windows sanal masaüstüne el ile geçiş yapmak için:

1. Tüm üst düzey nesneleri Azure portal oluşturmak için [Azure Portal ile bir konak havuzu oluşturma](create-host-pools-azure-marketplace.md) bölümündeki yönergeleri izleyin.
2. Zaten kullanmakta olduğunuz sanal makinelerin üzerine getirmek isterseniz, 1. adımda oluşturduğunuz yeni ana bilgisayar havuzuna el ile kaydetmek için [sanal makineleri Windows sanal masaüstü ana bilgisayar havuzuna kaydetme](create-host-pools-powershell.md#register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool) bölümündeki yönergeleri izleyin.
3. Yeni RemoteApp uygulama grupları oluşturun.
4. Kullanıcıları veya Kullanıcı gruplarını yeni masaüstü ve RemoteApp uygulama gruplarında yayımlayın.
5. [Multi-Factor Authentication 'ı ayarlama](set-up-mfa.md)bölümündeki yönergeleri Izleyerek, koşullu erişim ilkenizi yeni nesnelere izin verecek şekilde güncelleştirin.

Kapalı kalma süresini engellemek için, önce mevcut oturum konaklarınızı tek seferde küçük gruplardaki Azure Resource Manager tümleşik konak havuzlarına kaydetmeniz gerekir. Bundan sonra, kullanıcılarınızı daha sonra yeni Azure Resource Manager tümleşik uygulama gruplarına taşıyın.

## <a name="next-steps"></a>Sonraki adımlar

Geçiş yaptıktan sonra, [öğreticilerimizi](create-host-pools-azure-marketplace.md)Inceleyerek Windows sanal masaüstü 'nün nasıl çalıştığını öğrenin. [Mevcut bir konak havuzunu genişlettikten](expand-existing-host-pool.md) ve [RDP özelliklerini özelleştirerek](customize-rdp-properties.md)gelişmiş yönetim özellikleri hakkında bilgi edinin.

Hizmet nesneleri hakkında daha fazla bilgi edinmek için [Windows sanal masaüstü ortamına](environment-setup.md)göz atın.
