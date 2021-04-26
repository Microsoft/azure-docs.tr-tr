---
title: ML işlem hatlarını yayımlama
titleSuffix: Azure Machine Learning
description: Machine Learning işlem hatları ve Python için Azure Machine Learning SDK ile makine öğrenimi iş akışlarını çalıştırın.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: sgilley
ms.author: laobri
author: lobrien
ms.date: 8/25/2020
ms.topic: conceptual
ms.custom: how-to, contperf-fy21q1
ms.openlocfilehash: efedb21a1ec1ed53a8c6bfadf337d23a89c04383
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102520185"
---
# <a name="publish-and-track-machine-learning-pipelines"></a>Makine öğrenimi işlem hatlarını yayımlama ve izleme



Bu makalede, bir makine öğrenimi işlem hattının iş arkadaşlarınızla veya müşterilerinizle nasıl paylaşılacağını gösterilmektedir.

Machine Learning işlem hatları, Machine Learning görevleri için yeniden kullanılabilir iş akışlarıdır. İşlem hatlarından bir avantajı daha fazla işbirliği. Ayrıca, yeni bir sürümde çalışırken müşterilerin geçerli modeli kullanmasına izin vererek işlem hatlarını de kullanabilirsiniz. 

## <a name="prerequisites"></a>Önkoşullar

* Tüm işlem hattı kaynaklarınızın tutulacağı bir [Azure Machine Learning çalışma alanı](how-to-manage-workspace.md) oluşturun

* Azure Machine Learning SDK 'yı yüklemek için [geliştirme ortamınızı yapılandırın](how-to-configure-environment.md) veya SDK 'nın zaten yüklü olduğu bir [Azure Machine Learning işlem örneği](concept-compute-instance.md) kullanın

* Aşağıdaki öğreticiden yararlanarak bir makine öğrenimi işlem hattı oluşturun ve çalıştırın [: Batch Puanlama için Azure Machine Learning işlem hattı](tutorial-pipeline-batch-scoring-classification.md)oluşturun. Diğer seçenekler için bkz. [Azure MACHINE LEARNING SDK ile makine öğrenimi işlem hatları oluşturma ve çalıştırma](./how-to-create-machine-learning-pipelines.md)

## <a name="publish-a-pipeline"></a>İşlem hattı yayımlama

İşlem hattınızı çalışır durumda yaptıktan sonra, farklı girişlerle çalışacak şekilde bir işlem hattı yayımlayabilirsiniz. Önceden yayımlanmış bir işlem hattının REST uç noktası parametreleri kabul etmek için, işlem hattınızı, `PipelineParameter` değişen bağımsız değişkenler için nesneleri kullanacak şekilde yapılandırmanız gerekir.

1. Bir işlem hattı parametresi oluşturmak için bir [pipelineparameter](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter) nesnesini varsayılan bir değerle kullanın.

   ```python
   from azureml.pipeline.core.graph import PipelineParameter
   
   pipeline_param = PipelineParameter(
     name="pipeline_arg",
     default_value=10)
   ```

2. Bu nesneyi işlem hattındaki `PipelineParameter` adımlardan herhangi birine bir parametre olarak aşağıdaki gibi ekleyin:

   ```python
   compareStep = PythonScriptStep(
     script_name="compare.py",
     arguments=["--comp_data1", comp_data1, "--comp_data2", comp_data2, "--output_data", out_data3, "--param1", pipeline_param],
     inputs=[ comp_data1, comp_data2],
     outputs=[out_data3],
     compute_target=compute_target,
     source_directory=project_folder)
   ```

3. Çağrıldığında bir parametreyi kabul edecek bu işlem hattını yayımlayın.

   ```python
   published_pipeline1 = pipeline_run1.publish_pipeline(
        name="My_Published_Pipeline",
        description="My Published Pipeline Description",
        version="1.0")
   ```

## <a name="run-a-published-pipeline"></a>Yayımlanmış bir işlem hattı çalıştırma

Yayımlanan tüm işlem hatları bir REST uç noktasına sahiptir. Ardışık düzen uç noktasıyla, Python olmayan istemciler de dahil olmak üzere herhangi bir dış sistemlerden işlem hattının çalıştırılmasını tetikleyebilirsiniz. Bu uç nokta, toplu Puanlama ve yeniden eğitim senaryolarında "Managed yinelenebilirlik" öğesini sunar.

> [!IMPORTANT]
> İşlem hattınızı erişimi yönetmek için Azure rol tabanlı erişim denetimi (Azure RBAC) kullanıyorsanız, işlem [hattı senaryonuz (eğitim veya Puanlama) için izinleri ayarlayın](how-to-assign-roles.md#common-scenarios).

Önceki işlem hattının çalıştırılmasını çağırmak için bir Azure Active Directory kimlik doğrulaması üst bilgisi belirtecine ihtiyacınız vardır. Bu tür bir belirteci almak, [Azurecliauthentication sınıf](/python/api/azureml-core/azureml.core.authentication.azurecliauthentication) başvurusunda ve Azure Machine Learning not defterindeki [kimlik doğrulamasında](https://aka.ms/pl-restep-auth) açıklanmıştır.

```python
from azureml.pipeline.core import PublishedPipeline
import requests

response = requests.post(published_pipeline1.endpoint,
                         headers=aad_token,
                         json={"ExperimentName": "My_Pipeline",
                               "ParameterAssignments": {"pipeline_arg": 20}})
```

`json`POST isteğinin bağımsız değişkeni, anahtar için işlem `ParameterAssignments` hattı parametrelerini ve değerlerini içeren bir sözlüğü içermelidir. Ayrıca `json` bağımsız değişken aşağıdaki anahtarları içerebilir:

| Anahtar | Description |
| --- | --- | 
| `ExperimentName` | Bu uç noktayla ilişkili denemenin adı |
| `Description` | Uç noktayı açıklayan serbest biçimli metin | 
| `Tags` | İstekleri etiketlemek ve Not eklemek için kullanılabilecek serbest biçimli anahtar-değer çiftleri  |
| `DataSetDefinitionValueAssignments` | Yeniden eğitim olmadan veri kümelerini değiştirmek için kullanılan sözlük (aşağıdaki tartışmaya bakın) | 
| `DataPathAssignments` | Yeniden eğitim olmadan veri yollarını değiştirmek için kullanılan sözlük (aşağıdaki tartışmaya bakın) | 

### <a name="run-a-published-pipeline-using-c"></a>C kullanarak yayımlanmış bir işlem hattı çalıştırma # 

Aşağıdaki kod, bir işlem hattının C# ' den zaman uyumsuz olarak nasıl çağrılacağını gösterir. Kısmi kod parçacığı yalnızca çağrı yapısını gösterir ve bir Microsoft örneğinin parçası değildir. Bütün sınıfları veya hata işlemeyi göstermez. 

```csharp
[DataContract]
public class SubmitPipelineRunRequest
{
    [DataMember]
    public string ExperimentName { get; set; }

    [DataMember]
    public string Description { get; set; }

    [DataMember(IsRequired = false)]
    public IDictionary<string, string> ParameterAssignments { get; set; }
}

// ... in its own class and method ... 
const string RestEndpoint = "your-pipeline-endpoint";

using (HttpClient client = new HttpClient())
{
    var submitPipelineRunRequest = new SubmitPipelineRunRequest()
    {
        ExperimentName = "YourExperimentName", 
        Description = "Asynchronous C# REST api call", 
        ParameterAssignments = new Dictionary<string, string>
        {
            {
                // Replace with your pipeline parameter keys and values
                "your-pipeline-parameter", "default-value"
            }
        }
    };

    string auth_key = "your-auth-key"; 
    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", auth_key);

    // submit the job
    var requestPayload = JsonConvert.SerializeObject(submitPipelineRunRequest);
    var httpContent = new StringContent(requestPayload, Encoding.UTF8, "application/json");
    var submitResponse = await client.PostAsync(RestEndpoint, httpContent).ConfigureAwait(false);
    if (!submitResponse.IsSuccessStatusCode)
    {
        await WriteFailedResponse(submitResponse); // ... method not shown ...
        return;
    }

    var result = await submitResponse.Content.ReadAsStringAsync().ConfigureAwait(false);
    var obj = JObject.Parse(result);
    // ... use `obj` dictionary to access results
}
```

### <a name="run-a-published-pipeline-using-java"></a>Java kullanarak yayımlanmış bir işlem hattı çalıştırma

Aşağıdaki kod, kimlik doğrulaması gerektiren bir işlem hattının çağrısını gösterir (bkz. [Azure Machine Learning kaynakları ve iş akışları için kimlik doğrulamasını ayarlama](how-to-setup-authentication.md)). İşlem hatlarınız herkese açık bir şekilde dağıtılırsa, üreten çağrılara gerek kalmaz `authKey` . Kısmi kod parçacığı, Java sınıfı ve özel durum işleme ortak öğesini göstermez. Kod, `Optional.flatMap` bir araya döndürebileceğiniz işlevleri birlikte zincirleme için kullanır `Optional` . , `flatMap` Kısaltmayı ve kodu açıklığa kavuşturun kullanımı, ancak `getRequestBody()` SBU kodun özel durumlara izin verdiğini unutmayın.

```java
import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;
import java.util.Optional;
// JSON library
import com.google.gson.Gson;

String scoringUri = "scoring-endpoint";
String tenantId = "your-tenant-id";
String clientId = "your-client-id";
String clientSecret = "your-client-secret";
String resourceManagerUrl = "https://management.azure.com";
String dataToBeScored = "{ \"ExperimentName\" : \"My_Pipeline\", \"ParameterAssignments\" : { \"pipeline_arg\" : \"20\" }}";

HttpClient client = HttpClient.newBuilder().build();
Gson gson = new Gson();

HttpRequest tokenAuthenticationRequest = tokenAuthenticationRequest(tenantId, clientId, clientSecret, resourceManagerUrl);
Optional<String> authBody = getRequestBody(client, tokenAuthenticationRequest);
Optional<String> authKey = authBody.flatMap(body -> Optional.of(gson.fromJson(body, AuthenticationBody.class).access_token);;
Optional<HttpRequest> scoringRequest = authKey.flatMap(key -> Optional.of(scoringRequest(key, scoringUri, dataToBeScored)));
Optional<String> scoringResult = scoringRequest.flatMap(req -> getRequestBody(client, req));
// ... etc (`scoringResult.orElse()`) ... 

static HttpRequest tokenAuthenticationRequest(String tenantId, String clientId, String clientSecret, String resourceManagerUrl)
{
    String authUrl = String.format("https://login.microsoftonline.com/%s/oauth2/token", tenantId);
    String clientIdParam = String.format("client_id=%s", clientId);
    String resourceParam = String.format("resource=%s", resourceManagerUrl);
    String clientSecretParam = String.format("client_secret=%s", clientSecret);

    String bodyString = String.format("grant_type=client_credentials&%s&%s&%s", clientIdParam, resourceParam, clientSecretParam);

    HttpRequest request = HttpRequest.newBuilder()
        .uri(URI.create(authUrl))
        .POST(HttpRequest.BodyPublishers.ofString(bodyString))
        .build();
    return request;
}

static HttpRequest scoringRequest(String authKey, String scoringUri, String dataToBeScored)
{
    HttpRequest request = HttpRequest.newBuilder()
        .uri(URI.create(scoringUri))
        .header("Authorization", String.format("Token %s", authKey))
        .POST(HttpRequest.BodyPublishers.ofString(dataToBeScored))
        .build();
    return request;

}

static Optional<String> getRequestBody(HttpClient client, HttpRequest request) {
    try {
        HttpResponse<String> response = client.send(request, HttpResponse.BodyHandlers.ofString());
        if (response.statusCode() != 200) {
            System.out.println(String.format("Unexpected server response %d", response.statusCode()));
            return Optional.empty();
        }
        return Optional.of(response.body());
    }catch(Exception x)
    {
        System.out.println(x.toString());
        return Optional.empty();
    }
}

class AuthenticationBody {
    String access_token;
    String token_type;
    int expires_in;
    String scope;
    String refresh_token;
    String id_token;
    
    AuthenticationBody() {}
}
```

### <a name="changing-datasets-and-datapaths-without-retraining"></a>Veri kümelerini ve veri yollarını yeniden eğitim olmadan değiştirme

Farklı veri kümelerinde ve veri yollarında eğmeniz ve çıkarımını isteyebilirsiniz. Örneğin, daha küçük bir veri kümesi üzerinde eğmek ve veri kümesinin tamamını çıkarklaştırmak isteyebilirsiniz. Veri kümelerini `DataSetDefinitionValueAssignments` isteğin `json` bağımsız değişkeninde anahtarla değiştirin. Veri yollarını ile değiştirin `DataPathAssignments` . Her ikisinin tekniği benzerdir:

1. İşlem hattı tanım betiğinizdeki `PipelineParameter` veri kümesi için bir oluşturun. `DatasetConsumptionConfig`Veya arasından bir oluşturun `DataPath` `PipelineParameter` :

    ```python
    tabular_dataset = Dataset.Tabular.from_delimited_files('https://dprepdata.blob.core.windows.net/demo/Titanic.csv')
    tabular_pipeline_param = PipelineParameter(name="tabular_ds_param", default_value=tabular_dataset)
    tabular_ds_consumption = DatasetConsumptionConfig("tabular_dataset", tabular_pipeline_param)
    ```

1. ML betiğinizdeki dinamik olarak belirtilen veri kümesine şunu kullanarak erişin `Run.get_context().input_datasets` :

    ```python
    from azureml.core import Run
    
    input_tabular_ds = Run.get_context().input_datasets['tabular_dataset']
    dataframe = input_tabular_ds.to_pandas_dataframe()
    # ... etc ...
    ```

    ML betiğinin () için belirtilen değere eriştiğini `DatasetConsumptionConfig` `tabular_dataset` ve () değerini değil olduğunu unutmayın `PipelineParameter` `tabular_ds_param` .

1. İşlem hattı tanım betiğinizin `DatasetConsumptionConfig` öğesine parametresi olarak ayarlayın `PipelineScriptStep` :

    ```python
    train_step = PythonScriptStep(
        name="train_step",
        script_name="train_with_dataset.py",
        arguments=["--param1", tabular_ds_consumption],
        inputs=[tabular_ds_consumption],
        compute_target=compute_target,
        source_directory=source_directory)
    
    pipeline = Pipeline(workspace=ws, steps=[train_step])
    ```

1. Veri kümelerini, ınlekrime REST çağrınızda dinamik olarak değiştirmek için şunu kullanın `DataSetDefinitionValueAssignments` :
    
    ```python
    tabular_ds1 = Dataset.Tabular.from_delimited_files('path_to_training_dataset')
    tabular_ds2 = Dataset.Tabular.from_delimited_files('path_to_inference_dataset')
    ds1_id = tabular_ds1.id
    d22_id = tabular_ds2.id
    
    response = requests.post(rest_endpoint, 
                             headers=aad_token, 
                             json={
                                "ExperimentName": "MyRestPipeline",
                               "DataSetDefinitionValueAssignments": {
                                    "tabular_ds_param": {
                                        "SavedDataSetReference": {"Id": ds1_id #or ds2_id
                                    }}}})
    ```

[Veri kümesi ve PipelineParameter](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-showcasing-dataset-and-pipelineparameter.ipynb) ve [Gösterim veri yolu ve pipelineparametresinin](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines/intro-to-pipelines/aml-pipelines-showcasing-datapath-and-pipelineparameter.ipynb) gösterildiği Not defterleri bu tekniğin tüm örneklerine sahiptir.

## <a name="create-a-versioned-pipeline-endpoint"></a>Sürümlü ardışık düzen uç noktası oluşturma

Birden çok yayınlanan işlem hattı arkasında bir ardışık düzen uç noktası oluşturabilirsiniz. Bu teknik, ML işlem hatlarınızı yinelemek ve güncellemek için size sabit bir REST uç noktası sağlar.

```python
from azureml.pipeline.core import PipelineEndpoint

published_pipeline = PipelineEndpoint.get(workspace=ws, name="My_Published_Pipeline")
pipeline_endpoint = PipelineEndpoint.publish(workspace=ws, name="PipelineEndpointTest",
                                            pipeline=published_pipeline, description="Test description Notebook")
```

## <a name="submit-a-job-to-a-pipeline-endpoint"></a>İşlem hattı uç noktasına iş gönderme

Bir işi bir ardışık düzen uç noktasının varsayılan sürümüne gönderebilirsiniz:

```python
pipeline_endpoint_by_name = PipelineEndpoint.get(workspace=ws, name="PipelineEndpointTest")
run_id = pipeline_endpoint_by_name.submit("PipelineEndpointExperiment")
print(run_id)
```

Ayrıca, belirli bir sürüme iş gönderebilirsiniz:

```python
run_id = pipeline_endpoint_by_name.submit("PipelineEndpointExperiment", pipeline_version="0")
print(run_id)
```

Aynı REST API kullanılarak aynı şekilde gerçekleştirilebilir:

```python
rest_endpoint = pipeline_endpoint_by_name.endpoint
response = requests.post(rest_endpoint, 
                         headers=aad_token, 
                         json={"ExperimentName": "PipelineEndpointExperiment",
                               "RunSource": "API",
                               "ParameterAssignments": {"1": "united", "2":"city"}})
```

## <a name="use-published-pipelines-in-the-studio"></a>Studio 'da yayınlanmış işlem hatlarını kullanma

Ayrıca, Studio 'dan yayımlanmış bir işlem hattı da çalıştırabilirsiniz:

1. [Azure Machine Learning Studio](https://ml.azure.com)'da oturum açın.

1. [Çalışma alanınızı görüntüleyin](how-to-manage-workspace.md#view).

1. Sol tarafta **uç noktalar**' ı seçin.

1. Üstte **ardışık düzen uç noktaları**' nı seçin.
 ![Machine Learning yayımlanmış işlem hatları listesi](./media/how-to-create-your-first-pipeline/pipeline-endpoints.png)

1. İşlem hattı uç noktasının önceki çalıştırmalarının sonuçlarını çalıştırmak, kullanmak veya gözden geçirmek için belirli bir işlem hattı seçin.

## <a name="disable-a-published-pipeline&quot;></a>Yayımlanmış bir ardışık düzeni devre dışı bırakma

Yayınlanan işlem hatları listenizden bir işlem hattını gizlemek için, bunu Studio 'da veya SDK 'dan devre dışı bırakabilirsiniz:

```python
# Get the pipeline by using its ID from Azure Machine Learning studio
p = PublishedPipeline.get(ws, id=&quot;068f4885-7088-424b-8ce2-eeb9ba5381a6")
p.disable()
```

İle yeniden etkinleştirebilirsiniz `p.enable()` . Daha fazla bilgi için bkz. [Publishedpipeline sınıf](/python/api/azureml-pipeline-core/azureml.pipeline.core.publishedpipeline) başvurusu.

## <a name="next-steps"></a>Sonraki adımlar

- Makine öğrenimi işlem hatlarını daha fazla incelemek için [GitHub 'da bu jupi not defterlerini](https://aka.ms/aml-pipeline-readme) kullanın.
- [Azureml işlem hatları-Core](/python/api/azureml-pipeline-core/) paketi ve [azureml-işlem hatları-adımlar](/python/api/azureml-pipeline-steps/) paketi için SDK başvuru yardımına bakın.
- Hata ayıklama ve işlem hatlarında sorun [giderme hakkında ipuçları için bkz](how-to-debug-pipelines.md) ..