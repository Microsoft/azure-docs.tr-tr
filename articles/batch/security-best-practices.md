---
title: Toplu güvenlik ve uyumluluk en iyi uygulamaları
description: Azure Batch çözümlerinizle güvenliği geliştirmek için en iyi uygulamaları ve yararlı ipuçlarını öğrenin.
ms.date: 12/18/2020
ms.topic: conceptual
ms.openlocfilehash: 6ec4a1d89ebaa9318986fc0d51e832652ba51683
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98723821"
---
# <a name="batch-security-and-compliance-best-practices"></a>Toplu güvenlik ve uyumluluk en iyi uygulamaları

Bu makalede, Azure Batch kullanırken güvenliği geliştirmek için rehberlik ve en iyi uygulamalar sağlanmaktadır.

Varsayılan olarak, Azure Batch hesapları ortak bir uç noktaya sahiptir ve herkese açık bir şekilde erişilebilir. Bir Azure Batch havuzu oluşturulduğunda havuz, bir Azure sanal ağının belirtilen alt ağında sağlanır. Batch havuzundaki sanal makinelere, Batch tarafından oluşturulan genel IP adresleri üzerinden erişilir. Bir havuzdaki işlem düğümleri, gerektiğinde birden çok örnek görevi çalıştırmak gibi, gerek duyulduğunda birbirleriyle iletişim kurabilir, ancak havuzdaki düğümler havuz dışındaki sanal makinelerle iletişim kuramaz.

:::image type="content" source="media/security-best-practices/typical-environment.png" alt-text="Tipik bir Batch ortamını gösteren diyagram.":::

Daha güvenli bir Azure Batch dağıtımı oluşturmanıza yardımcı olmak için birçok özellik mevcuttur. [Genel IP adresleri olmadan havuzun sağlamasını](batch-pool-no-public-ip-address.md)yaparak düğümlerin erişimini kısıtlayabilir ve internet 'ten düğümlerin bulunabilirliğini azaltabilirsiniz. İşlem düğümleri, [havuzu bir Azure sanal ağının alt ağında](batch-virtual-network.md)sunarak, diğer sanal makinelerle veya şirket içi bir ağla güvenli bir şekilde iletişim kurabilir. Azure özel bağlantısı tarafından desteklenen bir hizmetten [sanal ağlardan özel erişimi](private-connectivity.md) de etkinleştirebilirsiniz.

:::image type="content" source="media/security-best-practices/secure-environment.png" alt-text="Daha güvenli bir toplu Iş ortamı gösteren diyagram.":::

## <a name="general-security-related-best-practices"></a>Güvenlikle ilgili genel en iyi yöntemler

### <a name="pool-configuration"></a>Havuz yapılandırması

Birçok güvenlik özelliği yalnızca [sanal makine yapılandırması](nodes-and-pools.md#configurations)kullanılarak yapılandırılan havuzlar için kullanılabilir ve Cloud Services yapılandırmaya sahip havuzlar için kullanılamaz. Mümkün olduğunda [sanal makine ölçek kümelerini](../virtual-machine-scale-sets/overview.md)kullanan sanal makine yapılandırma havuzlarını kullanmanızı öneririz.

### <a name="batch-account-authentication"></a>Batch hesabı kimlik doğrulaması

Batch hesabı erişimi iki kimlik doğrulama yöntemini destekler: paylaşılan anahtar ve [Azure Active Directory (Azure AD)](batch-aad-auth.md).

Batch hesabı kimlik doğrulaması için Azure AD kullanmanızı kesinlikle öneririz. Bazı Batch özellikleri, burada ele alınan güvenlikle ilgili özelliklerin birçoğu dahil olmak üzere bu kimlik doğrulama yöntemini gerektirir.

### <a name="batch-account-pool-allocation-mode"></a>Batch hesap havuzu ayırma modu

Batch hesabı oluştururken iki [havuz ayırma modu](accounts.md#batch-accounts)arasından seçim yapabilirsiniz:

- **Batch hizmeti**: temel alınan bulut hizmeti veya havuz düğümlerini ayırmak ve yönetmek için kullanılan sanal makine ölçek kümesi kaynaklarının iç aboneliklerde oluşturulduğu ve doğrudan Azure Portal görünmeyen varsayılan seçenektir. Yalnızca Batch havuzları ve düğümleri görünür. 
- **Kullanıcı aboneliği**: temel alınan bulut hizmeti veya sanal makine ölçek kümesi kaynakları, Batch hesabıyla aynı abonelikte oluşturulur. Bu kaynaklar bu nedenle, ilgili Batch kaynaklarına ek olarak abonelikte görülebilir.

Kullanıcı aboneliği modunda, bir havuz oluşturulduğunda Batch VM 'Leri ve diğer kaynaklar doğrudan aboneliğinizde oluşturulur. Azure ayrılmış VM örneklerini kullanarak Batch havuzları oluşturmak, sanal makine ölçek kümesi kaynakları üzerinde Azure Ilkesi 'ni kullanmak ve/veya abonelikte çekirdek kotayı yönetmek istiyorsanız (abonelikteki tüm Batch hesaplarında paylaşılır) Kullanıcı aboneliği modu gereklidir. Kullanıcı aboneliği modunda bir Batch hesabı oluşturmak için aboneliğinizi Azure Batch hizmetine kaydetmeniz ve hesabı bir Azure Key Vault ile ilişkilendirmeniz de gerekir.

## <a name="restrict-network-endpoint-access"></a>Ağ uç noktası erişimini kısıtla

### <a name="batch-network-endpoints"></a>Batch ağ uç noktaları

Varsayılan olarak, genel IP adreslerine sahip uç noktalar Batch hesapları, toplu Iş havuzları ve havuz düğümleri ile iletişim kurmak için kullanılır.

### <a name="batch-account-api"></a>Batch hesabı API 'SI

 Bir Batch hesabı oluşturulduğunda, [REST API](/rest/api/batchservice/)kullanarak hesap için çoğu işlemi çağırmak için kullanılan genel bir uç nokta oluşturulur. Hesap uç noktasının, biçimi kullanan bir temel URL 'SI vardır `https://{account-name}.{region-id}.batch.azure.com` . Batch hesabına erişim, HTTPS kullanılarak Şifrelenmekte olan hesap uç noktası ile iletişim ile güvenli hale getirilir ve her istek, paylaşılan anahtar veya Azure Active Directory (Azure AD) kimlik doğrulaması kullanılarak doğrulanır.

### <a name="azure-resource-manager"></a>Azure Resource Manager

Bir Batch hesabına özgü işlemlere ek olarak, [yönetim işlemleri](/rest/api/batchmanagement/) tek ve birden çok Batch hesapları için de geçerlidir. Bu yönetim işlemlerine Azure Resource Manager aracılığıyla erişilir.

Azure Resource Manager aracılığıyla Batch yönetim işlemleri HTTPS kullanılarak şifrelenir ve her isteğin kimliği Azure AD kimlik doğrulaması kullanılarak doğrulanır.

### <a name="batch-pool-nodes"></a>Toplu işlem havuzu düğümleri

Batch hizmeti, havuzdaki her düğümde çalışan bir Batch düğüm aracısıyla iletişim kurar. Örneğin, hizmet, düğüm aracısının bir görevi çalıştırmasını, bir görevi durdurmasını veya bir görev için dosyaları almasını ister. Düğüm aracısıyla iletişim, bir veya daha fazla yük dengeleyicileri tarafından etkinleştirilir ve bu sayı bir havuzdaki düğüm sayısına bağlıdır. Yük dengeleyici, her düğüm için benzersiz bir bağlantı noktası numarasıyla iletişim kurmak istediğiniz düğüme iletişimi iletir. Varsayılan olarak, yük dengeleyicilerin kendileriyle ilişkili genel IP adresleri vardır. Ayrıca, RDP veya SSH aracılığıyla havuz düğümlerine uzaktan erişebilirsiniz (Bu erişim, yük dengeleyiciler aracılığıyla iletişim ile varsayılan olarak etkindir).

### <a name="restricting-access-to-batch-endpoints"></a>Batch uç noktalarına erişimi sınırlandırma 

Özellikle çözüm bir sanal ağ kullandığında çeşitli toplu uç noktalarına erişimi sınırlandırmak için çeşitli yetenekler mevcuttur. 

#### <a name="use-private-endpoints"></a>Özel uç noktaları kullanma

[Azure özel bağlantısı](../private-link/private-link-overview.md) , sanal ağınızdaki özel bir uç nokta üzerinden Azure PaaS hizmetlerine ve Azure 'da barındırılan müşteri/iş ortağı hizmetlerine erişim sağlar. Bir Batch hesabına erişimi sanal ağ içinden veya eşlenen herhangi bir sanal ağdan kısıtlamak için özel bağlantı kullanabilirsiniz. Özel bağlantıyla eşlenen kaynaklara, VPN veya Azure ExpressRoute aracılığıyla özel eşleme üzerinden şirket içinde de erişilebilir.

Özel uç noktaları kullanmak için bir Batch hesabının oluşturulduğunda uygun şekilde yapılandırılması gerekir; ortak ağ erişim yapılandırması devre dışı bırakılmalıdır. Oluşturulduktan sonra özel uç noktalar oluşturulup Batch hesabıyla ilişkilendirilebilir. Daha fazla bilgi için bkz. [Azure Batch hesaplarıyla özel uç noktaları kullanma](private-connectivity.md).

#### <a name="create-pools-in-virtual-networks"></a>Sanal ağlarda havuzlar oluşturma

Bir Batch havuzundaki işlem düğümleri bir sanal ağ (VNET) gerekmeden çok örnekli görevleri çalıştırmak gibi birbirleriyle iletişim kurabilir. Ancak, varsayılan olarak, bir havuzdaki düğümler bir sanal ağ üzerindeki havuzun dışındaki sanal makinelerle iletişim kuramaz ve lisans sunucuları veya dosya sunucuları gibi özel IP adresleri vardır.

İşlem düğümlerinin diğer sanal makinelerle veya şirket içi bir ağla güvenli bir şekilde iletişim kurmasına izin vermek için bir havuzu Azure VNET 'in alt ağında olacak şekilde yapılandırabilirsiniz.

Havuzlar genel IP uç noktalarına sahip olduğunda, alt ağ, Batch hizmetinden gelen iletişimin, Işlem düğümlerinde görevleri zamanlayabilmesini ve işlem düğümlerinde diğer işlemleri gerçekleştirmesini ve iş yükünüzün gerektirdiği şekilde Azure depolama veya diğer kaynaklarla iletişim kuracak giden iletişimi kullanmasına izin vermelidir. Sanal makine yapılandırmasındaki havuzlar için Batch, işlem düğümlerine bağlı ağ arabirimi düzeyinde ağ güvenlik grupları (NSG 'ler) ekler. Bu NSG 'ler etkinleştirilecek kurallara sahiptir:

- Batch hizmeti IP adreslerinden gelen TCP trafiği
- Uzaktan erişim için gelen TCP trafiği
- Sanal ağa herhangi bir bağlantı noktasında giden trafik (alt ağ düzeyindeki NSG kuralları başına değiştirilebilir)
- İnternet 'e herhangi bir bağlantı noktasında giden trafik (alt ağ düzeyindeki NSG kuralları başına değiştirilebilir)

Batch kendi NSG 'leri yapılandırdığından, sanal ağ alt ağ düzeyinde NSG 'ler belirtmeniz gerekmez. Toplu işlem düğümlerinin dağıtıldığı alt ağ ile ilişkili bir NSG varsa veya varsayılan değerleri geçersiz kılmak için özel NSG kuralları uygulamak isterseniz, bu NSG 'yi en az gelen ve giden güvenlik kurallarıyla, havuz düğümleri ve havuz düğümü için Azure depolama ile iletişim kurulmasına izin vermek üzere yapılandırmanız gerekir.

Daha fazla bilgi için bkz. [Sanal ağda Azure Batch havuzu oluşturma](batch-virtual-network.md).

#### <a name="create-pools-with-static-public-ip-addresses"></a>Statik genel IP adreslerine sahip havuzlar oluşturun

Varsayılan olarak, havuzlarla ilişkili genel IP adresleri dinamiktir; bir havuz oluşturulduğunda ve bir havuz yeniden boyutlandırılırken IP adresleri eklenebileceği veya kaldırılabileceği zaman oluşturulur. Havuz düğümlerinde çalışan görev uygulamalarının dış hizmetlere erişmesi gerektiğinde, bu hizmetlere erişimin belirli IP 'Ler ile kısıtlanması gerekebilir.  Bu durumda, dinamik IP adreslerine sahip olmak yönetilemez olur.

Havuz oluşturmadan önce Batch hesabıyla aynı abonelikte statik genel IP adresi kaynakları oluşturabilirsiniz. Daha sonra, havuzunuzu oluştururken bu adresleri belirtebilirsiniz.

Daha fazla bilgi için bkz. [belirtilen genel IP adreslerine sahip bir Azure Batch havuzu oluşturma](create-pool-public-ip.md).

#### <a name="create-pools-without-public-ip-addresses"></a>Genel IP adresleri olmayan havuzlar oluştur

Varsayılan olarak, bir Azure Batch sanal makine yapılandırma havuzundaki tüm işlem düğümlerine bir veya daha fazla genel IP adresi atanır. Bu uç noktalar, Batch hizmeti tarafından, internet 'e giden erişim de dahil olmak üzere görevleri zamanlamak ve işlem düğümleri ile iletişim kurmak için kullanılır.

Bu düğümlere erişimi kısıtlamak ve bu düğümlerin Internet 'ten bulunabilirliğini azaltmak için, havuzu genel IP adresleri olmadan sağlayabilirsiniz.

Daha fazla bilgi için bkz. [genel IP adresleri olmadan havuz oluşturma](batch-pool-no-public-ip-address.md).

#### <a name="limit-remote-access-to-pool-nodes"></a>Havuz düğümlerine uzaktan erişimi sınırlayın

Batch, varsayılan olarak, ağ bağlantısı olan düğüm kullanıcısının RDP veya SSH kullanarak bir Batch havuzundaki bir işlem düğümüne dışarıdan bağlanmasına izin verir.

Düğümlere uzaktan erişimi sınırlandırmak için aşağıdaki yöntemlerden birini kullanın:

- [PoolEndpointConfiguration](/rest/api/batchservice/pool/add#poolendpointconfiguration) erişimini reddedecek şekilde yapılandırın. Uygun ağ güvenlik grubu (NSG) havuz ile ilişkilendirilecek.
- [Genel IP adresleri olmadan](batch-pool-no-public-ip-address.md)havuzunuzu oluşturun. Varsayılan olarak, Bu havuzların VNet dışında erişilemez.
- RDP veya SSH bağlantı noktalarına erişimi reddetmek için bir NSG 'yi VNet ile ilişkilendirin.
- Düğümde hiçbir Kullanıcı oluşturmayın. Düğüm kullanıcıları olmadan uzaktan erişim mümkün olmayacaktır.

## <a name="encrypt-data"></a>Verileri şifreleme

### <a name="encrypt-data-in-transit"></a>Yoldaki verileri şifreleme

Batch hesabı uç noktası (veya Azure Resource Manager aracılığıyla) ile yapılan tüm iletişimin HTTPS kullanması gerekir. `https://`Batch hizmetine bağlanırken API 'lerde belirtilen Batch hesabı URL 'lerinde kullanmanız gerekir.

Batch hizmeti ile iletişim kuran istemciler, Aktarım Katmanı Güvenliği (TLS) 1,2 kullanacak şekilde yapılandırılmalıdır.

### <a name="encrypt-batch-data-at-rest"></a>Bekleyen toplu Iş verilerini şifreleyin

Batch API 'Lerinde belirtilen bazı bilgiler, hesap sertifikaları, iş ve görev meta verileri ve görev komut satırları gibi Batch hizmeti tarafından depolandığında otomatik olarak şifrelenir. Varsayılan olarak, bu veriler her Batch hesabına özgü Azure Batch platform tarafından yönetilen anahtarlar kullanılarak şifrelenir.

Ayrıca, bu verileri [müşteri tarafından yönetilen anahtarları](batch-customer-managed-key.md)kullanarak şifreleyebilirsiniz. [Azure Key Vault](../key-vault/general/overview.md) , anahtarı oluşturmak ve depolamak için kullanılır ve anahtar tanımlayıcısı Batch hesabınızla kaydedilir.

### <a name="encrypt-compute-node-disks"></a>İşlem düğümü disklerini şifreleyin

Batch işlem düğümlerinde varsayılan olarak iki disk vardır: bir işletim sistemi diski ve yerel geçici SSD. Batch tarafından yönetilen [Dosyalar ve dizinler](files-and-directories.md) , geçici SSD üzerinde bulunur. Bu, Görev çıkış dosyaları gibi dosyalar için varsayılan konumdur. Batch görev uygulamaları SSD veya işletim sistemi diskinde varsayılan konumu kullanabilir.

Ek güvenlik için, şu Azure disk şifrelemesi olanaklarından birini kullanarak bu diskleri şifreleyin:

- [Platform tarafından yönetilen anahtarlarla bekleyen yönetilen disk şifrelemesi](../virtual-machines/disk-encryption.md#platform-managed-keys)
- [Platform tarafından yönetilen anahtar kullanarak konakta şifreleme](../virtual-machines/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)
- [Azure Disk Şifrelemesi](disk-encryption.md)

## <a name="securely-access-services-from-compute-nodes"></a>İşlem düğümlerinden hizmetlere güvenli bir şekilde erişin

Batch düğümleri, diğer hizmetlere erişmek için görev uygulamaları tarafından kullanılabilecek [Azure Key Vault](../key-vault/general/overview.md)depolanan [kimlik bilgileri ve gizli dizileri güvenli bir şekilde erişebilir](credential-access-key-vault.md) . Havuz düğümlerine Key Vault erişimi sağlamak için bir sertifika kullanılır.

## <a name="governance-and-compliance"></a>İdare ve uyum

### <a name="compliance"></a>Uyumluluk

Müşterilerin, dünya çapındaki sektörler ve pazarlar genelinde kendi uyumluluk yükümlülüklerini karşılamasına yardımcı olmak için Azure [büyük bir uyumluluk teklifi yelpazesi](https://azure.microsoft.com/overview/trusted-cloud/compliance)kullanır. 

Bu teklifler, bağımsız üçüncü taraf denetim firmaları ve Microsoft tarafından üretilen müşteri Kılavuzu belgeleri tarafından oluşturulan resmi sertifikalar, belirlediğimizi karşıladığımızı, doğrulamalar, yetkilendirmeler ve değerlendirmeler dahil olmak üzere çeşitli türlerde temel bilgiler alır. Toplu Iş çözümlerinizle ilgili olanları öğrenmek için [Uyumluluk sunumlarına kapsamlı genel bakış](https://aka.ms/AzureCompliance) konusunu gözden geçirin.

### <a name="azure-policy"></a>Azure İlkesi

[Azure ilkesi](../governance/policy/overview.md) , kurumsal standartları zorunlu kılmaya ve uygun ölçekte uyumluluğu değerlendirmenize yardımcı olur. Azure Ilkesi için genel kullanım örnekleri, kaynak tutarlılığı, mevzuata uyumluluk, güvenlik, maliyet ve yönetim için idare uygulamayı içerir.

Havuz ayırma modunuza ve bir ilkenin uygulanması gereken kaynaklara bağlı olarak, aşağıdaki yollarla Batch ile Azure Ilkesi kullanın:

- Microsoft.Batch/batchAccounts kaynağını kullanarak doğrudan. Batch hesabı için özelliklerin bir alt kümesi kullanılabilir. Örneğin, ilkeniz geçerli Batch hesap bölgelerini, izin verilen havuz ayırma modunu ve hesaplar için bir ortak ağın etkinleştirilip etkinleştirilmediğini içerebilir.
- Microsoft. COMPUTE/virtualMachineScaleSets kaynağını kullanarak dolaylı olarak. Kullanıcı aboneliği havuz ayırma modundaki Batch hesapları, Batch hesabı aboneliğinde oluşturulan sanal makine ölçek kümesi kaynaklarında ilke kümesine sahip olabilir. Örneğin, izin verilen VM boyutları ve her havuz düğümünde belirli uzantıların çalıştırıldıklarından emin olun.

## <a name="next-steps"></a>Sonraki adımlar

- [Batch Için Azure Güvenlik taban çizgisini](security-baseline.md)gözden geçirin.
- [Azure Batch için daha fazla en iyi yöntem](best-practices.md)okuyun.