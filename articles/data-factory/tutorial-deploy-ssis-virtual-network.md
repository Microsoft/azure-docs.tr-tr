---
title: Bir Azure-SSIS tümleştirme çalışma zamanını bir sanal ağa katmak üzere yapılandırma öğreticisi
description: Azure sanal ağına katmak için bir Azure-SSIS tümleştirme çalışma zamanına katılmayı öğrenin.
author: chugugrace
ms.author: chugu
ms.service: data-factory
ms.topic: conceptual
ms.date: 1/10/2020
ms.openlocfilehash: 3dbbce4adc44696fdd12f6ce948b48b34caaed75
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100391244"
---
# <a name="configure-an-azure-sql-server-integration-services-ssis-integration-runtime-ir-to-join-a-virtual-network"></a>Bir sanal ağa katılması için bir Azure-SQL Server Integration Services (SSIS) tümleştirme çalışma zamanı (IR) yapılandırma

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Bu öğretici, bir sanal ağa katılması için Azure-SQL Server Integration Services (SSIS) tümleştirme çalışma zamanı (IR) yapılandırmak üzere Azure portal kullanmaya yönelik temel adımları sağlar.

Adımlar şunlardır:

- Bir sanal ağ yapılandırın.
- Azure-SSIS IR Azure Data Factory portalından bir sanal ağa katın.

## <a name="prerequisites"></a>Önkoşullar

- **Azure-SSIS tümleştirme çalışma zamanı**. Azure-SSIS tümleştirme çalışma zamanı yoksa, başlamadan önce [Azure Data Factory ' de bir Azure-SSIS tümleştirme çalışma zamanı sağlayın](tutorial-deploy-ssis-packages-azure.md) .

- **Kullanıcı izni**. Azure-SSIS IR oluşturan kullanıcının, aşağıdaki seçeneklerden birini içeren en az Azure Data Factory kaynağında [rol ataması](../role-based-access-control/role-assignments-list-portal.md#list-role-assignments-for-a-user-at-a-scope) olması gerekir:

    - Yerleşik ağ katılımcısı rolünü kullanın. Bu rol, gerekenden çok daha büyük bir kapsama sahip olan _Microsoft. Network/ \*_ Permission ile birlikte gelir.
    - Yalnızca gerekli _Microsoft. Network/virtualNetworks/ \* /Join/Action_ iznini içeren özel bir rol oluşturun. Ayrıca, Azure Resource Manager bir sanal ağa katılırken Azure-SSIS IR için kendi genel IP adreslerini getirmek istiyorsanız, lütfen role _Microsoft. Network/publicIPAddresses/*/Join/Action_ iznini de ekleyin.

- **Sanal ağ**.

    - Bir sanal ağınız yoksa [Azure Portal kullanarak bir sanal ağ oluşturun](../virtual-network/quick-create-portal.md).

    - Sanal ağın kaynak grubunun belirli Azure ağ kaynaklarını oluşturup silmesi için emin olun.
    
        Azure-SSIS IR, sanal ağ ile aynı kaynak grubunda belirli ağ kaynaklarını oluşturması gerekir. Bu kaynaklar şunları içerir:
        - *\<Guid> -Azurebatch-cloudserviceloaddengeleyici* adlı bir Azure yük dengeleyici
        - * \<Guid> -Azurebatch-cloudservicenetworksecuritygroup adlı bir ağ güvenlik grubu
        - -Azurebatch-cloudservicepublicıp adlı bir Azure genel IP adresi
    
        Azure-SSIS IR, bu kaynaklar oluşturulur. Azure-SSIS IR durdurulduğunda bunlar silinir. Azure-SSIS IR durdurmadan engellemeden kaçınmak için, diğer kaynaklarınızda bu ağ kaynaklarını yeniden kullanmayın.

    - Sanal ağın ait olduğu kaynak grubunda/abonelikte [kaynak kilidi](../azure-resource-manager/management/lock-resources.md) olmadığından emin olun. Salt bir okuma/silme kilidi yapılandırırsanız, Azure-SSIS IR başlatıp durdurma başarısız olur veya yanıt vermeyi durdurur.

    - Aşağıdaki kaynakların, sanal ağın ait olduğu kaynak grubu/abonelik altında oluşturulmasını önleyen bir Azure Ilke atamasından olmadığından emin olun:
        - Microsoft. Network/LoadBalancers
        - Microsoft. Network/NetworkSecurityGroups

- Bu öğreticide **ağ yapılandırma** senaryolarının aşağıdaki konu başlığı altında değildir:
    - Azure-SSIS IR için kendi genel IP adreslerini getir.
    - Kendi etki alanı adı sistemi (DNS) sunucunuzu kullanıyorsanız.
    - Alt ağda bir ağ güvenlik grubu (NSG) kullanıyorsanız.
    - Azure ExpressRoute veya Kullanıcı tanımlı yol (UDR) kullanıyorsanız.
    - Özelleştirilmiş Azure-SSIS IR kullanıyorsanız.
    
    Daha fazla bilgi için [sanal ağ yapılandırmasını](join-azure-ssis-integration-runtime-virtual-network.md#virtual-network-configuration)denetleyin.

## <a name="configure-a-virtual-network"></a>Sanal ağ yapılandırma

Bir Azure-SSIS IR katılmayı denemeden önce bir sanal ağı yapılandırmak için Azure portal kullanın.

1. Microsoft Edge veya Google Chrome 'ı başlatın. Şu anda yalnızca bu Web tarayıcıları Data Factory Kullanıcı arabirimini destekler.

1. [Azure portalında](https://portal.azure.com) oturum açın.

1. **Diğer hizmetler**' i seçin. **Sanal ağları** filtreleyin ve seçin.

1. İçin filtre uygulayın ve listedeki Sanal ağınızı seçin.

1. **Sanal ağ** sayfasında **Özellikler**' i seçin.

1. Sanal ağ için kaynak KIMLIĞINI panoya kopyalamak üzere **kaynak kimliği** için Kopyala düğmesini seçin. KODU OneNote 'taki panodan veya bir dosyada kaydedin.

1. Sol taraftaki menüde **alt ağlar**' ı seçin.

    - Seçtiğiniz alt ağın Azure-SSIS IR için kullanılabilir adres alanının yeterli olduğundan emin olun. IR düğüm numarasının en az iki katı için kullanılabilir IP adreslerini bırakın. Azure, bazı IP adreslerini her alt ağ içinde ayırır. Bu adresler kullanılamaz. Alt ağların ilk ve son IP adresleri protokol uyumu için ayrılmıştır ve Azure hizmetleri için üç adres daha kullanılır. Daha fazla bilgi için bkz. [Bu alt AĞLARDAKI IP adreslerini kullanma konusunda herhangi bir kısıtlama var mı?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)
    - Azure-SSIS IR dağıtmak için GatewaySubnet 'i seçmeyin. Sanal ağ geçitleri için ayrılmıştır.
    - Diğer Azure hizmetleri tarafından (örneğin, SQL veritabanı SQL yönetilen örneği, App Service vb.) özel olarak bulunan bir alt ağ kullanmayın.

1. Azure Batch sağlayıcısının sanal ağa sahip Azure aboneliğine kayıtlı olduğunu doğrulayın. Veya Azure Batch sağlayıcıyı kaydedin. Aboneliğinizde zaten bir Azure Batch hesabınız varsa, aboneliğiniz Azure Batch için kaydedilir. (Data Factory portalında Azure-SSIS IR oluşturursanız, Azure Batch sağlayıcı sizin için otomatik olarak kaydedilir.)

   1. Azure portal, sol taraftaki menüden **abonelikler**' i seçin.

   1. Aboneliğinizi seçin.

   1. Sol tarafta **kaynak sağlayıcıları**' nı seçin ve **Microsoft.Batch** 'in kayıtlı bir sağlayıcı olduğunu onaylayın.

   !["Kayıtlı" durumunun onayı](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Listede **Microsoft.Batch** görmüyorsanız, bunu kaydetmek için aboneliğinizde [boş bir Azure Batch hesabı oluşturun](../batch/batch-account-create-portal.md) . Daha sonra silebilirsiniz.

## <a name="join-the-azure-ssis-ir-to-a-virtual-network"></a>Azure-SSIS IR bir sanal ağa ekleyin

Azure Resource Manager Sanal ağınızı veya klasik Sanal ağınızı yapılandırdıktan sonra, Azure-SSIS IR sanal ağa ekleyebilirsiniz:

1. Microsoft Edge veya Google Chrome 'ı başlatın. Şu anda yalnızca bu Web tarayıcıları Data Factory Kullanıcı arabirimini destekler.

1. [Azure Portal](https://portal.azure.com), sol taraftaki menüden **veri fabrikaları**' nı seçin. Menüde **veri fabrikaları** görmüyorsanız, **diğer hizmetler**' i seçin ve ardından **zekası + analiz** bölümünde **veri fabrikaları**' nı seçin.

   ![Veri fabrikaları listesi](media/join-azure-ssis-integration-runtime-virtual-network/data-factories-list.png)

1. Listeden Azure-SSIS IR veri fabrikanızı seçin. Veri fabrikanızın giriş sayfasını görürsünüz. **Yazar & İzleyicisi** kutucuğunu seçin. Data Factory Kullanıcı arabirimini ayrı bir sekmede görürsünüz.

   ![Data factory giriş sayfası](media/join-azure-ssis-integration-runtime-virtual-network/data-factory-home-page.png)

1. Data Factory Kullanıcı arabiriminde, **Düzenle** sekmesine geçin, **Bağlantılar**' ı seçin ve **tümleştirme çalışma zamanları** sekmesine geçin.

   !["Tümleştirme çalışma zamanları" sekmesi](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtimes-tab.png)

1. Azure-SSIS IR çalışıyorsa, **tümleştirme çalışma zamanları** listesinde, **eylemler** sütununda, Azure-SSIS IR için **Durdur** düğmesini seçin. Azure-SSIS IR durduruncaya kadar düzenleyemezsiniz.

   ![IR 'yi durdur](media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png)

1. Tümleştirme çalışma **zamanları** listesinde, **eylemler** sütununda, Azure-SSIS IR için **Düzenle** düğmesini seçin.

   ![Tümleştirme çalışma zamanını düzenleme](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png)

1. Tümleştirme çalışma zamanı kurulum panelinde, **İleri** düğmesini seçerek **Genel ayarlar** ve **SQL ayarları** bölümlerinde ilerleyin.

1. **Gelişmiş ayarlar** bölümünde:
   1. **Azure-SSIS Integration Runtime katılması için bir VNET seçin, ADF 'nin belirli ağ kaynaklarını oluşturmasına izin verin ve isteğe bağlı olarak kendi statik ortak IP adreslerinizi getirin** onay kutusunu seçin.

   1. **Abonelik** için sanal ağınıza sahip Azure aboneliğini seçin.

   1. **Konum** için, tümleştirme çalışma zamanından aynı konum seçilidir.

   1. **Tür** için, sanal ağınızın türünü seçin: klasik veya Azure Resource Manager. Klasik sanal ağların yakında kullanım dışı olacağı için bir Azure Resource Manager sanal ağı seçmenizi öneririz.

   1. **VNET adı** için sanal ağınızın adını seçin. Sanal ağ hizmet uç noktaları veya SQL yönetilen örneği, SSıSDB barındırmak için özel uç nokta ile SQL veritabanı için kullanılan bir tane olmalıdır. Ya da şirket içi ağınıza bağlı bir aynı olmalıdır. Aksi takdirde, Azure-SSIS IR için kendi statik genel IP adreslerinizi getirmek için herhangi bir sanal ağ olabilir.

   1. **Alt ağ adı** için, sanal ağınız için alt ağ adını seçin. SSıSDB 'yi barındırmak için sanal ağ hizmet uç noktaları ile SQL datbase için kullanılan aynı olmalıdır. Ya da SSıSDB barındırmak için özel uç nokta olan SQL yönetilen örneğiniz için kullanılan farklı bir alt ağ olmalıdır. Aksi takdirde, Azure-SSIS IR için kendi statik genel IP adreslerinizi getirmek için herhangi bir alt ağ olabilir.

   1. **VNET doğrulaması**' nı seçin. Doğrulama başarılı olursa **devam**' ı seçin.

   ![Bir sanal ağda gelişmiş ayarlar](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

1. **Özet** bölümünde, Azure-SSIS IR için tüm ayarları gözden geçirin. Ardından **Güncelleştir**' i seçin.

1. Azure-SSIS IR **Eylemler** sütunundaki **başlat** düğmesini seçerek Azure-SSIS IR başlatın. Bir sanal ağa katılan Azure-SSIS IR başlatmak için 20 ila 30 dakika sürer.

## <a name="next-steps"></a>Sonraki Adımlar

[Azure-SSIS IR bir sanal ağa katılma](join-azure-ssis-integration-runtime-virtual-network.md)hakkında daha fazla bilgi edinin.