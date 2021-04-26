---
title: Eğitiminde kimlik doğrulama gizli dizileri
titleSuffix: Azure Machine Learning
description: Çalışma alanınızın Azure Key Vault kullanarak güvenli biçimde nasıl gizli dizi eğitimlere gizli dizi geçireceğinizi öğrenin.
services: machine-learning
author: rastala
ms.author: roastala
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: core
ms.date: 03/09/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: b93da1e252357830578783c8f3ab5ca02f5a3e5b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102520753"
---
# <a name="use-authentication-credential-secrets-in-azure-machine-learning-training-runs"></a>Azure Machine Learning eğitim çalışmalarından kimlik doğrulama kimlik bilgileri gizli dizilerini kullanın


Bu makalede, eğitimin nasıl gizli bir şekilde çalıştığını öğrenirsiniz. Kullanıcı adınız ve parolanız gibi kimlik doğrulama bilgileri gizli dizilerlerdir. Örneğin, eğitim verilerini sorgulamak için bir dış veritabanına bağlanıyorsanız, Kullanıcı adınızı ve parolanızı uzak çalışma bağlamına geçirmeniz gerekir. Bu tür değerleri şifresiz metin olarak kodlamak, gizli dizi sergilediğinden güvenli değildir. 

Bunun yerine, Azure Machine Learning çalışma alanınızın [Azure Key Vault](../key-vault/general/overview.md)adlı ilişkili bir kaynağı vardır. Gizli dizileri Azure Machine Learning Python SDK 'sında bir dizi API aracılığıyla güvenli bir şekilde geçirmek için bu Key Vault kullanın.

Gizli dizileri kullanmak için standart akış:
 1. Yerel bilgisayarda Azure 'da oturum açın ve çalışma alanınıza bağlanın.
 2. Yerel bilgisayarda Key Vault çalışma alanında bir gizli dizi belirleyin.
 3. Uzaktan çalıştırma gönder.
 4. Uzaktan çalıştırma içinde Key Vault gizli anahtarı alın ve kullanın.

## <a name="set-secrets"></a>Gizli dizileri ayarla

Azure Machine Learning, [Keykasası](/python/api/azureml-core/azureml.core.keyvault.keyvault) sınıfı gizli dizileri ayarlamaya yönelik yöntemleri içerir. Yerel Python oturumunda, önce çalışma alanınızın Key Vault bir başvurusunu alın ve sonra bu yöntemi kullanarak bir [`set_secret()`](/python/api/azureml-core/azureml.core.keyvault.keyvault#set-secret-name--value-) gizli dizi adı ve değeri ayarlayın. __Set_secret__ yöntemi, ad zaten varsa gizli değeri günceller.

```python
from azureml.core import Workspace
from azureml.core import Keyvault
import os


ws = Workspace.from_config()
my_secret = os.environ.get("MY_SECRET")
keyvault = ws.get_default_keyvault()
keyvault.set_secret(name="mysecret", value = my_secret)
```

Gizli bir değeri, şifresiz metin olarak depolamak için güvenli olmadığı için Python kodunuza yerleştirmeyin. Bunun yerine, bir ortam değişkeninden (örneğin, Azure DevOps derleme sırrı) veya etkileşimli Kullanıcı girişinden gizli değer elde edin.

Yöntemini kullanarak gizli adları listeleyebilir [`list_secrets()`](/python/api/azureml-core/azureml.core.keyvault.keyvault#list-secrets--) ve aynı anda birden çok gizli dizi ayarlamanıza olanak sağlayan bir Batch sürümü[(set_secrets ()](/python/api/azureml-core/azureml.core.keyvault.keyvault#set-secrets-secrets-batch-) de mevcuttur.

## <a name="get-secrets"></a>Gizli dizileri al

Yerel kodunuzda, [`get_secret()`](/python/api/azureml-core/azureml.core.keyvault.keyvault#get-secret-name-) gizli değeri ada göre almak için yöntemini kullanabilirsiniz.

' A gönderilen çalıştırmalar için [`Experiment.submit`](/python/api/azureml-core/azureml.core.experiment.experiment#submit-config--tags-none----kwargs-) [`get_secret()`](/python/api/azureml-core/azureml.core.run.run#get-secret-name-) yöntemini sınıfıyla birlikte kullanın [`Run`](/python/api/azureml-core/azureml.core.run%28class%29) . Gönderilen bir çalıştırma çalışma alanının farkında olduğundan, bu yöntem çalışma alanı örneklemesini kısayollar ve gizli değeri doğrudan döndürür.

```python
# Code in submitted run
from azureml.core import Experiment, Run

run = Run.get_context()
secret_value = run.get_secret(name="mysecret")
```

Yazarak veya yazdırarak gizli değeri göstermemeye dikkat edin.

Aynı zamanda birden çok gizli dizi sürümüne erişmek için [get_secrets ()](/python/api/azureml-core/azureml.core.run.run#get-secrets-secrets-) Batch sürümü de vardır.

## <a name="next-steps"></a>Sonraki adımlar

 * [Örnek Not defterini görüntüle](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/manage-azureml-service/authentication-in-azureml/authentication-in-azureml.ipynb)
 * [Azure Machine Learning ile kurumsal güvenlik hakkında bilgi edinin](concept-enterprise-security.md)