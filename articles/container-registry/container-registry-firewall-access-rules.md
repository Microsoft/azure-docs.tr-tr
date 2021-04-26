---
title: Güvenlik Duvarı erişim kuralları
description: REST API ve veri uç noktası etki alanı adlarına veya hizmete özel IP adresi aralıklarına erişime izin vererek bir güvenlik duvarının arkasındaki Azure Container Registry 'ye erişmek için kuralları yapılandırın.
ms.topic: article
ms.date: 05/18/2020
ms.openlocfilehash: 6aea4415468eb21e8d010b74597fc68e4ebf573f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783944"
---
# <a name="configure-rules-to-access-an-azure-container-registry-behind-a-firewall"></a>Güvenlik duvarının arkasındaki bir Azure Container Registry 'ye erişmek için kuralları yapılandırma

Bu makalede, Azure Container Registry 'ye erişime izin vermek için güvenlik duvarınızdaki kuralların nasıl yapılandırılacağı açıklanır. Örneğin, bir güvenlik duvarı veya proxy sunucusu arkasındaki bir Azure IoT Edge cihazın bir kapsayıcı görüntüsünü çekmek için bir kapsayıcı kayıt defterine erişmesi gerekebilir. Ya da, bir şirket içi ağdaki kilitlenmiş bir sunucunun bir görüntüyü göndermek için erişmesi gerekebilir.

Bunun yerine yalnızca bir Azure sanal ağı içindeki bir kapsayıcı kayıt defterine gelen ağ erişimini yapılandırmak istiyorsanız bkz. Azure [Container kayıt defteri Için Azure özel bağlantısını yapılandırma](container-registry-private-link.md).

## <a name="about-registry-endpoints"></a>Kayıt defteri uç noktaları hakkında

Bir Azure Container Registry 'ye görüntü veya diğer yapıtları çekmek veya göndermek için, Docker Daemon gibi bir istemcinin iki ayrı uç nokta ile HTTPS üzerinden etkileşimde bulunması gerekir. Bir güvenlik duvarının arkasındaki bir kayıt defterine erişen istemciler için, her iki uç nokta için de erişim kuralları yapılandırmanız gerekir. 443 numaralı bağlantı noktası üzerinden her iki uç noktaya de ulaşıldı.

* **Kayıt defteri REST API uç noktası** -kimlik doğrulama ve kayıt defteri yönetim işlemleri kayıt defterinin ortak REST API uç noktası aracılığıyla işlenir. Bu uç nokta, kayıt defterinin oturum açma sunucusu adıdır. Örnek: `myregistry.azurecr.io`

* **Depolama (veri) uç noktası** -Azure, kapsayıcı görüntüleri ve diğer yapıtlar için verileri yönetmek üzere her kayıt defteri adına Azure Storage hesaplarında [BLOB depolamayı ayırır](container-registry-storage.md) . Bir istemci bir Azure Container Registry içindeki görüntü katmanlarına eriştiğinde, kayıt defteri tarafından sağlanmış bir depolama hesabı uç noktası kullanarak istek yapar.

Kayıt defteriniz coğrafi olarak [çoğaltılırsa](container-registry-geo-replication.md), bir istemcinin belirli bir bölgedeki veya birden çok çoğaltılan bölgedeki veri uç noktasıyla etkileşmesi gerekebilir.

## <a name="allow-access-to-rest-and-data-endpoints"></a>REST ve veri uç noktalarına erişime izin ver

* **REST uç noktası** -tam kayıt defteri oturum açma sunucusu adına `<registry-name>.azurecr.io` veya ilişkili bir IP adresi aralığına erişime izin ver
* **Depolama (veri) uç noktası** -joker karakter `*.blob.core.windows.net` veya ilişkili bir IP adresi aralığı kullanılarak tüm Azure Blob depolama hesaplarına erişime izin verin.
> [!NOTE]
> Azure Container Registry, [özel veri uç noktaları](#enable-dedicated-data-endpoints)sunarak, kayıt defteri depolarınız için istemci güvenlik duvarı kurallarını sıkı şekilde kapsamlarınıza olanak sağlar. İsteğe bağlı olarak, form kullanılarak kayıt defterinin bulunduğu ya da çoğaltılan tüm bölgelerde veri uç noktalarını etkinleştirin `<registry-name>.<region>.data.azurecr.io` .

## <a name="allow-access-by-ip-address-range"></a>IP adresi aralığına göre erişime izin ver

Kuruluşunuzun yalnızca belirli IP adreslerine veya adres aralıklarına erişime izin vermek için ilkeler varsa, [Azure IP aralıklarını ve hizmet etiketleri – genel bulut](https://www.microsoft.com/download/details.aspx?id=56519)' u indirin.

Erişime izin vermeniz gereken ACR REST uç nokta IP aralıklarını bulmak için JSON dosyasında **AzureContainerRegistry** aratın.

> [!IMPORTANT]
> Azure hizmetleri için IP adresi aralıkları değişebilir ve güncelleştirmeler haftalık olarak yayımlanır. JSON dosyasını düzenli olarak indirin ve erişim kurallarınızın gerekli güncelleştirmelerini yapın. Senaryonuz bir Azure sanal ağında ağ güvenlik grubu kurallarını yapılandırmayı içeriyorsa veya Azure Güvenlik Duvarı 'nı kullanıyorsanız, bunun yerine **AzureContainerRegistry** [Service etiketini](#allow-access-by-service-tag) kullanın.
>

### <a name="rest-ip-addresses-for-all-regions"></a>Tüm bölgeler için REST IP adresleri

```json
{
  "name": "AzureContainerRegistry",
  "id": "AzureContainerRegistry",
  "properties": {
    "changeNumber": 10,
    "region": "",
    "platform": "Azure",
    "systemService": "AzureContainerRegistry",
    "addressPrefixes": [
      "13.66.140.72/29",
    [...]
```

### <a name="rest-ip-addresses-for-a-specific-region"></a>Belirli bir bölge için REST IP adresleri

**AzureContainerRegistry. AustraliaEast** gibi belirli bir bölgeyi arayın.

```json
{
  "name": "AzureContainerRegistry.AustraliaEast",
  "id": "AzureContainerRegistry.AustraliaEast",
  "properties": {
    "changeNumber": 1,
    "region": "australiaeast",
    "platform": "Azure",
    "systemService": "AzureContainerRegistry",
    "addressPrefixes": [
      "13.70.72.136/29",
    [...]
```

### <a name="storage-ip-addresses-for-all-regions"></a>Tüm bölgeler için depolama IP adresleri

```json
{
  "name": "Storage",
  "id": "Storage",
  "properties": {
    "changeNumber": 19,
    "region": "",
    "platform": "Azure",
    "systemService": "AzureStorage",
    "addressPrefixes": [
      "13.65.107.32/28",
    [...]
```

### <a name="storage-ip-addresses-for-specific-regions"></a>Belirli bölgeler için depolama IP adresleri

**Storage. AustraliaCentral** gibi belirli bir bölgeyi arayın.

```json
{
  "name": "Storage.AustraliaCentral",
  "id": "Storage.AustraliaCentral",
  "properties": {
    "changeNumber": 1,
    "region": "australiacentral",
    "platform": "Azure",
    "systemService": "AzureStorage",
    "addressPrefixes": [
      "52.239.216.0/23"
    [...]
```

## <a name="allow-access-by-service-tag"></a>Hizmet etiketine göre erişime izin ver

Bir Azure sanal ağında, sanal makine gibi bir kaynaktan bir kapsayıcı kayıt defterine giden trafiği filtrelemek için ağ güvenlik kurallarını kullanın. Azure ağ kurallarının oluşturulmasını basitleştirmek için **AzureContainerRegistry** [Service etiketini](../virtual-network/network-security-groups-overview.md#service-tags)kullanın. Hizmet etiketi, bir Azure hizmetine küresel olarak veya Azure bölgesine erişmek için bir IP adresi ön eki grubunu temsil eder. Adresler değiştiğinde etiket otomatik olarak güncelleştirilir. 

Örneğin, Azure Container Registry 'ye giden trafiğe izin vermek için hedef **AzureContainerRegistry** ile giden ağ güvenlik grubu kuralı oluşturun. Yalnızca belirli bir bölgedeki hizmet etiketine erişime izin vermek için, bölgeyi şu biçimde belirtin: **AzureContainerRegistry**. [*bölge adı*].

## <a name="enable-dedicated-data-endpoints"></a>Adanmış veri uç noktalarını etkinleştir 

> [!WARNING]
> Önceden var olan uç noktalara istemci güvenlik duvarı erişimi yapılandırdıysanız `*.blob.core.windows.net` , adanmış veri uç noktalarına geçiş, istemci bağlantısını etkiler ve çekme hatalarıyla başarısız olur. İstemcilerin tutarlı erişimi olduğundan emin olmak için, yeni veri uç noktası kurallarını istemci güvenlik duvarı kurallarına ekleyin. Tamamlandıktan sonra, Azure CLı veya diğer araçları kullanarak kayıt defterlerine yönelik adanmış veri uç noktalarını etkinleştirin.

Adanmış veri uç noktaları, **Premium** kapsayıcı kayıt defteri hizmet katmanının isteğe bağlı bir özelliğidir. Kayıt defteri hizmeti katmanları ve limitleri hakkında bilgi için bkz. [Azure Container Registry hizmet katmanları](container-registry-skus.md). 

Azure portal veya Azure CLı kullanarak adanmış veri uç noktalarını etkinleştirebilirsiniz. Veri uç noktaları bölgesel bir düzene sahiptir `<registry-name>.<region>.data.azurecr.io` . Coğrafi olarak çoğaltılan bir kayıt defterinde, veri uç noktalarının etkinleştirilmesi tüm çoğaltma bölgelerinde uç noktalar etkinleştirir.

### <a name="portal"></a>Portal

Portalı kullanarak veri uç noktalarını etkinleştirmek için:

1. Kapsayıcı Kayıt defterinize gidin.
1. **Ağ**  >  **genel erişimini** seçin.
1. **Adanmış veri uç noktası etkinleştir** onay kutusunu seçin.
1. **Kaydet**’i seçin.

Veri uç noktası veya uç noktalar portalda görüntülenir.

:::image type="content" source="media/container-registry-firewall-access-rules/dedicated-data-endpoints-portal.png" alt-text="Portalda adanmış veri uç noktaları":::

### <a name="azure-cli"></a>Azure CLI

Azure CLı kullanarak veri uç noktalarını etkinleştirmek için Azure CLı sürüm 2.4.0 veya üstünü kullanın. Yüklemeniz veya yükseltmeniz gerekirse, bkz. [Azure CLI yükleme](/cli/azure/install-azure-cli).

Aşağıdaki [az ACR Update][az-acr-update] komutu, kayıt defteri *myregistry* üzerinde adanmış veri uç noktalarını etkin bir şekilde sunar. 

```azurecli
az acr update --name myregistry --data-endpoint-enabled
```

Veri uç noktalarını görüntülemek için [az ACR Show-endpoints][az-acr-show-endpoints] komutunu kullanın:

```azurecli
az acr show-endpoints --name myregistry
```

Tanıtım amacıyla çıkış, iki bölgesel bitiş noktası gösterir

```
{
    "loginServer": "myregistry.azurecr.io",
    "dataEndpoints": [
        {
            "region": "eastus",
            "endpoint": "myregistry.eastus.data.azurecr.io",
        },
        {
            "region": "westus",
            "endpoint": "myregistry.westus.data.azurecr.io",
        }
    ]
}
```

Kayıt defteriniz için adanmış veri uç noktalarını ayarladıktan sonra, veri uç noktaları için istemci güvenlik duvarı erişim kurallarını etkinleştirebilirsiniz. Tüm gerekli kayıt defteri bölgeleri için veri uç noktası erişim kurallarını etkinleştirin.

## <a name="configure-client-firewall-rules-for-mcr"></a>MCR için istemci güvenlik duvarı kurallarını yapılandırma

Bir güvenlik duvarının arkasındaki Microsoft Container Registry (MCR) öğesine erişmeniz gerekiyorsa, [MCR istemci güvenlik duvarı kurallarını](https://github.com/microsoft/containerregistry/blob/master/client-firewall-rules.md)Yapılandırma Kılavuzu ' na bakın. MCR, Windows Server görüntüleri gibi tüm Microsoft tarafından yayınlanan Docker görüntülerinin birincil kayıt defteridir.

## <a name="next-steps"></a>Sonraki adımlar

* [Ağ güvenliği için En Iyi Azure uygulamaları](../security/fundamentals/network-best-practices.md) hakkında bilgi edinin

* Azure sanal ağındaki [güvenlik grupları](../virtual-network/network-security-groups-overview.md) hakkında daha fazla bilgi edinin

* Bir kapsayıcı kayıt defteri için [özel bağlantı](container-registry-private-link.md) ayarlama hakkında daha fazla bilgi edinin

* Azure Container Registry için [adanmış veri uç noktaları](https://azure.microsoft.com/blog/azure-container-registry-mitigating-data-exfiltration-with-dedicated-data-endpoints/) hakkında daha fazla bilgi edinin



<!-- IMAGES -->

<!-- LINKS - External -->

<!-- LINKS - Internal -->

[az-acr-update]: /cli/azure/acr#az_acr_update
[az-acr-show-endpoints]: /cli/azure/acr#az_acr_show_endpoints