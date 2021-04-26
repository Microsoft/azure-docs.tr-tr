---
title: Azure Data Factory 'de yinelemeli geliştirme ve hata ayıklama
description: ADF UX 'te yinelemeli olarak Data Factory işlem hatlarını geliştirmeyi ve hata ayıklamanızı öğrenin
ms.date: 02/23/2021
ms.topic: conceptual
ms.service: data-factory
services: data-factory
documentationcenter: ''
ms.workload: data-services
author: kromerm
ms.author: makromer
ms.openlocfilehash: ef47d311f5f096db962ea27792e7871dbf0ef81a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101712972"
---
# <a name="iterative-development-and-debugging-with-azure-data-factory"></a>Azure Data Factory ile yinelemeli geliştirme ve hata ayıklama
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Factory, veri tümleştirme çözümlerinizi geliştirirken Data Factory işlem hatlarını yeniden geliştirmenize ve hata ayıklamanıza olanak tanır. Bu özellikler, çekme isteği oluşturmadan veya bunları Data Factory hizmetinde yayımlamadan önce yaptığınız değişiklikleri test etmeniz için izin verir. 

Bu özelliğin sekiz dakikalık bir girişi ve gösterimi için aşağıdaki videoyu izleyin:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Iterative-development-and-debugging-with-Azure-Data-Factory/player]

## <a name="debugging-a-pipeline"></a>İşlem hattı hatalarını ayıklama

İşlem hattı tuvali 'ni kullanarak yazarınızda, **hata ayıklama** özelliğini kullanarak etkinliklerinizi test edebilirsiniz. Test çalıştırmalarını gerçekleştirdiğinizde, **Hata Ayıkla**' yı seçmeden önce veri fabrikasında yaptığınız değişiklikleri yayımlamanız gerekmez. Bu özellik, Data Factory iş akışını güncelleştirmeden önce değişikliklerin beklendiği gibi çalıştığından emin olmak istediğiniz senaryolarda yararlıdır.

![İşlem hattı tuvalindeki hata ayıklama özelliği](media/iterative-development-debugging/iterative-development-1.png)

İşlem hattı çalışırken, her etkinliğin sonucunu ardışık düzen tuvalinin **Çıkış** sekmesinde görebilirsiniz.

İşlem hattı tuvalinin **Çıkış** penceresinde, test çalıştırmalarının sonuçlarını görüntüleyin.

![İşlem hattı tuvalinin çıkış penceresi](media/iterative-development-debugging/iterative-development-2.png)

Bir test çalıştırması başarılı olduktan sonra, işlem hattınızda daha fazla etkinlik ekleyin ve hata ayıklamaya yinelemeli bir şekilde devam edin. Ayrıca, devam ederken bir test çalıştırmasını **iptal** edebilirsiniz.

> [!IMPORTANT]
> **Hata ayıklamayı** seçtiğinizde işlem hattı çalıştırılır. Örneğin, işlem hattı kopyalama etkinliği içeriyorsa, test çalıştırması verileri kaynaktan hedefe kopyalar. Sonuç olarak, hata ayıklama sırasında kopyalama etkinliklerinizde ve diğer etkinliklerinizde test klasörleri kullanmanızı öneririz. İşlem hattının hata ayıkladıktan sonra, normal işlemlerde kullanmak istediğiniz gerçek klasörlere geçin.

### <a name="setting-breakpoints"></a>Kesme noktalarını ayarlama

Azure Data Factory, işlem hattı tuvalindeki belirli bir etkinliğe ulaşana kadar bir işlem hattından hata ayıklamanıza olanak tanır. Test etmek istediğiniz kadar etkinliğe bir kesme noktası koyun ve **Hata Ayıkla**' yı seçin. Data Factory, testin yalnızca işlem hattı tuvalindeki kesme noktası etkinliğine kadar çalışmasını sağlar. İşlem hattının tamamını test etmek istemediğiniz durumlarda bu *hata ayıklama* işlemi, yalnızca işlem hattının içindeki etkinliklerin bir alt kümesi yararlı olur.

![İşlem hattı tuvalindeki kesme noktaları](media/iterative-development-debugging/iterative-development-3.png)

Kesme noktası ayarlamak için işlem hattı tuvalinde bir öğe seçin. Öğenin sağ üst köşesinde boş bir kırmızı daire olarak bir *hata ayıklama* seçeneği belirir.

![Seçili öğede bir kesme noktası ayarlamadan önce](media/iterative-development-debugging/iterative-development-4.png)

*Hata ayıklamayı Until* seçeneğini belirledikten sonra, kesme noktasının etkin olduğunu göstermek için dolgulu kırmızı bir daireye dönüşür.

![Seçili öğe üzerinde bir kesme noktası ayarladıktan sonra](media/iterative-development-debugging/iterative-development-5.png)

## <a name="monitoring-debug-runs"></a>Hata ayıklama çalıştırmalarını izleme

Bir işlem hattı hata ayıklama çalıştırması çalıştırdığınızda sonuçlar, işlem hattı tuvalinin **Çıkış** penceresinde görünür. Çıkış sekmesi yalnızca geçerli tarayıcı oturumu sırasında gerçekleşen en son çalışmayı içerir. 

![İşlem hattı tuvalinin çıkış penceresi](media/iterative-development-debugging/iterative-development-2.png)

Hata ayıklama çalıştırmalarının geçmiş bir görünümünü görüntülemek veya tüm etkin hata ayıklama çalıştırmalarının bir listesini görmek için **izleyici** deneyimine gidebilirsiniz. 

![Etkin hata ayıklama çalıştırmalarını görüntüle simgesini seçin](media/iterative-development-debugging/view-debug-runs.png)

> [!NOTE]
> Azure Data Factory hizmeti yalnızca 15 gün boyunca hata ayıklama çalıştırma geçmişine devam ettirir. 

## <a name="debugging-mapping-data-flows"></a>Eşleme veri akışları hatalarını ayıklama

Veri akışlarını eşleme, ölçeklendirerek çalışan kod içermeyen veri dönüştürme mantığı oluşturmanıza olanak sağlar. Mantığınızı oluştururken, canlı bir Spark kümesi kullanarak verilerinize etkileşimli olarak çalışmak için bir hata ayıklama oturumu açabilirsiniz. Daha fazla bilgi edinmek için [veri akışı hata ayıklama modunu eşleme](concepts-data-flow-debug-mode.md)konusunu okuyun.

**İzleyici** deneyiminde etkin veri akışı hata ayıklama oturumlarını bir fabrika genelinde izleyebilirsiniz.

![Veri akışı hata ayıklama oturumlarını görüntüle](media/iterative-development-debugging/view-dataflow-debug-sessions.png)

Veri akışı Tasarımcısı 'nda veri önizlemesi ve veri akışlarının işlem hattı hata ayıklaması, küçük verilerle en iyi şekilde çalışacak şekilde tasarlanmıştır. Ancak, mantığınızı büyük miktarlarda verilere karşı bir işlem hattı veya veri akışında test etmeniz gerekiyorsa, hata ayıklama oturumunda kullanılan Azure Integration Runtime boyutunu daha fazla çekirdeğe ve en az genel amaçlı işlem ile artırın.
 
### <a name="debugging-a-pipeline-with-a-data-flow-activity"></a>Veri akışı etkinliğiyle işlem hattında hata ayıklama

Bir veri akışı ile bir hata ayıklama işlem hattı çalıştırıldığında, hangi işlemin kullanılması gerektiğini iki seçeneğe sahip olursunuz. Mevcut bir hata ayıklama kümesini kullanabilir veya veri akışlarınız için yeni bir tam zamanında kümesi çalıştırabilirsiniz.

Mevcut bir hata ayıklama oturumunun kullanılması, küme zaten çalışırken veri akışı başlangıç süresini büyük ölçüde azaltır, ancak birden çok iş aynı anda çalıştırıldığında başarısız olabileceğinden karmaşık veya paralel iş yükleri için önerilmez.

Etkinlik çalışma zamanının kullanılması, her bir veri akışı etkinliğinin tümleştirme çalışma zamanı 'nda belirtilen ayarları kullanarak yeni bir küme oluşturur. Bu, her bir işin yalıtılmasını sağlar ve karmaşık iş yükleri veya performans testi için kullanılmalıdır. Ayrıca, hata ayıklama için kullanılan küme kaynaklarının ek iş isteklerine hizmeti sağlamak üzere bu süre için kullanılabilir olacağı şekilde, Azure IR TTL 'yi de kontrol edebilirsiniz.

> [!NOTE]
> Büyük veri kümeleriyle test olması gereken paralel veya veri akışlarında yürütülen veri akışları ile bir işlem hattına sahipseniz, Data Factory veri akışı etkinliğinizdeki seçtiğiniz Integration Runtime kullanabilmesi için "etkinlik çalışma zamanını kullan" seçeneğini belirleyin. Bu, veri akışlarının birden fazla kümede yürütülmesine izin verir ve paralel veri akışı yürütmelerine uyum sağlayabilir.

![Veri akışı ile işlem hattı çalıştırma](media/iterative-development-debugging/iterative-development-dataflow.png)

## <a name="next-steps"></a>Sonraki adımlar

Değişikliklerinizi test ettikten sonra, [Azure Data Factory ' de sürekli tümleştirme ve dağıtım](continuous-integration-deployment.md)kullanarak bunları daha yüksek ortamlara yükseltin.
