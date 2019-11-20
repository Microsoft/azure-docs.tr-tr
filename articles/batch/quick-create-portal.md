---
title: Azure Hızlı Başlangıç - Batch işi çalıştırma - Portal
description: Azure portalı ile bir Batch işi çalıştırmayı hızlı bir şekilde öğrenin.
services: batch
author: laurenhughes
manager: gwallace
ms.service: batch
ms.topic: quickstart
ms.date: 07/03/2018
ms.author: lahugh
ms.custom: mvc
ms.openlocfilehash: 92f8499a10c07e7d5ea74e49c374cef192b8a47f
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70094116"
---
# <a name="quickstart-run-your-first-batch-job-in-the-azure-portal"></a>Hızlı Başlangıç: Azure portal ilk Batch işinizi çalıştırma

Bu hızlı başlangıçta, Azure portalını kullanarak bir Batch hesabı, bir işlem düğümleri (sanal makine) *havuzu* ve havuz üzerinde temel *görevler* çalıştıran bir *iş* oluşturma işlemi gösterilmektedir. Bu hızlı başlangıcı tamamladıktan sonra, Batch hizmetinin temel kavramlarını anlayacak ve Batch’i daha büyük ölçekte daha gerçekçi iş yükleri ile denemeye hazır olacaksınız.

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

## <a name="sign-in-to-azure"></a>Azure'da oturum açma 

[https://portal.azure.com](https://portal.azure.com ) adresinden Azure portalında oturum açın.

## <a name="create-a-batch-account"></a>Batch hesabı oluşturma

Test amacıyla örnek bir Batch hesabı oluşturmak için bu adımları izleyin. Havuzlar ve işler oluşturmak için bir Batch hesabı gerekir. Burada gösterildiği gibi, bir Azure depolama hesabını Batch hesabına bağlayabilirsiniz. Bu hızlı başlangıç için gerekli olmamasına karşın, depolama hesabı uygulamaları dağıtmak ve çoğu gerçek yaşam iş yükleri için giriş ve çıkış verilerini depolamak üzere yararlıdır.


1. **Kaynak oluştur** > **İşlem** > **Batch Hizmeti**'ni seçin. 

   ![Market’te Batch][marketplace_portal]

2. **Hesap adı** ve **Kaynak grubu** değerlerini girin. Hesap adı, seçili Azure **Konumu** içinde benzersiz olmalı, yalnızca küçük harf veya rakam içermeli ve 3-24 karakterden oluşmalıdır. 

3. **Depolama hesabında**, mevcut bir depolama hesabını seçin veya yeni bir depolama hesabı oluşturun.

4. Diğer ayarlar için varsayılan ayarları tutun ve **Oluştur**'u seçerek hesabı oluşturun.

   ![Batch hesabı oluşturma][account_portal]  

**Dağıtım başarılı** iletisi görüntülendiğinde portalda Batch hesabına gidin.

## <a name="create-a-pool-of-compute-nodes"></a>İşlem düğümleri havuzu oluşturma

Bir Batch hesabı edindikten sonra, test amacıyla örnek bir Windows işlem düğümleri havuzu oluşturun. Bu hızlı örneğe yönelik havuz, Azure Market’te Windows Server 2012 R2 görüntüsü çalıştıran 2 düğümden oluşur.


1. Batch hesabında **Havuzlar** > **Ekle**'yi seçin.

2. *mypool* adlı bir **Havuz Kimliği** girin. 

3. **İşletim Sistemi** menüsünde aşağıdaki ayarları seçin (diğer seçenekleri araştırabilirsiniz).
  
   |Ayar  |Value  |
   |---------|---------|
   |**Görüntü Türü**|Market (Linux/Windows)|
   |**Yayımcı**     |MicrosoftWindowsServer|
   |**Teklif**     |WindowsServer|
   |**Sku**     |2012-R2-Datacenter-smalldisk|

   ![Bir havuz işletim sistemi seçin][pool_os] 

4. Aşağıya kaydırarak **Düğüm Boyutu** ve **Ölçek** ayarlarına girin. Önerilen düğüm boyutu, bu hızlı örnek için performans ile maliyetin iyi bir dengesini sunar.
  
   |Ayar  |Value  |
   |---------|---------|
   |**Düğüm fiyatlandırma katmanı**     |Standard_A1|
   |**Hedef adanmış düğümler**     |2|

   ![Bir havuz boyutu seçin][pool_size] 

5. Diğer ayarlar için varsayılan ayarları tutun ve **Tamam**'ı seçerek havuzu oluşturun.

Batch, havuzu hemen oluşturur ancak işlem düğümlerinin ayrılması ve başlatılması birkaç dakika sürer. Bu süre boyunca, havuzun **Ayırma durumu** **Yeniden boyutlandırılıyor** şeklindedir. Havuz yeniden boyutlandırılırken devam ederek iş ve görevler oluşturabilirsiniz. 

![Yeniden boyutlandırma durumunda havuz][pool_resizing]

Birkaç dakika sonra havuzun durumu **Sabit** olur ve düğümler başlar. Düğümlerin durumunu denetlemek için **Düğümler**'i seçin. Bir düğümün durumu **Boşta** olduğunda görevleri çalıştırmaya hazırdır. 

## <a name="create-a-job"></a>İş oluştur

Bir havuza sahip olduktan sonra üzerinde çalıştıracak bir iş oluşturun. Batch işi bir veya daha fazla görevin mantıksal grubudur. Bir iş, öncelik gibi görevler arasında ortak olan ayarları ve görevlerin çalıştırılacağı havuzu içerir. Başlangıçta iş hiçbir görev içermez. 

1. Batch hesabı görünümünde **İşler** > **Ekle**'yi seçin. 

2. *myjob* adlı bir **İş Kimliği** girin. **Havuz** menüsünde *mypool*’u seçin. Diğer ayarlar için varsayılan değerleri kabul edin ve **Tamam**'ı seçin.

   ![İş oluştur][job_create]

İş oluşturulduktan sonra **Görevler** sayfası açılır.

## <a name="create-tasks"></a>Görev oluşturma

Şimdi işin içinde çalıştırılacak örnek görevler oluşturun. Genellikle, işlem düğümleri üzerinde çalışmak üzere Batch tarafından kuyruğa alınan ve dağıtılan birden fazla görev oluşturursunuz. Bu örnekte, iki türdeş görev oluşturursunuz. Her görev, bir işlem düğümündeki Batch ortam değişkenlerini görüntülemek için bir komut satırı çalıştırır ve 90 saniye bekler. 

Batch kullandığınızda komut satırı, uygulamanızı veya betiğinizi belirttiğiniz yerdir. Batch, işlem düğümlerine uygulama ve betik dağıtmanın birkaç yolunu sağlar. 

İlk görevi oluşturmak için:

1. **Add (Ekle)** seçeneğini belirleyin.

2. *mytask* adlı bir **Görev Kimliği** girin. 

3. **Komut satırı**’na `cmd /c "set AZ_BATCH & timeout /t 90 > NUL"` girin. Diğer ayarlar için varsayılan değerleri kabul edin ve **Tamam**'ı seçin.

   ![Bir görev oluşturun][task_create]

Bir görev oluşturduktan sonra Batch, görevi havuzda çalışmak üzere kuyruğa alır. Görevi çalıştıracak bir düğüm kullanılabilir olduğunda, görev çalışır.

İkinci bir görev oluşturmak için 1. adıma geri dönün. Farklı bir **Görev Kimliği** ile birlikte aynı komut satırını girin. İlk görev hala çalışıyorsa, Batch havuzdaki diğer düğüm üzerinde ikinci görevi başlatır.

## <a name="view-task-output"></a>Görev çıkışını görüntüleme

Yukarıdaki görev örnekleri birkaç dakika içinde tamamlanır. Tamamlanmış bir görevin çıktısını görüntülemek için **Düğüm üzerindeki dosyalar**'ı ve sonra `stdout.txt` dosyasını seçin. Bu dosya, görevin standart çıkışını gösterir. İçeriği aşağıdakine benzerdir:

![Görev çıkışını görüntüleme][task_output]

İçerik, düğüm üzerinde ayarlanmış Azure Batch ortam değişkenlerini gösterir. Kendi Batch işlerinizi ve görevlerinizi oluşturduğunuzda, görev komut satırlarında bu görev değişkenlerine ve komut satırları tarafından çalıştırılan uygulama ve betiklere başvurabilirsiniz.

## <a name="clean-up-resources"></a>Kaynakları temizleme

Batch öğreticileri ve örnekleri ile devam etmek istiyorsanız, bu hızlı başlangıçta kullanılan Batch hesabını ve bağlı depolama hesabını kullanın. Batch hesabının kendisi için herhangi bir ücret alınmaz.

Zamanlanmış bir iş olmasa bile, düğümler çalışırken havuz için sizden ücret alınır. Havuz artık gerekli değilse silin. Hesap görünümünde **Havuzlar**'ı ve havuzun adını seçin. Ardından **Sil**’i seçin.  Havuzu sildiğinizde düğümler üzerindeki tüm görev çıkışları silinir. 

Artık gerekli olmadığında kaynak grubunu, Batch hesabını ve tüm ilişkili kaynakları silin. Bu işlemi yapmak için Batch hesabına ait kaynak grubunu seçin ve **Kaynak Grubunu Sil**'i seçin.

## <a name="next-steps"></a>Sonraki adımlar

Bu hızlı başlangıçta bir Batch hesabı, Batch havuzu ve Batch işi oluşturdunuz. İş, örnek görevler çalıştırdı ve düğümlerden biri üzerinde oluşturulan çıktıyı görüntülediniz. Batch hizmetinin temel kavramlarını anladıktan sonra, Batch’i daha büyük ölçekte daha gerçekçi iş yükleri ile denemeye hazırsınız. Azure Batch hakkında daha fazla bilgi için Azure Batch öğreticilerine devam edin. 

> [!div class="nextstepaction"]
> [Azure Batch öğreticileri](./tutorial-parallel-dotnet.md)

[marketplace_portal]: ./media/quick-create-portal/marketplace-batch.png

[account_portal]: ./media/quick-create-portal/batch-account-portal.png

[account_keys]: ./media/quick-create-portal/batch-account-keys.png

[pool_os]: ./media/quick-create-portal/pool-operating-system.png

[pool_size]: ./media/quick-create-portal/pool-size.png

[pool_resizing]: ./media/quick-create-portal/pool-resizing.png

[job_create]: ./media/quick-create-portal/job-create.png

[task_create]: ./media/quick-create-portal/task-create.png

[task_output]: ./media/quick-create-portal/task-output.png