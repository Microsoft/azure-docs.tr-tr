---
title: Windows 'da Linux için Azure IoT Edge PowerShell betikleri | Microsoft Docs
description: Windows PowerShell betiklerine yönelik Windows sanal makinelerinde Linux için IoT Edge dağıtmak, sağlamak ve durum Azure IoT Edge için başvuru bilgileri.
author: v-tcassi
manager: philmea
ms.author: v-tcassi
ms.date: 02/16/2021
ms.topic: reference
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: a24b39107d8f78c049afa230fe678ec92852eeb0
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105959696"
---
# <a name="powershell-scripts-for-iot-edge-for-linux-on-windows"></a>Windows üzerinde Linux için IoT Edge PowerShell betikleri

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Windows sanal makinesinde Linux için IoT Edge dağıtım, sağlama ve durum almak için gereken PowerShell betiklerini anlayın.

Bu makalede açıklanan komutlar `AzureEFLOW.psm1` , altında dizininizde bulunan sisteminizde bulunan dosyasıdır `WindowsPowerShell` `C:\Program Files\WindowsPowerShellModules\AzureEFLOW` .

## <a name="deploy-eflow"></a>Deploy-Eflow

**Dağıt-eFlow** komutu ana dağıtım yöntemidir. Dağıtım komutu sanal makineyi oluşturur, dosyaları hazırlar ve IoT Edge aracı modülünü dağıtır. Parametrelerin hiçbiri gerekli olmasa da, dağıtım sırasında IoT Edge cihazınızı sağlamak ve oluşturma sırasında sanal makinenin ayarlarını değiştirmek için kullanılabilirler. Tam liste için komutunu kullanın `Get-Help Deploy-Eflow -full` .  

>[!NOTE]
>Hazırlama türü için, **x509** Şu anda özel olarak bir [Azure IoT Hub cihaz sağlama hizmeti](../iot-dps/about-iot-dps.md)kullanarak x509 sağlama anlamına gelir. El ile x509 sağlama yöntemi şu anda desteklenmiyor.

| Parametre | Kabul edilen değerler | Yorumlar |
| --------- | --------------- | -------- |
| eflowVhdxDir | Dizin yolu | Dağıtımın sanal makine için VHDX dosyasını depoladığı dizin. |
| provisioningType | **el ile**, **TPM**, **x509** veya **simetrik** |  IoT Edge cihazınız için kullanmak istediğiniz sağlama türünü tanımlar. |
| devConnString | Mevcut bir IoT Edge cihazının cihaz bağlantı dizesi | IoT Edge cihazı el ile sağlamaya yönelik cihaz bağlantı dizesi (**el ile**). |
| symmKey | Mevcut bir DPS kaydı için birincil anahtar veya simetrik anahtarlar kullanılarak kaydedilen mevcut bir IoT Edge cihazının birincil anahtarı | IoT Edge cihazının sağlanması için simetrik anahtar (**x509** veya **simetrik**). |
| KML | Mevcut bir DPS örneğinin kapsam KIMLIĞI. | IoT Edge cihazının (**x509** veya **simetrik**) sağlanması için kapsam kimliği. |
| RegistrationId | Mevcut bir IoT Edge cihazının kayıt KIMLIĞI | IoT Edge cihazının (**x509** veya **simetrik**) sağlanması için kayıt kimliği. |
| ıdentitycertlocvm | Dizin yolu; hizmet tarafından sahip olunabilir bir klasörde olmalıdır `iotedge` | IoT Edge cihazının (**x509** veya **simetrik**) sağlanması için sanal makinenizde kimlik sertifikasının mutlak hedef yolu. |
| ıdentitycertlocwin | Dizin yolu | Bir IoT Edge cihazının (**x509** veya **simetrik**) sağlanması için Windows 'da kimlik sertifikasının mutlak kaynak yolu. |
| identityPkLocVm | Dizin yolu; hizmet tarafından sahip olunabilir bir klasörde olmalıdır `iotedge` | IoT Edge cihazının (**x509** veya **simetrik**) sağlanması için sanal makinenizde kimlik özel anahtarının mutlak hedef yolu. |
| identityPkLocWin | Dizin yolu | Bir IoT Edge cihazının (**x509** veya **simetrik**) sağlanması için Windows 'daki kimlik özel anahtarının mutlak kaynak yolu. |
| vmSizeDefintion | En fazla 30 karakter uzunluğunda | Sanal makine için çekirdek sayısının ve kullanılabilir RAM 'in tanımı. **Varsayılan değer**: Standard_K8S_v1. |
| vmDiskSize | 8 GB ile 256 GB arasında | Dinamik olarak genişleyen sanal sabit diskin en büyük disk boyutu. **Varsayılan değer**: 16 GB. |
| vmUser | En fazla 30 karakter uzunluğunda | Sanal makinede oturum açmak için Kullanıcı adı. |
| vnetType | **Saydam** veya **ICS** | Sanal anahtar türü. **Varsayılan değer**: saydam. Saydam bir dış anahtara başvurur, ancak ICS bir iç anahtara başvurur. |
| vnetName | 64 karakterden daha uzun | Sanal anahtarın adı. **Varsayılan değer**: dış. |
| enableVtpm | Yok | **Anahtar parametresi**. TPM etkin veya devre dışı olan sanal makineyi oluşturun. |
| mobyPackageVersion | En fazla 30 karakter uzunluğunda |  Sanal makinede doğrulanacak veya yüklenecek Moby paketinin sürümü.  **Varsayılan değer:** 19.03.11. |
| iotedgePackageVersion | En fazla 30 karakter uzunluğunda | Sanal makinede doğrulanacak veya yüklenecek IoT Edge paketinin sürümü. **Varsayılan değer:** 1.1.0. |
| InstallPackages | Yok | **Anahtar parametresi**. Bu işlem açıldığında, komut dosyası yalnızca paketlerin mevcut olduğunu doğrulamak yerine Moby ve IoT Edge paketlerini yüklemeyi dener. |

>[!NOTE]
>Varsayılan olarak, işlem ada sahip bir dış anahtar bulamazsa `External` , BIR IP adresi almak için mevcut herhangi bir dış anahtarı arar. Kullanılabilir dış anahtar yoksa, bir iç anahtar arar. Kullanılabilir bir iç anahtar yoksa, bir IP adresi almak için varsayılan anahtarı oluşturmaya çalışır.

## <a name="verify-eflowvm"></a>Verify-EflowVm

**Verify-EflowVm** komutu, Windows sanal makinesinde Linux için IoT Edge oluşturulduğunu denetlemek için sunulan bir işlevdir. Yalnızca ortak parametreleri alır ve sanal makine oluşturulduysa **true** , değilse **false** döndürür. Daha fazla bilgi için komutunu kullanın `Get-Help Verify-EflowVm -full` .

## <a name="provision-eflowvm"></a>Provision-EflowVm

**Sağlama-EflowVm** komutu, IoT Edge cihazınız için sağlama bilgilerini sanal makinenin IoT Edge `config.yaml` dosyasına ekler. Dağıtım aşaması sırasında, **dağıtma-eFlow** komutundaki parametreleri ayarlayarak sağlama de yapılabilir. Daha fazla bilgi için komutunu kullanın `Get-Help Provision-EflowVm -full` .

| Parametre | Kabul edilen değerler | Yorumlar |
| --------- | --------------- | -------- |
| vmUser | En fazla 30 karakter uzunluğunda | Sanal makinede oturum açmak için Kullanıcı adı. |
| provisioningType | **el ile**, **TPM**, **x509** veya **simetrik** |  IoT Edge cihazınız için kullanmak istediğiniz sağlama türünü tanımlar. |
| devConnString | Mevcut bir IoT Edge cihazının cihaz bağlantı dizesi | IoT Edge cihazı el ile sağlamaya yönelik cihaz bağlantı dizesi (**el ile**). |
| symmKey | Mevcut bir DPS kaydı için birincil anahtar veya simetrik anahtarlar kullanılarak kaydedilen mevcut bir IoT Edge cihazının birincil anahtarı | IoT Edge cihaz sağlamak için simetrik anahtar (**DPS**, **simetrik**). |
| KML | Mevcut bir DPS örneğinin kapsam KIMLIĞI. | IoT Edge cihazının (**DPS**) sağlanması IÇIN kapsam kimliği. |
| RegistrationId | Mevcut bir IoT Edge cihazının kayıt KIMLIĞI | IoT Edge cihazının (**DPS**) sağlanması IÇIN kayıt kimliği. |
| ıdentitycertlocvm | Dizin yolu; hizmet tarafından sahip olunabilir bir klasörde olmalıdır `iotedge` | Bir IoT Edge cihazının (**DPS**, **x509**) sağlanması için sanal makinenizde kimlik sertifikasının mutlak hedef yolu. |
| ıdentitycertlocwin | Dizin yolu | IoT Edge cihaz sağlamak için Windows 'ta kimlik sertifikasının mutlak kaynak yolu (**DPS**, **x509**). |
| identityPkLocVm  | Dizin yolu; hizmet tarafından sahip olunabilir bir klasörde olmalıdır `iotedge` | Bir IoT Edge cihazının (**DPS**, **x509**) sağlanması için sanal makinenizde kimlik özel anahtarının mutlak hedef yolu. |
| identityPkLocWin | Dizin yolu | Bir IoT Edge cihazının (**DPS**, **x509**) sağlanması için Windows 'daki kimlik özel anahtarının mutlak kaynak yolu. |

## <a name="get-eflowvmname"></a>Get-EflowVmName

**Get-EflowVmName** komutu, sanal makinenin geçerli ana bilgisayar adını sorgulamak için kullanılır. Bu komut, Windows ana bilgisayar adının zaman içinde değiştiremeyeceği gerçeğine yönelik hesapta bulunur. Yalnızca ortak parametreleri alır ve sanal makinenin geçerli ana bilgisayar adını döndürür. Daha fazla bilgi için komutunu kullanın `Get-Help Get-EflowVmName -full` .

## <a name="get-eflowlogs"></a>Get-EflowLogs

**Get-EflowLogs** komutu, Windows dağıtımında Linux için IoT Edge günlükleri toplamak ve paket almak için kullanılır. Paketlenmiş günlükleri bir klasör biçiminde verir `.zip` . Daha fazla bilgi için komutunu kullanın `Get-Help Get-EflowLogs -full` .

| Parametre | Kabul edilen değerler | Yorumlar |
| --------- | --------------- | -------- |
| vmUser | En fazla 30 karakter uzunluğunda | Sanal makinede oturum açmak için Kullanıcı adı. |

## <a name="get-eflowvmtpmprovisioninginfo"></a>Get-EflowVmTpmProvisioningInfo

**Get-Eflowvmtpmprovisioningınfo** komutu, sanal makinenin vtpm sağlama bilgilerini toplayıp göstermek için kullanılır. Sanal makine vTPM olmadan oluşturulduysa, komut TPM sağlama bilgilerini bulamama işlemini geri döndürür. Daha fazla bilgi için komutunu kullanın `Get-Help Get-EflowVmTpmProvisioningInfo -full` .

| Parametre | Kabul edilen değerler | Yorumlar |
| --------- | --------------- | -------- |
| vmUser | En fazla 30 karakter uzunluğunda | Sanal makinede oturum açmak için Kullanıcı adı. |

## <a name="get-eflowvmaddr"></a>Get-EflowVmAddr

**Get-EflowVmAddr** komutu, sanal makinenin IP ve MAC adreslerini bulmak ve göstermek için kullanılır. Yalnızca ortak parametreleri alır. Daha fazla bilgi için komutunu kullanın `Get-Help Get-EflowVmAddr -full` .

## <a name="get-eflowvmsysteminformation"></a>Get-EflowVmSystemInformation

**Get-Eflowvmsystemınformation** komutu, sanal makineden bellek ve depolama kullanımı gibi sistem bilgilerini toplamak ve göstermek için kullanılır. Daha fazla bilgi için komutunu kullanın `Get-Help Get-EflowVmSystemInformation -full` .

| Parametre | Kabul edilen değerler | Yorumlar |
| --------- | --------------- | -------- |
| vmUser | En fazla 30 karakter uzunluğunda | Sanal makinede oturum açmak için Kullanıcı adı. |

## <a name="get-eflowvmedgeinformation"></a>Get-EflowVmEdgeInformation

**Get-Eflowvmedgeınformation** komutu, sanal makinenin çalıştığı IoT Edge sürümü gibi IoT Edge bilgilerini toplayıp göstermek için kullanılır. Daha fazla bilgi için komutunu kullanın `Get-Help Get-EflowVmEdgeInformation -full` .

| Parametre | Kabul edilen değerler | Yorumlar |
| --------- | --------------- | -------- |
| vmUser | En fazla 30 karakter uzunluğunda | Sanal makinede oturum açmak için Kullanıcı adı. |

## <a name="get-eflowvmedgemodulelist"></a>Get-EflowVmEdgeModuleList

**Get-EflowVmEdgeModuleList** komutu, sanal makinede çalışan IoT Edge modüllerinin listesini sorgulamak ve göstermek için kullanılır. Daha fazla bilgi için komutunu kullanın `Get-Help Get-EflowVmEdgeModuleList -full` .

| Parametre | Kabul edilen değerler | Yorumlar |
| --------- | --------------- | -------- |
| vmUser | En fazla 30 karakter uzunluğunda | Sanal makinede oturum açmak için Kullanıcı adı. |

## <a name="get-eflowvmedgestatus"></a>Get-EflowVmEdgeStatus

**Get-EflowVmEdgeStatus** komutu, sanal makinede IoT Edge çalışma zamanının durumunu sorgulamak ve göstermek için kullanılır. Daha fazla bilgi için komutunu kullanın `Get-Help Get-EflowVmEdgeStatus -full` .

| Parametre | Kabul edilen değerler | Yorumlar |
| --------- | --------------- | -------- |
| vmUser | En fazla 30 karakter uzunluğunda | Sanal makinede oturum açmak için Kullanıcı adı. |

## <a name="get-eflowvmusername"></a>Get-EflowVmUserName

**Get-EflowVmUserName** komutu, kayıt defterinden sanal makineyi oluşturmak için kullanılan sanal makine Kullanıcı adını sorgulamak ve göstermek için kullanılır. Yalnızca ortak parametreleri alır. Daha fazla bilgi için komutunu kullanın `Get-Help Get-EflowVmUserName -full` .

## <a name="get-eflowvmsshkey"></a>Get-EflowVmSshKey

**Get-EflowVmSshKey** komutu, sanal makine tarafından kullanılan SSH anahtarını sorgulamak ve göstermek için kullanılır. Yalnızca ortak parametreleri alır. Daha fazla bilgi için komutunu kullanın `Get-Help Get-EflowVmSshKey -full` .

## <a name="ssh-eflowvm"></a>Ssh-EflowVm

**SSH-EflowVm** komutu, sanal makinede SSH için kullanılır. Sanal makineye SSH 'ye izin verilen tek hesap, kendisini oluşturan kullanıcı olur. Daha fazla bilgi için komutunu kullanın `Get-Help Ssh-EflowVm -full` .

| Parametre | Kabul edilen değerler | Yorumlar |
| --------- | --------------- | -------- |
| vmUser | En fazla 30 karakter uzunluğunda | Sanal makinede oturum açmak için Kullanıcı adı. |

## <a name="next-steps"></a>Sonraki adımlar

Aşağıdaki makalede bu komutları kullanmayı öğrenin:

* [Windows 'da Linux için Azure IoT Edge yüklemesi](./how-to-install-iot-edge-windows-on-windows.md)

* PowerShell aracılığıyla kullanılabilen tüm komutlara yönelik [Windows PowerShell betik başvurusunda Linux için IoT Edge](reference-iot-edge-for-linux-on-windows-scripts.md#deploy-eflow) bakın.
