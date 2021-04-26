---
title: Kaynak taşınmaların ve topoloji değişikliklerinin Azure Dosya Eşitleme
description: Kaynak grupları, abonelikler ve Azure Active Directory (AAD) kiracılarının arasında eşitleme kaynaklarını taşımayı öğrenin.
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 04/13/2021
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: fb59b8d48d8411ade620304fbd143d86ac5ae919
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107797282"
---
# <a name="move-azure-file-sync-resources-to-a-different-resource-group-subscription-or-aad-tenant"></a>Azure Dosya Eşitleme kaynaklarını farklı bir kaynak grubuna, aboneliğe veya AAD kiracısına taşıma

Bu makalede, Azure Dosya Eşitleme bulut kaynaklarınız ve Azure depolama hesaplarınız için kaynak grubu, abonelik veya Azure Active Directory (AAD) kiracısında nasıl değişiklik yapılacağı açıklanır.

Azure Dosya Eşitleme bulut kaynaklarında değişiklik yapmayı planlarken, depolama kaynaklarını aynı anda göz önünde bulundurmanız önemlidir. Aşağıdaki kaynaklar mevcuttur:

**Kaynakları Azure Dosya Eşitleme (hiyerarşik düzende)**

* :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-storage-sync-service.png" border="false"::: Depolama eşitleme hizmeti
  * :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-registered-servers.png" border="false"::: Kayıtlı sunucu
  * :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-sync-group.png" border="false"::: Eşitleme grubu
    * :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-cloud-endpoint.png" border="false"::: Bulut uç noktası
    * :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-server-endpoint.png" border="false"::: Sunucu uç noktası

Azure Dosya Eşitleme, yalnızca depolama eşitleme hizmeti kaynağıdır. Tüm alt kaynaklar üst öğesine bağlanır ve başka bir depolama Eşitleme hizmetine taşınamaz.

**Azure depolama kaynakları (hiyerarşik düzende)**

* :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-storage-account.png" border="false"::: Depolama hesabı
    * :::image type="icon" source="media/storage-sync-resource-move/storage-sync-resource-move-file-share.png" border="false"::: Dosya paylaşma

Azure depolama 'da, depolama hesabı yalnızca taşıma yeteneğine sahip olan bir kaynaktır. Bir Azure dosya paylaşımının alt kaynak olarak farklı bir depolama hesabına taşınamaz.

## <a name="supported-combinations"></a>Desteklenen birleşimler

Kaynak taşıma planlarken, depolama hesabı ve *depolama eşitleme hizmeti* olarak adlandırılan en üst düzey Azure dosya eşitleme kaynak birlikte değerlendirilmelidir.

En iyi uygulama olarak, depolama eşitleme hizmeti ve dosya paylaşımlarının eşitlenmesi olan depolama hesapları, her zaman aynı abonelikte yer almalıdır. Bu birleşimler desteklenir:

* Depolama eşitleme hizmeti ve depolama hesapları **farklı kaynak gruplarında** (aynı Azure kiracısı) bulunur
* Depolama eşitleme hizmeti ve depolama hesapları **farklı aboneliklerde** (aynı Azure kiracısı) bulunur

> [!IMPORTANT]
> Farklı saklama birleşimleri sayesinde, depolama eşitleme hizmeti ve depolama hesapları farklı AAD kiracılarına göre yönetilen farklı aboneliklerde bulunabilir. Eşitleme çalışıyor gibi görünse de, bu desteklenen bir yapılandırma değildir. Eşitleme, gelecekte bir çalışma koşuluna geri alınamaz şekilde durabilir.

Kaynak taşımayı planlarken, [aynı AAD kiracısı içinde gezinmek](#move-within-the-same-azure-active-directory-tenant) ve [farklı bir AAD kiracısına](#move-to-a-new-azure-active-directory-tenant)taşımak için farklı hususlar vardır. AAD kiracılarını taşırken, eşitleme ve depolama kaynaklarını her zaman birlikte taşıyın.

### <a name="move-within-the-same-azure-active-directory-tenant"></a>Aynı Azure Active Directory kiracısı içinde taşı

:::row:::
    :::column:::
        :::image type="content" source="media/storage-sync-resource-move/storage-sync-resource-move-small.png" alt-text="Bir depolama eşitleme hizmeti kaynağı için, MOVE komutuyla genişletilen Azure portal gösteren bir görüntü. Kaynak grubu taşıma ve abonelik taşıma seçeneklerini gösterir." lightbox="media/storage-sync-resource-move/storage-sync-resource-move.png":::
    :::column-end:::
    :::column:::
        Depolama eşitleme hizmeti kaynağını taşımanın uygun bir yolu Azure portal kullanmaktır. Taşımak istediğiniz depolama Eşitleme hizmetine gidin ve komut çubuğundan **Taşı** ' yı seçin. Bir depolama hesabını taşımak için aynı adımlar geçerlidir. Ayrıca, bir kaynak grubundaki tüm kaynakları bu şekilde taşıyabilirsiniz. Bir kaynak grubunun tamamını taşımak, depolama eşitleme hizmeti ve bu kaynak grubundaki tüm depolama hesaplarını kullandığınızda önerilir.
    :::column-end:::
:::row-end:::

> [!WARNING]
> Bir depolama hesabı kaynağını taşıdığınızda, eşitleme hemen durdurulur. Yeni abonelikteki ilgili depolama hesaplarına erişmek için eşitlemeyi el ile yetkilendirmeniz gerekir. [Azure dosya eşitleme depolama erişim yetkilendirmesi](#azure-file-sync-storage-access-authorization) bölümü, gerekli adımları sağlar.

### <a name="move-to-a-new-azure-active-directory-tenant"></a>Yeni bir Azure Active Directory kiracıya taşıma

Depolama eşitleme hizmeti veya depolama hesapları gibi ayrı kaynaklar kendilerine farklı bir AAD kiracısına taşınamaz. AAD kiracılarını yalnızca Azure abonelikleri taşıyabilir. Yeni AAD kiracısında abonelik yapınızı düşünün. Azure Dosya Eşitleme için adanmış bir abonelik kullanabilirsiniz. 

1. Bir Azure aboneliği oluşturun (veya eski kiracının taşınması gereken mevcut bir tane olduğunu tespit edin.
1. Depolama eşitleme hizmetinizin ve tüm ilişkili depolama hesaplarının [aynı AAD kiracısında bir abonelik hareketi gerçekleştirin](#move-within-the-same-azure-active-directory-tenant) .
1. Eşitleme durdurulacak. Kiracınızı hemen doldurun veya [taşınan depolama hesaplarına erişim sağlamak için eşitlemenin geri yükleme yeteneğini geri yükleyin](#azure-file-sync-storage-access-authorization). Daha sonra yeni AAD kiracısına daha sonra geçebilirsiniz.

Tüm ilgili Azure Dosya Eşitleme kaynakları kendi aboneliklerinde kullanıma alındıktan sonra, tüm aboneliği hedef AAD kiracısına taşımaya hazırsınızdır. [Abonelik aktarma Kılavuzu](../../role-based-access-control/transfer-subscription.md) , bu tür bir aktarımı planlayıp yürütbırakmanıza olanak tanır.

> [!WARNING]
> Bir kiracıdan diğerine bir abonelik aktardığınızda eşitleme hemen durdurulur. Yeni abonelikteki ilgili depolama hesaplarına erişmek için eşitlemeyi el ile yetkilendirmeniz gerekir. [Azure dosya eşitleme depolama erişim yetkilendirmesi](#azure-file-sync-storage-access-authorization) bölümü, gerekli adımları sağlar.

:::row:::
    :::column:::
        :::image type="content" source="media/storage-sync-resource-move/storage-sync-resource-move-aad-tenant.png" alt-text="Azure portal, aboneliğe genel bakış dikey penceresini gösteren bir resim, sayfanın üst kısmında bulunan dizini Değiştir araç çubuğu komutunu vurgular." lightbox="media/storage-sync-resource-move/storage-sync-resource-move-aad-tenant-expanded.png":::
    :::column-end:::
    :::column:::
        Bir plana ve gerekli izinlere sahip olduktan sonra geçişe başlamaya başlayabilirsiniz:
        1. Azure portal, aboneliğinize **genel bakış** dikey penceresine gidin.
        1. **Dizini değiştir**'i seçin
        1. Yeni AAD kiracısını atamak için Sihirbaz adımlarını izleyin.
    :::column-end:::
:::row-end:::

## <a name="azure-file-sync-storage-access-authorization"></a>Azure Dosya Eşitleme depolama erişim yetkilendirmesi

Depolama hesapları yeni bir aboneliğe taşındığında veya bir abonelik içinde yeni bir Azure Active Directory (AAD) kiracısına taşındığında, eşitleme durdurulur. Rol tabanlı erişim (RBAC), bir depolama hesabına erişmek için Azure Dosya Eşitleme yetkilendirmek için kullanılır ve bu rol atamaları kaynaklarla birlikte geçirilmez.

### <a name="azure-file-sync-service-principal"></a>Azure Dosya Eşitleme hizmet sorumlusu

:::row:::
    :::column:::
        :::image type="content" source="media/storage-sync-resource-move/storage-sync-resource-move-afs-rp-registered-small.png" alt-text="Azure portal, abonelik yönetimi, kayıtlı kaynak sağlayıcılarını gösteren bir görüntü." lightbox="media/storage-sync-resource-move/storage-sync-resource-move-afs-rp-registered.png":::
    :::column-end:::
    :::column:::
        Bir depolama hesabına eşitleme erişimini yetkilendirebilmeniz için önce Azure Dosya Eşitleme hizmet sorumlusu AAD kiracınızda bulunmalıdır. </br></br> Bugün yeni bir Azure aboneliği oluşturduğunuzda, *Microsoft. Storagessync* Azure dosya eşitleme kaynak sağlayıcısı aboneliğinize otomatik olarak kaydedilir. Kaynak sağlayıcısı kaydı, aboneliği yöneten Azure Active Directory kiracısında kullanılabilir bir *hizmet sorumlusu* oluşturacak. Hizmet sorumlusu, AAD 'nizin Kullanıcı hesabına benzer. Azure Dosya Eşitleme hizmet sorumlusunu, rol tabanlı erişim denetimi (RBAC) aracılığıyla kaynaklara erişim yetkisi vermek için kullanabilirsiniz. Tek kaynak eşitleme, eşitlenmesi gereken dosya paylaşımlarını içeren depolama hesaplarıdır. Depolama hesabındaki yerleşik rol **okuyucusuna ve veri erişimine** *Microsoft. storagessync* atanmalıdır. </br></br> Bu atama, bir eşitleme grubuna dosya paylaşma eklediğinizde veya başka bir deyişle bir bulut uç noktası oluşturduğunuzda, oturum açmış kullanıcının Kullanıcı bağlamı üzerinden otomatik olarak gerçekleştirilir. Bir depolama hesabı yeni bir aboneliğe veya AAD kiracısına taşınırsa, bu rol ataması kaybedilir ve [el ile yeniden kurulması gerekir](#establish-sync-access-to-a-storage-account).
    :::column-end:::
:::row-end:::

> [!IMPORTANT]
> Hedef Azure aboneliği son zamanlarda oluşturulmadıysa, *Microsoft. Storagessync* kaynak sağlayıcısının abonelikle kayıtlı olduğunu denetleyin. Değilse, aynı portal dikey penceresine el ile ekleyin.

### <a name="establish-sync-access-to-a-storage-account"></a>Bir depolama hesabına eşitleme erişimi oluşturma

[Azure dosya eşitleme hizmet sorumlusu](#azure-file-sync-service-principal) , rol tabanlı erişim denetımı (RBAC) aracılığıyla bir depolama hesabına erişim yetkisi vermek için kullanılmalıdır. Depolama hesabındaki yerleşik rol **okuyucusuna ve veri erişimine** *Microsoft. storagessync* atanmalıdır. 

Bu atama genellikle, bir eşitleme grubuna bir dosya paylaşma eklediğinizde veya başka bir deyişle bir bulut uç noktası oluşturduğunuzda, oturum açmış kullanıcının Kullanıcı bağlamı üzerinden otomatik olarak gerçekleştirilir. Ancak, bir depolama hesabı yeni bir aboneliğe veya AAD kiracısına taşınırsa, bu rol ataması kaybedilir ve el ile yeniden kurulması gerekir.

:::row:::
    :::column:::
        :::image type="content" source="media/storage-sync-resource-move/storage-sync-resource-move-assign-rbac.png" alt-text="Bir depolama hesabındaki okuyucu ve veri erişim rolüne atanan Microsoft. Storagessync hizmet sorumlusunu görüntüleyen bir görüntü":::
    :::column-end:::
    :::column:::
        Azure portal, eşitleme erişimini yeniden sağlamak için ihtiyacınız olan depolama hesabına gidin. <ol><li>Sol taraftaki içindekiler tablosunda **erişim denetimi (IAM)** seçeneğini belirleyin.</li><li>Depolama hesabınıza erişimi olan Kullanıcı ve uygulamaları (hizmet sorumluları) listelemek için rol atamaları sekmesini seçin.</li><li>**Ekle**’yi seçin</li><li>**Rol alanında**, **okuyucu ve veri erişimi**' ni seçin.</li><li>**Seç alanında** *Microsoft. storagessync* yazın, rolü seçin ve **Kaydet**' e tıklayın.</li></ol>
    :::column-end:::
:::row-end:::

## <a name="move-to-a-different-azure-region"></a>Farklı bir Azure bölgesine taşıma

Azure Dosya Eşitleme kaynak *depolama eşitleme hizmeti* ve eşitlenen dosya paylaşımları içeren depolama hesaplarının, dağıtıldığı bir Azure bölgesi vardır. Kaynak oluşturduğunuzda bu bölgeyi belirlersiniz. Depolama eşitleme hizmeti ve depolama hesabı kaynaklarının bölgesi eşleşmelidir. Bu bölgeler, oluşturulduktan sonra kaynak türü üzerinde değiştirilemez.

Bir kaynağa farklı bir bölge atamak, depolama hesabı yedekliliğe bağlı olarak desteklenen bir [bölgeden](#region-fail-over)farklı olur.

## <a name="region-fail-over"></a>Bölgeden yük devreder

Azure depolama, bir depolama hesabı için [coğrafi artıklık seçenekleri sunar](../common/storage-redundancy.md#geo-redundant-storage) . Bu artıklık seçenekleri Azure Dosya Eşitleme ile kullanılan depolama hesapları için sorun oluşturabilir. Ana neden, coğrafi olarak uzak bölgeler arasındaki çoğaltmanın Azure Dosya Eşitleme tarafından gerçekleştirilmemektedir, ancak Azure 'daki depolama alt sisteminde yerleşik olarak bulunan bir depolama çoğaltma teknolojisidir. Uygulama durumunun anlaşılmasına ve Azure Dosya Eşitleme herhangi bir anda Azure dosya paylaşımlarından ve bunlara eşitlenen dosyalar içeren bir uygulamadır. Bu coğrafi olarak yedekli depolama yedekliliği seçeneklerinin herhangi birini kabul ediyorsanız, büyük ölçekli bir olağanüstü durumda verilerinizin tümünü kaybetmezsiniz. Ancak, [veri kaybını tahmin](../common/storage-disaster-recovery-guidance.md#anticipate-data-loss)etmeniz gerekir.

> [!CAUTION]
> Yük devretme işlemi, kaynaklarınızın doğru Azure bölgesinde sağlanması için hiçbir şekilde uygun bir alternatif değildir. Kaynaklarınızın "yanlış" bölgesi varsa, eşitlemeyi durdurmayı ve istediğiniz bölgede dağıtılan yeni Azure dosya paylaşımlarına yeniden eşitlemeyi durdurmanız gerekir.

Bölgesel olarak yük devretme, Microsoft tarafından uzun bir süre boyunca bir Azure bölgesindeki veri merkezlerini işleyecek çok zararlı bir olayda başlatılabilir. İşletmenizin en düşük kalma süresi, Microsoft 'un bir bölgesel yük devretme başlatmadan önce geçmesi için hazırlandığı zamandan daha az olabilir. Bunun gibi durumlarda, yük devretme [işlemleri müşteriler tarafından da başlatılabilir](../common/storage-initiate-account-failover.md).

> [!IMPORTANT]
> Yük devretme durumunda, eşitlemenin tekrar çalışması için etkilenen depolama eşitleme hizmetlerinizin bir destek bileti oluşturmanız gerekir.

## <a name="see-also"></a>Ayrıca bkz.

- [Azure dosya paylaşma ve eşitleme geçiş kılavuzlarıyla genel bakış](../files/storage-files-migration-overview.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json)
- [Azure Dosya Eşitleme'de sorun giderme](file-sync-troubleshoot.md)
- [Azure Dosya Eşitleme dağıtımı planlama](file-sync-planning.md)