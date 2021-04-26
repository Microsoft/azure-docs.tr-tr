---
title: Web Hizmetleri olarak dağıtılan modeller için kimlik doğrulamasını yapılandırma
titleSuffix: Azure Machine Learning
description: Azure Machine Learning 'de Web hizmetlerine dağıtılan makine öğrenimi modelleri için kimlik doğrulamanın nasıl yapılandırılacağını öğrenin.
services: machine-learning
author: cjgronlund
ms.author: cgronlun
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: core
ms.date: 11/06/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 5bd938fce347d439c2acb4e3fcace04d5b27d770
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "94447781"
---
# <a name="configure-authentication-for-models-deployed-as-web-services"></a>Web Hizmetleri olarak dağıtılan modeller için kimlik doğrulamasını yapılandırma

Azure Machine Learning, eğitilen makine öğrenimi modellerinizi Web Hizmetleri olarak dağıtmanızı sağlar. Bu makalede, bu dağıtımlar için kimlik doğrulamanın nasıl yapılandırılacağı hakkında bilgi edinin.

Azure Machine Learning tarafından oluşturulan model dağıtımları iki kimlik doğrulama yönteminden birini kullanacak şekilde yapılandırılabilir:

* **anahtar tabanlı**: Web hizmetinde kimlik doğrulaması yapmak için statik anahtar kullanılır.
* **belirteç tabanlı**: bir geçici belirtecin Azure Machine Learning çalışma alanından alınması gerekir (Azure Active Directory kullanılarak) ve Web hizmetinde kimlik doğrulaması için kullanılır. Bu belirtecin bir süre sonra süresi dolar ve Web hizmetiyle çalışmaya devam etmek için yenilenmesi gerekir.

    > [!NOTE]
    > Belirteç tabanlı kimlik doğrulaması yalnızca Azure Kubernetes hizmetine dağıtılmasında kullanılabilir.

## <a name="key-based-authentication"></a>Anahtar tabanlı kimlik doğrulaması

Azure Kubernetes Service (AKS) üzerinde dağıtılan Web Hizmetleri, varsayılan olarak anahtar tabanlı kimlik doğrulama *etkindir* .

Azure Container Instances (ACI) dağıtılan hizmetlerde varsayılan olarak anahtar tabanlı kimlik doğrulaması *devre dışıdır* , ancak `auth_enabled=True` aci Web-Service oluşturma sırasında ayarı yaparak etkinleştirebilirsiniz. Aşağıdaki kod, anahtar tabanlı kimlik doğrulaması etkinleştirilmiş bir acı dağıtım yapılandırması oluşturma örneğidir.

```python
from azureml.core.webservice import AciWebservice

aci_config = AciWebservice.deploy_configuration(cpu_cores = 1,
                                                memory_gb = 1,
                                                auth_enabled=True)
```

Daha sonra, sınıfını kullanarak dağıtımda özel ACI yapılandırmasını kullanabilirsiniz `Model` .

```python
from azureml.core.model import Model, InferenceConfig


inference_config = InferenceConfig(entry_script="score.py",
                                   environment=myenv)
aci_service = Model.deploy(workspace=ws,
                       name="aci_service_sample",
                       models=[model],
                       inference_config=inference_config,
                       deployment_config=aci_config)
aci_service.wait_for_deployment(True)
```

Kimlik doğrulama anahtarlarını getirmek için kullanın `aci_service.get_keys()` . Bir anahtarı yeniden oluşturmak için, `regen_key()` işlevini kullanın ve **birincil** ya da **İkincil** olarak geçirin.

```python
aci_service.regen_key("Primary")
# or
aci_service.regen_key("Secondary")
```

## <a name="token-based-authentication"></a>Belirteç tabanlı kimlik doğrulaması

Bir Web hizmeti için belirteç kimlik doğrulamasını etkinleştirdiğinizde, kullanıcılar, Web hizmetine erişmek için bir Azure Machine Learning JSON Web Token sunmalıdır. Belirtilen sürenin sonunda belirteç geçersiz olur ve çağrı yapmaya devam edilebilmesi için yenilenmesi gerekir.

* Belirteç kimlik doğrulaması, Azure Kubernetes hizmetine dağıtırken **Varsayılan olarak devre dışıdır** .
* Azure Container Instances ' a dağıtırken belirteç kimlik doğrulaması **desteklenmez** .
* Belirteç kimlik doğrulaması **, anahtar tabanlı kimlik doğrulama ile aynı anda kullanılamaz**.

Belirteç kimlik doğrulamasını denetlemek için, `token_auth_enabled` bir dağıtım oluştururken veya güncelleştirdiğinizde parametresini kullanın:

```python
from azureml.core.webservice import AksWebservice
from azureml.core.model import Model, InferenceConfig

# Create the config
aks_config = AksWebservice.deploy_configuration()

#  Enable token auth and disable (key) auth on the webservice
aks_config = AksWebservice.deploy_configuration(token_auth_enabled=True, auth_enabled=False)

aks_service_name ='aks-service-1'

# deploy the model
aks_service = Model.deploy(workspace=ws,
                           name=aks_service_name,
                           models=[model],
                           inference_config=inference_config,
                           deployment_config=aks_config,
                           deployment_target=aks_target)

aks_service.wait_for_deployment(show_output = True)
```

Belirteç kimlik doğrulaması etkinleştirilirse, `get_token` bir JSON Web token (JWT) almak için yöntemini ve bu belirtecin sona erme süresini kullanabilirsiniz:

> [!TIP]
> Belirteci almak için bir hizmet sorumlusu kullanırsanız ve bir belirteci almak için gereken en az erişime sahip olmasını istiyorsanız, bunu çalışma alanı için **okuyucu** rolüne atayın.

```python
token, refresh_by = aks_service.get_token()
print(token)
```

> [!IMPORTANT]
> Belirtecin `refresh_by` süresi dolduktan sonra yeni bir belirteç istemeniz gerekir. Belirteçleri Python SDK 'sının dışında yenilemeniz gerekiyorsa, bir seçenek, `service.get_token()` daha önce anlatıldığı gibi, çağrıyı düzenli olarak yapmak için hizmet sorumlusu kimlik doğrulamasıyla REST API kullanmaktır.
>
> Azure Machine Learning çalışma alanınızı Azure Kubernetes hizmet kümeniz ile aynı bölgede oluşturmanızı önemle öneririz.
>
> Bir belirteçle kimlik doğrulaması yapmak için Web hizmeti, Azure Machine Learning çalışma alanınızın oluşturulduğu bölgeye bir çağrı yapar. Çalışma alanı bölgeniz kullanılamıyorsa, kümeniz çalışma alanınızdan farklı bir bölgede olsa bile, Web hizmetiniz için bir belirteç getirimeyeceksiniz. Sonuç olarak, çalışma alanı bölgeniz yeniden kullanılabilir olana kadar Azure AD kimlik doğrulaması kullanılamaz.
>
> Ayrıca, kümenizin bölgesi ve çalışma alanı bölgeniz arasındaki mesafe arttıkça, bir belirteci getirmek için bu daha uzun sürer.

## <a name="next-steps"></a>Sonraki adımlar

Dağıtılan bir modele kimlik doğrulama hakkında daha fazla bilgi için bkz. [Web hizmeti olarak dağıtılan bir model için Istemci oluşturma](how-to-consume-web-service.md).