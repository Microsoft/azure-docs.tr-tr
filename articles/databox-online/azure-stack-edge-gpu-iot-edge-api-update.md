---
title: Azure Stack Edge Ocak 2021 güncelleştirme etkisi | Microsoft Docs
description: Bu makalede, Ocak 2021 güncelleştirmesini yükledikten sonra Azure Stack Edge cihazlarında rol yönetiminin IoT Edge etkisi açıklanmaktadır.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 10/26/2020
ms.author: alkohli
ms.openlocfilehash: f16f33e9aadcc01427602a1bd81f81cb0710e4dd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94578748"
---
# <a name="iot-edge-role-management-changes-for-your-azure-stack-edge-device"></a>Azure Stack Edge cihazınız için rol yönetimi değişikliklerini IoT Edge

Azure Stack Edge cihazınız için rol yönetimi Azure IoT Edge için, 2021 Ocak sürümü için zamanlanan API, SDK ve Azure PowerShell güncelleştirilmiş sürümünü kullanın. 

Bu makalede, bu en yeni sürümü kullandığınızda yapmanız gereken değişiklikler ayrıntılı olarak açıklanmaktadır.

Ocak 2021 güncelleştirmesi yalnızca Azure Stack Edge Pro-GPU, Azure Stack Edge Pro R ve Azure Stack Edge Mini R cihazları için kullanılabilir. Bu makaledeki bilgiler yalnızca bu cihazlar için geçerlidir.

> [!NOTE]
> Ocak 2021 sürümüne yükseltmeniz gerekmez. Geçerli sürümünüzü kullanmaya devam etmeyi seçerseniz IoT Edge rol yönetimi üzerinde hiçbir etkisi olmaz. Ancak, yeni özelliklerden yararlanmak ve güvenlik risklerini azaltmaya yardımcı olmak için, yeni sürümü yüklemenizi öneririz. 

## <a name="iot-edge-role-management-changes"></a>Rol yönetimi değişikliklerini IoT Edge

İsteğe bağlı Ocak 2021 güncelleştirmesini Azure Stack Edge cihazınıza yükledikten sonra, IoT Edge rol yönetimi için API, SDK ve PowerShell cmdlet 'lerinin en son sürümünü kullanmanız gerekir.

Aşağıdaki değişiklikler yalnızca Ocak 2021 Güncelleştirmesi ' ni uyguladığınızda gereklidir:

- Şu anda rol yönetimi API 'SI sürüm &nbsp; 2019-08-01 ' i kullanıyorsanız, 2021 Ocak 'ta YAYıMLANACAK API sürümüne yükseltin. 
- Şu anda SDK sürüm 1.0.0 aracılığıyla rol yönetimi kullanıyorsanız &nbsp; , ocak 2021 ' de yayımlanacak sürüme yükseltin.
- ,, Veya gibi Azure PowerShell cmdlet 'leri (Önizleme) ile rol yönetimi kullanıyorsanız, `Get-AzStackEdgeRole` `New-AzStackEdgeRole` `Set-AzStackEdgeRole` `Remove-AzStackEdgeRole` Yeni cmdlet 'lerin Şubat 2021 ' de yayınlanma gelmesini bekleyin.

## <a name="api-usage"></a>API kullanımı

Şu anda API aracılığıyla IoT Edge rol yönetimi uyguluyorsanız, daha sonra yayımlanacak yeni API sürüm 2020-12-01 ' i kullanmanız gerekir. Geçerli rol API 'sini kullanıyorsanız, yaklaşan cihaz yazılımı sürümünü yükledikten sonra IoT eklentisi API 'sini put, GET veya DELETE Kubernetes rolüne taşımanız gerekir.

### <a name="for-the-put-method"></a>PUT yöntemi için

#### <a name="the-current-http-request"></a>Geçerli HTTP isteği 

- API çağrıları bu URI 'de yapılır: ' https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/IoTRole1?api-version=2019-08-01 '

- İstek gövdesi şöyle görünür:

    ```json
    {
        "kind": "IOT",
        "properties": {
            "hostPlatform": "Linux",
            "ioTDeviceDetails": {
                "deviceId": "iotdevice",
                "ioTHostHub": "iothub.azure-devices.net",
                "ioTHostHubId": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/Microsoft.Devices/IotHubs/testrxiothub",
                "authentication": {
                    "symmetricKey": {
                        "connectionString": {
                            "value": "Encrypted<<HostName=iothub.azure-devices.net;DeviceId=iotDevice;SharedAccessKey=2C750FscEas3JmQ8Bnui5yQWZPyml0/UiRt1bQwd8=>>",
                            "encryptionCertThumbprint": "348586569999244",
                            "encryptionAlgorithm": "AES256"
                        }
                    }
                }
            },
            "ioTEdgeDeviceDetails": {
                "deviceId": "iotEdge",
                "ioTHostHub": "iothub.azure-devices.net",
                "ioTHostHubId": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/Microsoft.Devices/IotHubs/testrxiothub",
                "authentication": {
                    "symmetricKey": {
                        "connectionString": {
                            "value": "Encrypted<<HostName=iothub.azure-devices.net;DeviceId=iotEdge;SharedAccessKey=2C750FscEas3JmQ8Bnui5yQWZPyml0/UiRt1bQwd8=>>",
                            "encryptionCertThumbprint": "1245475856069999244",
                            "encryptionAlgorithm": "AES256"
                        }
                    }
                }
            },
            "shareMappings": [],
            "roleStatus": "Enabled"
        }
    }
    ```

#### <a name="the-upcoming-http-request"></a>Yaklaşan HTTP isteği 

- Kubernetes rolüne yönelik API çağrıları şu URI 'de yapılır: 

  'https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/KubernetesRole1?api-version=2020-12-01'

    İstek gövdesi şöyle görünür:

    ```json
    {
        "kind": "Kubernetes",
        "properties": {
            "hostPlatform": "Linux",
            "kubernetesClusterInfo": {
                "version": "v1.17.3"
            },
            "kubernetesRoleResources": {
                "storage": {
                    "endpoints": []
                },
                "compute": {
                    "vmProfile": "DS1_v2"
                }
            }
        }
    }
    ```

- IoT Edge eklentisi için API çağrıları aşağıdaki URI 'de yapılır: 

   'https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/KubernetesRole1/addons/iotaddon?api-version=2020-12-01'

    İstek gövdesi şöyle görünür:

    ```json
    {
        "kind": "IoT",
        "properties": {
            "ioTDeviceDetails": {
                "deviceId": "iotdevice",
                "ioTHostHub": "iothub.azure-devices.net",
                "ioTHostHubId": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/Microsoft.Devices/IotHubs/testrxiothub",
                "authentication": {
                    "symmetricKey": {
                        "connectionString": {
                            "value": "Encrypted<<HostName=iothub.azure-devices.net;DeviceId=iotDevice;SharedAccessKey=2C750FscEas3JmQ8Bnui5yQWZPyml0/UiRt1bQwd8=>>",
                            "encryptionCertThumbprint": "348586569999244",
                            "encryptionAlgorithm": "AES256"
                        }
                    }
                }
            },
            "ioTEdgeDeviceDetails": {
                "deviceId": "iotEdge",
                "ioTHostHub": "iothub.azure-devices.net",
                "ioTHostHubId": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/Microsoft.Devices/IotHubs/testrxiothub",
                "authentication": {
                    "symmetricKey": {
                        "connectionString": {
                            "value": "Encrypted<<HostName=iothub.azure-devices.net;DeviceId=iotEdge;SharedAccessKey=2C750FscEas3JmQ8Bnui5yQWZPyml0/UiRt1bQwd8=>>",
                            "encryptionCertThumbprint": "1245475856069999244",
                            "encryptionAlgorithm": "AES256"
                        }
                    }
                }
            }
        }
    }
    ```


### <a name="for-the-get-method"></a>GET yöntemi için

#### <a name="the-current-http-response"></a>Geçerli HTTP yanıtı

- API çağrıları şu URI 'de yapılır:

   'https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/IoTRole1?api-version=2019-08-01'

- Yanıt gövdesi şöyle görünür:

    ```json
        "kind": "IOT",
        "properties": {
            "hostPlatform": "Linux",
            "ioTDeviceDetails": {
                "deviceId": "iotdevice",
                "ioTHostHub": "iothub.azure-devices.net",
                "ioTHostHubId": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/Microsoft.Devices/IotHubs/testrxiothub",
                "authentication": {
                    "symmetricKey": {}
                }
            },
            "ioTEdgeDeviceDetails": {
                "deviceId": "iotEdge",
                "ioTHostHub": "iothub.azure-devices.net",
                "ioTHostHubId": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/Microsoft.Devices/IotHubs/testrxiothub",
                "authentication": {
                    "symmetricKey": {}
                }
            },
            "shareMappings": [],
            "roleStatus": "Enabled"
        },
        "id": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/IoTRole1",
        "name": "IoTRole1",
        "type": "dataBoxEdgeDevices/roles"
    }    
    ```

#### <a name="the-upcoming-http-response"></a>Yaklaşan HTTP yanıtı

- API çağrıları şu URI 'de yapılır: 

   'https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/KubernetesRole1/addons/iotaddon?api-version=2020-12-01'

- Yanıt gövdesi şöyle görünür: 

    ```json
    {
        "kind": "IoT",
        "properties": {
            "provisioningState": "Creating",
            "ioTDeviceDetails": {
                "deviceId": "iotdevice",
                "ioTHostHub": "iothub.azure-devices.net",
                "ioTHostHubId": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/Microsoft.Devices/IotHubs/testrxiothub",
                "authentication": {
                    "symmetricKey": {}
                }
            },
            "ioTEdgeDeviceDetails": {
                "deviceId": "iotEdge",
                "ioTHostHub": "iothub.azure-devices.net",
                "ioTHostHubId": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/Microsoft.Devices/IotHubs/testrxiothub",
                "authentication": {
                    "symmetricKey": {}
                }
            },
            "version": "0.1.0-beta10"
        },
        "id": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/res1/roles/kubernetesRole/addons/iotName",
        "name": " iotName",
        "type": "Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/addon",
    }
    ```

### <a name="for-the-delete-method"></a>DELETE yöntemi için

### <a name="the-current-api-calls"></a>Geçerli API çağrıları

API çağrıları şu URI 'de yapılır: 

'https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/IoTRole1?api-version=2019-08-01'

### <a name="the-upcoming-api-calls"></a>Yaklaşan API çağrıları

API çağrıları şu URI 'de yapılır: 

'https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/KubernetesRole1/addons/iotaddon?api-version=2020-12-01'

## <a name="sdk-usage"></a>SDK kullanımı

SDK kullanıyorsanız, Ocak 2021 güncelleştirmesini yükledikten sonra, aşağıdaki örnekte gösterildiği gibi IoT Edge rolünü ayarlama yönteminizi değiştirmeniz gerekir (). Daha sonra, burada gösterildiği gibi yeni SDK 'ya geçiş yapmak için yaklaşan NuGet paketini indirip yüklersiniz.

### <a name="the-current-sdk-sample"></a>Geçerli SDK örneği

```csharp
var iotRoleStatus = "Enabled";
var iotHostPlatform = "Linux";
var id = $@"/subscriptions/546ec571-2d7f-426f-9cd8-0d695fa7edba/resourceGroups/resourceGroup/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/deviceName/roles/iotrole"; 
var name = "iotrole";
var type = "Microsoft.DataBoxEdge/dataBoxEdgeDevices/role";
var iotRoleName = "iotrole";
var ioTDeviceDetails = new IoTDeviceInfo(...);
var ioTEdgeDeviceDetails = new IoTDeviceInfo(...);
var ioTEdgeAgentInfo = new IoTEdgeAgentInfo(...);
var shareMappings = new List<MountPointMap>(...);

var role = new IoTRole(roleStatus, 
    hostPlatform, 
    shareMappings, 
    ioTDeviceDetails, 
    ioTEdgeDeviceDetails, 
    ioTEdgeAgentInfo, 
    id, 
    name, 
    type);

DataBoxEdgeManagementClient.Roles.CreateOrUpdate(deviceName, iotRoleName, role, resourceGroup);
```


### <a name="the-new-sdk-sample"></a>Yeni SDK örneği

```csharp
var k8sRoleStatus = "Enabled";
var k8sHostPlatform = "Linux";
var k8sId = $@"/subscriptions/546ec571-2d7f-426f-9cd8-0d695fa7edba/resourceGroups/resourceGroup/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/deviceName/roles/KubernetesRole"; 
var k8sRoleName = "KubernetesRole";
var k8sClusterVersion = "v1.17.3"; //Final values will be updated here around January 2021
var k8sVmProfile = "DS1_v2"; //Final values will be updated here around January 2021
var type = "Microsoft.DataBoxEdge/dataBoxEdgeDevices/role";
var k8sRole = new KubernetesRole(
    roleStatus,
    hostPlatform,
    shareMappings,
    k8sClusterVersion,
    k8sVmProfile,
    k8sId,
    k8sRoleName,
    type
);
DataBoxEdgeManagementClient.Roles.CreateOrUpdate(deviceName, k8sRoleName, k8sRole, resourceGroup); //Final usage will be updated here around January 2021

var ioTId = $@"/subscriptions/546ec571-2d7f-426f-9cd8-0d695fa7edba/resourceGroups/resourceGroup/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/deviceName/roles/KubernetesRole/addons/iotaddon";
var ioTAddonName = "iotaddon";
var ioTAddonType = "Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/addons";
var addon = new IoTAddon(
    ioTDeviceDetails, 
    ioTEdgeDeviceDetails, 
    ioTEdgeAgentInfo, 
    ioTId, 
    ioTAddonName, 
    ioTAddonType);
DataBoxEdgeManagementClient.AddOns.CreateOrUpdate(deviceName, k8sRoleName, addonName, addon, resourceGroup); //Final usage will be updated here around January 2021
```

## <a name="cmdlet-usage"></a>Cmdlet kullanımı

Şu anda `Get-AzStackEdgeRole` ,, `New-AzStackEdgeRole` `Set-AzStackEdgeRole` veya `Remove-AzStackEdgeRole` cmdlet 'ini kullanıyorsanız Şubat 2021 sürümü için planlı yeni sürümü beklemeniz gerekir.

## <a name="frequently-asked-questions"></a>Sık sorulan sorular

**Azure Stack Edge Pro-FPGA kullanıyorum. Ocak 2021 güncelleştirmesi FPGA modelini etkiler mi?**

Hayır. Ocak 2021 güncelleştirmesi yalnızca Azure Stack Edge Pro-FPGA, Azure Stack Edge Pro R ve Azure Stack Edge Mini R cihazları için geçerlidir. Azure Stack Edge Pro-FPGA bu güncelleştirmeden etkilenmez ve IoT Edge rol yönetiminde herhangi bir değişiklik gerektirmez.

**Azure Stack Edge Pro-GPU sürümünü Ocak 2021 ' deki yeni cihaz yazılımına güncelleştirdikten sonra, mevcut hizmetlerden herhangi biri etkileniyor mu?**

Hayır. Ocak 2021 cihaz güncelleştirmesi yüklendikten sonra yapılandırdığınız hizmetler etkilenmez.

**IoT Edge yönetim API 'SI, SDK veya cmdlet 'inde üst düzey değişiklikler nelerdir?**

IoT Edge, Kubernetes rolü altındaki bir eklentidir. Bu, ilk olarak Kubernetes 'in yapılandırıldığından emin olmak ve sonra IoT Edge yapılandırmayı gerçekleştirmeniz gerektiğini gösterir.


## <a name="next-steps"></a>Sonraki adımlar

- [Güncelleştirmelerin nasıl uygulanacağını öğrenin](azure-stack-edge-gpu-install-update.md)

