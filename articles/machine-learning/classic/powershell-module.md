---
title: 'ML Studio (klasik): PowerShell modülleri-Azure'
description: PowerShell kullanarak Azure Machine Learning Studio (klasik) çalışma alanları, denemeleri, Web Hizmetleri ve daha fazlasını oluşturun ve yönetin.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.date: 04/25/2019
ms.openlocfilehash: 684299d61ba6e9e27e16a162c9f226a7ea3b5f58
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "100518020"
---
# <a name="powershell-modules-for-azure-machine-learning-studio-classic"></a>Azure Machine Learning Studio için PowerShell modülleri (klasik)

**Uygulama hedefi:** ![ İçin geçerlidir. ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (klasik) ![ için geçerlidir.](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  


PowerShell modüllerini kullanarak, çalışma alanları, veri kümeleri ve Web Hizmetleri gibi Studio (klasik) kaynaklarınızı ve varlıklarınızı programlama yoluyla yönetebilirsiniz.

Üç PowerShell modülünü kullanarak Studio (klasik) kaynaklarıyla etkileşime geçebilirsiniz:

* 2018 ' de yayınlanan [Azure PowerShell az](#az-rm) , farklı cmdlet adlarıyla birlikte Azurerd 'nin tüm işlevlerini içerir
* [Azurerd](#az-rm) 2016 içinde yayınlandı, PowerShell Ile değiştirilmiştir az
* [Azure Machine Learning PowerShell klasik](#classic) sürümünü 2016 içinde yayınlandı

Bu PowerShell modüllerinin bazı benzerlikler olsa da, her biri belirli senaryolar için tasarlanmıştır. Bu makalede, PowerShell modülleri arasındaki farklar açıklanmakta ve hangilerinin seçileceğine karar vermenize yardımcı olur.  

Her modül tarafından desteklenen kaynakları görmek için aşağıdaki [destek tablosunu](#support-table) kontrol edin. 

## <a name="azure-powershell-az-and-azurerm"></a><a name="az-rm"></a> Azure PowerShell az ve Azurerd

Az önce Azure ile etkileşim kurmak için tasarlanan PowerShell modülü, Azurerd 'nin önceki tüm işlevlerini içerir. AzureRM hata düzeltmelerini almaya devam eder, ancak yeni cmdlet 'ler veya özellikler almaz.  Az ve Azurerd, **Azure Resource Manager** dağıtım modeli kullanılarak dağıtılan çözümleri yönetir. Bu kaynaklar Studio (klasik) çalışma alanlarını ve Studio (klasik) "yeni" Web hizmetlerini içerir. 

PowerShell klasik, hem "yeni" hem de "klasik" kaynak türlerini kapsamak için az veya Azurerd ile birlikte yüklenebilir. Ancak, az ve Azurerd 'nin aynı anda yüklü olması önerilmez. Az ve Azurerd arasında karar vermek için Microsoft, gelecekteki tüm dağıtımlar için az önerilir.  Az ve Azurerd hakkında daha fazla bilgi edinin ve Azure PowerShell az olan geçiş yolu ' [na gidin](/powershell/azure/new-azureps-module-az).

Az kullanmaya başlamak için, [Azure için yükleme yönergelerini az](/powershell/azure/install-az-ps)izleyin.

## <a name="powershell-classic"></a><a name="classic"></a> PowerShell klasik

Studio (klasik) [PowerShell klasik modülü](https://aka.ms/amlps) , **klasik dağıtım modeli** kullanılarak dağıtılan kaynakları yönetmenizi sağlar. Bu kaynaklar, Studio (klasik) Kullanıcı varlıkları, "klasik" Web Hizmetleri ve "klasik" Web hizmeti uç noktaları içerir.

Ancak Microsoft, kaynakların dağıtımını ve yönetimini basitleştirmek amacıyla gelecekteki tüm kaynaklar için Kaynak Yöneticisi dağıtım modelini kullanmanızı önerir. Dağıtım modelleri hakkında daha fazla bilgi edinmek istiyorsanız, [Azure Resource Manager ile klasik dağıtım](../../azure-resource-manager/management/deployment-models.md) makalesine bakın.

PowerShell klasik ile çalışmaya başlamak için, GitHub 'dan [yayın paketini](https://github.com/hning86/azuremlps/releases) indirin ve [yükleme yönergelerini](https://github.com/hning86/azuremlps/blob/master/README.md)izleyin. Yönergeler, indirilen/daraltılmış DLL 'nin engellemesini kaldırma işleminin yanı sıra PowerShell ortamınıza nasıl içeri aktarılacağını açıklar.

PowerShell klasik, hem "yeni" hem de "klasik" kaynak türlerini kapsamak için az veya Azurerd ile birlikte yüklenebilir.

## <a name="powershell-support-table"></a><a name="support-table"></a> PowerShell destek tablosu


| Görev | **Az** |  **PowerShell klasik** |
| --- | --- | --- |
| Çalışma alanları oluşturma/silme | [Resource Manager şablonları](./deploy-with-resource-manager-template.md) |  |
| Çalışma alanı taahhüt planlarını yönetme | [New-AzMlCommitmentPlan](/powershell/module/az.machinelearning/new-azmlcommitmentplan) | |
| Çalışma alanı kullanıcılarını yönetme |  | [Add-Amlworkspace kullanıcıları](https://github.com/hning86/azuremlps#add-amlworkspaceusers)|
| Web hizmetlerini yönetme | [New-AzMlWebService](/powershell/module/az.machinelearning/new-azmlwebservice) <br>("yeni" Web Hizmetleri)| [New-AmlWebService](https://github.com/hning86/azuremlps#manage-classic-web-service) <br>("klasik" Web Hizmetleri) |
| Web hizmeti uç noktalarını/anahtarlarını yönetme |  [Get-AzMlWebServiceKey](/powershell/module/az.machinelearning/get-azmlwebservicekey)|  [Add-AmlWebServiceEndpoint](https://github.com/hning86/azuremlps#manage-classic-web-servcie-endpoint)|
| Kullanıcı veri kümeleri/eğitilen modellerini yönetme| | [Get-AmlDataset](https://github.com/hning86/azuremlps#manage-user-assets-dataset-trained-model-transform) |
| Kullanıcı denemeleri yönetme |  | [Start-AmlExperiment](https://github.com/hning86/azuremlps#manage-experiment) |
| Özel modülleri yönetme | | [New-AmlCustomModule](https://github.com/hning86/azuremlps#manage-custom-module) |


## <a name="next-steps"></a>Sonraki adımlar
Bu PowerShell modülünün tüm belgelerini inceleyin:
* [PowerShell klasik](https://aka.ms/amlps)
* [Azure PowerShell Az](/powershell/module/az.machinelearning/#machine_learning)