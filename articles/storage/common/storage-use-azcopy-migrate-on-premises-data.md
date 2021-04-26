---
title: "Öğretici: AzCopy ile şirket içi verileri Azure depolama 'ya geçirme | Microsoft Docs"
description: Bu öğreticide AzCopy komutunu kullanarak blob, tablo ve dosya içeriğine/içeriğinden verileri geçirecek veya kopyalayacaksınız. Yerel depolama alanınızdan Azure Depolama’ya kolayca verileri geçirin.
author: normesta
ms.service: storage
ms.topic: tutorial
ms.date: 05/14/2019
ms.author: normesta
ms.reviewer: seguler
ms.subservice: common
ms.openlocfilehash: 682394329205d74859c1af0c0a68a37539da7872
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98881036"
---
#  <a name="tutorial-migrate-on-premises-data-to-cloud-storage-with-azcopy"></a>Öğretici: AzCopy ile şirket içi verileri bulut depolamaya geçirme

AzCopy; basit komutlar kullanılarak Azure Blob depolamaya, Azure Dosyaları’na ve Azure Tablosu depolama alanına veya bunlardan veri kopyalamaya yönelik bir komut satırı aracıdır. Komutlar, en iyi performans için tasarlanmıştır. AzCopy ile bir dosya sistemi ile depolama hesabı arasında veya depolama hesapları arasında verileri kopyalayabilirsiniz. AzCopy, yerel verileri (şirket içi) bir depolama hesabına kopyalamak için kullanılabilir.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:

> [!div class="checklist"]
> * Depolama hesabı oluşturma. 
> * AzCopy komutunu kullanarak tüm verilerinizi karşıya yükleme.
> * Test amacıyla verileri değiştirme.
> * Karşıya yüklenecek yeni dosyaları belirlemek için zamanlanmış bir görev veya sıralanmış iş oluşturma.

Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) oluşturun.

## <a name="prerequisites"></a>Önkoşullar

Bu öğreticiyi tamamlayabilmeniz için AzCopy 'in en son sürümünü indirin. Bkz. [AzCopy ile çalışmaya başlama](storage-use-azcopy-v10.md).

Windows kullanıyorsanız bu öğreticide görev zamanlamak için kullanılan [Schtasks](/windows/win32/taskschd/schtasks) uygulamasını edinmeniz gerekir. Linux kullanıcıları bunun yerine crontab komutunu kullanacaktır.

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

## <a name="create-a-container"></a>Kapsayıcı oluşturma

İlk adım bir kapsayıcı oluşturmaktır. Blobların her zaman bir kapsayıcı içine yüklenmesi gerekir. Bilgisayarınızdaki dosyaları düzenlemek için klasörleri kullandığınız gibi blob gruplarını düzenlemek için de kapsayıcılardan faydalanabilirsiniz.

Kapsayıcı oluşturmak için şu adımları izleyin:

1. Ana sayfadan **Depolama hesapları** düğmesini seçin ve oluşturduğunuz depolama hesabını seçin.
2. **Hizmetler** bölümünden **Bloblar**’ı seçin ve sonra **Kapsayıcı**’yı seçin.

   ![Kapsayıcı oluşturmayı gösteren ekran görüntüsü](media/storage-azcopy-migrate-on-premises-data/CreateContainer.png)
 
Kapsayıcı harfleri bir harf veya sayı ile başlamalıdır. Bunlar yalnızca harf, sayı ve kısa çizgi karakterini (-) içerebilir. Kapsayıcıları ve blobları adlandırma kuralları hakkında daha fazla bilgi için bkz. [Kapsayıcıları, blobları ve meta verileri adlandırma ve bunlara başvurma](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

## <a name="download-azcopy"></a>AzCopy'yi indirin

AzCopy Ile v10 arasındaki çalıştırılabilir dosyasını indirin.

- [Windows](https://aka.ms/downloadazcopy-v10-windows) (zip)
- [Linux](https://aka.ms/downloadazcopy-v10-linux) (tar)
- [MacOS](https://aka.ms/downloadazcopy-v10-mac) (zip)

AzCopy dosyasını bilgisayarınızda herhangi bir yere yerleştirin. Bilgisayarınızdaki herhangi bir klasörden bu yürütülebilir dosyaya başvurabilmeniz için dosyanın konumunu sistem yolu değişkeninizden ekleyin.

## <a name="authenticate-with-azure-ad"></a>Azure AD ile kimlik doğrulaması

İlk olarak, [Depolama Blobu veri katılımcısı](../../role-based-access-control/built-in-roles.md#storage-queue-data-contributor) rolünü kimliğinize atayın. [BLOB ve kuyruk verilerine erişim Için Azure rolü atamak üzere Azure Portal kullanma](./storage-auth-aad-rbac-portal.md)konusuna bakın.

Ardından, bir komut istemi açın, aşağıdaki komutu yazın ve ENTER tuşuna basın.

```azcopy
azcopy login
```

Bu komut, bir kimlik doğrulama kodu ve bir Web sitesinin URL 'sini döndürür. Web sitesini açın, kodu sağlayın ve sonra **İleri** düğmesini seçin.

![Oturum açma isteminin gösterildiği ekran görüntüsü](media/storage-use-azcopy-v10/azcopy-login.png)

Oturum açma penceresi görüntülenir. Bu pencerede, Azure hesabı kimlik bilgilerinizi kullanarak Azure hesabınızda oturum açın. Başarıyla oturum açtıktan sonra, tarayıcı penceresini kapatabilir ve AzCopy kullanmaya başlayabilirsiniz.

## <a name="upload-contents-of-a-folder-to-blob-storage"></a>Bir klasörün içeriğini Blob depolama alanına yükleme

AzCopy komutunu kullanarak, bir klasördeki tüm dosyaları, [Windows](./storage-use-azcopy-v10.md) veya [Linux](./storage-use-azcopy-v10.md) üzerindeki Blob depolama alanına yükleyebilirsiniz. Bir klasördeki tüm blobları karşıya yüklemek için aşağıdaki AzCopy komutunu girin:

```AzCopy
azcopy copy "<local-folder-path>" "https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>" --recursive=true
```

* `<local-folder-path>`Yer tutucusunu, dosya içeren bir klasörün yolu ile değiştirin (örneğin: `C:\myFolder` veya `/mnt/myFolder` ).

* `<storage-account-name>`Yer tutucusunu depolama hesabınızın adıyla değiştirin.

* `<container-name>`Yer tutucusunu, oluşturduğunuz kapsayıcının adıyla değiştirin.

Belirtilen dizinin içeriğini BLOB depolama alanına yinelemeli olarak yüklemek için, `--recursive` seçeneğini belirtin. AzCopy komutunu bu seçenekle çalıştırdığınızda, tüm alt klasörler ve dosyaları da karşıya yüklenir.

## <a name="upload-modified-files-to-blob-storage"></a>Değiştirilen dosyaları Blob depolama alanına yükleme

Son değiştirilme zamanına göre dosyaları karşıya yüklemek için de AzCopy kullanabilirsiniz. 

Bunu denemek için, test amacıyla kaynak dizininizde yeni dosyalar oluşturun veya değiştirin. Ardından AzCopy `sync` komutunu kullanın.

```AzCopy
azcopy sync "<local-folder-path>" "https://<storage-account-name>.blob.core.windows.net/<container-name>" --recursive=true
```

* `<local-folder-path>`Yer tutucusunu, dosya içeren bir klasörün yolu ile değiştirin (örneğin: `C:\myFolder` veya `/mnt/myFolder` .

* `<storage-account-name>`Yer tutucusunu depolama hesabınızın adıyla değiştirin.

* `<container-name>`Yer tutucusunu, oluşturduğunuz kapsayıcının adıyla değiştirin.

Komutu hakkında daha fazla bilgi edinmek için `sync` bkz. [Synchronize Files](./storage-use-azcopy-v10.md#transfer-data).

## <a name="create-a-scheduled-task"></a>Zamanlanmış görev oluşturma

AzCopy komut betiğini çalıştıran bir zamanlanmış görev veya sıralanmış iş oluşturabilirsiniz. Betik, belirli bir zaman aralığında yeni şirket içi verileri belirler ve bulut depolama alanına yükler.

AzCopy komutunu bir metin düzenleyiciye kopyalayın. AzCopy komutunun parametre değerlerini uygun değerlerle güncelleştirin. Dosyayı, AzCopy için `script.sh` (Linux) veya `script.bat` (Windows) olarak kaydedin. 

Bu örneklerde, klasörünüzün adlandırıldığı `myFolder` , depolama hesabınızın adı `mystorageaccount` ve kapsayıcının adı olduğu varsayılır `mycontainer` .

> [!NOTE]
> Linux örneği bir SAS belirteci ekler. Komutunuz için bir tane sağlamanız gerekir. AzCopy Ile v10 arasındaki 'ın geçerli sürümü cron işlerinde Azure AD yetkilendirmesini desteklemez.

# <a name="linux"></a>[Linux](#tab/linux)

```bash
azcopy sync "/mnt/myfiles" "https://mystorageaccount.blob.core.windows.net/mycontainer?sv=2018-03-28&ss=bfqt&srt=sco&sp=rwdlacup&se=2019-05-30T06:57:40Z&st=2019-05-29T22:57:40Z&spr=https&sig=BXHippZxxx54hQn%2F4tBY%2BE2JHGCTRv52445rtoyqgFBUo%3D" --recursive=true
```

# <a name="windows"></a>[Windows](#tab/windows)

```bash
azcopy sync "C:\myFolder" "https://mystorageaccount.blob.core.windows.net/mycontainer" --recursive=true
```

---

Bu öğreticide, Windows üzerinde zamanlanmış görev oluşturmak için [Schtasks](/windows/win32/taskschd/schtasks) kullanılır. Linux üzerinde bir sıralanmış iş oluşturmak için [Crontab](http://crontab.org/) komutu kullanılır.

 **Schtasks**, bir yöneticinin yerel veya uzak bilgisayarda zamanlanmış görevler oluşturmasına, silmesine, sorgulamasına, değiştirmesine, çalıştırmasına ve sonlandırmasına olanak sağlar. **Cron**, Linux ve Unix kullanıcılarının [sıralanmış iş ifadeleri](https://en.wikipedia.org/wiki/Cron#CRON_expression) kullanarak belirtilen bir tarih ve saatte komutları veya betikleri çalıştırmasına olanak sağlar.

# <a name="linux"></a>[Linux](#tab/linux)

Linux üzerinde sıralanmış iş oluşturmak için bir terminale aşağıdaki komutu girin:

```bash
crontab -e
*/5 * * * * sh /path/to/script.sh
```

Komutta `*/5 * * * *` sıralanmış iş ifadesi belirtilmesi, `script.sh` kabuk betiğinin beş dakikada bir çalıştırılacağını belirtir. Betiği, günlük, aylık veya yıllık olarak belirli bir saatte çalıştırılacak şekilde zamanlayabilirsiniz. İş yürütme için tarih ve saati ayarlama hakkında daha fazla bilgi edinmek istiyorsanız bkz. [sıralanmış iş ifadeleri](https://en.wikipedia.org/wiki/Cron#CRON_expression).

# <a name="windows"></a>[Windows](#tab/windows)

Windows üzerinde zamanlanmış görev oluşturmak için, komut satırına veya PowerShell’e aşağıdaki komutu girin:

Bu örnek, betiğinizin bilgisayarınızın kök sürücüsünde bulunduğunu varsayar, ancak betiğinizin istediğiniz her yerde olabilir.

```cmd
schtasks /CREATE /SC minute /MO 5 /TN "AzCopy Script" /TR C:\script.bat
```

Komut:
- Bir dakikalık zamanlamayı belirtmek için `/SC` parametresini kullanır.
- Beş dakikalık zaman aralığını belirtmek için `/MO` parametresini kullanır.
- Görev adını belirtmek için `/TN` parametresini kullanır.
- `script.bat` dosyasının yolunu belirtmek için `/TR` parametresini kullanır.

Windows üzerinde zamanlanmış görev oluşturma hakkında daha fazla bilgi için bkz. [Schtasks](/previous-versions/orphan-topics/ws.10/cc772785(v=ws.10)#BKMK_minutes).

---

Zamanlanmış görevin/sıralanmış işin düzgün şekilde çalıştığını doğrulamak için `myFolder` dizininizde yeni dosyalar oluşturun. Yeni dosyaların depolama hesabınıza yüklendiğini onaylamak için beş dakika bekleyin. Zamanlanmış görevin veya sıralanmış işin çıktı günlüklerini görüntülemek için günlük dizininize gidin.

## <a name="next-steps"></a>Sonraki adımlar

Şirket içi verileri Azure Depolama’ya (veya tam tersi) taşıma yolları hakkında daha fazla bilgi edinmek için bkz.:

* [Azure Depolamadan/Depolamaya veri taşıma](./storage-choose-data-transfer-solution.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).  

AzCopy hakkında daha fazla bilgi için şu makalelerden birine bakın:

* [AzCopy’yi kullanmaya başlama](storage-use-azcopy-v10.md)

* [AzCopy ve BLOB Storage ile veri aktarma](./storage-use-azcopy-v10.md#transfer-data)

* [AzCopy ve dosya depolama ile verileri aktarma](storage-use-azcopy-files.md)

* [AzCopy ve Amazon S3 demetleri ile veri aktarma](storage-use-azcopy-s3.md)
 
* [AzCopy 'i yapılandırma, iyileştirme ve sorun giderme](storage-use-azcopy-configure.md)
