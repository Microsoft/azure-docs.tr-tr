---
title: Azure hızlı başlangıç-Azure portal ilk Batch işinizi çalıştırın
description: Bu hızlı başlangıçta, bir Batch hesabı, bir işlem düğümleri havuzu ve havuzda temel görevleri çalıştıran bir iş oluşturmak için Azure portal nasıl kullanılacağı gösterilmektedir.
ms.date: 08/17/2020
ms.topic: quickstart
ms.custom:
- mvc
- mode-portal
ms.openlocfilehash: 3333097b4bd55173725aa33bc4bfbae318510cf1
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538538"
---
# <a name="quickstart-run-your-first-batch-job-in-the-azure-portal"></a>Hızlı başlangıç: Azure portalında ilk Batch işinizi çalıştırma

Batch hesabı, bir işlem düğümleri Havuzu (sanal makineler) ve havuzdaki görevleri çalıştıran bir iş oluşturmak için Azure portal kullanarak Azure Batch kullanmaya başlayın. Bu hızlı başlangıcı tamamladıktan sonra Batch hizmetinin temel kavramlarını anlayacaksınız ve toplu Işi daha büyük ölçekte daha gerçekçi iş yükleri ile denemeye hazır olacaksınız.

## <a name="prerequisites"></a>Önkoşullar

- Etkin aboneliği olan bir Azure hesabı. [Ücretsiz hesap oluşturun](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-batch-account"></a>Batch hesabı oluşturma

Test amacıyla örnek bir Batch hesabı oluşturmak için bu adımları izleyin. Havuzlar ve işler oluşturmak için bir Batch hesabı gerekir. Burada gösterildiği gibi, bir Azure depolama hesabını Batch hesabına bağlayabilirsiniz. Bu hızlı başlangıç için gerekli olmamasına karşın, depolama hesabı uygulamaları dağıtmak ve çoğu gerçek yaşam iş yükleri için giriş ve çıkış verilerini depolamak üzere yararlıdır.

1. [Azure Portal](https://portal.azure.com), **kaynak**  >  **Hesaplama**  >  **toplu işlem hizmeti** oluştur ' u seçin. 

   :::image type="content" source="media/quick-create-portal/marketplace-batch.png" alt-text="Azure Marketi 'nde Batch hizmetinin ekran görüntüsü.":::

1. **Kaynak grubu** alanında, **Yeni oluştur** ' u seçin ve kaynak grubunuz için bir ad girin.

1. **Hesap adı** için bir değer girin. Bu ad, seçilen Azure **konumu** içinde benzersiz olmalıdır. Bu, yalnızca küçük harf ve rakam içerebilir ve 3-24 karakter arasında olmalıdır.

1. **Depolama hesabı** altında, var olan bir depolama hesabını seçin veya yeni bir tane oluşturun.

1. Diğer ayarları değiştirmeyin. Toplu Iş hesabını oluşturmak için **gözden geçir + oluştur**' u seçin ve **Oluştur** ' u seçin.

**Dağıtım başarılı** iletisi göründüğünde, oluşturduğunuz Batch hesabına gidin.

## <a name="create-a-pool-of-compute-nodes"></a>İşlem düğümleri havuzu oluşturma

Bir Batch hesabı edindikten sonra, test amacıyla örnek bir Windows işlem düğümleri havuzu oluşturun. Bu hızlı örnekteki havuz, Azure Marketi 'nden Windows Server 2019 görüntüsünü çalıştıran iki düğümden oluşur.

1. Batch hesabında **havuzlar**  >  **Ekle**' yi seçin.

1. *mypool* adlı bir **Havuz Kimliği** girin.

1. **İşletim Sistemi** menüsünde aşağıdaki ayarları seçin (diğer seçenekleri araştırabilirsiniz).
  
   |Ayar  |Değer  |
   |---------|---------|
   |**Görüntü türü**|Market|
   |**Publisher**     |microsoftwindowsserver|
   |**Teklif**     |windowsserver|
   |**İsteyin**     |2019-Datacenter-Core-smalldisk|

1. Aşağıya kaydırarak **Düğüm Boyutu** ve **Ölçek** ayarlarına girin. Önerilen düğüm boyutu, bu hızlı örnek için performans ile maliyetin iyi bir dengesini sunar.
  
   |Ayar  |Değer  |
   |---------|---------|
   |**Düğüm fiyatlandırma katmanı**     |Standart a1|
   |**Hedef adanmış düğümler**     |2|

1. Diğer ayarlar için varsayılan ayarları tutun ve **Tamam**'ı seçerek havuzu oluşturun.

Batch, havuzu hemen oluşturur ancak işlem düğümlerinin ayrılması ve başlatılması birkaç dakika sürer. Bu süre boyunca, havuzun **Ayırma durumu****Yeniden boyutlandırılıyor** şeklindedir. Havuz yeniden boyutlandırılırken devam ederek iş ve görevler oluşturabilirsiniz.

Birkaç dakika sonra, ayırma durumu **sabit** olarak değişir ve düğümler başlar. Düğümlerin durumunu denetlemek için havuzu seçin ve ardından **düğümler**' i seçin. Bir düğümün durumu **Boşta** olduğunda görevleri çalıştırmaya hazırdır.

## <a name="create-a-job"></a>Bir iş oluşturma

Bir havuza sahip olduktan sonra üzerinde çalıştıracak bir iş oluşturun. Batch işi bir veya daha fazla görevin mantıksal grubudur. Bir iş, öncelik gibi görevler arasında ortak olan ayarları ve görevlerin çalıştırılacağı havuzu içerir. Başlangıçta iş hiçbir görev içermez.

1. Batch hesabı görünümünde, **işler**  >  **Ekle**' yi seçin.

1. *myjob* adlı bir **İş Kimliği** girin. **Havuz** menüsünde *mypool*’u seçin. Diğer ayarlar için varsayılan değerleri kabul edin ve **Tamam**'ı seçin.

## <a name="create-tasks"></a>Görev oluşturma

Şimdi **Görevler** sayfasını açmak için işi seçin. İşte, işte çalıştırmak için örnek görevler oluşturacaksınız. Genellikle, toplu Iş kuyruklarını ve işlem düğümlerinde çalışacak şekilde dağıtılan birden çok görev oluşturursunuz. Bu örnekte, iki türdeş görev oluşturursunuz. Her görev, bir işlem düğümündeki Batch ortam değişkenlerini görüntülemek için bir komut satırı çalıştırır ve 90 saniye bekler.

Batch kullandığınızda komut satırı, uygulamanızı veya betiğinizi belirttiğiniz yerdir. Batch, işlem düğümlerine uygulama ve betik dağıtmanın birkaç yolunu sağlar.

İlk görevi oluşturmak için:

1. **Add (Ekle)** seçeneğini belirleyin.

1. *mytask* adlı bir **Görev Kimliği** girin.

1. **Komut satırı**’na `cmd /c "set AZ_BATCH & timeout /t 90 > NUL"` girin. Geri kalan ayarlar için varsayılan değerleri tutun ve **Gönder**' i seçin.

Bir görev oluşturduktan sonra Batch, görevi havuzda çalışmak üzere kuyruğa alır. Görevi çalıştıracak bir düğüm kullanılabilir olduğunda, görev çalışır.

İkinci bir görev oluşturmak için yukarıdaki adımları tekrarlayın. Farklı bir **Görev Kimliği** ile birlikte aynı komut satırını girin. İlk görev hala çalışıyorsa, Batch havuzdaki diğer düğüm üzerinde ikinci görevi başlatır.

## <a name="view-task-output"></a>Görev çıktısını görüntüleme

Oluşturduğunuz örnek görevler birkaç dakika içinde tamamlanır. Tamamlanan bir görevin çıkışını görüntülemek için, görevi seçin, sonra **düğümdeki dosyalar**' ı seçin. `stdout.txt`Görevin standart çıkışını görüntülemek için dosyayı seçin. İçeriği aşağıdakine benzerdir:

:::image type="content" source="media/quick-create-portal/task-output.png" alt-text="Tamamlanan bir görevin çıktısının ekran görüntüsü.":::

İçerik, düğüm üzerinde ayarlanmış Azure Batch ortam değişkenlerini gösterir. Kendi Batch işlerinizi ve görevlerinizi oluşturduğunuzda, görev komut satırlarında bu görev değişkenlerine ve komut satırları tarafından çalıştırılan uygulama ve betiklere başvurabilirsiniz.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Batch öğreticileri ve örnekleri ile devam etmek istiyorsanız, bu hızlı başlangıçta kullanılan Batch hesabını ve bağlı depolama hesabını kullanın. Batch hesabının kendisi için herhangi bir ücret alınmaz.

Zamanlanmış bir iş olmasa bile, düğümler çalışırken havuz için sizden ücret alınır. Havuz artık gerekli değilse silin. Hesap görünümünde **Havuzlar**'ı ve havuzun adını seçin. Ardından **Sil**’i seçin.  Havuzu sildiğinizde düğümler üzerindeki tüm görev çıkışları silinir.

Artık gerekli olmadığında kaynak grubunu, Batch hesabını ve tüm ilişkili kaynakları silin. Bu işlemi yapmak için Batch hesabına ait kaynak grubunu seçin ve **Kaynak Grubunu Sil**'i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta bir Batch hesabı, Batch havuzu ve Batch işi oluşturdunuz. İş, örnek görevler çalıştırdı ve düğümlerden biri üzerinde oluşturulan çıktıyı görüntülediniz. Batch hizmetinin temel kavramlarını anladıktan sonra, Batch’i daha büyük ölçekte daha gerçekçi iş yükleri ile denemeye hazırsınız. Azure Batch hakkında daha fazla bilgi için Azure Batch öğreticilerine devam edin.

> [!div class="nextstepaction"]
> [Azure Batch öğreticileri](./tutorial-parallel-dotnet.md)
