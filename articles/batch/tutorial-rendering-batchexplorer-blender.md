---
title: Öğretici-Azure Batch ve Batch Explorer kullanarak bir blender sahneyi Işleme
description: 'Öğretici: Azure Batch ve Batch Explorer istemci uygulamasını kullanarak bir Blender sahnesinden birden fazla kare işleme'
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: tutorial
ROBOTS: NOINDEX
ms.openlocfilehash: 11805d2a15e7f369ad38ab7e92409c1b357d2d8e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103490849"
---
# <a name="tutorial-render-a-blender-scene-using-batch-explorer"></a>Öğretici: Batch Explorer'ı kullanarak Blender sahnesi işleme

Bu öğreticide bir Blender tanıtım sahnesinden birden fazla kare işleme adımları gösterilmektedir. Hem istemci hem de işleme VM'lerinde kullanımı ücretsiz olduğundan öğreticide Blender kullanılmıştır ancak işlem Maya veya 3ds Max gibi diğer uygulamalarda da benzer olacaktır.

Bu öğreticide şunların nasıl yapıldığını öğreneceksiniz:
> [!div class="checklist"]
> * Azure depolamasına Blender sahnesi yükleme
> * İşlemeyi gerçekleştirmek için birden fazla düğüme sahip Batch havuzu oluşturma
> * Birden çok çerçeve işleme
> * İşlenen kare dosyalarını görüntüleme ve indirme

## <a name="prerequisites"></a>Önkoşullar

Batch’teki işleme uygulamalarını kullandığın kadar öde esasıyla kullanmak için bir kullandıkça öde aboneliğine veya diğer Azure satın alma seçeneğine ihtiyacınız vardır. Para kredi sağlayan ücretsiz bir Azure teklifi kullanıyorsanız, kullandığın kadar öde lisansı desteklenmez.

İlişkilendirilmiş depolama hesabına sahip bir Azure Batch hesabına ihtiyacınız vardır.  Batch hesabı oluşturmak için [CLI makalesi](./quick-create-cli.md) gibi Batch Hızlı Başlangıç makalelerinden birine bakın.

Bu öğreticide belirtilen VM boyutu ve VM sayısı için en az 50 çekirdekten oluşan düşük öncelikli bir çekirdek kotası yeterli olacaktır. Varsayılan kota kullanılabilir ancak bu durumda daha küçük bir VM boyutu kullanılması gerekir ve görüntüleri işleme süreci daha uzun sürer. Artırılmış çekirdek kotası isteme işlemi [bu makalede](./batch-quota-limit.md) anlatılmıştır.

Son olarak [Batch Explorer](https://azure.github.io/BatchExplorer/)'ın yüklenmesi gerekir. Windows, OSX ve Linux sürümleri mevcuttur. Bu adım isteğe bağlıdır ancak [Blender](https://www.blender.org/download/) yüklüyse aynı örnek model görüntülenebilir.

## <a name="download-the-demo-scene"></a>Tanıtım sahnesini indirme

[Blender Demo Files web sayfasından](https://www.blender.org/download/demo-files/) "Class room" tanıtım ZIP dosyasını indirin ve yerel sürücüye çıkarın.

## <a name="upload-a-scene-to-azure-storage"></a>Azure depolamasına sahne yükleme

Tanıtım amaçlı sahne dosyaları için bir depolama hesabı kapsayıcısı oluşturun:

* Batch Explorer'ı başlatın
* Sol taraftaki ana menüden 'Data' (Veri) menü öğesini seçin.
* Açılan menüde 'File groups' (Dosya grupları) öğesinin seçildiğinden emin olun.
* '+' düğmesini seçin ve 'blender-classroom' adlı yeni bir 'Empty file group' (Boş dosya grubu) oluşturun
  * Dosya grubu aslında 'fgrp-' ön ekine sahip bir Azure Depolama blob kapsayıcısıdır. Depolama hesabındaki diğer kapsayıcıları filtrelemek için bu kullanım benimsenmiştir

![Sahne dosyaları için dosya grubu](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_scene_filegroup.png)

Sahne dosyalarını yükleyin:

* Yeni kapsayıcıyı seçin ve 'classroom' klasörünün içeriğini sürükleyip Batch Explorer'daki kapsayıcıya bırakın.

![Yüklenen sahne dosyaları](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_scene_filegroup_uploaded.png)

## <a name="create-azure-storage-container-for-output-images"></a>Çıkış görüntüleri için Azure depolama kapsayıcısı oluşturma

Tanıtım amaçlı sahne çıkış dosyaları için bir depolama hesabı kapsayıcısı oluşturun:

* Sol taraftaki ana menüden 'Data' (Veri) menü öğesini seçin.
* '+' düğmesini seçin ve 'render-output' adlı yeni bir 'Empty file group' (Boş dosya grubu) oluşturun

![Çıkış dosyaları için dosya grubu](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_output_filegroup.png)

## <a name="create-a-pool-of-vms-for-rendering"></a>İşleme için VM havuzu oluşturma

Blender uygulamasını içeren Azure Marketplace VM görüntüsünü işleyen Batch havuzu oluşturma:

* Sol taraftaki ana menüden 'Gallery' (Galeri) menü öğesini seçin.
* Uygulama öğe listesinden 'Blender' öğesini seçin.
* Windows Server'da kare işlemek için gerekli öğeleri seçin
* Öğenin sağ tarafındaki bağlantı simgesini seçerek havuz ve iş oluşturmak için kullanılacak şablon dosyalarını görüntüleyebilirsiniz.

![Blender galeri öğeleri](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_gallery_item.png)

* ‘Create pool for later use’ (Daha sonra kullanmak üzere havuz oluştur) düğmesini seçin
  * Havuz adını “blender-windows” olarak bırakın
  * ‘Dedicated vm count’ (Ayrılmış VM sayısı) değerini “0” olarak belirleyin
  * ‘Low priority vm count’ (Düşük öncelikli VM sayısı) değerini “3” olarak belirleyin
  * ‘Node size’ (Düğüm boyutu) değerini “Standard_F16” olarak belirleyin. Başka bir VM boyutu seçebilirsiniz ancak kare işleme süresi çekirdek sayısına göre değişecektir.
* Havuzu oluşturmak için yeşil düğmeyi seçin
  * Havuz neredeyse anında oluşturulur ancak VM'lerin ayrılması ve başlatılması birkaç dakika sürebilir.

![Blender için havuz şablonu](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_pool_template.png)

> [!WARNING]
> Havuza eklenen VM'lerin maliyetinin Azure aboneliğinize yansıtılacağını unutmayın. Ücretlerin durdurulması için havuzun veya VM'lerin silinmesi gerekir. Ücret alınmasını önlemek için bu öğreticinin sonunda havuzu silin.

Havuzun ve VM'lerin durumu 'Pools' (Havuzlar) görünümünde izlenebilir. Aşağıdaki örnekte ikisi başlatılmış ve boşta, biri de başlatılıyor olmak üzere ayrılan üç VM gösterilmektedir: ![Havuz ısı haritası](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_pool_heatmap.png)

## <a name="create-a-rendering-job"></a>İşleme işi oluşturma

Oluşturulan havuzu kullanarak kare işlemek için bir işleme işi oluşturun:

* Sol taraftaki ana menüden 'Gallery' (Galeri) menü öğesini seçin.
* Uygulama öğe listesinden 'Blender' öğesini seçin.
* Windows Server'da kare işlemek için gerekli öğeleri seçin.
* 'Run job with existing pool' (İşi var olan havuzla çalıştır) düğmesini seçin
* 'blender-windows' havuzunu seçin
* 'Job name' (İş adı) değerini 'blender-render-tutorial1' olarak ayarlayın
* 'Input data' (Giriş verileri) için 'fgrp-blender-classroom' seçeneğini belirleyin
* 'Blend file' (Blend dosyası) dosya simgesini ve 'classroom.blend' öğesini seçin
* 'Frame start' (Başlangıç karesi) değerini '1', 'Frame end' (Bitiş karesi) değerini '5' olarak ayarlayın
* 'Outputs' (Çıkışlar) değerini 'fgrp-render-output' olarak ayarlayın
* İşi oluşturmak için yeşil düğmeyi seçin. Her kare için bir adet olmak üzere beş görev içeren bir iş oluşturulur

![Blender için iş şablonu](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_job_template.png)

İş ve tüm görevleri oluşturulduktan sonra tümü birlikte görüntülenir: ![İş görevlerinin listesi](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_task_list.png)

Bir görev havuz VM'sinde ilk kez çalıştırıldığında Blender tarafından erişilebilmesi için sahne dosyalarını depolama dosya grubundan VM'ye kopyalayan bir Batch işi hazırlama görevi çalıştırılır.
İşleme durumunu görmek için Blender tarafından oluşturulan stdout.txt günlük dosyasını inceleyebilirsiniz.  Bir görevi seçtiğinizde varsayılan olarak 'Task Outputs' (Görev Çıkışları) penceresi görüntülenir ve buradan 'stdout.txt' dosyası seçilip izlenebilir.
![stdout dosyası](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_stdout.png)

'blender-windows' havuzu seçiliyse havuz VM'leri çalışır durumda görünür: ![Çalışan düğümlerle havuz ısı haritası](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_pool_heatmap_running.png)

İşlenmiş görüntülerin oluşturulma süresi seçilen VM boyutuna göre farklılık gösterir.  Önceden belirtilen F16 VM kullanılarak karelerin işlenmesi yaklaşık 16 dakika sürmüştür.

## <a name="view-the-rendering-output"></a>İşleme çıkışını görüntüleme

Karelerin işlenmesi tamamlandığında görevlerin durumu tamamlanmış olarak görünür: ![Tamamlanmış görevler](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_tasks_complete.png)

İşlenen görüntü önce VM'ye yazılır ve 'wd' klasörü seçilerek görüntülenebilir: ![Havuz düğümünde işlenen görüntü](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_output_image.png)

İş şablonu ayrıca çıkış karesinin ve günlük dosyalarının iş oluşturulduğunda belirtilen Azure Depolama hesabı dosya grubuna yazıldığını gösterir.  'Data' (Veri) arabirimini kullanarak çıkış dosyalarını ve günlüklerini görüntüleyebilir, dosyaları indirebilirsiniz: ![Depolama dosya grubunda işlenen görüntü](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_output_image_storage.png)

Tüm görevler tamamlandığında iş tamamlanıyor olarak işaretlenir: ![İş ve tüm görevler tamamlandı](./media/tutorial-rendering-batchexplorer-blender/batch_explorer_job_alltasks_complete.png)

## <a name="clean-up-resources"></a>Kaynakları temizleme

> [!WARNING]
> VM'lerin Azure aboneliğinize ücret yansıtmasını durdurmak için havuzun silinmesi gerekir (sıfır düğüme indirilmesi de yeterli olacaktır).

* 'Pools' (Havuzlar) öğesini seçin
* 'blender-windows' havuzunu seçin
* Sağ tıklayıp 'Delete' (Sil) öğesini veya havuzun üzerindeki çöp kutusu simgesini seçin

## <a name="next-steps"></a>Sonraki adımlar

Bu öğreticide, şunların nasıl yapıldığını öğrendiniz:

> [!div class="checklist"]
> * Azure depolamasına Blender sahnesi yükleme
> * İşlemeyi gerçekleştirmek için birden fazla düğüme sahip Batch havuzu oluşturma
> * Birden çok çerçeve işleme
> * İşlenen kare dosyalarını görüntüleme ve indirme

**Galeri** bölümündeki Batch Explorer aracılığıyla kullanılabilen işleme uygulamalarını inceleyerek devam edin. Her uygulama için birkaç şablon vardır ve şablon sayısı zaman içinde artacaktır. Örneğin bir görüntüyü parçalara ayırarak bu parçaların paralel olarak işlenmesini sağlayan Blender şablonları bulunmaktadır.

Bulut ölçekli işleme hakkında daha fazla bilgi edinmek için, Batch Rendering hizmetinin seçeneklerine bakın.

> [!div class="nextstepaction"]
> [Toplu İşleme hizmeti](batch-rendering-service.md)
