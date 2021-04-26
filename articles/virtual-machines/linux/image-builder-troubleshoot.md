---
title: Azure Image Builder hizmeti sorunlarını giderme
description: Azure VM Image Builder hizmetini kullanırken karşılaşılan yaygın sorunları ve hataları giderme
author: cynthn
ms.author: danis
ms.date: 10/02/2020
ms.topic: troubleshooting
ms.service: virtual-machines
ms.subservice: image-builder
ms.collection: linux
ms.openlocfilehash: f76c3e6c739ae4dd13355d350a01b878e4d4f360
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101666217"
---
# <a name="troubleshoot-azure-image-builder-service"></a>Azure Image Builder hizmeti sorunlarını giderme

Bu makale, Azure Image Builder hizmetini kullanırken karşılaşabileceğiniz yaygın sorunları gidermenize ve çözmenize yardımcı olur.

2 alanda AıB başarısızlığı oluşabilir:
- Görüntü şablonu gönderimi
- Görüntü derleme

## <a name="troubleshoot-image-template-submission-errors"></a>Görüntü şablonu gönderme hatalarında sorun giderme

Görüntü şablonu gönderme hataları yalnızca gönderim sırasında döndürülür. Görüntü şablonu gönderme hataları için bir hata günlüğü yok. Gönderim sırasında bir hata oluşursa, özellikle ve ' ı inceleyerek, şablonun durumunu denetleyerek hatayı döndürebilirsiniz `ProvisioningState` `ProvisioningErrorMessage` / `provisioningError` .

```azurecli
az image builder show --name $imageTemplateName  --resource-group $imageResourceGroup
```

```azurepowershell-interactive
Get-AzImageBuilderTemplate -ImageTemplateName  <imageTemplateName> -ResourceGroupName <imageTemplateResourceGroup> | Select-Object ProvisioningState, ProvisioningErrorMessage
```
> [!NOTE]
> PowerShell için [Azure Image Builder PowerShell modüllerini](../windows/image-builder-powershell.md#prerequisites)yüklemeniz gerekir.

Aşağıdaki bölümler, yaygın görüntü şablonu gönderme hataları için sorun çözümleme kılavuzunu içerir.

### <a name="updateupgrade-of-image-templates-is-currently-not-supported"></a>Görüntü şablonlarının güncelleştirilmesi/yükseltilmesi Şu anda desteklenmiyor

#### <a name="error"></a>Hata

```text
'Conflict'. Details: Update/Upgrade of image templates is currently not supported
```

#### <a name="cause"></a>Nedeni

Şablon zaten var.

#### <a name="solution"></a>Çözüm

Bir görüntü yapılandırma şablonu gönderirseniz ve gönderim başarısız olursa, hatalı bir şablon yapıtı hala var demektir. Başarısız şablonu silin.

### <a name="the-resource-operation-completed-with-terminal-provisioning-state-failed"></a>Kaynak işlemi, Terminal sağlama durumu ' başarısız ' ile tamamlandı

#### <a name="error"></a>Hata

```text
Microsoft.VirtualMachineImages/imageTemplates 'helloImageTemplateforSIG01' failed with message '{
  "status": "Failed",
  "error": {
    "code": "ResourceDeploymentFailure",
    "message": "The resource operation completed with terminal provisioning state 'Failed'.",
    "details": [
      {
        "code": "InternalOperationError",
        "message": "Internal error occurred."
```
#### <a name="cause"></a>Nedeni

Çoğu durumda, eksik izinler nedeniyle kaynak dağıtım hatası hatası oluşur.

#### <a name="solution"></a>Çözüm

Senaryonuza bağlı olarak, Azure Image Builder şunlar için izinler gerektirebilir:
- Kaynak görüntü veya paylaşılan görüntü Galerisi kaynak grubu
- Dağıtım görüntüsü veya paylaşılan görüntü Galerisi kaynağı
- Dosya Özelleştirici 'nin eriştiği depolama hesabı, kapsayıcı veya blob. 

İzinleri yapılandırma hakkında daha fazla bilgi için bkz. Azure [CLI kullanarak Azure Image Builder hizmet Izinlerini yapılandırma](image-builder-permissions-cli.md) veya [PowerShell kullanarak Azure Image Builder hizmeti izinleri yapılandırma](image-builder-permissions-powershell.md)

### <a name="error-getting-managed-image"></a>Yönetilen görüntü alınırken hata oluştu

#### <a name="error"></a>Hata

```text
Build (Managed Image) step failed: Error getting Managed Image '/subscriptions/.../providers/Microsoft.Compute/images/mymanagedmg1': Error getting managed image (...): compute.
ImagesClient#Get: Failure responding to request: StatusCode=403 -- Original Error: autorest/azure: Service returned an error.
Status=403 Code="AuthorizationFailed" Message="The client '......' with object id '......' does not have authorization to perform action 'Microsoft.Compute/images/read' over scope 
```
#### <a name="cause"></a>Nedeni

Eksik izinler.

#### <a name="solution"></a>Çözüm

Senaryonuza bağlı olarak, Azure Image Builder şunlar için izinler gerektirebilir:
* Kaynak görüntü veya paylaşılan görüntü Galerisi kaynak grubu
* Dağıtım görüntüsü veya paylaşılan görüntü Galerisi kaynağı
* Dosya Özelleştirici 'nin eriştiği depolama hesabı, kapsayıcı veya blob. 

İzinleri yapılandırma hakkında daha fazla bilgi için bkz. Azure [CLI kullanarak Azure Image Builder hizmet Izinlerini yapılandırma](image-builder-permissions-cli.md) veya [PowerShell kullanarak Azure Image Builder hizmeti izinleri yapılandırma](image-builder-permissions-powershell.md)

### <a name="build--step-failed-for-image-version"></a>Görüntü sürümü için derleme adımı başarısız oldu

#### <a name="error"></a>Hata
```text
Build (Shared Image Version) step failed for Image Version '/subscriptions/.../providers/Microsoft.Compute/galleries/.../images/... /versions/0.23768.4001': Error getting Image Version '/subscriptions/.../resourceGroups/<rgName>/providers/Microsoft.Compute/galleries/.../images/.../versions/0.23768.4001': Error getting image version '... :0.23768.4001': compute.GalleryImageVersionsClient#Get: Failure responding to request: StatusCode=404 -- Original Error: autorest/azure: Service returned an error. 
Status=404 Code="ResourceNotFound" Message="The Resource 'Microsoft.Compute/galleries/.../images/.../versions/0.23768.4001' under resource group '<rgName>' was not found."
```
#### <a name="cause"></a>Nedeni

Azure Image Builder kaynak görüntüyü bulamıyor.

#### <a name="solution"></a>Çözüm

Kaynak görüntünün doğru olduğundan ve Azure görüntü Oluşturucu hizmeti konumunda bulunduğundan emin olun.

### <a name="downloading-external-file-to-local-file"></a>Dış dosya yerel dosyaya indiriliyor

#### <a name="error"></a>Hata

```text
Downloading external file (<myFile>) to local file (xxxxx.0.customizer.fp) [attempt 1 of 10] failed: Error downloading '<myFile>' to 'xxxxx.0.customizer.fp'..
```

#### <a name="cause"></a>Nedeni

Dosya adı veya konumu yanlış veya konuma erişilemiyor.

#### <a name="solution"></a>Çözüm

Dosyanın erişilebilir olduğundan emin olun. Ad ve konumun doğru olduğundan emin olun.

## <a name="troubleshoot-build-failures"></a>Derleme hatalarıyla ilgili sorunları giderme

Görüntü derleme hataları için, öğesinden hatayı alabilir `lastrunstatus` ve ardından özelleştirme. log içindeki ayrıntıları gözden geçirebilirsiniz.


```azurecli
az image builder show --name $imageTemplateName  --resource-group $imageResourceGroup
```

```azurepowershell-interactive
Get-AzImageBuilderTemplate -ImageTemplateName  <imageTemplateName> -ResourceGroupName <imageTemplateResourceGroup> | Select-Object LastRunStatus, LastRunStatusMessage
```

### <a name="customization-log"></a>Özelleştirme günlüğü

Görüntü derlemesi çalışırken, Günlükler oluşturulup bir depolama hesabında depolanır. Azure Image Builder, bir görüntü şablonu yapıtı oluştururken geçici kaynak grubunda depolama hesabı oluşturur.

Depolama hesabı adı şu kalıbı kullanır: **IT_ \<ImageResourceGroupName\> _\<TemplateName\>_ \<GUID\>**

Örneğin, *IT_aibmdi_helloImageTemplateLinux01*.

**Depolama hesabı**  >  **Blobları**' nı seçerek kaynak grubunda, özelleştirme. log depolama hesabı ' nı görebilirsiniz  >  `packerlogs` .  Ardından **dizin > özelleştirme. log**' u seçin.


### <a name="understanding-the-customization-log"></a>Özelleştirme günlüğünü anlama

Günlük ayrıntılıdır. Görüntü dağıtımında paylaşılan görüntü Galerisi çoğaltması gibi sorunlar da dahil olmak üzere görüntü derlemesini ele alır. Bu hatalar, görüntü şablonu durumunun hata iletisinde ortaya çıkmış.

Özelleştirme. log aşağıdaki aşamaları içerir:

1. ARM şablonlarını kullanarak yapı sanal makinesini ve bağımlılıklarını IT_ hazırlama kaynak grubu aşamasına dağıtın. Bu aşama, Azure görüntü Oluşturucu kaynak sağlayıcısına birden çok gönderi içerir:
    ```text
    Azure request method="POST" request="https://management.azure.com/subscriptions/<subID>/resourceGroups/IT_aibImageRG200_window2019VnetTemplate01_dec33089-1cc3-cccc-cccc-ccccccc/providers/Microsoft.Storage/storageAccounts
    ..
    PACKER OUT ==> azure-arm: Deploying deployment template ...
    ..
    ```

2. Dağıtımlar aşamasının durumu. Bu aşamada her kaynak dağıtımının durumu bulunur:
    ```text
    PACKER ERR 2020/04/30 23:28:50 packer: 2020/04/30 23:28:50 Azure request method="GET" request="https://management.azure.com/subscriptions/<subID>/resourcegroups/IT_aibImageRG200_window2019VnetTemplate01_dec33089-1cc3-4505-ae28-6661e43fac48/providers/Microsoft.Resources/deployments/pkrdp51lc0339jg/operationStatuses/08586133176207523519?[REDACTED]" body=""
    ```

3. Derleme VM 'si aşamasına bağlanın.

    Windows, Azure görüntü Oluşturucu hizmeti, WinRM kullanarak bağlanır:
    ```text
    PACKER ERR 2020/04/30 23:30:50 packer: 2020/04/30 23:30:50 Waiting for WinRM, up to timeout: 10m0s
    ..
    PACKER OUT     azure-arm: WinRM connected.
    ```

    Linux ise, Azure görüntü Oluşturucu hizmeti SSH kullanarak bağlanır:
    ```text
    PACKER OUT ==> azure-arm: Waiting for SSH to become available...
    PACKER ERR 2019/12/10 17:20:51 packer: 2020/04/10 17:20:51 [INFO] Waiting for SSH, up to timeout: 20m0s
    PACKER OUT ==> azure-arm: Connected to SSH!
    ```

4. Özelleştirmeler aşamasını Çalıştır. Özelleştirmeler çalıştırıldığında, özelleştirme. log ' u inceleyerek bunları tanımlayabilirsiniz. Arama *(telemetri)*.
    ```text
    (telemetry) Starting provisioner windows-update
    (telemetry) ending windows-update
    (telemetry) Starting provisioner powershell
    (telemetry) ending powershell
    (telemetry) Starting provisioner file
    (telemetry) ending file
    (telemetry) Starting provisioner windows-restart
    (telemetry) ending windows-restart
    
    (telemetry) Finalizing. - This means the build hasfinished
    ```
5. Serbest sağlama aşaması. Azure görüntü Oluşturucu gizli bir Özelleştirici ekler. Bu geçersiz-sağlama adımı VM 'nin de sağlama için hazırlanmasından sorumludur. Windows Sysprep (c:\DeprovisioningScript.ps1 kullanarak) veya Linux waagent sağlamayı kaldırma (/TMP/deprovisioningscript.exe kullanarak) çalıştırır. 

    Örnek:
    ```text
    PACKER ERR 2020/03/04 23:05:04 [INFO] (telemetry) Starting provisioner powershell
    PACKER ERR 2020/03/04 23:05:04 packer: 2020/03/04 23:05:04 Found command: if( TEST-PATH c:\DeprovisioningScript.ps1 ){cat c:\DeprovisioningScript.ps1} else {echo "Deprovisioning script [c:\DeprovisioningScript.ps1] could not be found. Image build may fail or the VM created from the Image may not boot. Please make sure the deprovisioning script is not accidentally deleted by a Customizer in the Template."}
    ```

6. Aşamayı temizle. Oluşturma işlemi tamamlandıktan sonra, Azure görüntü Oluşturucu kaynakları silinir.
    ```text
    PACKER ERR 2020/02/04 02:04:23 packer: 2020/02/04 02:04:23 Azure request method="DELETE" request="https://management.azure.com/subscriptions/<subId>/resourceGroups/IT_aibDevOpsImg_t_vvvvvvv_yyyyyy-de5f-4f7c-92f2-xxxxxxxx/providers/Microsoft.Network/networkInterfaces/pkrnijamvpo08eo?[REDACTED]" body=""
    ```
## <a name="tips-for-troubleshooting-scriptinline-customization"></a>Betiği/satır içi özelleştirmeyi sorun gidermeye yönelik ipuçları
- Kodu görüntü oluşturucuya vermeden önce test etme
- Azure Ilkesinin ve güvenlik duvarlarının uzak kaynaklara bağlanmasına izin verildiğinden emin olun.
- Ya da gibi konsola giden Yorumları, bu, `Write-Host` `echo` Özelleştirme. log ' u aramanıza olanak sağlayacak.

## <a name="troubleshoot-common-build-errors"></a>Sık karşılaşılan derleme hatalarıyla ilgili sorunları giderme

### <a name="packer-build-command-failure"></a>Packer derleme komutu hatası

#### <a name="error"></a>Hata

```text
  "provisioningState": "Succeeded",
  "lastRunStatus": {
   "startTime": "2020-04-30T23:24:06.756985789Z",
   "endTime": "2020-04-30T23:39:14.268729811Z",
   "runState": "Failed",
   "message": "Failed while waiting for packerizer: Microservice has failed: Failed while processing request: Error when executing packerizer: Packer build command has failed: exit status 1. During the image build, a failure has occurred, please review the build log to identify which build/customization step failed. For more troubleshooting steps go to https://aka.ms/azvmimagebuilderts. Image Build log location: https://xxxxxxxxxx.blob.core.windows.net/packerlogs/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx/customization.log. OperationId: xxxxxx-5a8c-4379-xxxx-8d85493bc791. Use this operationId to search packer logs."
```

#### <a name="cause"></a>Nedeni

Özelleştirme hatası.

#### <a name="solution"></a>Çözüm

Özelleştiriciler başarısızlıklarını bulmak için günlüğü gözden geçirin. Arama *(telemetri)*. 

Örnek:
```text
(telemetry) Starting provisioner windows-update
(telemetry) ending windows-update
(telemetry) Starting provisioner powershell
(telemetry) ending powershell
(telemetry) Starting provisioner file
(telemetry) ending file
(telemetry) Starting provisioner windows-restart
(telemetry) ending windows-restart

(telemetry) Finalizing. - This means the build has finished
```

### <a name="timeout-exceeded"></a>Zaman aşımı aşıldı

#### <a name="error"></a>Hata

```text
Deployment failed. Correlation ID: xxxxx-xxxx-xxxx-xxxx-xxxxxxxxx. Failed in building/customizing image: Failed while waiting for packerizer: Timeout waiting for microservice to complete: 'context deadline exceeded'
```

#### <a name="cause"></a>Nedeni

Derleme, derleme zaman aşımını aştı. Bu hata ' LastRunStatus ' içinde görülür.

#### <a name="solution"></a>Çözüm

1. Özelleştirme. log öğesini gözden geçirin. Çalıştırılacak son Özelleştirici 'yi belirler. `(telemetry)`Günlüğün altından başlayarak arama yapın. 

2. Betik özelleştirmelerini denetleyin. Özelleştirmeler, seçenekler gibi komutların kullanıcı etkileşimini bastırmayabilir `quiet` . Örneğin, `apt-get install -y` komut dosyası yürütmesinin kullanıcı etkileşimini beklediği sonuçlara neden olur.

3. `File`20 MB 'tan büyük yapıtları indirmek için Özelleştirici kullanıyorsanız, bkz. geçici çözümler bölümü.

4. Betikte betiğin beklemesini neden olabilecek hataları ve bağımlılıkları gözden geçirin.

5. Özelleştirmelerin daha fazla zaman ihtiyacı olduğunu düşünüyorsanız [Buildtimeoutınminutes](image-builder-json.md)değerini arttırın. Varsayılan değer dört saattir.

6. Gigabayt dosya indirme gibi kaynak yoğunluklu eylemlere sahipseniz, temel alınan yapı VM boyutunu göz önünde bulundurun. Hizmet bir Standard_D1_v2 VM kullanır. VM, 1 vCPU ve 3,5 GB bellek içerir. 50 GB 'yi karşıdan yüklüyorsanız, bu durum büyük olasılıkla VM kaynaklarını tüketebilir ve Azure görüntü Oluşturucu hizmeti ile derleme VM arasında iletişim başarısızlıklarına neden olur. [VM_Size](image-builder-json.md#vmprofile)ayarlayarak, derlemeyi daha büyük BIR bellek VM 'si ile yeniden deneyin.

### <a name="long-file-download-time"></a>Uzun dosya indirme süresi

#### <a name="error"></a>Hata
```text
[086cf9c4-0457-4e8f-bfd4-908cfe3fe43c] PACKER OUT 
myBigFile.zip 826 B / 826000 B  1.00%
[086cf9c4-0457-4e8f-bfd4-908cfe3fe43c] PACKER OUT 
myBigFile.zip 1652 B / 826000 B  2.00%
[086cf9c4-0457-4e8f-bfd4-908cfe3fe43c] PACKER OUT 
..
hours later...
..
myBigFile.zip 826000 B / 826000 B  100.00%
[086cf9c4-0457-4e8f-bfd4-908cfe3fe43c] PACKER OUT 
```
#### <a name="cause"></a>Nedeni 

Dosya Özelleştirici büyük bir dosyayı indiriyor.

#### <a name="solution"></a>Çözüm

Dosya Özelleştirici yalnızca 20 MB 'tan küçük dosya indirmeleri için uygundur. Daha büyük dosya indirmeleri için bir betik veya satır içi komutu kullanın. Örneğin, Linux üzerinde `wget` veya kullanabilirsiniz `curl` . Windows 'ta kullanabilirsiniz `Invoke-WebRequest` .

### <a name="error-waiting-on-shared-image-gallery"></a>Paylaşılan görüntü galerisinde beklenirken hata oluştu

#### <a name="error"></a>Hata

```text
Deployment failed. Correlation ID: XXXXXX-XXXX-XXXXXX-XXXX-XXXXXX. Failed in distributing 1 images out of total 1: {[Error 0] [Distribute 0] Error publishing MDI to shared image gallery:/subscriptions/<subId>/resourceGroups/xxxxxx/providers/Microsoft.Compute/galleries/xxxxx/images/xxxxxx, Location:eastus. Error: Error returned from SIG client while publishing MDI to shared image gallery for dstImageLocation: eastus, dstSubscription: <subId>, dstResourceGroupName: XXXXXX, dstGalleryName: XXXXXX, dstGalleryImageName: XXXXXX. Error: Error waiting on shared image gallery future for resource group: XXXXXX, gallery name: XXXXXX, gallery image name: XXXXXX.Error: Future#WaitForCompletion: context has been cancelled: StatusCode=200 -- Original Error: context deadline exceeded}
```

#### <a name="cause"></a>Nedeni

Görüntü Oluşturucu görüntünün paylaşılan görüntü galerisine eklenmesini ve çoğaltılmasını beklerken zaman aşımına uğradı (SıG). Resim SıG öğesine eklendiğinde, resim yapısının başarılı olduğu kabul edilebilir. Ancak, genel işlem başarısız oldu, çünkü görüntü Oluşturucu çoğaltmayı tamamlayacak paylaşılan görüntü galerisini bekliyor. Yapı başarısız olsa bile çoğaltma devam eder. Dağıtım *Runoutput* öğesini denetleyerek görüntü sürümünün özelliklerini alabilirsiniz.

```bash
$runOutputName=<distributionRunOutput>
az resource show \
    --ids "/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.VirtualMachineImages/imageTemplates/$imageTemplateName/runOutputs/$runOutputName"  \
    --api-version=2019-05-01-preview
```

#### <a name="solution"></a>Çözüm

**Buildtimeoutınminutes** değerini artırın.
 
### <a name="low-windows-resource-information-events"></a>Düşük Windows kaynak bilgisi olayları

#### <a name="error"></a>Hata

```text
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT     azure-arm: Waiting for operation to complete (system performance: 1% cpu; 37% memory)...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT     azure-arm: Waiting for operation to complete (system performance: 51% cpu; 35% memory)...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT     azure-arm: Waiting for operation to complete (system performance: 21% cpu; 37% memory)...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT     azure-arm: Waiting for operation to complete (system performance: 21% cpu; 36% memory)...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT     azure-arm: Waiting for operation to complete (system performance: 90% cpu; 32% memory)...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT     azure-arm: Waiting for the Windows Modules Installer to exit...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer: 2020/04/30 23:38:58 [INFO] command 'PowerShell -ExecutionPolicy Bypass -OutputFormat Text -File C:/Windows/Temp/packer-windows-update-elevated.ps1' exited with code: 101
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT ==> azure-arm: Restarting the machine...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer: 2020/04/30 23:38:58 [INFO] RPC endpoint: Communicator ended with: 101
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] 1672 bytes written for 'stdout'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] 0 bytes written for 'stderr'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] RPC client: Communicator ended with: 101
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] RPC endpoint: Communicator ended with: 101
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT ==> azure-arm: Waiting for machine to become available...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer-provisioner-windows-update: 2020/04/30 23:38:58 [INFO] 1672 bytes written for 'stdout'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer-provisioner-windows-update: 2020/04/30 23:38:58 [INFO] 0 bytes written for 'stderr'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer-provisioner-windows-update: 2020/04/30 23:38:58 [INFO] RPC client: Communicator ended with: 101
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer: 2020/04/30 23:38:58 [INFO] starting remote command: shutdown.exe -f -r -t 0 -c "packer restart"
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer: 2020/04/30 23:38:58 [INFO] command 'shutdown.exe -f -r -t 0 -c "packer restart"' exited with code: 0
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer: 2020/04/30 23:38:58 [INFO] RPC endpoint: Communicator ended with: 0
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] 0 bytes written for 'stderr'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] 0 bytes written for 'stdout'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT ==> azure-arm: A system shutdown is in progress.(1115)
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] RPC client: Communicator ended with: 0
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] RPC endpoint: Communicator ended with: 0
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer-provisioner-windows-update: 2020/04/30 23:38:58 [INFO] 0 bytes written for 'stdout'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer-provisioner-windows-update: 2020/04/30 23:38:58 [INFO] 0 bytes written for 'stderr'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer-provisioner-windows-update: 2020/04/30 23:38:58 [INFO] RPC client: Communicator ended with: 0
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 packer: 2020/04/30 23:38:59 [INFO] starting remote command: shutdown.exe -f -r -t 60 -c "packer restart test"
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 packer: 2020/04/30 23:38:59 [INFO] command 'shutdown.exe -f -r -t 60 -c "packer restart test"' exited with code: 1115
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 packer: 2020/04/30 23:38:59 [INFO] RPC endpoint: Communicator ended with: 1115
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 [INFO] 0 bytes written for 'stdout'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 [INFO] 40 bytes written for 'stderr'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 [INFO] RPC client: Communicator ended with: 1115
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 [INFO] RPC endpoint: Communicator ended with: 1115
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 packer-provisioner-windows-update: 2020/04/30 23:38:59 [INFO] 40 bytes written for 'stderr'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 packer-provisioner-windows-update: 2020/04/30 23:38:59 [INFO] 0 bytes written for 'stdout'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 packer-provisioner-windows-update: 2020/04/30 23:38:59 [INFO] RPC client: Communicator ended with: 1115
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 packer-provisioner-windows-update: 2020/04/30 23:38:59 Retryable error: Machine not yet available (exit status 1115)
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT Build 'azure-arm' errored: unexpected EOF
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT 
```
#### <a name="cause"></a>Nedeni

Kaynak tükenmesi. Bu sorun genellikle varsayılan derleme VM boyutu D1_V2 kullanılarak çalışan Windows Update görülür.

#### <a name="solution"></a>Çözüm

Derleme VM 'sinin boyutunu artırın.

### <a name="builds-finished-but-no-artifacts-were-created"></a>Derlemeler tamamlandı, ancak hiçbir yapıt oluşturulmadı

#### <a name="error"></a>Hata

```text
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER OUT Build 'azure-arm' errored: Future#WaitForCompletion: context has been cancelled: StatusCode=200 -- Original Error: context deadline exceeded
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER ERR ==> Some builds didn't complete successfully and had errors:
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER OUT 
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER ERR 2020/04/30 22:29:23 machine readable: azure-arm,error []string{"Future#WaitForCompletion: context has been cancelled: StatusCode=200 -- Original Error: context deadline exceeded"}
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER OUT ==> Some builds didn't complete successfully and had errors:
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER ERR ==> Builds finished but no artifacts were created.
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER OUT --> azure-arm: Future#WaitForCompletion: context has been cancelled: StatusCode=200 -- Original Error: context deadline exceeded
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER ERR 2020/04/30 22:29:23 Cancelling builder after context cancellation context canceled
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER OUT 
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER ERR 2020/04/30 22:29:23 [INFO] (telemetry) Finalizing.
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER OUT ==> Builds finished but no artifacts were created.
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER ERR 2020/04/30 22:29:24 waiting for all plugin processes to complete...
Done exporting Packer logs to Azure for Packer prefix: [a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER OUT
```
#### <a name="cause"></a>Nedeni

Gerekli Azure kaynaklarının oluşturulması beklenirken zaman aşımı oldu.

#### <a name="solution"></a>Çözüm

Yeniden denemek için derlemeyi yeniden çalıştırın.

### <a name="resource-not-found"></a>Kaynak bulunamadı

#### <a name="error"></a>Hata

```text
  "provisioningState": "Succeeded",
  "lastRunStatus": {
   "startTime": "2020-05-01T00:13:52.599326198Z",
   "endTime": "2020-05-01T00:15:13.62366898Z",
   "runState": "Failed",
   "message": "network.InterfacesClient#UpdateTags: Failure responding to request: StatusCode=404 -- Original Error: autorest/azure: Service returned an error. Status=404 Code=\"ResourceNotFound\" Message=\"The Resource 'Microsoft.Network/networkInterfaces/aibpls7lz2e.nic.4609d697-be0a-4cb0-86af-49b6fe877fe1' under resource group 'IT_aibImageRG200_window2019VnetTemplate01_9988723b-af56-413a-9006-84130af0e9df' was not found.\""
  },
```
#### <a name="cause"></a>Nedeni

Eksik izinler.

#### <a name="solution"></a>Çözüm

Azure Image Builder 'ın ihtiyaç duyduğu tüm izinlere sahip olduğunu denetleyin. 

İzinleri yapılandırma hakkında daha fazla bilgi için bkz. Azure [CLI kullanarak Azure Image Builder hizmet Izinlerini yapılandırma](image-builder-permissions-cli.md) veya [PowerShell kullanarak Azure Image Builder hizmeti izinleri yapılandırma](image-builder-permissions-powershell.md)

### <a name="sysprep-timing"></a>Sysprep zamanlaması

#### <a name="error"></a>Hata

```text
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: Write-Output '>>> Waiting for GA Service (RdAgent) to start ...'
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: while ((Get-Service RdAgent) -and ((Get-Service RdAgent).Status -ne 'Running')) { Start-Sleep -s 5 }
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: Write-Output '>>> Waiting for GA Service (WindowsAzureTelemetryService) to start ...'
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: while ((Get-Service WindowsAzureTelemetryService) -and ((Get-Service WindowsAzureTelemetryService).Status -ne 'Running')) { Start-Sleep -s 5 }
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: Write-Output '>>> Waiting for GA Service (WindowsAzureGuestAgent) to start ...'
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: while ((Get-Service WindowsAzureGuestAgent) -and ((Get-Service WindowsAzureGuestAgent).Status -ne 'Running')) { Start-Sleep -s 5 }
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: Write-Output '>>> Sysprepping VM ...'
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: if( Test-Path $Env:SystemRoot\system32\Sysprep\unattend.xml ) {
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm:   Remove-Item $Env:SystemRoot\system32\Sysprep\unattend.xml -Force
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: }
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: & $Env:SystemRoot\System32\Sysprep\Sysprep.exe /oobe /generalize /quiet /quit
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: while($true) {
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm:   $imageState = (Get-ItemProperty HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Setup\State).ImageState
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm:   Write-Output $imageState
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm:   if ($imageState -eq 'IMAGE_STATE_GENERALIZE_RESEAL_TO_OOBE') { break }
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm:   Start-Sleep -s 5
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: }
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: Write-Output '>>> Sysprep complete ...'
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: >>> Waiting for GA Service (RdAgent) to start ...
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: >>> Waiting for GA Service (WindowsAzureTelemetryService) to start ...
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: >>> Waiting for GA Service (WindowsAzureGuestAgent) to start ...
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: >>> Sysprepping VM ...
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_COMPLETE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm: Get-Service : Cannot find any service with service name 'WindowsAzureGuestAgent'.
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm: At C:\DeprovisioningScript.ps1:6 char:9
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm: + while ((Get-Service WindowsAzureGuestAgent) -and ((Get-Service Window ...
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm: +         ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm:     + CategoryInfo          : ObjectNotFound: (WindowsAzureGuestAgent:String) [Get-Service], ServiceCommandException
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm:     + FullyQualifiedErrorId : NoServiceFoundForGivenName,Microsoft.PowerShell.Commands.GetServiceCommand
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm:
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
...
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER ERR 2020/05/05 22:26:17 Cancelling builder after context cancellation context canceled
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT Cancelling build after receiving terminated
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER ERR 2020/05/05 22:26:17 packer: 2020/05/05 22:26:17 Cancelling provisioning due to context cancellation: context canceled
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm: 
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER ERR 2020/05/05 22:26:17 packer: 2020/05/05 22:26:17 Cancelling hook after context cancellation context canceled
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm: The resource group was not created by Packer, deleting individual resources ...
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER ERR ==> azure-arm: The resource group was not created by Packer, deleting individual resources ...
```
#### <a name="cause"></a>Nedeni

Bunun nedeni D1_V2 VM boyutu nedeniyle bir zamanlama sorunu olabilir. Özelleştirmeler sınırlı ve üç saniyeden kısa bir süre içinde yürütülübilirse, Sysprep komutları Azure görüntü Oluşturucu tarafından sağlanması için çalıştırılır. Azure Image Builder 'ın de sağlamasını yaparken, Sysprep komutu, zamanlama sorununa neden olan, tam olarak yüklenmemiş olabilecek *WindowsAzureGuestAgent*'yi denetler. 

#### <a name="solution"></a>Çözüm

VM boyutunu artırın. Ya da zamanlama sorununa engel olmak için 60 saniyelik bir PowerShell Sleep özelleştirmesi ekleyebilirsiniz.

### <a name="cancelling-builder-after-context-cancellation-context-canceled"></a>Bağlam iptali bağlamı iptal edildikten sonra Oluşturucu iptal ediliyor

#### <a name="error"></a>Hata
```text
PACKER ERR 2020/03/26 22:11:23 Cancelling builder after context cancellation context canceled
PACKER OUT Cancelling build after receiving terminated
PACKER ERR 2020/03/26 22:11:23 packer-builder-azure-arm plugin: Cancelling hook after context cancellation context canceled
..
PACKER ERR 2020/03/26 22:11:23 packer-builder-azure-arm plugin: Cancelling provisioning due to context cancellation: context canceled
PACKER ERR 2020/03/26 22:11:25 packer-builder-azure-arm plugin: [ERROR] Remote command exited without exit status or exit signal.
PACKER ERR 2020/03/26 22:11:25 packer-builder-azure-arm plugin: [INFO] RPC endpoint: Communicator ended with: 2300218
PACKER ERR 2020/03/26 22:11:25 [INFO] 148974 bytes written for 'stdout'
PACKER ERR 2020/03/26 22:11:25 [INFO] 0 bytes written for 'stderr'
PACKER ERR 2020/03/26 22:11:25 [INFO] RPC client: Communicator ended with: 2300218
PACKER ERR 2020/03/26 22:11:25 [INFO] RPC endpoint: Communicator ended with: 2300218
```
#### <a name="cause"></a>Nedeni
Görüntü Oluşturucu hizmeti, derleme VM 'sine bağlanmak için 22 (Linux) veya 5986 (Windows) bağlantı noktasını kullanır. bu durum, hizmetin bir görüntü derlemesi sırasında yapı VM bağlantısı kesildiğinde oluşur. Bağlantı kesilmesi nedenleri farklılık gösterebilir, ancak betikte güvenlik duvarlarını etkinleştirmek veya yapılandırmak yukarıdaki bağlantı noktalarını engelleyebilir.

#### <a name="solution"></a>Çözüm
Güvenlik Duvarı değişiklikleri/etkinleştirme için betiklerinizi gözden geçirin veya SSH ya da WinRM üzerinde değişiklikler yapın ve tüm değişikliklerin yukarıdaki bağlantı noktalarında hizmet ve yapı VM 'si arasında sabit bağlantıya izin verildiğinden emin olun. Görüntü Oluşturucu ağı hakkında daha fazla bilgi için lütfen [gereksinimleri](./image-builder-networking.md)gözden geçirin.

## <a name="devops-task"></a>DevOps görevi 

### <a name="troubleshooting-the-task"></a>Görev sorunlarını giderme
Görev yalnızca özelleştirme sırasında bir hata oluşursa başarısız olur, bu durumda görev hatayı bildirir ve hazırlama kaynak grubunu Günlükler ile bırakır, böylece sorunu belirleyebilirsiniz. 

Günlüğü bulmak için, şablon adını bilmeniz, işlem hattının > başarısız olması gerekir > AıB DevOps görevinde detaya gitmek için, günlüğü ve bir şablon adını görürsünüz:
```text
start reading task parameters...
found build at:  /home/vsts/work/r1/a/_ImageBuilding/webapp
end reading parameters
getting storage account details for aibstordot1556933914
created archive /home/vsts/work/_temp/temp_web_package_21475337782320203.zip
Source for image:  { type: 'SharedImageVersion',
  imageVersionId: '/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<imageDefName>/versions/<imgVersionNumber>' }
template name:  t_1556938436xxx
``` 

Portala gidin, kaynak grubunda Şablon adını arayın ve ardından IT_ * ile kaynak grubunu arayın.
Blob 'ları > kapsayıcılar > günlüklere > depolama hesabına gidin.

### <a name="troubleshooting-successful-builds"></a>Başarılı derlemelerin sorunlarını giderme
Başarılı derlemeleri araştırmanız gereken bazı durumlar olabilir ve günlüğü gözden geçirmek isteyebilirsiniz. Belirtildiği gibi, görüntü derlemesi başarılı olursa, temizleme işleminin bir parçası olarak günlükleri içeren hazırlama kaynak grubu silinir. Ancak yapabilecekleriniz, satır içi komuttan sonra bir uyku ortaya çıkarabilir ve sonra yapı duraklatıldığından günlükleri alır. Bunu yapmak için şu adımları izleyin:
 
1. Satır içi komutu güncelleştirin ve şunu ekleyin: Write-Host/echo "Sleep"-Bu, günlükte arama yapmanıza olanak tanır
2. En az 10dakika için bir uyku ekleyin, [Start-Sleep](/powershell/module/microsoft.powershell.utility/start-sleep)veya `Sleep` Linux komutunu kullanabilirsiniz.
3. Günlük konumunu tanımlamak için yukarıdaki yöntemi kullanın ve ardından uykuya gelene kadar günlüğü indirmeyi/denetlemeyi sürdürün.


### <a name="operation-was-canceled"></a>İşlem iptal edildi

#### <a name="error"></a>Hata

```text
2020-05-05T18:28:24.9280196Z ##[section]Starting: Azure VM Image Builder Task
2020-05-05T18:28:24.9609966Z ==============================================================================
2020-05-05T18:28:24.9610739Z Task         : Azure VM Image Builder Test(Preview)
2020-05-05T18:28:24.9611277Z Description  : Build images using Azure Image Builder resource provider.
2020-05-05T18:28:24.9611608Z Version      : 1.0.18
2020-05-05T18:28:24.9612003Z Author       : Microsoft Corporation
2020-05-05T18:28:24.9612718Z Help         : For documentation, and end to end example, please visit: http://aka.ms/azvmimagebuilderdevops
2020-05-05T18:28:24.9613390Z ==============================================================================
2020-05-05T18:28:26.0651512Z start reading task parameters...
2020-05-05T18:28:26.0673377Z found build at:  d:\a\r1\a\_AppsAndImageBuilder\webApp
2020-05-05T18:28:26.0708785Z end reading parameters
2020-05-05T18:28:26.0745447Z getting storage account details for aibstagstor1565047758
2020-05-05T18:28:29.8812270Z created archive d:\a\_temp\temp_web_package_09737279437949953.zip
2020-05-05T18:28:33.1568013Z Source for image:  { type: 'PlatformImage',
2020-05-05T18:28:33.1584131Z   publisher: 'MicrosoftWindowsServer',
2020-05-05T18:28:33.1585965Z   offer: 'WindowsServer',
2020-05-05T18:28:33.1592768Z   sku: '2016-Datacenter',
2020-05-05T18:28:33.1594191Z   version: '14393.3630.2004101604' }
2020-05-05T18:28:33.1595387Z template name:  t_1588703313152
2020-05-05T18:28:33.1597453Z starting put template...
2020-05-05T18:28:52.9278603Z put template:  Succeeded
2020-05-05T18:28:52.9281282Z starting run template...
2020-05-05T19:33:14.3923479Z ##[error]The operation was canceled.
2020-05-05T19:33:14.3939721Z ##[section]Finishing: Azure VM Image Builder Task
```
#### <a name="cause"></a>Nedeni

Derleme bir kullanıcı tarafından iptal edilmediğinde, Azure DevOps Kullanıcı Aracısı tarafından iptal edildi. Büyük olasılıkla, Azure DevOps özellikleri nedeniyle 1 saatlik zaman aşımı oluştu. Özel bir proje ve aracı kullanıyorsanız, 60 dakikalık derleme zamanı alırsınız. Yapı zaman aşımını aşarsa, DevOps çalışan görevi iptal eder.

Azure DevOps özellikleri ve sınırlamaları hakkında daha fazla bilgi için bkz. [Microsoft tarafından barındırılan aracılar](/azure/devops/pipelines/agents/hosted#capabilities-and-limitations)
 
#### <a name="solution"></a>Çözüm

Kendi DevOps aracılarınızı barındırabilir veya derlemenizi süresini azaltmak için arama yapabilirsiniz. Örneğin, paylaşılan görüntü galerisine dağıtıyorsanız, tek bir bölgeye çoğaltılır. Zaman uyumsuz olarak çoğaltmak istiyorsanız. 

### <a name="slow-windows-logon-please-wait-for-the-windows-modules-installer"></a>Yavaş Windows oturum açma: ' lütfen Windows Modül Yükleyicisi için bekleyin '

#### <a name="error"></a>Hata
Image Builder ile bir Windows 10 görüntüsü oluşturduktan sonra, görüntüden bir VM oluşturun ve ardından ilk oturum açmada dakika beklemeniz gerekir ve iletiyi içeren mavi bir ekran görürsünüz:
```text
Please wait for the Windows Modules Installer
```

#### <a name="solution"></a>Çözüm
Görüntü derlemesinde, son özelleştirme olarak bir Windows yeniden başlatma Özelleştirici ekleyerek ve tüm yazılım yüklemesinin tamamlanmasıyla ilgili bekleyen yeniden başlatmalar olmadığından emin olun. Son olarak, AıB 'nin kullandığı varsayılan Sysprep 'e [/Mode: VM](/windows-hardware/manufacture/desktop/sysprep-command-line-options) seçeneğini ekleyin, aşağıya bakın, ' AIB görüntülerden oluşturulan VM 'ler başarıyla oluşturulmaz ' > ' komutları geçersiz kılar '  

 
## <a name="vms-created-from-aib-images-do-not-create-successfully"></a>AıB görüntülerinden oluşturulan VM 'Ler başarıyla oluşturmaz

Varsayılan olarak, Azure görüntü Oluşturucu görüntüyü *genelleştirmek* için her görüntü özelleştirme aşamasının sonunda, *serbest sağlama* kodunu çalıştırır. Genelleştirmek, görüntünün birden çok VM oluşturmak için yeniden kullanmak üzere ayarlandığı ve ana bilgisayar adı, Kullanıcı adı vb. gibi VM ayarlarını geçirebileceğiniz bir işlemdir. Windows için Azure Image Builder, *Sysprep*'i yürütür ve Linux Azure Image Builder çalıştırmaları için çalışır `waagent -deprovision` . 

Azure Image Builder, Windows için genel bir Sysprep komutu kullanır. Ancak, bu, başarılı olan her Windows genelleştirmesi için uygun olmayabilir. Azure Image Builder, Sysprep komutunu özelleştirmenize olanak sağlar. Azure görüntü Oluşturucu bir görüntü Otomasyonu aracıdır. Sysprep komutunu başarıyla çalıştırmaktan sorumludur. Ancak, görüntünüzü yeniden kullanılabilir hale getirmek için farklı Sysprep komutlarına ihtiyacınız olabilir. Linux için Azure görüntü Oluşturucu genel bir komut kullanır `waagent -deprovision+user` . Daha fazla bilgi için bkz. [Linux Aracısı belgeleri Microsoft Azure](https://github.com/Azure/WALinuxAgent#command-line-options).

Var olan bir özelleştirmeyi geçiriyorsanız ve farklı Sysprep/waagent komutları kullanıyorsanız, görüntü Oluşturucu genel komutlarını deneyebilirsiniz. VM oluşturma başarısız olursa, önceki Sysprep/waagent komutlarınızı kullanın.

> [!NOTE]
> AıB, başarılı bir şekilde bir Windows özel görüntüsü oluşturursa ve bundan sonra bir VM oluşturup, sanal makinenin başarıyla oluşturulmamasıyla ilgili daha fazla bilgi edinin (örneğin, VM oluşturma komutu tamamlanmaz/zaman aşımları), Windows Server Sysprep belgelerini gözden geçirmeniz gerekir. Ya da, doğru Sysprep komutunda sorun giderebilen ve önererişebilecek Windows Server Sysprep müşteri hizmetleri destek ekibi ile bir destek isteği oluşturabilirsiniz.

### <a name="command-locations-and-filenames"></a>Komut konumları ve dosya adları

Windows:

```
c:\DeprovisioningScript.ps1
```

Linux:
```bash
/tmp/DeprovisioningScript.sh
```

### <a name="sysprep-command-windows"></a>Sysprep komutu: Windows

```PowerShell
Write-Output '>>> Waiting for GA Service (RdAgent) to start ...'
while ((Get-Service RdAgent).Status -ne 'Running') { Start-Sleep -s 5 }
Write-Output '>>> Waiting for GA Service (WindowsAzureTelemetryService) to start ...'
while ((Get-Service WindowsAzureTelemetryService) -and ((Get-Service WindowsAzureTelemetryService).Status -ne 'Running')) { Start-Sleep -s 5 }
Write-Output '>>> Waiting for GA Service (WindowsAzureGuestAgent) to start ...'
while ((Get-Service WindowsAzureGuestAgent).Status -ne 'Running') { Start-Sleep -s 5 }
Write-Output '>>> Sysprepping VM ...'
if( Test-Path $Env:SystemRoot\system32\Sysprep\unattend.xml ) {
  Remove-Item $Env:SystemRoot\system32\Sysprep\unattend.xml -Force
}
& $Env:SystemRoot\System32\Sysprep\Sysprep.exe /oobe /generalize /quiet /quit
while($true) {
  $imageState = (Get-ItemProperty HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Setup\State).ImageState
  Write-Output $imageState
  if ($imageState -eq 'IMAGE_STATE_GENERALIZE_RESEAL_TO_OOBE') { break }
  Start-Sleep -s 5
}
Write-Output '>>> Sysprep complete ...'
```

### <a name="deprovision-command-linux"></a>Sağlamayı kaldırma komutu: Linux

```bash
/usr/sbin/waagent -force -deprovision+user && export HISTSIZE=0 && sync
```

### <a name="overriding-the-commands"></a>Komutları geçersiz kılma

Komutları geçersiz kılmak için PowerShell veya Shell betik hazırlayıcılar ' ni kullanarak tam dosya adına sahip komut dosyalarını oluşturun ve bunları daha önce listelenen dizinlere yerleştirin. Azure Image Builder, bu komutları okur ve çıktı *Özelleştirme. log* dosyasına yazılır.

## <a name="getting-support"></a>Destek Alma
Kılavuza başvurdıysanız ve sorun giderme sorununuzu yaşamaya devam ediyorsanız bir destek talebi açabilirsiniz. Bunu yaparken, lütfen doğru ürün ve destek konusunu seçin; bunu yaptığınızda Azure VM görüntü Oluşturucu destek ekibi devreye alınacaktır.

Büyük/küçük harf ürünü seçme:
```bash
Product Family: Azure
Product: Virtual Machine Running (Window\Linux)
Support Topic: Azure Features
Support Subtopic: Azure Image Builder
```

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi için bkz. [Azure Image Builder 'a genel bakış](../image-builder-overview.md).
