---
title: Özel uç noktaları olan şirket içi makineler için çoğaltmayı etkinleştirme
description: Bu makalede, Site Recovery içindeki özel uç noktaları kullanarak şirket içi makineler için çoğaltmanın nasıl yapılandırılacağı açıklanır.
author: Harsha-CS
ms.author: harshacs
ms.service: site-recovery
ms.topic: article
ms.date: 07/14/2020
ms.openlocfilehash: 7f10654e1c96f1756e5864d20fa2a6817385e994
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98629802"
---
# <a name="replicate-on-premises-machines-by-using-private-endpoints"></a>Özel uç noktaları kullanarak şirket içi makineleri çoğaltma

Azure Site Recovery, şirket içi makinelerinizi Azure 'da bir sanal ağa çoğaltmak için [Azure özel bağlantı](../private-link/private-endpoint-overview.md) özel noktalarını kullanmanıza olanak sağlar. Bir kurtarma kasasına özel uç nokta erişimi, tüm Azure ticari & kamu bölgelerinde desteklenir.

Bu makalede, aşağıdaki adımların nasıl tamamlanacağı açıklanmaktadır:

- Makinelerinizi korumak için Azure Backup bir kurtarma hizmetleri Kasası oluşturun.
- Kasa için yönetilen bir kimliği etkinleştirin. Şirket içinden Azure hedef konumlarına trafik çoğaltılmasını sağlamak üzere depolama hesaplarına erişmek için gerekli izinleri verin. Kasaya özel bağlantı erişimi için depolama için yönetilen kimlik erişimi gereklidir.

- Özel uç noktalar için gereken DNS değişikliklerini yapın.
- Bir sanal ağ içindeki bir kasa için özel uç noktalar oluşturun ve onaylayın.
- Depolama hesapları için özel uç noktalar oluşturun. Gerektiğinde depolama için ortak veya güvenlik duvarı erişimine izin vermeyi sürdürmeye devam edebilirsiniz. Depolama alanına erişmek için özel bir uç nokta oluşturulması Azure Site Recovery için gerekli değildir.
  
Aşağıdaki diyagramda, Özel uç noktalarla karma olağanüstü durum kurtarma için çoğaltma iş akışı gösterilmektedir. Şirket içi ağınızda özel uç noktalar oluşturamazsınız. Özel bağlantıları kullanmak için, bir Azure sanal ağı oluşturmanız (Bu makalede *ağ geçişi* olarak adlandırılır), şirket içi ve atlama ağı arasında özel bağlantı oluşturmanız ve ardından atlama ağında özel uç noktalar oluşturmanız gerekir. Herhangi bir özel bağlantı biçimini seçebilirsiniz.

:::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/architecture.png" alt-text="Azure Site Recovery ve özel uç noktalar için mimariyi gösteren diyagram.":::

## <a name="prerequisites-and-caveats"></a>Önkoşullar ve uyarılar

- Özel bağlantılar Site Recovery 9,35 ve üzeri sürümlerde desteklenir.
- Özel uç noktaları yalnızca, kendisine kayıtlı bir öğe bulunmayan yeni kurtarma hizmetleri kasaları için oluşturabilirsiniz. Bu nedenle, kasaya herhangi bir öğe eklenmeden önce özel uç noktalar oluşturmanız gerekir. Fiyatlandırma bilgileri için bkz. [Azure özel bağlantı fiyatlandırması](https://azure.microsoft.com/pricing/details/private-link/) .
- Kasa için özel bir uç nokta oluşturduğunuzda, kasa kilitlenir. Yalnızca özel uç noktaları olan ağlardan erişilebilir.
- Azure Active Directory şu anda özel uç noktaları desteklemez. Bu nedenle, güvenli Azure sanal ağından IP 'lere giden erişime ve Azure Active Directory bir bölgede çalışması için gerekli olan tam etki alanı adlarına izin vermeniz gerekir.
  Ayrıca, Azure Active Directory erişimine izin vermek için "Azure Active Directory" ağ güvenlik grubu etiketini ve Azure Güvenlik Duvarı etiketlerini de kullanabilirsiniz.
- Özel uç noktanızı oluşturduğunuz atlama ağında beş IP adresi gereklidir. Kasa için özel bir uç nokta oluşturduğunuzda, Site Recovery mikro hizmetlerine erişmek için beş özel bağlantı oluşturur.
- Önbellek depolama hesabına yönelik özel uç nokta bağlantısı için atlama ağında bir ek IP adresi gereklidir. Şirket içi ve depolama hesabı uç noktanız arasında herhangi bir bağlantı yöntemi kullanabilirsiniz. Örneğin, Internet veya Azure [ExpressRoute](../expressroute/index.yml)kullanabilirsiniz. Özel bir bağlantı kurmak isteğe bağlıdır. Yalnızca Genel Amaçlı v2 hesaplarında, depolama için özel uç noktalar oluşturabilirsiniz. Genel Amaçlı v2 hesaplarında veri aktarımı fiyatlandırması hakkında bilgi için bkz. [Azure sayfa Blobları fiyatlandırması](https://azure.microsoft.com/pricing/details/storage/page-blobs/) .

 ## <a name="create-and-use-private-endpoints-for-site-recovery"></a>Site Recovery için özel uç noktalar oluşturma ve kullanma

 Aşağıdaki bölümlerde, sanal ağlarınızda Site Recovery için özel uç noktalar oluşturmak ve kullanmak için gerçekleştirmeniz gereken adımlar açıklanır.

> [!NOTE]
> Bu adımları gösterilen sırada izlemenizi öneririz. Aksi takdirde, kasadaki özel uç noktaları kullanabilmeyebilir ve işlemi yeni bir kasa ile yeniden başlatmanız gerekebilir.

### <a name="create-a-recovery-services-vault"></a>Kurtarma Hizmetleri kasası oluşturma

Bir kurtarma hizmetleri Kasası, makinelerin çoğaltma bilgilerini içerir. Site Recovery işlemleri tetiklemek için kullanılır. Bir olağanüstü durum oluşursa yük devretmek istediğiniz Azure bölgesinde bir kurtarma hizmetleri Kasası oluşturma hakkında bilgi için bkz. [Kurtarma Hizmetleri Kasası oluşturma](./azure-to-azure-tutorial-enable-replication.md#create-a-recovery-services-vault).

### <a name="enable-the-managed-identity-for-the-vault"></a>Kasa için yönetilen kimliği etkinleştirme

[Yönetilen bir kimlik](../active-directory/managed-identities-azure-resources/overview.md) , kasasının depolama hesaplarınıza erişmesini sağlar. Site Recovery, gereksinimlerinize bağlı olarak hedef depolama ve önbellek/günlük depolama hesaplarına erişmeniz gerekebilir. Kasa için özel bağlantı hizmetini kullandığınızda yönetilen kimlik erişimi gereklidir.

1. Kurtarma Hizmetleri kasanıza gidin. **Ayarlar** altındaki **kimlik** ' i seçin:

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/enable-managed-identity-in-vault.png" alt-text="Kimlik ayarları sayfasını gösteren ekran görüntüsü.":::

1. **Durumu** **Açık** olarak değiştirin ve **Kaydet**' i seçin.

   Bir nesne KIMLIĞI oluşturulur. Kasa artık Azure Active Directory kaydedilir.

### <a name="create-private-endpoints-for-the-recovery-services-vault"></a>Kurtarma Hizmetleri Kasası için özel uç noktalar oluşturma

Şirket içi kaynak ağındaki makineleri korumak için, atlama ağı 'ndaki kasa için bir özel uç nokta gerekir. Azure portal veya [Azure PowerShell](../private-link/create-private-endpoint-powershell.md)kullanarak özel bağlantı merkezini kullanarak özel uç nokta oluşturun.

1. Azure portal arama kutusunda, "özel bağlantı" araması yapın. Özel bağlantı merkezine gitmek için **özel bağlantı** ' yı seçin:

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/search-private-links.png" alt-text="Özel bağlantı merkezi için Azure portal aramayı gösteren ekran görüntüsü.":::

1. Sol bölmede **Özel uç noktalar**' ı seçin. **Özel uç noktalar** sayfasında, kasanız için özel bir uç nokta oluşturmaya başlamak üzere **Ekle** ' yi seçin:

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-endpoints.png" alt-text="Özel bağlantı merkezinde özel bir uç noktanın nasıl oluşturulacağını gösteren ekran görüntüsü.":::

1. **Özel uç nokta oluştur** sayfasında, Özel uç nokta bağlantınızı oluşturma ayrıntılarını belirtin.

   1. **Temel bilgiler**. Özel uç noktalarınız için temel ayrıntıları sağlayın. Atlama ağı için kullandığınız bölgeyi kullanın:

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-endpoints-basic-tab.png" alt-text="Özel uç nokta oluşturmak için temel sekmeyi gösteren ekran görüntüsü.":::

   1. **Kaynak**. Bu sekmede, bağlantınızı oluşturmak istediğiniz hizmet olarak platform kaynağını belirtmeniz gerekir. Seçtiğiniz abonelik için **kaynak türü** altında, **Microsoft. recoveryservices/kasa**' yı seçin. **Kaynak** bölümünde kurtarma hizmetleri kasasının adını seçin. **Hedef alt kaynak** olarak **Azure Site Recovery** seçin.

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-endpoints-resource-tab.png" alt-text="Özel bir uç noktaya bağlamak için kaynak sekmesini gösteren ekran görüntüsü.":::

   1. **Yapılandırma**. Bu sekmede, Özel uç noktanın oluşturulmasını istediğiniz ağ ve alt ağı atla ' yı belirtin. 

      **Evet**' i seçerek özel bir DNS bölgesi ile tümleştirmeyi etkinleştirin.
      Var olan bir DNS bölgesi seçin veya yeni bir tane oluşturun. **Evet** seçildiğinde bölge, atlama ağına otomatik olarak bağlanır. Bu eylem Ayrıca, yeni IP 'lerin DNS çözümlemesi için gereken DNS kayıtlarını ve özel uç nokta için oluşturulan tam etki alanı adlarını ekler.

      Aynı kasada bağlantı kurarak her yeni özel uç nokta için yeni bir DNS bölgesi oluşturmayı seçtiğinizden emin olun. Mevcut bir özel DNS bölgesi seçerseniz, önceki CNAME kayıtlarının üzerine yazılır. Devam etmeden önce bkz. [Özel uç nokta Kılavuzu](../private-link/private-endpoint-overview.md#private-endpoint-properties) .

      Ortamınızda bir hub ve bağlı bileşen modeli varsa, Kurulumun tamamı için yalnızca bir özel uç noktaya ve yalnızca bir özel DNS bölgesine ihtiyacınız vardır. Bunun nedeni, tüm sanal ağlarınızın aralarında eşleme özelliği zaten etkinleştirilmiş olmasından kaynaklanır. Daha fazla bilgi için bkz. [Özel uç nokta DNS tümleştirmesi](../private-link/private-endpoint-dns.md#virtual-network-workloads-without-custom-dns-server).

      Özel DNS bölgesini el ile oluşturmak için [özel DNS bölgeleri oluşturma ' daki adımları izleyin ve DNS kayıtlarını el ile ekleyin](#create-private-dns-zones-and-add-dns-records-manually).

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-endpoints-configuration-tab.png" alt-text="Özel bir uç noktanın yapılandırmasına yönelik yapılandırma sekmesini gösteren ekran görüntüsü.":::

   1. **Etiketler**. İsteğe bağlı olarak, Özel uç noktanız için Etiketler ekleyebilirsiniz.

   1. **İnceleme \+ Oluştur**. Doğrulama tamamlandığında, Özel uç noktayı oluşturmak için **Oluştur** ' u seçin.

Özel uç nokta oluşturulduğunda, Özel uç noktaya beş tam etki alanı adı (FQDN) eklenir. Bu bağlantılar, şirket içi ağdaki makinelerin, atlama ağı aracılığıyla, tüm gerekli Site Recovery mikro hizmetlerini kasa bağlamında erişimine olanak tanır. Atlama ağı ve eşlenen tüm ağlarda herhangi bir Azure makinesinin korunması için aynı özel uç noktasını kullanabilirsiniz.

Bu düzende, beş etki alanı adı biçimlendirilir:

`{Vault-ID}-asr-pod01-{type}-.{target-geo-code}.siterecovery.windowsazure.com`

### <a name="approve-private-endpoints-for-site-recovery"></a>Site Recovery için özel uç noktaları Onayla

Özel uç noktasını oluşturursanız ve kurtarma hizmetleri kasasının sahibiyseniz, daha önce oluşturduğunuz özel uç nokta birkaç dakika içinde otomatik olarak onaylanır. Aksi takdirde, kasanın sahibi onu kullanabilmeniz için özel uç noktayı onaylaması gerekir. İstenen özel uç nokta bağlantısını onaylamak veya reddetmek için, kurtarma Kasası sayfasında **Ayarlar** altında **Özel uç nokta bağlantıları** ' na gidin.

Devam etmeden önce bağlantının durumunu gözden geçirmek için özel uç nokta kaynağına gidebilirsiniz:

:::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/vault-private-endpoint-connections.png" alt-text="Kasadaki özel uç nokta bağlantıları sayfasını ve bağlantı listesini gösteren ekran görüntüsü.":::

### <a name="optional-create-private-endpoints-for-the-cache-storage-account"></a><a name="create-private-endpoints-for-the-cache-storage-account"></a>Seçim Önbellek depolama hesabı için özel uç noktalar oluşturma

Azure depolama 'ya özel bir uç nokta kullanabilirsiniz. Depolama erişimi için özel uç noktalar oluşturmak Azure Site Recovery çoğaltma için isteğe bağlıdır. Depolama için özel bir uç nokta oluşturursanız, geçiş sanal ağınızdaki önbellek/günlük depolama hesabı için özel bir uç nokta gerekir.

> [!NOTE]
> Depolama hesabında özel uç noktalar etkinleştirilmemişse, koruma yine de başarılı olur. Ancak, çoğaltma trafiği, genel uç noktalara Azure Site Recovery internet üzerinden geçiş yapılır. Çoğaltma trafiğinin özel bağlantılar aracılığıyla akmasını sağlamak için, depolama hesabının özel uç noktalarla etkinleştirilmesi gerekir.

> [!NOTE]
> Depolama için özel uç noktalar yalnızca Genel Amaçlı v2 depolama hesaplarında oluşturulabilir. Fiyatlandırma bilgileri için bkz. [Azure sayfa Blobları fiyatlandırması](https://azure.microsoft.com/pricing/details/storage/page-blobs/).

Özel bir uç nokta ile bir depolama hesabı oluşturmak için [özel depolama oluşturma kılavuzunu](../private-link/tutorial-private-endpoint-storage-portal.md#create-storage-account-with-a-private-endpoint) izleyin. **Özel DNS bölgesiyle tümleştirin** altında **Evet** ' i seçtiğinizden emin olun. Var olan bir DNS bölgesi seçin veya yeni bir tane oluşturun.

### <a name="grant-required-permissions-to-the-vault"></a>Kasaya gerekli izinleri verme

Kurulumunuza bağlı olarak, hedef Azure bölgesinde bir veya daha fazla depolama hesabına ihtiyacınız bulunabilir. Daha sonra, Site Recovery için gereken tüm önbellek/günlük depolama hesapları için yönetilen kimlik izinlerini verin. Bu durumda, gerekli depolama hesaplarını önceden oluşturmanız gerekir.

Sanal makinelerin çoğaltılmasını etkinleştirmeden önce, kasanın yönetilen kimliği, depolama hesabının türüne bağlı olarak aşağıdaki rol izinlerine sahip olmalıdır.

- Kaynak Yöneticisi tabanlı depolama hesapları (Standart tür):
  - [Katkıda Bulunan](../role-based-access-control/built-in-roles.md#contributor)
  - [Depolama Blob Verileri Katkıda Bulunanı](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)
- Kaynak Yöneticisi tabanlı depolama hesapları (Premium türü):
  - [Katkıda Bulunan](../role-based-access-control/built-in-roles.md#contributor)
  - [Depolama Blob Verileri Sahibi](../role-based-access-control/built-in-roles.md#storage-blob-data-owner)
- Klasik depolama hesapları:
  - [Klasik depolama hesabı Katılımcısı](../role-based-access-control/built-in-roles.md#classic-storage-account-contributor)
  - [Klasik depolama hesabı anahtar operatörü hizmet rolü](../role-based-access-control/built-in-roles.md#classic-storage-account-key-operator-service-role)

Bu adımlar, depolama hesabınıza bir rol atamasının nasıl ekleneceğini anlatmaktadır:

1. Depolama hesabına gidin. Sol bölmedeki **erişim denetimi (IAM)** seçeneğini belirleyin.

1. **Rol ataması Ekle** bölümünde **Ekle**' yi seçin.

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/storage-role-assignment.png" alt-text="Bir depolama hesabı için erişim denetimi (ıAM) sayfasını gösteren ekran görüntüsü.":::

1. **Rol ataması Ekle** sayfasında, **rol** listesinde, bu bölümün başlangıcında bulunan listeden rolü seçin. Kasanın adını girip **Kaydet**' i seçin.

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/storage-role-assignment-select-role.png" alt-text="Rol ataması Ekle sayfasını gösteren ekran görüntüsü.":::

Bu izinleri ekledikten sonra, Microsoft Trusted Services erişimine izin vermeniz gerekir. **Güvenlik duvarları ve sanal ağlar** ' a gidin ve **Güvenilen Microsoft hizmetlerinin bu depolama hesabına erişmesine izin ver** ' i **seçin.**

### <a name="protect-your-virtual-machines"></a>Sanal makinelerinizi koruyun

Önceki görevleri tamamladıktan sonra şirket içi altyapınızın kurulumuna devam edin. Aşağıdaki görevlerden birini tamamlayarak devam edin: 

- [VMware ve fiziksel makineler için bir yapılandırma sunucusu dağıtma](./vmware-azure-deploy-configuration-server.md)
- [Hyper-V ortamını çoğaltma için ayarlama](./hyper-v-azure-tutorial.md#set-up-the-source-environment)

Kurulum tamamlandıktan sonra, kaynak makineleriniz için çoğaltmayı etkinleştirin. Kasayı atlama ağı 'nda ana kadar özel uç noktalar oluşturuluncaya kadar altyapıyı ayarlama.

### <a name="create-private-dns-zones-and-add-dns-records-manually"></a>Özel DNS bölgeleri oluşturma ve DNS kayıtlarını el ile ekleme

Kasa için özel uç nokta oluştururken özel bir DNS bölgesi ile tümleştirme seçeneğini seçmediyseniz, bu bölümdeki adımları izleyin.

Site Recovery sağlayıcısının (Hyper-V makineleri için) veya Işlem sunucusunun (VMware/fiziksel makineler için) özel FQDN 'Leri özel IP 'lere çözümlemesine izin vermek için bir özel DNS bölgesi oluşturun.

1. Özel bir DNS bölgesi oluşturun.

   1. **Tüm hizmetler** arama kutusunda "özel DNS bölgesi" araması yapın ve ardından sonuçlarda **özel DNS bölge** ' yi seçin:

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/search-private-dns-zone.png" alt-text="Azure portal yeni kaynaklar sayfasında özel DNS bölgesi aramasını gösteren ekran görüntüsü.":::

   1. **Özel DNS bölgeleri** sayfasında, yeni bir bölge oluşturmaya başlamak için **Ekle** düğmesini seçin.

   1. **Özel DNS bölgesi oluştur** sayfasında, gerekli ayrıntıları girin. Özel DNS bölgesinin adı için **Privatelink.siterecovery.windowsazure.com** girin. Herhangi bir kaynak grubunu ve aboneliği seçebilirsiniz.

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-dns-zone.png" alt-text="Özel DNS bölgesi oluştur sayfasının temel bilgiler sekmesini gösteren ekran görüntüsü.":::

   1. DNS bölgesini gözden geçirmek ve oluşturmak için **İnceleme \+ Oluştur** sekmesine geçin.

1. Özel DNS bölgesini sanal ağınıza bağlayın.

   Şimdi, oluşturduğunuz özel DNS bölgesini atlamaya bağlamanız gerekir.

   1. Önceki adımda oluşturduğunuz özel DNS bölgesine gidin ve sol bölmedeki **sanal ağ bağlantıları** ' na gidin. **Add (Ekle)** seçeneğini belirleyin.

   1. Gerekli ayrıntıları girin. **Abonelik** ve **sanal ağ** listelerinde, atlama ağına karşılık gelen ayrıntıları seçin. Diğer alanlarda varsayılan değerleri değiştirmeyin.

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/add-virtual-network-link.png" alt-text="Sanal ağ bağlantısı ekleme sayfasını gösteren ekran görüntüsü.":::

1. DNS kayıtları ekleyin.

   Gerekli özel DNS bölgesini ve özel uç noktayı oluşturduğdığınıza göre DNS kayıtlarınızı DNS bölgenize eklemeniz gerekir.

   > [!NOTE]
   > Özel bir özel DNS bölgesi kullanıyorsanız, aşağıdaki adımda anlatıldığı gibi benzer girdileri yaptığınızdan emin olun.

   Bu adımda, Özel uç noktanıza ait her FQDN için özel DNS bölgenize giriş yapmanız gerekir.

   1. Özel DNS bölgenize gidin ve sol bölmedeki **genel bakış** bölümüne gidin. Kayıt eklemeyi başlatmak için **kayıt kümesi** ' ni seçin.

   1. **Kayıt kümesi Ekle** sayfasında, her bir tam etki alanı adı için bir giriş ve **bir** tür kaydı olarak özel IP ekleyin. Tam etki alanı adlarının bir listesini ve **Özel uç nokta** sayfasındaki IP 'Leri **genel bakış**' da alabilirsiniz. Aşağıdaki ekran görüntüsünde görebileceğiniz gibi, Özel uç noktasındaki ilk tam etki alanı adı özel DNS bölgesindeki kayıt kümesine eklenir.

      Bu tam etki alanı adları bu Düzenle eşleşir: `{Vault-ID}-asr-pod01-{type}-.{target-geo-code}.siterecovery.windowsazure.com`

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/add-record-set.png" alt-text="Kayıt kümesi Ekle sayfasını gösteren ekran görüntüsü.":::

## <a name="next-steps"></a>Sonraki adımlar

Sanal makine çoğaltmalarınızda özel uç noktaları etkinleştirdiğiniz için, ek ve ilgili bilgiler için şu diğer makalelere bakın:

- [Şirket içi yapılandırma sunucusu dağıtma](./vmware-azure-deploy-configuration-server.md)
- [Şirket içi Hyper-V sanal makineleri için Azure’da olağanüstü durum kurtarma ayarlama](./hyper-v-azure-tutorial.md)