---
title: Azure görüntü Oluşturucu hizmeti DevOps görevi
description: Uygulamanızı ve işletim sistemini yükleyip yapılandırabilmeniz için bir VM görüntüsüne derleme yapıtları eklemek üzere Azure DevOps görevi.
author: danielsollondon
ms.author: danis
ms.date: 01/27/2021
ms.topic: article
ms.service: virtual-machines
ms.subservice: image-builder
ms.collection: linux
ms.openlocfilehash: d02a5c6bc194009d459647721dab16be0dcade84
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101670484"
---
# <a name="azure-image-builder-service-devops-task"></a>Azure görüntü Oluşturucu hizmeti DevOps görevi

Bu makalede, uygulamanızı ve işletim sistemini yükleyip yapılandırmak için bir VM görüntüsüne derleme yapıtları eklemek üzere bir Azure DevOps görevinin nasıl kullanılacağı gösterilmektedir.

## <a name="devops-task-versions"></a>DevOps görev sürümleri
İki Azure VM görüntü Oluşturucu (AıB) DevOps görevi vardır:

* [' Stable ' AıB görevi](https://marketplace.visualstudio.com/items?itemName=AzureImageBuilder.devOps-task-for-azure-image-builder), bu test edilen en son kararlı derleme ve telemetri hiçbir sorun göstermez. 


* [' Kararsız ' bir görev](https://marketplace.visualstudio.com/items?itemName=AzureImageBuilder.devOps-task-for-azure-image-builder-canary), bu, en son güncelleştirmeleri ve özellikleri koymamızı sağlar, bu sayede müşterilerin bunları ' kararlı ' göreve yükseltmeden önce test etmesine izin verir. Bildirilen bir sorun yoksa ve telemetrimiz bir sorun olmadığını gösteriyorsa, yaklaşık 1 hafta sonra görev kodunu ' Stable ' olarak yükseltecektir. 

## <a name="prerequisites"></a>Önkoşullar

* Visual Studio Market ' [den kararlı DevOps görevini](https://marketplace.visualstudio.com/items?itemName=AzureImageBuilder.devOps-task-for-azure-image-builder)yükler.
* Bir VSTS DevOps hesabınız ve derleme işlem hattının oluşturulması gerekir
* İşlem hatları tarafından kullanılan abonelikte görüntü Oluşturucu özelliği gereksinimlerini kaydedin ve etkinleştirin:
    * [Az PowerShell](../windows/image-builder-powershell.md#register-features)
    * [Az CLı](../windows/image-builder.md#register-the-features)
    
* Kaynak görüntü kaynak grubunda standart bir Azure depolama hesabı oluşturun, diğer kaynak grubu/depolama hesaplarını kullanabilirsiniz. Depolama hesabı, yapı yapıtlarını DevOps görevinin görüntüsüne aktarma kullanılır.

    ```powerShell
    # Az PowerShell
    $timeInt=$(get-date -UFormat "%s")
    $storageAccName="aibstorage"+$timeInt
    $location=westus
    # create storage account and blob in resource group
    New-AzStorageAccount -ResourceGroupName $strResourceGroup -Name $storageAccName -Location $location -SkuName Standard_LRS
    ```

    ```bash
    # Az CLI
    location=westus
    scriptStorageAcc=aibstordot$(date +'%s')
    # create storage account and blob in resource group
    az storage account create -n $scriptStorageAcc -g $strResourceGroup -l $location --sku Standard_LRS
    ```

## <a name="add-task-to-release-pipeline"></a>Yayın ardışık düzenine görev ekleme

**Yayın ardışık düzen**  >  **düzenlemesini** seçin

Kullanıcı aracısında, Ekle ' *+* yi seçerek **Görüntü Oluşturucu**' yı arayın. **Add (Ekle)** seçeneğini belirleyin.

Aşağıdaki görev özelliklerini ayarlayın:

### <a name="azure-subscription"></a>Azure Aboneliği

Görüntü oluşturucunun çalıştırmasını istediğiniz aboneliğin açılan menüsünde seçim yapın. Kaynak görüntülerinizin bulunduğu ve görüntülerin dağıtılacağı aboneliği kullanın. Görüntü Oluşturucu katılımcısı için abonelik veya kaynak grubuna erişim yetkisi vermeniz gerekir.

### <a name="resource-group"></a>Kaynak Grubu

Geçici görüntü şablonu yapısının depolanacağı kaynak grubunu kullanın. Şablon yapıtı oluştururken, ek bir geçici görüntü Oluşturucu kaynak grubu `IT_<DestinationResourceGroup>_<TemplateName>_guid` oluşturulur. Geçici kaynak grubu, komut dosyaları gibi görüntü meta verilerini depolar. Görevin sonunda, görüntü şablonu yapıtı ve geçici görüntü Oluşturucu kaynak grubu silinir.
 
### <a name="location"></a>Konum

Konum, Image Builder 'ın çalışacağı bölgedir. Yalnızca bir dizi [bölge](../image-builder-overview.md#regions) desteklenir. Kaynak görüntülerin bu konumda bulunması gerekir. Örneğin, paylaşılan görüntü Galerisi kullanıyorsanız, bu bölgede bir çoğaltma olması gerekir.

### <a name="managed-identity-required"></a>Yönetilen kimlik (gerekli)
Image Builder, kaynak özel görüntüleri okumak, Azure depolama 'ya bağlanmak ve özel görüntüler oluşturmak için kullandığı yönetilen bir kimlik gerektirir. Daha ayrıntılı bilgi için [buraya](../image-builder-overview.md#permissions) bakın.

### <a name="vnet-support"></a>VNET desteği

Şu anda DevOps görevi var olan bir alt ağın belirtilmesini desteklemiyor, bu yol haritasında yer alır, ancak mevcut bir VNET 'i kullanmak istiyorsanız, içinde yer alan bir görüntü Oluşturucu şablonuyla bir ARM şablonu kullanabilirsiniz, bunun nasıl elde edildiğini öğrenmek için lütfen Windows görüntü Oluşturucu şablon örneklerine bakın veya alternatif olarak [az AıB PowerShell](../windows/image-builder-powershell.md)kullanın.

### <a name="source"></a>Kaynak

Kaynak görüntülerin desteklenen görüntü Oluşturucu OSs 'i olması gerekir. Görüntü oluşturucunun üzerinde çalıştığı aynı bölgede bulunan özel görüntüleri seçebilirsiniz:
* Yönetilen görüntü-RESOURCEID 'yi geçirmeniz gerekir, örneğin:
    ```json
    /subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/images/<imageName>
    ```
* Azure Paylaşılan görüntü Galerisi-görüntü sürümünün RESOURCEID 'sini geçirmeniz gerekir, örneğin:
    ```json
    /subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup/providers/Microsoft.Compute/galleries/$sigName/images/$imageDefName/versions/<versionNumber>
    ```

    En son paylaşılan görüntü Galerisi sürümünü almanız gerekiyorsa, en son sürümü alacak ve bir DevOps değişkeni ayarlayabilmesi için AZ bir PowerShell veya AZ CLı görevi kullanabilirsiniz. Bu değişkeni AZ VM Image Builder DevOps görevinde kullanın. Daha fazla bilgi için [örneklere](https://github.com/danielsollondon/azvmimagebuilder/tree/master/solutions/8_Getting_Latest_SIG_Version_ResID#getting-the-latest-image-version-resourceid-from-shared-image-gallery)bakın.

* 'Nde Temel görüntü popüler görüntülerin açılan bir listesi varsa, bunlar her zaman desteklenen işletim sisteminin ' en son ' sürümünü kullanır. 

    Temel görüntü listede yoksa, kullanarak tam görüntüyü belirtebilirsiniz `Publisher:Offer:Sku` .

    Temel görüntü sürümü (isteğe bağlı)-kullanmak istediğiniz görüntünün sürümünü sağlayabilirsiniz, varsayılan olarak `latest` .

### <a name="customize"></a>Özelleştirme

#### <a name="provisioner"></a>Sağlayıcısı oluştur

Başlangıçta, iki özelleştiriciler desteklenir- **Shell** ve **PowerShell**. Yalnızca satır içi desteklenir. Betikleri indirmek isterseniz, bunu yapmak için satır içi komutlar geçirebilirsiniz.

İşletim sistemi için PowerShell veya Shell ' i seçin.

#### <a name="windows-update-task"></a>Windows Update görev

Yalnızca Windows için görev, özelleştirmelerin sonunda Windows Update çalıştırır. Gerekli yeniden başlatmaları işler.

Aşağıdaki Windows Update yapılandırması yürütülür:
```json
    "type": "WindowsUpdate",
    "searchCriteria": "IsInstalled=0",
    "filters": [
        "exclude:$_.Title -like '*Preview*'",
        "include:$true"
```
Önizleme olmayan önemli ve önerilen Windows güncelleştirmelerini yüklüyor.

#### <a name="handling-reboots"></a>Yeniden başlatmalar işleniyor
Şu anda DevOps görevinin Windows derlemelerini yeniden başlatma desteği yoktur, PowerShell koduyla yeniden başlatmaya çalışırsanız derleme başarısız olur. Ancak, Linux derlemelerini yeniden başlatmak için kodu kullanabilirsiniz.

#### <a name="build-path"></a>Derleme yolu

Görev, görüntüye DevOps derleme yayın yapıtları ekleyebilecek şekilde tasarlanmıştır. Bu işi yapmak için bir yapı işlem hattı ayarlamanız gerekir. Yayın işlem hattının kurulumunda, derleme yapıtlarının depoyu eklemeniz gerekir.

:::image type="content" source="./media/image-builder-devops-task/add-artifact.png" alt-text="Yayın ardışık düzeninde yapıt Ekle ' ye seçme.":::

Görüntüye yerleştirilmesini istediğiniz yapı klasörünü seçmek için **Yapı yolu** düğmesini seçin. Görüntü Oluşturucu görevi, içindeki tüm dosyaları ve dizinleri kopyalar. Görüntü oluşturulduğunda, görüntü Oluşturucu işletim sistemine bağlı olarak dosyaları ve dizinleri farklı yollarla dağıtır.

> [!IMPORTANT]
> Bir depo yapıtı eklerken, dizinin bir alt çizgi *_* öneki olduğunu fark edebilirsiniz. Alt çizgi, satır içi komutlarla ilgili sorunlara neden olabilir. Komutlarda uygun teklifleri kullanın.
> 

Aşağıdaki örnek bunun nasıl çalıştığını açıklar:

:::image type="content" source="./media/image-builder-devops-task/build-artifacts.png" alt-text="Hiyerarşiyi gösteren bir dizin yapısı.":::


* Windows-dosyaları içinde bulunur `C:\` . Dizinini içeren adlı bir dizin `buildArtifacts` oluşturulur `webapp` .

* Linux dosyaları içinde bulunur  `/tmp` . `webapp`Tüm dosyaları ve dizinleri içeren dizin oluşturulur. Dosyaları bu dizinden taşımanız gerekir. Aksi takdirde, bunlar geçici dizin olduğundan silinir.

#### <a name="inline-customization-script"></a>Satır içi özelleştirme betiği

* Windows-virgülle ayırarak PowerShell satır içi komutlarını girebilirsiniz. Yapı dizininizde bir betik çalıştırmak istiyorsanız şunu kullanabilirsiniz:

    ```PowerShell
    & 'c:\buildArtifacts\webapp\webconfig.ps1'
    ```

   Birden çok betikte başvurabilir veya daha fazla komut ekleyebilirsiniz, örneğin:

    ```PowerShell
    & 'c:\buildArtifacts\webapp\webconfig.ps1'
    & 'c:\buildArtifacts\webapp\installAgent.ps1'
    ```
* Linux-Linux sistemlerinde derleme yapıtları `/tmp` dizine konur. Ancak, birçok Linux OSs 'de, bir yeniden başlatma işlemi için/tmp dizin içeriği silinir. Yapıtların görüntüde mevcut olmasını istiyorsanız, başka bir dizin oluşturmanız ve bunları üzerine kopyalamanız gerekir.  Örnek:

    ```bash
    sudo mkdir /lib/buildArtifacts
    sudo cp -r "/tmp/_ImageBuilding/webapp" /lib/buildArtifacts/.
    ```
    
    "/Tmp" dizinini kullanarak Tamam ' ı seçerseniz, betiği yürütmek için aşağıdaki kodu kullanabilirsiniz.
    
    ```bash
    # grant execute permissions to execute scripts
    sudo chmod +x "/tmp/_ImageBuilding/webapp/coreConfig.sh"
    echo "running script"
    sudo . "/tmp/AppsAndImageBuilderLinux/_WebApp/coreConfig.sh"
    ```
    
#### <a name="what-happens-to-the-build-artifacts-after-the-image-build"></a>Görüntü derlemeden sonra derleme yapıtlarına ne olur?

> [!NOTE]
> Image Builder, derleme yapıtlarını otomatik olarak kaldırmaz, derleme yapıtlarını kaldırmak için her zaman kodunuzun olması önemle önerilir.
> 

* Windows-Image Builder dosyaları `c:\buildArtifacts` dizine dağıtır. Dizin kalıcı olduğundan dizini kaldırmanız gerekir. Bunu, yürüttiğiniz betikte kaldırabilirsiniz. Örnek:

    ```PowerShell
    # Clean up buildArtifacts directory
    Remove-Item -Path "C:\buildArtifacts\*" -Force -Recurse
    
    # Delete the buildArtifacts directory
    Remove-Item -Path "C:\buildArtifacts" -Force 
    ```
    
* Linux-derleme yapıtları `/tmp` dizine konur. Ancak, birçok Linux OSs 'de, bir yeniden başlatma işlemi, `/tmp` Dizin içerikleri silinir. İçeriği kaldırmak için işletim sistemine bağlı olmayan ve içeriği kaldırmak için kodunuzun olması önerilir. Örnek:

    ```bash
    sudo rm -R "/tmp/AppsAndImageBuilderLinux"
    ```
    
#### <a name="total-length-of-image-build"></a>Görüntü derlemesinin toplam uzunluğu

Henüz DevOps ardışık düzen görevinde Toplam uzunluk değiştirilemez. Varsayılan değer olan 240 dakika kullanılır. [Buildtimeoutınminutes](./image-builder-json.md#properties-buildtimeoutinminutes)değerini artırmak Istiyorsanız, sürüm ARDıŞıK DÜZENINDE az CLI görevi kullanabilirsiniz. Görevi bir şablonu kopyalamak ve gönderecek şekilde yapılandırın. Bir örnek için, bu [çözüme](https://github.com/danielsollondon/azvmimagebuilder/tree/master/solutions/4_Using_ENV_Variables#using-environment-variables-and-parameters-with-image-builder)bakın veya az PowerShell kullanın.


#### <a name="storage-account"></a>Depolama Hesabı

Önkoşullarda oluşturduğunuz depolama hesabını seçin. Listede görmüyorsanız, görüntü Oluşturucu için izinleri yoktur.

Yapı başladığında, görüntü Oluşturucu adlı bir kapsayıcı oluşturur `imagebuilder-vststask` . Kapsayıcı, deponun derleme yapıtlarının depolandığı yerdir.

> [!NOTE]
> Her derlemeden sonra depolama hesabını veya kapsayıcıyı el ile silmeniz gerekir.
>

### <a name="distribute"></a>Dağıt

Desteklenen 3 dağıtım türü vardır.

#### <a name="managed-image"></a>Yönetilen görüntü

* RESOURCEID
    ```bash
    /subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/images/<imageName>
    ```

* Konumlar

#### <a name="azure-shared-image-gallery"></a>Azure Paylaşılan görüntü Galerisi

Paylaşılan görüntü Galerisi zaten var **olmalıdır** .

* RESOURCEID 
    ```bash
    /subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<imageDefName>
    ```

* Bölgeler: bölge listesi, virgülle ayrılmış. Örneğin, westus, eastus, merkezde ABD

#### <a name="vhd"></a>VHD

Bu değere bir değer geçiremezsiniz, görüntü Oluşturucu VHD 'yi VHD kapsayıcısında geçici görüntü Oluşturucu kaynak grubuna yayar `IT_<DestinationResourceGroup>_<TemplateName>` .  Yayın derlemesini başlattığınızda, görüntü Oluşturucu günlükleri yayar. İşlem tamamlandığında, VHD URL 'sini yayacaktır.

### <a name="optional-settings"></a>İsteğe bağlı ayarlar

* [VM boyutu](image-builder-json.md#vmprofile) -VM boyutunu varsayılan *Standard_D1_v2* geçersiz kılabilirsiniz. Toplam özelleştirme süresini azaltmak için veya GPU/HPC gibi belirli sanal makine boyutlarına bağlı olan görüntüleri oluşturmak istiyorsanız geçersiz kılabilirsiniz.

## <a name="how-it-works"></a>Nasıl çalışır?

Yayını oluşturduğunuzda, görev, depolama hesabında *ımagebuilder-vststask* adlı bir kapsayıcı oluşturur. It 'ler, derleme yapıtlarını yükler ve ZIP dosyası için bir SAS belirteci oluşturur.

Görev, görüntü Oluşturucu şablonu yapıtı oluşturmak için göreve geçirilen özellikleri kullanır. Görev şunları yapar:
* Derleme yapıtı ZIP dosyasını ve diğer ilişkili betikleri indirir. Dosyalar, geçici görüntü Oluşturucu kaynak grubundaki bir depolama hesabına kaydedilir `IT_<DestinationResourceGroup>_<TemplateName>` .
* Önek olarak *T_* bir şablon ve 10 basamaklı monoton tamsayısı oluşturur. Şablon, seçtiğiniz kaynak grubuna kaydedilir. Şablon, kaynak grubundaki derleme süresince bulunur. 

Örnek çıktı:

```text
start reading task parameters...
found build at:  /home/vsts/work/r1/a/_ImageBuilding/webapp
end reading parameters
getting storage account details for aibstordot1556933914
created archive /home/vsts/work/_temp/temp_web_package_21475337782320203.zip
Source for image:  { type: 'SharedImageVersion',
  imageVersionId: '/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<imageDefName>/versions/<imgVersionNumber>' }
template name:  t_1556938436xxx
starting put template...
```

Görüntü oluşturma başladığında, çalışma durumu yayın günlüklerinde bildirilir:

```text
starting run template...
```

Görüntü oluşturma tamamlandığında, aşağıdaki metne benzer bir çıktı görürsünüz:

```text
2019-05-06T12:49:52.0558229Z starting run template...
2019-05-06T13:36:33.8863094Z run template:  Succeeded
2019-05-06T13:36:33.8867768Z getting runOutput for  SharedImage_distribute
2019-05-06T13:36:34.6652541Z ==============================================================================
2019-05-06T13:36:34.6652925Z ## task output variables ##
2019-05-06T13:36:34.6658728Z $(imageUri) =  /subscriptions/<subscriptionID>/resourceGroups/aibwinsig/providers/Microsoft.Compute/galleries/my22stSIG/images/winWAppimages/versions/0.23760.13763
2019-05-06T13:36:34.6659989Z ==============================================================================
2019-05-06T13:36:34.6663500Z deleting template t_1557146959485...
2019-05-06T13:36:34.6673713Z deleting storage blob imagebuilder-vststask\webapp/18-1/webapp_1557146958741.zip
2019-05-06T13:36:34.9786039Z blob imagebuilder-vststask\webapp/18-1/webapp_1557146958741.zip is deleted
2019-05-06T13:38:37.4884068Z delete template:  Succeeded
```

Görüntü şablonu ve `IT_<DestinationResourceGroup>_<TemplateName>` silinir.

' $ (Imageuri) ' VSTS değişkenini alabilir ve bunu bir sonraki görevde kullanabilir ya da yalnızca değeri kullanabilir ve bir VM oluşturabilirsiniz.

## <a name="output-devops-variables"></a>Çıkış DevOps değişkenleri

Kaynak Market görüntüsünün yayın/teklif/SKU 'SU sürümü:
* $ (Pırpublisher)
* $ (pirOffer)
* $ (Pırsku)
* $ (Pırversion)

Görüntü URI 'si-dağıtılan görüntünün RESOURCEID değeri:
* $ (ımageuri)

## <a name="faq"></a>SSS

### <a name="can-i-use-an-existing-image-template-i-have-already-created-outside-of-devops"></a>DevOps dışında, zaten oluşturduğumuz var olan bir görüntü şablonunu kullanabilir miyim?

Şu anda şu anda değil.

### <a name="can-i-specify-the-image-template-name"></a>Görüntü şablonu adını belirtebilir miyim?

Hayır. Benzersiz bir şablon adı kullanılır ve sonra silinir.

### <a name="the-image-builder-failed-how-can-i-troubleshoot"></a>Görüntü Oluşturucusu başarısız oldu. Nasıl sorun giderebilirim?

Bir yapı hatası varsa, DevOps görevi hazırlama kaynak grubunu silmez. Yapı özelleştirme günlüğünü içeren hazırlama kaynak grubuna erişebilirsiniz.

Sanal makine görüntüsü Oluşturucu görevi için DevOps günlüğünde bir hata görürsünüz ve özelleştirme. log konumunu görürsünüz. Örnek:

:::image type="content" source="./media/image-builder-devops-task/devops-task-error.png" alt-text="Hata gösteren örnek DevOps görev hatası.":::

Sorun giderme hakkında daha fazla bilgi için bkz. [Azure Image Builder hizmetinde sorun giderme](image-builder-troubleshoot.md). 

Hatayı araştırdıktan sonra hazırlama kaynak grubunu silebilirsiniz. İlk olarak, görüntü şablonu kaynak yapıtı ' ni silin. Yapıtın ön eki *T_* ve DevOps görev derleme günlüğünde bulunabilir:

```text
...
Source for image:  { type: 'SharedImageVersion',
  imageVersionId: '/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<imageDefName>/versions/<imgVersionNumber>' }
...
template name:  t_1556938436xxx
...

```

Görüntü şablonu kaynak yapıtı başlangıçta görevde belirtilen kaynak grubudur. Sorun gidermeyi tamamladığınızda yapıtı silin. Azure portal kullanarak silme, kaynak grubu içinde, yapıyı görüntülemek için **gizli türleri göster**' i seçin.


## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi için bkz. [Azure Image Builder 'a genel bakış](../image-builder-overview.md).
