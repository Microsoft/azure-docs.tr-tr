---
title: Azure'da genel VM hata kodları | Microsoft Docs
description: Bazı sağlamanıza ve azure'daki sanal makineleri yönetme karşılaşılan yaygın hata kodlarını anlama
services: virtual-machines
documentationcenter: ''
author: xujing-ms
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure
ms.date: 5/22/2017
ms.author: xujing
ms.openlocfilehash: 3b8c9c80c93430d8dc5a888742e4e4e96ba99400
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67695322"
---
# <a name="understand-common-error-messages-when-you-manage-virtual-machines-in-azure"></a>Azure sanal makineler'de yönetirken genel hata iletileri anlama

Bu makalede en yaygın hata kodları ve iletiler oluşturduğunuzda veya azure'da sanal makineler (VM'ler) yönetmek, karşılaşabileceğiniz bazılarını açıklar.

>[!NOTE]
> Bu sayfadaki geri bildirim veya aracılığıyla açıklamaları bırakabilirsiniz [Azure geri bildirim](https://feedback.azure.com/forums/216843-virtual-machines) #azerrormessage etiketi.

## <a name="error-response-format"></a>Hata yanıt biçimi 
Azure sanal makineler için hata yanıtı şu JSON biçimini kullanın:

```json
{
  "status": "status code",
  "error": {
    "code":"Top level error code",
    "message":"Top level error message",
    "details":[
     {
      "code":"Inner evel error code",
      "message":"Inner level error message"
     }
    ]
   }
}
```

Bir hata yanıtı, her zaman bir durum kodu ve bir hata nesnesi içerir. Her hata nesnesi, her zaman bir hata kodu ve bir ileti içerir. Şablon ile VM oluşturduysanız, hata nesnesi içeren bir iç hata kodları ve ileti düzeyi ayrıntıları bölümü de içerir. Normalde, hata iletisi en içteki düzeyini kök hatasıdır.


## <a name="common-virtual-machine-management-errors"></a>Genel sanal makine yönetim hataları

Bu bölümde, VM'ler yönetirken karşılaşabileceğiniz genel hata iletileri listelenir:

|  Hata Kodu  |  Hata İletisi  |  
|  :------| :-------------|  
|  AcquireDiskLeaseFailed  |  Diski oluşturulurken kira alınamadı '{0}' URI'sini içeren blob kullanılarak {1}. BLOB zaten kullanılıyor.  |  
|  AllocationFailed  |  Ayırma başarısız oldu. Lütfen VM boyutu veya VM sayısını azaltmayı deneyin, daha sonra yeniden deneyin veya bir farklı kullanılabilirlik kümesi veya farklı bir Azure konumuna dağıtmayı deneyin.  |  
|  AllocationFailed  |  VM ayırma, bir iç hata nedeniyle başarısız oldu. Lütfen daha sonra yeniden deneyin veya farklı bir konuma dağıtmayı deneyin.  |
|  ArtifactNotFound  |  VM uzantısı ile Yayımcı '{0}'ve türü'{1}'konumunda bulunamadı'{2}'.  |
|  ArtifactNotFound  |  Yayımcı uzantısıyla '{0}', türü '{1}', tür işleyicisi sürümü '{2}' uzantısı depoda bulunamadı.  |
|  ArtifactVersionNotFound  |  İstenen sürüm karşılayan hiçbir sürüm yapıt deposunda bulunamadı '{0}'.  |
|  ArtifactVersionNotFound  |  İstenen sürüm karşılayan hiçbir sürüm yapıt deposunda bulunamadı '{0}'için VM uzantısı ile Yayımcı'{1}'ve türü'{2}'.  |
|  AttachDiskWhileBeingDetached  |  Veri diski VM'ye bağlanamaz '{0}'için VM'{1}' disk şu anda ayrılmakta olduğu. Lütfen disk tamamen ayrılana kadar bekleyin ve işlemi yeniden deneyin.  |
|  BadRequest  |  Hizalanan ' kullanılabilirlik kümeleri, bu bölgede henüz desteklenmiyor.  |
|  BadRequest  |  Yönetilen kullanılabilirlik kümesi için blob tabanlı diskleri olan VM eklenmesi veya yönetilmeyen kullanılabilirlik kümesi için yönetilen disklerle bir VM eklenmesi desteklenmiyor. Lütfen 'managed' özelliği yönetilen disklerle bir VM eklemek için ayarlanmış bir kullanılabilirlik kümesi oluşturun.  |
|  BadRequest  |  Yönetilen diskler bu bölgede desteklenmez.  |
|  BadRequest  |  Desteklenmeyen işletim sistemi türü için işleyici başına birden çok Vmextension '{0}'. VMExtension '{1}'işleyicisiile'{2}' zaten eklenmiş veya belirtilen giriş.  |
|  BadRequest  |  İşlem '{0}'Kaynağında desteklenmiyor'{1}' yönetilen disklere sahip.  |
|  CertificateImproperlyFormatted  |  Parolanın JSON gösterimi alınan {0} düzgün biçimlendirilmiş bir PFX dosyası olmayan bir veri alanı içeriyor veya belirtilen parola PFX dosyasını doğru şekilde çözmüyor.  |
|  CertificateImproperlyFormatted  |  Alınan veri {0} JSON'a durumdan çıkarılamaz durumda değil.  |
|  Çakışma  |  Disk yeniden boyutlandırması, yalnızca bir VM oluşturulurken veya VM serbest bırakıldığında izin verilir.  |
|  ConflictingUserInput  |  Disk '{0}'eklenemiyor, disk zaten vm'inde olduğu gibi'{1}'.  |
|  ConflictingUserInput  |  Kaynak ve hedef kaynak grupları aynıdır.  |
|  ConflictingUserInput  |  Kaynak ve hedef depolama hesapları için disk {0} farklıdır.  |
|  ContainerAlreadyOnLease  |  Zaten var. bir kira URI'sini içeren blobu barındıran depolama kapsayıcısı üzerinde {0}.  |
|  CrossSubscriptionMoveWithKeyVaultResources  |  Kaynakları taşıma isteği, bir veya daha fazlası tarafından başvurulan KeyVault kaynakları içeriyor {0}s isteği. Bu çapraz abonelik taşıma şu anda desteklenmiyor. Lütfen KeyVault kaynak kimlikleri için hata ayrıntılarına bakın.  |
|  DiagnosticsOperationInternalError  |  VM tanılama profili işlenirken bir iç hata oluştu {0}.  |
|  DiskBlobAlreadyInUseByAnotherDisk  |  BLOB {0} VM'ine ait başka bir disk tarafından zaten kullanılıyor '{1}'. Disk başvuru bilgisi için blob meta verilerini inceleyebilirsiniz.  |
|  DiskBlobNotFound  |  URI'sine sahip VHD blobu bulunamıyor {0} disk için '{1}'.  |
|  DiskBlobNotFound  |  URI'sine sahip VHD blobu bulunamıyor {0}.  |
|  DiskEncryptionKeySecretMissingTags  |  {0} Gizli dizi yoksa {1} etiketler. Lütfen parolanın sürümünü güncelleştirin, gerekli etiketleri ekleyip yeniden deneyin.  |
|  DiskEncryptionKeySecretUnwrapFailed  |  Gizli dizisini sarmalamadan çıkarma {0} anahtarı kullanarak değer {1} başarısız oldu.  |
|  DiskImageNotReady  |  Disk görüntüsü {0} bulunduğu {1} durumu. Görüntü hazır olduğunda lütfen yeniden deneyin.  |
|  DiskPreparationError  |  VM diskleri hazırlanırken bir veya daha fazla hata oluştu. Ayrıntılar için disk örneği görünümüne bakın.  |
|  DiskProcessingError  |  VM, başarısız olan disklerle diğer disklere sahip olduğundan disk işleme işlemi durduruldu.  |
|  ImageBlobNotFound  |  URI'sine sahip VHD blobu bulunamıyor {0} disk için '{1}'.  |
|  ImageBlobNotFound  |  URI'sine sahip VHD blobu bulunamıyor {0}.  |
|  IncorrectDiskBlobType  |  Disk blobları yalnızca türü sayfa blobu türünde olabilir. BLOB {0} disk için '{1}' türü blok blobudur.  |
|  IncorrectDiskBlobType  |  Disk blobları yalnızca türü sayfa blobu türünde olabilir. BLOB {0} türünde '{1}'.  |
|  IncorrectImageBlobType  |  Disk blobları yalnızca türü sayfa blobu türünde olabilir. BLOB {0} disk için '{1}' türü blok blobudur.  |
|  IncorrectImageBlobType  |  Disk blobları yalnızca türü sayfa blobu türünde olabilir. BLOB {0} türünde '{1}'.  |
|  InternalOperationError  |  Depolama hesabı çözümlenemedi {0}. İşlem kaynağı ile aynı konumda bulunan depolama kaynağı sağlayıcısı aracılığıyla oluşturulduğundan emin olun.  |
|  InternalOperationError  |  {0} Hedef arama görevi başarısız oldu.  |
|  InternalOperationError  |  Hata oluştu VM'nin ağ profili doğrulanırken '{0}'.  |
|  InvalidAccountType  |  AccountType {0} geçersiz.  |
|  InvalidParameter  |  Parametresinin değeri {0} geçersiz.  |
|  InvalidParameter  |  Belirtilen yönetici parolasına izin verilmiyor.  |
|  InvalidParameter  |  "Sağlanan parola arasında olmalıdır {0}-{1} karakter uzunluğunda ve en az karşılamalıdır {2} şu parola karmaşıklık gereksinimlerinden biri: <ol><li> Bir büyük harf içerir</li><li>Bir küçük harf içerir</li><li>Sayısal hane içerir</li><li>Özel bir karakter içeriyor.</li></ol>  |
|  InvalidParameter  |  Belirtilen yönetici kullanıcı adı izin verilmez.  |
|  InvalidParameter  |  VM bir platform veya kullanıcı görüntüsünden oluşturulduysa var olan bir işletim sistemi diski VM'ye bağlanamaz.  |
|  InvalidParameter  |  Kapsayıcı adı {0} geçersiz. Kapsayıcı adları 3 ila 63 karakter uzunluğunda olmalıdır ve yalnızca küçük harf alfasayısal karakterler ve kısa çizgi içerebilir. Kısa çizgiden önce ve bir alfasayısal karakter.  |
|  InvalidParameter  |  Kapsayıcı adı {0} URL'de {1} geçersiz. Kapsayıcı adları 3 ila 63 karakter uzunluğunda olmalıdır ve yalnızca küçük harf alfasayısal karakterler ve kısa çizgi içerebilir. Kısa çizgiden önce ve bir alfasayısal karakter.  |
|  InvalidParameter  |  URL'sindeki blob adı {0} bir eğik çizgi içerir. Bu diskler için şu anda desteklenmiyor.  |
|  InvalidParameter  |  URI {0} doğru blob URI'si gibi görünmüyor.  |
|  InvalidParameter  |  Adlı disk '{0}' zaten aynı LUN'u kullanan: {1}.  |
|  InvalidParameter  |  Adlı disk '{0}' zaten mevcut.  |
|  InvalidParameter  |  Belirtilen görüntü başvurusunda zaten tanımlı olan bir disk için kullanıcı görüntüsü geçersiz kılmaları belirtilemez.  |
|  InvalidParameter  |  Adlı disk '{0}' VHD URL'si zaten kullanıyor {1}.  |
|  InvalidParameter  |  Belirtilen hata etki alanı sayısı {0} aralığında olmalıdır {1} için {2}.  |
|  InvalidParameter  |  Lisans türü {0} geçersiz. Geçerli lisans türleri şunlardır: Windows_Client ve Windows_Server, büyük küçük harfe duyarlı.  |
|  InvalidParameter  |  Linux konak adı aşamaz {0} karakter uzunluğunda veya şu karakterleri içeremez: {1}.  |
|  InvalidParameter  |  Ssh ortak anahtarları için hedef yol için varsayılan değer şu anda sınırlı {0} Linux sağlama aracısındaki bilinen bir sorun nedeniyle.  |
|  InvalidParameter  |  Bir diskin LUN {0} zaten mevcut.  |
|  InvalidParameter  |  Abonelik {0} abonelik isteğini eşleşmelidir {1} yönetilen disk kimliğindeki.  |
|  InvalidParameter  |  Osprofile özel verileri Base64 kodlaması ile maksimum uzunlukta olmalıdır {0} karakter.  |
|  InvalidParameter  |  URL'sindeki BLOB adı {0} ile bitmelidir '{1}' uzantısı.  |
|  InvalidParameter  |  {0}' geçerli bir yakalanmış VHD blob adı öneki değildir. Normal ifade geçerli bir ön eki eşleşen '{1}'.  |
|  InvalidParameter  |  VM Aracısı sağlanmamış halinde sertifikaları VM'nize eklenemez.  |
|  InvalidParameter  |  Bir diskin LUN {0} zaten mevcut.  |
|  InvalidParameter  |  VM oluşturulamıyor çünkü istenen boyuta {0} kullanılabilirlik kümesi şu anda ayrıldığı kümede kullanılabilir değil. Bulunan boyutlar: {1}. Yeniden boyutlandırma stratejisi, VM hakkında daha fazla https://aka.ms/azure-resizevm.  |
|  InvalidParameter  |  İstenen VM boyutu {0} geçerli bölgede kullanılamıyor. Geçerli bölgede kullanılabilir boyutlar: {1}. Her bir bölgede kullanılabilen VM boyutları hakkında daha fazla bilgi bulmak https://aka.ms/azure-regions.  |
|  InvalidParameter  |  İstenen VM boyutu {0} geçerli bölgede kullanılamıyor. Her bir bölgede kullanılabilen VM boyutları hakkında daha fazla bilgi bulmak https://aka.ms/azure-regions.  |
|  InvalidParameter  |  Windows yönetici kullanıcı adı olamaz daha fazla {0} karakter uzunluğunda, ile bitemez veya şu karakterleri içeremez: {1}.  |
|  InvalidParameter  |  Windows bilgisayar adı olamaz daha fazla {0} karakterden uzun, tamamen rakamlardan oluşamaz veya şu karakterleri içeremez: {1}.  |
|  MissingMoveDependentResources  |  Kaynakları taşıma isteği tüm bağımlı kaynakları içermiyor. Eksik kaynak kimlikleri için hata ayrıntılarını gözden geçirin.  |
|  MoveResourcesHaveInvalidState  |  Kaynakları taşıma isteği geçersiz depolama hesapları ile ilişkili olan VM'ler içeriyor. Lütfen bu kaynak kimlikleri ve başvurulan depolama hesabı adları için ayrıntıları kontrol edin.  |
|  MoveResourcesHavePendingOperations  |  Kaynakları taşıma isteği, bir işlem beklemede kaynakları içerir. Lütfen bu kaynak kimliklerinin ayrıntılarını kontrol edin. Bekleyen işlemler tamamlandıktan sonra işleminizi yeniden deneyin.  |
|  MoveResourcesNotFound  |  Kaynakları taşıma isteği bulunamayan kaynaklar içeriyor. Lütfen bu kaynak kimliklerinin ayrıntılarını kontrol edin.  |
|  NetworkingInternalOperationError  |  Bilinmeyen ağ ayırma hatası.  |
|  NetworkingInternalOperationError  |  Bilinmeyen ağ ayırma hatası  |
|  NetworkingInternalOperationError  |  VM'nin ağ profili işlenirken bir iç hata oluştu.  |
|  NotFound  |  Kullanılabilirlik kümesi {0} bulunamıyor.  |
|  NotFound  |  Kaynak sanal makine '{0}' içinde belirtilen istek bu Azure konumunda yok.  |
|  NotFound  |  Kiracı kimliği ile {0} nebyl nalezen.  |
|  NotFound  |  Görüntü {0} bulunamıyor.  |
|  NotSupported  |  Lisans türü {0}, ancak görüntü blob'u {1} şirket içinden değil.  |
|  OperationNotAllowed  |  Kullanılabilirlik kümesi {0} silinemiyor. Bir kullanılabilirlik kümesi silmeden önce lütfen herhangi bir VM içermediğinden emin olun.  |
|  OperationNotAllowed  |  'Hizalı' SKU 'Klasik' kullanılabilirlik kümesi değiştirilmesine izin verilmez.  |
|  OperationNotAllowed  |  VM'nin çalışır durumda değilken VM uzantıları değiştirilemez.  |
|  OperationNotAllowed  |  Yakalama eylemi yalnızca blob tabanlı diskleri olan bir sanal makinede desteklenir. Lütfen yönetilen bir sanal makineden bir görüntü oluşturmak için 'Image' kaynağı API'lerini kullanın.  |
|  OperationNotAllowed  |  Kaynak {0} görüntüden oluşturulan {1} kadar görüntü başarıyla oluşturuldu.  |
|  OperationNotAllowed  |  EncryptionSettings güncelleştirmeleri verilmez VM ayrılır, VM serbest sonra lütfen yeniden deneyin  |
|  OperationNotAllowed  |  Blob tabanlı diskleri olan bir VM'ye yönetilen disk eklenmesi desteklenmez.  |
|  OperationNotAllowed  |  Veri disklerinin bu boyuttaki bir VM'ye bağlı izin verilen en büyük sayı {0}.  |
|  OperationNotAllowed  |  Yönetilen disklere sahip sanal makine için blob tabanlı disk eklenmesi desteklenmez.  |
|  OperationNotAllowed  |  İşlem '{0}'Görüntüye izin verilmez'{1}' görüntüsü silinmek üzere işaretlendiğinden. Yalnızca silme işlemini yeniden deneyin (veya devam eden bir tamamlanmasını bekleyin).  |
|  OperationNotAllowed  |  İşlem '{0}'VM'inde izin verilmez'{1}' VM genelleştirilmiş olduğundan.  |
|  OperationNotAllowed  |  İşlem '{0}'geri yükleme noktası koleksiyonu izin verilmez'{1}' silinmek üzere işaretlenmiş.  |
|  OperationNotAllowed  |  İşlem '{0}'VM uzantısı izin verilmez'{1}' silinmek üzere işaretlendiğinden. Yalnızca silme işlemini yeniden deneyin (veya devam eden bir tamamlanmasını bekleyin).  |
|  OperationNotAllowed  |  İşlem '{0}'Sanal makineleri izin verilmez'{1}'görüntüsü kullanılarak sağlandığından'{2}'.  |
|  OperationNotAllowed  |  İşlem '{0}'Sanal makine Scaleset'i beri izin verilmez'{1}'şu anda görüntü kullanıyor'{2}'.  |
|  OperationNotAllowed  |  İşlem '{0}'VM'inde izin verilmez'{1}' VM silme için işaretlendiğinden. Yalnızca silme işlemini yeniden deneyin (veya devam eden bir tamamlanmasını bekleyin).  |
|  OperationNotAllowed  |  İşlem '{0}'VM'inde izin verilmez'{1}' VM serbest veya serbest bırakılmak üzere işaretlendiği olduğundan.  |
|  OperationNotAllowed  |  İşlem '{0}'VM'inde izin verilmez'{1}' VM'nin çalışır durumda olduğundan. VM'yi konuk işletim sisteminin içinden kapatmanız durumunda güç kapalı açıkça Lütfen.  |
|  OperationNotAllowed  |  İşlem '{0}'VM'inde izin verilmez'{1}' VM serbest bırakılmadığı olduğundan.  |
|  OperationNotAllowed  |  İşlem '{0}'VM'inde izin verilmez'{1}'VM uzantısı olduğundan'{2}' başarısız durumda.  |
|  OperationNotAllowed  |  İşlem '{0}'VM'inde izin verilmez'{1}' başka bir işlem sürmekte olduğundan.  |
|  OperationNotAllowed  |  İşlem '{0}'Sanal makine gerektiriyor'{1}' genelleştirilmesini.  |
|  OperationNotAllowed  |  İşlem, VM'nin çalışıyor (veya çalıştırmayı ayarlamak) gerektiriyor.  |
|  OperationNotAllowed  |  Disk boyutu olan {0}boyutundan daha küçük olan GB {1}GB görüntüde, karşılık gelen disk izin verilmiyor.  |
|  OperationNotAllowed  |  VM ölçek kümesi uzantıları işleyicisinin '{0}' yalnızca VM ölçek kümesi oluşturma sırasında eklenebilir.  |
|  OperationNotAllowed  |  VM ölçek kümesi uzantıları işleyicisinin '{0}' yalnızca VM ölçek kümesi silme işlemi sırasında silinebilir.  |
|  OperationNotAllowed  |  VM '{0}' zaten yönetilen disk kullanıyor.  |
|  OperationNotAllowed  |  VM '{0}'' Klasik' kullanılabilirlik kümesine ait olan'{1}'. Kullanılabilirlik kümesini 'Hizalı' SKU kullanacak ve sonra dönüştürme işlemini yeniden deneyin. Lütfen güncelleştirin.  |
|  OperationNotAllowed  |  Görüntüden oluşturulan VM, blob tabanlı disklere sahip olamaz. Tüm diskler yönetilen disk olması gerekir.  |
|  OperationNotAllowed  |  Yakalama VM genelleştirilmiş olmadığından işlem tamamlanamıyor.  |
|  OperationNotAllowed  |  VM üzerinde yönetim işlemlerine '{0}' VM diskleri yönetilen disklere dönüştürüldüğü için izin verilmiyor.  |
|  OperationNotAllowed  |  Devam eden bir işlem, sanal makinenin güç durumunu değiştirmeyi {0} için {1}. Lütfen işlemi {2} bir süre sonra.  |
|  OperationNotAllowed  |  Eklenemiyor veya VM'yi güncelleştirin. İstenen VM boyutu {0} mevcut ayırma biriminde kullanılamıyor olabilir. Yeniden boyutlandırma stratejisi, VM hakkında daha fazla https://aka.ms/azure-resizevm.  |
|  OperationNotAllowed  |  Alınamıyor çünkü sanal makine yeniden boyutlandırmak istenen boyut {0} kullanılabilirlik kümesi şu anda ayrıldığı kümede kullanılabilir değil. Bulunan boyutlar: {1}. Yeniden boyutlandırma stratejisi, VM hakkında daha fazla https://aka.ms/azure-resizevm.  |
|  OperationNotAllowed  |  Alınamıyor çünkü sanal makine yeniden boyutlandırmak istenen boyut {0} VM şu anda ayrıldığı kümede kullanılabilir değil. Sanal makinenizde yeniden boyutlandırmak için {1} Lütfen serbest (Bu, Azure portalında durdurma işlemi) ve yeniden boyutlandırma işlemi yeniden deneyin. Yeniden boyutlandırma stratejisi, VM hakkında daha fazla https://aka.ms/azure-resizevm.  |
|  OSProvisioningClientError  |  VM için işletim sistemi sağlama başarısız '{0}' olduğundan ' % s'konuk işletim sistemi şu anda sağlanıyor.  |
|  OSProvisioningClientError  |  VM için işletim sistemi sağlama '{0}' başarısız oldu. Hata ayrıntıları: {1} Görüntünün düzgün hazırlandığından emin olun (genelleştirildiğinden). <ul><li>Windows için yönergeler: https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/  </li></ul> |
|  OSProvisioningClientError  |  SSH ana bilgisayar anahtarı oluşturma başarısız oldu. Hata ayrıntıları: {0}. Gidermek için bu sorunu doğrulayın Linux Aracısı düzgün şekilde ayarlanmış olup. <ul><li>Bölümündeki yönergeleri denetleyebilirsiniz: https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux/ </li></ul> |
|  OSProvisioningClientError  |  Bu Linux dağıtımı için bir VM için belirtilen kullanıcı adı geçersiz. Hata ayrıntıları: {0}.  |
|  OSProvisioningInternalError  |  VM için işletim sistemi sağlama başarısız '{0}' bir iç hata nedeniyle.  |
|  OSProvisioningTimedOut  |  VM için işletim sistemi sağlama '{0}' ayrılan sürede tamamlanmadı. VM, yine de sağlamayı başarıyla tamamlayabilir son. Lütfen sağlama durumunu daha sonra denetleyin.  |
|  OSProvisioningTimedOut  |  VM için işletim sistemi sağlama '{0}' ayrılan sürede tamamlanmadı. VM, yine de sağlamayı başarıyla tamamlayabilir son. Lütfen sağlama durumunu daha sonra denetleyin. Ayrıca, görüntünün düzgün hazırlandığından emin olun (genelleştirildiğinden).   <ul><li>Windows için yönergeler: https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/ </li><li> Linux için yönergeler: https://azure.microsoft.com/documentation/articles/virtual-machines-linux-capture-image/</li></ul>  |
|  OSProvisioningTimedOut  |  VM için işletim sistemi sağlama '{0}' ayrılan sürede tamamlanmadı. Ancak, VM Konuk Aracısı çalıştıran algılandı. Bu konuk işletim Sisteminin VM görüntüsü olarak kullanılacak doğru hazırlanmış çalıştırılmadı önerir (Fromımage ile CreateOption =). Bu sorunu çözmek için VHD'yi CreateOption ile olduğu gibi kullanın Ekle = ya da bir görüntü olarak kullanmak için düzgün şekilde hazırlayın:   <ul><li>Windows için yönergeler: https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/ </li><li> Linux için yönergeler: https://azure.microsoft.com/documentation/articles/virtual-machines-linux-capture-image/</li></ul>  |
|  OverConstrainedAllocationRequest  |  Gerekli VM boyutu seçilen konumda şu anda kullanılabilir değil.  |
|  ResourceUpdateBlockedOnPlatformUpdate  |  Şu anda devam eden platform güncelleştirmesi nedeniyle kaynak güncelleştirilemiyor. Lütfen daha sonra tekrar deneyin.  |
|  StorageAccountLimitation  |  Depolama hesabı '{0}', disk oluşturmak için gerekli sayfa bloblarını desteklemez.  |
|  StorageAccountLimitation  |  Depolama hesabı '{0}', ayrılmış kotasını aştı.  |
|  StorageAccountLocationMismatch  |  Depolama hesabı çözümlenemedi {0}. İşlem kaynağı ile aynı konumda bulunan depolama kaynağı sağlayıcısı aracılığıyla oluşturulduğundan emin olun.  |
|  StorageAccountNotFound  |  Depolama hesabı {0} nebyl nalezen. Depolama hesabının silinmediğinden ve VM ile aynı Azure konumuna ait emin olun.  |
|  StorageAccountNotRecognized  |  Lütfen depolama kaynak sağlayıcısı tarafından yönetilen bir depolama hesabı kullanın. Kullanım {0} desteklenmiyor.  |
|  StorageAccountOperationInternalError  |  İç hata oluştu. depolama hesabına erişilirken {0}.  |
|  StorageAccountSubscriptionMismatch  |  Depolama hesabı {0} aboneliğine ait değil {1}.  |
|  StorageAccountTooBusy  |  Depolama hesabı '{0}' şu anda çok meşgul. Başka bir hesabı kullanmayı göz önünde bulundurun.  |
|  StorageAccountTypeNotSupported  |  Disk {0} kullanan {1} Blob Depolama hesabı olduğu. Lütfen genel amaçlı depolama hesabıyla yeniden deneyin.  |
|  StorageAccountTypeNotSupported  |  Depolama hesabı {0} değil {1} türü. Önyükleme tanılama destekler {2} depolama hesabı türleri.  <ul><li>Önyükleme tanılaması için premium depolama hesabı kullanıyorsanız bu hata oluşur. Daha fazla bilgi için [önyükleme tanılama kullanma](boot-diagnostics.md). </li></ul> |
|  SubscriptionNotAuthorizedForImage  |  Abonelik yetkisi yok.  |
|  TargetDiskBlobAlreadyExists  |  BLOB {0} zaten mevcut. Lütfen farklı bir blob URI'si yeni boş veri diski oluşturmak için sağlayın '{1}'.  |
|  TargetDiskBlobAlreadyExists  |  Yakalama işlemine devam edilemiyor çünkü hedef görüntü blob {0} zaten var ve VHD bloblarının üzerine yazmayı bayrağı ayarlı değil. Blob silme ya da VHD bloblarının üzerine yazmayı ve yeniden denemek için bayrağı ayarlayın.  |
|  TargetDiskBlobAlreadyExists  |  Yakalama işlemine devam edilemiyor çünkü hedef görüntü blob {0} üzerinde etkin bir kiralama sahiptir.   |
|  TargetDiskBlobAlreadyExists  |  BLOB {0} zaten mevcut. Lütfen farklı bir blob URI'si diski için hedef olarak sağlayın. '{1}'.  |
|  TooManyVMRedeploymentRequests  |  VM için çok fazla yeniden dağıtım isteği alındı '{0}' veya bu VM ile aynı kullanılabilirlik kümesinde VM'ler. Lütfen daha sonra yeniden deneyin.  |
|  VHDSizeInvalid  |  Belirtilen disk boyutu değeri {0} disk için '{1}' blob ile {2} geçersiz. Disk boyutu arasında olmalıdır {3} ve {4}.  |
|  VMAgentStatusCommunicationError  |  VM '{0}' VM aracısının veya uzantılarının durumunu bildirmedi. Lütfen VM çalıştıran bir VM Aracısı olduğunu ve Azure depolamaya giden bağlantılar kurabilirsiniz doğrulayın.  |
|  VMArtifactRepositoryInternalError  |  VM yapıt ayrıntıları almak için yapıt deposu ile iletişim kurulurken bir hata oluştu.  |
|  VMArtifactRepositoryInternalError  |  Yapıt deposundan VM yapıt verileri alınırken bir iç hata oluştu.  |
|  VMExtensionHandlerNonTransientError  |  İşleyici '{0}'VM uzantısı için hata bildirdi'{1}'terminal hata koduyla'{2}' ve hata iletisi: '{3}'  |
|  VMExtensionManagementInternalError  |  İç hata oluştu. VM uzantısı işlenirken '{0}'.  |
|  VMExtensionManagementInternalError  |  VM uzantıları hazırlanırken birden çok hata oluştu. Ayrıntılar için VM uzantısı örnek görünümüne bakın.  |
|  VMExtensionProvisioningError  |  VM uzantısı işlenirken bir hata bildirdi '{0}'. Hata iletisi: "{1}".  |
|  VMExtensionProvisioningError  |  Birden çok VM uzantısının VM sağlanacağı başarısız oldu. Lütfen ayrıntılar için VM uzantısı örnek görünümüne bakın.  |
|  VMExtensionProvisioningTimeout  |  VM uzantısının sağlama '{0}' zaman aşımına uğradı. Uzantı yükleme çok uzun sürüyor ya da uzantı durumu alınamadı.  |
|  Vmmarketplaceınvalidınput  |  Olmayan Market görüntüsünden sanal makine oluşturulurken gerekmez bilgi planlayın, lütfen Plan bilgisini istekte kaldırın. İşletim sistemi diski adı {0}.  |
|  Vmmarketplaceınvalidınput  |  Satın alma bilgileri eşleşmiyor. Market görüntüsünden dağıtım yapılamıyor. İşletim sistemi diski adı {0}.  |
|  Vmmarketplaceınvalidınput  |  Market görüntüsünden sanal makine oluşturulurken Plan bilgisi istek gerektirir. İşletim sistemi diski adı {0}.  |
|  VMNotFound  |  VM '{0}' bulunamıyor.  |
|  VMRedeploymentFailed  |  VM '{0}' yeniden dağıtma işlemi bir iç hata nedeniyle başarısız oldu. Lütfen daha sonra yeniden deneyin.  |
|  VMRedeploymentTimedOut  |  VM'nin yeniden dağıtım '{0}' ayrılan sürede bitmedi. Başarıyla içindeki süre bitirmek. Aksi takdirde isteği yeniden deneyebilirsiniz.  |
|  VMStartTimedOut  |  VM '{0}' ayrılan sürede başlatılmadı. VM'nin yine de başarıyla başlatılabilir. Lütfen daha sonra güç durumunu denetleyin.  |


## <a name="next-steps"></a>Sonraki adımlar
Daha fazla yardıma ihtiyacınız olursa, üzerinde Azure uzmanlarıyla iletişime geçebilirsiniz [Azure MSDN ve Stack Overflow forumları](https://azure.microsoft.com/support/forums/). Alternatif olarak, bir Azure destek olayına dosya. Git [Azure Destek sitesi](https://azure.microsoft.com/support/options/) seçip **alma desteği**.