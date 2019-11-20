---
title: Azure depolama geçişi hakkında SSS | Microsoft Docs
description: Azure Storage 'ı geçirme hakkında sık sorulan soruların yanıtları
services: storage
author: genlin
manager: dcscontentpm
ms.service: storage
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.subservice: common
ms.openlocfilehash: 250cdedaa5155f1487cb842be492acd82c0f26b3
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/18/2019
ms.locfileid: "71090811"
---
# <a name="frequently-asked-questions-about-azure-storage-migration"></a>Azure depolama geçişi hakkında sık sorulan sorular

Bu makalede, Azure depolama geçişi hakkında sık sorulan sorular yanıtlanmaktadır. 

## <a name="faq"></a>SSS

**Dosyaları bir kapsayıcıdan diğerine kopyalamak için bir betik oluşturmak Nasıl yaparım??**

Kapsayıcılar arasında dosya kopyalamak için AzCopy kullanabilirsiniz. Aşağıdaki örneğe bakın:

    AzCopy /Source:https://xxx.blob.core.windows.net/xxx
    /Dest:https://xxx.blob.core.windows.net/xxx /SourceKey:xxx /DestKey:xxx
    /S

AzCopy, kapsayıcıdaki her dosyayı kopyalamak için [kopyalama blobu API](https://docs.microsoft.com/rest/api/storageservices/copy-blob) 'sini kullanır.  
  
AzCopy çalıştırmak için internet erişimi olan herhangi bir sanal makineyi veya yerel makineyi kullanabilirsiniz. Bunu otomatik olarak yapmak için bir Azure Batch zamanlaması da kullanabilirsiniz, ancak bu daha karmaşıktır.  
  
Otomasyon betiği, depolama içeriği düzenlemesi yerine Azure Resource Manager dağıtım için tasarlanmıştır. Daha fazla bilgi için bkz. [Kaynak Yöneticisi şablonları ve Azure PowerShell ile kaynak dağıtma](../../azure-resource-manager/resource-group-template-deploy.md).

**Aynı bölgedeki aynı depolama hesabındaki iki dosya paylaşımı arasında veri kopyalamaya yönelik bir ücret alınır mı?**

Hayır. Bu süreç için ücret alınmaz.

**Depolama hesabımın tamamını başka bir depolama hesabına yedeklemek Nasıl yaparım? mı?**

Tüm depolama hesabını doğrudan yedekleme seçeneği yoktur. Ancak, bu depolama hesabındaki kapsayıcıyı AzCopy veya Depolama Gezgini kullanarak el ile başka bir hesaba taşıyabilirsiniz. Aşağıdaki adımlar, kapsayıcıyı taşımak için AzCopy 'in nasıl kullanılacağını göstermektedir:  
 

1.  [AzCopy](storage-use-azcopy.md) komut satırı aracını yükler. Bu araç, VHD dosyasını depolama hesapları arasında taşımanızı sağlar.

2.  Yükleyiciyi kullanarak Windows 'a AzCopy yükledikten sonra, bir komut Istemi penceresi açın ve bilgisayarınızdaki AzCopy yükleme klasörüne gidin. AzCopy varsayılan olarak, yüklü **% ProgramFiles (x86) %\Microsoft SDKs\Azure\AzCopy** veya **%ProgramFiles%\Microsoft SDKs\Azure\AzCopy**.

3.  Kapsayıcıyı taşımak için aşağıdaki komutu çalıştırın. Metni gerçek değerlerle değiştirmeniz gerekir.   
     
            AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1
            /Dest:https://destaccount.blob.core.windows.net/mycontainer2
            /SourceKey:key1 /DestKey:key2 /S

    - `/Source`: Kaynak depolama hesabı için URI 'yi sağlayın (kapsayıcıya kadar).  
    - `/Dest`: Hedef depolama hesabı için URI 'yi sağlayın (kapsayıcıya kadar).  
    - `/SourceKey`: Kaynak depolama hesabı için birincil anahtarı sağlayın. Depolama hesabını seçerek bu anahtarı Azure portal kopyalayabilirsiniz.  
    - `/DestKey`: Hedef depolama hesabı için birincil anahtarı sağlayın. Depolama hesabını seçerek bu anahtarı portaldan kopyalayabilirsiniz.

Bu komutu çalıştırdıktan sonra kapsayıcı dosyaları hedef depolama hesabına taşınır.

> [!NOTE]
> Bir Azure blobundan diğerine kopyalama yaptığınızda AzCopy CLı, **model** anahtarla birlikte çalışmaz.
>
> AzCopy komutunu doğrudan kopyalayabilir ve düzenleyebilir ve **modelin** kaynakla eşleştiğinden emin olmak için çapraz denetim yapabilirsiniz. Ayrıca, **/s** joker karakterlerinin etkin olduğundan emin olun. Daha fazla bilgi için bkz. [AzCopy parametreleri](storage-use-azcopy.md).

**Verileri bir depolama kapsayıcısından diğerine taşımak Nasıl yaparım? istiyor musunuz?**

Şu adımları uygulayın:

1.  Hedef Blobun kapsayıcıyı (klasör) oluşturun.

2.  İçeriği özgün blob kapsayıcısından farklı bir blob kapsayıcısına kopyalamak için [AzCopy](https://azure.microsoft.com/blog/azcopy-5-1-release/) kullanın.

**Azure Storage 'da verileri bir Azure dosya paylaşımından diğerine taşımak için bir PowerShell betiği oluşturmak Nasıl yaparım? misiniz?**

Azure depolama 'da verileri bir Azure dosya paylaşımından diğerine taşımak için AzCopy kullanın. Daha fazla bilgi için bkz. [Windows üzerinde AzCopy ile veri aktarma](storage-use-azcopy.md) ve [Linux üzerinde AzCopy ile veri aktarma](storage-use-azcopy-linux.md).

**Büyük. csv dosyalarını Azure depolama birimine karşıya Nasıl yaparım?.**

Büyük. csv dosyalarını Azure depolama 'ya yüklemek için AzCopy kullanın. Daha fazla bilgi için bkz. [Windows üzerinde AzCopy ile veri aktarma](storage-use-azcopy.md) ve [Linux üzerinde AzCopy ile veri aktarma](storage-use-azcopy-linux.md).

**Günlükleri D sürücüsünden Azure Storage hesabımın her gün taşıma yapmam gerekir. Nasıl yaparım? otomatikleştirin?**

AzCopy 'i kullanabilir ve Görev Zamanlayıcı bir görev oluşturabilirsiniz. AzCopy toplu betiği kullanarak bir Azure depolama hesabına dosya yükleme. Daha fazla bilgi için bkz. [bulut hizmeti için başlangıç görevlerini yapılandırma ve çalıştırma](../../cloud-services/cloud-services-startup-tasks.md).

**Depolama hesabımı abonelikler arasında taşımak Nasıl yaparım? mı?**

Depolama hesabınızı abonelikler arasında taşımak için AzCopy kullanın. Daha fazla bilgi için bkz. [Windows üzerinde AzCopy ile veri aktarma](storage-use-azcopy.md) ve [Linux üzerinde AzCopy ile veri aktarma](storage-use-azcopy-linux.md).

**Farklı bir bölgedeki depolama alanına yaklaşık 10 TB veri nasıl taşıyabilirim?**

Verileri taşımak için AzCopy kullanın. Daha fazla bilgi için bkz. [Windows üzerinde AzCopy ile veri aktarma](storage-use-azcopy.md) ve [Linux üzerinde AzCopy ile veri aktarma](storage-use-azcopy-linux.md).

**Verileri Şirket içinden Azure depolama 'ya nasıl kopyalayabilirim?**

Verileri kopyalamak için AzCopy kullanın. Daha fazla bilgi için bkz. [Windows üzerinde AzCopy ile veri aktarma](storage-use-azcopy.md) ve [Linux üzerinde AzCopy ile veri aktarma](storage-use-azcopy-linux.md).

**Verileri Şirket içinden Azure dosyalarına nasıl taşıyabilirim?**

Verileri taşımak için AzCopy kullanın. Daha fazla bilgi için bkz. [Windows üzerinde AzCopy ile veri aktarma](storage-use-azcopy.md) ve [Linux üzerinde AzCopy ile veri aktarma](storage-use-azcopy-linux.md).

**Nasıl yaparım? sanal makinedeki bir kapsayıcı klasörü eşlenir mi?**

Azure dosya paylaşma kullanın.

**Azure dosya depolama Nasıl yaparım? yedekmi?**

Yedekleme çözümü yok. Bununla birlikte, Azure dosyaları da zaman uyumsuz kopyayı destekler. Bu nedenle, dosyaları kopyalayabilirsiniz:

- Bir paylaşımdan bir depolama hesabı içindeki başka bir paylaşıma veya farklı bir depolama hesabına.

- Bir paylaşımdan bir depolama hesabı içindeki blob kapsayıcısına veya farklı bir depolama hesabına.

Daha fazla bilgi için bkz. [Windows üzerinde AzCopy ile veri aktarma](storage-use-azcopy.md).

**Yönetilen diskleri başka bir depolama hesabına taşımak Nasıl yaparım? istiyor musunuz?**

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Şu adımları uygulayın:

1.  Yönetilen diskin bağlı olduğu sanal makineyi durdurun.

2.  Yönetilen disk VHD 'yi bir alandan diğerine kopyalamak için aşağıdaki Azure PowerShell betiği çalıştırın:

    ```
    Connect-AzAccount

    Select-AzSubscription -SubscriptionId <ID>

    $sas = Grant-AzDiskAccess -ResourceGroupName <RG name> -DiskName <Disk name> -DurationInSecond 3600 -Access Read

    $destContext = New-AzStorageContext –StorageAccountName contosostorageav1 -StorageAccountKey <your account key>

    Start-AzStorageBlobCopy -AbsoluteUri $sas.AccessSAS -DestContainer 'vhds' -DestContext $destContext -DestBlob 'MyDestinationBlobName.vhd'
    ```

3.  VHD dosyasını kopyaladığınız başka bir bölgede VHD dosyasını kullanarak yönetilen bir disk oluşturun. Bunu yapmak için aşağıdaki Azure PowerShell betiği çalıştırın:  

    ```
    $resourceGroupName = 'MDDemo'

    $diskName = 'contoso\_os\_disk1'

    $vhdUri = 'https://contoso.storageaccou.com.vhd

    $storageId = '/subscriptions/<ID>/resourceGroups/<RG name>/providers/Microsoft.Storage/storageAccounts/contosostorageaccount1'

    $location = 'westus'

    $storageType = 'StandardLRS'

    $diskConfig = New-AzDiskConfig -AccountType $storageType -Location $location -CreateOption Import -SourceUri $vhdUri -StorageAccountId $storageId -DiskSizeGB 128

    $osDisk = New-AzDisk -DiskName $diskName -Disk $diskConfig -ResourceGroupName $resourceGroupName
    ``` 

Bir sanal makinenin yönetilen bir diskten nasıl dağıtılacağı hakkında daha fazla bilgi için bkz. [Createvmfrommanagedosdisk. ps1](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/blob/master/CreateVmFromManagedOsDisk.ps1).

**Azure portal 1-2 TB veriyi nasıl indirebilirim?**

Verileri indirmek için AzCopy kullanın. Daha fazla bilgi için bkz. [Windows üzerinde AzCopy ile veri aktarma](storage-use-azcopy.md) ve [Linux üzerinde AzCopy ile veri aktarma](storage-use-azcopy-linux.md).

**Nasıl yaparım?, ikincil konumu bir depolama hesabı için Avrupa bölgesine değiştirmek istiyor musunuz?**

Bir depolama hesabı oluşturduğunuzda, hesabın birincil bölgesini seçersiniz. İkincil bölgenin seçimi birincil bölgeye dayalıdır ve değiştirilemez. Daha fazla bilgi için bkz [. coğrafi olarak yedekli depolama (GRS): Azure depolama](storage-redundancy.md)için çapraz bölgesel çoğaltma.

**Azure Depolama Hizmeti Şifrelemesi (SSE) hakkında daha fazla bilgiyi nereden edinebilirim?**  
  
Aşağıdaki makalelere bakın:

-  [Azure Depolama güvenlik kılavuzu](storage-security-guide.md)

-  [Bekleyen veriler için Azure Depolama Hizmeti Şifrelemesi](storage-service-encryption.md)

**Nasıl yaparım? bir depolama hesabından verileri taşımak veya indirmek mi istiyorsunuz?**

Verileri indirmek için AzCopy kullanın. Daha fazla bilgi için bkz. [Windows üzerinde AzCopy ile veri aktarma](storage-use-azcopy.md) ve [Linux üzerinde AzCopy ile veri aktarma](storage-use-azcopy-linux.md).


**Nasıl yaparım? bir depolama hesabındaki verileri şifreliyor musunuz?**

Bir depolama hesabında şifrelemeyi etkinleştirdikten sonra, mevcut veriler şifrelenmez. Mevcut verileri şifrelemek için depolama hesabına yeniden yüklemeniz gerekir.

AzCopy kullanarak verileri farklı bir depolama hesabına kopyalayın ve sonra verileri geri taşıyın. Ayrıca, [bekleyen şifreleme](storage-service-encryption.md)da kullanabilirsiniz.

**Bir VHD 'yi portaldaki indir seçeneğini kullanarak yerel bir makineye nasıl indirebilirim?**

Bir VHD 'YI indirmek için [Depolama Gezgini](https://azure.microsoft.com/features/storage-explorer/) kullanabilirsiniz.

**Bir depolama hesabının coğrafi olarak yedekli depolama alanından yerel olarak yedekli depolamaya çoğaltılmasını değiştirmek için herhangi bir önkoşul var mı?**

Hayır. 

**Azure dosyaları yedekli depolamaya erişmek Nasıl yaparım?.**

Yedekli depolamaya erişmek için Okuma Erişimli Coğrafi olarak yedekli depolama gereklidir. Ancak, Azure dosyaları yalnızca yerel olarak yedekli depolamayı ve salt okuma erişimine izin verilmeyen standart coğrafi olarak yedekli depolamayı destekler. 

**Nasıl yaparım? Premium Depolama hesabından standart depolama hesabına geçiş mi?**

Şu adımları uygulayın:

1.  Standart depolama hesabı oluşturun. (Veya aboneliğinizde mevcut bir standart depolama hesabı kullanın.)

2.  AzCopy indirin. Aşağıdaki AzCopy komutlarından birini çalıştırın.
      
    Depolama hesabındaki tüm diskleri kopyalamak için:

        AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1
        /Dest:https://destaccount.blob.core.windows.net/mycontainer2
        /SourceKey:key1 /DestKey:key2 /S 

    Yalnızca bir diski kopyalamak için, **Şu düzende diskin adını belirtin:**

        AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1
        /Dest:https://destaccount.blob.core.windows.net/mycontainer2
        /SourceKey:key1 /DestKey:key2 /Pattern:abc.vhd

   
İşlemin tamamlanması birkaç saat sürebilir.

Aktarımın başarıyla tamamlandığını doğrulamak için Azure portal hedef depolama hesabı kapsayıcısını inceleyin. Diskler standart depolama hesabına kopyalandıktan sonra, bunları sanal makineye mevcut bir disk olarak ekleyebilirsiniz. Daha fazla bilgi için, bkz. [Azure Portal bir Windows sanal makinesine yönetilen veri diski iliştirme](../../virtual-machines/windows/attach-managed-disk-portal.md).  
  
**Nasıl yaparım? bir dosya paylaşımının Azure Premium depolamasına dönüştürmek istiyor musunuz?**

Azure dosya paylaşımında Premium depolamaya izin verilmez.

**Standart depolama hesabından Premium depolama hesabına yükseltme Nasıl yaparım?. Premium Depolama hesabından standart depolama hesabına düşürme Nasıl yaparım? mı?**

Hedef depolama hesabını oluşturmanız, kaynak hesaptan hedef hesaba veri kopyalamanız ve ardından kaynak hesabını silmeniz gerekir. Verileri kopyalamak için AzCopy gibi bir araç kullanabilirsiniz.

Sanal makineleriniz varsa, depolama hesabı verilerini geçirmeden önce ek adımlar gerçekleştirmeniz gerekir. Daha fazla bilgi için bkz. [Azure Premium Depolama 'Ya geçiş (yönetilmeyen diskler)](storage-migration-to-premium-storage.md).

**Nasıl yaparım?, klasik bir depolama hesabından Azure Resource Manager depolama hesabına taşımak istiyor musunuz?**

**Move-AzStorageAccount** cmdlet 'ini kullanabilirsiniz. Bu cmdlet 'in birden çok adımı vardır (doğrulama, hazırlama, tamamlama). Bunu yapmadan önce taşımayı doğrulayabilirsiniz.

Sanal makineleriniz varsa, depolama hesabı verilerini geçirmeden önce ek adımlar gerçekleştirmeniz gerekir. Daha fazla bilgi için [Azure PowerShell kullanarak IaaS kaynaklarını klasik 'dan Azure Resource Manager geçirme](../..//virtual-machines/windows/migration-classic-resource-manager-ps.md)konusuna bakın.

**Azure Storage hesabından Linux tabanlı bir bilgisayara veri yüklemek veya Linux makinesinden veri yüklemek Nasıl yaparım?.**

Azure CLı kullanabilirsiniz.

- Tek bir blobu indir:

      azure storage blob download -k "<Account Key>" -a "<Storage Account Name>" --container "<Blob Container Name>" -b "<Remote File Name>" -d "<Local path where the file will be downloaded to>"

- Tek bir blobu karşıya yükle: 

      azure storage blob upload -k "<Account Key>" -a "<Storage Account Name>" --container "<Blob Container Name>" -f "<Local File Name>"

**Diğer kişilere depolama kaynaklarıma nasıl erişim izni verebilirim?**

Diğer kişilere depolama kaynaklarına erişim sağlamak için:

-   Bir kaynağa erişim sağlamak için paylaşılan erişim imzası (SAS) belirteci kullanın. 

-   Depolama hesabı için birincil veya ikincil anahtarı olan bir Kullanıcı sağlayın. Daha fazla bilgi için bkz. [depolama hesabınızı yönetme](storage-account-manage.md#access-keys).

-   Erişim ilkesini anonim erişime izin verecek şekilde değiştirin. Daha fazla bilgi için bkz. [kapsayıcılar ve bloblara anonim kullanıcı Izinleri verme](../blobs/storage-manage-access-to-resources.md#grant-anonymous-users-permissions-to-containers-and-blobs).

**AzCopy nereye yüklenir?**

-   AzCopy komutunu Microsoft Azure Depolama komut satırından eriştiğinizde **AzCopy**yazın. Komut satırı AzCopy ile birlikte yüklenir.

-   32 bitlik sürümü yüklediyseniz şu konumda bulunur: **%\\ProgramFiles (x86)% Microsoft SDK 'ları\\Azure\\AzCopy**.

-   64 bitlik sürümü yüklediyseniz şu konumda bulunur: **% ProgramFiles%\\Microsoft SDK 'ları\\\\Azure AzCopy**.

**Çoğaltılan depolama hesabı için (bölgesel olarak yedekli depolama, coğrafi olarak yedekli depolama veya Okuma Erişimli Coğrafi olarak yedekli depolama gibi), ikincil bölgede depolanan verilere nasıl erişebilirim?**

-   Bölgesel olarak yedekli depolama veya coğrafi olarak yedekli depolama kullanıyorsanız, bu bölgeye bir yük devretme işlemi başlatmadığınız takdirde ikincil bölgedeki verilere erişemezsiniz. Yük devretme işlemi hakkında daha fazla bilgi için bkz. [Azure Storage 'Da olağanüstü durum kurtarma ve depolama hesabı yük devretme (Önizleme)](storage-disaster-recovery-guidance.md).

-   Okuma Erişimli Coğrafi olarak yedekli depolama kullanıyorsanız, verileri istediğiniz zaman ikincil bölgeden erişebilirsiniz. Aşağıdaki yöntemlerden birini kullanın:  
      
    - **AzCopy**: İkincil uç noktaya erişmek için URL 'deki depolama hesabı adına **İkincil** ekleyin. Örneğin:  
     
      https://storageaccountname-secondary.blob.core.windows.net/vhds/BlobName.vhd

    - **SAS belirteci**: Uç noktadan veriye erişmek için bir SAS belirteci kullanın. Daha fazla bilgi için bkz. [paylaşılan erişim Imzalarını kullanma](storage-sas-overview.md).

**Depolama hesabımın bulunduğu HTTPS özel etki alanını kullanmak Nasıl yaparım? mı? Örneğin, "https:\//mystorageaccountname.blob.Core.Windows.net/images/image.gif" nasıl "https:\//www.contoso.com/images/image.gif" olarak görünirim?**

SSL Şu anda özel etki alanları olan depolama hesaplarında desteklenmemektedir.
Ancak HTTPS olmayan özel etki alanlarını kullanabilirsiniz. Daha fazla bilgi için bkz. [BLOB depolama uç noktanız için özel bir etki alanı adı yapılandırma](../blobs/storage-custom-domain-name.md).

**Nasıl yaparım?, bir depolama hesabındaki verilere erişmek için FTP 'yi kullanmak mı istiyorsunuz?**

Bir depolama hesabına doğrudan FTP kullanarak erişmenin bir yolu yoktur. Ancak, bir Azure sanal makinesini ayarlayabilir ve ardından sanal makineye bir FTP sunucusu yükleyebilirsiniz. FTP sunucu deposu dosyalarını bir Azure dosyaları paylaşımında ya da sanal makine tarafından kullanılabilen bir veri diskinde oluşturabilirsiniz.

Yalnızca Depolama Gezgini veya benzer bir uygulama kullanmak zorunda kalmadan verileri indirmek isterseniz, bir SAS belirteci kullanabilirsiniz. Daha fazla bilgi için bkz. [paylaşılan erişim Imzalarını kullanma](storage-sas-overview.md).

**Blob 'Ları bir depolama hesabından diğerine geçirmek Nasıl yaparım? istiyor musunuz?**

 Bunu, [BLOB geçiş betiğimizi](../scripts/storage-common-transfer-between-storage-accounts.md)kullanarak yapabilirsiniz.

## <a name="need-help-contact-support"></a>Yardım mı gerekiyor? Desteğe başvurun.

Yine de yardıma ihtiyacınız varsa sorununuzun hızla çözülmesini sağlamak için [desteğe başvurun](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
