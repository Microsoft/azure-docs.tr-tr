---
title: AzCopy kullanarak Azure DevTest Labs VHD dosyasını karşıya yükleme | Microsoft Docs
description: Bu makalede, Azure DevTest Labs ' de bir laboratuvarın depolama hesabına VHD dosyası yüklemek için AzCopy komut satırı yardımcı programını kullanma kılavuzu sağlanmıştır.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 1d8ede0f78726b04ac862a00b559b8d42c3ed1cd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "88650783"
---
# <a name="upload-vhd-file-to-labs-storage-account-using-azcopy"></a>AzCopy kullanarak VHD dosyasını laboratuvarın depolama hesabına yükleyin

[!INCLUDE [devtest-lab-upload-vhd-selector](../../includes/devtest-lab-upload-vhd-selector.md)]

Azure DevTest Labs, VHD dosyaları, sanal makineleri sağlamak için kullanılan özel görüntüler oluşturmak için kullanılabilir. Aşağıdaki adımlarda, bir laboratuvarın depolama hesabına bir VHD dosyası yüklemek için AzCopy komut satırı yardımcı programını kullanma işleminde size yol gösterilmektedir. VHD dosyanızı karşıya yükledikten sonra, [sonraki adımlar bölümünde](#next-steps) KARŞıYA yüklenen VHD dosyasından nasıl özel bir görüntü oluşturulacağını gösteren bazı makaleler listelenmektedir. Azure 'daki diskler ve VHD 'ler hakkında daha fazla bilgi için bkz. [yönetilen disklere giriş](../virtual-machines/managed-disks-overview.md)

> [!NOTE] 
>  
> AzCopy yalnızca Windows komut satırı yardımcı programıdır.

## <a name="step-by-step-instructions"></a>Adım adım yönergeler

Aşağıdaki adımlarda, [AzCopy](https://aka.ms/downloadazcopy)kullanarak Azure DEVTEST Labs bir VHD dosyasını karşıya yükleme işlemi adım adım gösterilmektedir. 

1. Azure portal kullanarak laboratuvarın depolama hesabının adını alın:

1. [Azure portalında](https://go.microsoft.com/fwlink/p/?LinkID=525040) oturum açın.

1. **Tüm hizmetler**' i seçin ve ardından listeden **DevTest Labs** ' i seçin.

1. Laboratuvarlar listesinden istediğiniz Laboratuvarı seçin.  

1. Laboratuvarın dikey penceresinde **yapılandırma**' yı seçin. 

1. Laboratuvar **yapılandırması** dikey penceresinde **özel görüntüler (VHD)** seçeneğini belirleyin.

1. **Özel görüntüler** dikey penceresinde **+ Ekle**' yi seçin. 

1. **Özel görüntü** dikey penceresinde, **VHD**' yi seçin.

1. **VHD** dikey penceresinde **PowerShell kullanarak bir VHD 'yi karşıya yükle**' yi seçin.

    ![PowerShell kullanarak VHD 'YI karşıya yükleme](./media/devtest-lab-upload-vhd-using-azcopy/upload-image-using-psh.png)

1. **PowerShell kullanarak görüntü yükleme** dikey penceresi, **Add-AzureVhd** cmdlet 'ine bir çağrı görüntüler. İlk parametre (*hedef*) bir blob kapsayıcısının (*yüklemeler*) URI 'sini aşağıdaki biçimde içerir:

    ```
    https://<STORAGE-ACCOUNT-NAME>.blob.core.windows.net/uploads/...
    ``` 

1. Sonraki adımlarda kullanılan tam URI 'yi unutmayın.

1. AzCopy kullanarak VHD dosyasını karşıya yükleyin:
 
1. [AzCopy 'in en son sürümünü indirip yükleyin](https://aka.ms/downloadazcopy).

1. Bir komut penceresi açın ve AzCopy yükleme dizinine gidin. İsteğe bağlı olarak, AzCopy yükleme konumunu sistem yolunuza ekleyebilirsiniz. Varsayılan olarak, AzCopy şu dizine yüklenir:

    ```command-line
    %ProgramFiles(x86)%\Microsoft SDKs\Azure\AzCopy
    ```

1. Depolama hesabı anahtarını ve BLOB kapsayıcı URI 'sini kullanarak, komut isteminde aşağıdaki komutu çalıştırın. *Vhdfilename* değerinin tırnak içinde olması gerekir. Bir VHD dosyasını karşıya yükleme işlemi, VHD dosyasının boyutuna ve bağlantı hızına bağlı olarak uzun olabilir.   

    ```command-line
    AzCopy /Source:<sourceDirectory> /Dest:<blobContainerUri> /DestKey:<storageAccountKey> /Pattern:"<vhdFileName>" /BlobType:page
    ```

## <a name="next-steps"></a>Sonraki adımlar

- [Azure portal kullanarak bir VHD dosyasından Azure DevTest Labs özel görüntü oluşturma](devtest-lab-create-template.md)
- [PowerShell kullanarak bir VHD dosyasından Azure DevTest Labs özel görüntü oluşturma](devtest-lab-create-custom-image-from-vhd-using-powershell.md)