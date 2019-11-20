---
title: Azure Batch hizmet işleri
titleSuffix: ML Studio (classic) - Azure
description: Machine Learning Studio (klasik) işlere yönelik Azure Batch hizmetlerine genel bakış. Toplu iş havuzu işleme, üzerinde toplu işler gönderebileceğiniz havuzlar oluşturmanıza olanak sağlar.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18, previous-title='Dedicated capacity for batch execution service jobs - Azure Machine Learning Studio (classic) | Microsoft Docs'
ms.date: 04/19/2017
ms.openlocfilehash: ad3ae99b04d5386172c9598f35cbe7d64fddcd36
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73837702"
---
# <a name="azure-batch-service-for-azure-machine-learning-studio-classic-jobs"></a>Azure Machine Learning Studio (klasik) işler için Azure Batch hizmeti

Machine Learning Batch havuzu işleme, Azure Machine Learning Batch yürütme hizmeti için müşteri tarafından yönetilen ölçek sağlar. Makine öğrenimi için klasik toplu işlem, gönderebileceğiniz eşzamanlı işlerin sayısını sınırlayan, çok kiracılı bir ortamda yer alır ve işler ilk kez ilk çıkar temelinde sıraya alınır. Bu belirsizlik, işinizin ne zaman çalıştırılacağını doğru bir şekilde tahmin edemeyeceğiniz anlamına gelir.

Toplu iş havuzu işleme, üzerinde toplu işler gönderebileceğiniz havuzlar oluşturmanıza olanak sağlar. Havuzun boyutunu ve işin gönderildiği havuzu kontrol edersiniz. BES işiniz, tahmin edilebilir işlem performansı ve gönderdiğiniz işleme yüküne karşılık gelen kaynak havuzları oluşturma yeteneği sağlayan kendi işleme alanında çalışır.

> [!NOTE]
> Havuz oluşturmak için yeni bir Kaynak Yöneticisi Machine Learning Web hizmetine sahip olmanız gerekir. Oluşturulduktan sonra, havuzda hem yeni Kaynak Yöneticisi tabanlı hem de klasik bir BES Web hizmeti çalıştırabilirsiniz.

## <a name="how-to-use-batch-pool-processing"></a>Batch havuzu işlemeyi kullanma

Batch havuzu Işleme yapılandırması şu anda Azure portal aracılığıyla kullanılamıyor. Batch havuzu işlemeyi kullanmak için şunları yapmanız gerekir:

-   Bir Batch havuzu hesabı oluşturmak ve bir havuz hizmeti URL 'SI ve yetkilendirme anahtarı almak için CSS 'yi çağırın
-   Yeni bir Kaynak Yöneticisi tabanlı Web hizmeti ve faturalandırma planı oluşturma

Hesabınızı oluşturmak için Microsoft Müşteri Hizmetleri ve desteği (CSS) ' ni çağırın ve abonelik KIMLIĞINIZI belirtin. CSS, senaryonuza uygun kapasiteyi belirlemede sizinle birlikte çalışır. Ardından CSS, hesabınızı oluşturabileceğiniz maksimum havuz sayısı ve her havuza yerleştirebileceğiniz maksimum sanal makine (VM) sayısı ile yapılandırır. Hesabınız yapılandırıldıktan sonra, havuz hizmeti URL 'niz ve yetkilendirme anahtarınız sağlanır.

Hesabınız oluşturulduktan sonra, Batch havuzunuzdaki havuz yönetimi işlemlerini gerçekleştirmek için havuz hizmeti URL 'sini ve yetkilendirme anahtarını kullanın.

![Batch havuzu hizmet mimarisi.](./media/dedicated-capacity-for-bes-jobs/pool-architecture.png)

Havuzları, CSS 'nin size verdiği havuz hizmeti URL 'sindeki havuz oluşturma işlemini çağırarak oluşturabilirsiniz. Bir havuz oluşturduğunuzda, sanal makine sayısını ve yeni bir Kaynak Yöneticisi Web hizmeti Machine Learning Swagger. JSON URL 'sini belirtin. Bu Web hizmeti faturalandırma ilişkilendirmesini oluşturmak için verilmiştir. Batch havuzu hizmeti, havuzu bir faturalandırma planıyla ilişkilendirmek için Swagger. JSON öğesini kullanır. Havuz üzerinde hem yeni Kaynak Yöneticisi hem de klasik bir BES Web hizmeti çalıştırabilirsiniz.

Herhangi bir yeni Kaynak Yöneticisi tabanlı Web hizmeti kullanabilirsiniz, ancak işlerin faturalandırılması bu hizmetle ilişkili faturalandırma planına göre ücretlendirilir. Toplu havuz işlerini çalıştırmak için özel olarak bir Web hizmeti ve yeni faturalandırma planı oluşturmak isteyebilirsiniz.

Web Hizmetleri oluşturma hakkında daha fazla bilgi için bkz. [Azure Machine Learning Web hizmeti dağıtma](deploy-a-machine-learning-web-service.md).

Bir havuz oluşturduktan sonra, Web hizmeti için Batch Istekleri URL 'sini kullanarak BES işini gönderebilirsiniz. Bir havuza veya klasik toplu işleme göndermek için bu seçeneği belirleyebilirsiniz. Batch havuzu işlemeye bir iş göndermek için şu parametreyi iş gönderim isteği gövdesine eklersiniz:

"AzureBatchPoolId": "&lt;havuz KIMLIĞI&gt;"

Parametresini eklerseniz, iş klasik Batch işlem ortamında çalıştırılır. Havuzun kullanılabilir kaynakları varsa, iş hemen çalışmaya başlar. Havuzda boş kaynaklar yoksa, işiniz bir kaynak kullanılabilir olana kadar sıraya alınır.

Havuzlarınızın kapasitesine düzenli olarak ulaştırabileceğinizi fark ederseniz ve daha fazla kapasiteye ihtiyacınız varsa, kotalarınızı artırmak için CSS 'yi çağırıp bir temsilciyle çalışabilirsiniz.

Örnek Istek:

https://ussouthcentral.services.azureml.net/subscriptions/80c77c7674ba4c8c82294c3b2957990c/services/9fe659022c9747e3b9b7b923c3830623/jobs?api-version=2.0

```json
{

    "Input":{
    
        "ConnectionString":"DefaultEndpointsProtocol=https;BlobEndpoint=https://sampleaccount.blob.core.windows.net/;TableEndpoint
        =https://sampleaccount.table.core.windows.net/;QueueEndpoint=https://sampleaccount.queue.core.windows.net/;FileEndpoint=https://zhguim
        l.file.core.windows.net/;AccountName=sampleaccount;AccountKey=&lt;Key&gt;;",
        
        "BaseLocation":null,
        
        "RelativeLocation":"testint/AdultCensusIncomeBinaryClassificationDataset.csv",
        
        "SasBlobToken":null
    
    },
    
    "GlobalParameters":{ },
    
    "Outputs":{
    
        "output1":{
        
            "ConnectionString":"DefaultEndpointsProtocol=https;BlobEndpoint=https://sampleaccount.blob.core.windows.net/;TableEndpo
            int=https://sampleaccount.table.core.windows.net/;QueueEndpoint=https://sampleaccount.queue.core.windows.net/;FileEndpoint=https://sampleaccount.file.core.windows.net/;AccountName=sampleaccount;AccountKey=&lt;Key&gt;",
            "BaseLocation":null,
            "RelativeLocation":"testintoutput/testint\_results.csv",
            
            "SasBlobToken":null
        
        }
    
    },
    
    "AzureBatchPoolId":"8dfc151b0d3e446497b845f3b29ef53b"

}
```

## <a name="considerations-when-using-batch-pool-processing"></a>Batch havuzu işleme kullanılırken dikkat edilecek noktalar

Toplu iş havuzu Işleme, her zaman faturalandırılabilir bir hizmettir ve bunu bir Kaynak Yöneticisi tabanlı faturalandırma planıyla ilişkilendirmenizi gerektirir. Yalnızca havuzun çalıştığı işlem saati sayısı için faturalandırılırsınız; Bu zaman havuzu sırasında çalıştırılan iş sayısı ne olursa olsun. Havuz oluşturursanız, havuzda hiçbir Batch işi çalıştırmasa bile havuz silinene kadar havuzdaki her bir sanal makinenin işlem saatleri için faturalandırılırsınız. Sanal makineler için faturalandırma, bunların silindiği ve durdurulduğu durumlarda başlar. [Machine Learning fiyatlandırma sayfasında](https://azure.microsoft.com/pricing/details/machine-learning/)bulunan planlardan herhangi birini kullanabilirsiniz.

Faturalandırma örneği:

2 sanal makineyle bir Batch havuzu oluşturur ve 24 saat sonra silerseniz, faturalandırma planınız 48 işlem saati olarak borçlandırılır; Bu dönemde kaç iş çalıştırıldığına bakılmaksızın.

4 sanal makineyle bir Batch havuzu oluşturup 12 saat sonra silerseniz, faturalandırma planınız ayrıca 48 işlem saati olarak borçlandırılır.

İşlerin ne zaman tamamlandığını öğrenmek için iş durumunu yoklamenizi öneririz. Tüm işleriniz çalışmayı bitirdiğinde, havuzdaki sanal makinelerin sayısını sıfıra ayarlamak için havuzu yeniden boyutlandır işlemini çağırın. Havuz kaynakları üzerinde kısaysa ve yeni bir havuz oluşturmanız gerekiyorsa (örneğin, farklı bir faturalandırma planına karşı fatura yapmak için), tüm işleriniz çalışmayı tamamladığında havuzu silebilirsiniz.


| **Şu durumlarda Batch havuzu Işlemeyi kullan**    | **Şu durumlarda klasik toplu işleme kullan**  |
|---|---|
|Çok sayıda iş çalıştırmanız gerekir<br>Veya<br/>İşlerinizin hemen çalışacağını bilmeniz gerekir<br/>Veya<br/>Garantili işleme gerekir. Örneğin, belirli bir zaman çerçevesinde birkaç iş çalıştırmanız ve gereksinimlerinizi karşılamak için işlem kaynaklarınızın ölçeğini ölçeklendirmek istemeniz gerekir.    | Yalnızca birkaç iş çalıştırıyorsunuz<br/>'<br/> İşlerin hemen çalıştırılmasına gerek yok |
