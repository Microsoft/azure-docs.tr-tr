---
title: Azure bulut hizmeti dağıtma (genişletilmiş destek)-Şablonlar
description: ARM şablonlarını kullanarak bir Azure bulut hizmeti (genişletilmiş destek) dağıtma
ms.topic: tutorial
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 8804febe81afc79a4a7eadb56e8350e758ea38ba
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105519"
---
# <a name="deploy-a-cloud-service-extended-support-using-arm-templates"></a>ARM şablonları kullanarak bir bulut hizmeti (genişletilmiş destek) dağıtma

Bu öğreticide, [ARM şablonları](../azure-resource-manager/templates/overview.md)kullanarak bir bulut hizmeti (genişletilmiş destek) dağıtımı oluşturma açıklanmaktadır. 

## <a name="before-you-begin"></a>Başlamadan önce

1. Cloud Services (genişletilmiş destek) için [dağıtım önkoşullarını](deploy-prerequisite.md) gözden geçirin ve ilişkili kaynakları oluşturun.

2. [Azure Portal](../azure-resource-manager/management/manage-resource-groups-portal.md) veya [PowerShell](../azure-resource-manager/management/manage-resource-groups-powershell.md)kullanarak yeni bir kaynak grubu oluşturun. Mevcut bir kaynak grubu kullanıyorsanız, bu adım isteğe bağlıdır.

3. Genel IP adresi oluşturun ve genel IP adresinin DNS etiketi özelliğini ayarlayın. Cloud Services (genişletilmiş destek) yalnızca [temel](https://docs.microsoft.com/azure/virtual-network/public-ip-addresses#basic) SKU genel IP adreslerini destekler. Standart SKU genel IP 'Leri Cloud Services çalışmaz.
Statik IP kullanıyorsanız, hizmet yapılandırma (. cscfg) dosyasında bir Ayrılmış IP olarak başvurulmalıdır. Var olan bir IP adresi kullanıyorsanız, bu adımı atlayın ve IP adresi bilgilerini ARM şablonunuzun yük dengeleyici yapılandırma ayarlarına doğrudan ekleyin.
 
4. [Azure Portal](../storage/common/storage-account-create.md?tabs=azure-portal) veya [PowerShell](../storage/common/storage-account-create.md?tabs=azure-powershell)'i kullanarak yeni bir depolama hesabı oluşturun. Mevcut bir depolama hesabı kullanıyorsanız, bu adım isteğe bağlıdır.

5. Hizmet tanımı (. csdef) ve hizmet yapılandırma (. cscfg) dosyalarınızı [Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md#upload-a-block-blob), [AzCopy](../storage/common/storage-use-azcopy-blobs-upload.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) veya [PowerShell](../storage/blobs/storage-quickstart-blobs-powershell.md#upload-blobs-to-the-container)kullanarak depolama hesabına yükleyin. Bu öğreticide daha sonra ARM şablonuna eklenecek her iki dosyanın SAS URI 'Lerini alın.

6. Seçim Bir Anahtar Kasası oluşturun ve sertifikaları karşıya yükleyin.

    -  Sertifikalar, hizmete veya hizmetten güvenli iletişim sağlamak için bulut hizmetlerine iliştirilebilir. Sertifikaları kullanabilmeniz için, kendi parmak izleri hizmet yapılandırma (. cscfg) dosyanızda belirtilmelidir ve bir anahtar kasasında karşıya yüklenmelidir. Bir Anahtar Kasası [Azure Portal](../key-vault/general/quick-create-portal.md) veya [PowerShell](../key-vault/general/quick-create-powershell.md)aracılığıyla oluşturulabilir.
    - İlişkili anahtar Kasası, bulut hizmeti ile aynı bölgede ve abonelikte bulunmalıdır.
    - Cloud Services (genişletilmiş destek) kaynağının Key Vault sertifikaları alabilmesi için ilgili Anahtar Kasası için uygun izinler etkinleştirilmelidir. Daha fazla bilgi için bkz. [Sertifikalar ve Key Vault](certificates-and-key-vault.md)
    - Anahtar kasasının, aşağıdaki adımlarda gösterilen ARM şablonunun OsProfile bölümünde başvurulması gerekir.

## <a name="deploy-a-cloud-service-extended-support"></a>Bulut hizmeti dağıtma (genişletilmiş destek)

> [!NOTE]
> ARM şablonunuzu ve parametre dosyanızı oluşturmanın daha kolay ve daha hızlı bir yolu [Azure Portal](https://portal.azure.com)aracılığıyla yapılır. Bulut hizmetinizi PowerShell aracılığıyla oluşturmak için, [oluşturulan ARM şablonunu](generate-template-portal.md) Portal aracılığıyla indirebilirsiniz
 
1. Sanal ağ oluşturun. Sanal ağın adı, hizmet yapılandırma (. cscfg) dosyasındaki başvurularla aynı olmalıdır. Var olan bir sanal ağı kullanıyorsanız, ARM şablonundan bu bölümü atlayın.

    ```json
    "resources": [ 
        { 
          "apiVersion": "2019-08-01", 
          "type": "Microsoft.Network/virtualNetworks", 
          "name": "[parameters('vnetName')]", 
          "location": "[parameters('location')]", 
          "properties": { 
            "addressSpace": { 
              "addressPrefixes": [ 
                "10.0.0.0/16" 
              ] 
            }, 
            "subnets": [ 
              { 
                "name": "WebTier", 
                "properties": { 
                  "addressPrefix": "10.0.0.0/24" 
                } 
              } 
            ] 
          } 
        } 
    ] 
    ```
    
     Yeni bir sanal ağ oluşturuyorsanız, `dependsOn` platformun bulut hizmetini oluşturmadan önce sanal ağı oluşturduğundan emin olmak için bölümüne aşağıdakileri ekleyin.

    ```json
    "dependsOn": [ 
            "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]" 
     ] 
    ```
 
2. Genel IP adresi oluşturun ve (isteğe bağlı olarak) genel IP adresinin DNS etiketi özelliğini ayarlayın. Statik IP kullanıyorsanız, bunu hizmet yapılandırma (. cscfg) dosyasında bir Ayrılmış IP olarak başvurmanız gerekir. Var olan bir IP adresi kullanıyorsanız, bu adımı atlayın ve IP adresi bilgilerini ARM şablonunuzun yük dengeleyici yapılandırma ayarlarına doğrudan ekleyin.
 
    ```json
    "resources": [ 
        { 
          "apiVersion": "2019-08-01", 
          "type": "Microsoft.Network/publicIPAddresses", 
          "name": "[parameters('publicIPName')]", 
          "location": "[parameters('location')]", 
          "properties": { 
            "publicIPAllocationMethod": "Dynamic", 
            "idleTimeoutInMinutes": 10, 
            "publicIPAddressVersion": "IPv4", 
            "dnsSettings": { 
              "domainNameLabel": "[variables('dnsName')]" 
            } 
          }, 
          "sku": { 
            "name": "Basic" 
          } 
        } 
    ] 
    ```
     
     Yeni bir IP adresi oluşturuyorsanız, `dependsOn` platformun bulut hizmetini oluşturmadan önce IP adresini oluşturduğundan emin olmak için bölümüne aşağıdakileri ekleyin.
    
    ```json
    "dependsOn": [ 
            "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPName'))]" 
          ] 
    ```
 
3. Bir ağ profili nesnesi oluşturun ve genel IP adresini yük dengeleyicinin ön ucunda ilişkilendirin. Yük dengeleyici, platform tarafından otomatik olarak oluşturulur.

    ```json
    "networkProfile": { 
        "loadBalancerConfigurations": [ 
          { 
            "id": "[concat(variables('resourcePrefix'), 'Microsoft.Network/loadBalancers/', variables('lbName'))]", 
            "name": "[variables('lbName')]", 
            "properties": { 
              "frontendIPConfigurations": [ 
                { 
                  "name": "[variables('lbFEName')]", 
                  "properties": { 
                    "publicIPAddress": { 
                      "id": "[concat(variables('resourcePrefix'), 'Microsoft.Network/publicIPAddresses/', parameters('publicIPName'))]" 
                    } 
                  } 
                } 
              ] 
            } 
          } 
        ] 
      } 
    ```
 

4.  `OsProfile`   ARM şablonunun bölümüne Anahtar Kasası başvurunuz ekleyin. Key Vault, Cloud Services ilişkili sertifikaları (genişletilmiş destek) depolamak için kullanılır. Sertifikaları Key Vault ekleyin ve ardından hizmet yapılandırma (. cscfg) dosyasındaki sertifika parmak izleriyle referans yapın. Ayrıca, Cloud Services (genişletilmiş destek) kaynağı Key Vault gizli dizi olarak depolanan sertifikayı alabilmesi için, ' erişim için Azure sanal makineler ' için Key Vault ' erişim ilkeleri ' ' ni etkinleştirmeniz gerekir. Anahtar Kasası, bulut hizmeti ile aynı bölgede ve abonelikte yer almalıdır ve benzersiz bir ada sahip olmalıdır. Daha fazla bilgi için bkz. [Cloud Services sertifikaları kullanma (genişletilmiş destek)](certificates-and-key-vault.md).
     
    ```json
    "osProfile": { 
          "secrets": [ 
            { 
              "sourceVault": { 
                "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.KeyVault/vaults/{keyvault-name}" 
              }, 
              "vaultCertificates": [ 
                { 
                  "certificateUrl": "https://{keyvault-name}.vault.azure.net:443/secrets/ContosoCertificate/{secret-id}" 
                } 
              ] 
            } 
          ] 
        } 
    ```
  
    > [!NOTE]
    > Sourcekasası, anahtar kasanıza ARM kaynak KIMLIĞIDIR. Bu bilgileri, anahtar kasalarınızın Özellikler bölümünde kaynak KIMLIĞINI bularak bulabilirsiniz.
    > - Sertifika URL 'si, **gizli tanımlayıcı** olarak etiketlenen anahtar kasasındaki sertifikaya gidilerek bulunabilir.  
   >  - certificateUrl, https://{keykasası-endpoin}/gizlilikler/{secretname}/{Secret-ID} biçiminde olmalıdır

5. Rol profili oluşturun. Rol sayısı, rol adı, her bir rol ve boyutta örnek sayısı, hizmet yapılandırma (. cscfg), hizmet tanımı (. csdef) ve ARM şablonundaki rol profili bölümünde aynı olduğundan emin olun.
    
    ```json
    "roleProfile": {
      "roles": {
        "value": [
          {
            "name": "WebRole1",
            "sku": {
              "name": "Standard_D1_v2",
              "capacity": "1"
            }
          },
          {
            "name": "WorkerRole1",
            "sku": {
              "name": "Standard_D1_v2",
              "capacity": "1"
            } 
          } 
        ]
      }
    }   
    ```

6. Seçim Bulut hizmetinize uzantı eklemek için bir uzantı profili oluşturun. Bu örnekte, uzak masaüstü ve Windows Azure tanılama uzantısı ekliyoruz.
   > [!Note] 
   > Uzak Masaüstü parolası 8-123 karakter uzunluğunda olmalıdır ve şunlardan en az 3 parola karmaşıklığı gereksinimini karşılamalıdır: 1) bir büyük harfli karakter 2) içeren bir büyük harf 3), bir sayısal basamak 4) bir özel karakter içerir 5) denetim karakterlerine izin verilmez

    ```json
        "extensionProfile": {
          "extensions": [
            {
              "name": "RDPExtension",
              "properties": {
                "autoUpgradeMinorVersion": true,
                "publisher": "Microsoft.Windows.Azure.Extensions",
                "type": "RDP",
                "typeHandlerVersion": "1.2.1",
                "settings": "<PublicConfig>\r\n <UserName>[Insert Username]</UserName>\r\n <Expiration>1/21/2022 12:00:00 AM</Expiration>\r\n</PublicConfig>",
                "protectedSettings": "<PrivateConfig>\r\n <Password>[Insert Password]</Password>\r\n</PrivateConfig>"
              }
            },
            {
              "name": "Microsoft.Insights.VMDiagnosticsSettings_WebRole1",
              "properties": {
                "autoUpgradeMinorVersion": true,
                "publisher": "Microsoft.Azure.Diagnostics",
                "type": "PaaSDiagnostics",
                "typeHandlerVersion": "1.5",
                "settings": "[parameters('wadPublicConfig_WebRole1')]",
                "protectedSettings": "[parameters('wadPrivateConfig_WebRole1')]",
                "rolesAppliedTo": [
                  "WebRole1"
                ]
              }
            }
          ]
        }
    ```

7. Tam şablonu gözden geçirin.

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "cloudServiceName": {
          "type": "string",
          "metadata": {
            "description": "Name of the cloud service"
          }
        },
        "location": {
          "type": "string",
          "metadata": {
            "description": "Location of the cloud service"
          }
        },
        "deploymentLabel": {
          "type": "string",
          "metadata": {
            "description": "Label of the deployment"
          }
        },
        "packageSasUri": {
          "type": "securestring",
          "metadata": {
            "description": "SAS Uri of the CSPKG file to deploy"
          }
        },
        "configurationSasUri": {
          "type": "securestring",
          "metadata": {
            "description": "SAS Uri of the service configuration (.cscfg)"
          }
        },
        "roles": {
          "type": "array",
          "metadata": {
            "description": "Roles created in the cloud service application"
          }
        },
        "wadPublicConfig_WebRole1": {
          "type": "string",
          "metadata": {
             "description": "Public configuration of Windows Azure Diagnostics extension"
          }
        },
        "wadPrivateConfig_WebRole1": {
          "type": "securestring",
          "metadata": {
            "description": "Private configuration of Windows Azure Diagnostics extension"
          }
        },
        "vnetName": {
          "type": "string",
          "defaultValue": "[concat(parameters('cloudServiceName'), 'VNet')]",
          "metadata": {
            "description": "Name of vitual network"
          }
        },
        "publicIPName": {
          "type": "string",
          "defaultValue": "contosocsIP",
          "metadata": {
            "description": "Name of public IP address"
          }
        },
        "upgradeMode": {
          "type": "string",
          "defaultValue": "Auto",
          "metadata": {
            "UpgradeMode": "UpgradeMode of the CloudService"
          }
        }
      },
      "variables": {
        "cloudServiceName": "[parameters('cloudServiceName')]",
        "subscriptionID": "[subscription().subscriptionId]",
        "dnsName": "[variables('cloudServiceName')]",
        "lbName": "[concat(variables('cloudServiceName'), 'LB')]",
        "lbFEName": "[concat(variables('cloudServiceName'), 'LBFE')]",
        "resourcePrefix": "[concat('/subscriptions/', variables('subscriptionID'), '/resourceGroups/', resourceGroup().name, '/providers/')]"
      },
      "resources": [
        {
          "apiVersion": "2019-08-01",
          "type": "Microsoft.Network/virtualNetworks",
          "name": "[parameters('vnetName')]",
          "location": "[parameters('location')]",
          "properties": {
            "addressSpace": {
              "addressPrefixes": [
                "10.0.0.0/16"
              ]
            },
            "subnets": [
              {
                "name": "WebTier",
                "properties": {
                  "addressPrefix": "10.0.0.0/24"
                }
              }
            ]
          }
        },
        {
          "apiVersion": "2019-08-01",
          "type": "Microsoft.Network/publicIPAddresses",
          "name": "[parameters('publicIPName')]",
          "location": "[parameters('location')]",
          "properties": {
            "publicIPAllocationMethod": "Dynamic",
            "idleTimeoutInMinutes": 10,
            "publicIPAddressVersion": "IPv4",
            "dnsSettings": {
              "domainNameLabel": "[variables('dnsName')]"
            }
          },
          "sku": {
            "name": "Basic"
          }
        },
        {
          "apiVersion": "2021-03-01",
          "type": "Microsoft.Compute/cloudServices",
          "name": "[variables('cloudServiceName')]",
          "location": "[parameters('location')]",
          "tags": {
            "DeploymentLabel": "[parameters('deploymentLabel')]",
            "DeployFromVisualStudio": "true"
          },
          "dependsOn": [
            "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]",
            "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPName'))]"
          ],
          "properties": {
            "packageUrl": "[parameters('packageSasUri')]",
            "configurationUrl": "[parameters('configurationSasUri')]",
            "upgradeMode": "[parameters('upgradeMode')]",
            "roleProfile": {
              "roles": [
                {
                  "name": "WebRole1",
                  "sku": {
                    "name": "Standard_D1_v2",
                    "capacity": "1"
                  }
                },
                {
                  "name": "WorkerRole1",
                  "sku": {
                    "name": "Standard_D1_v2",
                    "capacity": "1"
                  }
                }
              ]
            },
            "networkProfile": {
              "loadBalancerConfigurations": [
                {
                  "id": "[concat(variables('resourcePrefix'), 'Microsoft.Network/loadBalancers/', variables('lbName'))]",
                  "name": "[variables('lbName')]",
                  "properties": {
                    "frontendIPConfigurations": [
                      {
                        "name": "[variables('lbFEName')]",
                        "properties": {
                          "publicIPAddress": {
                            "id": "[concat(variables('resourcePrefix'), 'Microsoft.Network/publicIPAddresses/', parameters('publicIPName'))]"
                          }
                        }
                      }
                    ]
                  }
                }
              ]
            },
            "osProfile": {
              "secrets": [
                {
                  "sourceVault": {
                    "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.KeyVault/vaults/{keyvault-name}"
                  },
                  "vaultCertificates": [
                    {
                      "certificateUrl": "https://{keyvault-name}.vault.azure.net:443/secrets/ContosoCertificate/{secret-id}"
                    }
                  ]
                }
              ]
            },
            "extensionProfile": {
              "extensions": [
                {
                  "name": "RDPExtension",
                  "properties": {
                    "autoUpgradeMinorVersion": true,
                    "publisher": "Microsoft.Windows.Azure.Extensions",
                    "type": "RDP",
                    "typeHandlerVersion": "1.2.1",
                    "settings": "<PublicConfig>\r\n <UserName>[Insert Username]</UserName>\r\n <Expiration>1/21/2022 12:00:00 AM</Expiration>\r\n</PublicConfig>",
                    "protectedSettings": "<PrivateConfig>\r\n <Password>[Insert Password]</Password>\r\n</PrivateConfig>"
                  }
                },
                {
                  "name": "Microsoft.Insights.VMDiagnosticsSettings_WebRole1",
                  "properties": {
                    "autoUpgradeMinorVersion": true,
                    "publisher": "Microsoft.Azure.Diagnostics",
                    "type": "PaaSDiagnostics",
                    "typeHandlerVersion": "1.5",
                    "settings": "[parameters('wadPublicConfig_WebRole1')]",
                    "protectedSettings": "[parameters('wadPrivateConfig_WebRole1')]",
                    "rolesAppliedTo": [
                      "WebRole1"
                  ]
                }
              }
            ]
          }
        }
       }
      ]
    }
    ```

8. Bulut hizmeti (genişletilmiş destek) dağıtımını oluşturmak için şablon ve parametre dosyasını (şablon dosyasında parametreleri tanımlama) dağıtın. Lütfen bu [örnek şablonlara](https://github.com/Azure-Samples/cloud-services-extended-support) gereken şekilde bakın.

    ```powershell
    New-AzResourceGroupDeployment -ResourceGroupName "ContosOrg" -TemplateFile "file path to your template file" -TemplateParameterFile "file path to your parameter file"
    ```

## <a name="next-steps"></a>Sonraki adımlar 

- Cloud Services için [sık sorulan soruları](faq.md) gözden geçirin (genişletilmiş destek).
- [Azure Portal](deploy-portal.md), [PowerShell](deploy-powershell.md), [şablon](deploy-template.md) veya [Visual Studio](deploy-visual-studio.md)kullanarak bir bulut hizmeti (genişletilmiş destek) dağıtın.
- [Cloud Services (genişletilmiş destek) örnekleri deposunu](https://github.com/Azure-Samples/cloud-services-extended-support) ziyaret edin
