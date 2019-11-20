---
title: Dosyaları Azure Cloud Shell Sürdür | Microsoft Docs
description: Azure Cloud Shell dosyalarının sürekli olarak devam edip etmediğini gösteren yönergeler.
services: azure
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/04/2018
ms.author: damaerte
ms.openlocfilehash: ee68400d000ca823816c8efc6bcbc224d1388832
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74082988"
---
# <a name="persist-files-in-azure-cloud-shell"></a>Dosyaları Azure Cloud Shell Sürdür
Cloud Shell, dosyaları oturumlarda kalıcı hale getirmek için Azure dosya depolama kullanır. İlk başlangıçta Cloud Shell, dosyaları oturumlardaki kalıcı hale getirmek için yeni veya var olan bir dosya paylaşımından ilişkilendiremenizi ister.

> [!NOTE]
> Bash ve PowerShell aynı dosya paylaşımının aynısını paylaşır. Cloud Shell otomatik bağlama ile yalnızca bir dosya paylaşımının ilişkilendirilmesi olabilir.

## <a name="create-new-storage"></a>Yeni depolama alanı oluştur

Temel ayarları kullandığınızda ve yalnızca bir abonelik seçtiğinizde, Cloud Shell desteklenen bölgede sizin adınıza en yakın üç kaynak oluşturur:
* Kaynak grubu: `cloud-shell-storage-<region>`
* Depolama hesabı: `cs<uniqueGuid>`
* Dosya paylaşma: `cs-<user>-<domain>-com-<uniqueGuid>`

![Abonelik ayarı](media/persisting-shell-storage/basic-storage.png)

Dosya paylaşma, `$Home` dizininizde `clouddrive` olarak takar. Bu tek seferlik bir eylemdir ve dosya paylaşımının sonraki oturumlarda otomatik olarak takar. 

> [!NOTE]
> Güvenlik için, her kullanıcının kendi depolama hesabını sağlaması gerekir.  Rol tabanlı erişim denetimi (RBAC) için, kullanıcıların depolama hesabı düzeyinde katkıda bulunan erişimi veya üzeri olması gerekir.

Dosya paylaşımında Ayrıca, `$Home` dizininizdeki verileri otomatik olarak sürdüren, sizin için oluşturulan 5 GB bir görüntü de bulunur. Bu hem Bash hem PowerShell için geçerlidir.

## <a name="use-existing-resources"></a>Mevcut kaynakları kullan

Gelişmiş seçeneğini kullanarak, mevcut kaynakları ilişkilendirebilirsiniz. Bir Cloud Shell bölgesi seçerken aynı bölgede birlikte bulunan bir yedekleme depolama hesabı seçmeniz gerekir. Örneğin, atanan bölgeniz Batı ABD, Batı ABD içinde bulunan bir dosya paylaşımının de ilişkilendirilmesi gerekir.

Depolama kurulumu istemi göründüğünde, ek seçenekleri görüntülemek için **Gelişmiş ayarları göster** ' i seçin. Doldurulmuş depolama seçenekleri için yerel olarak yedekli depolama (LRS), coğrafi olarak yedekli depolama (GRS) ve bölgesel olarak yedekli depolama (ZRS) hesapları için filtre. 

> [!NOTE]
> Yedekleme dosya paylaşımınız için ek dayanıklılık için GRS veya ZRS depolama hesaplarının kullanılması önerilir. Hangi artıklık türü, hedeflerinize ve fiyat tercihlerinize bağlıdır. [Azure depolama hesapları için çoğaltma seçenekleri hakkında daha fazla bilgi edinin](https://docs.microsoft.com/azure/storage/common/storage-redundancy).

![Kaynak grubu ayarı](media/persisting-shell-storage/advanced-storage.png)

### <a name="supported-storage-regions"></a>Desteklenen depolama bölgeleri
İlişkili Azure depolama hesapları, bunları bağlamak istediğiniz Cloud Shell makineyle aynı bölgede bulunmalıdır. Geçerli bölgenizi bulmak için bash 'de `env` çalıştırıp `ACC_LOCATION`değişkenini bulabilirsiniz. Dosya paylaşımları, `$Home` dizininizi kalıcı hale getirmek için oluşturduğunuz 5 GB bir görüntü alır.

Cloud Shell makineler aşağıdaki bölgelerde mevcuttur:

|Alan|Bölge|
|---|---|
|Kuzey ve Güney Amerika|Doğu ABD, Orta Güney ABD, Batı ABD|
|Avrupa|Kuzey Avrupa, Batı Avrupa|
|Asya Pasifik|Hindistan Orta, Güneydoğu Asya|

## <a name="restrict-resource-creation-with-an-azure-resource-policy"></a>Azure Kaynak ilkesiyle kaynak oluşturmayı kısıtlama
Cloud Shell içinde oluşturduğunuz depolama hesapları `ms-resource-usage:azure-cloud-shell`olarak etiketlenir. Kullanıcıların Cloud Shell depolama hesapları oluşturmalarına izin vermemek istiyorsanız, bu özel etiket tarafından tetiklenen [Etiketler için bir Azure Kaynak ilkesi](../azure-policy/json-samples.md) oluşturun.



## <a name="how-cloud-shell-storage-works"></a>Cloud Shell depolama nasıl kullanılır 
Aşağıdaki yöntemlerin her ikisinde de dosyaları Cloud Shell devam ettirir: 
* Dizin içindeki tüm içerikleri kalıcı hale getirmek için `$Home` dizininizin disk görüntüsünü oluşturma. Disk görüntüsü, `fileshare.storage.windows.net/fileshare/.cloudconsole/acc_<User>.img``acc_<User>.img` olarak belirtilen dosya paylaşımınızda kaydedilir ve değişiklikleri otomatik olarak eşitler. 
* Belirtilen dosya paylaşımınızı doğrudan dosya paylaşma etkileşimi için `$Home` dizininize `clouddrive` olarak bağlama. `/Home/<User>/clouddrive` `fileshare.storage.windows.net/fileshare`eşlenir.
 
> [!NOTE]
> SSH anahtarları gibi `$Home` dizininizdeki tüm dosyalar, bağlı dosya paylaşımınızda saklanan Kullanıcı diski yansımanızda kalıcıdır. `$Home` dizininizde ve bağlı dosya paylaşımında bilgileri kalıcı hale getirmek için en iyi yöntemleri uygulayın.

## <a name="clouddrive-commands"></a>CloudDrive komutları

### <a name="use-the-clouddrive-command"></a>`clouddrive` komutunu kullanın
Cloud Shell, Cloud Shell 'e bağlı dosya paylaşımından el ile güncelleştirmenizi sağlayan `clouddrive`adlı bir komutu çalıştırabilirsiniz.
"CloudDrive" komutu çalıştırılarak ![](media/persisting-shell-storage/clouddrive-h.png)

### <a name="list-clouddrive"></a>Liste `clouddrive`
`clouddrive`olarak hangi dosya paylaşımının takıldığını saptamak için `df` komutunu çalıştırın. 

CloudDrive dosya yolu, URL 'deki depolama hesabı adınızı ve dosya paylaşımınızı gösterir. Örneğin, `//storageaccountname.file.core.windows.net/filesharename`

```
justin@Azure:~$ df
Filesystem                                          1K-blocks   Used  Available Use% Mounted on
overlay                                             29711408 5577940   24117084  19% /
tmpfs                                                 986716       0     986716   0% /dev
tmpfs                                                 986716       0     986716   0% /sys/fs/cgroup
/dev/sda1                                           29711408 5577940   24117084  19% /etc/hosts
shm                                                    65536       0      65536   0% /dev/shm
//mystoragename.file.core.windows.net/fileshareName 5368709120    64 5368709056   1% /home/justin/clouddrive
justin@Azure:~$
```

### <a name="mount-a-new-clouddrive"></a>Yeni bir CloudDrive takın

#### <a name="prerequisites-for-manual-mounting"></a>El ile bağlama önkoşulları
`clouddrive mount` komutunu kullanarak Cloud Shell ilişkili dosya paylaşımının güncelleştirilmesini sağlayabilirsiniz.

Var olan bir dosya paylaşımından bağlama yaparsanız, depolama hesaplarının Select Cloud Shell bölgesinde bulunması gerekir. `env` çalıştırıp `ACC_LOCATION`denetleyerek konumu alın.

#### <a name="the-clouddrive-mount-command"></a>`clouddrive mount` komutu

> [!NOTE]
> Yeni bir dosya paylaşma oluşturuyorsanız, `$Home` dizininiz için yeni bir Kullanıcı görüntüsü oluşturulur. Önceki `$Home` görüntünüz önceki dosya paylaşımınızda tutulur.

`clouddrive mount` komutunu aşağıdaki parametrelerle çalıştırın:

```
clouddrive mount -s mySubscription -g myRG -n storageAccountName -f fileShareName
```

Daha fazla ayrıntı görüntülemek için, `clouddrive mount -h`aşağıda gösterildiği gibi çalıştırın:

![' CloudDrive Mount 'komutunu çalıştırma](media/persisting-shell-storage/mount-h.png)

### <a name="unmount-clouddrive"></a>CloudDrive bağlantısını kaldır
Cloud Shell bağlanan bir dosya paylaşımının bağlantısını dilediğiniz zaman kaldırabilirsiniz. Cloud Shell bağlı bir dosya paylaşımının kullanılmasını gerektirdiğinden, bir sonraki oturumda başka bir dosya paylaşma oluşturmanız ve bağlamanız istenir.

1. `clouddrive unmount` öğesini çalıştırın.
2. İstemleri kabul edin ve onaylayın.

Dosya paylaşımınız, el ile silmediğiniz takdirde mevcut olmaya devam edecektir. Cloud Shell artık sonraki oturumlarda bu dosya paylaşımının aranmayacak. Daha fazla ayrıntı görüntülemek için, `clouddrive unmount -h`aşağıda gösterildiği gibi çalıştırın:

![' CloudDrive unmount'komutunu çalıştırma](media/persisting-shell-storage/unmount-h.png)

> [!WARNING]
> Bu komutu çalıştırmak hiçbir kaynağı silmez, bir kaynak grubu, depolama hesabı veya Cloud Shell eşlenen dosya paylaşımının el ile silinmesi, `$Home` Dizin disk görüntünüzü ve dosya paylaşımınızda bulunan tüm dosyaları siler. Bu eylem geri alınamaz.
## <a name="powershell-specific-commands"></a>PowerShell 'e özgü komutlar

### <a name="list-clouddrive-azure-file-shares"></a>Azure dosya paylaşımlarını `clouddrive` listeleme
`Get-CloudDrive` cmdlet 'i, şu anda Cloud Shell `clouddrive` tarafından takılan Azure dosya paylaşma bilgilerini alır. <br>
Get-CloudDrive](media/persisting-shell-storage-powershell/Get-Clouddrive.png) çalıştıran ![

### <a name="unmount-clouddrive"></a>`clouddrive` çıkarın
Cloud Shell bağlanan bir Azure dosya paylaşımının bağlantısını dilediğiniz zaman kaldırabilirsiniz. Azure dosya paylaşımında kaldırılan bir sonraki oturumda yeni bir Azure dosya paylaşma oluşturmanız ve bağlamanız istenir.

`Dismount-CloudDrive` cmdlet 'i, geçerli depolama hesabından bir Azure dosya paylaşımının serbest bir şekilde çıkarır. `clouddrive` çıkarma geçerli oturumu sonlandırır. Kullanıcıdan bir sonraki oturum sırasında yeni bir Azure dosya paylaşımının oluşturulması ve bağlanması istenir.
Dismount-CloudDrive](media/persisting-shell-storage-powershell/Dismount-Clouddrive.png) ![çalışıyor

[!INCLUDE [PersistingStorage-endblock](../../includes/cloud-shell-persisting-shell-storage-endblock.md)]

Note: bir dosyada bir işlev tanımlamanız ve PowerShell cmdlet 'lerinden çağırmak gerekiyorsa, nokta işlecinin dahil olması gerekir. Örneğin:. .\MyFunctions.ps1

## <a name="next-steps"></a>Sonraki adımlar
[Hızlı başlangıç Cloud Shell](quickstart.md) <br>
[Microsoft Azure dosyaları depolama hakkında bilgi edinin](https://docs.microsoft.com/azure/storage/storage-introduction) <br>
[Depolama etiketleri hakkında bilgi edinin](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) <br>
