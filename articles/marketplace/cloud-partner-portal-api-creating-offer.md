---
title: Teklif oluşturma veya değiştirme-Azure Marketi
description: Yeni bir teklif oluşturmak veya var olan bir teklifi güncelleştirmek için API.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 07/29/2020
ms.openlocfilehash: dba47073a6676b17c091af048f583ca3e7712999
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "87420235"
---
# <a name="create-or-modify-an-offer"></a>Teklifi oluşturma veya değiştirme

> [!NOTE]
> Bulut İş Ortağı Portalı API 'Leri ile tümleşiktir ve Iş Ortağı Merkezi 'nde çalışmaya devam edecektir. Geçiş küçük değişiklikler sunar. Iş Ortağı Merkezi 'ne geçtikten sonra kodunuzun çalışmaya devam ettiğinden emin olmak için [bulut iş ortağı PORTALı API başvurusunda](./cloud-partner-portal-api-overview.md) listelenen değişiklikleri gözden geçirin. CPP API 'Leri yalnızca Iş Ortağı Merkezi 'ne geçişten önce tümleştirilmiş mevcut ürünler için kullanılmalıdır; Yeni ürünlerin Iş Ortağı Merkezi gönderme API 'Leri kullanması gerekir.

Bu çağrı, yayımcı ad alanı içinde belirli bir teklifi güncelleştirir veya yeni bir teklif oluşturur.

  `PUT https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>?api-version=2017-10-31`

## <a name="uri-parameters"></a>URI parametreleri

|  **Ad**         |  **Açıklama**                      |  **Veri türü**  |
|  --------         |  ----------------                     |  -------------  |
| PublisherId       |  Yayımcı tanımlayıcı, örneğin `contoso` |   Dize |
| OfferId           |  Teklif tanımlayıcısı                     |   Dize        |
| api-sürümü       |  API 'nin en son sürümü            |   Tarih           |
|  |  |  |

## <a name="header"></a>Üst bilgi

|  **Ad**        |  **Değer**               |
|  ---------       |  ----------              | 
| İçerik Türü     | `application/json`       |
| Yetkilendirme    | `Bearer YOUR_TOKEN`      |
|  |  |

## <a name="body-example"></a>Gövde örneği

Aşağıdaki örnek OfferId ile bir teklif oluşturur `contosovirtualmachine` .

### <a name="request"></a>İstek

``` json
  {
      "publisherId": "contoso",
      "offerTypeId": "microsoft-azure-virtualmachines",
      "id": "contosovirtualmachine",
      "offerTypeVersions": {
          "microsoft-azure-virtualmachines": 87,
          "microsoft-azure-marketplace": 39
      },
      "definition": {
          "displayText": "Contoso Virtual Machine Offer",
          "offer": {
          "microsoft-azure-marketplace.title": "Contoso App",
          "microsoft-azure-marketplace.summary": "Contoso App makes dev ops a breeze",
          "microsoft-azure-marketplace.longSummary": "Contoso App makes dev ops a breeze",
          "microsoft-azure-marketplace.description": "Contoso App makes dev ops a breeze",
          "microsoft-azure-marketplace.offerMarketingUrlIdentifier": "contosoapp",
          "microsoft-azure-marketplace.allowedSubscriptions": ["59160c40-2e25-4dcf-a2fd-6514cb08bf08"],
          "microsoft-azure-marketplace.usefulLinks": [{
              "linkTitle": "Contoso App for Azure",
              "linkUrl": "https://azuremarketplace.microsoft.com"
          }],
          "microsoft-azure-marketplace.categoryMap": [
                {
                    "categoryL1": "analytics",
                    "categoryL2-analytics": [
                        "visualization-and-reporting"
                    ]
                },
                {
                    "categoryL1": "ai-plus-machine-learning",
                    "categoryL2-ai-plus-machine-learning":[
                        "bot-services",
                        "cognitive-services",
                        "other"
                    ]
                }
            ],
          "microsoft-azure-marketplace.smallLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/6218c455-9cbc-450c-9920-f2e7a69ee132.png?sv=2014-02-14&sr=b&sig=6O8MM9dgiJ48VK0MwddkyVbprRAnBszyhVkVHGShhkI%3D&se=2019-03-28T19%3A46%3A50Z&sp=r",
          "microsoft-azure-marketplace.mediumLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/557e714b-2f31-4e12-b0cc-e48dd840edf4.png?sv=2014-02-14&sr=b&sig=NwL67NTQf9Gc9VScmZehtbHXpYmxhwZc2foy3o4xavs%3D&se=2019-03-28T19%3A46%3A49Z&sp=r",
          "microsoft-azure-marketplace.largeLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/142485da-784c-44cb-9523-d4f396446258.png?sv=2014-02-14&sr=b&sig=xaMxhwx%2FlKYfz33mJGIg8UBdVpsOwVvqhjTJ883o0iY%3D&se=2019-03-28T19%3A46%3A49Z&sp=r",
          "microsoft-azure-marketplace.wideLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/48af9013-1df7-4c94-8da8-4626e5039ce0.png?sv=2014-02-14&sr=b&sig=%2BnN7f2tprkrqb45ID6JlT01zXcy1PMTkWXtLKD6nfoE%3D&se=2019-03-28T19%3A46%3A49Z&sp=r",
          "microsoft-azure-marketplace.heroLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/c46ec74d-d214-4fb5-9082-3cee55200eba.png?sv=2014-02-14&sr=b&sig=RfDvjowFGpP4WZGAHylbF2CuXwO2NXOrwycrXEJvJI4%3D&se=2019-03-28T19%3A46%3A49Z&sp=r",
          "microsoft-azure-marketplace.screenshots": [],
          "microsoft-azure-marketplace.videos": [],
          "microsoft-azure-marketplace.leadDestination": "None",
          "microsoft-azure-marketplace.privacyURL": "https://azuremarketplace.microsoft.com",
          "microsoft-azure-marketplace.termsOfUse": "Terms of use",
          "microsoft-azure-marketplace.engineeringContactName": "Jon Doe",
          "microsoft-azure-marketplace.engineeringContactEmail": "jondoe@outlook.com",
          "microsoft-azure-marketplace.engineeringContactPhone": "555-555-5555",
          "microsoft-azure-marketplace.supportContactName": "Jon Doe",
          "microsoft-azure-marketplace.supportContactEmail": "jondoe@outlook.com",
          "microsoft-azure-marketplace.supportContactPhone": "555-555-5555",
          "microsoft-azure-marketplace.publicAzureSupportUrl": "",
          "microsoft-azure-marketplace.fairfaxSupportUrl": ""
      },
      "plans": 
      [
          {
              "planId": "contososkuidentifier",
              "microsoft-azure-virtualmachines.skuTitle": "Contoso App",
              "microsoft-azure-virtualmachines.skuSummary": "Contoso App makes dev ops a breeze.",
              "microsoft-azure-virtualmachines.skuDescription": "This is a description for the Contoso App that makes dev ops a breeze.",
              "microsoft-azure-virtualmachines.hideSKUForSolutionTemplate": false,
              "microsoft-azure-virtualmachines.cloudAvailability": ["PublicAzure"],
              "virtualMachinePricing": {
                  "isByol": true,
                  "freeTrialDurationInMonths": 0
              },
              "microsoft-azure-virtualmachines.operatingSystemFamily": "Windows",
              "microsoft-azure-virtualmachines.windowsOSType": "Other",
              "microsoft-azure-virtualmachines.operationSystem": "Contoso App",
              "microsoft-azure-virtualmachines.recommendedVMSizes": ["a0-basic", "a0-standard", "a1-basic", "a1-standard", "a2-basic", "a2-standard"],
              "microsoft-azure-virtualmachines.openPorts": [],
              "microsoft-azure-virtualmachines.vmImages": 
              {
                  "1.0.1": 
                  {
                      "osVhdUrl": "http://contosoteststorage.blob.core.windows.net/test/contosoVM.vhd?sv=2014-02-14&sr=c&sig=WlDo6Q4xwYH%2B5QEJbItPUVdgHhBcrVxPBmntZ2vU96w%3D&st=2016-06-25T18%3A30%3A00Z&se=2017-06-25T18%3A30%3A00Z&sp=rl",
                      "lunVhdDetails": []
                  }
              },
              "regions": ["AZ"]
          }
          ]
      },
      "eTag": "W/\"datetime'2017-06-07T06%3A15%3A40.4771399Z'\"",
      "version": 5
  }
```

### <a name="response"></a>Yanıt

``` json
 {
         "offerTypeId": "microsoft-azure-virtualmachines",
         "publisherId": "contoso",
         "status": "neverPublished",
         "id": "contosovirtualmachine",
         "version": 1,
         "definition": {
         "displayText": "Contoso Virtual Machine Offer",
         "offer": 
         {
             "microsoft-azure-marketplace-testdrive.videos": [],
             "microsoft-azure-marketplace.title": "Contoso App",
             "microsoft-azure-marketplace.summary": "Contoso App makes dev ops a breeze",
             "microsoft-azure-marketplace.longSummary": "Contoso App makes dev ops a breeze",
             "microsoft-azure-marketplace.description": "Contoso App makes dev ops a breeze",
             "microsoft-azure-marketplace.offerMarketingUrlIdentifier": "contosoapp",
             "microsoft-azure-marketplace.allowedSubscriptions": 
             [
                 "59160c40-2e25-4dcf-a2fd-6514cb08bf08"
             ],
             "microsoft-azure-marketplace.usefulLinks": 
             [
                 {
                     "linkTitle": "Contoso App for Azure",
                     "linkUrl": "https://azuremarketplace.microsoft.com"
                 }
             ],
             "microsoft-azure-marketplace.categoryMap":
             [
                 {
                    "categoryL1": "analytics",
                    "categoryL2-analytics": [
                    "visualization-and-reporting"
                    ]
                 },
                 {
                    "categoryL1": "ai-plus-machine-learning",
                    "categoryL2-ai-plus-machine-learning": [
                    "bot-services",
                    "cognitive-services",
                    "other"
                    ]
                 }
             ],
             "microsoft-azure-marketplace.smallLogo": "https://publishingstoredm.blob.core.windows.net/prodcontent/D6191_publishers_marketplace:2Dtest/testaoffer/8affcd28-60a5-4839-adf8-c560e069fd61.png?sv=2014-02-14&sr=b&sig=nGErAgn%2BDUecrX892wcmk32kh0MHgIZeJ5jcKyY%2Fuew%3D&se=2020-03-28T22%3A27%3A13Z&sp=r",
             "microsoft-azure-marketplace.mediumLogo": "https://publishingstoredm.blob.core.windows.net/prodcontent/D6191_publishers_marketplace:2Dtest/testaoffer/39550bca-1110-432c-9ea9-e12b3a2288cd.png?sv=2014-02-14&sr=b&sig=4X0hlkXYtuZOmcYq%2BsbYVZz3k5k26kngcFX6yBAJjNI%3D&se=2020-03-28T22%3A27%3A13Z&sp=r",
             "microsoft-azure-marketplace.largeLogo": "https://publishingstoredm.blob.core.windows.net/prodcontent/D6191_publishers_marketplace:2Dtest/testaoffer/ce3576e3-df12-4074-b0a3-0b8d3f329df1.png?sv=2014-02-14&sr=b&sig=mFhtCUQh%2FbFz10nlIWbqsz6jq5MBZ0M%2F5cIREE9P6V0%3D&se=2020-03-28T22%3A27%3A13Z&sp=r",
             "microsoft-azure-marketplace.wideLogo": "https://publishingstoredm.blob.core.windows.net/prodcontent/D6191_publishers_marketplace:2Dtest/testaoffer/476d6edd-12d3-4414-9def-d2970c4a9de4.png?sv=2014-02-14&sr=b&sig=pg4MDSZjAb8w8D%2FrQ9RT%2BodpynSy%2FlYOvpx0yeam2Bw%3D&se=2020-03-28T22%3A27%3A13Z&sp=r",
             "microsoft-azure-marketplace.heroLogo": "https://publishingstoredm.blob.core.windows.net/prodcontent/D6191_publishers_marketplace:2Dtest/testaoffer/46c85b7b-4438-4e0d-8218-24fb5651727a.png?sv=2014-02-14&sr=b&sig=wIsCOO5%2BDj8NsLVSwwzwTgogF71oA7Q1XjKhNB1ni5g%3D&se=2020-03-28T22%3A27%3A13Z&sp=r",
             "microsoft-azure-marketplace.screenshots": [],
             "microsoft-azure-marketplace.videos": [],
             "microsoft-azure-marketplace.leadDestination": "None",
             "microsoft-azure-marketplace.tableLeadConfiguration": {},
             "microsoft-azure-marketplace.blobLeadConfiguration": {},
             "microsoft-azure-marketplace.salesForceLeadConfiguration": {},
             "microsoft-azure-marketplace.crmLeadConfiguration": {},
             "microsoft-azure-marketplace.httpsEndpointLeadConfiguration": {},
             "microsoft-azure-marketplace.marketoLeadConfiguration": {},
             "microsoft-azure-marketplace.privacyURL": "https://azuremarketplace.microsoft.com",
             "microsoft-azure-marketplace.termsOfUse": "Terms of use",
             "microsoft-azure-marketplace.engineeringContactName": "Jon Doe",
             "microsoft-azure-marketplace.engineeringContactEmail": "jondoe@outlook.com",
             "microsoft-azure-marketplace.engineeringContactPhone": "555-555-5555",
             "microsoft-azure-marketplace.supportContactName": "Jon Doe",
             "microsoft-azure-marketplace.supportContactEmail": "jondoe@outlook.com",
             "microsoft-azure-marketplace.supportContactPhone": "555-555-5555",
             "microsoft-azure-marketplace.publicAzureSupportUrl": "",
             "microsoft-azure-marketplace.fairfaxSupportUrl": ""
         },
         "plans": 
         [
             {
                 "planId": "contososkuidentifier",
                 "microsoft-azure-virtualmachines.skuTitle": "Contoso App (Old Title)",
                 "microsoft-azure-virtualmachines.skuSummary": "Contoso App makes dev ops a breeze.",
                 "microsoft-azure-virtualmachines.skuDescription": "This is a description for the Contoso App that makes dev ops a breeze.",
                 "microsoft-azure-virtualmachines.hideSKUForSolutionTemplate": false,
                 "microsoft-azure-virtualmachines.cloudAvailability": 
                 [
                     "PublicAzure"
                 ],
                 "microsoft-azure-virtualmachines.certificationsFairfax": [],
                 "virtualMachinePricing": {
                     "isByol": true,
                     "freeTrialDurationInMonths": 0
                 },
                 "microsoft-azure-virtualmachines.operatingSystemFamily": "Windows",
                 "microsoft-azure-virtualmachines.operationSystem": "Contoso App",
                 "microsoft-azure-virtualmachines.recommendedVMSizes": 
                 [
                     "a0-basic",
                     "a0-standard",
                     "a1-basic",
                     "a1-standard",
                     "a2-basic",
                     "a2-standard"
                 ],
                 "microsoft-azure-virtualmachines.openPorts": [],
                 "microsoft-azure-virtualmachines.vmImages": 
                 {
                     "1.0.1": 
                     {
                         "osVhdUrl": "http://contosoteststorage.blob.core.windows.net/test/contosoVM.vhd?sv=2014-02-14&sr=c&sig=WlDo6Q4xwYH%2B5QEJbItPUVdgHhBcrVxPBmntZ2vU96w%3D&st=2016-06-25T18%3A30%3A00Z&se=2017-06-25T18%3A30%3A00Z&sp=rl",
                         "lunVhdDetails": []
                     }
                 },
                 "regions":
                 [
                     "AZ"
                 ]
             } 
         ]
     },
     "changedTime": "2018-03-28T22:27:13.8363879Z"
 }
```

> [!NOTE]
> Bu teklifi değiştirmek için yukarıdaki isteğe bir **IF-Match** üst bilgisini * olarak ayarlayın. Yukarıdaki gibi aynı istek gövdesini kullanın, ancak değerleri istediğiniz şekilde değiştirin. 

### <a name="response-status-codes"></a>Yanıt durum kodları

| **Kod**  |  **Açıklama**                                                                            |
| --------  |  ---------------                                                                            |
|  200      | `OK`. İstek başarıyla işlendi ve teklif başarıyla değiştirildi.           |
|  201      | `Created`. İstek başarıyla işlendi ve teklif başarıyla oluşturuldu.   |
|  400      | `Bad/Malformed request`. Hata yanıtı gövdesi daha fazla bilgi sağlayabilir.            |
|  403      | `Forbidden`. İstemcinin istenen ad alanına erişimi yok.                     |
|  404      | `Not found`. İstemci tarafından başvurulan varlık yok.                           |
|  412      | Sunucu istekte bulunan istekten birini karşılamıyor. İstemci, istekle gönderilen ETAG öğesini denetlemelidir. |
|  |  |

## <a name="uploading-artifacts"></a>Yapıtlar karşıya yükleniyor

Görüntüler ve amblemler gibi yapıtlar, Web üzerinde erişilebilir bir konuma karşıya yükleyerek ve ardından her biri, yukarıdaki örnekte olduğu gibi, PUT isteğinde bir URI olarak dahil olmak üzere paylaşılmalıdır. Sistem bu dosyaların Azure Market depolamada mevcut olup olmadığı tespit eder ve bu dosyaları depolama alanına indirir.  Sonuç olarak, gelecekteki GET isteklerinin bu dosyalar için bir Azure Market hizmeti URL 'SI döndürabileceğini görürsünüz.

## <a name="categories-and-industries"></a>Kategoriler ve endüstriler

Yeni bir teklif oluştururken, Market 'te teklifiniz için bir kategori belirtmeniz gerekir. İsteğe bağlı olarak, bazı teklif türleri için sektörlerin de belirtebilirsiniz. Teklif türüne bağlı olarak, aşağıdaki tablolardaki belirli anahtar değerlerini kullanarak teklif için geçerli olan kategorileri/endüstrileri sağlayın.

### <a name="azure-marketplace-categories"></a>Azure Market Kategorileri

Bu kategoriler ve ilgili anahtarları Azure uygulamaları, sanal makineler, çekirdek sanal makineler, kapsayıcılar, kapsayıcı uygulamaları, IoT Edge modüller ve SaaS teklif türleri için geçerlidir. Kalın italik ( ***analiz*** gibi) öğeler Kategoriler ve standart metin öğeleri (Data-Insights gibi) bunların alt kategorileridir. Boşluk veya büyük harfleri değiştirmeden tam anahtar değerlerini kullanın.

| Kategori | SaaS tuşları | Azure uygulama anahtarları | Sanal makine, kapsayıcılar, kapsayıcı uygulamaları, IoT Edge modülü, çekirdek sanal makine anahtarları |
| --- | --- | --- | --- |
| ***Analytics** _ | _*_analiz_*_ | _*_Analytics-Azure-uygulamalar_*_ | _ *_Analytics-amp_** |
| Veri öngörüleri | veri öngörüleri | veri öngörüleri | veri öngörüleri |
| Veri Analizi | Veri Analizi | Veri Analizi | Veri Analizi |
| Büyük Veri | büyük veri | bigData | büyük veri |
| Tahmine Dayalı Analiz | tahmine dayalı analiz | tahmine dayalı analiz | tahmine dayalı analiz |
| Gerçek zamanlı/Akış Analizi | gerçek zamanlı Akış Analizi | gerçek zamanlı Akış Analizi | gerçek zamanlı Akış Analizi |
| Diğer | other | diğer analiz | other |
| ***AI + Machine Learning** _ | _*_ArtificialIntelligence_*_ | _*_AI-Plus-makine öğrenimi_*_ | _ *_AI-Plus-makine öğrenimi_** |
| Bot Hizmetleri | bot-hizmetler | bot-hizmetler | bot-hizmetler |
| Bilişsel Hizmetler | bilişsel hizmetler | bilişsel hizmetler | bilişsel hizmetler |
| ML hizmeti | ml-hizmeti | ml-hizmeti | ml-hizmeti |
| Otomatikleştirilmiş ML | otomatikleştirilen-ml | otomatikleştirilen-ml | otomatikleştirilen-ml |
| İş/robot Işlem Otomasyonu | iş-robot-işlem-otomasyon | iş-robot-işlem-otomasyon | iş-robot-işlem-otomasyon |
| Veri etiketlemeyi | veri etiketlemeyi | veri etiketlemeyi | veri etiketlemeyi |
| Veri Hazırlama | veri hazırlama | veri hazırlama | veri hazırlama |
| Bilgi madenciliği | bilgi-araştırma | bilgi-araştırma | bilgi-araştırma |
| ML Işlemleri | ml-işlemler | ml-işlemler | ml-işlemler |
| Diğer | diğer-AI-Plus-makine öğrenimi | other | other |
| ***Blok zinciri** _ | _*_blok zinciri_*_ | _*_blok zinciri_*_ | _ *_blok zinciri_** |
| Uygulama hızlandırıcıları | uygulama-Hızlandırıcılar | uygulama-Hızlandırıcılar | uygulama-Hızlandırıcılar |
| Tek düğümlü defter | tek düğümlü-defter | tek düğümlü-defter | tek düğümlü-defter |
| Çok düğümlü defter | çok düğümlü-defter | çok düğümlü-defter | çok düğümlü-defter |
| Araçlar | araçlar | araçlar | araçlar |
| Diğer | other | other | other |
| ***İşlem** _ | _*_işlem-SaaS_*_ | _*_işlem-Azure-uygulamalar_*_ | _ *_işlem_** |
| Uygulama altyapısı | appInfra | Appınfrastructure | uygulama altyapısı |
| İşletim Sistemleri | clientOS | clientOS | işletim sistemleri |
| Önbellek | cache | cache | cache |
| Diğer | diğer işlem | diğer işlem | other |
| ***Kapsayıcılar** _ | _*_kapsayıcıları_*_ | _*_kapsayıcıları_*_ | _ *_kapsayıcıları_** |
| Kapsayıcı uygulamaları | kapsayıcı-uygulamalar | kapsayıcı-uygulamalar | kapsayıcı-uygulamalar |
| Kapsayıcı görüntüleri | kapsayıcı-görüntüler | kapsayıcı-görüntüler | kapsayıcı-görüntüler |
| Kapsayıcıları kullanmaya başlama | ile çalışmaya başlama-kapsayıcılar | ile çalışmaya başlama-kapsayıcılar | ile çalışmaya başlama-kapsayıcılar |
| Diğer | other | other | other |
| ***Veritabanları** _ | _*_veritabanları-SaaS_*_ | _*_veritabanınızı_*_ | _ *_veritabanları_** |
| NoSQL veritabanları | NoSQL veritabanları | NoSQL veritabanları | NoSQL veritabanları |
| İlişkisel Veritabanları | ilişkisel veritabanları | ilişkisel veritabanları | ilişkisel veritabanları |
| Muhasebe/blok zinciri veritabanları | muhasebe-blok zinciri-veritabanları | muhasebe-blok zinciri-veritabanları | muhasebe-blok zinciri-veritabanları |
| Veri Lakes | veri-Lakes | veri-Lakes | veri-Lakes |
| Veri Ambarı | veri ambarı | veri ambarı | veri ambarı |
| Diğer | diğer veritabanları | diğer veritabanları | other |
| ***Geliştirici Araçları** _ | _*_Geliştirici-Araçlar-SaaS_*_ | _*_Geliştirici-Araçlar-Azure-uygulamalar_*_ | _ *_Geliştirici-Araçlar_** |
| Araçlar | Araçlar-Geliştirici-Araçlar | Araçlar-Geliştirici-Araçlar | Araçlar-Geliştirici-Araçlar |
| Betikler | betikler | betikler | betikler |
| Geliştirici hizmeti | devService | devService | geliştirici-hizmet |
| Diğer | Diğer geliştirici araçları | Diğer geliştirici araçları | other |
| ***DevOps** _ | _*_DevOps_*_ | _*_DevOps_*_ | _ *_DevOps_** |
| Diğer | other | other | other |
| ***Identity** _ | _*_IDENTITY_*_ | _*_IDENTITY_*_ | _ *_kimlik_** |
| Erişim yönetimi | erişim-yönetim | erişim-yönetim | erişim-yönetim |
| Diğer | other | other | other |
| ***Tümleştirme** _ | _*_Tümleştirme_*_ | _*_Tümleştirme_*_ | _ *_tümleştirmesi_** |
| Mesajlaşma | gönderip | gönderip | gönderip |
| Diğer | other | other | other |
| ***Nesnelerin interneti** _ | _*_IoT_*_ | _*_Nesnelerin İnterneti-Azure-Apps_*_ | _ nesnelerin *_interneti_** |
| IoT Çekirdek Hizmetleri | Yok | IoT-Çekirdek Hizmetleri | IoT-Çekirdek Hizmetleri |
| IoT Edge modüller | Yok | IoT-Edge-modüller | IoT-Edge-modüller |
| IoT çözümleri | IoT-çözümler | IoT-çözümler | IoT-çözümler |
| Veri Analizi & görselleştirme | veri analizi ve görselleştirme | veri analizi ve görselleştirme | veri analizi ve görselleştirme |
| IoT bağlantısı | IoT-bağlantı | IoT-bağlantı | IoT-bağlantı |
| Diğer | diğer-çeşitli şeyler | diğer-çeşitli şeyler | other |
| ***Bt & yönetim araçları** _ | _*_ITandAdministration_*_ | _*_BT-ve-yönetim-araçlar-Azure-uygulamalar_*_ | _ *_BT-ve-yönetim-araçlar_** |
| Yönetim Çözümleri | Yönetim-çözümler | Yönetim-çözümler | Yönetim-çözümler |
| İş Uygulamaları | businessApplication | businessApplication | iş uygulamaları |
| Diğer | diğer BT yönetimi araçları | diğer BT yönetimi araçları | other |
| ***Tanılama & izleme** _ | _*_izleme ve tanılama_*_ | _*_izleme ve tanılama_*_ | _ *_izleme ve tanılama_** |
| Diğer | other | other | other |
| ***Medya** _ | _*_medyasını_*_ | _*_medyasını_*_ | _ *_medya_** |
| Media Services | media-services | media-services | media-services |
| Content Protection | içerik koruma | içerik koruma | içerik koruma |
| Isteğe bağlı akış & canlı | canlı ve isteğe bağlı akış | canlı ve isteğe bağlı akış | canlı ve isteğe bağlı akış |
| Diğer | other | other | other |
| ***Geçiş** _ | _*_geçiş_*_ | _*_geçiş_*_ | _ *_geçişi_** |
| Veri Taşıma | veri geçişi | veri geçişi | veri geçişi |
| Diğer | other | other | other |
| ***Karma gerçeklik** _ | _*_Karma Gerçeklik_*_ | _*_Karma Gerçeklik_*_ | _ *_karma gerçeklik_** |
| Diğer | other | other | other |
| ***Ağ** _ | _*_işlemleri_*_ | _*_işlemleri_*_ | _ *_ağ iletişimi_** |
| Gereç yöneticileri | Gereç-Yöneticiler | Gereç-Yöneticiler | Gereç-Yöneticiler |
| Bağlantı | bağlantı | bağlantı | bağlantı |
| Güvenlik duvarları | duvar | duvar | duvar |
| Yük Dengeleyiciler | yük dengeleyiciler | yük dengeleyiciler | yük dengeleyiciler |
| Diğer | other | other | other |
| ***Güvenlik** _ | _*_güven_*_ | _*_güven_*_ | _ *_güvenlik_** |
| Kimlik & erişim yönetimi | kimlik ve erişim yönetimi | kimlik ve erişim yönetimi | kimlik ve erişim yönetimi |
| Tehdit Koruması | tehdit koruması | tehdit koruması | tehdit koruması |
| Information Protection | bilgi koruma | bilgi koruma | bilgi koruma |
| Diğer | other | other | other |
| ***Depolama** _ | _*_depolama-SaaS_*_ | _*_depolama-Azure-uygulamalar_*_ | _ *_depolama alanı_** |
| Yedekleme & kurtarma | yedekleme | yedekleme | Yedekleme ve kurtarma |
| Kurumsal karma depolama | Kurumsal karma depolama | Kurumsal karma depolama | Kurumsal karma depolama |
| Dosya paylaşımı | dosya paylaşımı | dosya paylaşımı | dosya paylaşımı |
| Veri yaşam döngüsü yönetimi | veri yaşam döngüsü yönetimi | veri yaşam döngüsü yönetimi | veri yaşam döngüsü yönetimi |
| Diğer | diğer depolama alanı | diğer depolama alanı | other |
| ***Web** _ | _*_Web_*_ | _*_Web_*_ | _ *_Web_** |
| Web günlükleri & CMSs | Bloglar-ve-CMSS | Bloglar-ve-CMSS | Bloglar-ve-CMSS |
| Başlangıç Web Apps | Starter-Web-Apps | Starter-Web-Apps | Starter-Web-Apps |
| E-ticaret | e-ticaret | e-ticaret | e-ticaret |
| Web Apps çerçeveler | Web uygulamaları-çerçeveler | Web uygulamaları-çerçeveler | Web uygulamaları-çerçeveler |
| Web Apps | Web uygulamaları | Web uygulamaları | Web uygulamaları |
| Diğer | other | other | other |
||||

### <a name="microsoft-appsource-categories"></a>Microsoft AppSource kategorileri

Bu kategoriler ve ilgili anahtarları SaaS, PowerBI uygulaması, Dynamics 365 İş Merkezi, müşteri katılımı için Dynamics 365 ve işlem teklifi türleri için Dynamics 365 için geçerlidir. Kalın italik ( ***analiz*** gibi) öğeleri Kategoriler ve standart metin öğeleri (gelişmiş analiz gibi) bunların alt kategorileridir. Boşluk veya büyük harfleri değiştirmeden tam anahtar değerlerini kullanın.

| Kategori | SaaS tuşları | Dynamics 365 İş Merkezi, müşteri katılımı için Dynamics 365, işlem anahtarları için Dynamics 365 | PowerBI uygulama anahtarları |
| --- | --- | --- | --- |
| ***Analytics** _ | _*_analiz_*_ | _*_Analiz_*_ | _ *_Analiz_** |
| Gelişmiş Analiz | Gelişmiş-analiz | Gelişmiş-analiz | Gelişmiş-analiz |
| Görselleştirme & raporlama | görselleştirme-raporlama | görselleştirme-raporlama | görselleştirme-raporlama |
| Diğer | other | diğer analiz | diğer analiz |
| ***AI + Machine Learning** _ | _*_ArtificialIntelligence_*_ | _*_AI-Plus-Machine-Learning-Dynamics_*_ | _ *_AI-Plus-makine-öğrenme-appsource_** |
| Iş için AI | iş için AI | iş için AI | iş için AI |
| Bot uygulamaları | bot-uygulamalar | bot-uygulamalar | bot-uygulamalar |
| Diğer | diğer-AI-Plus-makine öğrenimi | diğer-AI-Plus-makine öğrenimi | diğer-AI-Plus-makine öğrenimi |
| ***İşbirliği** _ | _*_İş_*_ | _*_İş_*_ | _ *_işbirliği_** |
| & kişilerle iletişim kurun | kişi-kişiler | kişi-kişiler | kişi ve kişiler |
| Toplantı Yönetimi | Toplantı-yönetim | Toplantı-yönetim | Toplantı-yönetim |
| Site tasarımı & yönetimi | Site-Tasarım-yönetim | Site-Tasarım-yönetim | site tasarımı ve yönetimi |
| Görev & proje yönetimi | görev-proje-yönetim | görev-proje-yönetim | görev-ve-proje-yönetim |
| Sesli & görüntülü konferans | ses video konferansı | ses video konferansı | Ses ve video konferansı |
| Diğer | diğer işbirliği | diğer işbirliği | other |
| ***Uyumluluk & geçerli** _ | _*_Uyumluluk_*_ | _*_Uyumluluk_*_ | _ *_Uyumluluk-ve-yasal_** |
| Vergi & denetimi | Vergi-Denetim | Vergi-Denetim | vergi ve-denetim |
| Yasal Bildirim | Yasal Bildirim | Yasal Bildirim | kanun |
| Veri, Idare & gizliliği | veri yönetimi-Gizlilik | veri yönetimi-Gizlilik | veri yönetimi ve Gizlilik |
| Sistem durumu & güvenliği | sistem durumu-güvenlik | sistem durumu-güvenlik | sistem durumu ve güvenlik |
| Diğer | diğer-uyumluluk-yasal | diğer-uyumluluk-yasal | other |
| ***Müşteri hizmeti** _ | _*_CustomerService_*_ | _*_CustomerService_*_ | _ *_müşteri-hizmet_** |
| Kişi Merkezi | İletişim Merkezi | İletişim Merkezi | İletişim Merkezi |
| Yüz yüze hizmet | yüz yüze hizmet | yüz yüze hizmet | yüz yüze hizmet |
| Arka ofis & çalışan hizmeti | arka ofis-çalışan-hizmet | arka ofis-çalışan-hizmet | arka ofis-ve-Employee-Service |
| Bilgi & durum yönetimi | bilgi talebi-yönetim | bilgi talebi-yönetim | bilgi ve durum yönetimi |
| Sosyal medya & omnichannel katılımı | Sosyal medya-omnichannel-katılım | Sosyal medya-omnichannel-katılım | Sosyal medya-ve-omnichannel-katılım |
| Diğer | diğer müşteri hizmetleri | diğer müşteri hizmetleri | other |
| ***Finans** _ | _*_Finance_*_ | _*_Finance_*_ | _ *_finans_** |
| Muhasebe | accounting (muhasebe) | accounting (muhasebe) | accounting (muhasebe) |
| Varlık Yönetimi | varlık yönetimi | varlık yönetimi | varlık yönetimi |
| Analiz, birleştirme & raporlama | Analiz-birleştirme-raporlama | Analiz-birleştirme-raporlama | Analiz-birleştirme-ve-raporlama |
| & koleksiyonları kredi | Kredi-Koleksiyonlar | Kredi-Koleksiyonlar | kredi ve koleksiyonlar |
| Uyumluluk & risk yönetimi | Uyumluluk-risk yönetimi | Uyumluluk-risk yönetimi | Uyumluluk ve risk yönetimi |
| Diğer | diğer finans | diğer finans | other |
| ***Insan kaynakları** _ | _*_Insankaynakları_*_ | _*_Insankaynakları_*_ | _ *_insan kaynakları_** |
| Taödünme alma | taödünçi alma | taödünçi alma | taödünçi alma |
| Taödünme yönetimi | taödünme yönetimi | taödünme yönetimi | taödünme yönetimi |
| HR Işlemleri | HR-işlemler | HR-işlemler | HR-işlemler |
| İş gücü planlama & Analizi | iş gücü-planlama-analiz | iş gücü-planlama-analiz | iş gücü-planlama ve analiz |
| Diğer | diğer insan kaynakları | diğer insan kaynakları | other |
| ***Nesnelerin interneti** _ | _*_IoT_*_ | _*_Nesnelerin İnterneti-Dynamics_*_ | _ *_nesnelerin interneti-appsource_** |
| Varlık yönetimi & Işlemleri | varlık yönetimi-işlemler | varlık yönetimi-işlemler | varlık yönetimi-ve-işlemler |
| Bağlı ürünler | bağlı-ürünler | bağlı-ürünler | bağlı-ürünler |
| Akıllı tedarik zinciri | Akıllı tedarik zinciri | Akıllı tedarik zinciri | Akıllı tedarik zinciri |
| Tahmine Dayalı Bakım | tahmine dayalı-bakım | tahmine dayalı-bakım | tahmine dayalı-bakım |
| Uzaktan İzleme | Uzaktan izleme | Uzaktan izleme | Uzaktan izleme |
| Güvenlik & güvenliği | Güvenlik-güvenlik | Güvenlik-güvenlik | Güvenlik ve güvenlik |
| Akıllı altyapı & kaynakları | akıllı altyapı-kaynaklar | akıllı altyapı-kaynaklar | akıllı altyapı-ve-kaynaklar |
| & hareket eden araçlar | Araçlar-taşınabilirlik | Araçlar-taşınabilirlik | Araçlar-ve-taşınabilirlik |
| Diğer | diğer-çeşitli şeyler | diğer-çeşitli şeyler | other |
| ***Bt & yönetim araçları** _ | _*_ITandAdministration_*_ | _*_ITandAdministration_*_ | _ *_BT-ve-yönetim-araçlar_** |
| Yönetim Çözümleri | Yönetim-çözümler | Yönetim-çözümler | Yönetim-çözümler |
| İş Uygulamaları | businessApplication | businessApplication | iş uygulamaları |
| Diğer | diğer BT yönetimi araçları | diğer BT yönetimi araçları | other |
| ***Pazarlama** _ | _*_Marketing_*_ | _*_Marketing_*_ | _ *_Pazarlama_** |
| Bildir | bildir | bildir | bildir |
| Analiz | analiz-pazarlama | analiz-pazarlama | analiz-pazarlama |
| Kampanya Yönetimi & Otomasyonu | kampanya-yönetim-Otomasyon | kampanya-yönetim-Otomasyon | kampanya-yönetim-ve otomasyon |
| E-posta pazarlama | e-posta-pazarlama | e-posta-pazarlama | e-posta-pazarlama |
| L2-Events & kaynak yönetimi | olaylar-kaynak yönetimi | olaylar-kaynak yönetimi | olaylar-ve-kaynak yönetimi |
| Araştırma & Analizi | araştırma Analizi | araştırma Analizi | araştırma ve analiz |
| Sosyal medya | Sosyal medya | Sosyal medya | Sosyal medya |
| Diğer | diğer-pazarlama | diğer-pazarlama | other |
| ***Operations & tedarik zinciri** _ | _*_Operationssupplyzincirde_*_ | _*_Operationssupplyzincirde_*_ | _ *_işlemler-ve-tedarik zinciri_** |
| Varlık & üretim yönetimi | varlık-üretim-yönetim | varlık-üretim-yönetim | varlık-ve-üretim-yönetim |
| Talep Tahmini | talep tahmini | talep tahmini | talep tahmini |
| Bilgi yönetimi & bağlantısı | bilgi-yönetim-bağlantı | bilgi-yönetim-bağlantı | bilgi-yönetim ve-bağlantı |
| Planlama, satın alma & raporlama | planlama-satın alma-raporlama | planlama-satın alma-raporlama | Planlama-ve-raporlama |
| Quality & hizmet yönetimi | kalite-hizmet yönetimi | kalite-hizmet yönetimi | kalite ve hizmet yönetimi |
| Satış & sipariş yönetimi | Satış siparişi yönetimi | Satış siparişi yönetimi | Satış ve sipariş yönetimi |
| Taşıma & ambarı yönetimi | taşıma-ambar-yönetim | taşıma-ambar-yönetim | taşıma ve ambar-yönetim |
| Diğer | diğer işlemler-tedarik zinciri | diğer işlemler-tedarik zinciri | other |
| ***Üretkenlik** _ | _*_Üretkenlik_*_ | _*_Üretkenlik_*_ | _ *_üretkenlik_** |
| İçerik oluşturma & yönetimi | içerik oluşturma-yönetim | içerik oluşturma-yönetim | içerik oluşturma ve yönetme |
| Dil & çevirisi | dil çevirisi | dil çevirisi | dil ve çeviri |
| Belge Yönetimi | belge yönetimi | belge yönetimi | belge yönetimi |
| E-posta yönetimi | e-posta yönetimi | e-posta yönetimi | e-posta yönetimi |
| & başvuru ara | arama başvurusu | arama başvurusu | Arama ve başvuru |
| Diğer | diğer üretkenlik | diğer üretkenlik | other |
| Oyunlaştırma | Oyunlaştırma | Oyunlaştırma | oyunlaştırma |
| ***Sales** _ | _*_Sales_*_ | _*_Sales_*_ | _ *_Satış_** |
| Telesales | Telesales | Telesales | Telesales |
| Yapılandırma, Fiyat, teklif (CPQ) | Configure-Price-quote | Configure-Price-quote | Configure-Price-quote |
| Anlaşma Yönetimi | Sözleşme yönetimi | Sözleşme yönetimi | Sözleşme yönetimi |
| CRM | crm | crm | crm |
| E-ticaret | e-ticaret | e-ticaret | e-ticaret |
| İş verileri zenginleştirme | İşletme verileri-zenginleştirme | İşletme verileri-zenginleştirme | İşletme verileri-zenginleştirme |
| Satış etkinleştirme | Satış-etkinleştirme | Satış-etkinleştirme | Satış-etkinleştirme |
| Diğer | diğer-satış | diğer-satış | diğer-satış |
| ***Coğrafi konum** _ | _*_konumu_*_ | _*_konumu_*_ | _ *_coğrafi konum_** |
| Haritalar | maps | maps | maps |
| Haber & Hava durumu | Haberler-ve hava durumu | Haberler-ve hava durumu | Haberler-ve hava durumu |
| Diğer | diğer-coğrafi konum | diğer-coğrafi konum | diğer-coğrafi konum |
||||

### <a name="microsoft-appsource-industries"></a>Microsoft AppSource endüstriler

Bu endüstriler ve ilgili anahtarları SaaS, PowerBI uygulaması, Dynamics 365 İş Merkezi, müşteri katılımı için Dynamics 365 ve işlem teklifi türleri için Dynamics 365 için geçerlidir. Kalın italik (örneğin, ***oto***) içindeki öğeler Kategoriler ve standart metin öğeleri (AutomotiveL2 gibi) bunların alt kategorileridir. Boşluk veya büyük harfleri değiştirmeden tam anahtar değerlerini kullanın.

| Sektör | SaaS, Dynamics 365 İş Merkezi, müşteri katılımı için Dynamics 365, işlem anahtarları için Dynamics 365 | PowerBI uygulama anahtarları |
| --- | --- | --- |
| ***Oto \ Molt** | _*_Otomotiv_*_ | _ *_oto_** |
| Otomotiv | AutomotiveL2 | AutomotiveL2 |
| ***Tarım** _ | _*_Tarım_*_ | _ *_Agric_** |
| Diğer-kesimli | Agric, \_ diğerunkesimli | diğer-kesimli |
| ***Dağıtım** _ | _*_Dağıtım_*_ | _ *_dağıtım_** |
| Toplu | Toplu | toplu |
| Paket Gönderimi & paket dağıtımı | ParcelAndPackageShipping | paket-ve-paket-gönderim |
| ***Eğitim** _ | _*_Eğitim_*_ | _ *_eğitim_** |
| Daha yüksek eğitim | HigherEducation | daha yüksek eğitim |
| Birincil & Ikincil Eğitim/K-12 | PrimaryAndSecondaryEducationK12 | birincil-ve-ikincil-eğitim |
| Kitaplıklar & Museums | Kütüphaneiesandmuseums | Kitaplıklar-ve-Museums |
| ***Finansal hizmetler** _ | _*_Mali hizmetler_*_ | _ *_Finans-hizmetler_** |
| Bankacılık & büyük pazarlar | BankingAndCapitalMarkets | Bankacılık-ve-sermaye-pazarlar |
| Eleri | Eleri | eleri |
| ***Kamu** _ | _*_Kamu_*_ | _ *_kamu_** |
| Savunma & zekası | DefenseAndIntelligence | Savunma ve zeka |
| Genel güvenlik & de | Publicsafetyandlet | Ortak Güvenlik-ve-Let |
| Cıvilian kamu | Cıvtik Ankamu | cıvilian-kamu |
| ***Sağlık**\ | _*_Healthgelişme ve kullanım ömrü_*_ | _ *_sağlık_** |
| Sistem durumu ödemi veya | HealthPayor | Sağlık-payor |
| Sistem durumu sağlayıcısı | HealthProvider | sistem durumu-sağlayıcı |
| Pharmaceuticals | Pharmaceuticals | Pharmaceuticals |
| ***& kaynaklarını üretim** | _*_Üretim_*_ | _ *_üretim-ve-kaynaklar_** |
| Kimyasal & Agrokimyasal | ChemicalAndAgrochemical | kimyasal ve-Agrokimyasal |
| Ayrık üretim | DiscreteManufacturing | ayrık üretim |
| Enerji | Enerji | energy |
| ***Perakende & tüketici malları** _ | _*_RetailandConsumerGoods_*_ | _ *_Perakende-ve-tüketici-mallar_** |
| Tüketici malları | Consumermallar | Tüketici-mallar |
| Tercih | Tercih | tercih |
| ***Medya & iletişimleri** _ | _*_MediaAndCommunications_*_ | _ *_medya-ve-iletişim_** |
| Medya & eğlence | Mediaandenterlationment | Medya-ve-eğlence |
| Telekomünikasyon | Telekomünikasyon | Telekomünikasyon |
| ***Profesyonel hizmetler** _ | _*_ProfessionalServices_*_ | _ *_Professional-hizmetler_** |
| Yasal Bildirim | Yasal Bildirim | kanun |
| İş ortağı profesyonel hizmetleri | PartnerProfessionalServices | iş ortağı-profesyonel hizmetler |
| ***Mimari & oluşturma** _ | _*_Mimari Tureandinşaat_*_ | _ *_mimari ve-yapım_** |
| Diğer-kesimli | Mimari Tureandinşaat \_ otherunkesimli | diğer-kesimli |
| ***& yolculuğu** | _*_Hospitalityandyolculuğu_*_ | _ *_hosititesi-ve-seyahat_** |
|    Oteller & boş | Hotelsandboş | oteller-ve-boş |
| Seyahat & ulaşım | Seyahat | seyahat ve ulaşım |
| Restoran hizmetleri & Restoranlar | Restoran Antsandağdservices | Restoranlar-ve yiyecek hizmetleri |
| ***Diğer kamu sektörü endüstrileri** | _*_Otherpublicsectorındustries_*_ | _ *_diğer-kamu sektörü-sektör_** |
| Ormancılık & | Forestryandfçılık | Ormancılık ve balıkçılık |
| Şartlara | Şartlara | şartlara |
| ***Emlak _** | _*_RealEstate_*_ | _ *_gerçek emlak_** |
| Diğer-kesimli | Reatastate \_ otherunkesimli | diğer-kesimli |
|||
