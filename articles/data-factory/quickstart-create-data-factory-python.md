---
title: 'Hızlı başlangıç: Python kullanarak Azure Data Factory oluşturma'
description: Azure Blob depolama alanındaki bir konumdan başka bir konuma veri kopyalamak için bir veri fabrikası kullanın.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.devlang: python
ms.topic: quickstart
ms.date: 04/12/2021
ms.custom: seo-python-october2019, devx-track-python
ms.openlocfilehash: 534b5b3aca86cc2f6d7ee2d703939420f80abb8e
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107365102"
---
# <a name="quickstart-create-a-data-factory-and-pipeline-using-python"></a>Hızlı Başlangıç: Python kullanarak veri fabrikası ve işlem hattı oluşturma

> [!div class="op_single_selector" title1="Kullandığınız Data Factory hizmeti sürümünü seçin:"]
> * [Sürüm 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Güncel sürüm](quickstart-create-data-factory-python.md)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Bu hızlı başlangıçta Python kullanarak bir veri fabrikası oluşturursunuz. Bu veri fabrikasındaki işlem hattı, verileri Azure Blob depolama alanındaki bir klasörden başka bir klasöre kopyalar.

Azure Data Factory, veri taşıma ve veri dönüştürmeyi düzenlemek ve otomatikleştirmek için veri odaklı iş akışları oluşturmanıza olanak tanıyan bulut tabanlı bir veri tümleştirme hizmetidir. Azure Data Factory kullanarak, işlem hatları olarak adlandırılan veri odaklı iş akışları oluşturabilir ve zamanlayabilirsiniz.

İşlem hatları, farklı veri depolarından veri alabilir. İşlem hatları Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics ve Azure Machine Learning gibi işlem hizmetlerini kullanarak verileri işler veya dönüştürür. İşlem hatları, çıktı verilerini iş zekası (BI) uygulamaları için Azure SYNAPSE Analytics gibi veri depolarında yayımlar.

## <a name="prerequisites"></a>Önkoşullar

* Etkin aboneliği olan bir Azure hesabı. [Ücretsiz bir tane oluşturun](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

* [Python 3.6 +](https://www.python.org/downloads/).

* [Bir Azure depolama hesabı](../storage/common/storage-account-create.md).

* [Azure Depolama Gezgini](https://storageexplorer.com/) (isteğe bağlı).

* [Azure Active Directory bir uygulama](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal). Bu bağlantıdaki adımları izleyerek uygulamayı oluşturun ve aynı makaledeki yönergeleri izleyerek uygulamayı  **katkıda bulunan** rolüne atayın. Sonraki adımlarda kullanılan makalede gösterilen şekilde aşağıdaki değerleri unutmayın: **uygulama kimliği (aşağıdaki hizmet sorumlusu kimliği), kimlik doğrulama anahtarı (aşağıda istemci parolası) ve KIRACı kimliği.**

## <a name="create-and-upload-an-input-file"></a>Giriş dosyası oluşturma ve yükleme

1. Not Defteri'ni başlatın. Aşağıdaki metni kopyalayıp diskinizde **input.txt** dosyası olarak kaydedin.

    ```text
    John|Doe
    Jane|Doe
    ```
2.  [Azure Depolama Gezgini](https://storageexplorer.com/) gibi araçları kullanarak **adfv2tutorial** kapsayıcısı ve kapsayıcı içinde **input** klasörü oluşturun. Sonra, **input.txt** dosyasını **input** klasörüne yükleyin.

## <a name="install-the-python-package"></a>Python paketi yükleme

1. Yönetici ayrıcalıklarıyla bir terminal veya komut istemi açın. 
2. İlk olarak, Azure yönetim kaynakları için Python paketini yükleyin:

    ```python
    pip install azure-mgmt-resource
    ```
3. Data Factory için Python paketini yüklemek üzere aşağıdaki komutu çalıştırın:

    ```python
    pip install azure-mgmt-datafactory
    ```

    [Data Factory Için Python SDK 'Sı](https://github.com/Azure/azure-sdk-for-python) Python 2,7 ve 3.6 + ' yı destekler.

4. Azure kimlik doğrulaması için Python paketini yüklemek için aşağıdaki komutu çalıştırın:

    ```python
    pip install azure-identity
    ```
    > [!NOTE] 
    > "Azure-Identity" paketi bazı yaygın bağımlılıklarda "Azure-CLI" ile çakışıyor olabilir. Herhangi bir kimlik doğrulama sorununu karşılayabilir, "Azure-CLI" ve bağımlılıklarını kaldırın veya "Azure-CLI" paketini yüklemeden temiz bir makine kullanarak çalışır hale getirin.
    > Sovereign bulutları için, buluta özgü uygun sabitleri kullanmanız gerekir.  Lütfen [Python çoklu bulut Için Azure kitaplıklarını kullanarak tüm bölgelere bağlanma | Bağımsız bulutlarda Python ile bağlantı kurmak için yönergeler Microsoft Docs.](https://docs.microsoft.com/azure/developer/python/azure-sdk-sovereign-domain)
    
    
## <a name="create-a-data-factory-client"></a>Veri fabrikası istemcisi oluşturma

  
1. **datafactory.py** adlı bir dosya oluşturun. Ad alanlarına başvurular eklemek için aşağıdaki deyimleri ekleyin.

    ```python
    from azure.identity import ClientSecretCredential 
    from azure.mgmt.resource import ResourceManagementClient
    from azure.mgmt.datafactory import DataFactoryManagementClient
    from azure.mgmt.datafactory.models import *
    from datetime import datetime, timedelta
    import time
    ```
2. Bilgileri yazdıran aşağıdaki işlevleri ekleyin.

    ```python
    def print_item(group):
        """Print an Azure object instance."""
        print("\tName: {}".format(group.name))
        print("\tId: {}".format(group.id))
        if hasattr(group, 'location'):
            print("\tLocation: {}".format(group.location))
        if hasattr(group, 'tags'):
            print("\tTags: {}".format(group.tags))
        if hasattr(group, 'properties'):
            print_properties(group.properties)

    def print_properties(props):
        """Print a ResourceGroup properties instance."""
        if props and hasattr(props, 'provisioning_state') and props.provisioning_state:
            print("\tProperties:")
            print("\t\tProvisioning State: {}".format(props.provisioning_state))
        print("\n\n")

    def print_activity_run_details(activity_run):
        """Print activity run details."""
        print("\n\tActivity run details\n")
        print("\tActivity run status: {}".format(activity_run.status))
        if activity_run.status == 'Succeeded':
            print("\tNumber of bytes read: {}".format(activity_run.output['dataRead']))
            print("\tNumber of bytes written: {}".format(activity_run.output['dataWritten']))
            print("\tCopy duration: {}".format(activity_run.output['copyDuration']))
        else:
            print("\tErrors: {}".format(activity_run.error['message']))
    ```
3. Aşağıdaki kodu DataFactoryManagementClient sınıfının bir örneğini oluşturan **Main** yöntemine ekleyin. Veri fabrikası, bağlı hizmet, veri kümeleri ve işlem hattı oluşturmak için bu nesneyi kullanırsınız. Bu nesneyi ayrıca işlem hattı ayrıntılarını izlemek için kullanabilirsiniz. **subscription_id** değişkenini Azure aboneliğinizin kimliğine ayarlayın. Data Factory'nin kullanılabileceği Azure bölgelerinin bir listesi için bir sonraki sayfada ilgilendiğiniz bölgeleri seçin ve **Analytics**'i genişleterek **Data Factory**: [Products available by region](https://azure.microsoft.com/global-infrastructure/services/) (Bölgeye göre kullanılabilir durumdaki ürünler) bölümünü bulun. Veri fabrikası tarafından kullanılan verileri depoları (Azure Depolama, Azure SQL Veritabanı vb.) ve işlemler (HDInsight vb.) başka bölgelerde olabilir.

        
    ```python
    def main():

        # Azure subscription ID
        subscription_id = '<subscription ID>'

        # This program creates this resource group. If it's an existing resource group, comment out the code that creates the resource group
        rg_name = '<resource group>'

        # The data factory name. It must be globally unique.
        df_name = '<factory name>'

        # Specify your Active Directory client ID, client secret, and tenant ID
        credentials = ClientSecretCredential(client_id='<service principal ID>', client_secret='<service principal key>', tenant_id='<tenant ID>') 
        
        # Specify following for Soverign Clouds, import right cloud constant and then use it to connect.
        # from msrestazure.azure_cloud import AZURE_PUBLIC_CLOUD as CLOUD
        # credentials = DefaultAzureCredential(authority=CLOUD.endpoints.active_directory, tenant_id=tenant_id)
        
        resource_client = ResourceManagementClient(credentials, subscription_id)
        adf_client = DataFactoryManagementClient(credentials, subscription_id)

        rg_params = {'location':'westus'}
        df_params = {'location':'westus'}
    ```

## <a name="create-a-data-factory"></a>Veri fabrikası oluşturma

Aşağıdaki kodu **veri fabrikası** oluşturan **Main** yöntemine ekleyin. Kaynak grubunuz zaten varsa, birinci `create_or_update` deyimine açıklama ekleyin.

```python
    # create the resource group
    # comment out if the resource group already exits
    resource_client.resource_groups.create_or_update(rg_name, rg_params)

    #Create a data factory
    df_resource = Factory(location='westus')
    df = adf_client.factories.create_or_update(rg_name, df_name, df_resource)
    print_item(df)
    while df.provisioning_state != 'Succeeded':
        df = adf_client.factories.get(rg_name, df_name)
        time.sleep(1)
```

## <a name="create-a-linked-service"></a>Bağlı hizmet oluşturma

Aşağıdaki kodu bir **Azure Depolama bağlı hizmeti** oluşturan **Main** yöntemine ekleyin.

Veri depolarınızı ve işlem hizmetlerinizi veri fabrikasına bağlamak için veri fabrikasında bağlı hizmetler oluşturursunuz. Bu hızlı başlangıçta yalnızca bir Azure Depolama bağlı hizmetini örnekte "AzureStorageLinkedService" olarak adlandırılmış bir kopyalama kaynağı ve havuz deposu olarak oluşturmanız gerekir. `<storageaccountname>` ve `<storageaccountkey>` değerlerini Azure Depolama hesabınızın adı ve anahtarıyla değiştirin.

```python
    # Create an Azure Storage linked service
    ls_name = 'storageLinkedService001'

    # IMPORTANT: specify the name and key of your Azure Storage account.
    storage_string = SecureString(value='DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;EndpointSuffix=<suffix>')

    ls_azure_storage = LinkedServiceResource(properties=AzureStorageLinkedService(connection_string=storage_string)) 
    ls = adf_client.linked_services.create_or_update(rg_name, df_name, ls_name, ls_azure_storage)
    print_item(ls)
```
## <a name="create-datasets"></a>Veri kümeleri oluşturma

Bu bölümde biri kaynak, diğeri havuz için olmak üzere iki veri kümesi oluşturacaksınız.

### <a name="create-a-dataset-for-source-azure-blob"></a>Kaynak Azure Blob için veri kümesi oluşturma

Aşağıdaki kodu bir Azure blob veri kümesi oluşturan Main yöntemine ekleyin. Bir Azure Blob veri kümesinin özellikleri hakkında bilgi için [Azure blob bağlayıcısı](connector-azure-blob-storage.md#dataset-properties) makalesine bakın.

Azure Blob’da kaynak verilerini temsil eden bir veri kümesi tanımlayın. Bu Blob veri kümesi, önceki adımda oluşturduğunuz Azure Depolama bağlı hizmetini ifade eder.

```python
    # Create an Azure blob dataset (input)
    ds_name = 'ds_in'
    ds_ls = LinkedServiceReference(reference_name=ls_name)
    blob_path = '<container>/<folder path>'
    blob_filename = '<file name>'
    ds_azure_blob = DatasetResource(properties=AzureBlobDataset(
        linked_service_name=ds_ls, folder_path=blob_path, file_name=blob_filename)) 
    ds = adf_client.datasets.create_or_update(
        rg_name, df_name, ds_name, ds_azure_blob)
    print_item(ds)
```

### <a name="create-a-dataset-for-sink-azure-blob"></a>Havuz Azure Blob için veri kümesi oluşturma

Aşağıdaki kodu bir Azure blob veri kümesi oluşturan Main yöntemine ekleyin. Bir Azure Blob veri kümesinin özellikleri hakkında bilgi için [Azure blob bağlayıcısı](connector-azure-blob-storage.md#dataset-properties) makalesine bakın.

Azure Blob’da kaynak verilerini temsil eden bir veri kümesi tanımlayın. Bu Blob veri kümesi, önceki adımda oluşturduğunuz Azure Depolama bağlı hizmetini ifade eder.

```python
    # Create an Azure blob dataset (output)
    dsOut_name = 'ds_out'
    output_blobpath = '<container>/<folder path>'
    dsOut_azure_blob = DatasetResource(properties=AzureBlobDataset(linked_service_name=ds_ls, folder_path=output_blobpath))
    dsOut = adf_client.datasets.create_or_update(
        rg_name, df_name, dsOut_name, dsOut_azure_blob)
    print_item(dsOut)
```


## <a name="create-a-pipeline"></a>İşlem hattı oluşturma

Aşağıdaki kodu **bir kopyalama etkinliği ile işlem hattı** oluşturan **Main** yöntemine ekleyin.

```python
    # Create a copy activity
    act_name = 'copyBlobtoBlob'
    blob_source = BlobSource()
    blob_sink = BlobSink()
    dsin_ref = DatasetReference(reference_name=ds_name)
    dsOut_ref = DatasetReference(reference_name=dsOut_name)
    copy_activity = CopyActivity(name=act_name,inputs=[dsin_ref], outputs=[dsOut_ref], source=blob_source, sink=blob_sink)

    #Create a pipeline with the copy activity
    
    #Note1: To pass parameters to the pipeline, add them to the json string params_for_pipeline shown below in the format { “ParameterName1” : “ParameterValue1” } for each of the parameters needed in the pipeline.
    #Note2: To pass parameters to a dataflow, create a pipeline parameter to hold the parameter name/value, and then consume the pipeline parameter in the dataflow parameter in the format @pipeline().parameters.parametername.
    
    p_name = 'copyPipeline'
    params_for_pipeline = {}

    p_name = 'copyPipeline'
    params_for_pipeline = {}
    p_obj = PipelineResource(activities=[copy_activity], parameters=params_for_pipeline)
    p = adf_client.pipelines.create_or_update(rg_name, df_name, p_name, p_obj)
    print_item(p)
```

## <a name="create-a-pipeline-run"></a>İşlem hattı çalıştırması oluşturma

Aşağıdaki kodu **bir işlem hattı çalıştırması tetikleyen****Main** yöntemine ekleyin.

```python
    # Create a pipeline run
    run_response = adf_client.pipelines.create_run(rg_name, df_name, p_name, parameters={})
```

## <a name="monitor-a-pipeline-run"></a>İşlem hattı çalıştırmasını izleme

İşlem hattını izlemek için, aşağıdaki kodu **Main** yöntemine ekleyin:

```python
    # Monitor the pipeline run
    time.sleep(30)
    pipeline_run = adf_client.pipeline_runs.get(
        rg_name, df_name, run_response.run_id)
    print("\n\tPipeline run status: {}".format(pipeline_run.status))
    filter_params = RunFilterParameters(
        last_updated_after=datetime.now() - timedelta(1), last_updated_before=datetime.now() + timedelta(1))
    query_response = adf_client.activity_runs.query_by_pipeline_run(
        rg_name, df_name, pipeline_run.run_id, filter_params)
    print_activity_run_details(query_response.value[0])

```

Şimdi, program çalıştırıldığında **main** yöntemini çağırmak için aşağıdaki deyimi ekleyin:

```python
# Start the main method
main()
```

## <a name="full-script"></a>Tam betik

Tam Python kodu aşağıdaki gibidir:

```python
from azure.identity import ClientSecretCredential 
from azure.mgmt.resource import ResourceManagementClient
from azure.mgmt.datafactory import DataFactoryManagementClient
from azure.mgmt.datafactory.models import *
from datetime import datetime, timedelta
import time

def print_item(group):
    """Print an Azure object instance."""
    print("\tName: {}".format(group.name))
    print("\tId: {}".format(group.id))
    if hasattr(group, 'location'):
        print("\tLocation: {}".format(group.location))
    if hasattr(group, 'tags'):
        print("\tTags: {}".format(group.tags))
    if hasattr(group, 'properties'):
        print_properties(group.properties)

def print_properties(props):
    """Print a ResourceGroup properties instance."""
    if props and hasattr(props, 'provisioning_state') and props.provisioning_state:
        print("\tProperties:")
        print("\t\tProvisioning State: {}".format(props.provisioning_state))
    print("\n\n")

def print_activity_run_details(activity_run):
    """Print activity run details."""
    print("\n\tActivity run details\n")
    print("\tActivity run status: {}".format(activity_run.status))
    if activity_run.status == 'Succeeded':
        print("\tNumber of bytes read: {}".format(activity_run.output['dataRead']))
        print("\tNumber of bytes written: {}".format(activity_run.output['dataWritten']))
        print("\tCopy duration: {}".format(activity_run.output['copyDuration']))
    else:
        print("\tErrors: {}".format(activity_run.error['message']))


def main():

    # Azure subscription ID
    subscription_id = '<subscription ID>'

    # This program creates this resource group. If it's an existing resource group, comment out the code that creates the resource group
    rg_name = '<resource group>'

    # The data factory name. It must be globally unique.
    df_name = '<factory name>'

    # Specify your Active Directory client ID, client secret, and tenant ID
    credentials = ClientSecretCredential(client_id='<service principal ID>', client_secret='<service principal key>', tenant_id='<tenant ID>') 
    resource_client = ResourceManagementClient(credentials, subscription_id)
    adf_client = DataFactoryManagementClient(credentials, subscription_id)

    rg_params = {'location':'westus'}
    df_params = {'location':'westus'}
 
    # create the resource group
    # comment out if the resource group already exits
    resource_client.resource_groups.create_or_update(rg_name, rg_params)

    # Create a data factory
    df_resource = Factory(location='westus')
    df = adf_client.factories.create_or_update(rg_name, df_name, df_resource)
    print_item(df)
    while df.provisioning_state != 'Succeeded':
        df = adf_client.factories.get(rg_name, df_name)
        time.sleep(1)

    # Create an Azure Storage linked service
    ls_name = 'storageLinkedService001'

    # IMPORTANT: specify the name and key of your Azure Storage account.
    storage_string = SecureString(value='DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;EndpointSuffix=<suffix>')

    ls_azure_storage = LinkedServiceResource(properties=AzureStorageLinkedService(connection_string=storage_string)) 
    ls = adf_client.linked_services.create_or_update(rg_name, df_name, ls_name, ls_azure_storage)
    print_item(ls)

    # Create an Azure blob dataset (input)
    ds_name = 'ds_in'
    ds_ls = LinkedServiceReference(reference_name=ls_name)
    blob_path = '<container>/<folder path>'
    blob_filename = '<file name>'
    ds_azure_blob = DatasetResource(properties=AzureBlobDataset(
        linked_service_name=ds_ls, folder_path=blob_path, file_name=blob_filename))
    ds = adf_client.datasets.create_or_update(
        rg_name, df_name, ds_name, ds_azure_blob)
    print_item(ds)

    # Create an Azure blob dataset (output)
    dsOut_name = 'ds_out'
    output_blobpath = '<container>/<folder path>'
    dsOut_azure_blob = DatasetResource(properties=AzureBlobDataset(linked_service_name=ds_ls, folder_path=output_blobpath))
    dsOut = adf_client.datasets.create_or_update(
        rg_name, df_name, dsOut_name, dsOut_azure_blob)
    print_item(dsOut)

    # Create a copy activity
    act_name = 'copyBlobtoBlob'
    blob_source = BlobSource()
    blob_sink = BlobSink()
    dsin_ref = DatasetReference(reference_name=ds_name)
    dsOut_ref = DatasetReference(reference_name=dsOut_name)
    copy_activity = CopyActivity(name=act_name, inputs=[dsin_ref], outputs=[
                                 dsOut_ref], source=blob_source, sink=blob_sink)

    # Create a pipeline with the copy activity
    p_name = 'copyPipeline'
    params_for_pipeline = {}
    p_obj = PipelineResource(
        activities=[copy_activity], parameters=params_for_pipeline)
    p = adf_client.pipelines.create_or_update(rg_name, df_name, p_name, p_obj)
    print_item(p)

    # Create a pipeline run
    run_response = adf_client.pipelines.create_run(rg_name, df_name, p_name, parameters={})

    # Monitor the pipeline run
    time.sleep(30)
    pipeline_run = adf_client.pipeline_runs.get(
        rg_name, df_name, run_response.run_id)
    print("\n\tPipeline run status: {}".format(pipeline_run.status))
    filter_params = RunFilterParameters(
        last_updated_after=datetime.now() - timedelta(1), last_updated_before=datetime.now() + timedelta(1))
    query_response = adf_client.activity_runs.query_by_pipeline_run(
        rg_name, df_name, pipeline_run.run_id, filter_params)
    print_activity_run_details(query_response.value[0])


# Start the main method
main()
```

## <a name="run-the-code"></a>Kodu çalıştırma

Uygulamayı derleyip başlatın, ardından işlem hattı yürütmesini doğrulayın.

Konsol; veri fabrikası, bağlı hizmet, veri kümeleri, işlem hattı ve işlem hattı çalıştırmasının ilerleme durumunu yazdırır. Okunan/yazılan veri boyutunu içeren kopyalama etkinliği ayrıntılarını görene kadar bekleyin. Ardından, [Azure Depolama gezgini](https://azure.microsoft.com/features/storage-explorer/) gibi araçlar kullanarak, blobların değişkenlerde belirttiğiniz şekilde "inputBlobPath" yolundan "outputBlobPath" yoluna kopyalandığını doğrulayın.

Örnek çıktı aşağıdaki gibidir:

```console
Name: <data factory name>
Id: /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.DataFactory/factories/<data factory name>
Location: eastus
Tags: {}

Name: storageLinkedService
Id: /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.DataFactory/factories/<data factory name>/linkedservices/storageLinkedService

Name: ds_in
Id: /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.DataFactory/factories/<data factory name>/datasets/ds_in

Name: ds_out
Id: /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.DataFactory/factories/<data factory name>/datasets/ds_out

Name: copyPipeline
Id: /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.DataFactory/factories/<data factory name>/pipelines/copyPipeline

Pipeline run status: Succeeded
Datetime with no tzinfo will be considered UTC.
Datetime with no tzinfo will be considered UTC.

Activity run details

Activity run status: Succeeded
Number of bytes read: 18
Number of bytes written: 18
Copy duration: 4
```

## <a name="clean-up-resources"></a>Kaynakları temizleme

Veri fabrikasını silmek için aşağıdaki kodu programa ekleyin:

```python
adf_client.factories.delete(rg_name, df_name)
```

## <a name="next-steps"></a>Sonraki adımlar

Bu örnekteki işlem hattı, verileri bir konumdan Azure blob depolama alanındaki başka bir konuma kopyalar. Daha fazla senaryoda Data Factory’yi kullanma hakkında bilgi almak için [öğreticileri](tutorial-copy-data-dot-net.md) okuyun.
