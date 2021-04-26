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
ms.date: 02/24/2020
ms.author: damaerte
ms.openlocfilehash: f1846c126e81ca5851cfbb1d782e5315ae10a82a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92152268"
---
# <a name="persist-files-in-azure-cloud-shell"></a>Dosyaları Azure Cloud Shell Sürdür
Cloud Shell, dosyaları oturumlarda kalıcı hale getirmek için Azure dosya depolama kullanır. İlk başlangıçta Cloud Shell, dosyaları oturumlardaki kalıcı hale getirmek için yeni veya var olan bir dosya paylaşımından ilişkilendiremenizi ister.

> [!NOTE]
> Bash ve PowerShell aynı dosya paylaşımının aynısını paylaşır. Cloud Shell otomatik bağlama ile yalnızca bir dosya paylaşımının ilişkilendirilmesi olabilir.

> [!NOTE]
> Azure Storage güvenlik duvarı, Cloud Shell depolama hesapları için desteklenmez.

## <a name="create-new-storage"></a>Yeni depolama alanı oluştur

Temel ayarları kullandığınızda ve yalnızca bir abonelik seçtiğinizde, Cloud Shell desteklenen bölgede sizin adınıza en yakın üç kaynak oluşturur:
* Kaynak grubu: `cloud-shell-storage-<region>`
* Depolama hesabı: `cs<uniqueGuid>`
* Dosya paylaşma: `cs-<user>-<domain>-com-<uniqueGuid>`

![Abonelik ayarı](media/persisting-shell-storage/basic-storage.png)

Dosya paylaşma, `clouddrive` dizininizdeki gibi takar `$Home` . Bu tek seferlik bir eylemdir ve dosya paylaşımının sonraki oturumlarda otomatik olarak takar. 

Dosya paylaşımında Ayrıca, dizininizdeki verileri otomatik olarak sürdüren, sizin için oluşturulan 5 GB bir görüntü de bulunur `$Home` . Bu hem Bash hem PowerShell için geçerlidir.

## <a name="use-existing-resources"></a>Mevcut kaynakları kullan

Gelişmiş seçeneğini kullanarak, mevcut kaynakları ilişkilendirebilirsiniz. Bir Cloud Shell bölgesi seçerken aynı bölgede birlikte bulunan bir yedekleme depolama hesabı seçmeniz gerekir. Örneğin, atanan bölgeniz Batı ABD, Batı ABD içinde bulunan bir dosya paylaşımının de ilişkilendirilmesi gerekir.

Depolama kurulumu istemi göründüğünde, ek seçenekleri görüntülemek için **Gelişmiş ayarları göster** ' i seçin. Doldurulmuş depolama seçenekleri için yerel olarak yedekli depolama (LRS), coğrafi olarak yedekli depolama (GRS) ve bölgesel olarak yedekli depolama (ZRS) hesapları için filtre. 

> [!NOTE]
> Yedekleme dosya paylaşımınız için ek dayanıklılık için GRS veya ZRS depolama hesaplarının kullanılması önerilir. Hangi artıklık türü, hedeflerinize ve fiyat tercihlerinize bağlıdır. [Azure depolama hesapları için çoğaltma seçenekleri hakkında daha fazla bilgi edinin](../storage/common/storage-redundancy.md).

![Kaynak grubu ayarı](media/persisting-shell-storage/advanced-storage.png)

## <a name="securing-storage-access"></a>Depolama erişiminin güvenliğini sağlama
Güvenlik için, her kullanıcının kendi depolama hesabını sağlaması gerekir.  Azure rol tabanlı erişim denetimi (Azure RBAC) için, kullanıcıların depolama hesabı düzeyinde katkıda bulunan erişimi veya üzeri olması gerekir.

Cloud Shell, bir depolama hesabında, belirtilen bir abonelikte Azure dosya paylaşımından yararlanır. Devralınan izinler nedeniyle, abonelikte yeterli erişim haklarına sahip olan kullanıcılar, abonelikte bulunan tüm depolama hesaplarına ve dosya paylaşımlarına erişebilecektir.

Kullanıcılar, depolama hesabındaki veya abonelik düzeyindeki izinleri ayarlayarak dosyalarına erişimi kilitlemelidir.

## <a name="supported-storage-regions"></a>Desteklenen depolama bölgeleri
Geçerli bölgenizi bulmak için `env` Bash 'de çalıştırdığınız ve değişkeni `ACC_LOCATION` veya PowerShell çalıştırmasından ' ı bulabilirsiniz `$env:ACC_LOCATION` . Dosya paylaşımları, dizininizi kalıcı hale getirmek için oluşturduğunuz 5 GB bir görüntü alır `$Home` .

Cloud Shell makineler aşağıdaki bölgelerde mevcuttur:

|Alan|Region|
|---|---|
|Kuzey ve Güney Amerika|Doğu ABD, Orta Güney ABD, Batı ABD|
|Avrupa|Kuzey Avrupa, Batı Avrupa|
|Asya Pasifik|Hindistan Orta, Güneydoğu Asya|

Müşterilerin, bekleyen verilerinin belirli bir bölgede depolanmasını şart olmadığı durumlar dışında, müşteriler bir birincil bölge seçmelidir. Böyle bir gereklilik varsa, ikincil bir depolama bölgesinin kullanılması gerekir.

### <a name="secondary-storage-regions"></a>İkincil depolama bölgeleri
İkincil bir depolama bölgesi kullanılırsa, ilişkili Azure depolama hesabı, bunları bağlamak istediğiniz Cloud Shell makine olarak farklı bir bölgede bulunur. Örneğin, Gamze depolama hesabını Kanada Doğu, ikincil bir bölgede yer alan olarak ayarlayabilir, ancak bağlanan makine hala birincil bölgede yer alır. Rest 'teki veriler Kanada 'da bulunur, ancak Birleşik Devletler işlenir.

> [!NOTE]
> Bir ikincil bölge kullanılıyorsa, Cloud Shell için dosya erişimi ve başlangıç zamanı daha yavaş olabilir.

Bir Kullanıcı, `(Get-CloudDrive | Get-AzStorageAccount).Location` dosya paylaşımının konumunu görmek Için PowerShell 'de çalıştırılabilir.

## <a name="restrict-resource-creation-with-an-azure-resource-policy"></a>Azure Kaynak ilkesiyle kaynak oluşturmayı kısıtlama
Cloud Shell içinde oluşturduğunuz depolama hesapları ile etiketlenir `ms-resource-usage:azure-cloud-shell` . Kullanıcıların Cloud Shell depolama hesapları oluşturmalarına izin vermemek istiyorsanız, bu özel etiket tarafından tetiklenen [Etiketler için bir Azure Kaynak ilkesi](../governance/policy/samples/index.md) oluşturun.

## <a name="how-cloud-shell-storage-works"></a>Cloud Shell depolama nasıl kullanılır 
Aşağıdaki yöntemlerin her ikisinde de dosyaları Cloud Shell devam ettirir: 
* Dizin `$Home` içindeki tüm içerikleri kalıcı hale getirmek için dizininizin disk görüntüsünü oluşturma. Disk görüntüsü, belirtilen dosya paylaşımınızda olduğu gibi kaydedilir `acc_<User>.img` `fileshare.storage.windows.net/fileshare/.cloudconsole/acc_<User>.img` ve değişiklikleri otomatik olarak eşitler. 
* `clouddrive` `$Home` Doğrudan dosya paylaşma etkileşimi için belirtilen dosya paylaşımınızı dizininize bağlama. `/Home/<User>/clouddrive` öğesine eşlenir `fileshare.storage.windows.net/fileshare` .
 
> [!NOTE]
> `$Home`DIZININIZDEKI SSH anahtarları gibi tüm dosyalar, bağlı dosya paylaşımınızda saklanan Kullanıcı diski yansımanızda kalıcıdır. Dizininizde ve bağlı dosya paylaşımında bilgileri kalıcı hale getirebilmeniz için en iyi yöntemleri uygulayın `$Home` .

## <a name="clouddrive-commands"></a>CloudDrive komutları

### <a name="use-the-clouddrive-command"></a>Komutunu kullanın `clouddrive`
Cloud Shell ' de, `clouddrive` Cloud Shell bağlı dosya paylaşımının el ile güncelleştirilmesini sağlayan adlı bir komutu çalıştırabilirsiniz.

!["CloudDrive" komutu çalıştırılıyor](media/persisting-shell-storage/clouddrive-h.png)

### <a name="list-clouddrive"></a>Listele `clouddrive`
Hangi dosya paylaşımının bağlı olduğunu saptamak için `clouddrive` `df` komutunu çalıştırın. 

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
Komutunu kullanarak Cloud Shell ilişkili dosya paylaşımının güncelleştirilmesini sağlayabilirsiniz `clouddrive mount` .

Var olan bir dosya paylaşımından bağlama yaparsanız, depolama hesaplarının Select Cloud Shell bölgesinde bulunması gerekir. Çalıştırarak ve denetleyerek konumu alın `env` `ACC_LOCATION` .

#### <a name="the-clouddrive-mount-command"></a>`clouddrive mount`Komutu

> [!NOTE]
> Yeni bir dosya paylaşma oluşturuyorsanız, dizininiz için yeni bir Kullanıcı görüntüsü oluşturulur `$Home` . Önceki `$Home` resminiz önceki dosya paylaşımınızda tutulur.

`clouddrive mount`Komutunu aşağıdaki parametrelerle çalıştırın:

```
clouddrive mount -s mySubscription -g myRG -n storageAccountName -f fileShareName
```

Daha fazla ayrıntı görüntülemek için, `clouddrive mount -h` aşağıda gösterildiği gibi çalıştırın:

![' CloudDrive Mount 'komutunu çalıştırma](media/persisting-shell-storage/mount-h.png)

### <a name="unmount-clouddrive"></a>CloudDrive bağlantısını kaldır
Cloud Shell bağlanan bir dosya paylaşımının bağlantısını dilediğiniz zaman kaldırabilirsiniz. Cloud Shell bağlı bir dosya paylaşımının kullanılmasını gerektirdiğinden, bir sonraki oturumda başka bir dosya paylaşma oluşturmanız ve bağlamanız istenir.

1. `clouddrive unmount` öğesini çalıştırın.
2. İstemleri kabul edin ve onaylayın.

Dosya paylaşımınız, el ile silmediğiniz takdirde mevcut olmaya devam edecektir. Cloud Shell artık sonraki oturumlarda bu dosya paylaşımının aranmayacak. Daha fazla ayrıntı görüntülemek için, `clouddrive unmount -h` aşağıda gösterildiği gibi çalıştırın:

![' CloudDrive unmount'komutunu çalıştırma](media/persisting-shell-storage/unmount-h.png)

> [!WARNING]
> Bu komutu çalıştırmak hiçbir kaynağı silmez, bir kaynak grubu, depolama hesabı veya Cloud Shell eşlenen dosya paylaşımının el ile silinmesi, `$Home` Dizin disk görüntünüzü ve dosya paylaşımınızda bulunan dosyaları siler. Bu eylem geri alınamaz.
## <a name="powershell-specific-commands"></a>PowerShell 'e özgü komutlar

### <a name="list-clouddrive-azure-file-shares"></a>`clouddrive`Azure dosya paylaşımlarını listeleyin
`Get-CloudDrive`Cmdlet 'i, Cloud Shell tarafından şu anda bağlı olan Azure dosya paylaşma bilgilerini alır `clouddrive` . <br>
![Get-CloudDrive çalıştırılıyor](media/persisting-shell-storage-powershell/Get-Clouddrive.png)

### <a name="unmount-clouddrive"></a>Madı `clouddrive`
Cloud Shell bağlanan bir Azure dosya paylaşımının bağlantısını dilediğiniz zaman kaldırabilirsiniz. Azure dosya paylaşımında kaldırılan bir sonraki oturumda yeni bir Azure dosya paylaşma oluşturmanız ve bağlamanız istenir.

`Dismount-CloudDrive`Cmdlet 'i geçerli depolama hesabından bir Azure dosya paylaşımının takar. Kaldırma, `clouddrive` geçerli oturumu sonlandırır. Kullanıcıdan bir sonraki oturum sırasında yeni bir Azure dosya paylaşımının oluşturulması ve bağlanması istenir.
![Dismount-CloudDrive çalıştırılıyor](media/persisting-shell-storage-powershell/Dismount-Clouddrive.png)

[!INCLUDE [PersistingStorage-endblock](../../includes/cloud-shell-persisting-shell-storage-endblock.md)]

Note: bir dosyada bir işlev tanımlamanız ve PowerShell cmdlet 'lerinden çağırmak gerekiyorsa, nokta işlecinin dahil olması gerekir. Örneğin:. .\MyFunctions.ps1

## <a name="next-steps"></a>Sonraki adımlar
[Hızlı başlangıç Cloud Shell](quickstart.md) <br>
[Microsoft Azure dosyaları depolama hakkında bilgi edinin](../storage/files/storage-files-introduction.md) <br>
[Depolama etiketleri hakkında bilgi edinin](../azure-resource-manager/management/tag-resources.md) <br>