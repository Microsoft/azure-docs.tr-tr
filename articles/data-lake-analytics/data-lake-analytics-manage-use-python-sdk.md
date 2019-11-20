---
title: Python kullanarak Azure Data Lake Analytics yönetme
description: Bu makalede, Data Lake Analytics hesaplarını, veri kaynaklarını, kullanıcıları ve & işleri yönetmek için Python 'un nasıl kullanılacağı açıklanır.
services: data-lake-analytics
ms.service: data-lake-analytics
author: matt1883
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: d4213a19-4d0f-49c9-871c-9cd6ed7cf731
ms.topic: conceptual
ms.date: 06/08/2018
ms.openlocfilehash: d40658e1510c9ae8a2e3e1f865df7ac95f61abfb
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68355986"
---
# <a name="manage-azure-data-lake-analytics-using-python"></a>Python kullanarak Azure Data Lake Analytics yönetme
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Bu makalede, Python kullanarak Azure Data Lake Analytics hesaplarının, veri kaynaklarının, kullanıcıların ve işlerin nasıl yönetileceği açıklanmaktadır.

## <a name="supported-python-versions"></a>Desteklenen Python sürümleri

* Python 'un 64 bitlik bir sürümünü kullanın.
* **[Python.org İndirmeleri](https://www.python.org/downloads/)** adresinde bulunan standart Python dağıtımını kullanabilirsiniz. 
* Birçok geliştirici, **[Anaconda Python dağıtımını](https://www.anaconda.com/download/)** kullanmayı uygun şekilde bulur.  
* Bu makale, standart Python dağılımasından Python sürüm 3,6 kullanılarak yazılmıştır

## <a name="install-azure-python-sdk"></a>Azure Python SDK’yı yükleme

Aşağıdaki modülleri yükler:

* **Azure-MGMT-Resource** modülü, Active Directory vb. Için diğer Azure modüllerini içerir.
* **Azure-datalake-Store** modülü Azure Data Lake Store dosya sistemi işlemlerini içerir. 
* **Azure-MGMT-datalake-Store** modülü Azure Data Lake Store hesap yönetimi işlemlerini içerir.
* **Azure-MGMT-datalake-Analytics** modülü Azure Data Lake Analytics işlemlerini içerir. 

İlk olarak, aşağıdaki komutu çalıştırarak en `pip` son sürüme sahip olduğunuzdan emin olun:

```
python -m pip install --upgrade pip
```

Bu belge kullanılarak `pip version 9.0.1`yazılmıştır.

Komut satırında modülleri `pip` yüklemek için aşağıdaki komutları kullanın:

```
pip install azure-mgmt-resource
pip install azure-datalake-store
pip install azure-mgmt-datalake-store
pip install azure-mgmt-datalake-analytics
```

## <a name="create-a-new-python-script"></a>Yeni bir Python betiği oluştur

Aşağıdaki kodu betiğe yapıştırın:

```python
# Use this only for Azure AD service-to-service authentication
#from azure.common.credentials import ServicePrincipalCredentials

# Use this only for Azure AD end-user authentication
#from azure.common.credentials import UserPassCredentials

# Required for Azure Resource Manager
from azure.mgmt.resource.resources import ResourceManagementClient
from azure.mgmt.resource.resources.models import ResourceGroup

# Required for Azure Data Lake Store account management
from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
from azure.mgmt.datalake.store.models import DataLakeStoreAccount

# Required for Azure Data Lake Store filesystem management
from azure.datalake.store import core, lib, multithread

# Required for Azure Data Lake Analytics account management
from azure.mgmt.datalake.analytics.account import DataLakeAnalyticsAccountManagementClient
from azure.mgmt.datalake.analytics.account.models import DataLakeAnalyticsAccount, DataLakeStoreAccountInformation

# Required for Azure Data Lake Analytics job management
from azure.mgmt.datalake.analytics.job import DataLakeAnalyticsJobManagementClient
from azure.mgmt.datalake.analytics.job.models import JobInformation, JobState, USqlJobProperties

# Required for Azure Data Lake Analytics catalog management
from azure.mgmt.datalake.analytics.catalog import DataLakeAnalyticsCatalogManagementClient

# Use these as needed for your application
import logging
import getpass
import pprint
import uuid
import time
```

Modüllerin içeri aktarılabildiğini doğrulamak için bu betiği çalıştırın.

## <a name="authentication"></a>Authentication

### <a name="interactive-user-authentication-with-a-pop-up"></a>Açılır pencere ile etkileşimli kullanıcı kimlik doğrulaması

Bu yöntem desteklenmiyor.

### <a name="interactive-user-authentication-with-a-device-code"></a>Cihaz kodu ile etkileşimli kullanıcı kimlik doğrulaması

```python
user = input(
    'Enter the user to authenticate with that has permission to subscription: ')
password = getpass.getpass()
credentials = UserPassCredentials(user, password)
```

### <a name="noninteractive-authentication-with-spi-and-a-secret"></a>SPI ile etkileşimli olmayan kimlik doğrulaması ve gizli anahtar

```python
credentials = ServicePrincipalCredentials(
    client_id='FILL-IN-HERE', secret='FILL-IN-HERE', tenant='FILL-IN-HERE')
```

### <a name="noninteractive-authentication-with-api-and-a-certificate"></a>API ve sertifika ile etkileşimli olmayan kimlik doğrulaması

Bu yöntem desteklenmiyor.

## <a name="common-script-variables"></a>Ortak betik değişkenleri

Bu değişkenler örneklerde kullanılır.

```python
subid = '<Azure Subscription ID>'
rg = '<Azure Resource Group Name>'
location = '<Location>'  # i.e. 'eastus2'
adls = '<Azure Data Lake Store Account Name>'
adla = '<Azure Data Lake Analytics Account Name>'
```

## <a name="create-the-clients"></a>İstemcileri oluşturma

```python
resourceClient = ResourceManagementClient(credentials, subid)
adlaAcctClient = DataLakeAnalyticsAccountManagementClient(credentials, subid)
adlaJobClient = DataLakeAnalyticsJobManagementClient(
    credentials, 'azuredatalakeanalytics.net')
```

## <a name="create-an-azure-resource-group"></a>Azure Kaynak Grubu oluşturma

```python
armGroupResult = resourceClient.resource_groups.create_or_update(
    rg, ResourceGroup(location=location))
```

## <a name="create-data-lake-analytics-account"></a>Data Lake Analytics hesabı oluşturma

Önce bir mağaza hesabı oluşturun.

```python
adlsAcctResult = adlsAcctClient.account.create(
    rg,
    adls,
    DataLakeStoreAccount(
        location=location)
    )
).wait()
```
Ardından bu depoyu kullanan bir ADLA hesabı oluşturun.

```python
adlaAcctResult = adlaAcctClient.account.create(
    rg,
    adla,
    DataLakeAnalyticsAccount(
        location=location,
        default_data_lake_store_account=adls,
        data_lake_store_accounts=[DataLakeStoreAccountInformation(name=adls)]
    )
).wait()
```

## <a name="submit-a-job"></a>İş gönder

```python
script = """
@a  = 
    SELECT * FROM 
        (VALUES
            ("Contoso", 1500.0),
            ("Woodgrove", 2700.0)
        ) AS 
              D( customer, amount );
OUTPUT @a
    TO "/data.csv"
    USING Outputters.Csv();
"""

jobId = str(uuid.uuid4())
jobResult = adlaJobClient.job.create(
    adla,
    jobId,
    JobInformation(
        name='Sample Job',
        type='USql',
        properties=USqlJobProperties(script=script)
    )
)
```

## <a name="wait-for-a-job-to-end"></a>İşin bitmesini bekle

```python
jobResult = adlaJobClient.job.get(adla, jobId)
while(jobResult.state != JobState.ended):
    print('Job is not yet done, waiting for 3 seconds. Current state: ' +
          jobResult.state.value)
    time.sleep(3)
    jobResult = adlaJobClient.job.get(adla, jobId)

print('Job finished with result: ' + jobResult.result.value)
```

## <a name="list-pipelines-and-recurrences"></a>İşlem hatlarını ve tekrarları listeleyin
İşlerinizde işlem hattı veya yinelenme meta verileri eklenmiş olmasına bağlı olarak, işlem hatlarını ve tekrarları listeleyebilirsiniz.

```python
pipelines = adlaJobClient.pipeline.list(adla)
for p in pipelines:
    print('Pipeline: ' + p.name + ' ' + p.pipelineId)

recurrences = adlaJobClient.recurrence.list(adla)
for r in recurrences:
    print('Recurrence: ' + r.name + ' ' + r.recurrenceId)
```

## <a name="manage-compute-policies"></a>İşlem ilkelerini yönetme

Datalakeanalticsaccountmanagementclient nesnesi, bir Data Lake Analytics hesabının işlem ilkelerini yönetmek için yöntemler sağlar.

### <a name="list-compute-policies"></a>İşlem ilkelerini Listele

Aşağıdaki kod, bir Data Lake Analytics hesabı için işlem ilkelerinin bir listesini alır.

```python
policies = adlaAccountClient.computePolicies.listByAccount(rg, adla)
for p in policies:
    print('Name: ' + p.name + 'Type: ' + p.objectType + 'Max AUs / job: ' +
          p.maxDegreeOfParallelismPerJob + 'Min priority / job: ' + p.minPriorityPerJob)
```

### <a name="create-a-new-compute-policy"></a>Yeni bir işlem İlkesi Oluştur

Aşağıdaki kod, bir Data Lake Analytics hesabı için yeni bir işlem ilkesi oluşturur, belirtilen kullanıcı için kullanılabilir maksimum AU sayısını 50 olarak ayarlar ve en düşük iş önceliği 250 ' dir.

```python
userAadObjectId = "3b097601-4912-4d41-b9d2-78672fc2acde"
newPolicyParams = ComputePolicyCreateOrUpdateParameters(
    userAadObjectId, "User", 50, 250)
adlaAccountClient.computePolicies.createOrUpdate(
    rg, adla, "GaryMcDaniel", newPolicyParams)
```

## <a name="next-steps"></a>Sonraki adımlar

- Aynı öğreticiyi diğer araçları kullanarak görmek için sayfanın üst kısmındaki sekme seçicilerine tıklayın.
- U-SQL öğrenmek için bkz. [Azure Data Lake Analytics U-SQL dili ile çalışmaya başlama](data-lake-analytics-u-sql-get-started.md).
- Yönetim görevleri için bkz. [Azure portalı kullanarak Azure Data Lake Analytics'i yönetme](data-lake-analytics-manage-use-portal.md).

