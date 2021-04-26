---
title: 'ML Studio (klasik): dağıtım ve tüketim-Azure'
description: Makine öğrenimi iş akışlarını ve modellerini Web Hizmetleri olarak dağıtmak için Azure Machine Learning Studio (klasik) kullanabilirsiniz. Bu Web Hizmetleri daha sonra, gerçek zamanlı veya toplu işlem modunda tahmine dayalı olarak, makine öğrenimi modellerini Internet üzerinden uygulamalardan çağırmak için kullanılabilir.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 04/19/2017
ms.openlocfilehash: 57c5462cd6eccab1d9c42e674e10ea34db3612b7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "100520366"
---
# <a name="azure-machine-learning-studio-classic-web-services-deployment-and-consumption"></a>Azure Machine Learning Studio (klasik) Web Hizmetleri: dağıtım ve tüketim

**Uygulama hedefi:** ![ İçin geçerlidir. ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (klasik) ![ için geçerlidir.](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  

Makine öğrenimi iş akışlarını ve modellerini Web Hizmetleri olarak dağıtmak için Azure Machine Learning Studio (klasik) kullanabilirsiniz. Bu Web Hizmetleri daha sonra, gerçek zamanlı veya toplu işlem modunda tahmine dayalı olarak, makine öğrenimi modellerini Internet üzerinden uygulamalardan çağırmak için kullanılabilir. Web hizmetleri yeniden hazırlandığından, bunları .NET ve Java gibi çeşitli programlama dilleri ve platformlarından ve Excel gibi uygulamalardan çağırabilirsiniz.

Sonraki bölümlerde, başlamanıza yardımcı olmak için izlenecek yollara, koda ve belgelere bağlantılar sağlanmaktadır.

## <a name="deploy-a-web-service"></a>Bir web hizmetini dağıtma

### <a name="with-azure-machine-learning-studio-classic"></a>Azure Machine Learning Studio (klasik) ile

Studio (klasik) portalı ve Microsoft Azure Machine Learning Web Hizmetleri portalı, kod yazmadan bir Web hizmetini dağıtmanıza ve yönetmenize yardımcı olur.

Aşağıdaki bağlantılar, yeni bir Web hizmetinin nasıl dağıtılacağı hakkında genel bilgiler sağlar:

* Azure Resource Manager tabanlı yeni bir Web hizmetinin nasıl dağıtılacağı hakkında genel bir bakış için, bkz. [Yeni bir Web hizmeti dağıtma](deploy-a-machine-learning-web-service.md).
* Web hizmetinin nasıl dağıtılacağı hakkında bir anlatım için bkz. [Azure Machine Learning Web hizmeti dağıtma](deploy-a-machine-learning-web-service.md).
* Bir Web hizmeti oluşturma ve dağıtma hakkında tam bir anlatım için [öğretici 1: kredi riskini tahmin](tutorial-part1-credit-risk.md)edin ' i kullanmaya başlayın.
* Web hizmeti dağıtan belirli örnekler için bkz.:

  * [Öğretici 3: Kredi risk modelini dağıtma](tutorial-part3-credit-risk-deploy.md)
  * [Web hizmetini birden çok bölgeye dağıtma](deploy-a-machine-learning-web-service.md#multi-region)

### <a name="with-web-services-resource-provider-apis-azure-resource-manager-apis"></a>Web Hizmetleri kaynak sağlayıcısı API 'Leri (Azure Resource Manager API 'Leri) ile

Web Hizmetleri için Azure Machine Learning Studio (klasik) kaynak sağlayıcısı, REST API çağrılarını kullanarak Web hizmetlerinin dağıtılmasını ve yönetilmesine izin vermez. Daha fazla bilgi için [Machine Learning Web hizmeti (REST)](/rest/api/machinelearning/index) başvurusuna bakın.

<!-- [Machine Learning Web Service (REST)](/rest/api/machinelearning/webservices) reference. -->

### <a name="with-powershell-cmdlets"></a>PowerShell cmdlet 'leri ile

Web Hizmetleri için Azure Machine Learning Studio (klasik) kaynak sağlayıcısı, PowerShell cmdlet 'lerini kullanarak Web hizmetlerinin dağıtımına ve yönetimine izin vermez.

Cmdlet 'lerini kullanmak için öncelikle [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) cmdlet 'Ini kullanarak PowerShell ortamında Azure hesabınızda oturum açmanız gerekir. Kaynak Yöneticisi tabanlı PowerShell komutlarının nasıl çağrılacağını bilmiyorsanız, bkz. [Azure Resource Manager ile Azure PowerShell kullanma](../../azure-resource-manager/management/manage-resources-powershell.md).

Tahmine dayalı denemenize dışarı aktarmak için [Bu örnek kodu](https://github.com/ritwik20/AzureML-WebServices)kullanın. Koddan. exe dosyasını oluşturduktan sonra şunu yazabilirsiniz:

```azurepowershell
C:\<folder>\GetWSD <experiment-url> <workspace-auth-token>
```

Uygulama çalıştırıldığında bir Web hizmeti JSON şablonu oluşturulur. Bir Web hizmetini dağıtmak üzere şablonu kullanmak için aşağıdaki bilgileri eklemeniz gerekir:

* Depolama hesabı adı ve anahtarı

    [Azure Portal](https://portal.azure.com/)depolama hesabı adını ve anahtarını alabilirsiniz.
* Taahhüt planı KIMLIĞI

    [Azure Machine Learning Web Hizmetleri](https://services.azureml.net) PORTALıNDAN plan kimliği ' nden oturum açıp bir plan adına tıklayarak ulaşabilirsiniz.

Bunları, *Machinelearningworkspace* düğümü ile aynı düzeydeki *Özellikler* DÜĞÜMÜNÜN alt öğeleri olarak JSON şablonuna ekleyin.

Aşağıda bir örnek verilmiştir:

```json
"StorageAccount": {
        "name": "YourStorageAccountName",
        "key": "YourStorageAccountKey"
},
"CommitmentPlan": {
    "id": "subscriptions/YouSubscriptionID/resourceGroups/YourResourceGroupID/providers/Microsoft.MachineLearning/commitmentPlans/YourPlanName"
}
```

Ek ayrıntılar için aşağıdaki makalelere ve örnek koda bakın:

* MSDN 'de [Azure Machine Learning Studio (klasik) cmdlet](/powershell/module/az.machinelearning) başvurusu

## <a name="consume-the-web-services"></a>Web hizmetlerini kullanma

### <a name="from-the-azure-machine-learning-web-services-ui-testing"></a>Azure Machine Learning Web Hizmetleri kullanıcı arabiriminden (test)

Web hizmetinizi Azure Machine Learning Web Hizmetleri portalından test edebilirsiniz. Bu, Request-Response hizmeti (RR) ve toplu yürütme hizmeti (BES) arabirimlerinin test edilmesini içerir.

* [Yeni bir web hizmeti dağıtma](deploy-a-machine-learning-web-service.md)
* [Azure Machine Learning web hizmeti dağıtma](deploy-a-machine-learning-web-service.md)
* [Öğretici 3: Kredi risk modelini dağıtma](tutorial-part3-credit-risk-deploy.md)

### <a name="from-excel"></a>Excel'den

Web hizmetini tüketen bir Excel şablonu indirebilirsiniz:

* [Excel 'den bir Azure Machine Learning Web hizmeti kullanma](consuming-from-excel.md)
* [Azure Machine Learning Web Hizmetleri için Excel eklentisi](excel-add-in-for-web-services.md)

### <a name="from-a-rest-based-client"></a>REST tabanlı bir istemciden

Azure Machine Learning Web Hizmetleri, yeniden kullanılabilir API 'lerdir. Bu API 'Leri .NET, Python, R, Java vb. gibi çeşitli platformlarda kullanabilirsiniz. Web hizmetiniz için [Microsoft Azure Machine Learning Web Hizmetleri portalında](https://services.azureml.net) **kullanma sayfasında,** başlamanıza yardımcı olabilecek örnek kod bulunur. Daha fazla bilgi için bkz. [Azure Machine Learning web hizmetini kullanma](consume-web-services.md).