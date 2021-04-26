---
title: Bir Azure sanal ağında Cloud Shell
description: Azure sanal ağına Cloud Shell dağıtma
services: Azure
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/15/2020
ms.author: damaerte
ms.openlocfilehash: 58f6c7a3b5d68d2825cead545ba1b683d1faf1af
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98222811"
---
# <a name="deploy-cloud-shell-into-an-azure-virtual-network"></a>Azure sanal ağına Cloud Shell dağıtma

Düzenli bir Cloud Shell oturumu, Microsoft ağındaki bir kapsayıcıda kaynaklarınızdan ayrı çalışır. Bu, kapsayıcının içinde çalışan komutların yalnızca belirli bir sanal ağdan erişilebilen kaynaklara erişemeyeceği anlamına gelir. Örneğin, Cloud Shell 'dan yalnızca özel bir IP adresine sahip bir sanal makineye bağlanmak için SSH kullanamaz veya bir kubectl kullanarak erişimi kilitleyen bir Kubernetes kümesine bağlanabilirsiniz. 

Bu isteğe bağlı özellik bu sınırlamalara yöneliktir ve Cloud Shell, denetlediğiniz bir Azure sanal ağına dağıtmanıza olanak tanır. Buradan kapsayıcı, seçtiğiniz sanal ağ içindeki kaynaklarla etkileşim kurabilir.  

Aşağıda, bu senaryoda dağıtılacak ve kullanılacak kaynak mimarisini görebilirsiniz.

![Yalıtılmış VNET mimarisini Cloud Shell gösterir.](media/private-vnet/data-diagram.png)

Azure sanal ağınızda Cloud Shell kullanabilmeniz için, bu işlevselliği desteklemek üzere birkaç kaynak oluşturmanız gerekir. Bu makalede, bir ARM şablonu kullanarak gerekli kaynakların nasıl ayarlanacağı gösterilmektedir.

> [!NOTE]
> Bu kaynakların, sanal ağ için yalnızca bir kez ayarlanması gerekir. Daha sonra sanal ağa erişimi olan tüm yöneticiler tarafından paylaşılabilir.

## <a name="required-network-resources"></a>Gerekli ağ kaynakları

### <a name="virtual-network"></a>Sanal ağ
Bir sanal ağ, bir veya daha fazla alt ağın oluşturulduğu adres alanını tanımlar.

Cloud Shell için kullanılacak istenen sanal ağın tanımlanması gerekir. Bu, genellikle yönetmek istediğiniz kaynakları içeren var olan bir sanal ağ veya kaynaklarınızı içeren ağlarla eş bir ağ olur.

### <a name="subnet"></a>Alt ağ
Seçilen sanal ağ içinde, Cloud Shell kapsayıcılar için adanmış bir alt ağ kullanılmalıdır. Bu alt ağ Azure Container Instances (ACI) hizmeti için temsilci olarak oluşturulur.  Bir Kullanıcı bir sanal ağda Cloud Shell kapsayıcısı istediğinde, Cloud Shell bu Temsilcili alt ağdaki bir kapsayıcıyı oluşturmak için ACI 'yi kullanır.  Bu alt ağda başka kaynak oluşturulamaz.

### <a name="network-profile"></a>Ağ profili
Ağ profili, Azure kaynakları için kaynak için belirli ağ özelliklerini belirten bir ağ yapılandırması şablonudur.

### <a name="azure-relay"></a>Azure Geçişi
[Azure Relay](../azure-relay/relay-what-is-it.md) , iletişim kuracak şekilde doğrudan erişilebilen iki uç noktaya izin verir. Bu durumda, yöneticinin tarayıcısının özel ağdaki kapsayıcıya iletişim kurmasına izin vermek için kullanılır.

Cloud Shell için kullanılan Azure Relay örneği, hangi ağların kapsayıcı kaynaklarına erişebileceğini denetlemek için yapılandırılabilir: 
- Genel internet 'ten erişilebilir: Bu yapılandırmada, Cloud Shell dışında iç kaynaklara ulaşmak için bir yol sağlar. 
- Belirtilen ağlardan erişilebilir: Bu yapılandırmada, yöneticilerin Cloud Shell kullanabilmek için uygun ağda çalışan bir bilgisayardan Azure portal erişmesi gerekir.

## <a name="storage-requirements"></a>Depolama gereksinimleri
Standart Cloud Shell itibariyle, bir sanal ağda Cloud Shell kullanılırken bir depolama hesabı gerekir. Her yöneticinin dosyalarını depolamak için bir dosya paylaşımının olması gerekir.  Depolama hesabının Cloud Shell tarafından kullanılan sanal ağdan erişilebilir olması gerekir. 

## <a name="virtual-network-deployment-limitations"></a>Sanal ağ dağıtım sınırlamaları
* Dahil edilen ek ağ kaynakları nedeniyle, bir sanal ağdaki Cloud Shell başlatmak genellikle standart bir Cloud Shell oturumundan daha yavaştır.

* Orta Hindistan dışında tüm Cloud Shell bölgeleri Şu anda destekleniyor. 

* [Azure Relay](../azure-relay/relay-what-is-it.md) ücretsiz bir hizmet değil, lütfen [fiyatlandırmasını](https://azure.microsoft.com/pricing/details/service-bus/)görüntüleyin. Cloud Shell senaryosunda, her yönetici için Cloud Shell kullanılırken bir karma bağlantı kullanılır. Cloud Shell oturumu tamamlandıktan sonra bağlantı otomatik olarak kapatılacak.

## <a name="register-the-resource-provider"></a>Kaynak sağlayıcısını kaydetme

Microsoft. Containerınstances kaynak sağlayıcısının, kullanmak istediğiniz sanal ağı tutan abonelikte kayıtlı olması gerekir. İle uygun aboneliği seçin `Set-AzContext -Subscription {subscriptionName}` ve ardından şunu çalıştırın:

```powershell
PS> Get-AzResourceProvider -ProviderNamespace Microsoft.ContainerInstance | select ResourceTypes,RegistrationState

ResourceTypes                             RegistrationState
-------------                             -----------------
{containerGroups}                         Registered
...
```

**Registrationstate** ise `Registered` , herhangi bir eylem gerekmez. Varsa `NotRegistered` , öğesini çalıştırın `Register-AzResourceProvider -ProviderNamespace Microsoft.ContainerInstance` . 

## <a name="deploy-network-resources"></a>Ağ kaynaklarını dağıtma
 
### <a name="create-a-resource-group-and-virtual-network"></a>Kaynak grubu ve sanal ağ oluşturma
Bağlanmak istediğiniz bir VNET zaten varsa, bu bölümü atlayın.

Azure portal veya Azure CLı, Azure PowerShell vb. kullanarak yeni kaynak grubunda bir kaynak grubu ve sanal ağ oluşturun, **kaynak grubunun ve sanal ağın aynı bölgede olması gerekir**.

### <a name="arm-templates"></a>ARM şablonları
Bir sanal ağda Cloud Shell kaynak oluşturmak için [Azure hızlı başlangıç şablonunu](https://aka.ms/cloudshell/docs/vnet/template) ve gerekli depolama alanı oluşturmak Için [Azure hızlı başlangıç şablonunu](https://aka.ms/cloudshell/docs/vnet/template/storage) kullanın. Kaynak adlarınızı ve öncelikle dosya paylaşımınızın adını göz önünde atın.

### <a name="open-relay-firewall"></a>Geçiş güvenlik duvarını Aç
Yukarıdaki şablon kullanılarak oluşturulan geçişe gidin, Ayarlar ' da "ağ" seçeneğini belirleyin, tarayıcı ağınızdan geçişe erişime izin verin. Varsayılan olarak geçişe yalnızca içinde oluşturulduğu sanal ağdan erişilebilir. 

### <a name="configuring-cloud-shell-to-use-a-virtual-network"></a>Bir sanal ağ kullanmak için Cloud Shell yapılandırma.
> [!NOTE]
> Bu adım, her yönetici için Cloud Shell kullanacak şekilde tamamlanmalıdır.

Yukarıdaki adımları tamamladıktan sonra, Azure portal veya üzerinde Cloud Shell gidin https://shell.azure.com . Yalıtılmış bir Cloud Shell deneyimine her bağlanmak istediğinizde, bu deneyimlerden biri kullanılmalıdır.

> [!NOTE]
> Geçmişte Cloud Shell kullanılıyorsa, var olan CloudDrive 'ın çıkarılmalıdır. Bunu `clouddrive unmount` bir etkin Cloud Shell oturumundan çalıştırmak için, sayfanızı yenileyin.

Cloud Shell bağlantı kurmak için ilk çalıştırma deneyimiyle karşılaşacaktır. Tercih ettiğiniz kabuk deneyiminizi seçin, "Gelişmiş ayarları göster" i seçin ve "VNET yalıtım ayarlarını göster" kutusunu seçin. Açılır pencerede bulunan alanları doldurur.  Çoğu alan, bir sanal ağda Cloud Shell ile ilişkilendirilebilen kullanılabilir kaynaklara otomatik olarak doldurulur.  Dosya paylaşımının adının Kullanıcı tarafından doldurulması gerekir.


![Cloud Shell yalıtılmış VNET ilk deneyim ayarlarını gösterir.](media/private-vnet/vnet-settings.png)

## <a name="next-steps"></a>Sonraki adımlar
[Azure sanal ağları hakkında bilgi edinin](../virtual-network/virtual-networks-overview.md)
