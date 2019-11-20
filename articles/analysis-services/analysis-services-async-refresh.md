---
title: Azure Analysis Services modelleri için zaman uyumsuz yenileme | Microsoft Docs
description: Model verilerinin zaman uyumsuz yenilemesini kodlemek için Azure Analysis Services REST API nasıl kullanılacağını açıklar.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/28/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 7c6fba10264939335cdef26f288973f8217f340b
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73573389"
---
# <a name="asynchronous-refresh-with-the-rest-api"></a>REST API ile zaman uyumsuz yenileme

REST çağrılarını destekleyen herhangi bir programlama dilini kullanarak Azure Analysis Services tablolu modellerinizde zaman uyumsuz veri yenileme işlemleri gerçekleştirebilirsiniz. Bu, sorgu ölçeği için salt okuma çoğaltmalarının eşitlenmesini içerir. 

Veri hacmi, bölümler kullanılarak en iyi duruma getirme düzeyi vb. gibi bir dizi etkene bağlı olarak veri yenileme işlemleri biraz zaman alabilir. Bu işlemler, genellikle [Tom](https://docs.microsoft.com/bi-reference/tom/introduction-to-the-tabular-object-model-tom-in-analysis-services-amo) (tablolu nesne modeli), [PowerShell](https://docs.microsoft.com/analysis-services/powershell/analysis-services-powershell-reference) cmdlet 'leri veya [Tmsl](https://docs.microsoft.com/bi-reference/tmsl/tabular-model-scripting-language-tmsl-reference) (tablosal model betik dili) gibi mevcut yöntemlerle çağırılır. Ancak, bu yöntemler genellikle güvenilir olmayan, uzun süre çalışan HTTP bağlantılarına gerek duyar.

Azure Analysis Services REST API, veri yenileme işlemlerinin zaman uyumsuz olarak gerçekleştirilmesini sağlar. REST API kullanarak, istemci uygulamalarından uzun süre çalışan HTTP bağlantıları gerekli değildir. Güvenilirlik için otomatik yeniden denemeler ve toplu işlemeler gibi diğer yerleşik özellikler de vardır.

## <a name="base-url"></a>Taban URL 'SI

Temel URL şu biçimdedir:

```
https://<rollout>.asazure.windows.net/servers/<serverName>/models/<resource>/
```

Örneğin, Batı ABD Azure bölgesinde bulunan sunucum adlı sunucuda AdventureWorks adlı bir model düşünün. Sunucu adı:

```
asazure://westus.asazure.windows.net/myserver 
```

Bu sunucu adının temel URL 'SI:

```
https://westus.asazure.windows.net/servers/myserver/models/AdventureWorks/ 
```

Temel URL 'yi kullanarak kaynaklar ve işlemler aşağıdaki parametrelere göre eklenebilir: 

![Zaman uyumsuz yenileme](./media/analysis-services-async-refresh/aas-async-refresh-flow.png)

- **' De sonlanan her şey** bir koleksiyondur.
- **()** İle biten her şey bir işlevdir.
- Diğer her şey bir kaynak/nesnedir.

Örneğin, yenileme işlemini gerçekleştirmek için yenilemeler koleksiyonundaki POST fiilini kullanabilirsiniz:

```
https://westus.asazure.windows.net/servers/myserver/models/AdventureWorks/refreshes
```

## <a name="authentication"></a>Kimlik Doğrulaması

Tüm çağrıların yetkilendirme üstbilgisinde geçerli bir Azure Active Directory (OAuth 2) belirteciyle doğrulanması ve aşağıdaki gereksinimleri karşılaması gerekir:

- Belirtecin bir kullanıcı belirteci ya da bir uygulama hizmeti sorumlusu olması gerekir.
- Belirtecin `https://*.asazure.windows.net`için doğru hedef kitlesi ayarlanmış olması gerekir.
- Kullanıcı veya uygulamanın, istenen çağrıyı yapmak için sunucu veya modelde yeterli izinlere sahip olması gerekir. İzin düzeyi, modeldeki roller veya sunucudaki yönetim grubu tarafından belirlenir.

    > [!IMPORTANT]
    > Şu anda **Sunucu Yöneticisi** rolü izinleri gereklidir.

## <a name="post-refreshes"></a>GÖNDERI/yenilemeler

Yenileme işlemi gerçekleştirmek için, koleksiyona yeni bir yenileme öğesi eklemek için/Refresh koleksiyonundaki POST fiilini kullanın. Yanıttaki konum üst bilgisi yenileme KIMLIĞINI içerir. İstemci uygulaması, zaman uyumsuz olduğu için gerekirse bağlantıyı kesebilir ve durumu kontrol edebilir.

Bir model için tek seferde yalnızca bir yenileme işlemi kabul edilir. Çalışan geçerli bir yenileme işlemi varsa ve diğeri gönderiliyorsa, 409 çakışma HTTP durum kodu döndürülür.

Gövde aşağıdakine benzeyebilir:

```
{
    "Type": "Full",
    "CommitMode": "transactional",
    "MaxParallelism": 2,
    "RetryCount": 2,
    "Objects": [
        {
            "table": "DimCustomer",
            "partition": "DimCustomer"
        },
        {
            "table": "DimDate"
        }
    ]
}
```

### <a name="parameters"></a>Parametreler

Parametrelerin belirtilmesi gerekli değildir. Varsayılan değer uygulanır.

| Ad             | Tür  | Açıklama  |Varsayılan  |
|------------------|-------|--------------|---------|
| `Type`           | Sabit Listesi  | Gerçekleştirilecek işleme türü. Türler TMSL [yenileme komut](https://docs.microsoft.com/bi-reference/tmsl/refresh-command-tmsl) türleriyle hizalanır: Full, clearvalues, Calculate, dataonly, Automatic ve birleştirme. Tür ekleme desteklenmiyor.      |   Otomatik      |
| `CommitMode`     | Sabit Listesi  | Nesnelerin toplu işlemlere mi yoksa yalnızca tamamlandığında mi uygulanacağını belirler. Modlar şunlardır: Default, işlemsel, partialBatch.  |  işlem       |
| `MaxParallelism` | 'Tir   | Bu değer, işlem komutlarının paralel olarak çalıştırılacağı en fazla iş parçacığı sayısını belirler. Bu değer, TMSL [Sequence komutunda](https://docs.microsoft.com/bi-reference/tmsl/sequence-command-tmsl) veya diğer yöntemleri kullanarak ayarlanabir Maxparallelilik özelliği ile hizalanır.       | 10        |
| `RetryCount`     | 'Tir   | İşlemin başarısız olmadan önce kaç kez yeniden deneneceğini gösterir.      |     0    |
| `Objects`        | Dizi | İşlenecek nesne dizisi. Her nesne şunları içerir: bir bölümü işlerken tüm tablo veya "Tablo" ve "Bölüm" işlemlerini işlerken "Table". Hiçbir nesne belirtilmemişse, modelin tamamı yenilenir. |   Modelin tamamını işle      |

CommitMode, partialBatch 'e eşittir. Saat süretabilecek büyük veri kümelerinin ilk yüklemesi yapılırken kullanılır. Yenileme işlemi bir veya daha fazla toplu işlem başarıyla gerçekleştirildikten sonra başarısız olursa, başarıyla kaydedilmiş toplu işler tamamlandı olarak kalır (başarıyla işlenen toplu işleri geri almaz).

> [!NOTE]
> Yazma sırasında, toplu iş boyutu Maxparalellik değeridir, ancak bu değer değişebilir.

## <a name="get-refreshesrefreshid"></a>/Refreshes/\<refreshId > Al

Yenileme işleminin durumunu denetlemek için yenileme KIMLIĞI üzerinde GET fiilini kullanın. Yanıt gövdesine bir örnek aşağıda verilmiştir. İşlem devam ediyorsa, **sürüyor** durumu döndürülür.

```
{
    "startTime": "2017-12-07T02:06:57.1838734Z",
    "endTime": "2017-12-07T02:07:00.4929675Z",
    "type": "full",
    "status": "succeeded",
    "currentRefreshType": "full",
    "objects": [
        {
            "table": "DimCustomer",
            "partition": "DimCustomer",
            "status": "succeeded"
        },
        {
            "table": "DimDate",
            "partition": "DimDate",
            "status": "succeeded"
        }
    ]
}
```

## <a name="get-refreshes"></a>/Yenilemeler al

Bir model için geçmiş yenileme işlemlerinin bir listesini almak için/yenilemeler koleksiyonundaki GET fiilini kullanın. Yanıt gövdesine bir örnek aşağıda verilmiştir. 

> [!NOTE]
> Yazma sırasında, yenileme işlemlerinin son 30 günü saklanır ve döndürülür, ancak bu numara değişebilir.

```
[
    {
        "refreshId": "1344a272-7893-4afa-a4b3-3fb87222fdac",
        "startTime": "2017-12-09T01:58:04.76",
        "endTime": "2017-12-09T01:58:12.607",
        "status": "succeeded"
    },
    {
        "refreshId": "474fc5a0-3d69-4c5d-adb4-8a846fa5580b",
        "startTime": "2017-12-07T02:05:48.32",
        "endTime": "2017-12-07T02:05:54.913",
        "status": "succeeded"
    }
]
```

## <a name="delete-refreshesrefreshid"></a>/Refreshes/\<refreshId > SIL

Devam eden yenileme işlemini iptal etmek için yenileme KIMLIĞI üzerindeki DELETE fiilini kullanın.

## <a name="post-sync"></a>/Sync SONRASı

Yenileme işlemleri gerçekleştirdiyseniz, sorgu ölçeği genişletme için çoğaltmalarla yeni verileri eşitlemeniz gerekebilir. Bir model için eşitleme işlemi gerçekleştirmek için/Sync işlevindeki POST fiilini kullanın. Yanıttaki konum üst bilgisi eşitleme işlemi KIMLIĞINI içerir.

## <a name="get-sync-status"></a>/Sync durumunu al

Bir eşitleme işleminin durumunu denetlemek için, işlem KIMLIĞINI parametre olarak geçirerek GET fiilini kullanın. Yanıt gövdesine bir örnek aşağıda verilmiştir:

```
{
    "operationId": "cd5e16c6-6d4e-4347-86a0-762bdf5b4875",
    "database": "AdventureWorks2",
    "UpdatedAt": "2017-12-09T02:44:26.18",
    "StartedAt": "2017-12-09T02:44:20.743",
    "syncstate": 2,
    "details": null
}
```

`syncstate`değerleri:

- 0: çoğaltılıyor. Veritabanı dosyaları bir hedef klasöre çoğaltılıyor.
- 1: yeniden doldurma. Veritabanı, salt okunurdur ve sunucu örnekleri üzerinde yeniden doldurma işlemi uygulanıyor.
- 2: tamamlandı. Eşitleme işlemi başarıyla tamamlandı.
- 3: başarısız oldu. Eşitleme işlemi başarısız oldu.
- 4: sonlandırılıyor. Eşitleme işlemi tamamlandı ancak temizleme adımları gerçekleştiriliyor.

## <a name="code-sample"></a>Kod örneği

C# [GitHub üzerinde RestApiSample](https://github.com/Microsoft/Analysis-Services/tree/master/RestApiSample), başlamanıza olanak sağlamak için bir kod örneği aşağıda verilmiştir.

### <a name="to-use-the-code-sample"></a>Kod örneğini kullanmak için

1.  Depoyu kopyalayın veya indirin. RestApiSample çözümünü açın.
2.  Satır istemcisini bulun **. BaseAddress =..** . ve [temel URL](#base-url)'nizi sağlayın.

Kod örneği [hizmet sorumlusu](#service-principal) kimlik doğrulamasını kullanır.

### <a name="service-principal"></a>Hizmet sorumlusu

Hizmet sorumlusunu ayarlama hakkında daha fazla bilgi için bkz. [hizmet sorumlusu oluşturma-Azure Portal](../active-directory/develop/howto-create-service-principal-portal.md) ve [Sunucu Yöneticisi rolüne hizmet sorumlusu ekleme](analysis-services-addservprinc-admins.md) ve Azure 'da gerekli izinleri atama hakkında daha fazla bilgi için. Adımları tamamladıktan sonra aşağıdaki ek adımları uygulayın:

1.  Kod örneğinde, **dize yetkilisini bulun =...** , KURULUŞUNUZUN kiracı kimliğiyle **ortak** olarak değiştirin.
2.  Kimlik bilgileri nesnesinin örneğini oluşturmak için ClientCredential sınıfının kullanılması için açıklama/açıklama açıklama ekleyin. \<uygulama KIMLIĞI > ve \<uygulama anahtarı > değerlerine güvenli bir şekilde erişildiği veya hizmet sorumluları için sertifika tabanlı kimlik doğrulaması kullandığından emin olun.
3.  Örnek uygulamayı çalıştırın.


## <a name="see-also"></a>Ayrıca bkz.

[Örnekler](analysis-services-samples.md)   
[REST API](https://docs.microsoft.com/rest/api/analysisservices/servers)   


