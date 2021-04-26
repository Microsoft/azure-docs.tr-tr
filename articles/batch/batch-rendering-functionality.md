---
title: İşleme özellikleri
description: Standart Azure Batch özellikleri, işleme iş yüklerini ve uygulamaları çalıştırmak için kullanılır. Batch, iş yüklerini işlemeyi desteklemek için belirli özellikler içerir.
author: mscurrell
ms.author: markscu
ms.date: 03/12/2021
ms.topic: how-to
ms.openlocfilehash: a2e2cfb71999bd5ab83591448342d4bac1dabdd5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103496346"
---
# <a name="azure-batch-rendering-capabilities"></a>Azure Batch işleme özellikleri

Standart Azure Batch özellikleri, işleme iş yüklerini ve uygulamaları çalıştırmak için kullanılır. Batch, iş yüklerini işlemeyi desteklemek için belirli özellikler de içerir.

Havuzlar, işler ve görevler dahil toplu Iş kavramlarına genel bir bakış için [Bu makaleye](./batch-service-workflow-features.md)bakın.

## <a name="batch-pools-using-custom-vm-images-and-standard-application-licensing"></a>Özel VM görüntülerini ve standart uygulama lisansını kullanan Batch havuzları

Diğer iş yükleri ve uygulama türlerinde olduğu gibi, gerekli işleme uygulamaları ve eklentileri ile özel bir VM görüntüsü oluşturulabilir. Özel VM görüntüsü [paylaşılan görüntü galerisine](../virtual-machines/shared-image-galleries.md) yerleştirilir ve [toplu işlem havuzları oluşturmak için kullanılabilir](batch-sig-images.md).

Görev komut satırı dizelerinin, özel VM görüntüsü oluşturulurken kullanılan uygulamalara ve yollara başvurması gerekir.

Çoğu işleme uygulaması, lisans sunucusundan alınan lisanslar gerektirir. Mevcut bir şirket içi lisans sunucusu varsa, hem havuz hem de lisans sunucusunun aynı [sanal ağ](../virtual-network/virtual-networks-overview.md)üzerinde olması gerekir. Ayrıca, Batch havuzu ve lisans sunucusu VM 'si aynı sanal ağ üzerinde olan bir Azure VM 'de bir lisans sunucusu çalıştırmak mümkündür.

## <a name="batch-pools-using-rendering-vm-images"></a>VM görüntülerini işleme kullanan Batch havuzları

> [!IMPORTANT]
> İşlenen VM görüntüleri ve kullanım için ödeme lisansı [kullanım dışı bırakılmıştır ve 29 şubat 2024 tarihinde kullanımdan](https://azure.microsoft.com/updates/azure-batch-rendering-vm-images-licensing-will-be-retired-on-29-february-2024/)kaldırılacaktır. Toplu Işi işlemek için kullanmak üzere [Özel BIR VM görüntüsü ve standart uygulama lisanslama kullanılmalıdır.](batch-rendering-functionality.md#batch-pools-using-custom-vm-images-and-standard-application-licensing)

### <a name="rendering-application-installation"></a>Uygulama yükleme işleme

Yalnızca önceden yüklenmiş uygulamaların kullanılması gerekiyorsa, havuz yapılandırmasında bir Azure Marketi işleme VM görüntüsü belirtilebilir.

Bir Windows görüntüsü ve bir CentOS görüntüsü vardır.  [Azure Marketi](https://azuremarketplace.microsoft.com)'nde VM görüntüleri ' toplu işleme ' aranarak bulunabilir.

Örnek havuz yapılandırması için bkz. [Azure CLI işleme öğreticisi](./tutorial-rendering-cli.md).  Azure portal ve Batch Explorer, bir havuz oluşturduğunuzda bir işleme VM görüntüsü seçmek için GUI araçları sağlar.  Bir Batch API 'SI kullanıyorsanız, bir havuz oluştururken [ImageReference](/rest/api/batchservice/pool/add#imagereference) için aşağıdaki özellik değerlerini belirtin:

| Publisher | Sunduğu | Sku | Sürüm |
|---------|---------|---------|--------|
| toplu iş | işleme-centos73 | çizmeye | en son |
| toplu iş | işleme-windows2016 | çizmeye | en son |

Havuz VM 'lerinde ek uygulamalar gerekliyse diğer seçenekler kullanılabilir:

* Paylaşılan görüntü galerisindeki özel bir görüntü:
  * Bu seçeneği kullanarak VM'nizi ihtiyacınız olan uygulamalar ve sürümlerle yapılandırabilirsiniz. Daha fazla bilgi için bkz. [paylaşılan görüntü Galerisi ile havuz oluşturma](batch-sig-images.md). Autodesk ve Chaos grubu, Azure Batch lisanslama hizmetine göre doğrulanacak şekilde, sırasıyla Arnold ve V-Ray ' i değiştirdi. Bu uygulamaların sürümleriyle bu desteğe sahip olduğunuzdan emin olun, aksi takdirde kullanım başına ödeme lisansı çalışmaz. Maya veya 3ds Max 'ın geçerli sürümleri, gözetimsiz (Batch/komut satırı modunda) çalıştırıldığında bir lisans sunucusu gerektirmez. Bu seçenekle devam etme konusunda emin değilseniz Azure desteği 'ne başvurun.
* [Uygulama paketleri](./batch-application-packages.md):
  * Bir veya daha fazla ZIP dosyası kullanarak uygulama dosyalarını paketleyin, Azure portal aracılığıyla yükleyin ve havuzu yapılandırma bölümünde paketi belirtin. Havuz VM 'Leri oluşturulduğunda, ZIP dosyaları indirilir ve dosyalar ayıklanır.
* Kaynak dosyaları:
  * Uygulama dosyaları Azure Blob depolama alanına yüklenir ve [Havuz başlangıç görevinde](/rest/api/batchservice/pool/add#starttask)dosya başvurularını belirtirsiniz. Havuz VM 'Leri oluşturulduğunda, kaynak dosyalar her bir sanal makineye indirilir.

### <a name="pay-for-use-licensing-for-pre-installed-applications"></a>Önceden yüklenmiş uygulamalar için kullanım için ödeme lisansı

Kullanılacak uygulamalar ve havuz yapılandırmasında bir lisans ücreti belirtilmesi gerekir.

* `applicationLicenses` [Havuz oluştururken](/rest/api/batchservice/pool/add#request-body)özelliğini belirtin.  Aşağıdaki değerler dizeler dizisinde belirtilebilir-"Vray", "Arnold", "3dsmax", "Maya".
* Bir veya daha fazla uygulama belirttiğinizde, bu uygulamaların maliyeti VM 'lerin maliyetine eklenir.  Uygulama fiyatları [Azure Batch fiyatlandırma sayfasında](https://azure.microsoft.com/pricing/details/batch/#graphic-rendering)listelenir.

> [!NOTE]
> Bunun yerine, işleme uygulamalarını kullanmak için bir lisans sunucusuna bağlanıyorsanız, `applicationLicenses` özelliği belirtmeyin.

Uygulamaları seçmek ve uygulama fiyatlarını göstermek için Azure portal veya Batch Explorer kullanabilirsiniz.

Uygulama kullanma girişiminde bulunuldu, ancak uygulama `applicationLicenses` Havuz yapılandırmasının özelliğinde belirtilemezse veya bir lisans sunucusuna ulaşmadıysa, uygulama yürütmesi bir lisanslama hatası ve sıfır olmayan çıkış kodu ile başarısız olur.

### <a name="environment-variables-for-pre-installed-applications"></a>Önceden yüklenmiş uygulamalar için ortam değişkenleri

Görevleri işlemek için komut satırı oluşturabilmeniz için, işleme uygulaması yürütülebilir dosyalarının yükleme konumu belirtilmelidir.  Azure Marketi VM görüntülerinde, gerçek yolları belirtmek yerine kullanılabilen sistem ortam değişkenleri oluşturulmuştur.  Bu ortam değişkenleri, her görev için oluşturulan [Standart Batch ortam değişkenlerine](./batch-compute-node-environment-variables.md) ek niteliğindedir.

|Uygulama|Uygulama çalıştırılabilir|Ortam değişkeni|
|---------|---------|---------|
|Autodesk 3ds Max 2021|3dsmaxcmdio.exe|3DSMAX_2021_EXEC|
|Autodesk Maya 2020|render.exe|MAYA_2020_EXEC|
|Chaos Group V-Ray tek başına|vray.exe|VRAY_4 VRAY_4.10.03_EXEC|
|Arnold 2020 komut satırı|kick.exe|ARNOLD_2020_EXEC|
|Blender|blender.exe|BLENDER_2018_EXEC|

## <a name="azure-vm-families"></a>Azure VM aileleri

Diğer iş yüklerinde olduğu gibi, uygulama sistemi gereksinimlerinin işlenmesi farklılık gösterir ve performans gereksinimleri işler ve projeler için farklılık gösterir.  Gereksinimlerinize bağlı olarak, Azure 'da çok çeşitli VM aileleri mevcuttur: en düşük maliyet, en iyi fiyat/performans, en iyi performans ve benzeri.
Arnold gibi bazı işleme uygulamaları CPU tabanlıdır; V-Ray ve Blender döngüleri gibi diğerleri, CPU ve/veya GPU 'Ları kullanabilir.
Kullanılabilir VM aileleri ve VM boyutlarının açıklaması için [bkz. VM türleri ve boyutları](../virtual-machines/sizes.md).

## <a name="low-priority-vms"></a>Düşük öncelikli sanal makineler

Diğer iş yüklerinde olduğu gibi, düşük öncelikli VM 'Ler işleme için Batch havuzlarında kullanılabilir.  Düşük öncelikli VM 'Ler, normal adanmış VM 'Lerle aynı şekilde gerçekleştirilir, ancak daha fazla Azure kapasitesi kullanır ve büyük bir indirimle kullanılabilir.  Düşük öncelikli VM 'Lerin kullanılması için zorunluluğunu getirir, kullanılabilir kapasiteye bağlı olarak, bu VM 'Lerin ayrılmayabilir veya herhangi bir zamanda yok edilebilir hale gelebilir. Bu nedenle, düşük öncelikli VM 'Ler tüm işleme işleri için uygun olmaz. Örneğin, görüntülerin işlenmesi çok saat sürebileceğinden, bu görüntülerin işlenmesi, süresi dolan VM 'Lerin kabul edilebilir olması nedeniyle kesintiye uğratılmasına ve yeniden başlatılmasına neden olur.

Düşük öncelikli VM 'lerin özellikleri ve Batch kullanarak bunları yapılandırmanın çeşitli yolları hakkında daha fazla bilgi için bkz. [Batch ile düşük öncelikli VM 'Ler kullanma](./batch-low-pri-vms.md).

## <a name="jobs-and-tasks"></a>İşler ve görevler

İşler ve görevler için işleme özgü destek gerekmez.  Ana yapılandırma öğesi, gerekli uygulamaya başvurması gereken görev komut satıröğesidir.
Azure Marketi VM görüntüleri kullanıldığında en iyi yöntem, yolu ve uygulama yürütülebilirini belirtmek için ortam değişkenlerini kullanmaktır.

## <a name="next-steps"></a>Sonraki adımlar

Toplu işleme örnekleri için iki öğreticiyi deneyin:

* [Azure CLı kullanarak işleme](./tutorial-rendering-cli.md)
* [Batch Explorer'ı kullanarak işleme](./tutorial-rendering-batchexplorer-blender.md)