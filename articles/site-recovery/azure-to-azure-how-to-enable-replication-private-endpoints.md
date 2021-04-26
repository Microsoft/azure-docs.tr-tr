---
title: Azure Site Recovery Özel uç noktalar için çoğaltmayı etkinleştir
description: Bu makalede, Site Recovery kullanarak bir Azure bölgesinden diğerine özel uç noktaları olan VM 'Ler için çoğaltmanın nasıl yapılandırılacağı açıklanır.
author: Harsha-CS
ms.author: harshacs
ms.service: site-recovery
ms.topic: article
ms.date: 07/14/2020
ms.custom: references_regions
ms.openlocfilehash: 86f18be73966cb07489630191420b846622e45b8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98629836"
---
# <a name="replicate-machines-with-private-endpoints"></a>Makineleri özel uç noktalarla Çoğalt

Azure Site Recovery, makinelerinizi yalıtılmış bir sanal ağın içinden çoğaltmak için [Azure özel bağlantı](../private-link/private-endpoint-overview.md) özel noktalarını kullanmanıza olanak sağlar. Bir kurtarma kasasına özel uç nokta erişimi, tüm Azure ticari & kamu bölgelerinde desteklenir.

Bu makalede, aşağıdaki adımları gerçekleştirmenize yönelik yönergeler sağlanmaktadır:

- Makinelerinizi korumak için Azure Backup bir kurtarma hizmetleri Kasası oluşturun.
- Kasadaki bir yönetilen kimliği etkinleştirin ve trafiği kaynaktan hedef konumlara çoğaltmak için müşteri depolama hesaplarına erişim için gerekli izinleri verin. Kasaya özel bağlantı erişimi ayarlarken, depolama için yönetilen kimlik erişimi gereklidir.
- Özel uç noktalar için gereken DNS değişikliklerini yapın
- Bir sanal ağ içindeki bir kasa için özel uç noktalar oluşturma ve onaylama
- Depolama hesapları için özel uç noktalar oluşturun. Gerektiğinde depolama için ortak veya güvenlik duvarı erişimine izin vermeyi sürdürmeye devam edebilirsiniz. Depolama erişimi için özel bir uç nokta oluşturulması Azure Site Recovery için zorunlu değildir.
  
Aşağıda, çoğaltma iş akışının özel uç noktalarla nasıl değiştiği üzerine bir başvuru mimarisi verilmiştir.

:::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/architecture.png" alt-text="Özel uç noktalarla Site Recovery için başvuru mimarisi.":::

## <a name="prerequisites-and-caveats"></a>Önkoşullar ve uyarılar

- Özel uç noktalar yalnızca kasaya kayıtlı bir öğe bulunmayan yeni kurtarma hizmetleri kasaları için oluşturulabilir. Bu nedenle, **kasaya herhangi bir öğe eklenmeden önce** özel uç noktaların oluşturulması gerekir. [Özel uç noktalar](https://azure.microsoft.com/pricing/details/private-link/)için fiyatlandırma yapısını gözden geçirin.
- Kasa için özel bir uç nokta oluşturulduğunda, kasa kilitlenir ve **Özel uç noktaları olan ağlardan başka ağlardan erişilemez**.
- Azure Active Directory şu anda özel uç noktaları desteklemiyor. Bu nedenle, Azure Active Directory bir bölgede çalışması için gereken IP 'Ler ve tam etki alanı adlarının, güvenli ağdan giden erişime izin verilmesi gerekir. Ayrıca, Azure Active Directory erişimine izin vermek için "Azure Active Directory" ağ güvenlik grubu etiketini ve Azure Güvenlik Duvarı etiketlerini de kullanabilirsiniz.
- Hem kaynak makinelerinizin hem de kurtarma makinelerinizin alt ağlarında **en az yedı IP adresi gereklidir** . Kasa için özel bir uç nokta oluşturduğunuzda, Site Recovery mikro hizmetlerine erişmek için beş özel bağlantı oluşturur. Ayrıca, çoğaltmayı etkinleştirdiğinizde, kaynak ve hedef bölge eşleştirmesi için iki ek özel bağlantı ekler.
- Hem kaynak hem de kurtarma alt ağlarında **bir ek IP adresi gereklidir** . Bu IP adresi yalnızca önbellek depolama hesaplarına bağlanan özel uç noktaları kullanmanız gerektiğinde gereklidir.
  Depolama için özel uç noktalar yalnızca Genel Amaçlı v2 türünde oluşturulabilir. [GPv2 üzerindeki veri aktarımının](https://azure.microsoft.com/pricing/details/storage/page-blobs/)fiyatlandırma yapısını gözden geçirin.

 ## <a name="creating-and-using-private-endpoints-for-site-recovery"></a>Site Recovery için özel uç noktalar oluşturma ve kullanma

 Bu bölüm, sanal ağlarınızdaki Azure Site Recovery için özel uç noktalar oluşturma ve kullanma ile ilgili adımlar hakkında bilgi alır.

> [!NOTE]
> Bu adımları, belirtilen sırayla izlemeniz önemle tavsiye edilir. Bunun yapılmaması, işlenen kasanın özel uç noktaları kullanamaması ve işlemi yeni bir kasa ile yeniden başlatmanızı gerektirebilir.

## <a name="create-a-recovery-services-vault"></a>Kurtarma Hizmetleri kasası oluşturma

Kurtarma Hizmetleri Kasası, makinelerin çoğaltma bilgilerini içeren ve Site Recovery işlemleri tetiklemek için kullanılan bir varlıktır. Daha fazla bilgi için bkz. [Kurtarma Hizmetleri Kasası oluşturma](./azure-to-azure-tutorial-enable-replication.md#create-a-recovery-services-vault).

## <a name="enable-the-managed-identity-for-the-vault"></a>Kasa için yönetilen kimliği etkinleştirin.

[Yönetilen bir kimlik](../active-directory/managed-identities-azure-resources/overview.md) , kasanın müşterinin depolama hesaplarına erişim sağlamasına izin verir. Site Recovery, senaryo gereksinimine bağlı olarak kaynak depolama, hedef depolama ve önbellek/günlük depolama hesaplarına erişmesi gerekir.
Kasa için özel bağlantılar hizmeti kullanırken, yönetilen kimlik erişimi önemlidir.

1. Kurtarma Hizmetleri kasanıza gidin. _Ayarlar_ altında **kimlik** ' i seçin.

   :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/enable-managed-identity-in-vault.png" alt-text="Azure portal ve Kurtarma Hizmetleri sayfasını gösterir.":::

1. **Durumu** _Açık_ olarak değiştirin ve **Kaydet**' i seçin.

1. Kasanın Azure Active Directory kayıtlı olduğunu gösteren bir **nesne kimliği** oluşturulur.

## <a name="create-private-endpoints-for-the-recovery-services-vault"></a>Kurtarma Hizmetleri Kasası için özel uç noktalar oluşturma

Azure sanal makineleri için hem yük devretmeyi hem de yeniden çalışmayı etkinleştirmek için kasa için iki özel uç nokta gerekir. Kaynak ağdaki makinelerin korunması için bir özel uç nokta ve kurtarma ağındaki yük devredilen makinelerin yeniden korunması için başka bir uç nokta.

Bu kurulum işlemi sırasında hedef bölgenizde de bir kurtarma sanal ağı oluşturduğunuzdan emin olun.

Portaldaki özel bağlantı merkezini veya [Azure PowerShell](../private-link/create-private-endpoint-powershell.md)aracılığıyla, kaynak sanal ağınız içinde kasanızın ilk özel uç noktasını oluşturun. Kurtarma ağınızın içindeki kasa için ikinci özel uç noktayı oluşturun. Aşağıda, kaynak ağda özel uç nokta oluşturma adımları verilmiştir. İkinci özel uç noktayı oluşturmak için aynı Kılavuzu tekrarlayın.

1. Azure portal arama çubuğunda, "özel bağlantı" öğesini arayın ve seçin. Bu eylem sizi özel bağlantı merkezine götürür.

   :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/search-private-links.png" alt-text="Özel bağlantı merkezi için Azure portal arama gösterir.":::

1. Sol gezinti çubuğunda **Özel uç noktalar**' ı seçin. Özel uç noktalar sayfasında, kasa için özel bir uç nokta oluşturmaya başlamak üzere **\+ Ekle** ' yi seçin.

   :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/create-private-endpoints.png" alt-text="Özel bağlantı merkezinde özel uç nokta oluşturmayı gösterir.":::

1. "Özel uç nokta oluştur" deneyiminden sonra, Özel uç nokta bağlantınızın oluşturulması için ayrıntıları belirtmeniz gerekir.

   1. **Temel bilgiler**: özel uç noktalarınız için temel ayrıntıları girin. Bölge, kaynak makinelerle aynı olmalıdır.

      :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/create-private-endpoints-basic-tab.png" alt-text="Azure portal özel bir uç nokta oluşturmak için temel sekmeyi, proje ayrıntılarını, aboneliği ve diğer ilgili alanları gösterir.":::

   1. **Kaynak**: Bu sekme, bağlantınızı oluşturmak istediğiniz hizmet olarak platform kaynağını bahsetmeniz gerekir. Seçtiğiniz abonelik için **kaynak türünden** _Microsoft. recoveryservices/kasaults_ ' yi seçin. Ardından, **kaynak** Için kurtarma hizmetleri kasasının adını seçin ve **hedef alt kaynak** olarak _Azure Site Recovery_ ayarlayın.

      :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/create-private-endpoints-resource-tab.png" alt-text="Azure portal özel bir uç noktaya bağlanmak için kaynak sekmesini, kaynak türünü, kaynağı ve hedef alt kaynak alanlarını gösterir.":::

   1. **Yapılandırma**: yapılandırma bölümünde, Özel uç noktanın oluşturulmasını istediğiniz sanal ağı ve alt ağı belirtin. Bu sanal ağ, sanal makinenin bulunduğu ağ. **Evet**' i seçerek özel DNS bölgesi ile tümleştirmeyi etkinleştirin. Zaten oluşturulmuş bir DNS bölgesi seçin veya yeni bir tane oluşturun. **Evet** seçildiğinde, bölge otomatik olarak kaynak sanal ağa bağlanır ve yeni IP 'lerin DNS çözümlemesi IÇIN gereken DNS kayıtları ve özel uç nokta için oluşturulan tam etki alanı adları eklenir.

      Aynı kasada bağlantı kurarak her yeni özel uç nokta için yeni bir DNS bölgesi oluşturmayı seçtiğinizden emin olun. Mevcut bir özel DNS bölgesi seçerseniz, önceki CNAME kayıtlarının üzerine yazılır. Devam etmeden önce [Özel uç nokta kılavuzuna](../private-link/private-endpoint-overview.md#private-endpoint-properties) bakın.

      Ortamınızda bir hub ve bağlı bileşen modeli varsa, tüm sanal ağlarınızın aralarında eşleme özelliği zaten etkinleştirilmiş olduğundan Kurulumun tamamı için yalnızca bir özel uç nokta ve yalnızca bir özel DNS bölgesine ihtiyacınız vardır. Daha fazla bilgi için bkz. [Özel uç nokta DNS tümleştirmesi](../private-link/private-endpoint-dns.md#virtual-network-workloads-without-custom-dns-server).

      Özel DNS bölgesini el ile oluşturmak için [özel DNS bölgeleri oluşturma ' daki adımları izleyin ve DNS kayıtlarını el ile ekleyin](#create-private-dns-zones-and-add-dns-records-manually).

      :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/create-private-endpoints-configuration-tab.png" alt-text="Azure portal özel bir uç noktanın yapılandırılması için ağ ve DNS tümleştirme alanlarıyla birlikte yapılandırma sekmesini gösterir.":::

   1. **Etiketler**: isteğe bağlı olarak, Özel uç noktanız için Etiketler ekleyebilirsiniz.

   1. **İnceleme \+ oluşturma**: doğrulama tamamlandığında, Özel uç noktayı oluşturmak için **Oluştur** ' u seçin.

Özel uç nokta oluşturulduktan sonra, Özel uç noktaya beş tam etki alanı adı eklenir. Bu bağlantılar, sanal ağdaki makinelerin kasadaki tüm gerekli Site Recovery mikro hizmetlere erişmesini sağlar. Daha sonra, çoğaltmayı etkinleştirdiğinizde, aynı özel uç noktaya iki ek tam etki alanı adı eklenir.

Beş etki alanı adı aşağıdaki Düzenle biçimlendirilir:

`{Vault-ID}-asr-pod01-{type}-.{target-geo-code}.siterecovery.windowsazure.com`

## <a name="approve-private-endpoints-for-site-recovery"></a>Site Recovery için özel uç noktaları Onayla

Özel uç noktayı oluşturan kullanıcı aynı zamanda kurtarma hizmetleri kasasının sahibiyseniz, yukarıda oluşturulan özel uç nokta birkaç dakika içinde otomatik olarak onaylanır. Aksi takdirde, kasanın sahibi, kullanmadan önce özel uç noktayı onaylamalıdır. İstenen özel uç nokta bağlantısını onaylamak veya reddetmek için, kurtarma Kasası sayfasında "Ayarlar" altında **Özel uç nokta bağlantılarına** gidin.

Devam etmeden önce bağlantının durumunu gözden geçirmek için özel uç nokta kaynağına gidebilirsiniz.

:::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/vault-private-endpoint-connections.png" alt-text="Kasadaki özel uç nokta bağlantıları sayfasını ve Azure portal bağlantıların listesini gösterir.":::

## <a name="optional-create-private-endpoints-for-the-cache-storage-account"></a><a name="create-private-endpoints-for-the-cache-storage-account"></a>Seçim Önbellek depolama hesabı için özel uç noktalar oluşturma

Azure depolama 'ya özel bir uç nokta kullanılıyor olabilir. Depolama erişimi için özel uç noktalar oluşturmak Azure Site Recovery çoğaltma için _isteğe bağlıdır_ . Depolama için özel bir uç nokta oluştururken aşağıdaki gereksinimler geçerlidir:

- Kaynak sanal ağınızdaki önbellek/günlük depolama hesabı için özel bir uç noktaya ihtiyacınız vardır.
- Kurtarma ağındaki yük devredilen makinelerin yeniden korunması sırasında ikinci bir özel uç noktaya ihtiyacınız vardır. Bu özel uç nokta, hedef bölgede oluşturulan yeni depolama hesabı içindir.

> [!NOTE]
> Depolama hesabında özel uç noktalar etkinleştirilmemişse, koruma yine de başarılı olur. Ancak, çoğaltma trafiği Azure Site Recovery ortak uç noktalara geçiş yapılır. Çoğaltma trafiğinin özel bağlantılar aracılığıyla akmasını sağlamak için, depolama hesabının özel uç noktalarla etkinleştirilmesi gerekir.

> [!NOTE]
> Depolama için özel uç nokta yalnızca bir **genel amaçlı v2** depolama hesabında oluşturulabilir. Fiyatlandırma bilgileri için bkz. [Standart Sayfa Blobu fiyatları](https://azure.microsoft.com/pricing/details/storage/page-blobs/).

Özel uç nokta ile bir depolama hesabı oluşturmak için [özel depolama oluşturma kılavuzunu](../private-link/tutorial-private-endpoint-storage-portal.md#create-storage-account-with-a-private-endpoint) izleyin. Özel DNS bölgesiyle tümleştirilmesi için **Evet** ' i seçtiğinizden emin olun. Zaten oluşturulmuş bir DNS bölgesi seçin veya yeni bir tane oluşturun.

## <a name="grant-required-permissions-to-the-vault"></a>Kasaya gerekli izinleri verme

Sanal makineleriniz yönetilen diskler kullanıyorsa, yönetilen kimlik izinlerini yalnızca önbellek depolama hesaplarına vermeniz gerekir. Sanal makinelerin yönetilmeyen diskler kullanıyorsa, kaynak, önbellek ve hedef depolama hesapları için yönetilen kimlik izinlerini vermeniz gerekir. Bu durumda, hedef depolama hesabını önceden oluşturmanız gerekir.

Sanal makinelerin çoğaltılmasını etkinleştirmeden önce, kasanın yönetilen kimliği, depolama hesabının türüne bağlı olarak aşağıdaki rol izinlerine sahip olmalıdır:

- Kaynak Yöneticisi tabanlı depolama hesapları (Standart tür):
  - [Katkıda Bulunan](../role-based-access-control/built-in-roles.md#contributor)
  - [Depolama Blob Verileri Katkıda Bulunanı](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)
- Kaynak Yöneticisi tabanlı depolama hesapları (Premium türü):
  - [Katkıda Bulunan](../role-based-access-control/built-in-roles.md#contributor)
  - [Depolama Blob Verileri Sahibi](../role-based-access-control/built-in-roles.md#storage-blob-data-owner)
- Klasik depolama hesapları:
  - [Klasik depolama hesabı Katılımcısı](../role-based-access-control/built-in-roles.md#classic-storage-account-contributor)
  - [Klasik depolama hesabı anahtar operatörü hizmet rolü](../role-based-access-control/built-in-roles.md#classic-storage-account-key-operator-service-role)

Aşağıdaki adımlar, depolama hesaplarınıza birer birer rol atamasının nasıl ekleneceğini anlatmaktadır:

1. Depolama hesabına gidin ve sayfanın sol tarafındaki **erişim denetimi (IAM)** sayfasına gidin.

1. **Access Control (IAM)** üzerinde bir kez, "rol ataması Ekle" kutusunda **Ekle**' yi seçin.

   :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/storage-role-assignment.png" alt-text="Bir depolama hesabındaki erişim denetimi (ıAM) sayfasını ve Azure portal ' rol ataması Ekle ' düğmesini gösterir.":::

1. "Rol ataması Ekle" yan sayfasında, **rol** açılan listesinden yukarıdaki listeden rolü seçin. Kasanın **adını** girip **Kaydet**' i seçin.

   :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/storage-role-assignment-select-role.png" alt-text="Bir depolama hesabındaki erişim denetimi (ıAM) sayfasını ve rol seçme seçeneklerini ve bu rolü Azure portal için hangi sorumluyu gösterir.":::

Bu izinlere ek olarak, MS güvenilen hizmetlerin de erişime izin verilmesi gerekir. **Özel durumlar** bölümünde "güvenlik duvarları ve sanal ağlar" a gidin ve "Güvenilen Microsoft hizmetlerinin bu depolama hesabına erişmesine izin ver" onay kutusunu seçin.

## <a name="protect-your-virtual-machines"></a>Sanal makinelerinizi koruyun

Yukarıdaki yapılandırmaların tümü tamamlandıktan sonra, sanal makineleriniz için çoğaltmayı etkinleştirme ile devam edin. Site Recovery tüm işlemler, kasadaki özel uç noktalar oluşturulurken DNS tümleştirmesi kullanılmışsa ek adımlar olmadan çalışır. Ancak, DNS bölgeleri el ile oluşturulup yapılandırılmışsa, çoğaltmayı etkinleştirdikten sonra hem kaynak hem de hedef DNS bölgelerinde belirli DNS kayıtları eklemek için ek adımlara ihtiyacınız vardır. Ayrıntılar ve adımlar için bkz. [özel DNS bölgeleri oluşturma ve DNS kayıtlarını el ile ekleme](#create-private-dns-zones-and-add-dns-records-manually).

## <a name="create-private-dns-zones-and-add-dns-records-manually"></a>Özel DNS bölgeleri oluşturma ve DNS kayıtlarını el ile ekleme

Kasa için özel uç nokta oluşturma sırasında özel DNS bölgesi ile tümleştirme seçeneğini seçmediyseniz, bu bölümdeki adımları izleyin.

Mobility aracısının özel IP 'lere tam etki alanı adlarını çözümlemesine izin vermek için bir özel DNS bölgesi oluşturun.

1. Özel bir DNS bölgesi oluşturma

   1. **Tüm hizmetler** arama çubuğunda "özel DNS bölgesi" araması yapın ve açılan listeden "özel DNS bölgeler" seçeneğini belirleyin.

      :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/search-private-dns-zone.png" alt-text="Azure portal yeni kaynaklar sayfasında ' özel DNS Bölgesi ' aramasını gösterir.":::

   1. "Özel DNS bölgeleri" sayfasında, yeni bir bölge oluşturmaya başlamak için **\+ Ekle** düğmesini seçin.

   1. "Özel DNS bölgesi oluştur" sayfasında, gerekli ayrıntıları girin. Özel DNS bölgesinin adını olarak girin `privatelink.siterecovery.windowsazure.com` . Herhangi bir kaynak grubunu ve bunu oluşturmak için herhangi bir aboneliği seçebilirsiniz.

      :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/create-private-dns-zone.png" alt-text="Özel DNS bölgesi oluştur sayfasının temel bilgiler sekmesini ve Azure portal ilgili proje ayrıntılarını gösterir.":::

   1. DNS bölgesini gözden geçirmek ve oluşturmak için **İnceleme \+ Oluştur** sekmesine geçin.

1. Özel DNS bölgesini sanal ağınıza bağlama

   Yukarıda oluşturulan özel DNS bölgelerinin artık sunucularınızın bulunduğu sanal ağ ile bağlantılı olması gerekir. Ayrıca, özel DNS bölgesini hedef sanal ağa önceden bağlamanız gerekir.

   1. Önceki adımda oluşturduğunuz özel DNS bölgesine gidin ve sayfanın sol tarafındaki **sanal ağ bağlantıları** ' na gidin. Buradan, **\+ Ekle** düğmesini seçin.

   1. Gerekli ayrıntıları girin. **Abonelik** ve **sanal ağ** alanları, sunucularınızın bulunduğu sanal ağın ilgili ayrıntıları ile doldurulmalıdır. Diğer alanların olduğu gibi bırakılması gerekir.

      :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/add-virtual-network-link.png" alt-text="Azure portal bağlantı adı, abonelik ve ilgili sanal ağla bir sanal ağ bağlantısı eklemek için sayfayı gösterir.":::

1. DNS kayıtları ekleme

   Gerekli özel DNS bölgelerini ve özel uç noktaları oluşturduktan sonra DNS kayıtlarınızı DNS bölgelerine eklemeniz gerekir.

   > [!NOTE]
   > Özel bir özel DNS bölgesi kullanıyorsanız, benzer girişlerin aşağıda anlatıldığı şekilde yapıldığından emin olun.

   Bu adım özel uç noktanıza ait her bir tam etki alanı adı için özel DNS bölgenize giriş yapmanızı gerektirir.

   1. Özel DNS bölgenize gidin ve sayfanın sol tarafındaki **genel bakış** bölümüne gidin. Buradan, kayıt eklemeyi başlatmak için **\+ kayıt kümesi** ' ni seçin.

   1. Açılan "kayıt kümesi Ekle" sayfasında, her bir tam etki alanı adı ve _bir_ tür kaydı olarak özel IP için bir giriş ekleyin. Tam etki alanı adları ve IP 'Lerin listesi **genel bakış** bölümünde "özel uç nokta" sayfasından elde edilebilir. Aşağıdaki örnekte gösterildiği gibi, Özel uç noktasındaki ilk tam etki alanı adı özel DNS bölgesindeki kayıt kümesine eklenir.

      Bu tam etki alanı adları düzeniyle eşleşir: `{Vault-ID}-asr-pod01-{type}-.{target-geo-code}.siterecovery.windowsazure.com`

      :::image type="content" source="./media/azure-to-azure-how-to-enable-replication-private-endpoints/add-record-set.png" alt-text="Azure portal, tam etki alanı adı için bir DNS A türü kaydını, Özel uç noktasına eklemek için sayfayı gösterir.":::

   > [!NOTE]
   > Çoğaltmayı etkinleştirdikten sonra, her iki bölgedeki özel uç noktalar üzerinde iki tam etki alanı adı oluşturulur. Bu yeni oluşturulan tam etki alanı adları için DNS kayıtlarını da eklemediğinizden emin olun.

## <a name="next-steps"></a>Sonraki adımlar

Sanal makine çoğaltmalarınızda özel uç noktaları etkinleştirdiğiniz için, bu diğer sayfalara ek ve ilgili bilgiler için bkz.

- [Azure sanal makinelerini başka bir Azure bölgesine çoğaltma](./azure-to-azure-how-to-enable-replication.md)
- [Öğretici: Azure VM 'Leri için olağanüstü durum kurtarmayı ayarlama](./azure-to-azure-tutorial-enable-replication.md)