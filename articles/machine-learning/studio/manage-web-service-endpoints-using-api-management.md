---
title: API Management kullanarak Web hizmetlerini yönetme
titleSuffix: ML Studio (classic) - Azure
description: API Management kullanarak AzureML Web hizmetlerini yönetmeyi gösteren kılavuz. Kullanıcı erişimi, kullanım azaltma ve Pano izlemeyi tanımlayarak REST API uç noktalarınızı yönetin.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 11/03/2017
ms.openlocfilehash: 084f3a1ecc7e44dc404d63a75b4561f8d5cb57cb
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73839807"
---
# <a name="manage-azure-machine-learning-studio-classic-web-services-using-api-management"></a>API Management kullanarak Azure Machine Learning Studio (klasik) Web hizmetlerini yönetme
## <a name="overview"></a>Genel Bakış
Bu kılavuzda, Azure Machine Learning Studio (klasik) Web hizmetlerinizi yönetmek için API Management kullanarak nasıl hızlı bir şekilde başlacağınız gösterilmektedir.

## <a name="what-is-azure-api-management"></a>Azure API Management nedir?
Azure API Management, Kullanıcı erişimi, kullanım azaltma ve Pano izlemeyi tanımlayarak REST API uç noktalarınızı yönetmenize olanak tanıyan bir Azure hizmetidir. Daha fazla bilgi için bkz. [Azure API Management sitesi](https://azure.microsoft.com/services/api-management/) . Azure API Management kullanmaya başlamak için [içeri aktarma ve yayımlama kılavuzuna](/azure/api-management/import-and-publish)bakın. Bu kılavuzun temel aldığı bu diğer kılavuz, bildirim konfigürasyonları, katman fiyatlandırması, yanıt işleme, Kullanıcı kimlik doğrulaması, ürün oluşturma, geliştirici abonelikleri ve kullanım oluşturmasının dahil olduğu daha fazla konuyu ele alır.

## <a name="prerequisites"></a>Ön koşullar
Bu kılavuzu gerçekleştirmek için şunlar gerekir:

* Bir Azure hesabı.
* Bir AzureML hesabı.
* Web hizmeti olarak dağıtılan bir AzureML denemesinde çalışma alanı, hizmet ve api_key. Bir AzureML denemesi oluşturma hakkında ayrıntılı bilgi için bkz. [Studio hızlı](create-experiment.md)başlangıcı. Bir Web hizmeti olarak bir Studio (klasik) denemesinin nasıl dağıtılacağı hakkında bilgi için bkz. bir Web hizmeti olarak bir AzureML denemesinin nasıl dağıtılacağı hakkında ayrıntılı bilgi için bkz. [Studio dağıtımı nasıl yapılır](deploy-a-machine-learning-web-service.md) . Alternatif olarak, ek A basit bir AzureML denemesi oluşturma ve test etme ve bir Web hizmeti olarak dağıtma yönergelerini içerir.

## <a name="create-an-api-management-instance"></a>API Management örneği oluşturma

Azure Machine Learning Web hizmetinizi API Management bir örnekle yönetebilirsiniz.

1. [Azure portalında](https://portal.azure.com) oturum açın.
2. **+ Kaynak oluştur**’u seçin.
3. Arama kutusuna "API Management" yazın, ardından "API Management" kaynağını seçin.
4. **Oluştur**'a tıklayın.
5. **Ad** değeri, BENZERSIZ bir URL oluşturmak için kullanılır (Bu örnek "demoazureml" kullanır).
6. Hizmet örneğiniz için bir **abonelik**, **kaynak grubu**ve **konum** seçin.
7. **Kuruluş adı** için bir değer belirtin (Bu örnekte "demoazureml" kullanılır).
8. **Yönetici e-** postanızı girin-Bu e-posta API Management sisteminden bildirimler için kullanılacaktır.
9. **Oluştur**'a tıklayın.

Yeni bir hizmetin oluşturulması 30 dakika kadar sürebilir.

![hizmet oluştur](./media/manage-web-service-endpoints-using-api-management/create-service.png)


## <a name="create-the-api"></a>API oluşturma
Hizmet örneği oluşturulduktan sonra, bir sonraki adım, API 'yi oluşturmaktır. Bir API, istemci uygulamasından çağrılabilen işlemler grubundan oluşur. API işlemleri mevcut web hizmetlerine taşınır. Bu kılavuzda, var olan AzureML RR 'ler ve BES Web Hizmetleri için proxy 'nin bulunduğu API 'Ler oluşturulur.

API 'yi oluşturmak için:

1. Azure portal oluşturduğunuz hizmet örneğini açın.
2. Sol gezinti bölmesinde **API 'ler**' i seçin.

   ![API-yönetim-menü](./media/manage-web-service-endpoints-using-api-management/api-management.png)

1. **API Ekle**' ye tıklayın.
2. Bir **Web API 'si adı** girin (Bu örnek, "AzureML tanıtım API 'si" kullanır).
3. **Web hizmeti URL 'si**için "`https://ussouthcentral.services.azureml.net`" girin.
4. Bir * * Web API URL 'SI son eki girin. Bu, müşterilerin hizmet örneğine istek göndermek için kullanacağı URL 'nin son bölümü olur (Bu örnekte, "azureml-demo" kullanılır).
5. **Web API URL şeması**için **https**' yi seçin.
6. **Ürünler**için **Starter**' ı seçin.
7. **Kaydet** düğmesine tıklayın.


## <a name="add-the-operations"></a>İşlemleri ekleyin

İşlemler, yayımcı portalındaki bir API 'ye eklenir ve yapılandırılır. Yayımcı portalına erişmek için API Management hizmetinizin Azure portal **Yayımcı portalı** ' na tıklayın, **API 'ler**, **işlemler**' i seçin ve sonra **İşlem Ekle**' ye tıklayın.

![İşlem Ekle](./media/manage-web-service-endpoints-using-api-management/add-an-operation.png)

**Yeni işlem** penceresi görüntülenir ve **imza** sekmesi varsayılan olarak seçilir.

## <a name="add-rrs-operation"></a>RR ekleme Işlemi
Öncelikle AzureML RR 'ler hizmeti için bir işlem oluşturun:

1. **Http fiili**için **gönderi**' ı seçin.
2. **URL şablonu**için "`/workspaces/{workspace}/services/{service}/execute?api-version={apiversion}&details={details}`" yazın.
3. Bir **görünen ad** girin (Bu örnekte "RR yürütme" kullanılır).

   ![Add-RR-işlem-imza](./media/manage-web-service-endpoints-using-api-management/add-rrs-operation-signature.png)

4. Sola **ekle** > **yanıtlar** ' a tıklayın ve **200 Tamam**' ı seçin.
5. Bu işlemi kaydetmek için **Kaydet** ' e tıklayın.

   ![Add-RR-Operation-yanıt](./media/manage-web-service-endpoints-using-api-management/add-rrs-operation-response.png)

## <a name="add-bes-operations"></a>BES Işlemleri ekleme

> [!NOTE]
> Aşağıdaki ekran görüntüleri, RR işlemleri eklemek için bunlara çok benzer olduklarından BES işlemlerine dahil edilmez.

### <a name="submit-but-not-start-a-batch-execution-job"></a>Toplu yürütme işini gönderme (ancak başlatma)

1. API 'ye bir BES işlemi eklemek için **İşlem Ekle** ' ye tıklayın.
2. **Http fiili**için **gönderi**' ı seçin.
3. **URL şablonu**için "`/workspaces/{workspace}/services/{service}/jobs?api-version={apiversion}`" yazın.
4. Bir **görünen ad** girin (Bu örnekte "bes gönder" kullanılır).
5. Sola **ekle** > **yanıtlar** ' a tıklayın ve **200 Tamam**' ı seçin.
6. **Kaydet** düğmesine tıklayın.

### <a name="start-a-batch-execution-job"></a>Toplu yürütme işi başlatma

1. API 'ye bir BES işlemi eklemek için **İşlem Ekle** ' ye tıklayın.
2. **Http fiili**için **gönderi**' ı seçin.
3. **Http fiili**için "`/workspaces/{workspace}/services/{service}/jobs/{jobid}/start?api-version={apiversion}`" yazın.
4. Bir **görünen ad** girin (Bu örnekte "bes Start" kullanılır).
6. Sola **ekle** > **yanıtlar** ' a tıklayın ve **200 Tamam**' ı seçin.
7. **Kaydet** düğmesine tıklayın.

### <a name="get-the-status-or-result-of-a-batch-execution-job"></a>Toplu yürütme işinin durumunu veya sonucunu al

1. API 'ye bir BES işlemi eklemek için **İşlem Ekle** ' ye tıklayın.
2. **Http fiili**için **Al**' ı seçin.
3. **URL şablonu**için "`/workspaces/{workspace}/services/{service}/jobs/{jobid}?api-version={apiversion}`" yazın.
4. Bir **görünen ad** girin (Bu örnekte "bes durumu" kullanılır).
6. Sola **ekle** > **yanıtlar** ' a tıklayın ve **200 Tamam**' ı seçin.
7. **Kaydet** düğmesine tıklayın.

### <a name="delete-a-batch-execution-job"></a>Toplu yürütme işini silme

1. API 'ye bir BES işlemi eklemek için **İşlem Ekle** ' ye tıklayın.
2. **Http fiili**için **Sil**' i seçin.
3. **URL şablonu**için "`/workspaces/{workspace}/services/{service}/jobs/{jobid}?api-version={apiversion}`" yazın.
4. Bir **görünen ad** girin (Bu örnekte "bes Delete" kullanılır).
5. Sola **ekle** > **yanıtlar** ' a tıklayın ve **200 Tamam**' ı seçin.
6. **Kaydet** düğmesine tıklayın.

## <a name="call-an-operation-from-the-developer-portal"></a>Geliştirici portalından bir işlem çağırma

İşlemler doğrudan geliştirici portalından çağrılabilir ve bu da bir API 'nin işlemlerini görüntülemek ve test etmek için kullanışlı bir yol sağlar. Bu adımda, **AzureML demo TANıTıM API**'Sine eklenen **RR yürütme** yöntemini çağıracaksınız. 

1. **Geliştirici Portalı**' na tıklayın.

   ![geliştirici-Portal](./media/manage-web-service-endpoints-using-api-management/developer-portal.png)

2. Üst menüden **API 'ler** ' e tıklayın ve ardından mevcut işlemleri görmek Için **AzureML demo tanıtım API 'si** ' ne tıklayın.

   ![demoazureml-API](./media/manage-web-service-endpoints-using-api-management/demoazureml-api.png)

3. İşlem için **RR yürütme** ' yi seçin. **Dene**'ye tıklayın.

   ![deneyin](./media/manage-web-service-endpoints-using-api-management/try-it.png)

4. **İstek parametreleri**için, **çalışma alanınızı** ve **hizmetinizi**yazın, " **apiversion**için 2,0" ve **Ayrıntılar**için "true" yazın. **Çalışma alanınızı** ve **hizmetinizi** AzureML Web hizmeti panosunda bulabilirsiniz (bkz. Ek A 'da **Web hizmetini test** etme).

   **İstek üstbilgileri**için **üst bilgi Ekle** ' ye tıklayın ve "Content-Type" ve "Application/JSON" yazın. **Üst bilgi Ekle** ' ye tıklayın ve "yetkilendirme" ve "taşıyıcı *\<HIZMET API-Key\>* " yazın. API ANAHTARıNıZı AzureML Web hizmeti panosunda bulabilirsiniz (bkz. Ek A 'da **Web hizmetini test** etme).

   **İstek gövdesi**için `{"Inputs": {"input1": {"ColumnNames": ["Col2"], "Values": [["This is a good day"]]}}, "GlobalParameters": {}}`yazın.

   ![azureml-demo-API](./media/manage-web-service-endpoints-using-api-management/azureml-demo-api.png)

5. **Gönder**' e tıklayın.

   ![Gönder](./media/manage-web-service-endpoints-using-api-management/send.png)

Bir işlem çağrıldıktan sonra, geliştirici portalı, **Istenen URL** 'yi arka uç hizmetinden, **Yanıt durumuyla**, **Yanıt başlıklarından**ve **Yanıt içeriğiyle**görüntüler.

![Yanıt-durum](./media/manage-web-service-endpoints-using-api-management/response-status.png)

## <a name="appendix-a---creating-and-testing-a-simple-azureml-web-service"></a>Ek A-basit bir AzureML Web hizmeti oluşturma ve test etme
### <a name="creating-the-experiment"></a>Deneme oluşturma
Aşağıda basit bir AzureML denemesi oluşturma ve bir Web hizmeti olarak dağıtma adımları verilmiştir. Web hizmeti, rastgele bir metnin sütununu alır ve tamsayılar olarak temsil edilen bir özellikler kümesi döndürür. Örneğin:

| Metin | Karma metin |
| --- | --- |
| Bu iyi bir gün |1 1 2 2 0 2 0 1 |

İlk olarak, seçtiğiniz bir tarayıcıyı kullanarak şuraya gidin: [https://studio.azureml.net/](https://studio.azureml.net/) ve oturum açmak için kimlik bilgilerinizi girin. Sonra yeni bir boş deneme oluşturun.

![Arama-deneme-Şablonlar](./media/manage-web-service-endpoints-using-api-management/search-experiment-templates.png)

**Simplefeaturehashingdeneme**olarak yeniden adlandırın. **Kayıtlı veri kümelerini** genişletin ve **kitap incelemelerini Amazon 'ten** denemenize sürükleyin.

![basit özellik-karma-deneme](./media/manage-web-service-endpoints-using-api-management/simple-feature-hashing-experiment.png)

**Veri dönüştürme** ve **düzenleme** ' yi genişletin ve veri **kümesindeki sütunları seçme** denemenize sürükleyin. **Veri kümesindeki sütunları seçmek**için **Amazon 'tan kitap incelemelerini** bağlayın.

![Kitap İnceleme veri kümesi modülünü bir proje sütunları modülüne bağlama](./media/manage-web-service-endpoints-using-api-management/project-columns.png)

**Veri kümesinde sütunları seç** ' e tıklayın ve ardından **sütun seçiciyi Başlat** ' a tıklayıp **col2**' ı seçin Bu değişiklikleri uygulamak için onay işaretine tıklayın.

![Sütun adlarını kullanarak sütunları seçme](./media/manage-web-service-endpoints-using-api-management/select-columns.png)

**Metin analizi** genişletin ve **özellik karma** ' i deneme üzerine sürükleyin. **Veri kümesindeki sütunları seçme** **özelliğini karma Özellik**olarak bağlayın.

![Bağlan-proje-sütunlar](./media/manage-web-service-endpoints-using-api-management/connect-project-columns.png)

**Karma bit boyutu**için **3** yazın. Bu, 8 (23) sütun oluşturur.

![Karma-bit boyutu](./media/manage-web-service-endpoints-using-api-management/hashing-bitsize.png)

Bu noktada, denemeyi test etmek için **Çalıştır** ' ı tıklatın.

![çalışmaz](./media/manage-web-service-endpoints-using-api-management/run.png)

### <a name="create-a-web-service"></a>Web hizmeti oluşturma
Şimdi bir Web hizmeti oluşturun. **Web hizmeti** 'ni genişletin ve **giriş** denemeniz üzerine sürükleyin. **Girişi** **özellik karmaya**bağlayın. Ayrıca, **çıktıyı** denemenize sürükleyin. **Çıktıyı** **özellik karma**'e bağlayın.

![çıkış-Özellik-karma](./media/manage-web-service-endpoints-using-api-management/output-to-feature-hashing.png)

**Web hizmeti Yayımla**' ya tıklayın.

![Web hizmeti yayımlama](./media/manage-web-service-endpoints-using-api-management/publish-web-service.png)

Denemeyi yayımlamak için **Evet** ' i tıklatın.

![Evet-Yayımla](./media/manage-web-service-endpoints-using-api-management/yes-to-publish.png)

### <a name="test-the-web-service"></a>Web hizmetini test etme
Bir AzureML Web hizmeti RSS (istek/yanıt hizmeti) ve BES (Batch yürütme hizmeti) uç noktalarından oluşur. RSS, zaman uyumlu yürütme içindir. BES zaman uyumsuz iş yürütmesi içindir. Web hizmetinizi aşağıdaki örnek Python kaynağıyla test etmek için Python için Azure SDK 'sını indirip yüklemeniz gerekebilir (bkz: [Python nasıl yüklenir](/azure/python/python-sdk-azure-install)).

Ayrıca, aşağıdaki örnek kaynağa yönelik denemenizin **çalışma alanı**, **hizmet**ve **api_key** olması gerekir. Web hizmeti panosunda denemeniz için **istek/yanıt** veya **toplu yürütme** ' ya tıklayarak çalışma alanını ve hizmeti bulabilirsiniz.

![bul-çalışma alanı-ve-hizmet](./media/manage-web-service-endpoints-using-api-management/find-workspace-and-service.png)

Web hizmeti panosunda denemenize tıklayarak **api_key** bulabilirsiniz.

![Find-api-Key](./media/manage-web-service-endpoints-using-api-management/find-api-key.png)

#### <a name="test-rrs-endpoint"></a>Test RR uç noktası
##### <a name="test-button"></a>Test düğmesi
RR uç noktasını test etmenin kolay bir yolu, Web hizmeti panosunda **Test** ' e tıklamanız.

![test](./media/manage-web-service-endpoints-using-api-management/test.png)

Bu, **col2**için **iyi bir gün** yazın. Onay işaretine tıklayın.

![veri gir](./media/manage-web-service-endpoints-using-api-management/enter-data.png)

Şöyle bir şey göreceksiniz:

![örnek çıkış](./media/manage-web-service-endpoints-using-api-management/sample-output.png)

##### <a name="sample-code"></a>Örnek kod
Kaynak kodlarınızı test etmenin başka bir yolu da istemci kodunuzda. Panoda **istek/yanıt** ' a tıklayıp en alta kaydırırsanız, Python ve R için C#örnek kod görürsünüz. Ayrıca, istek URI 'SI, üst bilgiler ve gövde dahil olmak üzere RR isteğinin söz dizimini görürsünüz.

Bu kılavuzda, çalışan bir Python örneği gösterilmektedir. Denemeniz için **çalışma alanı**, **hizmet**ve **api_key** değiştirmeniz gerekir.

    import urllib2
    import json
    workspace = "<REPLACE WITH YOUR EXPERIMENT’S WEB SERVICE WORKSPACE ID>"
    service = "<REPLACE WITH YOUR EXPERIMENT’S WEB SERVICE SERVICE ID>"
    api_key = "<REPLACE WITH YOUR EXPERIMENT’S WEB SERVICE API KEY>"
    data = {
    "Inputs": {
        "input1": {
            "ColumnNames": ["Col2"],
            "Values": [ [ "This is a good day" ] ]
        },
    },
    "GlobalParameters": { }
    }
    url = "https://ussouthcentral.services.azureml.net/workspaces/" + workspace + "/services/" + service + "/execute?api-version=2.0&details=true"
    headers = {'Content-Type':'application/json', 'Authorization':('Bearer '+ api_key)}
    body = str.encode(json.dumps(data))
    try:
        req = urllib2.Request(url, body, headers)
        response = urllib2.urlopen(req)
        result = response.read()
        print "result:" + result
            except urllib2.HTTPError, error:
        print("The request failed with status code: " + str(error.code))
        print(error.info())
        print(json.loads(error.read()))

#### <a name="test-bes-endpoint"></a>BES uç noktasını test etme
Panoda **toplu yürütme** ' ye tıklayın ve en alta kaydırın. , Python ve R için C#örnek kod görürsünüz. Ayrıca, bir işi göndermek, bir işi başlatmak, bir işin durumunu veya sonuçlarını almak ve bir işi silmek için BES isteklerinin söz dizimini görürsünüz.

Bu kılavuzda, çalışan bir Python örneği gösterilmektedir. Denemeniz için **çalışma alanı**, **hizmet**ve **api_key** değiştirmeniz gerekir. Ayrıca, **depolama hesabı adını**, **depolama hesabı anahtarını**ve **depolama kapsayıcısı adını**değiştirmeniz gerekir. Son olarak, **giriş dosyasının** konumunu ve **çıkış dosyasının**konumunu değiştirmeniz gerekecektir.

    import urllib2
    import json
    import time
    from azure.storage import *
    workspace = "<REPLACE WITH YOUR WORKSPACE ID>"
    service = "<REPLACE WITH YOUR SERVICE ID>"
    api_key = "<REPLACE WITH THE API KEY FOR YOUR WEB SERVICE>"
    storage_account_name = "<REPLACE WITH YOUR AZURE STORAGE ACCOUNT NAME>"
    storage_account_key = "<REPLACE WITH YOUR AZURE STORAGE KEY>"
    storage_container_name = "<REPLACE WITH YOUR AZURE STORAGE CONTAINER NAME>"
    input_file = "<REPLACE WITH THE LOCATION OF YOUR INPUT FILE>" # Example: C:\\mydata.csv
    output_file = "<REPLACE WITH THE LOCATION OF YOUR OUTPUT FILE>" # Example: C:\\myresults.csv
    input_blob_name = "mydatablob.csv"
    output_blob_name = "myresultsblob.csv"
    def printHttpError(httpError):
    print("The request failed with status code: " + str(httpError.code))
    print(httpError.info())
    print(json.loads(httpError.read()))
    return
    def saveBlobToFile(blobUrl, resultsLabel):
    print("Reading the result from " + blobUrl)
    try:
        response = urllib2.urlopen(blobUrl)
    except urllib2.HTTPError, error:
        printHttpError(error)
        return
    with open(output_file, "wb+") as f:
        f.write(response.read())
    print(resultsLabel + " have been written to the file " + output_file)
    return
    def processResults(result):
    first = True
    results = result["Results"]
    for outputName in results:
        result_blob_location = results[outputName]
        sas_token = result_blob_location["SasBlobToken"]
        base_url = result_blob_location["BaseLocation"]
        relative_url = result_blob_location["RelativeLocation"]
        print("The results for " + outputName + " are available at the following Azure Storage location:")
        print("BaseLocation: " + base_url)
        print("RelativeLocation: " + relative_url)
        print("SasBlobToken: " + sas_token)
        if (first):
            first = False
            url3 = base_url + relative_url + sas_token
            saveBlobToFile(url3, "The results for " + outputName)
    return

    def invokeBatchExecutionService():
    url = "https://ussouthcentral.services.azureml.net/workspaces/" + workspace +"/services/" + service +"/jobs"
    blob_service = BlobService(account_name=storage_account_name, account_key=storage_account_key)
    print("Uploading the input to blob storage...")
    data_to_upload = open(input_file, "r").read()
    blob_service.put_blob(storage_container_name, input_blob_name, data_to_upload, x_ms_blob_type="BlockBlob")
    print "Uploaded the input to blob storage"
    input_blob_path = "/" + storage_container_name + "/" + input_blob_name
    connection_string = "DefaultEndpointsProtocol=https;AccountName=" + storage_account_name + ";AccountKey=" + storage_account_key
    payload =  {
        "Input": {
            "ConnectionString": connection_string,
            "RelativeLocation": input_blob_path
        },
        "Outputs": {
            "output1": { "ConnectionString": connection_string, "RelativeLocation": "/" + storage_container_name + "/" + output_blob_name },
        },
        "GlobalParameters": {
        }
    }
        body = str.encode(json.dumps(payload))
    headers = { "Content-Type":"application/json", "Authorization":("Bearer " + api_key)}
    print("Submitting the job...")
    # submit the job
    req = urllib2.Request(url + "?api-version=2.0", body, headers)
    try:
        response = urllib2.urlopen(req)
    except urllib2.HTTPError, error:
        printHttpError(error)
        return
    result = response.read()
    job_id = result[1:-1] # remove the enclosing double-quotes
    print("Job ID: " + job_id)
    # start the job
    print("Starting the job...")
    req = urllib2.Request(url + "/" + job_id + "/start?api-version=2.0", "", headers)
    try:
        response = urllib2.urlopen(req)
    except urllib2.HTTPError, error:
        printHttpError(error)
        return
    url2 = url + "/" + job_id + "?api-version=2.0"

    while True:
        print("Checking the job status...")
        # If you are using Python 3+, replace urllib2 with urllib.request in the following code
        req = urllib2.Request(url2, headers = { "Authorization":("Bearer " + api_key) })
        try:
            response = urllib2.urlopen(req)
        except urllib2.HTTPError, error:
            printHttpError(error)
            return
        result = json.loads(response.read())
        status = result["StatusCode"]
        print "status:" + status
        if (status == 0 or status == "NotStarted"):
            print("Job " + job_id + " not yet started...")
        elif (status == 1 or status == "Running"):
            print("Job " + job_id + " running...")
        elif (status == 2 or status == "Failed"):
            print("Job " + job_id + " failed!")
            print("Error details: " + result["Details"])
            break
        elif (status == 3 or status == "Cancelled"):
            print("Job " + job_id + " cancelled!")
            break
        elif (status == 4 or status == "Finished"):
            print("Job " + job_id + " finished!")
            processResults(result)
            break
        time.sleep(1) # wait one second
    return
    invokeBatchExecutionService()
