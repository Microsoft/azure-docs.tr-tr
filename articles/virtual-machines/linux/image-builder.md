---
title: Azure Image Builder ile Linux VM oluşturma (Önizleme)
description: Azure görüntü Oluşturucu ile bir Linux VM oluşturun.
author: cynthn
ms.author: cynthn
ms.date: 05/02/2019
ms.topic: article
ms.service: virtual-machines-linux
manager: gwallace
ms.openlocfilehash: 1bac04bbb67c7472de92c6da322121bafc20a560
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/31/2019
ms.locfileid: "68695443"
---
# <a name="preview-create-a-linux-vm-with-azure-image-builder"></a>Önizleme: Azure Image Builder ile Linux VM oluşturma

Bu makalede, Azure görüntü Oluşturucu ve Azure CLı kullanarak özelleştirilmiş bir Linux görüntüsünü nasıl oluşturabileceğiniz gösterilmektedir. Bu makaledeki örnek, görüntüyü özelleştirmek için üç farklı [özelleştiriciler](image-builder-json.md#properties-customize) kullanır:

- Shell (ScriptUri)-bir [kabuk betiği](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/customizeScript.sh)indirir ve çalıştırır.
- Kabuk (satır içi)-belirli komutları çalıştırır. Bu örnekte, satır içi komutlar bir dizin oluşturma ve işletim sistemini güncelleştirme içerir.
- Dosya-GitHub ' [dan bir dosyayı](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/exampleArtifacts/buildArtifacts/index.html) VM 'deki bir dizine kopyalar.

Ayrıca, bir `buildTimeoutInMinutes`de belirtebilirsiniz. Varsayılan değer 240 dakikadır ve bir derleme süresini daha uzun süre çalışan derlemeler için de artırabilirsiniz.

Görüntüyü yapılandırmak için bir Sample. JSON şablonu kullanacağız. Kullandığımız. JSON dosyası şu şekildedir: [Helloımagetemplatelinux. JSON](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Linux_Managed_Image/helloImageTemplateLinux.json). 

> [!IMPORTANT]
> Azure görüntü Oluşturucu Şu anda genel önizleme aşamasındadır.
> Önizleme sürümü bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yüklerinde kullanılması önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir. Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="register-the-features"></a>Özellikleri kaydetme
Önizleme sırasında Azure Image Builder 'ı kullanmak için yeni özelliği kaydetmeniz gerekir.

```azurecli-interactive
az feature register --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview
```

Özellik kaydının durumunu denetleyin.

```azurecli-interactive
az feature show --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview | grep state
```

Kaydınızı denetleyin.

```azurecli-interactive
az provider show -n Microsoft.VirtualMachineImages | grep registrationState

az provider show -n Microsoft.Storage | grep registrationState
```

Kayıtlı değilse, aşağıdakileri çalıştırın:

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages

az provider register -n Microsoft.Storage
```

## <a name="setup-example-variables"></a>Kurulum örnek değişkenleri

Bazı bilgi parçalarını sürekli olarak kullanacağız. bu nedenle, bu bilgileri depolamak için bazı değişkenler oluşturacağız.


```azurecli-interactive
# Resource group name - we are using myImageBuilderRG in this example
imageResourceGroup=myImageBuilerRGLinux
# Datacenter location - we are using West US 2 in this example
location=WestUS2
# Name for the image - we are using myBuilderImage in this example
imageName=myBuilderImage
# Run output name
runOutputName=aibLinux
```

Abonelik KIMLIĞINIZ için bir değişken oluşturun. Bunu kullanarak `az account show | grep id`edinebilirsiniz.

```azurecli-interactive
subscriptionID=<Your subscription ID>
```

## <a name="create-the-resource-group"></a>Kaynak grubunu oluşturun.
Bu, görüntü yapılandırma şablonu yapıtı ve görüntüsünü depolamak için kullanılır.

```azurecli-interactive
az group create -n $imageResourceGroup -l $location
```

## <a name="set-permissions-on-the-resource-group"></a>Kaynak grubunda izinleri ayarla
Görüntü Oluşturucu ' katkıda bulunan ' iznini kaynak grubunda oluşturmak için izin verin. Doğru izinler olmadan görüntü oluşturma başarısız olur. 

`--assignee` Değer, görüntü Oluşturucu hizmeti için uygulama kayıt kimliğidir. 

```azurecli-interactive
az role assignment create \
    --assignee cf32a0cc-373c-47c9-9156-0db11f6a6dfc \
    --role Contributor \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```

## <a name="download-the-template-example"></a>Şablon örneğini indirin

Kullanmanız için parametreli bir örnek görüntü yapılandırma şablonu oluşturulmuştur. Sample. json dosyasını indirin ve daha önce ayarladığınız değişkenlerle yapılandırın.

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Linux_Managed_Image/helloImageTemplateLinux.json -o helloImageTemplateLinux.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateLinux.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateLinux.json
sed -i -e "s/<region>/$location/g" helloImageTemplateLinux.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateLinux.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateLinux.json
```

Bu örnek. JSON öğesini gerektiği şekilde değiştirebilirsiniz. Örneğin, değerini `buildTimeoutInMinutes` daha uzun süre çalışan derlemeler için izin verecek şekilde artırabilirsiniz. Dosyasını, gibi `vi`bir metin düzenleyicisi kullanarak Cloud Shell düzenleyebilirsiniz.

```azurecli-interactive
vi helloImageTemplateLinux.json
```

> [!NOTE]
> Kaynak görüntü için, her zaman `latest` [bir sürüm belirtmeniz](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-version-failure)gerekir.
>
> Görüntünün dağıtıldığı kaynak grubunu ekler veya değiştirirseniz, [izinlerin kaynak grubu için ayarlandığından](#set-permissions-on-the-resource-group)emin olmanız gerekir.


## <a name="submit-the-image-configuration"></a>Görüntü yapılandırmasını gönderme
Görüntü yapılandırmasını VM görüntü Oluşturucu hizmetine gönderme

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateLinux.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

Başarılı bir şekilde tamamlanırsa, bir başarı iletisi döndürür ve $imageResourceGroup bir görüntü Oluşturucu yapılandırma şablonu yapıtı oluşturur. ' Gizli türleri göster ' i etkinleştirirseniz, portalda kaynak grubunu görebilirsiniz.

Ayrıca, arka planda, görüntü Oluşturucu aboneliğinizde bir hazırlama kaynak grubu oluşturur. Image Builder, görüntü derlemesi için hazırlama kaynak grubunu kullanır. Kaynak grubunun adı şu biçimde olacaktır: `IT_<DestinationResourceGroup>_<TemplateName>`.

> [!IMPORTANT]
> Hazırlama kaynak grubunu doğrudan silmeyin. Görüntü şablonu yapıtı silerseniz, hazırlama kaynak grubunu otomatik olarak siler. Daha fazla bilgi için bu makalenin sonundaki [Temizleme](#clean-up) bölümüne bakın.

Görüntü yapılandırma şablonu gönderimi sırasında hizmet bir hata bildirirse, [sorun giderme](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#template-submission-errors--troubleshooting) adımlarına bakın. Derlemeyi göndermeyi yeniden denemeden önce şablonu da silmeniz gerekir. Şablonu silmek için:

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

## <a name="start-the-image-build"></a>Görüntü derlemesini Başlat

Görüntü derlemesini başlatın.


```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateLinux01 \
     --action Run 
```

Derleme tamamlanana kadar bekleyin, bu örnekte 10-15 dakika sürebilir.

Herhangi bir hatayla karşılaşırsanız lütfen bu [sorun giderme](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-build-errors--troubleshooting) adımlarını gözden geçirin.


## <a name="create-the-vm"></a>Sanal makine oluşturma

Oluşturduğunuz görüntüyü kullanarak VM 'yi oluşturun.

```azurecli-interactive
az vm create \
  --resource-group $imageResourceGroup \
  --name myVM \
  --admin-username azureuser \
  --image $imageName \
  --location $location \
  --generate-ssh-keys
```

VM 'yi oluşturma çıktısından IP adresini alın ve VM 'ye SSH için kullanın.

```azurecli-interactive
ssh azureuser@<pubIp>
```

Görüntünün, SSH bağlantınız kurulduğu anda günün Iletisiyle özelleştirildiğini görmeniz gerekir!

```console

*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

SSH `exit` bağlantısını kapatmak için işiniz bittiğinde yazın.

## <a name="check-the-source"></a>Kaynağı denetleyin

Görüntü Oluşturucu şablonunda, ' Özellikler ' içinde, kaynak görüntüyü, çalıştırılan özelleştirme betiğini ve nerede dağıtıldığını görürsünüz.

```azurecli-interactive
cat helloImageTemplateLinux.json
```

Bu. JSON dosyası hakkında daha ayrıntılı bilgi için bkz. [Görüntü Oluşturucu şablonu başvurusu](image-builder-json.md)

## <a name="clean-up"></a>Temizleme

İşiniz bittiğinde, kaynakları silebilirsiniz.

Görüntü Oluşturucu şablonunu silin.

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

Görüntü kaynak grubunu silin.

```bash
az group delete -n $imageResourceGroup
```


## <a name="next-steps"></a>Sonraki adımlar

Bu makalede kullanılan. json dosyasının bileşenleri hakkında daha fazla bilgi edinmek için bkz. [Görüntü Oluşturucu şablonu başvurusu](image-builder-json.md).
