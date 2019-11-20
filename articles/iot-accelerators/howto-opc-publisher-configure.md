---
title: OPC yayımcısını Yapılandırma-Azure | Microsoft Docs
description: Bu makalede, OPC yayımcısının, OPC UA düğüm veri değişikliklerini, yayımlanacak OPC UA olaylarını ve ayrıca telemetri biçimini belirtmek üzere nasıl yapılandırılacağı açıklanır.
author: dominicbetts
ms.author: dobett
ms.date: 06/10/2019
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 0db00f670dfcc526d3fc34d41ce731df4c6573ec
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73824158"
---
# <a name="configure-opc-publisher"></a>OPC Yayımcısını Yapılandırma

OPC yayımcısını şunları belirtecek şekilde yapılandırabilirsiniz:

- OPC UA düğüm verileri yayımlanacak şekilde değişir.
- Yayımlanacak OPC UA olayları.
- Telemetri biçimi.

OPC yayımcısını yapılandırma dosyalarını kullanarak veya yöntem çağrılarını kullanarak yapılandırabilirsiniz.

## <a name="use-configuration-files"></a>Yapılandırma dosyalarını kullanma

Bu bölümde, Configuration Files ile OPC UA düğümü yayımlamayı yapılandırma seçenekleri açıklanmaktadır.

### <a name="use-a-configuration-file-to-configure-publishing-data-changes"></a>Yayımlama verileri değişikliklerini yapılandırmak için bir yapılandırma dosyası kullanma

OPC UA düğümlerini yayımlanacak şekilde yapılandırmanın en kolay yolu bir yapılandırma dosyasıdır. Yapılandırma dosyası biçimi depodaki [publishednodes. JSON](https://github.com/Azure/iot-edge-opc-publisher/blob/master/opcpublisher/publishednodes.json) dosyasında belgelenmiştir.

Yapılandırma dosyası söz dizimi zaman içinde değiştirildi. OPC yayımcısı hala eski biçimleri okur, ancak yapılandırmayı devam ettirir en son biçime dönüştürür.

Aşağıdaki örnekte, yapılandırma dosyasının biçimi gösterilmektedir:

```json
[
  {
    "EndpointUrl": "opc.tcp://testserver:62541/Quickstarts/ReferenceServer",
    "UseSecurity": true,
    "OpcNodes": [
      {
        "Id": "i=2258",
        "OpcSamplingInterval": 2000,
        "OpcPublishingInterval": 5000,
        "DisplayName": "Current time"
      }
    ]
  }
]
```

### <a name="use-a-configuration-file-to-configure-publishing-events"></a>Yayımlama olaylarını yapılandırmak için bir yapılandırma dosyası kullanma

OPC UA olaylarını yayımlamak için, veri değişiklikleri için aynı yapılandırma dosyasını kullanırsınız.

Aşağıdaki örnek, [Simpleevents sunucusu](https://github.com/OPCFoundation/UA-.NETStandard/tree/master/SampleApplications/Workshop/SimpleEvents/Server)tarafından oluşturulan olaylar için yayımlamanın nasıl yapılandırılacağını gösterir. SimpleEvents sunucusu [OPC Foundation deposunda](https://github.com/OPCFoundation/UA-.NETStandard) bulunabilir:

```json
[
  {
    "EndpointUrl": "opc.tcp://testserver:62563/Quickstarts/SimpleEventsServer",
    "OpcEvents": [
      {
        "Id": "i=2253",
        "DisplayName": "SimpleEventServerEvents",
        "SelectClauses": [
          {
            "TypeId": "i=2041",
            "BrowsePaths": [
              "EventId"
            ]
          },
          {
            "TypeId": "i=2041",
            "BrowsePaths": [
              "Message"
            ]
          },
          {
            "TypeId": "nsu=http://opcfoundation.org/Quickstarts/SimpleEvents;i=235",
            "BrowsePaths": [
              "/2:CycleId"
            ]
          },
          {
            "TypeId": "nsu=http://opcfoundation.org/Quickstarts/SimpleEvents;i=235",
            "BrowsePaths": [
              "/2:CurrentStep"
            ]
          }
        ],
        "WhereClause": [
          {
            "Operator": "OfType",
            "Operands": [
              {
                "Literal": "ns=2;i=235"
              }
            ]
          }
        ]
      }
    ]
  }
]
```

## <a name="use-method-calls"></a>Yöntem çağrılarını kullanma

Bu bölümde OPC yayımcısını yapılandırmak için kullanabileceğiniz Yöntem çağrıları açıklanmaktadır.

### <a name="configure-using-opc-ua-method-calls"></a>OPC UA Yöntem çağrılarını kullanarak yapılandırma

OPC yayımcısı, 62222 numaralı bağlantı noktasından erişilebilen bir OPC UA sunucusu içerir. Ana bilgisayar adı **Yayımcı**ise, uç nokta URI 'si: `opc.tcp://publisher:62222/UA/Publisher`.

Bu uç nokta aşağıdaki dört yöntemi kullanıma sunar:

- PublishNode
- UnpublishNode
- GetPublishedNodes
- IoT HubDirectMethod

### <a name="configure-using-iot-hub-direct-method-calls"></a>IoT Hub doğrudan Yöntem çağrılarını kullanarak yapılandırma

OPC yayımcısı aşağıdaki IoT Hub doğrudan Yöntem çağrılarını uygular:

- PublishNodes
- UnpublishNodes
- UnpublishAllNodes
- GetConfiguredEndpoints
- GetConfiguredNodesOnEndpoint
- Getdiagnosticınfo
- GetDiagnosticLog
- GetDiagnosticStartupLog
- Exkurpplication
- GetInfo

Yöntem isteği ve yanıtlarının JSON yükünün biçimi [opcpublisher/HubMethodModel. cs](https://github.com/Azure/iot-edge-opc-publisher/blob/master/opcpublisher/HubMethodModel.cs)içinde tanımlanır.

Modülde bilinmeyen bir yöntemi çağırırsanız, yöntemin uygulanmadığını belirten bir dize ile yanıt verir. Modülün ping işlemi için bir yöntem olarak bilinmeyen bir yöntemi çağırabilirsiniz.

### <a name="configure-username-and-password-for-authentication"></a>Kimlik doğrulaması için Kullanıcı adını ve parolayı yapılandırın

Kimlik doğrulama modu IoT Hub bir doğrudan yöntem çağrısı aracılığıyla ayarlanabilir. Yükün **Opcauthenticationmode** ve username ve Password özelliği bulunmalıdır:

```csharp
{
    "EndpointUrl": "<Url of the endpoint to set authentication settings>",
    "OpcAuthenticationMode": "UsernamePassword",
    "Username": "<Username>",
    "Password": "<Password>"
    ...
}
```

Parola, IoT Hub Iş yükü Istemcisiyle şifrelenir ve yayımcının yapılandırmasında depolanır. Kimlik doğrulamasını anonim olarak değiştirmek için aşağıdaki yük ile yöntemi kullanın:

```csharp
{
    "EndpointUrl": "<Url of the endpoint to set authentication settings>",
    "OpcAuthenticationMode": "Anonymous"
    ...
}
```

Yük içinde **Opcauthenticationmode** özelliği ayarlanmamışsa, kimlik doğrulama ayarları yapılandırmada değişmeden kalır.

## <a name="configure-telemetry-publishing"></a>Telemetri yayımlamayı yapılandırma

OPC yayımcısı yayımlanmış bir düğümde bir değer değişikliği bildirimi aldığında, IoT Hub gönderilen JSON biçimli bir ileti oluşturur.

Bu JSON biçimli ileti içeriğini bir yapılandırma dosyası kullanarak yapılandırabilirsiniz. `--tc` seçeneği ile hiçbir yapılandırma dosyası belirtilmemişse, [bağlı Factory Çözüm Hızlandırıcısı](https://github.com/Azure/azure-iot-connected-factory)ile uyumlu olan bir varsayılan yapılandırma kullanılır.

OPC yayımcısı Batch iletileri olarak yapılandırıldıysa, geçerli bir JSON dizisi olarak gönderilir.

Telemetri aşağıdaki kaynaklardan türetilir:

- Düğüm için OPC yayımcı düğümü yapılandırması
- OPC yayımcısı tarafından bir bildirim alınan OPC UA yığınının **MonitoredItem** nesnesi.
- Veri değeri değişikliğine ilişkin ayrıntılar sağlayan bu bildirime geçilen bağımsız değişken.

JSON biçimli iletiye yerleştirilen telemetri, bu nesnelerin önemli özelliklerinin seçiminden oluşur. Daha fazla özelliğe ihtiyaç duyuyorsanız OPC yayımcısı kod tabanını değiştirmeniz gerekir.

Yapılandırma dosyasının sözdizimi şöyledir:

```json
// The configuration settings file consists of two objects:
// 1) The 'Defaults' object, which defines defaults for the telemetry configuration
// 2) An array 'EndpointSpecific' of endpoint specific configuration
// Both objects are optional and if they are not specified, then publisher uses
// its internal default configuration, which generates telemetry messages compatible
// with the Microsoft Connected factory Preconfigured Solution (https://github.com/Azure/azure-iot-connected-factory).

// A JSON telemetry message for Connected factory looks like:
//  {
//      "NodeId": "i=2058",
//      "ApplicationUri": "urn:myopcserver",
//      "DisplayName": "CurrentTime",
//      "Value": {
//          "Value": "10.11.2017 14:03:17",
//          "SourceTimestamp": "2017-11-10T14:03:17Z"
//      }
//  }

// The 'Defaults' object in the sample below, are similar to what publisher is
// using as its internal default telemetry configuration.
{
    "Defaults": {
        // The first two properties ('EndpointUrl' and 'NodeId' are configuring data
        // taken from the OpcPublisher node configuration.
        "EndpointUrl": {

            // The following three properties can be used to configure the 'EndpointUrl'
            // property in the JSON message send by publisher to IoT Hub.

            // Publish controls if the property should be part of the JSON message at all.
            "Publish": false,

            // Pattern is a regular expression, which is applied to the actual value of the
            // property (here 'EndpointUrl').
            // If this key is ommited (which is the default), then no regex matching is done
            // at all, which improves performance.
            // If the key is used you need to define groups in the regular expression.
            // Publisher applies the regular expression and then concatenates all groups
            // found and use the resulting string as the value in the JSON message to
            //sent to IoT Hub.
            // This example mimics the default behaviour and defines a group,
            // which matches the conplete value:
            "Pattern": "(.*)",
            // Here some more exaples for 'Pattern' values and the generated result:
            // "Pattern": "i=(.*)"
            // defined for Defaults.NodeId.Pattern, will generate for the above sample
            // a 'NodeId' value of '2058'to be sent by publisher
            // "Pattern": "(i)=(.*)"
            // defined for Defaults.NodeId.Pattern, will generate for the above sample
            // a 'NodeId' value of 'i2058' to be sent by publisher

            // Name allows you to use a shorter string as property name in the JSON message
            // sent by publisher. By default the property name is unchanged and will be
            // here 'EndpointUrl'.
            // The 'Name' property can only be set in the 'Defaults' object to ensure
            // all messages from publisher sent to IoT Hub have a similar layout.
            "Name": "EndpointUrl"

        },
        "NodeId": {
            "Publish": true,

            // If you set Defaults.NodeId.Name to "ni", then the "NodeId" key/value pair
            // (from the above example) will change to:
            //      "ni": "i=2058",
            "Name": "NodeId"
        },

        // The MonitoredItem object is configuring the data taken from the MonitoredItem
        // OPC UA object for published nodes.
        "MonitoredItem": {

            // If you set the Defaults.MonitoredItem.Flat to 'false', then a
            // 'MonitoredItem' object will appear, which contains 'ApplicationUri'
            // and 'DisplayNode' proerties:
            //      "NodeId": "i=2058",
            //      "MonitoredItem": {
            //          "ApplicationUri": "urn:myopcserver",
            //          "DisplayName": "CurrentTime",
            //      }
            // The 'Flat' property can only be used in the 'MonitoredItem' and
            // 'Value' objects of the 'Defaults' object and will be used
            // for all JSON messages sent by publisher.
            "Flat": true,

            "ApplicationUri": {
                "Publish": true,
                "Name": "ApplicationUri"
            },
            "DisplayName": {
                "Publish": true,
                "Name": "DisplayName"
            }
        },
        // The Value object is configuring the properties taken from the event object
        // the OPC UA stack provided in the value change notification event.
        "Value": {
            // If you set the Defaults.Value.Flat to 'true', then the 'Value'
            // object will disappear completely and the 'Value' and 'SourceTimestamp'
            // members won't be nested:
            //      "DisplayName": "CurrentTime",
            //      "Value": "10.11.2017 14:03:17",
            //      "SourceTimestamp": "2017-11-10T14:03:17Z"
            // The 'Flat' property can only be used for the 'MonitoredItem' and 'Value'
            // objects of the 'Defaults' object and will be used for all
            // messages sent by publisher.
            "Flat": false,

            "Value": {
                "Publish": true,
                "Name": "Value"
            },
            "SourceTimestamp": {
                "Publish": true,
                "Name": "SourceTimestamp"
            },
            // 'StatusCode' is the 32 bit OPC UA status code
            "StatusCode": {
                "Publish": false,
                "Name": "StatusCode"
                // 'Pattern' is ignored for the 'StatusCode' value
            },
            // 'Status' is the symbolic name of 'StatusCode'
            "Status": {
                "Publish": false,
                "Name": "Status"
            }
        }
    },

    // The next object allows to configure 'Publish' and 'Pattern' for specific
    // endpoint URLs. Those will overwrite the ones specified in the 'Defaults' object
    // or the defaults used by publisher.
    // It is not allowed to specify 'Name' and 'Flat' properties in this object.
    "EndpointSpecific": [
        // The following shows how a endpoint specific configuration can look like:
        {
            // 'ForEndpointUrl' allows to configure for which OPC UA server this
            // object applies and is a required property for all objects in the
            // 'EndpointSpecific' array.
            // The value of 'ForEndpointUrl' must be an 'EndpointUrl' configured in
            // the publishednodes.json confguration file.
            "ForEndpointUrl": "opc.tcp://<your_opcua_server>:<your_opcua_server_port>/<your_opcua_server_path>",
            "EndpointUrl": {
                // We overwrite the default behaviour and publish the
                // endpoint URL in this case.
                "Publish": true,
                // We are only interested in the URL part following the 'opc.tcp://' prefix
                // and define a group matching this.
                "Pattern": "opc.tcp://(.*)"
            },
            "NodeId": {
                // We are not interested in the configured 'NodeId' value, 
                // so we do not publish it.
                "Publish": false
                // No 'Pattern' key is specified here, so the 'NodeId' value will be
                // taken as specified in the publishednodes configuration file.
            },
            "MonitoredItem": {
                "ApplicationUri": {
                    // We already publish the endpoint URL, so we do not want
                    // the ApplicationUri of the MonitoredItem to be published.
                    "Publish": false
                },
                "DisplayName": {
                    "Publish": true
                }
            },
            "Value": {
                "Value": {
                    // The value of the node is important for us, everything else we
                    // are not interested in to keep the data ingest as small as possible.
                    "Publish": true
                },
                "SourceTimestamp": {
                    "Publish": false
                },
                "StatusCode": {
                    "Publish": false
                },
                "Status": {
                    "Publish": false
                }
            }
        }
    ]
}
```

## <a name="next-steps"></a>Sonraki adımlar

OPC yayımcısını nasıl yapılandıracağınızı öğrendiniz, önerilen sonraki adım [OPC yayımcısını nasıl çalıştıracağınızı](howto-opc-publisher-run.md)öğrenmektedir.
