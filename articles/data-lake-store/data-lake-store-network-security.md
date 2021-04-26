---
title: Azure Data Lake Storage 1. Nesil'de ağ güvenliği | Microsoft Docs
description: Sanal ağ tümleştirmesinin Azure Data Lake Storage 1. 'da nasıl çalıştığını anlama
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/09/2018
ms.author: elsung
ms.openlocfilehash: 716e3766fdd7c1999efa12456346862a9902d7a0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92108720"
---
# <a name="virtual-network-integration-for-azure-data-lake-storage-gen1"></a>Azure Data Lake Storage 1. için sanal ağ tümleştirmesi

Bu makalede Azure Data Lake Storage 1. için sanal ağ tümleştirmesi tanıtılmaktadır. Sanal ağ tümleştirmesi ile hesaplarınızı yalnızca belirli sanal ağlardan ve alt ağlardan gelen trafiği kabul edecek şekilde yapılandırabilirsiniz. 

Bu özellik, Data Lake Storage hesabınızı dışarıdan gelebilecek tehditlere karşı korumanıza yardımcı olur.

Data Lake Storage 1. Nesil için sanal ağ tümleştirmesi, erişim belirtecinde ek güvenlik talepleri oluşturmak için sanal ağınızla Azure Active Directory (Azure AD) arasında sanal ağ hizmet uç noktası güvenliğini kullanır. Ardından bu talepler sanal ağınız için Data Lake Storage 1. Nesil hesabınızda kimlik doğrulaması gerçekleştirme ve erişim izni verme amacıyla kullanılır.

> [!NOTE]
> Bu özellikler ek ücrete tabi değildir. Hesabınız Data Lake Storage 1. Nesil için standart ücretler üzerinden faturalandırılır. Daha fazla bilgi için bkz. [Fiyatlandırma](https://azure.microsoft.com/pricing/details/data-lake-store/?cdn=disable). Kullandığınız diğer tüm Azure hizmetleri için bkz. [Fiyatlandırma](https://azure.microsoft.com/pricing/#product-picker).

## <a name="scenarios-for-virtual-network-integration-for-data-lake-storage-gen1"></a>Data Lake Storage 1. Nesil sanal ağ tümleştirmesi senaryoları

Data Lake Storage 1. Nesil sanal ağ tümleştirmesi ile Data Lake Storage 1. Nesil hesabınıza erişimi belirli sanal ağlar ve alt ağlar ile sınırlandırabilirsiniz. Hesabınız belirtilen sanal ağ alt ağına kilitlendikten sonra Azure'daki diğer sanal ağlar/VM'ler tarafından erişim sağlanamaz. Data Lake Storage 1. Nesil sanal ağ tümleştirmesi, işlev açısından [sanal ağ hizmet uç noktaları](../virtual-network/virtual-network-service-endpoints-overview.md) ile aynı senaryoyu etkinleştirir. İki senaryo arasındaki önemli farklar aşağıdaki bölümlerde belirtilmiştir. 

![Data Lake Storage 1. Nesil sanal ağ tümleştirmesi senaryo diyagramı](media/data-lake-store-network-security/scenario-diagram.png)

> [!NOTE]
> Sanal ağ kurallarına ek olarak var olan IP güvenlik duvarı kurallarını kullanarak şirket içi ağlardan erişime de izin verebilirsiniz. 

## <a name="optimal-routing-with-data-lake-storage-gen1-virtual-network-integration"></a>Data Lake Storage 1. Nesil sanal ağ tümleştirmesi için en uygun rota

Sanal ağ hizmet uç noktalarının temel avantajlarından biri, sanal ağınızdan [en iyi rotayı](../virtual-network/virtual-network-service-endpoints-overview.md#key-benefits) kullanma seçeneğidir. Data Lake Storage 1. Nesil hesaplarında aynı rota iyileştirme adımlarını gerçekleştirebilirsiniz. Sanal ağınızdan Data Lake Storage 1. Nesil hesabınıza aşağıdaki [kullanıcı tanımlı rotaları](../virtual-network/virtual-networks-udr-overview.md#user-defined) kullanın.

**Data Lake Storage genel IP adresi**: Hedef Data Lake Storage 1. Nesil hesaplarınız için genel IP adresini kullanın. Hesaplarınızın [DNS adlarını çözümleyerek](./data-lake-store-connectivity-from-vnets.md#enabling-connectivity-to-azure-data-lake-storage-gen1-from-vms-with-restricted-connectivity) Data Lake Storage 1. Nesil hesabınızın IP adresini tanımlayabilirsiniz. Her adres için ayrı bir giriş oluşturun.

```azurecli
# Create a route table for your resource group.
az network route-table create --resource-group $RgName --name $RouteTableName

# Create route table rules for Data Lake Storage public IP addresses.
# There's one rule per Data Lake Storage public IP address. 
az network route-table route create --name toADLSregion1 --resource-group $RgName --route-table-name $RouteTableName --address-prefix <ADLS Public IP Address> --next-hop-type Internet

# Update the virtual network, and apply the newly created route table to it.
az network vnet subnet update --vnet-name $VnetName --name $SubnetName --resource-group $RgName --route-table $RouteTableName
```

## <a name="data-exfiltration-from-the-customer-virtual-network"></a>Müşteri sanal ağından veri sızdırma

Data Lake Storage hesaplarını sanal ağ erişimiyle sınırlandırmaya ek olarak yetkisiz hesaba sızma olmamasını da sağlamak isteyebilirsiniz.

Giden trafiği hedef hesap URL'sine göre filtrelemek için sanal ağınızda bir güvenlik duvarı çözümü kullanın. Yalnızca onaylanan Data Lake Storage 1. Nesil hesaplarına erişim izni verin.

Kullanılabilen seçeneklerin bazıları şunlardır:
- [Azure Güvenlik Duvarı](../firewall/overview.md): Sanal ağınızda bir [Azure Güvenlik Duvarı dağıtın ve yapılandırın](../firewall/tutorial-firewall-deploy-portal.md). Giden Data Lake Storage trafiğinin güvenliğini sağlayın, bilinen ve onaylı hesap URL'si ile kilitleyin.
- [Sanal ağ gereci](https://azure.microsoft.com/solutions/network-appliances/) güvenlik duvarı: Yöneticiniz yalnızca belirli ticari güvenlik duvarı satıcılarının kullanılmasına izin veriyor olabilir. Aynı işlevi gerçekleştirmek için Azure Market'te bulunan ağ sanal gereci güvenlik duvarı çözümlerinden birini kullanın.

> [!NOTE]
> Veri yolunda güvenlik duvarı kullanmak yeni bir atlama eklenmesine neden olur. Bu durum ağ performansını ve uçtan uca veri alışverişini etkileyebilir. Kullanılabilen aktarım hızı ve bağlantı gecikme süresi bu durumdan etkilenebilir. 

## <a name="limitations"></a>Sınırlamalar

- Sanal ağ tümleştirme desteği Data Lake Storage 1. önce oluşturulan HDInsight kümeleri, bu yeni özelliği desteklemek için yeniden oluşturulmalıdır.
 
- Yeni bir HDInsight kümesi oluşturduğunuzda ve sanal ağ tümleştirmesi etkinleştirilmiş bir Data Lake Storage 1. Nesil hesabını seçtiğinizde bu işlem başarısız olur. Öncelikle sanal ağ kuralını devre dışı bırakmanız gerekir. Alternatif olarak Data Lake Storage hesabının **Güvenlik duvarı ve sanal ağlar** dikey penceresinde **Tüm ağlardan ve hizmetlerden erişime izin ver**'i seçebilirsiniz. Ardından, son olarak sanal ağ kuralını yeniden etkinleştirmeden önce HDInsight kümesini oluşturun veya **tüm ağlardan ve hizmetlerden erişime Izin ver** seçimini kaldırın. Daha fazla bilgi için [Özel durumlar](#exceptions) bölümüne bakın.

- Data Lake Storage 1. sanal ağ tümleştirmesi, [Azure kaynakları için yönetilen kimliklerle](../active-directory/managed-identities-azure-resources/overview.md)birlikte çalışmaz.
  
- Sanal ağ tümleştirmesi etkin Data Lake Storage 1. Nesil hesabınızdaki dosya ve klasör verilerine portaldan erişim sağlayamazsınız. Bu kısıtlamaya sanal ağ içindeki VM'lerden erişim ve Veri Gezgini kullanımı gibi etkinlikler dahildir. Hesap yönetimi etkinlikleri çalışmaya devam eder. Sanal ağ tümleştirmesi etkin Data Lake Storage hesabınızdaki dosya ve klasör verilerine portal dışı kaynaklardan erişim sağlayabilirsiniz. Bu kaynaklara SDK erişimi, PowerShell betikleri ve portaldan başlatılmayan diğer tüm Azure hizmetleri dahildir. 

## <a name="configuration"></a>Yapılandırma

### <a name="step-1-configure-your-virtual-network-to-use-an-azure-ad-service-endpoint"></a>1. Adım: Sanal ağınızı bir Azure AD hizmet uç noktasını kullanacak şekilde yapılandırma

1.  Azure portala gidin ve hesabınızda oturum açın.
 
2.  Aboneliğinizde [Yeni bir sanal ağ oluşturun](../virtual-network/quick-create-portal.md). İsterseniz var olan bir sanal ağa da gidebilirsiniz. Sanal ağın Data Lake Storage 1. Nesil hesabıyla aynı bölgede olması gerekir.
 
3.  **Sanal ağ** dikey penceresinde **Hizmet uç noktaları**'nı seçin.
 
4.  **Ekle**'yi seçerek yeni bir hizmet uç noktası ekleyin.

    ![Sanal ağ hizmet uç noktası ekleme](media/data-lake-store-network-security/config-vnet-1.png)

5.  Uç noktası hizmeti olarak **Microsoft.AzureActiveDirectory** seçeneğini belirleyin.

     ![Select the Microsoft.AzureActiveDirectory hizmet uç noktasını seçme](media/data-lake-store-network-security/config-vnet-2.png)

6.  Bağlantı izni vermek istediğiniz alt ağları seçin. **Add (Ekle)** seçeneğini belirleyin.

    ![Alt ağı seçme](media/data-lake-store-network-security/config-vnet-3.png)

7.  Hizmet uç noktasının eklenmesi 15 dakika sürebilir. Eklendikten sonra listede gösterilir. Göründüğünden ve tüm ayrıntıların yapılandırmaya uygun olduğundan emin olun.
 
    ![Hizmet uç noktası ekleme başarılı](media/data-lake-store-network-security/config-vnet-4.png)

### <a name="step-2-set-up-the-allowed-virtual-network-or-subnet-for-your-data-lake-storage-gen1-account"></a>2. Adım: Data Lake Storage 1. Nesil hesabınız için izin verilen sanal ağı veya alt ağı ayarlama

1.  Sanal ağınızı yapılandırdıktan sonra aboneliğinizde [yeni bir Azure Data Lake Storage 1. Nesil hesabı](data-lake-store-get-started-portal.md#create-a-data-lake-storage-gen1-account) oluşturun. İsterseniz var olan bir Data Lake Storage 1. Nesil hesabına da gidebilirsiniz. Data Lake Storage 1. Nesil hesabının sanal ağ ile aynı bölgede olması gerekir.
 
2.  **Güvenlik duvarı ve sanal ağlar**'ı seçin.

    > [!NOTE]
    > Ayarlarda **Güvenlik duvarı ve sanal ağlar** seçeneği görünmüyorsa portal oturumunu kapatın. Tarayıcıyı kapatın ve tarayıcı önbelleğini temizleyin. Makineyi yeniden başlatın ve yeniden deneyin.

       ![Data Lake Storage hesabınıza sanal ağ kuralı ekleme](media/data-lake-store-network-security/config-adls-1.png)

3.  **Seçili ağlar**'ı seçin.
 
4.  **Var olan sanal ağı ekle**' yi seçin.

    ![Var olan sanal ağı ekle](media/data-lake-store-network-security/config-adls-2.png)

5.  Bağlantıya izin vermek istediğiniz sanal ağları ve alt ağları seçin. **Add (Ekle)** seçeneğini belirleyin.

    ![Sanal ağı ve alt ağları seçme](media/data-lake-store-network-security/config-adls-3.png)

6.  Sanal ağların ve alt ağların listede düzgün şekilde gösterildiğinden emin olun. **Kaydet**’i seçin.

    ![Yeni kuralı kaydetme](media/data-lake-store-network-security/config-adls-4.png)

    > [!NOTE]
    > Kaydettikten sonra ayarların geçerli olması 5 dakika sürebilir.

7.  [İsteğe bağlı] **Güvenlik duvarı ve sanal ağlar** sayfasının **Güvenlik duvarı** bölümünde belirli IP adreslerinden gelen bağlantılara izin verebilirsiniz. 

## <a name="exceptions"></a>Özel durumlar
Seçtiğiniz sanal ağların dışındaki Azure hizmetlerinden ve VM'lerden gelen bağlantılara izin verebilirsiniz. **Güvenlik duvarı ve sanal ağlar** dikey penceresinin **Özel durumlar** bölümündeki iki seçenekten birini belirleyin:
 
- **Tüm Azure hizmetlerinin bu Data Lake Storage 1. Nesil hesabına erişmesine izin ver**. Bu seçenek Azure Data Factory ve Azure Event Hubs gibi Azure hizmetlerinin ve tüm Azure VM'lerinin Data Lake Storage hesabınızla iletişim kurmasına izin verir.

- **Azure Data Lake Analytics'in bu Data Lake Storage 1. Nesil hesabına erişmesine izin ver**. Bu seçenek, Data Lake Analytics hizmetinin bu Data Lake Storage hesabına bağlanmasını sağlar. 

  ![Güvenlik duvarı ve sanal ağ özel durumları](media/data-lake-store-network-security/firewall-exceptions.png)

Bu özel durumları kapalı tutmanızı öneririz. Bunları ancak sanal ağınızdaki diğer hizmetlerden bağlantı kurulmasını istiyorsanız açmanız önerilir.