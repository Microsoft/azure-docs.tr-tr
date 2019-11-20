---
title: İşlem düğümlerine uygulama paketleri yükler-Azure Batch | Microsoft Docs
description: Toplu işlem düğümlerinde yüklenmek üzere birden çok uygulamayı ve sürümü kolayca yönetmek için Azure Batch uygulama paketleri özelliğini kullanın.
services: batch
documentationcenter: .net
author: laurenhughes
manager: gwallace
editor: ''
ms.assetid: 3b6044b7-5f65-4a27-9d43-71e1863d16cf
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/26/2019
ms.author: lahugh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9c9d6d13efaa07bff2a1eaabe05725a3257cf895
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/21/2019
ms.locfileid: "70095687"
---
# <a name="deploy-applications-to-compute-nodes-with-batch-application-packages"></a>Batch uygulama paketleriyle işlem düğümlerine uygulama dağıtma

Azure Batch uygulama paketleri özelliği, görev uygulamalarının ve bunların havuzunuzdaki işlem düğümlerine dağıtımını kolay bir şekilde yönetme olanağı sağlar. Uygulama paketleriyle, görevlerinizin çalıştırdığı uygulamaların birden çok sürümünü, destekleyici dosyaları da dahil olmak üzere karşıya yükleyebilir ve yönetebilirsiniz. Daha sonra bu uygulamalardan bir veya daha fazlasını havuzunuzdaki işlem düğümlerine otomatik olarak dağıtabilirsiniz.

Bu makalede, Azure portal uygulama paketlerini karşıya yüklemeyi ve yönetmeyi öğreneceksiniz. Ardından, [Batch .net][api_net] kitaplığıyla bir havuzun işlem düğümlerine nasıl yükleneceğini öğreneceksiniz.

> [!NOTE]
> Uygulama paketleri 5 Temmuz 2017’den sonra oluşturulmuş tüm Batch havuzlarında desteklenir. Bunların 10 Mart 2016 ve 5 Haziran 2017 arasında oluşturulmuş Batch havuzlarında desteklenebilmesi için, havuzun Bulut Hizmeti yapılandırması kullanılarak oluşturulmuş olması gerekir. 10 Mart 2016’dan önce oluşturulan Batch havuzları uygulama paketlerini desteklemez.
>
> Uygulama paketleri oluşturmak ve yönetmek için API 'Ler [Batch yönetimi .net][api_net_mgmt] kitaplığı 'nın bir parçasıdır. Uygulama paketlerini bir işlem düğümüne yüklemek için API 'Ler [Batch .net][api_net] kitaplığı 'nın bir parçasıdır. Benzer özellikler, diğer diller için kullanılabilir Batch API 'Lerinde bulunur. 
>
> Burada açıklanan uygulama paketleri özelliği, hizmetin önceki sürümlerinde bulunan Batch Apps özelliğinin yerini alır.

## <a name="application-package-requirements"></a>Uygulama paketi gereksinimleri
Uygulama paketlerini kullanmak için, Batch hesabınıza [bir Azure depolama hesabı bağlamanız](#link-a-storage-account) gerekir.

## <a name="about-applications-and-application-packages"></a>Uygulamalar ve uygulama paketleri hakkında
Azure Batch içinde bir *uygulama* , havuzunuzdaki işlem düğümlerine otomatik olarak indirilebilen bir sürümlü ikili dosyalar kümesine başvurur. Bir *uygulama paketi* , bu ikili dosyaların *belirli bir kümesini* gösterir ve uygulamanın belirli bir *sürümünü* temsil eder.

![Uygulamalar ve uygulama paketlerinin üst düzey diyagramı][1]

### <a name="applications"></a>Uygulamalar
Batch 'deki bir uygulama bir veya daha fazla uygulama paketi içerir ve uygulama için yapılandırma seçeneklerini belirtir. Örneğin, bir uygulama, işlem düğümlerine yüklenecek varsayılan uygulama paketi sürümünü ve paketlerin güncelleştirilip güncelleştirimeyeceğini veya silinemeyeceğini belirtebilir.

### <a name="application-packages"></a>Uygulama paketleri
Uygulama paketi, uygulama ikililerini içeren bir. zip dosyasıdır ve görevleriniz uygulamayı çalıştırmak için gerekli dosyaları destekler. Her uygulama paketi, uygulamanın belirli bir sürümünü temsil eder.

Uygulama paketlerini havuz ve görev düzeylerinde belirtebilirsiniz. Bir havuz veya görev oluşturduğunuzda, bu paketlerden bir veya daha fazlasını ve (isteğe bağlı olarak) bir sürümü belirtebilirsiniz.

* **Havuz uygulama paketleri** havuzdaki *her* düğüme dağıtılır. Uygulamalar, bir düğüm bir havuza katıldığında ve yeniden başlatıldığında veya yeniden görüntülendiğinde dağıtılır.
  
    Havuz uygulama paketleri, bir havuzdaki tüm düğümler bir işin görevlerini yürütür. Bir havuz oluşturduğunuzda bir veya daha fazla uygulama paketi belirtebilir ve var olan bir havuzun paketlerini ekleyebilir veya güncelleştirebilirsiniz. Mevcut bir havuzun uygulama paketlerini güncelleştirirseniz, yeni paketi yüklemek için düğümlerini yeniden başlatmanız gerekir.
* Görev **uygulama paketleri** , görevin komut satırı çalıştırılmadan hemen önce yalnızca bir görevi çalıştırmak üzere zamanlanan bir işlem düğümüne dağıtılır. Belirtilen uygulama paketi ve sürümü zaten düğümde ise, yeniden dağıtılmadı ve mevcut paket kullanılır.
  
    Görev uygulama paketleri, farklı işlerin tek bir havuzda çalıştırıldığı ve bir iş tamamlandığında havuzun silinmediği paylaşılan havuz ortamlarında yararlıdır. İşinizin havuzdaki görevleri, düğümlerinden azsa uygulamanız yalnızca görevleri çalıştıran düğümlere dağıtıldığı için görev uygulama paketleri veri aktarımını azaltabilir.
  
    Görev uygulama paketlerinden faydalanabilir diğer senaryolar, büyük bir uygulamayı çalıştıran, ancak yalnızca birkaç görevde çalışan işlerdir. Örneğin, ön işleme veya birleştirme uygulamasının ağır olduğu bir ön işleme aşaması veya birleştirme görevi, görev uygulama paketlerinin kullanılması yararlı olabilir.

> [!IMPORTANT]
> Bir Batch hesabı içindeki uygulama ve uygulama paketlerinin sayısı ve en fazla uygulama paketi boyutu için kısıtlamalar vardır. Bu limitlerin ayrıntıları için [Azure Batch hizmetine yönelik kotalar ve sınırlar](batch-quota-limit.md) bölümüne bakın.
> 
> 

### <a name="benefits-of-application-packages"></a>Uygulama paketlerinin avantajları
Uygulama paketleri, Batch çözümünüzdeki kodu basitleştirebilir ve görevlerinizin çalıştırdığı uygulamaları yönetmek için gereken ek yükü düşürebilirler.

Uygulama paketleriyle, havuzunuzun başlangıç görevinin düğümlere yüklenecek tek kaynak dosyalarının uzun bir listesini belirtmek zorunda değildir. Azure depolama 'da veya düğümlerdeki uygulama dosyalarınızın birden çok sürümünü el ile yönetmeniz gerekmez. Ve Depolama hesabınızdaki dosyalara erişim sağlamak için [SAS URL 'leri](../storage/common/storage-dotnet-shared-access-signature-part-1.md) oluşturma konusunda endişelenmeniz gerekmez. Batch, uygulama paketlerini depolamak ve bunları işlem düğümlerine dağıtmak için Azure depolama ile arka planda çalışmaktadır.

> [!NOTE] 
> Bir başlangıç görevinin toplam boyutunun kaynak dosyaları ve ortam değişkenleri dahil olmak üzere 32.768 karakter veya daha az olması gerekir. Başlangıç göreviniz bu sınırı aşarsa, uygulama paketlerinin kullanılması başka bir seçenektir. Ayrıca, kaynak dosyalarınızı içeren daraltılmış bir arşiv oluşturabilir, Azure depolama 'ya blob olarak yükleyebilir ve sonra başlangıç görevinin komut satırından bu dosyayı indirebilirsiniz. 
>
>

## <a name="upload-and-manage-applications"></a>Uygulamaları karşıya yükleme ve yönetme
Batch hesabınızdaki uygulama paketlerini yönetmek için [Azure Portal][portal] veya Batch yönetimi API 'lerini kullanabilirsiniz. Sonraki birkaç bölümde, önce bir depolama hesabını bağlamayı, ardından uygulama ve paket ekleme ve bunları portalla yönetme hakkında tartışın.

### <a name="link-a-storage-account"></a>Depolama hesabını bağlama
Uygulama paketlerini kullanmak için önce Batch hesabınıza bir [Azure depolama hesabı](batch-api-basics.md#azure-storage-account) bağlamanız gerekir. Henüz bir depolama hesabı yapılandırmadıysanız, Batch hesabınızdaki **uygulamalar** ilk kez tıkladığınızda Azure Portal bir uyarı görüntüler.



![Azure portal 'de ' depolama hesabı Yapılandırılmadı ' uyarısı][9]

Batch hizmeti, uygulama paketlerinizi depolamak için ilişkili depolama hesabını kullanır. İki hesabı bağladıktan sonra toplu Işlem, bağlantılı depolama hesabında depolanan paketleri işlem düğümlerine otomatik olarak dağıtabilir. Bir depolama hesabını Batch hesabınıza bağlamak için, **Uyarı** penceresinde **depolama hesabı** ' na tıklayın ve ardından **depolama hesabı** ' na tıklayın.

![Azure portal depolama hesabı dikey penceresini seçin][10]

*Özel olarak* Batch hesabınızla birlikte kullanmak Için bir depolama hesabı oluşturmanızı ve burada seçmeniz önerilir. Bir depolama hesabı oluşturduktan sonra **depolama** hesabı penceresini kullanarak bunu Batch hesabınıza bağlayabilirsiniz.

> [!NOTE] 
> Şu anda uygulama paketlerini [güvenlik duvarı kurallarıyla](../storage/common/storage-network-security.md)yapılandırılmış bir Azure depolama hesabıyla kullanamazsınız.
> 

Batch hizmeti, uygulama paketlerinizi blok blob 'ları olarak depolamak için Azure Storage 'ı kullanır. Blok Blobu verileri için [normal olarak ücretlendirilirsiniz][storage_pricing] ve her bir paketin boyutu, [Blok Blobu boyutunun üst sınırını](../storage/common/storage-scalability-targets.md#azure-blob-storage-scale-targets)aşamaz. Uygulama paketlerinizin boyutunu ve sayısını göz önünde bulundurmanız ve maliyetleri en aza indirmek için kullanım dışı bırakılan paketleri düzenli olarak kaldırmayı unutmayın.
> 
> 

### <a name="view-current-applications"></a>Geçerli uygulamaları görüntüle
Batch hesabınızdaki uygulamaları görüntülemek için, **Batch hesabınızı**görüntülerken sol menüdeki **uygulamalar** menü öğesine tıklayın.

![Uygulamalar bölmesi][2]

Bu menü seçeneği belirlendiğinde **uygulamalar** penceresi açılır:

![Uygulamaları listeleme][3]

Bu pencere, hesabınızdaki her uygulamanın KIMLIĞINI ve aşağıdaki özellikleri görüntüler:

* **Paketler**: Bu uygulamayla ilişkili sürümlerin sayısı.
* **Varsayılan sürüm**: bir havuz için uygulama belirttiğinizde bir sürüm belirtmezseniz, uygulamanın sürümü yüklenir. Bu ayar isteğe bağlıdır.
* **Güncelleştirmelere Izin ver**: paket güncelleştirme, silme ve eklemelere izin verilip verilmeyeceğini belirten değer. **Hayır**olarak ayarlanırsa, uygulama için paket güncelleştirmeleri ve silmeleri devre dışı bırakılır. Yalnızca yeni uygulama paketi sürümleri eklenebilir. Varsayılan değer **Evet**’tir.

İşlem düğümünüz üzerinde uygulama paketinin dosya yapısını görmek isterseniz, portalda Batch hesabınıza gidin. Batch hesabınızdan **havuzlar**' a gidin. İlgilendiğiniz işlem düğümlerini içeren havuzu seçin.

![Havuzdaki düğümler][13]

Havuzunuzu seçtikten sonra, uygulama paketinin yüklü olduğu işlem düğümüne gidin. Buradan, uygulama paketinin ayrıntıları **uygulamalar** klasöründe bulunur. İşlem düğümündeki ek klasörler, başlangıç görevleri, çıkış dosyaları, hata çıktısı vb. gibi diğer dosyaları içerir.

![Düğümdeki dosyalar][14]

### <a name="view-application-details"></a>Uygulama ayrıntılarını görüntüle
Bir uygulamanın ayrıntılarını görmek için **uygulamalar** penceresinde uygulamayı seçin.

![Uygulama ayrıntıları][4]

Uygulama ayrıntılarında, uygulamanız için aşağıdaki ayarları yapılandırabilirsiniz.

* **Güncelleştirmelere Izin ver**: uygulama paketlerinin güncelleştirilip güncelleştirilmediğini veya silinemeyeceğini belirtin. Bu makalenin devamındaki "uygulama paketini güncelleştirme veya silme" bölümüne bakın.
* **Varsayılan sürüm**: işlem düğümlerine dağıtılacak bir varsayılan uygulama paketi belirtin.
* **Görünen ad**: Batch çözümünüzün uygulamayla ilgili bilgileri (örneğin, toplu iş aracılığıyla müşterilerinize sağladığınız bir hizmetin Kullanıcı arabiriminde) görüntülediği kolay bir ad belirtin.

### <a name="add-a-new-application"></a>Yeni uygulama ekle
Yeni bir uygulama oluşturmak için bir uygulama paketi ekleyin ve yeni, benzersiz bir uygulama KIMLIĞI belirtin. Yeni uygulama KIMLIĞIYLE eklediğiniz ilk uygulama paketi de yeni bir uygulama oluşturur.

**Uygulamalar** > **Ekle**’ye tıklayın.

![Azure portal yeni uygulama dikey penceresi][5]

Yeni **uygulama** penceresi, yeni uygulamanızın ve uygulama paketinizin ayarlarını belirtmek için aşağıdaki alanları sağlar.

**Uygulama KIMLIĞI**

Bu alan, standart Azure Batch KIMLIĞI doğrulama kurallarına tabi olan yeni uygulamanızın KIMLIĞINI belirtir. Uygulama KIMLIĞI sağlama kuralları aşağıdaki gibidir:

* Windows düğümlerinde, KIMLIK alfasayısal karakter, kısa çizgi ve alt çizgi birleşimini içerebilir. Linux düğümlerinde yalnızca alfasayısal karakterlere ve alt çizgilere izin verilir.
* 64 'den fazla karakter içeremez.
* Batch hesabı dahilinde benzersiz olmalıdır.
* Büyük/küçük harf koruma ve büyük/küçük harfe duyarlı değildir.

**Sürüm**

Bu alan, karşıya yüklediğiniz uygulama paketinin sürümünü belirtir. Sürüm dizeleri aşağıdaki doğrulama kurallarına tabidir:

* Windows düğümlerinde, sürüm dizesi alfasayısal karakter, tire, alt çizgi ve dönemlerin herhangi bir birleşimini içerebilir. Linux düğümlerinde, sürüm dizesinde yalnızca alfasayısal karakterler ve alt çizgiler bulunabilir.
* 64 'den fazla karakter içeremez.
* Uygulama içinde benzersiz olmalıdır.
* Büyük/küçük harf koruma ve büyük/küçük harfe duyarlı değildir.

**Uygulama paketi**

Bu alan, uygulama ikililerini ve uygulamayı yürütmek için gereken destekleyici dosyaları içeren. zip dosyasını belirtir. **Bir dosya seç** kutusu veya klasör simgesine tıklayarak uygulamanızın dosyalarını içeren bir. zip dosyasına gidin ve seçin.

Bir dosya seçtikten sonra **Tamam** ' a tıklayarak Azure Storage 'a yükleme işlemini başlatın. Karşıya yükleme işlemi tamamlandığında, Portal bir bildirim görüntüler. Karşıya yüklediğiniz dosyanın boyutuna ve ağ bağlantınızın hızına bağlı olarak bu işlem biraz zaman alabilir.

> [!WARNING]
> Karşıya yükleme işlemi tamamlanmadan önce **Yeni uygulama** penceresini kapatmayın. Bunun yapılması yükleme işlemini sonlandırır.
> 
> 

### <a name="add-a-new-application-package"></a>Yeni bir uygulama paketi Ekle
Mevcut bir uygulama için bir uygulama paketi sürümü eklemek için, **uygulamalar** penceresinde bir uygulama seçin ve ardından**Ekle** >  **paketler** ' e tıklayın.

![Azure portal 'de uygulama paketi dikey penceresi ekleme][8]

Görebileceğiniz gibi, alanlar **Yeni uygulama** penceresiyle eşleşir, ancak **uygulama kimliği** kutusu devre dışıdır. Yeni uygulama için yaptığınız gibi, yeni paketinizin **sürümünü** belirtin, **uygulama paketinizin** . zip dosyasına gidin ve ardından **Tamam** ' a tıklayarak paketi karşıya yükleyin.

### <a name="update-or-delete-an-application-package"></a>Uygulama paketini güncelleştirme veya silme
Var olan bir uygulama paketini güncelleştirmek veya silmek için, uygulamanın ayrıntılarını açın, **paketler**' e tıklayın, değiştirmek istediğiniz uygulama paketinin satırındaki **üç nokta** simgesine tıklayın ve gerçekleştirmek istediğiniz eylemi seçin.

![Azure portal paketi güncelleştirme veya silme][7]

**Güncelleştirme**

**Güncelleştir**' e tıkladığınızda, **güncelleştirme paketi** penceresi görüntülenir. Bu pencere, **Yeni uygulama paketi** penceresine benzer ancak yalnızca paket seçimi alanı etkinleştirilir ve karşıya yüklenecek yenı bir ZIP dosyası belirtmenize olanak tanır.

![Azure portal paket dikey penceresini güncelleştirme][11]

**Silme**

**Sil**' e tıkladığınızda paket sürümünün silinmesini onaylamanız Istenir ve Batch, paketi Azure Storage 'dan siler. Bir uygulamanın varsayılan sürümünü silerseniz, **varsayılan sürüm** ayarı uygulama için kaldırılır.

![Uygulamayı Sil][12]

## <a name="install-applications-on-compute-nodes"></a>İşlem düğümlerine uygulama yükler
Uygulama paketlerini Azure portal ile yönetmeyi öğrendiğinize göre, bunları işlem düğümlerine dağıtmayı ve Batch görevleriyle çalıştırmayı tartışabiliriz.

### <a name="install-pool-application-packages"></a>Havuz uygulama paketlerini yükler
Bir havuzdaki tüm işlem düğümlerine bir uygulama paketi yüklemek için havuz için bir veya daha fazla uygulama paketi *başvurusu* belirtin. Bir havuz için belirttiğiniz uygulama paketleri, düğüm havuza katıldığında ve düğüm yeniden başlatıldığında veya yeniden görüntülendiğinde her bir işlem düğümüne yüklenir.

Batch .NET sürümünde bir veya daha fazla [Cloudpool][net_cloudpool]belirtin. Yeni bir havuz oluşturduğunuzda veya mevcut bir havuz için [Applicationpackagereferferde][net_cloudpool_pkgref] . [Applicationpackagereference][net_pkgref] sınıfı, bir havuzun işlem düğümlerine yüklenecek BIR uygulama kimliği ve sürümünü belirtir.

```csharp
// Create the unbound CloudPool
CloudPool myCloudPool =
    batchClient.PoolOperations.CreatePool(
        poolId: "myPool",
        targetDedicatedComputeNodes: 1,
        virtualMachineSize: "standard_d1_v2",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));

// Specify the application and version to install on the compute nodes
myCloudPool.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference {
        ApplicationId = "litware",
        Version = "1.1001.2b" }
};

// Commit the pool so that it's created in the Batch service. As the nodes join
// the pool, the specified application package is installed on each.
await myCloudPool.CommitAsync();
```

> [!IMPORTANT]
> Bir uygulama paketi dağıtımı herhangi bir nedenle başarısız olursa, Batch Hizmeti düğümü [kullanılamaz][net_nodestate]olarak işaretler ve bu düğümde yürütülmek üzere hiçbir görev planlanmaz. Bu durumda, paket dağıtımını yeniden başlatmak için düğümü **yeniden başlatmanız** gerekir. Düğüm yeniden başlatıldığında, düğüm üzerinde görev zamanlamayı yeniden de etkinleştirilir.
> 
> 

### <a name="install-task-application-packages"></a>Görev uygulama paketlerini yükler
Bir havuza benzer şekilde, bir görev için uygulama paketi *başvurularını* belirtirsiniz. Bir görev bir düğümde çalışmak üzere zamanlandığında, bu paket, görevin komut satırı yürütülmeden hemen önce indirilir ve ayıklanır. Belirtilen bir paket ve sürüm düğümde zaten yüklüyse, paket indirilmez ve mevcut paket kullanılır.

Bir görev uygulama paketini yüklemek için görevin [Cloudtask][net_cloudtask]öğesini yapılandırın. [Applicationpackagereferenceözelliği][net_cloudtask_pkgref] :

```csharp
CloudTask task =
    new CloudTask(
        "litwaretask001",
        "cmd /c %AZ_BATCH_APP_PACKAGE_LITWARE%\\litware.exe -args -here");

task.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference
    {
        ApplicationId = "litware",
        Version = "1.1001.2b"
    }
};
```

## <a name="execute-the-installed-applications"></a>Yüklü uygulamaları yürütme
Bir havuz veya görev için belirttiğiniz paketler, düğümün `AZ_BATCH_ROOT_DIR` bir adlandırılmış dizine indirilir ve ayıklanır. Batch Ayrıca, adlandırılmış dizinin yolunu içeren bir ortam değişkeni oluşturur. Görev komut satırlarında, düğümdeki uygulamaya başvururken bu ortam değişkeni kullanılır. 

Windows düğümlerinde, değişkeni aşağıdaki biçimdedir:

```
Windows:
AZ_BATCH_APP_PACKAGE_APPLICATIONID#version
```

Linux düğümlerinde biçim biraz farklıdır. Nokta (.), tire (-) ve sayı işaretleri (#), ortam değişkeninde alt çizgi olarak düzleştirilir. Ayrıca, uygulama KIMLIĞI durumunun korunmadığını unutmayın. Örnek:

```
Linux:
AZ_BATCH_APP_PACKAGE_applicationid_version
```

`APPLICATIONID` ve `version`, dağıtım için belirttiğiniz uygulama ve paket sürümüne karşılık gelen değerlerdir. Örneğin, *blender* uygulamasının 2,7 sürümünün Windows düğümlerinde yüklü olması gerektiğini belirttiyseniz, görev komut satırları bu ortam değişkenini, dosyalarına erişmek için kullanır:

```
Windows:
AZ_BATCH_APP_PACKAGE_BLENDER#2.7
```

Linux düğümlerinde, ortam değişkenini bu biçimde belirtin. Noktaları (.), kısa çizgiler (-) ve sayı işaretlerini (#) düzleştirin ve uygulama KIMLIĞI durumunu koruyun:

```
Linux:
AZ_BATCH_APP_PACKAGE_blender_2_7
``` 

Bir uygulama paketini karşıya yüklediğinizde, işlem düğümleriniz için dağıtım yapmak üzere varsayılan bir sürüm belirtebilirsiniz. Bir uygulama için varsayılan bir sürüm belirttiyseniz, uygulamaya başvuru yaptığınızda sürüm sonekini atlayabilirsiniz. **Uygulamalar penceresinde,** [uygulamaları karşıya yükleme ve yönetme](#upload-and-manage-applications)bölümünde gösterildiği gibi, varsayılan uygulama sürümünü Azure Portal belirleyebilirsiniz.

Örneğin, uygulama *blender*için varsayılan sürüm olarak "2,7" ayarlarsanız ve görevleriniz aşağıdaki ortam değişkenine Başvurdıysanız, Windows düğümleriniz 2,7 sürümünü yürütür:

`AZ_BATCH_APP_PACKAGE_BLENDER`

Aşağıdaki kod parçacığında, *blender* uygulamasının varsayılan sürümünü başlatan örnek bir görev komut satırı gösterilmektedir:

```csharp
string taskId = "blendertask01";
string commandLine =
    @"cmd /c %AZ_BATCH_APP_PACKAGE_BLENDER%\blender.exe -args -here";
CloudTask blenderTask = new CloudTask(taskId, commandLine);
```

> [!TIP]
> İşlem düğümü ortam ayarları hakkında daha fazla bilgi için bkz. [Batch özelliğine genel bakış](batch-api-basics.md) içindeki [Görevler için ortam ayarları](batch-api-basics.md#environment-settings-for-tasks) .
> 
> 

## <a name="update-a-pools-application-packages"></a>Bir havuzun uygulama paketlerini güncelleştirme
Mevcut bir havuz zaten bir uygulama paketiyle yapılandırıldıysa, havuz için yeni bir paket belirtebilirsiniz. Bir havuz için yeni bir paket başvurusu belirtirseniz aşağıdakiler geçerlidir:

* Batch hizmeti, yeni belirtilen paketi havuza ve yeniden başlatılan veya yeniden görüntü oluşturulan tüm mevcut düğümlerde birleştirir.
* Paket başvurularını güncelleştirdiğinizde zaten havuzda olan işlem düğümleri yeni uygulama paketini otomatik olarak yüklemez. Yeni paketi almak için bu işlem düğümlerinin yeniden başlatılması veya yeniden oluşturulması gerekir.
* Yeni bir paket dağıtıldığında oluşturulan ortam değişkenleri yeni uygulama paketi başvurularını yansıtır.

Bu örnekte, mevcut havuzda [Cloudpool][net_cloudpool]'un biri olarak yapılandırılmış *blender* uygulamasının 2,7 sürümü vardır. [Applicationpackagereferinin][net_cloudpool_pkgref]. Havuzun düğümlerini 2.76 b sürümüyle güncelleştirmek için yeni sürümle yeni bir [Applicationpackagereference][net_pkgref] belirtip değişikliği işleyin.

```csharp
string newVersion = "2.76b";
CloudPool boundPool = await batchClient.PoolOperations.GetPoolAsync("myPool");
boundPool.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference {
        ApplicationId = "blender",
        Version = newVersion }
};
await boundPool.CommitAsync();
```

Yeni sürüm yapılandırıldıktan sonra Batch hizmeti, 2.76 b sürümünü havuza katılan *Yeni* bir düğüme yüklemiştir. 2\.76 b 'yi *zaten* havuzda olan düğümlere yüklemek için yeniden başlatın veya yeniden başlatın. Yeniden başlatılan düğümlerin dosyaları önceki paket dağıtımlarından korumadığını unutmayın.

## <a name="list-the-applications-in-a-batch-account"></a>Bir Batch hesabındaki uygulamaları listeleyin
Uygulamaları ve bunların paketlerini, [Applicationoperations][net_appops]' i kullanarak bir Batch hesabında listeleyebilirsiniz. [Listapplicationözetler][net_appops_listappsummaries] yöntemi.

```csharp
// List the applications and their application packages in the Batch account.
List<ApplicationSummary> applications = await batchClient.ApplicationOperations.ListApplicationSummaries().ToListAsync();
foreach (ApplicationSummary app in applications)
{
    Console.WriteLine("ID: {0} | Display Name: {1}", app.Id, app.DisplayName);

    foreach (string version in app.Versions)
    {
        Console.WriteLine("  {0}", version);
    }
}
```

## <a name="wrap-up"></a>Yukarı kaydırın
Uygulama paketleriyle, müşterilerinizin işlerinin uygulamalarını seçmesini ve Batch özellikli hizmetlerinizle işleri işlerken kullanılacak tam sürümü belirtmesini sağlayabilirsiniz. Müşterilerinizin hizmetinize kendi uygulamalarını yükleme ve izleme özelliği de sağlayabilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar
* [Batch REST API][api_rest] Ayrıca uygulama paketleriyle çalışmak için destek sağlar. Örneğin, REST API kullanarak yüklenecek paketleri belirtme hakkında bilgi için bkz. [bir hesaba havuz ekleme][rest_add_pool] Içindeki [applicationpackagereferges][rest_add_pool_with_packages] öğesi. Toplu REST API kullanarak uygulama bilgilerini alma hakkında ayrıntılar için bkz. [uygulamalar][rest_applications] .
* [Batch yönetimi .NET ile Azure Batch hesaplarını ve kotaları](batch-management-dotnet.md)programlı bir şekilde yönetmeyi öğrenin. [Batch yönetimi .net][api_net_mgmt] kitaplığı, Batch uygulamanız veya hizmetiniz için hesap oluşturma ve silme özelliklerini etkinleştirebilir.

[api_net]: https://docs.microsoft.com/dotnet/api/overview/azure/batch/client?view=azure-dotnet
[api_net_mgmt]: https://docs.microsoft.com/dotnet/api/overview/azure/batch/management?view=azure-dotnet
[api_rest]: https://docs.microsoft.com/rest/api/batchservice/
[batch_mgmt_nuget]: https://www.nuget.org/packages/Microsoft.Azure.Management.Batch/
[github_samples]: https://github.com/Azure/azure-batch-samples
[storage_pricing]: https://azure.microsoft.com/pricing/details/storage/
[net_appops]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationoperations.aspx
[net_appops_listappsummaries]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationoperations.listapplicationsummaries.aspx
[net_cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_cloudpool_pkgref]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.applicationpackagereferences.aspx
[net_cloudtask]: https://msdn.microsoft.com/library/microsoft.azure.batch.cloudtask.aspx
[net_cloudtask_pkgref]: https://msdn.microsoft.com/library/microsoft.azure.batch.cloudtask.applicationpackagereferences.aspx
[net_nodestate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.state.aspx
[net_pkgref]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationpackagereference.aspx
[portal]: https://portal.azure.com
[rest_applications]: https://msdn.microsoft.com/library/azure/mt643945.aspx
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[rest_add_pool_with_packages]: https://msdn.microsoft.com/library/azure/dn820174.aspx#bk_apkgreference

[1]: ./media/batch-application-packages/app_pkg_01.png "Uygulama paketleri üst düzey diyagram"
[2]: ./media/batch-application-packages/app_pkg_02.png "Azure portal içinde uygulamalar bölmesi"
[3]: ./media/batch-application-packages/app_pkg_03.png "Azure portal uygulamalar dikey penceresi"
[4]: ./media/batch-application-packages/app_pkg_04.png "Azure portal 'de uygulama ayrıntıları dikey penceresi"
[5]: ./media/batch-application-packages/app_pkg_05.png "Azure portal yeni uygulama dikey penceresi"
[7]: ./media/batch-application-packages/app_pkg_07.png "Azure portal paketleri güncelleştirme veya silme açılan"
[8]: ./media/batch-application-packages/app_pkg_08.png "Azure portal yeni uygulama paketi dikey penceresi"
[9]: ./media/batch-application-packages/app_pkg_09.png "Bağlı depolama hesabı uyarısı yok"
[10]: ./media/batch-application-packages/app_pkg_10.png "Azure portal depolama hesabı dikey penceresini seçin"
[11]: ./media/batch-application-packages/app_pkg_11.png "Azure portal paket dikey penceresini güncelleştirme"
[12]: ./media/batch-application-packages/app_pkg_12.png "Azure portal paket onay iletişim kutusunu Sil"
[13]: ./media/batch-application-packages/package-file-structure.png "Azure portal 'de işlem düğümü bilgileri"
[14]: ./media/batch-application-packages/package-file-structure-node.png "Azure portal ' de görünen işlem düğümündeki dosyalar"
