---
title: Klasik modelden Azure Resource Manager’a geçişle ilgili sık sorulan sorular
description: Klasik 'dan Azure Resource Manager 'ye geçiş hakkında sık sorulan sorular.
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.subservice: classic-to-arm-migration
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: 034f09a8b616450e472dcc11427191f187cf489f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102615168"
---
# <a name="frequently-asked-questions-about-classic-to-azure-resource-manager-migration"></a>Klasik modelden Azure Resource Manager’a geçişle ilgili sık sorulan sorular

> [!IMPORTANT]
> Bugün, IaaS VM 'lerinin yaklaşık %90 ' u [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)kullanıyor. 28 Şubat 2020 itibariyle klasik VM 'Ler kullanımdan kaldırılmıştır ve 1 Mart 2023 tarihinde tamamen kullanımdan kaldırılacaktır. Bu kullanımdan kaldırma ve [nasıl etkilediği](./classic-vm-deprecation.md#how-does-this-affect-me)hakkında [daha fazla bilgi edinin]( https://aka.ms/classicvmretirement) .

## <a name="what-is-azure-service-manager-and-what-does-it-mean-by-classic"></a>Azure Service Manager nedir ve klasik olarak ne anlama geliyor?

IaaS VM (klasik) içindeki "klasik" sözcüğü, Azure Service Manager (ASM) tarafından yönetilen VM 'lere başvurur. Azure Service Manager (ASM), VM 'Leri oluşturma, yönetme, silme ve diğer denetim düzlemi işlemlerini gerçekleştirmekten sorumlu Azure 'un eski denetim düzledir. 

## <a name="what-is-azure-resource-manager"></a>Azure Resource Manager nedir?

[Azure Resource Manager](../azure-resource-manager/management/overview.md) , VM 'leri oluşturma, yönetme, silme ve diğer denetim düzlemi işlemlerini gerçekleştirmekten sorumlu Azure 'un en son denetim düzledir. 

## <a name="what-is-the-time-required-for-migration"></a>Geçiş için gereken süre nedir?

Geçişin planlanması ve yürütülmesi büyük ölçüde mimarinin karmaşıklığına bağlıdır ve birkaç ay sürebilir.  

## <a name="what-is-the-definition-of-a-new-customer-on-iaas-vms-classic"></a>IaaS VM 'lerinde (klasik) yeni bir müşterinin tanımı nedir?

2020 Şubat ayının (kullanım dışı bırakılmadan önce bir ay), aboneliklerinde IaaS VM 'Leri (klasik) olmayan müşteriler yeni müşteri olarak kabul edilir. 

## <a name="what-is-the-definition-of-an-existing-customer-on-iaas-virtual-machines-classic"></a>IaaS sanal makinelerinde (klasik) mevcut bir müşterinin tanımı nedir?

Etkin veya durdurulmuş olan, ancak aboneliklerinde, IaaS VM 'Leri (klasik), Şubat 2020 ' nin ayından Itibaren, mevcut bir müşteri olarak kabul edilen müşteri. Yalnızca bu müşteriler, 1 Mart 2023 ' e kadar, sanal makinelerini Azure Service Manager Azure Resource Manager 'e geçirmeye kadar alır. 

## <a name="why-am-i-getting-an-error-stating-newclassicvmcreationnotallowedforsubscription"></a>Neden "NewClassicVMCreationNotAllowedForSubscription" belirten bir hata alıyorum?

Kullanımdan kaldırma işleminin bir parçası olarak, IaaS VM (klasik) artık yeni müşteriler için kullanılamaz. Size yeni müşteri olarak tanımladınız ve bu nedenle işlem yetkilendirilmedi. Azure Resource Manager kullanmanızı kesinlikle öneririz. Azure Resource Manager kullanarak Azure VM 'Leri kullanyükleyemezsiniz, aboneliğinizi izin verilenler listesine eklemek için lütfen desteğe başvurun.

## <a name="does-this-migration-plan-affect-any-of-my-existing-services-or-applications-that-run-on-azure-virtual-machines"></a>Bu geçiş planı Azure sanal makinelerde çalışan mevcut hizmetlerimi ya da uygulamaların herhangi birini etkiliyor mu? 

IaaS VM 'Leri (klasik) için 1 Mart 2023 ' ye kadar değil. IaaS VM 'Leri (klasik) genel kullanıma yönelik olarak tam olarak desteklenen hizmetlerdir. Microsoft Azure’da, ayak izinizi genişletmek için bu kaynakları kullanmaya devam edebilirsiniz. 1 Mart 2023 ' de, bu VM 'Ler tamamen kullanımdan kaldırılacak ve etkin veya ayrılmış VM 'Ler serbest bırakılacak & durdurulur.

Cloud Services (klasik), depolama hesapları (klasik) vb. gibi klasik diğer kaynaklara hiçbir etkisi olmaz.

## <a name="what-happens-to-my-vms-if-i-dont-plan-on-migrating-in-the-near-future"></a>Yakın gelecekte geçirmeyi planlamadığım VM'lerime ne olur? 

1 Mart 2023 ' de, IaaS VM 'Leri (klasik) tamamen kullanımdan kaldırılacak ve etkin veya ayrılmış VM 'Ler serbest bırakılacak & durdurulur. İş etkisini engellemek için, şimdi geçişinizi planlamaya başlamak ve 1 Mart 2023 ' den önce bu öğeyi doldurmanız çok daha fazla yorum yaptık. Mevcut klasik API 'Leri, Cloud Services ve kaynak modelini kullanımdan kaldırmıyoruz. Resource Manager dağıtım modelinde kullanılabilen gelişmiş özellikleri dikkate alarak geçiş kolaylaştırmak istiyoruz. Bu kaynakları Azure Resource Manager geçirmeye yönelik planlamayı başlatmanız önerilir. 

## <a name="what-does-this-migration-plan-mean-for-my-existing-tooling"></a>Bu geçiş planı, mevcut araçlarım için ne anlama geliyor? 

Resource Manager dağıtım modeli için araçlarınızı güncelleştirmek, geçiş planlarınızda hesaba katmanız gereken en önemli değişikliklerden biridir.

## <a name="how-long-will-the-management-plane-downtime-be"></a>Yönetim düzeyi kapalı kalma süresi ne kadar olacak? 

Bu, geçirilmekte olan kaynakların sayısına bağlıdır. Küçük dağıtımlar için (birkaç düzine VM) tüm geçişin bir saatten az sürmesi gerekir. Büyük ölçekli dağıtımlar (yüzlerce VM) için geçiş birkaç saat sürebilir.

## <a name="can-i-roll-back-after-my-migrating-resources-are-committed-in-resource-manager"></a>Geçiş kaynaklarım Kaynak Yöneticisi'ne işlendikten sonra işlemi geri alabilir miyim? 

Kaynaklar hazır durumda olduğu sürece geçişinizi iptal edebilirsiniz. Kaynakların işleme süreci başarıyla tamamlandıktan sonra geri alma desteklenmez.

## <a name="can-i-roll-back-my-migration-if-the-commit-operation-fails"></a>İşleme süreci başarısız olursa geçişi geri alabilir miyim? 

İşleme süreci başarısız olursa geçişi geri alamazsınız. Kaydetme işlemi de dahil olmak üzere tüm geçiş işlemleri birden fazla kez denenebilir. Bu nedenle kısa bir süre sonra işlemi yeniden denemenizi öneririz. Hala bir hata durumunda bir destek bileti oluşturun.

## <a name="do-i-have-to-buy-another-express-route-circuit-if-i-have-to-use-iaas-under-resource-manager"></a>Kaynak Yöneticisi altında IaaS kullanmam gerekiyorsa başka bir hızlı yol devresi satın almam gerekir mi? 

Hayır. Yakın zamanda [ExpressRoute devrelerini klasikten Resource Manager dağıtım modeline taşıma](../expressroute/expressroute-move.md) özelliğini etkinleştirdik. Bir ExpressRoute devreniz varsa yeni bir tane satın almak zorunda değilsiniz.

## <a name="what-if-i-had-configured-azure-role-based-access-control-policies-for-my-classic-iaas-resources"></a>Klasik IaaS kaynaklarım için Azure rol tabanlı erişim denetimi ilkelerini yapılandırdım. 

Geçiş sırasında kaynaklar klasikten Resource Manager’a dönüşür. Bu nedenle, geçişten sonra gerçekleşmesi gereken Azure RBAC ilke güncelleştirmelerini planlamanız önerilir.

## <a name="i-backed-up-my-classic-vms-in-a-vault-can-i-migrate-my-vms-from-classic-mode-to-resource-manager-mode-and-protect-them-in-a-recovery-services-vault"></a>Klasik VM 'lerimi bir kasada yedekledim. VM’lerimi klasik moddan Resource Manager moduna geçirip bunları bir Kurtarma Hizmetleri kasasında koruyabilir miyim?

Bir VM 'yi klasik moddan Kaynak Yöneticisi moduna taşıdığınızda, geçişten önce alınan yedeklemeler, yeni geçirilen Kaynak Yöneticisi sanal makinesine geçirilmez. Ancak, klasik sanal makinelerinize ait yedeklemelerinizi korumak istiyorsanız geçişten önce bu adımları izleyin. 

1. Kurtarma Hizmetleri kasasında **yedekleme öğeleri** dikey penceresine gidin ve VM 'yi seçin. 
2. Yedeklemeyi Durdur ' a tıklayın. Açılan menüden "yedekleme verilerini sakla" seçeneğini belirleyin.

> [!NOTE]
> Bu seçenek, gelecekteki tüm yedekleme işlerinin VM 'nizi korumasını durduracak. Ancak, Azure Backup hizmet yedeklenen kurtarma noktalarını korur.  Kurtarma noktalarını kasada tutmak için ödeme yapmanız gerekir (Ayrıntılar için [Azure Backup fiyatlandırmasına](https://azure.microsoft.com/pricing/details/backup/) bakın). Gerekirse VM 'yi geri yükleyebileceksiniz. VM korumasını sürdürmeye karar verirseniz, *yedeklemeyi yeniden başlatma* seçeneğini kullanabilirsiniz.
>
>

Sanal makineyi Kaynak Yöneticisi moduna geçirmek için 

1. Yedekleme/anlık görüntü uzantısını VM'den silin.
2. Sanal makineyi, klasik moddan Resource Manager moduna geçirin. Sanal makineye karşılık gelen depolama ve ağ bilgilerinin de Resource Manager moduna geçirildiğinden emin olun.

Ayrıca, geçirilen VM 'yi yedeklemek istiyorsanız, [yedeklemeyi etkinleştirmek](../backup/quick-backup-vm-portal.md#enable-backup-on-a-vm)Için sanal makine yönetimi dikey penceresine gidin.

## <a name="can-i-validate-my-subscription-or-resources-to-see-if-theyre-capable-of-migration"></a>Geçişe uygun olup olmadıklarını görmek için aboneliğimi ya da kaynaklarımı doğrulayabilir miyim? 

Evet. Platform destekli geçiş seçeneğinde, geçiş için hazırlanmanın ilk adımı kaynakları geçişe uygun olup olmadığını doğrulamaktır. Doğrulama işlemi başarısız olursa, geçiş işleminin neden tamamlanamadığına dair tüm nedenlerle ilgili iletiler alırsınız.

## <a name="what-happens-if-i-run-into-a-quota-error-while-preparing-the-iaas-resources-for-migration"></a>Geçiş için IaaS kaynaklarını hazırlarken bir kota hatasıyla karşılaşırsam ne olur? 

Geçiş işlemini durdurmanızı ve VM’leri geçirdiğiniz bölgede kotaları artırmak için bir destek isteği göndermenizi öneririz. Kota isteği onaylandıktan sonra geçiş adımlarını yeniden başlatabilirsiniz.

## <a name="how-do-i-report-an-issue"></a>Bir sorunu nasıl bildirebilirim? 

[Microsoft Q&, bir sanal makine için soru sayfası](/answers/topics/azure-virtual-machines.html)olan ve Classicıaasmigration anahtar sözcüğüyle ilgili sorunlarınızı ve sorularınızı gönderin. Tüm sorularınızı bu foruma göndermenizi öneririz. Destek anlaşmanız varsa, destek bileti de açabilirsiniz.

## <a name="what-if-i-dont-like-the-names-of-the-resources-that-the-platform-chose-during-migration"></a>Geçiş sırasında platformun seçtiği kaynak adlarını beğenmezsem ne yapabilirim? 

Klasik dağıtım modelinde adlarını özellikle belirttiğiniz tüm kaynaklar geçiş sırasında korunur. Bazı durumlarda yeni kaynaklar oluşturulur. Örneğin tüm VM’ler için ağ arabirimleri oluşturulur. Geçiş sırasında oluşturulan bu yeni kaynakların adlarını denetleme olanağını şu anda desteklemiyoruz. Bu özellik için oylarınızı [Azure geri bildirim forumuna](https://feedback.azure.com) girebilirsiniz.

## <a name="can-i-migrate-expressroute-circuits-used-across-subscriptions-with-authorization-links"></a>Yetkilendirme bağlantıları olan aboneliklerde kullanılan ExpressRoute devrelerini geçirebilir miyim? 

Çapraz abonelik yetkilendirme bağlantılar kullanan ExpressRoute devreleri kapalı kalma süresi olmadan otomatik olarak geçirilemez. Bunları elle nasıl geçirebileceğiniz hakkında yönergelerimiz vardır. Adımlar ve daha fazla bilgi için bkz. [ExpressRoute devrelerini ve ilgili sanal ağları klasikten Resource Manager dağıtım modeline geçirme](../expressroute/expressroute-migration-classic-resource-manager.md).

## <a name="i-got-the-message-vm-is-reporting-the-overall-agent-status-as-not-ready-hence-the-vm-cannot-be-migrated-ensure-that-the-vm-agent-is-reporting-overall-agent-status-as-ready-or-vm-contains-extension-whose-status-is-not-being-reported-from-the-vm-hence-this-vm-cannot-be-migrated"></a>*"VM, genel aracı durumunu kullanılamıyor olarak raporluyor" iletisini aldım. Bu nedenle, VM geçirilemez. VM aracısının genel aracı durumunu Ready olarak bildirdiğinden emin olun "* veya *" VM, durumu VM 'den bildirilmeyen bir uzantı içeriyor. Bu nedenle, bu VM geçirilemez. "*

Bu ileti, VM’nin giden İnternet bağlantısı olmadığında alınır. VM aracısı, aracı durumunu güncelleştirmek için, giden bağlantı üzerinden her beş dakikada bir Azure depolama hesabına erişir.


## <a name="next-steps"></a>Sonraki adımlar

* [IaaS kaynaklarının klasik ile Azure Resource Manager geçişine genel bakış](./migration-classic-resource-manager-overview.md)
* [Klasik modelden Azure Resource Manager’a platform destekli geçişe ayrıntılı teknik bakış](migration-classic-resource-manager-deep-dive.md)
* [IaaS kaynaklarının Klasik’ten Azure Resource Manager’a geçişini planlama](migration-classic-resource-manager-plan.md)
* [IaaS kaynaklarını klasik 'ten Azure Resource Manager geçirmek için PowerShell 'i kullanma](migration-classic-resource-manager-ps.md)
* [IaaS kaynaklarını klasik 'ten Azure Resource Manager geçirmek için CLı kullanma](migration-classic-resource-manager-cli.md)
* [IaaS kaynaklarının klasik 'dan Azure Resource Manager geçişine yardımcı olacak topluluk araçları](migration-classic-resource-manager-community-tools.md)
* [En sık karşılaşılan geçiş hatalarını gözden geçirme](migration-classic-resource-manager-errors.md)
