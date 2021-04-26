---
title: Azure Market'teki sanal makine ile ilgili sık sorulan sorular
description: Azure Marketi 'nde bir sanal makine oluşturulurken karşılaşılan ortak sorularla karşılaşıldı.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: guide
author: kriti-ms
ms.author: krsh
ms.date: 03/10/2021
ms.openlocfilehash: 2975d1f1558bc7f9e4a12c18882e43a163b97982
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104593433"
---
# <a name="common-questions-about-vm-in-azure-marketplace"></a>Azure Market'teki sanal makine ile ilgili sık sorulan sorular

Bu sık sorulan sorular (SSS), Azure Marketi 'nde bir sanal makine (VM) teklifi oluştururken karşılaşabileceğiniz yaygın sorunları ele alır.

## <a name="how-do-i-configure-a-virtual-private-network-vpn-to-work-with-my-vms"></a>Sanal özel ağ (VPN), VM 'lerimi çalışacak şekilde mi Nasıl yaparım??

Azure Resource Manager dağıtım modelini kullanıyorsanız, üç seçeneğiniz vardır:

- [Azure portal kullanarak rota tabanlı bir VPN ağ geçidi oluşturma](../vpn-gateway/tutorial-create-gateway-portal.md)
- [Azure PowerShell kullanarak rota tabanlı bir VPN ağ geçidi oluşturma](../vpn-gateway/create-routebased-vpn-gateway-powershell.md)
- [CLı kullanarak rota temelli VPN ağ geçidi oluşturma](../vpn-gateway/create-routebased-vpn-gateway-cli.md)

## <a name="what-are-microsoft-support-policies-for-running-microsoft-server-software-on-azure-based-vms"></a>Azure tabanlı VM 'lerde Microsoft sunucu yazılımını çalıştırmaya yönelik Microsoft destek ilkeleri nelerdir?

[Microsoft Azure sanal makineler Için Microsoft sunucu yazılımı desteği](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)' nde ayrıntıları bulabilirsiniz.

## <a name="in-a-vm-how-do-i-manage-the-custom-script-extension-in-the-startup-task"></a>Bir VM 'de, başlangıç görevinde Özel Betik uzantısını nasıl yönetebilirim?

Windows sistemlerinde Azure PowerShell modülünü, Azure Resource Manager şablonları ve sorun giderme adımlarını kullanarak özel betik uzantısının kullanımıyla ilgili ayrıntılar için, bkz. [Windows Için özel Betik uzantısı](../virtual-machines/extensions/custom-script-windows.md).

## <a name="are-32-bit-applications-or-services-supported-in-azure-marketplace"></a>32 bitlik uygulamalar veya hizmetler Azure Marketi 'nde destekleniyor mu?

Hayır. Azure VM 'Leri için desteklenen işletim sistemleri ve standart hizmetler 64 bittir. 64 bitlik işletim sistemleri, geriye dönük uyumluluk için uygulamaların 32 bit sürümlerini desteklese de, sanal makine çözümünüzün bir parçası olarak 32-bit uygulamalar kullanılması desteklenmez ve kesinlikle önerilmez. Uygulamanızı 64 bitlik bir proje olarak yeniden oluşturun.

Daha fazla bilgi için şu makalelere bakın:

- [32 bitlik uygulamalar çalıştırma](/windows/desktop/WinProg64/running-32-bit-applications)
- [Azure sanal makinelerinde 32 bit işletim sistemleri desteği](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines)
- [Microsoft Azure sanal makineleri için Microsoft sunucu yazılımı desteği](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)

## <a name="error-vhd-is-already-registered-with-image-repository-as-the-resource"></a>Hata: VHD, kaynak olarak görüntü deposuna zaten kayıtlı

VHD 'lerimin içinden bir görüntü oluşturmaya her seferinde, bu Azure PowerShell "VHD, kaynak olarak görüntü deposuna zaten kaydedildi" hatasını alıyorum. Azure 'da bu ada sahip herhangi bir görüntü oluşturmadım veya bu adı taşıyan bir resim bulmadı. Bu sorunu nasıl çözebilirim?

Bu sorun genellikle kilidi olan bir VHD 'den bir VM oluşturduysanız görüntülenir. Bu VHD 'den ayrılmış bir VM olmadığından emin olun ve işlemi yeniden deneyin. Bu sorun devam ederse bir destek bileti açın. Bkz. [Iş ortağı merkezi desteği](support.md).

## <a name="how-do-i-create-a-vm-from-a-generalized-vhd"></a>Genelleştirilmiş bir VHD 'den VM oluşturma Nasıl yaparım??

### <a name="prepare-an-azure-resource-manager-template"></a>Azure Resource Manager şablonu hazırlama

Bu bölümde, Kullanıcı tarafından belirtilen bir sanal makine (VM) görüntüsünün nasıl oluşturulacağı ve dağıtılacağı açıklanmaktadır. Bunu, Azure tarafından dağıtılan bir sanal sabit diskten işletim sistemi ve veri diski VHD görüntüleri sağlayarak yapabilirsiniz. Bu adımlar, genelleştirilmiş VHD kullanarak VM 'yi dağıtır.

1. Azure portalında oturum açın.
2. Genelleştirilmiş işletim sistemi VHD 'nizi ve veri diski VHD 'nizi Azure depolama hesabınıza yükleyin.
3. Giriş sayfasında, kaynak oluştur ' u seçin, "şablon dağıtımı" ifadesini arayın ve Oluştur ' u seçin.
4. Düzenleyicide kendi şablonunuzu oluşturun öğesini seçin.

   :::image type="content" source="media/vm/template-deployment.png" alt-text="Bir şablonun seçimini gösterir":::

5. Aşağıdaki JSON şablonunu düzenleyiciye yapıştırın ve Kaydet ' i seçin.
 ```json
  {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
           "userStorageAccountName": {
               "type": "String"
           },
           "userStorageContainerName": {
               "defaultValue": "vhds",
               "type": "String"
           },
           "dnsNameForPublicIP": {
               "type": "String"
           },
           "adminUserName": {
               "defaultValue": "isv",
               "type": "String"
           },
           "adminPassword": {
               "defaultValue": "",
               "type": "SecureString"
           },
           "osType": {
               "defaultValue": "windows",
               "allowedValues": [
                   "windows",
                   "linux"
               ],
               "type": "String"
           },
           "subscriptionId": {
               "type": "String"
           },
           "location": {
               "type": "String"
           },
           "vmSize": {
               "type": "String"
           },
           "publicIPAddressName": {
               "type": "String"
           },
           "vmName": {
               "type": "String"
           },
           "virtualNetworkName": {
               "type": "String"
           },
           "nicName": {
               "type": "String"
           },
           "vhdUrl": {
               "type": "String",
               "metadata": {
                   "description": "VHD Url..."
               }
           }
       },
       "variables": {
           "addressPrefix": "10.0.0.0/16",
           "subnet1Name": "Subnet-1",
           "subnet2Name": "Subnet-2",
           "subnet1Prefix": "10.0.0.0/24",
           "subnet2Prefix": "10.0.1.0/24",
           "publicIPAddressType": "Dynamic",
           "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",
           "subnet1Ref": "[concat(variables('vnetID'),'/subnets/',variables('subnet1Name'))]",
           "hostDNSNameScriptArgument": "[concat(parameters('dnsNameForPublicIP'),'.',parameters('location'),'.cloudapp.azure.com')]",
           "osDiskVhdName": "[concat('http://',parameters('userStorageAccountName'),'.blob.core.windows.net/',parameters('userStorageContainerName'),'/',parameters('vmName'),'osDisk.vhd')]"
       },
       "resources": [
           {
               "type": "Microsoft.Network/publicIPAddresses",
               "apiVersion": "2015-06-15",
               "name": "[parameters('publicIPAddressName')]",
               "location": "[parameters('location')]",
               "properties": {
                   "publicIPAllocationMethod": "[variables('publicIPAddressType')]",
                   "dnsSettings": {
                       "domainNameLabel": "[parameters('dnsNameForPublicIP')]"
                   }
               }
           },
           {
               "type": "Microsoft.Network/virtualNetworks",
               "apiVersion": "2015-06-15",
               "name": "[parameters('virtualNetworkName')]",
               "location": "[parameters('location')]",
               "properties": {
                   "addressSpace": {
                       "addressPrefixes": [
                           "[variables('addressPrefix')]"
                       ]
                   },
                   "subnets": [
                       {
                           "name": "[variables('subnet1Name')]",
                           "properties": {
                               "addressPrefix": "[variables('subnet1Prefix')]"
                           }
                       },
                       {
                           "name": "[variables('subnet2Name')]",
                           "properties": {
                               "addressPrefix": "[variables('subnet2Prefix')]"
                           }
                       }
                   ]
               }
           },
           {
               "type": "Microsoft.Network/networkInterfaces",
               "apiVersion": "2015-06-15",
               "name": "[parameters('nicName')]",
               "location": "[parameters('location')]",
               "dependsOn": [
                   "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]",
                   "[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]"
               ],
               "properties": {
                   "ipConfigurations": [
                       {
                           "name": "ipconfig1",
                           "properties": {
                               "privateIPAllocationMethod": "Dynamic",
                               "publicIPAddress": {
                                   "id": "[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]"
                               },
                               "subnet": {
                                   "id": "[variables('subnet1Ref')]"
                               }
                           }
                       }
                   ]
               }
           },
           {
               "type": "Microsoft.Compute/virtualMachines",
               "apiVersion": "2015-06-15",
               "name": "[parameters('vmName')]",
               "location": "[parameters('location')]",
               "dependsOn": [
                   "[concat('Microsoft.Network/networkInterfaces/', parameters('nicName'))]"
               ],
               "properties": {
                   "hardwareProfile": {
                       "vmSize": "[parameters('vmSize')]"
                   },
                   "osProfile": {
                       "computername": "[parameters('vmName')]",
                       "adminUsername": "[parameters('adminUsername')]",
                       "adminPassword": "[parameters('adminPassword')]"
                   },
                   "storageProfile": {
                       "osDisk": {
                           "name": "[concat(parameters('vmName'),'-osDisk')]",
                           "osType": "[parameters('osType')]",
                           "caching": "ReadWrite",
                           "image": {
                               "uri": "[parameters('vhdUrl')]"
                           },
                           "vhd": {
                               "uri": "[variables('osDiskVhdName')]"
                           },
                           "createOption": "FromImage"
                       }
                   },
                   "networkProfile": {
                       "networkInterfaces": [
                           {
                               "id": "[resourceId('Microsoft.Network/networkInterfaces',parameters('nicName'))]"
                           }
                       ]
                   }
               }
           }
       ]
   }
   ```


6. Görüntülenmiş özel dağıtım özelliği sayfaları için parametre değerlerini belirtin.

 **TABLO 1**

| **ResourceGroupName** | **Mevcut Azure Kaynak grubu adı. Genellikle, anahtar kasanız ile aynı RG 'yi kullanın.** |
| --- | --- |
| TemplateFile | VHDtoImage.jsdosyanın tam yol adı. |
| userStorageAccountName | Depolama hesabının adı. |
| Dnsnameforpublicıp | Genel IP için DNS adı; küçük harfle yazılmalıdır. |
| subscriptionId | Azure abonelik tanımlayıcısı. |
| Konum | Kaynak grubunun standart Azure coğrafi konumu. |
| vmName | Sanal makinenin adı. |
| vhdUrl | Sanal sabit diskin Web adresi. |
| vmSize | Sanal makine örneğinin boyutu. |
| Publicıpaddressname | Genel IP adresinin adı. |
| virtualNetworkName | Sanal ağın adı. |
| nicName | Sanal ağ için ağ arabirim kartının adı. |
| adminUserName | Yönetici hesabının Kullanıcı adı. |
| adminPassword | Yönetici parolası. |
|

7. Bu değerleri sağlamadıktan sonra, Satın Al ' ı seçin.

Azure, dağıtıma başlayacak. Belirtilen depolama hesabı yolunda belirtilen yönetilmeyen VHD 'ye sahip yeni bir VM oluşturur. Azure portal ilerlemeyi, portalın sol tarafındaki sanal makineler ' i seçerek izleyebilirsiniz. VM oluşturulduğunda, durum başlangıç durumundan çalışmaya başlayacak şekilde değişir.

2. nesil VM dağıtımı için şu şablonu kullanın:
 ```json
 {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
          "userStorageAccountName": {
              "type": "String"
          },
          "userStorageContainerName": {
              "defaultValue": "vhds",
              "type": "String"
          },
          "dnsNameForPublicIP": {
              "type": "String"
          },
          "adminUserName": {
              "defaultValue": "isv",
              "type": "String"
          },
          "adminPassword": {
              "defaultValue": "",
              "type": "SecureString"
          },
          "osType": {
              "defaultValue": "windows",
              "allowedValues": [
                  "windows",
                  "linux"
              ],
              "type": "String"
          },
          "subscriptionId": {
              "type": "String"
          },
          "location": {
              "type": "String"
          },
          "vmSize": {
              "type": "String"
          },
          "publicIPAddressName": {
              "type": "String"
          },
          "vmName": {
              "type": "String"
          },
          "virtualNetworkName": {
              "type": "String"
          },
          "nicName": {
              "type": "String"
          },
          "vhdUrl": {
              "type": "String",
              "metadata": {
                  "description": "VHD Url..."
              }
          }
      },
      "variables": {
          "addressPrefix": "10.0.0.0/16",
          "subnet1Name": "Subnet-1",
          "subnet2Name": "Subnet-2",
          "subnet1Prefix": "10.0.0.0/24",
          "subnet2Prefix": "10.0.1.0/24",
          "publicIPAddressType": "Dynamic",
          "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",
          "subnet1Ref": "[concat(variables('vnetID'),'/subnets/',variables('subnet1Name'))]",
          "hostDNSNameScriptArgument": "[concat(parameters('dnsNameForPublicIP'),'.',parameters('location'),'.cloudapp.azure.com')]",
          "osDiskVhdName": "[concat('http://',parameters('userStorageAccountName'),'.blob.core.windows.net/',parameters('userStorageContainerName'),'/',parameters('vmName'),'osDisk.vhd')]"
      },
      "resources": [
          {
              "type": "Microsoft.Network/publicIPAddresses",
              "apiVersion": "2015-06-15",
              "name": "[parameters('publicIPAddressName')]",
              "location": "[parameters('location')]",
              "properties": {
                  "publicIPAllocationMethod": "[variables('publicIPAddressType')]",
                  "dnsSettings": {
                      "domainNameLabel": "[parameters('dnsNameForPublicIP')]"
                  }
              }
          },
          {
              "type": "Microsoft.Network/virtualNetworks",
              "apiVersion": "2015-06-15",
              "name": "[parameters('virtualNetworkName')]",
              "location": "[parameters('location')]",
              "properties": {
                  "addressSpace": {
                      "addressPrefixes": [
                          "[variables('addressPrefix')]"
                      ]
                  },
                  "subnets": [
                      {
                          "name": "[variables('subnet1Name')]",
                          "properties": {
                              "addressPrefix": "[variables('subnet1Prefix')]"
                          }
                      },
                      {
                          "name": "[variables('subnet2Name')]",
                          "properties": {
                              "addressPrefix": "[variables('subnet2Prefix')]"
                          }
                      }
                  ]
              }
          },
          {
              "type": "Microsoft.Network/networkInterfaces",
              "apiVersion": "2015-06-15",
              "name": "[parameters('nicName')]",
              "location": "[parameters('location')]",
              "dependsOn": [
                  "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]",
                  "[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]"
              ],
              "properties": {
                  "ipConfigurations": [
                      {
                          "name": "ipconfig1",
                          "properties": {
                              "privateIPAllocationMethod": "Dynamic",
                              "publicIPAddress": {
                                  "id": "[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]"
                              },
                              "subnet": {
                                  "id": "[variables('subnet1Ref')]"
                              }
                          }
                      }
                  ]
              }
          },
          {
              "type": "Microsoft.Compute/virtualMachines",
              "apiVersion": "2015-06-15",
              "name": "[parameters('vmName')]",
              "location": "[parameters('location')]",
              "dependsOn": [
                  "[concat('Microsoft.Network/networkInterfaces/', parameters('nicName'))]"
              ],
              "properties": {
                  "hardwareProfile": {
                      "vmSize": "[parameters('vmSize')]"
                  },
                  "osProfile": {
                      "computername": "[parameters('vmName')]",
                      "adminUsername": "[parameters('adminUsername')]",
                      "adminPassword": "[parameters('adminPassword')]"
                  },
                  "storageProfile": {
                      "osDisk": {
                          "name": "[concat(parameters('vmName'),'-osDisk')]",
                          "osType": "[parameters('osType')]",
                          "caching": "ReadWrite",
                          "image": {
                              "uri": "[parameters('vhdUrl')]"
                          },
                          "vhd": {
                              "uri": "[variables('osDiskVhdName')]"
                          },
                          "createOption": "FromImage"
                      }
                  },
                  "networkProfile": {
                      "networkInterfaces": [
                          {
                              "id": "[resourceId('Microsoft.Network/networkInterfaces',parameters('nicName'))]"
                          }
                      ]
                  }
              }
          }
      ]
  }
  ```


### <a name="deploy-an-azure-vm-using-powershell"></a>PowerShell kullanarak bir Azure VM dağıtma

Ve değişkenleri için değerler sağlamak üzere aşağıdaki betiği kopyalayın ve düzenleyin `$storageaccount` `$vhdUrl` . Mevcut Genelleştirilmiş VHD 'nizden bir Azure VM kaynağı oluşturmak için yürütün.

```powershell
# storage account of existing generalized VHD
$storageaccount = "testwinrm11815"
# generalized VHD URL
$vhdUrl = "https://testwinrm11815.blob.core.windows.net/vhds/testvm1234562016651857.vhd"

echo "New-AzResourceGroupDeployment -Name "dplisvvm$postfix" -ResourceGroupName "$rgName" -TemplateFile "C:\certLocation\VHDtoImage.json" -userStorageAccountName "$storageaccount" -dnsNameForPublicIP "$vmName" -subscriptionId "$mysubid" -location "$location" -vmName "$vmName" -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl
$objAzureKeyVaultSecret.Id -vhdUrl "$vhdUrl" -vmSize "Standard\_A2" -publicIPAddressName "myPublicIP1" -virtualNetworkName "myVNET1" -nicName "myNIC1" -adminUserName "isv" -adminPassword $pwd"

# deploying VM with existing VHD
New-AzResourceGroupDeployment -Name "dplisvvm$postfix" -ResourceGroupName "$rgName"
```

## <a name="how-do-i-test-a-hidden-preview-image"></a>Gizli önizleme görüntüsünü test Nasıl yaparım??

Hızlı Başlangıç şablonlarını kullanarak gizli önizleme görüntülerini dağıtabilirsiniz.
Önizleme görüntüsünü dağıtmak için 
1. [Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-linux) veya [Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows)için ilgili hızlı başlangıç şablonunu goto, "Azure 'a dağıt" ı seçin. Bu, sizi Azure portal götürebilmelidir.
2. Azure portal, "şablonu Düzenle" seçeneğini belirleyin.
3. JSON şablonunda, ImageReference araması yapın ve PublisherId, OfferId, skuid ve görüntünün sürümünü güncelleştirin. Önizleme görüntüsünü test etmek için, OfferId 'ye "-PREVIEW" ekleyin.
 ![image](https://user-images.githubusercontent.com/79274470/110191995-71c7d500-7de0-11eb-9f3c-6a42f55d8f03.png)
4. Kaydet’e tıklayın.
5. Ayrıntıların geri kalanını doldurun. İnceleme ve oluşturma


## <a name="next-steps"></a>Sonraki adımlar

- [VM sertifikasyonu sorunlarını giderme](azure-vm-create-certification-faq.md)
