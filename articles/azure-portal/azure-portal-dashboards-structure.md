---
title: Azure panoları yapısı | Microsoft Docs
description: Bu makalede bir Azure panosunun JSON yapısı açıklanmaktadır
services: azure-portal
documentationcenter: ''
author: adamabmsft
manager: dougeby
editor: tysonn
ms.service: azure-portal
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 09/01/2017
ms.author: kfollis
ms.openlocfilehash: 5933521993b598ae3758df6e2e7dbf61bf424779
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73832788"
---
# <a name="the-structure-of-azure-dashboards"></a>Azure panoları yapısı
Bu belge, örnek olarak aşağıdaki Panoyu kullanarak bir Azure panosunun yapısını gösterir:

![örnek Pano](./media/azure-portal-dashboards-structure/sample-dashboard.png)

Paylaşılan [Azure panoları kaynak olduğundan](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview), bu pano JSON olarak temsil edilebilir.  Aşağıdaki JSON, yukarıda görselleştirilen panoyu temsil eder.

```json

{
    "properties": {
        "lenses": {
            "0": {
                "order": 0,
                "parts": {
                    "0": {
                        "position": {
                            "x": 0,
                            "y": 0,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [],
                            "type": "Extension[azure]/HubsExtension/PartType/MarkdownPart",
                            "settings": {
                                "content": {
                                    "settings": {
                                        "content": "## Azure Virtual Machines Overview\r\nNew team members should watch this video to get familiar with Azure Virtual Machines.",
                                        "title": "",
                                        "subtitle": ""
                                    }
                                }
                            }
                        }
                    },
                    "1": {
                        "position": {
                            "x": 3,
                            "y": 0,
                            "rowSpan": 4,
                            "colSpan": 8
                        },
                        "metadata": {
                            "inputs": [],
                            "type": "Extension[azure]/HubsExtension/PartType/MarkdownPart",
                            "settings": {
                                "content": {
                                    "settings": {
                                        "content": "This is the team dashboard for the test VM we use on our team. Here are some useful links:\r\n\r\n1. [Getting started](https://www.contoso.com/tsgs)\r\n1. [Troubleshooting guide](https://www.contoso.com/tsgs)\r\n1. [Architecture docs](https://www.contoso.com/tsgs)",
                                        "title": "Test VM Dashboard",
                                        "subtitle": "Contoso"
                                    }
                                }
                            }
                        }
                    },
                    "2": {
                        "position": {
                            "x": 0,
                            "y": 2,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [],
                            "type": "Extension[azure]/HubsExtension/PartType/VideoPart",
                            "settings": {
                                "content": {
                                    "settings": {
                                        "title": "",
                                        "subtitle": "",
                                        "src": "https://www.youtube.com/watch?v=YcylDIiKaSU&list=PLLasX02E8BPCsnETz0XAMfpLR1LIBqpgs&index=4",
                                        "autoplay": false
                                    }
                                }
                            }
                        }
                    },
                    "3": {
                        "position": {
                            "x": 0,
                            "y": 4,
                            "rowSpan": 3,
                            "colSpan": 11
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Percentage CPU",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "4": {
                        "position": {
                            "x": 0,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Disk Read Operations/Sec",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            },
                                            {
                                                "name": "Disk Write Operations/Sec",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "5": {
                        "position": {
                            "x": 3,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Disk Read Bytes",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            },
                                            {
                                                "name": "Disk Write Bytes",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "6": {
                        "position": {
                            "x": 6,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 3
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "queryInputs",
                                    "value": {
                                        "timespan": {
                                            "duration": "PT1H",
                                            "start": null,
                                            "end": null
                                        },
                                        "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
                                        "chartType": 0,
                                        "metrics": [
                                            {
                                                "name": "Network In",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            },
                                            {
                                                "name": "Network Out",
                                                "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                            }
                                        ]
                                    }
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart",
                            "settings": {}
                        }
                    },
                    "7": {
                        "position": {
                            "x": 9,
                            "y": 7,
                            "rowSpan": 2,
                            "colSpan": 2
                        },
                        "metadata": {
                            "inputs": [
                                {
                                    "name": "id",
                                    "value": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                                }
                            ],
                            "type": "Extension/Microsoft_Azure_Compute/PartType/VirtualMachinePart",
                            "asset": {
                                "idInputName": "id",
                                "type": "VirtualMachine"
                            },
                            "defaultMenuItemId": "overview"
                        }
                    }
                }
            }
        },
        "metadata": {
            "model": {
                "timeRange": {
                    "value": {
                        "relative": {
                            "duration": 24,
                            "timeUnit": 1
                        }
                    },
                    "type": "MsPortalFx.Composition.Configuration.ValueTypes.TimeRange"
                }
            }
        }
    },
    "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/dashboards/providers/Microsoft.Portal/dashboards/aa9786ae-e159-483f-b05f-1f7f767741a9",
    "name": "aa9786ae-e159-483f-b05f-1f7f767741a9",
    "type": "Microsoft.Portal/dashboards",
    "location": "eastasia",
    "tags": {
        "hidden-title": "Created via API"
    }
}

```

## <a name="common-resource-properties"></a>Ortak kaynak özellikleri

JSON 'ın ilgili bölümlerinin sonuna bakalım.  En üst düzey özellikler, __kimlik__, __ad__, __tür__, __konum__ve __Etiketler__ özellikleri tüm Azure Kaynak türleri arasında paylaşılır. Diğer bir deyişle, panonun içeriğiyle çok daha fazla şey yoktur.

### <a name="the-id-property"></a>ID özelliği

Azure kaynak kimliği, [Azure kaynakları adlandırma kurallarına](/azure/architecture/best-practices/resource-naming)tabidir. Portal bir pano oluşturduğunda genellikle GUID biçiminde bir kimlik seçer, ancak bunları programlı olarak oluşturduğunuzda geçerli bir ad kullanabilirsiniz. 

### <a name="the-name-property"></a>Name özelliği
Ad, abonelik, kaynak türü veya kaynak grubu bilgilerini içermeyen kaynak kimliğinin kesimdir. Esas olarak, kaynak kimliğinin son segmentinden oluşur.

### <a name="the-type-property"></a>Type özelliği
Tüm panolar __Microsoft. Portal/panolar__türündedir.

### <a name="the-location-property"></a>Location özelliği
Diğer kaynakların aksine, panolar çalışma zamanı bileşenine sahip değildir.  Panolar için konum, panonun JSON gösterimini depolayan birincil coğrafi konumu gösterir. Değer, [abonelikler kaynağında konumlar API 'si](https://docs.microsoft.com/rest/api/resources/subscriptions)kullanılarak getirilen konum kodlarından biri olmalıdır.

### <a name="the-tags-property"></a>Tags özelliği
Etiketler, Azure kaynaklarının, kaynağınızı rastgele ad değer çiftleriyle düzenlemenize olanak sağlayan ortak bir özelliktir. Panolar için __gizli-title__adlı bir özel etiket vardır. Panonuz bu özelliği doldurduktan sonra, portalda panonuz için görünen ad olarak kullanılır. Azure kaynak kimlikleri yeniden adlandırılamaz, ancak Etiketler olabilir. Bu etiket, panonuzda bir ekran adı vermek için bir yol sağlar.

`"tags": { "hidden-title": "Created via API" }`

### <a name="the-properties-object"></a>Properties nesnesi
Properties nesnesi iki özellik içerir, __mercekler__ ve __meta veriler__. __Merceklerden__ özelliği, kutucuklar hakkında bilgiler içerir (deyişle bölümleri) panoda.  __Meta veri__ özelliği, gelecekteki olası özellikler için mevcuttur.

### <a name="the-lenses-property"></a>Merceklerden özelliği
__Merceklerden__ özelliği panoyu içerir. Bu örnekteki mercekler nesnesinin "0" adlı tek bir özellik içerdiğini unutmayın. Mercekler, şu anda panolarda uygulanmayan bir gruplandırma kavramıdır. Şimdilik, Panolarınızın hepsi lens nesnesinde bu tek özelliğe sahiptir ve "0" olarak adlandırılır.

### <a name="the-lens-object"></a>Lens nesnesi
"0" altındaki nesne iki özellik, __Düzen__ ve __parça__içerir.  Panoların geçerli sürümünde, __Order__ her zaman 0 ' dır. __Parts__ özelliği bağımsız parçaları tanımlayan bir nesne içerir (deyişle Kutucuklar) Pano üzerinde.

__Bölümler__ nesnesi her bölüm için bir özellik içerir, burada özelliğin adı bir sayıdır. Bu sayı önemli değildir. 

### <a name="the-part-object"></a>Bölüm nesnesi
Her bir parça nesnesinin bir __konumu__ve __meta verileri__vardır.

### <a name="the-position-object"></a>Konum nesnesi
__Position__ özelliği, __x__, __y__, __RowSpan__ve __colspan__olarak ifade edilen bölüm için boyut ve konum bilgilerini içerir. Değerler, ızgara birimleri bakımından yapılır. Bu kılavuz birimleri, Pano özelleştirme modundayken burada gösterildiği gibi görünür. Bir kutucuğun iki ızgara birimi genişliği, bir ızgara birimi yüksekliği ve panonun sol üst köşesindeki bir konum olmasını istiyorsanız, konum nesnesi şöyle görünür:

`location: { x: 0, y: 0, rowSpan: 2, colSpan: 1 }`

![ızgara birimleri](./media/azure-portal-dashboards-structure/grid-units.png)

### <a name="the-metadata-object"></a>Meta veri nesnesi
Her bölümde bir meta veri özelliği bulunur, bir nesne, __Type__adlı yalnızca bir gerekli özelliğe sahiptir. Bu dize, portala hangi kutucuğun gösterileceğini söyler. Örnek panonuz bu tür kutucukları kullanır:


1. `Extension/Microsoft_Azure_Monitoring/PartType/MetricsChartPart` – izleme ölçümlerini göstermek için kullanılır
1. `Extension[azure]/HubsExtension/PartType/MarkdownPart`: listeler, bağlantılar vb. için temel biçimlendirmeye sahip metin veya görüntülerle göstermek için kullanılır.
1. `Extension[azure]/HubsExtension/PartType/VideoPart`: YouTube, Channel9 ve HTML video etiketinde kullanılan diğer herhangi bir video türü videoları göstermek için kullanılır.
1. `Extension/Microsoft_Azure_Compute/PartType/VirtualMachinePart`: bir Azure sanal makinesinin adını ve durumunu göstermek için kullanılır.

Her parça türünün kendi yapılandırması vardır. Olası yapılandırma özelliklerine __giriş__, __Ayarlar__ve __varlık__adı verilir. 

### <a name="the-inputs-object"></a>Girişler nesnesi
Girişler nesnesi genellikle bir kutucuğu kaynak örneğine bağlayan bilgiler içerir.  Örnek panomizdeki sanal makine bölümü, bağlamayı ifade etmek için Azure kaynak kimliği 'ni kullanan tek bir giriş içerir.  Bu kaynak kimliği biçimi tüm Azure kaynakları genelinde tutarlıdır.

```json
"inputs":
[
    {
        "name": "id",
        "value": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
    }
]

```
Ölçüm grafiği bölümünde, bağlanacak kaynağı ifade eden tek bir giriş ve görüntülenen ölçümler hakkında bilgiler vardır. Aşağıda ağı ve ağ çıkış ölçümlerini gösteren kutucuğun girişi verilmiştir.

```json
“inputs”:
[
    {
        "name": "queryInputs",
        "value": 
        {
            "timespan": 
            {
                "duration": "PT1H",
                "start": null,
                "end": null
           },
            "id": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1",
            "chartType": 0,
            "metrics": 
            [
                {
                    "name": "Network In",
                    "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                },
                {
                    "name": "Network Out",
                    "resourceId": "/subscriptions/6531c8c8-df32-4254-d717-b6e983273e5d/resourceGroups/contoso/providers/Microsoft.Compute/virtualMachines/myVM1"
                }
            ]
        }
    }
]

```

### <a name="the-settings-object"></a>Ayarlar nesnesi
Ayarlar nesnesi bir bölümün yapılandırılabilir öğelerini içerir.  Örnek panoımızda Markaşağı bölümü, özel markaşağı içeriği ve yapılandırılabilir bir başlık ve alt başlık depolamak için ayarları kullanır.

```json
"settings": 
{
    "content": 
    {
        "settings": 
        {
            "content": "This is the team dashboard for the test VM we use on our team. Here are some useful links:\r\n\r\n1. [Getting started](https://www.contoso.com/tsgs)\r\n1. [Troubleshooting guide](https://www.contoso.com/tsgs)\r\n1. [Architecture docs](https://www.contoso.com/tsgs)",
            "title": "Test VM Dashboard",
            "subtitle": "Contoso"
        }
    }
}

```

Benzer şekilde, video kutucuğunun yürütülmesi için videonun bir işaretçisini, bir Otomatik Kullan ayarını ve isteğe bağlı başlık bilgilerini içeren kendi ayarları vardır.

```json
"settings": 
{
   "content": 
    {
        "settings": 
        {
            "title": "",
            "subtitle": "",
            "src": "https://www.youtube.com/watch?v=YcylDIiKaSU&list=PLLasX02E8BPCsnETz0XAMfpLR1LIBqpgs&index=4",
            "autoplay": false
        }
    }
}

```

### <a name="the-asset-object"></a>Varlık nesnesi
Birinci sınıf yönetilebilir Portal nesnelerine (varlıklar olarak adlandırılır) bağlanan kutucuklar bu ilişkiye varlık nesnesi aracılığıyla ifade edilir.  Örnek panoımızda, sanal makine kutucuğu bu varlık açıklamasını içerir.  __Idınputname__ özelliği, portala kimlik girişinin varlık için benzersiz tanımlayıcıyı içerdiğini, bu durumda kaynak kimliğini belirtir. Çoğu Azure Kaynak türünün portalda tanımlanmış varlıkları vardır.

`"asset": {    "idInputName": "id",    "type": "VirtualMachine"    }`
