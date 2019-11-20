---
title: CLı kullanarak Internet çözümleyici testi oluşturma | Microsoft Docs
description: Bu makalede, ilk Internet Çözümleyicisi testinizi oluşturmayı öğrenin.
services: internet-analyzer
author: diego-perez-botero
ms.service: internet-analyzer
ms.topic: tutorial
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: 2952f7f24e92b952daafa682eee9d4605537a37b
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73839517"
---
# <a name="create-an-internet-analyzer-test-using-cli-preview"></a>CLı kullanarak Internet çözümleyici testi oluşturma (Önizleme)

[Azure Portal](internet-analyzer-create-test-portal.md) veya CLI kullanarak Internet çözümleyici kaynağı oluşturmanın iki yolu vardır. Bu bölüm, CLı deneyimimizi kullanarak yeni bir Azure Internet Çözümleyicisi kaynağı oluşturmanıza yardımcı olur. 


> [!IMPORTANT]
> Bu genel önizleme bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılmamalıdır. Belirli özellikler desteklenmiyor olabilir, kısıtlı yeteneklere sahip olabilir veya tüm Azure konumlarında mevcut olmayabilir. Ayrıntılar için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="before-you-begin"></a>Başlamadan önce

Genel Önizleme Genel kullanım için kullanılabilir; Ancak, veri depolama önizleme sırasında *ABD Batı 2* sınırlıdır.

## <a name="object-model"></a>Nesne modeli
Internet Çözümleyicisi CLı aşağıdaki kaynak türlerini kullanıma sunar:
* **Testler** -bir test, zaman içinde iki internet uç noktası (A ve B) için Son Kullanıcı performansını karşılaştırır.
* **Profiller** -testler bir Internet çözümleyici profili altında oluşturulur. Profiller ilgili testlerin gruplandırılmasına izin verir; tek bir profil, bir veya daha fazla test içerebilir.
* **Önceden yapılandırılmış uç** noktalar-uç noktaları çeşitli yapılandırmalara (bölgeler, hızlandırma teknolojileri vb.) ayarladık. Testlerinizde önceden yapılandırılmış bu uç noktaların herhangi birini kullanabilirsiniz.
* **Karneler** -bir karne, ölçüm sonuçlarının hızlı ve anlamlı özetlerini sağlar. [Karnelerinizi yorumlama](internet-analyzer-scorecard.md)bölümüne bakın.
* **Zaman serisi** -bir zaman serisi, bir ölçümün zaman içinde nasıl değişiklik gösterdiğini gösterir.

## <a name="profile-and-test-creation"></a>Profil ve test oluşturma
1. **Önizlemeye katılmak nasıl yaparım?** , [Azure Internet Çözümleyicisi hakkında SSS](internet-analyzer-faq.md)bölümündeki yönergeleri izleyerek Internet Çözümleyicisi önizleme erişimi alın.
2. [Azure CLI 'Yı yükler](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
3. Bir CLı oturumu başlatmak için `login` komutunu çalıştırın:
    ```azurecli-interactive
    az login
    ```

    CLı varsayılan tarayıcınızı açabildiğinden, bunu yapılır ve bir Azure oturum açma sayfası yükler.
    Aksi takdirde, https://aka.ms/devicelogin bir tarayıcı sayfası açın ve terminalinizde görünen yetkilendirme kodunu girin.

4. Tarayıcıda hesabınızın kimlik bilgileriyle oturum açın.

5. Internet Çözümleyicisi Genel önizlemesine erişim izni verilen abonelik KIMLIĞINIZI seçin.

    Oturum açtıktan sonra, Azure hesabınızla ilişkili Aboneliklerin listesini görürsünüz. `isDefault: true` sahip abonelik bilgileri, oturum açtıktan sonra şu anda etkinleştirilmiş olan aboneliğdir. Başka bir abonelik seçmek için, abone KIMLIĞIYLE [az Account set](https://docs.microsoft.com/cli/azure/account#az-account-set) komutunu kullanın. Abonelik seçimi hakkında daha fazla bilgi için bkz. [birden çok Azure aboneliği kullanma](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest).

    [Azure CLI ile oturum açma](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest) bölümünde ayrıntılı bir şekilde ele alınan etkileşimsiz olarak oturum açma yöntemleri mevcuttur.

6. **[Isteğe bağlı]** Yeni bir Azure Kaynak grubu oluşturun:
    ```azurecli-interactive
    az group create --location eastus --name "MyInternetAnalyzerResourceGroup"
    ```

7. Azure CLı Internet Çözümleyicisi uzantısını yükler:
     ```azurecli-interactive
    az extension add --name internet-analyzer
    ```

8. Yeni bir Internet Çözümleyicisi profili oluşturun:
    ```azurecli-interactive
    az internet-analyzer profile create --location eastus --resource-group "MyInternetAnalyzerResourceGroup" --name "MyInternetAnalyzerProfile" --enabled-state Enabled
    ```

9. Yeni oluşturulan profilin kullanabildiği tüm önceden yapılandırılmış uç noktaları listeleyin:
    ```azurecli-interactive
    az internet-analyzer preconfigured-endpoint list --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile"
    ```

10. Yeni oluşturulan ınternetanalyzer profili altında yeni bir test oluşturun:
    ```azurecli-interactive
    az internet-analyzer test create --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile" --endpoint-a-name "contoso" --endpoint-a-endpoint "www.contoso.com/some/path/to/trans.gif" --endpoint-b-name "microsoft" --endpoint-b-endpoint "www.microsoft.com/another/path/to/trans.gif" --name "MyFirstInternetAnalyzerTest" --enabled-state Enabled
    ```

    Yukarıdaki komutu hem `www.contoso.com` hem de `www.microsoft.com` özel yollar altında tek piksellik görüntüyü ([Trans. gif](https://fpc.msedge.net/apc/trans.gif)) barındırmakta olduğunu varsayar. Bir nesne yolu açıkça belirtilmemişse, Internet çözümleyici varsayılan olarak nesne yolu olarak `/apc/trans.gif` kullanır; bu, önceden yapılandırılmış uç noktaların tek piksellik görüntüyü barındırmakta olduğu yerdir. Ayrıca şemanın (https/http) belirtilmesi gerekmez; Internet çözümleyici yalnızca HTTPS uç noktalarını destekler, bu nedenle HTTPS kabul edilir.

11. Yeni test Internet Çözümleyicisi profili altında görünmelidir:
    ```azurecli-interactive
    az internet-analyzer test list --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile"
    ```

    Örnek çıktı:
    ````
    [
        {
            "description": null,
            "enabledState": "Enabled",
            "endpointA": {
            "endpoint": "www.contoso.com/some/path/to/1k.jpg",
            "name": "contoso"
            },
            "endpointB": {
            "endpoint": "www.microsoft.com/another/path/to/1k.jpg",
            "name": "microsoft"
            },
            "id": "/subscriptions/faa9ddd0-9137-4659-99b7-cdc55a953342/resourcegroups/MyInternetAnalyzerResourceGroup/providers/Microsoft.Network/networkexperimentprofiles/MyInternetAnalyzerProfile/experiments/MyFirstInternetAnalyzerTest",
            "location": null,
            "name": "MyFirstInternetAnalyzerTest",
            "resourceGroup": "MyInternetAnalyzerResourceGroup",
            "resourceState": "Enabled",
            "scriptFileUri": "https://fpc.msedge.net/client/v2/d8c6fc64238d464c882cee4a310898b2/ab.min.js",
            "status": "Created",
            "tags": null,
            "type": "Microsoft.Network/networkexperimentprofiles/experiments"
        }
    ]
    ````

12. Ölçümleri oluşturmaya başlamak için testin **Scriptfileuri** tarafından Işaret edilen JavaScript dosyasının Web uygulamanıza katıştırılması gerekir. Özel yönergeler [Internet Çözümleyicisi Istemci ekleme](internet-analyzer-embed-client.md) sayfasında bulunabilir.

13. Testin ilerlemesini, "durum" değerini izleyerek izleyebilirsiniz:
    ```azurecli-interactive
    az internet-analyzer test show --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile" --name "MyFirstInternetAnalyzerTest"
    ```

14. Testin toplanan sonuçlarını, için zaman serisi veya karne oluşturarak inceleyebilirsiniz:
    ```azurecli-interactive
    az internet-analyzer show-scorecard --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile" --name "MyFirstInternetAnalyzerTest" --aggregation-interval "Daily" --end-date-time-utc "2019-10-24T00:00:00"
    ```

    ```azurecli-interactive
    az internet-analyzer show-timeseries --resource-group "MyInternetAnalyzerResourceGroup" --profile-name "MyInternetAnalyzerProfile" --name "MyFirstInternetAnalyzerTest" --aggregation-interval "Hourly" --start-date-time-utc "2019-10-23T00:00:00" --end-date-time-utc "2019-10-24T00:00:00" --timeseries-type MeasurementCounts
    ```


## <a name="next-steps"></a>Sonraki adımlar

* [Internet ÇÖZÜMLEYICISI SSS](internet-analyzer-faq.md) makalesini okuyun
* [Internet Çözümleyicisi istemcisini](internet-analyzer-embed-client.md) katıştırma ve [özel bir uç nokta](internet-analyzer-custom-endpoint.md)oluşturma hakkında daha fazla bilgi edinin. 
