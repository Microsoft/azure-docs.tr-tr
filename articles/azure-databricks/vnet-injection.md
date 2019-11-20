---
title: Sanal ağınızda Azure Databricks'i dağıtma
description: Bu makalede VNet ekleme olarak da bilinen sanal ağınıza Azure Databricks dağıtma açıklanmaktadır.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: conceptual
ms.date: 10/10/2019
ms.openlocfilehash: 3894904575d545aed0dbfce470247afb145b7590
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/16/2019
ms.locfileid: "74129296"
---
# <a name="deploy-azure-databricks-in-your-virtual-network"></a>Sanal ağınızda Azure Databricks'i dağıtma

Azure Databricks varsayılan dağıtımı, Azure üzerinde tam olarak yönetilen bir hizmettir: bir sanal ağ (VNet) dahil tüm veri düzlemi kaynakları, kilitli bir kaynak grubuna dağıtılır. Ancak ağ özelleştirmeye ihtiyacınız varsa, kendi sanal ağınızda (VNet ekleme da denir) Azure Databricks kaynakları dağıtabilirsiniz:

* Azure Databricks diğer Azure hizmetlerine (Azure depolama gibi) hizmet uç noktalarını kullanarak daha güvenli bir şekilde bağlanın.
* Kullanıcı tanımlı yolların avantajlarından yararlanarak Azure Databricks birlikte kullanmak için şirket içi veri kaynaklarına bağlanın.
* Tüm giden trafiği incelemek ve izin verme ve reddetme kurallarına göre işlem yapmak için bir ağ sanal gerecine Azure Databricks bağlanın.
* Azure Databricks özel DNS kullanacak şekilde yapılandırın.
* Çıkış trafiği kısıtlamalarını belirtmek için ağ güvenlik grubu (NSG) kurallarını yapılandırın.
* Azure Databricks kümelerini var olan sanal ağınıza dağıtın.

Azure Databricks kaynaklarını kendi sanal ağınıza dağıtmak aynı zamanda esnek CıDR aralıklarından (sanal ağ için/16-/24 ve alt ağlar için/18-/26 arasında) yararlanmanızı sağlar.

  > [!NOTE]
  > Mevcut bir çalışma alanının sanal ağını değiştiremezsiniz. Geçerli çalışma alanınız gereken sayıda etkin küme düğümüne sığmazsa, daha büyük bir sanal ağda başka bir çalışma alanı oluşturun. Kaynakları (Not defterleri, küme yapılandırması, işler) eskileri yeni çalışma alanına kopyalamak için [Bu ayrıntılı geçiş adımlarını](howto-regional-disaster-recovery.md#detailed-migration-steps) izleyin.

## <a name="virtual-network-requirements"></a>Sanal ağ gereksinimleri

Mevcut bir sanal ağı gerekli alt ağlar, ağ güvenlik grubu ve beyaz listeye ekleme ayarlarıyla otomatik olarak yapılandırmak için Azure portal Azure Databricks çalışma alanı dağıtım arabirimini kullanabilirsiniz veya Azure Resource Manager kullanabilirsiniz Sanal ağınızı yapılandırmak ve çalışma alanınızı dağıtmak için şablonlar.

Azure Databricks çalışma alanınızı dağıttığınız sanal ağın aşağıdaki gereksinimleri karşılaması gerekir:

### <a name="location"></a>Konum

Sanal ağın Azure Databricks çalışma alanıyla aynı konumda bulunması gerekir.

### <a name="subnets"></a>Alt ağlar

Sanal ağ Azure Databricks için ayrılmış iki alt ağ içermelidir:

   1. Küme iç iletişimine izin veren yapılandırılmış bir ağ güvenlik grubu olan özel bir alt ağ

   2. Azure Databricks denetim düzlemi ile iletişime izin veren yapılandırılmış bir ağ güvenlik grubu olan ortak bir alt ağ.

### <a name="address-space"></a>Adres alanı

Sanal ağ için/16-/24 arasında bir CıDR bloğu ve özel ve genel alt ağlar için/18-/26 arasında bir CıDR bloğu.

### <a name="whitelisting"></a>Güvenilenler listesine eklenme

Alt ağlar ve Azure Databricks denetim düzlemi arasındaki tüm giden ve gelen trafik, beyaz listeye eklenmelidir.

## <a name="create-an-azure-databricks-workspace"></a>Azure Databricks çalışma alanı oluşturma

Bu bölümde, Azure portal bir Azure Databricks çalışma alanının nasıl oluşturulduğu ve var olan sanal ağınızda nasıl dağıtılacağı açıklanmaktadır. Azure Databricks sanal ağı, sizin tarafınızdan sunulan CıDR aralıklarını kullanarak iki yeni alt ağ ve ağ güvenlik grubu ile güncelleştirir, gelen ve giden alt ağ trafiğini beyaz listeler ve çalışma alanını güncelleştirilmiş sanal ağa dağıtır.

## <a name="prerequisites"></a>Önkoşullar

Azure Databricks çalışma alanını dağıtacaksınız bir sanal ağınız olmalıdır. Var olan bir sanal ağı kullanabilir veya yeni bir tane oluşturabilirsiniz ancak sanal ağın, oluşturmayı planladığınız Azure Databricks çalışma alanıyla aynı bölgede olması gerekir. Sanal ağ için/16-/24 arasında bir CıDR aralığı gereklidir.

  > [!Warning]
  > Daha küçük bir sanal ağa sahip bir çalışma alanı; diğer bir deyişle, daha düşük bir CıDR aralığı, IP adreslerinden (ağ alanı) daha büyük bir sanal ağa sahip bir çalışma alanından daha hızlı çalışmasını sağlayabilir. Örneğin,/24 sanal ağı ve/26 alt ağı olan bir çalışma alanında aynı anda en fazla 64 düğüm etkin olabilir, ancak/20 sanal ağı ve/22 alt ağı olan bir çalışma alanı en fazla 1024 düğüm barındırabilir.

  Alt ağlarınız, çalışma alanınızı yapılandırdığınızda otomatik olarak oluşturulur ve yapılandırma sırasında alt ağlar için CıDR aralığı sağlama fırsatına sahip olursunuz.

## <a name="configure-the-virtual-network"></a>Sanal ağı yapılandırma

1. Azure portal, Azure Databricks hizmeti iletişim kutusunu açmak için **> Analytics Azure Databricks > kaynak oluştur** ' u seçin.

2. Adım 2: Başlarken kılavuzundaki Azure Databricks çalışma alanı oluşturma bölümünde açıklanan yapılandırma adımlarını izleyin ve sanal ağ seçeneğinde Azure Databricks Dağıt çalışma alanını seçin.

   ![Azure Databricks hizmeti oluşturma](./media/vnet-injection/create-databricks-service.png)

3. Kullanmak istediğiniz sanal ağı seçin.

   ![Sanal ağ seçenekleri](./media/vnet-injection/select-vnet.png)

4. Azure Databricks için ayrılmış iki alt ağ için/18-/26 arasında bir blokta CıDR aralıkları sağlayın:

   * Azure Databricks denetim düzlemi ile iletişime izin veren ilişkili bir ağ güvenlik grubuyla ortak bir alt ağ oluşturulur.
   * Küme iç iletişimine izin veren ilişkili bir ağ güvenlik grubu ile özel bir alt ağ oluşturulur.

5. Azure Databricks çalışma alanını sanal ağa dağıtmak için **Oluştur** ' a tıklayın.

## <a name="advanced-resource-manager-configurations"></a>Gelişmiş Resource Manager yapılandırması

Sanal ağın yapılandırması üzerinde daha fazla denetime sahip olmak istiyorsanız (örneğin, var olan alt ağları kullanmak, var olan ağ güvenlik gruplarını kullanmak veya kendi güvenlik kurallarınızı oluşturmak istiyorsanız), aşağıdaki Azure Resource Manager şablonlarını kullanarak şunları yapabilirsiniz: Portal sanal ağ yapılandırması ve çalışma alanı dağıtımı.

### <a name="all-in-one"></a>Hepsi bir arada

Tek bir sanal ağ, ağ güvenlik grubu ve Azure Databricks çalışma alanı oluşturmak için, [Databricks VNET 'e eklenen çalışma alanları Için hepsi bir arada şablonu](https://azure.microsoft.com/resources/templates/101-databricks-all-in-one-template-for-vnet-injection/)kullanın.

Bu şablonu kullandığınızda, alt ağ trafiğinin el ile beyaz listesini almanız gerekmez.

### <a name="network-security-groups"></a>Ağ güvenlik grupları

Mevcut bir sanal ağ için gerekli kurallara sahip ağ güvenlik grupları oluşturmak için, [Databricks VNET ekleme Için ağ güvenlik grubu şablonunu](https://azure.microsoft.com/resources/templates/101-databricks-all-in-one-template-for-vnet-injection/)kullanın.

Bu şablonu kullandığınızda, alt ağ trafiğinin el ile beyaz listesini almanız gerekmez.

### <a name="virtual-network"></a>Sanal ağ

Uygun ortak ve özel alt ağlarla bir sanal ağ oluşturmak için, [Databricks VNET 'e ekleme Için sanal ağ şablonunu](https://azure.microsoft.com/resources/templates/101-databricks-vnet-for-vnet-injection)kullanın.

Bu şablonu ağ güvenlik grupları şablonunu da kullanmadan kullanırsanız, sanal ağla birlikte kullandığınız ağ güvenlik gruplarına el ile beyaz liste kuralları eklemeniz gerekir.

### <a name="azure-databricks-workspace"></a>Azure Databricks çalışma alanı

Ortak ve özel alt ağları olan ve düzgün şekilde yapılandırılmış ağ güvenlik grupları içeren var olan bir sanal ağa bir Azure Databricks çalışma alanı dağıtmak için, [Databricks VNET ekleme Için çalışma alanı şablonunu](https://azure.microsoft.com/resources/templates/101-databricks-workspace-with-vnet-injection)kullanın.

Bu şablonu ağ güvenlik grupları şablonunu da kullanmadan kullanırsanız, sanal ağla birlikte kullandığınız ağ güvenlik gruplarına el ile beyaz liste kuralları eklemeniz gerekir.

## <a name="whitelisting-subnet-traffic"></a>Alt ağ trafiğini beyaz listeleme

Ağ güvenlik gruplarınızı oluşturmak için [Azure Portal](/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject#vnet-inject-portal) veya [Azure Resource Manager şablonlarını](/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject#vnet-inject-advanced) kullanmıyorsanız, alt ağlarınıza aşağıdaki trafiği el ile eklemeniz gerekir.

|Yön|Protokol|Kaynak|Kaynak Bağlantı Noktası|Hedef|Hedef Bağlantı Noktası|
|---------|--------|------|-----------|-----------|----------------|
|Gelen|\*|VirtualNetwork|\*|\*|\*|
|Gelen|\*|Denetim düzlemi NAT IP 'si|\*|\*|22|
|Gelen|\*|Denetim düzlemi NAT IP 'si|\*|\*|5557|
|Giden|\*|\*|\*|WEBAPP IP|\*|
|Giden|\*|\*|\*|SQL (hizmet etiketi)|\*|
|Giden|\*|\*|\*|Depolama (hizmet etiketi)|\*|
|Giden|\*|\*|\*|VirtualNetwork|\*|

Aşağıdaki IP adreslerini kullanarak alt ağ trafiğini beyaz listeye ekleyin. SQL (metasme) ve depolama (yapıt ve günlük depolama) için SQL ve depolama [hizmeti etiketlerini](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags)kullanmanız gerekir.

|Azure Databricks bölgesi|Hizmet|Genel IP|
|-----------------------|-------|---------|
|Doğu ABD|Denetim düzlemi NAT </br></br>Web|23.101.152.95/32 </br></br>40.70.58.221/32|
|Doğu ABD 2|Denetim düzlemi NAT </br></br>Web|23.101.152.95/32 </br></br>40.70.58.221/32|
|Orta Kuzey ABD|Denetim düzlemi NAT </br></br>Web|23.101.152.95/32 </br></br>40.70.58.221/32|
|Orta ABD|Denetim düzlemi NAT </br></br>Web|23.101.152.95/32 </br></br>40.70.58.221/32|
|Orta Güney ABD|Denetim düzlemi NAT </br></br>Web|40.83.178.242/32 </br></br>40.118.174.12/32|
|Batı ABD|Denetim düzlemi NAT </br></br>Web|40.83.178.242/32 </br></br>40.118.174.12/32|
|Batı ABD 2|Denetim düzlemi NAT </br></br>Web|40.83.178.242/32 </br></br>40.118.174.12/32|
|Orta Kanada|Denetim düzlemi NAT </br></br>Web|40.85.223.25/32 </br></br>13.71.184.74/32|
|Doğu Kanada|Denetim düzlemi NAT </br></br>Web|40.85.223.25/32 </br></br>13.71.184.74/32|
|Birleşik Krallık Batı|Denetim düzlemi NAT </br></br>Web|51.140.203.27/32 </br></br>51.140.204.4/32|
|Birleşik Krallık Güney|Denetim düzlemi NAT </br></br>Web|51.140.203.27/32 </br></br>51.140.204.4/32|
|Batı Avrupa|Denetim düzlemi NAT </br></br>Web|23.100.0.135/32 </br></br>52.232.19.246/32|
|Kuzey Avrupa|Denetim düzlemi NAT </br></br>Web|23.100.0.135/32 </br></br>52.232.19.246/32|
|Orta Hindistan|Denetim düzlemi NAT </br></br>Web|104.211.89.81/32 </br></br>104.211.101.14/32|
|Güney Hindistan|Denetim düzlemi NAT </br></br>Web|104.211.89.81/32 </br></br>104.211.101.14/32|
|Batı Hindistan|Denetim düzlemi NAT </br></br>Web|104.211.89.81/32 </br></br>104.211.101.14/32|
|Güneydoğu Asya|Denetim düzlemi NAT </br></br>Web|52.187.0.85/32 </br></br>52.187.145.107/32|
|Doğu Asya|Denetim düzlemi NAT </br></br>Web|52.187.0.85/32 </br></br>52.187.145.107/32|
|Avustralya Doğu|Denetim düzlemi NAT </br></br>Web|13.70.105.50/32 </br></br>13.75.218.172/32|
|Avustralya Güneydoğu|Denetim düzlemi NAT </br></br>Web|13.70.105.50/32 </br></br>13.75.218.172/32|
|Avustralya Orta|Denetim düzlemi NAT </br></br>Web|13.70.105.50/32 </br></br>13.75.218.172/32|
|Avustralya Orta 2|Denetim düzlemi NAT </br></br>Web|13.70.105.50/32 </br></br>13.75.218.172/32|
|Japonya Doğu|Denetim düzlemi NAT </br></br>Web|13.78.19.235/32 </br></br>52.246.160.72/32|
|Japonya Batı|Denetim düzlemi NAT </br></br>Web|13.78.19.235/32 </br></br>52.246.160.72/32|

## <a name="troubleshooting"></a>Sorun giderme

### <a name="workspace-launch-errors"></a>Çalışma alanı başlatma hataları

Özel bir sanal ağda çalışma alanı başlatmak Azure Databricks oturum açma ekranında şu hata ile başarısız oluyor: **"çalışma alanınızı oluştururken bir hata ile karşılaştık. Özel ağ yapılandırmasının doğru olduğundan emin olun ve yeniden deneyin. "**

Bu hata, bir ağ yapılandırmasının toplantı gereksinimleri olmaması nedeniyle oluşur. Çalışma alanını oluştururken bu konudaki yönergeleri izlediğinizden emin olun.

### <a name="cluster-creation-errors"></a>Küme oluşturma hataları

**Erişilemeyen örneklere: kaynaklara SSH aracılığıyla ulaşılamadı.**

Olası neden: denetim düzleminin çalışanlara giden trafik engellenir. Gelen güvenlik kurallarının gereksinimleri karşıladığından emin olarak düzeltme. Şirket içi ağınıza bağlı mevcut bir sanal ağa dağıtıyorsanız, Azure Databricks çalışma alanınızı şirket Içi ağınıza bağlama bölümünde verilen bilgileri kullanarak kurulumunuzu gözden geçirin.

**Beklenmeyen başlatma hatası: küme ayarlanırken beklenmeyen bir hatayla karşılaşıldı. Yeniden deneyin ve sorun devam ederse Azure Databricks başvurun. İç hata iletisi: düğüm yerleştirilirken zaman aşımı oluştu.**

Olası neden: çalışanların Azure depolama uç noktalarına giden trafiği engellenir. Giden güvenlik kurallarının gereksinimleri karşıladığından emin olarak düzeltme. Özel DNS sunucuları kullanıyorsanız, sanal ağınızdaki DNS sunucularının durumunu da denetleyin.

**Bulut sağlayıcısı başlatma hatası: küme ayarlanırken bir bulut sağlayıcısı hatasıyla karşılaşıldı. Daha fazla bilgi için Azure Databricks kılavuzuna bakın. Azure hata kodu: AuthorizationFailed/InvalidResourceReference.**

Olası neden: sanal ağ veya alt ağlar artık mevcut değil. Sanal ağın ve alt ağların mevcut olduğundan emin olun.

**Küme sonlandırıldı. Neden: Spark başlatma hatası: Spark zamanında başlatılamadı. Bu sorun, hatalı çalışan Hive meta veri deposu, geçersiz Spark yapılandırmalarının veya hatalı başlatma betikleri nedeniyle oluşabilir. Bu sorunu gidermek için Spark sürücü günlüklerine bakın ve sorun devam ederse Databricks ile iletişim kurun. İç hata iletisi: Spark başlatılamadı: sürücü zamanında başlatılamadı.**

Olası neden: kapsayıcı, barındırma örneği veya DBFS depolama hesabıyla konuşamaz. Bir sonraki atlamada DBFS depolama hesabı için alt ağlara özel bir yol ekleyerek bu çözümü yapın.

### <a name="notebook-command-errors"></a>Not defteri komut hataları

**Komut yanıt vermiyor**

Olası neden: çalışandan çalışana iletişim engellendi. Gelen güvenlik kurallarının gereksinimleri karşıladığından emin olarak düzeltme.

**Not defteri iş akışı şu özel durumla başarısız oluyor: com. databricks. WorkflowException: org. Apache. http. conn. ConnectTimeoutException**

Olası neden: çalışanların Azure Databricks WebApp 'ye giden trafik engellenir. Giden güvenlik kurallarının gereksinimleri karşıladığından emin olarak düzeltme.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Azure Databricks kullanarak verileri ayıklama, dönüştürme ve yükleme](databricks-extract-load-sql-data-warehouse.md)
