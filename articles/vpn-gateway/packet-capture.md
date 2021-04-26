---
title: 'Azure VPN Gateway: paket yakalamayı yapılandırma'
description: Bir sorunun nedenini daraltmaya yardımcı olması için VPN Gateway 'lerde kullanabileceğiniz paket yakalama işlevselliği hakkında bilgi edinin.
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alzam
ms.openlocfilehash: 0983139d1c9af235eba4c9f99da7bc9dea3f231b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101726623"
---
# <a name="configure-packet-capture-for-vpn-gateways"></a>VPN ağ geçitleri için paket yakalamayı yapılandırma

Bağlantı ve performansla ilgili sorunlar genellikle karmaşıktır. Bu, sorunun nedenini azaltmak için önemli ölçüde zaman ve çaba sürebilirler. Paket yakalama, bir sorunun kapsamını ağın belirli bölümlerine daraltmanıza yardımcı olabilir. Sorunun, ağın müşteri tarafında, ağın Azure tarafında veya arasında bir yerde olup olmadığını belirlemenize yardımcı olabilir. Sorunu daraltdıktan sonra, hata ayıklama ve düzeltici eylem gerçekleştirmek daha etkilidir.

Yaygın olarak kullanılan bazı paket yakalama araçları vardır. Bu araçlarla ilgili paket yakalamalarını almak, özellikle de yüksek hacimli trafik senaryolarında çok daha fazla olabilir. Azure VPN Gateway paket yakalama tarafından sunulan filtreleme özellikleri, önemli bir farklıtır. VPN Gateway paket yakalamayı, yaygın olarak kullanılan paket yakalama araçlarıyla birlikte kullanabilirsiniz.

## <a name="vpn-gateway-packet-capture-filtering-capabilities"></a>Paket yakalama filtreleme özelliklerini VPN Gateway

Gereksinimlerinize bağlı olarak, ağ geçidinde veya belirli bir bağlantıda VPN Gateway paket yakalamayı çalıştırabilirsiniz. Ayrıca aynı anda birden çok tünelde paket yakalamayı çalıştırabilirsiniz. Tek yönlü veya çift yönlü trafiği, ıKE ve ESP trafiğini, iç paketleri ve bir VPN ağ geçidi üzerinde filtreleme ile birlikte yakalayabilirsiniz.

Yüksek hacimli trafikte sorunları yalıtdığınızda beş demet filtresi (kaynak alt ağ, hedef alt ağ, kaynak bağlantı noktası, hedef bağlantı noktası, protokol) ve TCP bayrakları (SYN, ACK, FIN, URG, PSH, RST) kullanılması yararlı olur.

Aşağıdaki JSON ve bir JSON şeması örnekleri her bir özelliğin açıklamalarını sağlar. Paket yakalamalarını çalıştırdığınızda aklınızda bulundurmanız gereken bazı sınırlamalar şunlardır:

- Burada gösterilen şemada, filtre bir dizidir, ancak şu anda yalnızca bir filtre kullanılabilir.
- Aynı anda birden çok ağ geçidi genelinde paket yakalamalarını çalıştıramazsınız.
- Tek bir bağlantıda birden çok paket yakalama aynı anda çalıştırılamaz. Farklı bağlantılarda aynı anda birden çok paket yakalamalarını çalıştırabilirsiniz.
- En fazla beş paket yakalama, ağ geçidi başına paralel olarak çalıştırılabilir. Bu paket yakalamaları, ağ geçidi genelinde paket yakalamaları ve bağlantı başına paket yakalamaları 'nın bir birleşimi olabilir.
- MaxPacketBufferSize birimi bayt ve MaxFileSize MB 'tır

### <a name="example-json"></a>Örnek JSON
```JSON-interactive
{
  "TracingFlags": 11,
  "MaxPacketBufferSize": 120,
  "MaxFileSize": 200,
  "Filters": [
    {
      "SourceSubnets": [
        "20.1.1.0/24"
      ],
      "DestinationSubnets": [
        "10.1.1.0/24"
      ],
      "SourcePort": [
        500
      ],
      "DestinationPort": [
        4500
      ],
      "Protocol": [
        6
      ],
      "TcpFlags": 16,
      "CaptureSingleDirectionTrafficOnly": true
    }
  ]
}
```
### <a name="json-schema"></a>JSON şeması
```JSON-interactive
{
    "type": "object",
    "title": "The Root Schema",
    "description": "The root schema input JSON filter for packet capture",
    "default": {},
    "additionalProperties": true,
    "required": [
        "TracingFlags",
        "MaxPacketBufferSize",
        "MaxFileSize",
        "Filters"
    ],
    "properties": {
        "TracingFlags": {
            "$id": "#/properties/TracingFlags",
            "type": "integer",
            "title": "The Tracingflags Schema",
            "description": "Tracing flags that customer can pass to define which packets are to be captured. Supported values are CaptureESP = 1, CaptureIKE = 2, CaptureOVPN = 8. The final value is OR of the bits.",
            "default": 11,
            "examples": [
                11
            ]
        },
        "MaxPacketBufferSize": {
            "$id": "#/properties/MaxPacketBufferSize",
            "type": "integer",
            "title": "The Maxpacketbuffersize Schema",
            "description": "Maximum buffer size of each packet. The capture will only contain contents of each packet truncated to this size.",
            "default": 120,
            "examples": [
                120
            ]
        },
        "MaxFileSize": {
            "$id": "#/properties/MaxFileSize",
            "type": "integer",
            "title": "The Maxfilesize Schema",
            "description": "Maximum file size of the packet capture file. It is a circular buffer.",
            "default": 100,
            "examples": [
                100
            ]
        },
        "Filters": {
            "$id": "#/properties/Filters",
            "type": "array",
            "title": "The Filters Schema",
            "description": "An array of filters that can be passed to filter inner ESP traffic.",
            "default": [],
            "examples": [
                [
                    {
                        "Protocol": [
                            6
                        ],
                        "CaptureSingleDirectionTrafficOnly": true,
                        "SourcePort": [
                            500
                        ],
                        "DestinationPort": [
                            4500
                        ],
                        "TcpFlags": 16,
                        "SourceSubnets": [
                            "20.1.1.0/24"
                        ],
                        "DestinationSubnets": [
                            "10.1.1.0/24"
                        ]
                    }
                ]
            ],
            "additionalItems": true,
            "items": {
                "$id": "#/properties/Filters/items",
                "type": "object",
                "title": "The Items Schema",
                "description": "An explanation about the purpose of this instance.",
                "default": {},
                "examples": [
                    {
                        "SourcePort": [
                            500
                        ],
                        "DestinationPort": [
                            4500
                        ],
                        "TcpFlags": 16,
                        "SourceSubnets": [
                            "20.1.1.0/24"
                        ],
                        "DestinationSubnets": [
                            "10.1.1.0/24"
                        ],
                        "Protocol": [
                            6
                        ],
                        "CaptureSingleDirectionTrafficOnly": true
                    }
                ],
                "additionalProperties": true,
                "required": [
                    "SourceSubnets",
                    "DestinationSubnets",
                    "SourcePort",
                    "DestinationPort",
                    "Protocol",
                    "TcpFlags",
                    "CaptureSingleDirectionTrafficOnly"
                ],
                "properties": {
                    "SourceSubnets": {
                        "$id": "#/properties/Filters/items/properties/SourceSubnets",
                        "type": "array",
                        "title": "The Sourcesubnets Schema",
                        "description": "An array of source subnets that need to match the Source IP address of a packet. Packet can match any one value in the array of inputs.",
                        "default": [],
                        "examples": [
                            [
                                "20.1.1.0/24"
                            ]
                        ],
                        "additionalItems": true,
                        "items": {
                            "$id": "#/properties/Filters/items/properties/SourceSubnets/items",
                            "type": "string",
                            "title": "The Items Schema",
                            "description": "An explanation about the purpose of this instance.",
                            "default": "",
                            "examples": [
                                "20.1.1.0/24"
                            ]
                        }
                    },
                    "DestinationSubnets": {
                        "$id": "#/properties/Filters/items/properties/DestinationSubnets",
                        "type": "array",
                        "title": "The Destinationsubnets Schema",
                        "description": "An array of destination subnets that need to match the Destination IP address of a packet. Packet can match any one value in the array of inputs.",
                        "default": [],
                        "examples": [
                            [
                                "10.1.1.0/24"
                            ]
                        ],
                        "additionalItems": true,
                        "items": {
                            "$id": "#/properties/Filters/items/properties/DestinationSubnets/items",
                            "type": "string",
                            "title": "The Items Schema",
                            "description": "An explanation about the purpose of this instance.",
                            "default": "",
                            "examples": [
                                "10.1.1.0/24"
                            ]
                        }
                    },
                    "SourcePort": {
                        "$id": "#/properties/Filters/items/properties/SourcePort",
                        "type": "array",
                        "title": "The Sourceport Schema",
                        "description": "An array of source ports that need to match the Source port of a packet. Packet can match any one value in the array of inputs.",
                        "default": [],
                        "examples": [
                            [
                                500
                            ]
                        ],
                        "additionalItems": true,
                        "items": {
                            "$id": "#/properties/Filters/items/properties/SourcePort/items",
                            "type": "integer",
                            "title": "The Items Schema",
                            "description": "An explanation about the purpose of this instance.",
                            "default": 0,
                            "examples": [
                                500
                            ]
                        }
                    },
                    "DestinationPort": {
                        "$id": "#/properties/Filters/items/properties/DestinationPort",
                        "type": "array",
                        "title": "The Destinationport Schema",
                        "description": "An array of destination ports that need to match the Destination port of a packet. Packet can match any one value in the array of inputs.",
                        "default": [],
                        "examples": [
                            [
                                4500
                            ]
                        ],
                        "additionalItems": true,
                        "items": {
                            "$id": "#/properties/Filters/items/properties/DestinationPort/items",
                            "type": "integer",
                            "title": "The Items Schema",
                            "description": "An explanation about the purpose of this instance.",
                            "default": 0,
                            "examples": [
                                4500
                            ]
                        }
                    },
                    "Protocol": {
                        "$id": "#/properties/Filters/items/properties/Protocol",
                        "type": "array",
                        "title": "The Protocol Schema",
                        "description": "An array of protocols that need to match the Protocol of a packet. Packet can match any one value in the array of inputs.",
                        "default": [],
                        "examples": [
                            [
                                6
                            ]
                        ],
                        "additionalItems": true,
                        "items": {
                            "$id": "#/properties/Filters/items/properties/Protocol/items",
                            "type": "integer",
                            "title": "The Items Schema",
                            "description": "An explanation about the purpose of this instance.",
                            "default": 0,
                            "examples": [
                                6
                            ]
                        }
                    },
                    "TcpFlags": {
                        "$id": "#/properties/Filters/items/properties/TcpFlags",
                        "type": "integer",
                        "title": "The Tcpflags Schema",
                        "description": "A list of TCP flags. The TCP flags set on the packet must match any flag in the list of flags provided. FIN = 0x01,SYN = 0x02,RST = 0x04,PSH = 0x08,ACK = 0x10,URG = 0x20,ECE = 0x40,CWR = 0x80. An OR of flags can be provided.",
                        "default": 0,
                        "examples": [
                            16
                        ]
                    },
                    "CaptureSingleDirectionTrafficOnly": {
                        "$id": "#/properties/Filters/items/properties/CaptureSingleDirectionTrafficOnly",
                        "type": "boolean",
                        "title": "The Capturesingledirectiontrafficonly Schema",
                        "description": "A flags which when set captures reverse traffic also.",
                        "default": false,
                        "examples": [
                            true
                        ]
                    }
                }
            }
        }
    }
}
```

## <a name="packet-capture---portal"></a>Paket yakalama-Portal

Azure portal paket yakalamayı ayarlayabilirsiniz.

:::image type="content" source="./media/packet-capture/portal.jpg" alt-text="Portalda paket yakalama ekran görüntüsü." lightbox="./media/packet-capture/portal.jpg":::

## <a name="packet-capture---powershell"></a>Paket yakalama-PowerShell

Aşağıdaki örneklerde, paket yakalamalarını başlatan ve durduran PowerShell komutları gösterilmektedir. Parametre seçenekleri hakkında daha fazla bilgi için bkz. [Start-AzVirtualnetworkGatewayPacketCapture](/powershell/module/az.network/start-azvirtualnetworkgatewaypacketcapture).

### <a name="start-packet-capture-for-a-vpn-gateway"></a>VPN ağ geçidi için paket yakalamayı Başlat

```azurepowershell-interactive
Start-AzVirtualnetworkGatewayPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayName"
```

Filtre uygulamak için isteğe bağlı parametresini kullanabilirsiniz `-FilterData` .

### <a name="stop-packet-capture-for-a-vpn-gateway"></a>VPN ağ geçidi için paket yakalamayı durdur

```azurepowershell-interactive
Stop-AzVirtualNetworkGatewayPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayName" -SasUrl "YourSASURL"
```

### <a name="start-packet-capture-for-a-vpn-gateway-connection"></a>VPN Ağ Geçidi bağlantısı için paket yakalamayı Başlat

```azurepowershell-interactive
Start-AzVirtualNetworkGatewayConnectionPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayConnectionName"
```

Filtre uygulamak için isteğe bağlı parametresini kullanabilirsiniz `-FilterData` .

### <a name="stop-packet-capture-on-a-vpn-gateway-connection"></a>VPN ağ geçidi bağlantısında paket yakalamayı durdur

```azurepowershell-interactive
Stop-AzVirtualNetworkGatewayConnectionPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayConnectionName" -SasUrl "YourSASURL"
```

## <a name="key-considerations"></a>Dikkat edilmesi gereken temel konular

- Çalışan paket yakalama, performansı etkileyebilir. İhtiyacınız olmadığında paket yakalamayı durdurmayı unutmayın.
- Önerilen minimum paket yakalama süresi 600 saniyedir. Yoldaki birden çok bileşen arasındaki eşitleme sorunları nedeniyle, daha kısa bir paket yakalama, verilerin tamamını sağlamayabilir.
- Paket yakalama veri dosyaları PCAP biçiminde oluşturulur. PCAP dosyalarını açmak için Wireshark veya diğer yaygın olarak kullanılabilir uygulamaları kullanın.
- Paket yakalamaları, ilke tabanlı ağ geçitlerinde desteklenmez.
- `SASurl`Parametre doğru yapılandırılmamışsa, Izleme depolama hatalarıyla başarısız olabilir. Bir parametrenin doğru şekilde nasıl üretiladığının örnekleri için `SASurl` bkz. [stop-AzVirtualNetworkGatewayPacketCapture](/powershell/module/az.network/stop-azvirtualnetworkgatewaypacketcapture).



## <a name="next-steps"></a>Sonraki adımlar

VPN Gateway hakkında daha fazla bilgi için bkz. [VPN Gateway nedir?](vpn-gateway-about-vpngateways.md).
