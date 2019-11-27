---
title: Sunucular için Azure Arc genel bakış
description: Altyapı ve uygulamaların yaşam döngüsünü otomatikleştirmek için sunucular için Azure Arc 'ı nasıl kullanacağınızı öğrenin.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: bobbytreed
ms.author: robreed
keywords: Azure Otomasyonu, DSC, PowerShell, istenen durum yapılandırması, güncelleştirme yönetimi, değişiklik izleme, envanter, runbook 'lar, Python, grafik, karma
ms.date: 11/04/2019
ms.custom: mvc
ms.topic: overview
ms.openlocfilehash: 7a2e9d39629e4fdb349652c9c48d0084d051f9f8
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74122846"
---
# <a name="what-is-azure-arc-for-servers"></a>Sunucular için Azure Arc nedir?

Sunucular için Azure Arc, Azure dışındaki makineleri yönetmenizi sağlar.
Azure olmayan bir makine Azure'a bağlandığında **Bağlı Makine** olur ve Azure'daki bir kaynak olarak kabul edilir. Her **Bağlı Makine** bir Kaynak Kimliğine sahiptir, bir abonelikteki Kaynak Grubunun parçası olarak yönetilir, Azure İlkesi ve etiketleme gibi standart Azure yapılarından faydalanır.

Her makineye Azure 'a bağlanmak için bir aracı paketinin yüklü olması gerekir. Bu belgenin geri kalanında işlem daha ayrıntılı olarak açıklanmaktadır.

Makineler, son zamanlarda aracının ne zaman iade edilme durumuna göre **bağlı** veya **bağlantısı kesilmiş** olur. Her iade etme işlemi sinyal olarak adlandırılır. Bir makine son 5 dakika içinde iade edilmedi ise, bağlantı geri yüklenene kadar çevrimdışı olarak görünür.  <!-- For more information on troubleshooting agent connectivity, see [Troubleshooting Azure Arc for servers](troubleshoot/arc-for-servers.md). -->

![Bağlı sunucular](./media/overview/arc-for-servers-onboarded-servers.png)

## <a name="clients"></a>İstemciler

### <a name="supported-operating-systems"></a>Desteklenen İşletim Sistemleri

Genel önizlemede şunları destekliyoruz:

- Windows Server 2012 R2 ve üzeri
- Ubuntu 16,04 ve 18,04

Genel önizleme sürümü, değerlendirme amacıyla tasarlanmıştır ve kritik üretim kaynaklarını yönetmek için kullanılmamalıdır.

## <a name="azure-subscription-and-service-limits"></a>Azure aboneliği ve hizmet limitleri

Lütfen Azure Resource Manager sınırlarını okuduğunuzdan emin olun ve [abonelik](../../azure-subscription-service-limits.md#subscription-limits---azure-resource-manager)için listelenen yönergelere ve [kaynak gruplarına](../../azure-subscription-service-limits.md#resource-group-limits)göre bağlanacak makinelerin sayısını planlayın. Özellikle, varsayılan olarak, kaynak grubu başına 800 sunucu sınırı vardır.

## <a name="networking-configuration"></a>Ağ yapılandırması

Yükleme ve çalışma zamanı sırasında, aracı **Azure Arc hizmet uç noktalarına**bağlantı gerektirir. Giden bağlantı güvenlik duvarları tarafından engellenirse, aşağıdaki URL 'Lerin varsayılan olarak engellenmediğinden emin olun. Tüm bağlantılar aracıdan Azure 'a giden ve **SSL**ile güvenli hale getirilir. Tüm trafik bir **https** proxy 'si aracılığıyla yönlendirilebilir. Sunucularının bağlanmasına izin verilen IP aralıklarına veya etki alanı adlarına izin verirseniz, aşağıdaki hizmet etiketlerine ve DNS adlarına bağlantı noktası 443 erişimine izin vermeniz gerekir.

Hizmet Etiketleri:

* AzureActiveDirectory
* AzureTrafficManager

Her bir hizmet etiketi/bölgesinin IP adresleri listesi için bkz. JSON dosyası- [Azure IP aralıkları ve hizmet etiketleri – genel bulut](https://www.microsoft.com/download/details.aspx?id=56519). Microsoft, her bir Azure hizmetini ve kullandığı IP aralıklarını içeren haftalık güncelleştirmeler yayımlar. Daha fazla ayrıntı için bkz. [hizmet etiketleri](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags).

Hizmetlerin çoğunluğu Şu anda bir hizmet etiketi kaydına sahip olmadığından ve bu nedenle IP 'Ler değişikliğe tabi olduğundan, bu DNS adları hizmet etiketi IP aralığı bilgilerine ek olarak sağlanır. Güvenlik Duvarı yapılandırmanız için IP aralıkları gerekliyse, tüm Azure hizmetlerine erişime izin vermek için **Azurecyüksek** hizmet etiketinin kullanılması gerekir. Bu URL 'Lerin güvenlik izlemesini veya denetimini devre dışı bırakmayın, ancak diğer internet trafiğinden yaptığınız gibi bunlara izin verin.

| Etki Alanı Ortamı | Gerekli Azure hizmet uç noktaları |
|---------|---------|
|management.azure.com|Azure Resource Manager|
|login.windows.net|Azure Active Directory|
|dc.services.visualstudio.com|Application Insights|
|agentserviceapi.azure-automation.net|Konuk Yapılandırması|
|*-agentservice-prod-1.azure-automation.net|Konuk Yapılandırması|
|*. his.hybridcompute.azure-automation.net|Karma kimlik hizmeti|

### <a name="installation-network-requirements"></a>Yükleme ağı gereksinimleri

[Azure bağlı makine Aracısı paketini](https://aka.ms/AzureConnectedMachineAgent) resmi dağıtım sunucularımızdan indirin aşağıdaki sitelerin ortamınızdan erişilebilir olması gerekir. Paketi bir dosya paylaşımında indirmeyi ve aracının oradan yüklenmesini seçebilirsiniz. Bu durumda, Azure portal oluşturulan ekleme betiğinin değiştirilmesi gerekebilir.

Windows:

* `aka.ms`
* `download.microsoft.com`

Linux:

* `aka.ms`
* `packages.microsoft.com`

Aracıyı proxy 'nizi kullanacak şekilde yapılandırma hakkında bilgi için, bkz. [proxy sunucu yapılandırması](quickstart-onboard-powershell.md#proxy-server-configuration).

## <a name="register-the-required-resource-providers"></a>Gerekli kaynak sağlayıcılarını Kaydet

Sunucular için Azure Arc kullanabilmek için gerekli kaynak sağlayıcılarını kaydetmeniz gerekir.

* **Microsoft. HybridCompute**
* **Microsoft. GuestConfiguration**

Aşağıdaki komutlarla kaynak sağlayıcılarını kaydedebilirsiniz:

Azure PowerShell:

```azurepowershell-interactive
Login-AzAccount
Set-AzContext -SubscriptionId [subscription you want to onboard]
Register-AzResourceProvider -ProviderNamespace Microsoft.HybridCompute
Register-AzResourceProvider -ProviderNamespace Microsoft.GuestConfiguration
```

Azure CLı:

```azurecli-interactive
az account set --subscription "{Your Subscription Name}"
az provider register --namespace 'Microsoft.HybridCompute'
az provider register --namespace 'Microsoft.GuestConfiguration'
```

Ayrıca, [Azure Portal](../../azure-resource-manager/resource-manager-supported-services.md#azure-portal)altındaki adımları izleyerek Portal 'ı kullanarak kaynak sağlayıcılarını kaydedebilirsiniz.

## <a name="supported-scenarios"></a>Desteklenen senaryolar

Bir düğümü kaydettikten sonra, diğer Azure hizmetlerini kullanarak düğümlerinizi yönetmeye başlayabilirsiniz.

Genel önizlemede, **bağlı makinelerde**aşağıdaki senaryolar desteklenir.

## <a name="guest-configuration"></a>Konuk Yapılandırması

Makineyi Azure 'a bağladıktan sonra, Azure sanal makinelerine ilke atamayla aynı deneyimi kullanarak **bağlı makinelere** Azure ilkeleri atayabilirsiniz.

Daha fazla bilgi için bkz. [Azure Ilkesinin Konuk yapılandırmasını anlama](../../governance/policy/concepts/guest-configuration.md).

**Bağlı bir makineye** yönelik Konuk yapılandırma Aracısı günlükleri aşağıdaki konumlarda bulunur:

* Windows - `%ProgramFiles%\AzureConnectedMachineAgent\logs\dsc.log`
* Linux:-`/opt/logs/dsc.log`

## <a name="log-analytics"></a>Log Analytics

[Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview) tarafından toplanan ve Log Analytics çalışma alanında depolanan günlük verileri artık, kaynak merkezli günlük erişimi Için kullanılabilecek **RESOURCEID**gibi makineye özgü özellikler içerir.

- Zaten MMA aracısının yüklü olduğu makinelerde, güncelleştirilmiş yönetim paketleri aracılığıyla **Azure Arc** işlevselliği etkinleştirilir.
- Sunucu tümleştirmesi için Azure Arc için [MMA Aracısı sürüm 10.20.18011 veya üzeri](https://docs.microsoft.com/azure/virtual-machines/extensions/oms-windows#agent-and-vm-extension-version) gereklidir.
- [Azure izleyici](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview)'de günlük verileri sorgulanırken, döndürülen veri şeması `/subscriptions/<SubscriptionId/resourceGroups/<ResourceGroup>/providers/Microsoft.HybridCompute/machines/<MachineName>`formundaki karma **RESOURCEID** 'yi içerir.

Daha fazla bilgi için bkz. [Azure izleyici 'de Log Analytics kullanmaya başlama](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal).

<!-- MMA agent version 10.20.18011 and later -->

## <a name="next-steps"></a>Sonraki Adımlar

Sunucular için Azure Arc kullanarak makineleri bağlamak için iki yöntem vardır.

* **Etkileşimli** -portalda bir betik oluşturup makinede yürütmek Için [Portal Hızlı](quickstart-onboard-portal.md) başlangıcını izleyin. Tek seferde bir makine bağlıyorsanız, bu en iyi seçenektir.
* **Ölçekte** , makineleri etkileşimli olmayan bir şekilde bağlamaya yönelik bir hizmet sorumlusu oluşturmak Için [PowerShell hızlı](quickstart-onboard-powershell.md) başlangıcını izleyin.
