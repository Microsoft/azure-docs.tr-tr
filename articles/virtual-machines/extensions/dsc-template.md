---
title: Azure Resource Manager şablonlarıyla istenen durum yapılandırma uzantısı
description: Azure 'da Istenen durum yapılandırması (DSC) uzantısı için Kaynak Yöneticisi şablonu tanımı hakkında bilgi edinin.
services: virtual-machines
author: mgoedtel
tags: azure-resource-manager
keywords: dsc
ms.assetid: b5402e5a-1768-4075-8c19-b7f7402687af
ms.service: virtual-machines
ms.subservice: extensions
ms.collection: windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 02/09/2021
ms.author: magoedte
ms.openlocfilehash: 6d22e93fe8bd36e24520108bc310d5b08c25120c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102560030"
---
# <a name="desired-state-configuration-extension-with-azure-resource-manager-templates"></a>Azure Resource Manager şablonlarıyla istenen durum yapılandırma uzantısı

Bu makalede, [Istenen durum yapılandırması (DSC) uzantı işleyicisi](dsc-overview.md)için Azure Resource Manager şablonu açıklanmaktadır. Örneklerin birçoğu **Registrationurl** 'Yi (dize olarak sunulur) ve **Registrationkey** (Azure Otomasyonu ile birlikte eklemek Için bir [PSCredential](/dotnet/api/system.management.automation.pscredential) olarak sunulur) kullanır. Bu değerleri alma hakkında daha fazla bilgi için bkz. [Azure Otomasyonu durum yapılandırması ile yönetimi için makineleri ekleme-güvenli kayıt](../../automation/automation-dsc-onboarding.md#enable-machines-securely-using-registration).

> [!NOTE]
> Biraz farklı şema örnekleriyle karşılaşabilirsiniz. Þemada değişiklik, Ekim 2016 sürümünde oluştu. Ayrıntılar için bkz. [önceki bir biçimden güncelleştirme](#update-from-a-previous-format).

## <a name="template-example-for-a-windows-vm"></a>Windows VM için şablon örneği

Aşağıdaki kod parçacığı, şablonun **kaynak** bölümüne gider.
DSC Uzantısı varsayılan uzantı özelliklerini devralır.
Daha fazla bilgi için bkz. [Virtualmachineextension sınıfı](/dotnet/api/microsoft.azure.management.compute.models.virtualmachineextension).

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "[concat(parameters('VMName'), '/Microsoft.Powershell.DSC')]",
  "apiVersion": "2018-06-01",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', parameters('VMName'))]"
  ],
  "properties": {
    "publisher": "Microsoft.Powershell",
    "type": "DSC",
    "typeHandlerVersion": "2.77",
    "autoUpgradeMinorVersion": true,
    "protectedSettings": {
      "Items": {
        "registrationKeyPrivate": "[listKeys(resourceId('Microsoft.Automation/automationAccounts/', parameters('automationAccountName')), '2018-06-30').Keys[0].value]"
      }
    },
    "settings": {
      "Properties": [
        {
          "Name": "RegistrationKey",
          "Value": {
            "UserName": "PLACEHOLDER_DONOTUSE",
            "Password": "PrivateSettingsRef:registrationKeyPrivate"
          },
          "TypeName": "System.Management.Automation.PSCredential"
        },
        {
          "Name": "RegistrationUrl",
          "Value": "[reference(concat('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))).registrationUrl]",
          "TypeName": "System.String"
        },
        {
          "Name": "NodeConfigurationName",
          "Value": "[parameters('nodeConfigurationName')]",
          "TypeName": "System.String"
        }
      ]
    }
  }
}
```

## <a name="template-example-for-windows-virtual-machine-scale-sets"></a>Windows sanal makine ölçek kümeleri için şablon örneği

Bir sanal makine ölçek kümesi düğümünün **Virtualmachineprofile, extensionprofile** özniteliğine sahip bir **Özellikler** bölümü vardır.
**Uzantılar** altında DSC uzantısının ayrıntılarını ekleyin.

DSC Uzantısı varsayılan uzantı özelliklerini devralır.
Daha fazla bilgi için bkz. [VirtualMachineScaleSetExtension Class](/dotnet/api/microsoft.azure.management.compute.models.virtualmachinescalesetextension).

```json
"extensionProfile": {
    "extensions": [
      {
        "name": "Microsoft.Powershell.DSC",
        "properties": {
          "publisher": "Microsoft.Powershell",
          "type": "DSC",
          "typeHandlerVersion": "2.77",
          "autoUpgradeMinorVersion": true,
          "protectedSettings": {
            "Items": {
              "registrationKeyPrivate": "[listKeys(resourceId('Microsoft.Automation/automationAccounts/', parameters('automationAccountName')), '2018-06-30').Keys[0].value]"
            }
          },
          "settings": {
            "Properties": [
              {
                "Name": "RegistrationKey",
                "Value": {
                  "UserName": "PLACEHOLDER_DONOTUSE",
                  "Password": "PrivateSettingsRef:registrationKeyPrivate"
                },
                "TypeName": "System.Management.Automation.PSCredential"
              },
              {
                "Name": "RegistrationUrl",
                "Value": "[reference(concat('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))).registrationUrl]",
                "TypeName": "System.String"
              },
              {
                "Name": "NodeConfigurationName",
                "Value": "[parameters('nodeConfigurationName')]",
                "TypeName": "System.String"
              }
            ]
          }
        }
      }
    ]
  }
```

## <a name="detailed-settings-information"></a>Ayrıntılı ayar bilgileri

Kaynak Yöneticisi şablonunda Azure DSC uzantısının **Ayarlar** bölümünde aşağıdaki şemayı kullanın.

Varsayılan yapılandırma betiği için kullanılabilen bağımsız değişkenlerin bir listesi için bkz. [varsayılan yapılandırma betiği](#default-configuration-script).

```json
"settings": {
    "wmfVersion": "latest",
    "configuration": {
        "url": "http://validURLToConfigLocation",
        "script": "ConfigurationScript.ps1",
        "function": "ConfigurationFunction"
    },
    "configurationArguments": {
        "argument1": "Value1",
        "argument2": "Value2"
    },
    "configurationData": {
        "url": "https://foo.psd1"
    },
    "privacy": {
        "dataCollection": "enable"
    },
    "advancedOptions": {
        "downloadMappings": {
            "customWmfLocation": "http://myWMFlocation"
        }
    }
},
"protectedSettings": {
    "configurationArguments": {
        "parameterOfTypePSCredential1": {
            "userName": "UsernameValue1",
            "password": "PasswordValue1"
        },
        "parameterOfTypePSCredential2": {
            "userName": "UsernameValue2",
            "password": "PasswordValue2"
        }
    },
    "configurationUrlSasToken": "?g!bber1sht0k3n",
    "configurationDataUrlSasToken": "?dataAcC355T0k3N"
}
```

## <a name="details"></a>Ayrıntılar

| Özellik adı | Tür | Description |
| --- | --- | --- |
| Settings. wmfVersion |string |Sanal makinenize yüklenmesi gereken Windows Management Framework (WMF) sürümünü belirtir. Bu özelliğin **en son** olarak ayarlanması WMF 'nin en son sürümünü yüklüyor. Şu anda bu özellik için olası tek değerler **4,0**, **5,0**, **5,1** ve **en son** değerlerdir. Bu olası değerler güncelleştirmelere tabidir. Varsayılan değer **en** sonuncusudur. |
| settings.configacation. URL |string |DSC yapılandırması. zip dosyanızın indirileceği URL konumunu belirtir. Belirtilen URL erişim için bir SAS belirteci gerektiriyorsa, **protectedSettings.configurationUrlSasToken** özelliğini SAS belirtecinizin değerine ayarlayın. **settings.configuration. Script** veya **settings.configuration. Function** tanımlanırsa bu özellik gereklidir. Bu özellikler için herhangi bir değer verilmezse, uzantı konum Configuration Manager (LCM) meta verilerini ayarlamak için varsayılan yapılandırma betiğini çağırır ve bağımsız değişkenlerin sağlanması gerekir. |
| settings.configuration. betiği |string |DSC yapılandırmanızın tanımını içeren betiğin dosya adını belirtir. Bu betik, **settings.configuration. URL** özelliği tarafından belirtilen URL 'den indirilen. zip dosyasının kök klasöründe olmalıdır. **settings.configuration. URL** veya **settings.configuration. Script** tanımlanmışsa bu özellik gereklidir. Bu özellikler için hiçbir değer verilmezse, uzantı, LCM meta verilerini ayarlamak için varsayılan yapılandırma betiğini çağırır ve bağımsız değişkenlerin sağlanması gerekir. |
| settings.configuration. Function |string |DSC yapılandırmanızın adını belirtir. Adlı yapılandırma, **settings.configuration. Script** tarafından tanımlanan betiğe eklenmelidir. **settings.configuration. URL** veya **settings.configuration. Function** tanımlanırsa bu özellik gereklidir. Bu özellikler için hiçbir değer verilmezse, uzantı, LCM meta verilerini ayarlamak için varsayılan yapılandırma betiğini çağırır ve bağımsız değişkenlerin sağlanması gerekir. |
| settings.configurationArguments |Koleksiyon |DSC yapılandırmanıza geçirmek istediğiniz parametreleri tanımlar. Bu özellik şifrelenmedi. |
| settings.configurationData. URL |string |DSC yapılandırmanız için giriş olarak kullanılacak yapılandırma verileri (. psd1) dosyanızın indirileceği URL 'YI belirtir. Belirtilen URL erişim için bir SAS belirteci gerektiriyorsa, **protectedSettings.configurationDataUrlSasToken** özelliğini SAS belirtecinizin değerine ayarlayın. |
| Settings. privacy. dataCollection |string |Telemetri toplamayı etkinleştirilir veya devre dışı bırakır. Bu özelliğin olası tek değeri **Etkinleştir**, **devre dışı bırak**, **' '** veya **$null**. Bu özelliğin boş veya null bırakılması telemetri sunar. Varsayılan değer **' '**. Daha fazla bilgi için bkz. [Azure DSC Uzantısı veri toplama](https://devblogs.microsoft.com/powershell/azure-dsc-extension-data-collection-2/). |
| Settings. Advancedoçen. downloadMappings |Koleksiyon |WMF 'nin indirileceği alternatif konumları tanımlar. Daha fazla bilgi için bkz. [Azure DSC uzantısı 2,8 ve uzantı bağımlılıklarını karşıdan yüklemelerin kendi konumunuza nasıl eşleneceğini öğrenin](https://devblogs.microsoft.com/powershell/azure-dsc-extension-2-8-how-to-map-downloads-of-the-extension-dependencies-to-your-own-location/). |
| protectedSettings.configurationArguments |Koleksiyon |DSC yapılandırmanıza geçirmek istediğiniz parametreleri tanımlar. Bu özellik şifrelenir. |
| protectedSettings.configurationUrlSasToken |string |**settings.configuration. URL** 'nın tanımladığı URL 'ye erişmek IÇIN kullanılacak SAS belirtecini belirtir. Bu özellik şifrelenir. |
| protectedSettings.configurationDataUrlSasToken |string |**settings.configurationData. URL** 'nın tanımladığı URL 'ye erişmek IÇIN kullanılacak SAS belirtecini belirtir. Bu özellik şifrelenir. |

## <a name="default-configuration-script"></a>Varsayılan yapılandırma betiği

Aşağıdaki değerler hakkında daha fazla bilgi için bkz. [yerel Configuration Manager temel ayarları](/powershell/scripting/dsc/managing-nodes/metaConfig#basic-settings).
Yalnızca aşağıdaki tabloda listelenen LCM özelliklerini yapılandırmak için DSC Uzantısı varsayılan yapılandırma komut dosyasını kullanabilirsiniz.

| Özellik adı | Tür | Description |
| --- | --- | --- |
| protectedSettings.configurationArguments. RegistrationKey |PSCredential |Gerekli özellik. Bir düğüm için bir PowerShell kimlik bilgisi nesnesinin parolası olarak Azure Automation hizmetine kaydolmak üzere kullanılan anahtarı belirtir. Bu değer, Otomasyon hesabına yönelik **ListKeys 'i al** yöntemi kullanılarak otomatik olarak bulunabilir.  [Örneğe](#example-using-referenced-azure-automation-registration-values)bakın. |
| settings.configurationArguments. RegistrationUrl |string |Gerekli özellik. Düğümün kaydolmaya çalıştığı Otomasyon uç noktasının URL 'sini belirtir. Bu değer, Otomasyon hesabında **başvuru** yöntemi kullanılarak otomatik olarak bulunabilir. |
| settings.configurationArguments. NodeConfigurationName |string |Gerekli özellik. Düğüme atanacak Otomasyon hesabındaki düğüm yapılandırmasını belirtir. |
| settings.configurationArguments.ConfigurationMode |string |LCM modunu belirtir. Geçerli seçenekler **Applyonly**, **Applyandmonitor** ve **applyandadutocorrect** içerir.  Varsayılan değer **Applyandmonitor**' dır. |
| settings.configurationArguments. RefreshFrequencyMins | Int32 | LCM 'nin güncelleştirmeler için Otomasyon hesabını ne sıklıkta kontrol etmek için deneme sayısını belirtir.  Varsayılan değer **30**' dur.  Minimum değer **15**' tir. |
| settings.configurationArguments.ConfigUrationmodefısmins | Int32 | LCM 'nin geçerli yapılandırmayı ne sıklıkta doğrulayacağını belirtir. Varsayılan değer **15**' tir. Minimum değer **15**' tir. |
| settings.configurationArguments. Rebootnodeifgerekiyordu | boolean | Bir DSC işlemi istediğinde bir düğümün otomatik olarak yeniden başlatılıp başlatılmayacağını belirtir. Varsayılan değer **false**'dur. |
| settings.configurationArguments. ActionAfterReboot | string | Bir yapılandırma uygulanırken yeniden başlatmanın ardından ne olacağını belirtir. Geçerli seçenekler **devam yapılandırması** ve **durma yapılandırması**. Varsayılan değer **devam yapılandırması**' dır. |
| settings.configurationArguments. AllowModuleOverwrite | boolean | LCM 'nin düğümdeki mevcut modüllerin üzerine yazmayacağını belirtir. Varsayılan değer **false**'dur. |

## <a name="settings-vs-protectedsettings"></a>ayarlar ve protectedSettings karşılaştırması

Tüm ayarlar VM 'deki bir ayarlar metin dosyasına kaydedilir.
**Ayarlar** altında listelenen özellikler ortak özelliklerdir.
Ortak özellikler, ayarlar metin dosyasında şifrelenmez.
**Protectedsettings** altında listelenen özellikler bir sertifikayla ŞIFRELENIR ve VM 'deki ayarlar dosyasında düz metin olarak gösterilmez.

Yapılandırma kimlik bilgilerine ihtiyaç duyuyorsa, bu kimlik bilgilerini **Protectedsettings**'e dahil edebilirsiniz:

```json
"protectedSettings": {
    "configurationArguments": {
        "parameterOfTypePSCredential1": {
               "userName": "UsernameValue1",
               "password": "PasswordValue1"
        }
    }
}
```

## <a name="example-configuration-script"></a>Örnek yapılandırma betiği

Aşağıdaki örnek, LCM 'ye meta veri ayarları sağlamak ve Automation DSC hizmetine kaydetmek için olan DSC uzantısının varsayılan davranışını gösterir.
Yapılandırma bağımsız değişkenleri gereklidir.
Yapılandırma bağımsız değişkenleri, LCM meta verilerini ayarlamak için varsayılan yapılandırma betiğine geçirilir.

```json
"settings": {
    "configurationArguments": {
        "RegistrationUrl" : "[parameters('registrationUrl1')]",
        "NodeConfigurationName" : "nodeConfigurationNameValue1"
    }
},
"protectedSettings": {
    "configurationArguments": {
        "RegistrationKey": {
            "userName": "NOT_USED",
            "Password": "registrationKey"
        }
    }
}
```

## <a name="example-using-the-configuration-script-in-azure-storage"></a>Azure Storage 'da yapılandırma betiğini kullanma örneği

Aşağıdaki örnek [DSC uzantı işleyicisine genel bakış](dsc-overview.md)' dır.
Bu örnek, uzantıyı dağıtmak için cmdlet 'ler yerine Kaynak Yöneticisi şablonları kullanır.
IisInstall.ps1 yapılandırmasını kaydedin, bir. zip dosyasına (örnek: `iisinstall.zip` ) yerleştirin ve ardından dosyayı erişilebilir BIR URL 'ye yükleyin.
Bu örnek Azure Blob depolamayı kullanır, ancak herhangi bir rasgele konumdan. zip dosyaları indirebilirsiniz.

Kaynak Yöneticisi şablonunda aşağıdaki kod VM 'nin doğru dosyayı indirmesini ve ardından uygun PowerShell işlevini çalıştırmasını söyler:

```json
"settings": {
    "configuration": {
        "url": "https://demo.blob.core.windows.net/iisinstall.zip",
        "script": "IisInstall.ps1",
        "function": "IISInstall"
    }
},
"protectedSettings": {
    "configurationUrlSasToken": "odLPL/U1p9lvcnp..."
}
```

## <a name="example-using-referenced-azure-automation-registration-values"></a>Başvurulan Azure Otomasyonu kayıt değerlerini kullanan örnek

Aşağıdaki örnek, Azure Otomasyonu hesap özelliklerine başvurarak ve birincil anahtarı (0) almak için **ListKeys 'i al** metodunu kullanarak **Registrationurl** ve **registrationkey** 'i alır.  Bu örnekte, şablonuna **Automationaccountname** ve **nodeconfigname** parametreleri sağlandı.

```json
"settings": {
    "RegistrationUrl" : "[reference(concat('Microsoft.Automation/automationAccounts/', parameters('automationAccountName'))).registrationUrl]",
    "NodeConfigurationName" : "[parameters('NodeConfigName')]"
},
"protectedSettings": {
    "configurationArguments": {
        "RegistrationKey": {
            "userName": "NOT_USED",
            "Password": "[listKeys(resourceId('Microsoft.Automation/automationAccounts/', parameters('automationAccountName')), '2018-01-15').Keys[0].value]"
        }
    }
}
```

## <a name="update-from-a-previous-format"></a>Önceki bir biçimden Güncelleştir

Uzantının önceki biçimindeki tüm ayarlar (ve genel Özellikler **ModulesUrl**, **modulesource**, **moduleversion**, **configurationfunction**, **sastoken** veya **Özellikler**), uzantının geçerli biçimine otomatik olarak uyum sağlar.
Yalnızca daha önce olduğu gibi çalışır.

Aşağıdaki şemada, önceki ayarlar şemasının nasıl göründüğünü gösterilmektedir:

```json
"settings": {
    "WMFVersion": "latest",
    "ModulesUrl": "https://UrlToZipContainingConfigurationScript.ps1.zip",
    "SasToken": "SAS Token if ModulesUrl points to private Azure Blob Storage",
    "ConfigurationFunction": "ConfigurationScript.ps1\\ConfigurationFunction",
    "Properties": {
        "ParameterToConfigurationFunction1": "Value1",
        "ParameterToConfigurationFunction2": "Value2",
        "ParameterOfTypePSCredential1": {
            "UserName": "UsernameValue1",
            "Password": "PrivateSettingsRef:Key1"
        },
        "ParameterOfTypePSCredential2": {
            "UserName": "UsernameValue2",
            "Password": "PrivateSettingsRef:Key2"
        }
    }
},
"protectedSettings": {
    "Items": {
        "Key1": "PasswordValue1",
        "Key2": "PasswordValue2"
    },
    "DataBlobUri": "https://UrlToConfigurationDataWithOptionalSasToken.psd1"
}
```

Önceki biçimin geçerli biçime uyum şu şekildedir:

| Geçerli özellik adı | Önceki şema eşdeğeri |
| --- | --- |
| Settings. wmfVersion |Ayarlar. WMFVersion |
| settings.configacation. URL |Ayarlar. ModulesUrl |
| settings.configuration. betiği |settings.ConfigurationFunction 'ın ilk kısmı (önce \\ \\ ) |
| settings.configuration. Function |settings.ConfigurationFunction 'ın ikinci parçası (sonra \\ \\ ) |
| settings.configuration.module.name | Ayarlar. ModuleSource |
| settings.configuration. Module. Version | Ayarlar. ModuleVersion |
| settings.configurationArguments |Ayarlar. Özelliklerinin |
| settings.configurationData. URL |protectedSettings. Davtabloburi (SAS belirteci olmayan) |
| Settings. privacy. dataCollection |Ayarlar. Gizlilik. dataCollection |
| Settings. Advancedoçen. downloadMappings |Ayarlar. Advancedoçen. DownloadMappings |
| protectedSettings.configurationArguments |protectedSettings. Properties |
| protectedSettings.configurationUrlSasToken |Ayarlar. SasToken |
| protectedSettings.configurationDataUrlSasToken |ProtectedSettings. Dadburi 'den SAS belirteci |

## <a name="troubleshooting"></a>Sorun giderme

İşte kullanabileceğiniz bazı hatalar ve bunları nasıl çözebilirim.

### <a name="invalid-values"></a>Geçersiz değerler

"Gizlilik. dataCollection ' {0} '.
Tek olası değerler şunlardır ' ', ' Enable ' ve ' Disable ' ".
"WmfVersion ' {0} '.
Yalnızca olası değerler şunlardır... ve ' Latest ' ".

**Sorun**: belirtilen değere izin verilmiyor.

**Çözüm**: geçersiz değeri geçerli bir değer olacak şekilde değiştirin.
Daha fazla bilgi için [Ayrıntılar](#details)bölümündeki tabloya bakın.

### <a name="invalid-url"></a>Geçersiz URL

"ConfigurationData. URL ' {0} '. Bu geçerli bir URL "", "" Otabloburı ' {0} ' değil. Bu geçerli bir URL "" yapılandırması değil. URL ' {0} '. Bu geçerli bir URL değil "

**Sorun**: BELIRTILEN bir URL geçerli değil.

**Çözüm**: tüm sağlanmış URL 'leri denetleyin.
Tüm URL 'Lerin, uzantının uzak makinede erişebileceği geçerli konumlara çözümlendiğinden emin olun.

### <a name="invalid-registrationkey-type"></a>Geçersiz RegistrationKey türü

"PSCredential türünde parametre RegistrationKey için geçersiz tür".

**Sorun**: protectedSettings.configurationArguments Içindeki *Registrationkey* değeri, PSCredential dışında herhangi bir tür olarak sağlanamaz.

**Çözüm**: registrationkey Için protectedSettings.configurationArguments girdinizi aşağıdaki biçimi kullanarak bir PSCredential türüne değiştirin:

```json
"configurationArguments": {
    "RegistrationKey": {
        "userName": "NOT_USED",
        "Password": "RegistrationKey"
    }
}
```

### <a name="invalid-configurationargument-type"></a>Geçersiz ConfigurationArgument türü

"Geçersiz configurationArguments türü {0} "

**Sorun**: *configurationarguments* özelliği bir **karma tablo** nesnesine çözümlenemiyor.

**Çözüm**: *configurationarguments* özelliğini bir **karma tablo** yapın.
Yukarıdaki örneklerde belirtilen biçimi izleyin. Tırnak işaretleri, virgüller ve küme ayraçları için izleyin.

### <a name="duplicate-configurationarguments"></a>Yinelenen ConfigurationArguments

" {0} Hem genel hem de korumalı configurationArguments içinde yinelenen bağımsız değişkenler ' ' bulundu"

**Sorun**: ortak ayarlarındaki *configurationarguments* ve korunan ayarlarındaki *configurationarguments* aynı ada sahip özelliklere sahip.

**Çözüm**: yinelenen özelliklerden birini kaldırın.

### <a name="missing-properties"></a>Eksik Özellikler

"settings.Configuration. Function, settings.configuration. URL veya settings.configuration. Module belirtilmesini gerektirir"

"settings.Configuration. URL, settings.configuration. betiğin belirtilmesini gerektirir"

"settings.Configuration. Script, settings.configuration. URL belirtilmesini gerektirir"

"settings.Configuration. URL, settings.configuration. işlevin belirtilmesini gerektirir"

"protectedSettings.ConfigurationUrlSasToken, settings.configuration. URL 'nin belirtilmesini gerektirir"

"protectedSettings.ConfigurationDataUrlSasToken, settings.configurationData. URL 'nin belirtilmesini gerektirir"

**Sorun**: tanımlı bir özellik, eksik olan başka bir özelliğe ihtiyaç duyuyor.

**Çözümler**:

- Eksik özelliği sağlayın.
- Eksik özelliğe ihtiyacı olan özelliği kaldırın.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure DSC Uzantısı ile sanal makine ölçek kümeleri kullanma](../../virtual-machine-scale-sets/virtual-machine-scale-sets-dsc.md)hakkında bilgi edinin.
- [DSC 'nin güvenli kimlik bilgisi yönetimi](dsc-credentials.md)hakkında daha fazla ayrıntı bulun.
- [Azure DSC uzantı işleyicisine bir giriş](dsc-overview.md)alın.
- PowerShell DSC hakkında daha fazla bilgi için [PowerShell belge merkezine](/powershell/scripting/dsc/overview/overview)gidin.
