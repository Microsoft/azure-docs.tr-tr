---
title: Azure geçişi ile değerlendirme için makineleri gruplandırın | Microsoft Docs
description: Azure geçişi hizmeti ile bir değerlendirme çalıştırmadan önce makinelerin nasıl gruplandırılacağını açıklar.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/17/2019
ms.author: hamusa
ms.openlocfilehash: 13c640d25265b2663520ef7ab203b0b0a33829e2
ms.sourcegitcommit: af58483a9c574a10edc546f2737939a93af87b73
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 07/17/2019
ms.locfileid: "68301693"
---
# <a name="create-a-group-for-assessment"></a>Değerlendirme için bir grup oluşturma

Bu makalede, Azure geçişi ile değerlendirme için makine gruplarının nasıl oluşturulacağı açıklanır: Sunucu değerlendirmesi.

[Azure geçişi](migrate-services-overview.md) , Azure 'a geçiş yapmanıza yardımcı olur. Azure geçişi, şirket içi altyapıyı, uygulamaları ve verileri Azure 'a bulmayı, değerlendirmeyi ve geçirmeyi izlemek için merkezi bir merkez sağlar. Hub, değerlendirme ve geçiş için Azure araçları ve ayrıca üçüncü taraf bağımsız yazılım satıcısı (ISV) tekliflerini sağlar. 

## <a name="grouping-machines"></a>Makineleri gruplandırma

Azure 'a geçiş için uygun olup olmadığını değerlendirmek ve bunlara yönelik Azure boyutlandırma ve maliyet tahminleri almak için makineleri gruplara toplayın. Grupları oluşturmanın birkaç yolu vardır:

- Birlikte geçirilmesi gereken makineleri biliyorsanız, Azure geçişi 'nde grubu el ile oluşturabilirsiniz.
- Birlikte gruplanmanız gereken makineler hakkında emin değilseniz, gruplar oluşturmak için Azure geçişi 'ndeki bağımlılık görselleştirme işlevini kullanabilirsiniz. 

> [!NOTE]
> Bağımlılık görselleştirme işlevselliği Azure Kamu 'da kullanılamaz.

## <a name="create-a-group-manually"></a>El ile bir grup oluşturun

Bir [değerlendirme oluşturduğunuz](how-to-create-assessment.md)sırada bir grup oluşturabilirsiniz.

Bir değerlendirme oluşturma dışında el ile bir grup oluşturmak istiyorsanız aşağıdakileri yapın:

1. Azure geçişi projesi > **genel bakış**' da, **sunucuları değerlendir ve geçir**' e tıklayın. Azure **geçişi 'nde: Sunucu değerlendirmesi**, **gruplar** ' a tıklayın.
    - Henüz Azure geçişini eklemediyseniz: Sunucu değerlendirmesi Aracı, eklemek için tıklayın. [Daha fazla bilgi edinin](how-to-assess.md).
    - Henüz bir Azure geçişi projesi oluşturmadıysanız [daha fazla bilgi edinin](how-to-add-tool-first-time.md).

    ![Grupları seçin](./media/how-to-create-a-group/select-groups.png)

2. **Grup** simgesine tıklayın.
3. **Grup Oluştur**' da bir grup adı belirtin ve **Gereç adı**' nda makine bulma için kullandığınız Azure geçiş gerecini seçin.
1. Makine listesinden, gruba eklemek istediğiniz makineleri seçin > **Oluştur**' a tıklayın.

    ![Grup Oluştur](./media/how-to-create-a-group/create-group.png)

Bu grubu artık [bir değerlendirme oluştururken](how-to-create-assessment.md)kullanabilirsiniz.

## <a name="refine-a-group-with-dependency-mapping"></a>Bağımlılık eşleme ile bir grubu daraltın

Bağımlılık eşleme, makineler arasında bağımlılıkları görselleştirmenize yardımcı olur. Daha yüksek güvenilirliğe sahip makine gruplarını değerlendirmek istediğinizde genellikle bağımlılık eşlemesini kullanırsınız.
- Bir değerlendirme çalıştırmadan önce makine bağımlılıklarını çapraz denetlemenize yardımcı olur. 
- Ayrıca, herhangi bir şeyin geri ayrılmamasını sağlayarak ve geçiş sırasında beklenmedik kesintilerden kaçınarak Azure 'a geçişinizi etkin bir şekilde planlamaya de yardımcı olur.
- Birlikte geçirilmesi gereken bağımlı sistemleri bulabilir ve çalışan bir sistemin hala kullanıcılara hizmet verip vermediğini ya da geçiş yerine yetki alma için bir aday olduğunu belirlemeniz gerekir.

Zaten [bağımlılık eşlemeyi ayarladıysanız](how-to-create-group-machine-dependencies.md)ve var olan bir grubu iyileştirmek istiyorsanız aşağıdakileri yapın:

1. **Sunucular** sekmesinde, **Azure geçişi: Sunucu değerlendirmesi** kutucuğu, **gruplar**' a tıklayın.
2. İyileştirmek istediğiniz gruba tıklayın.
    - Henüz bağımlılık eşlemesini ayarlamadıysanız, **Bağımlılıklar** sütununda bir **yükleme durumu olması gerekir** . Bağımlılıklarını görselleştirmek istediğiniz her VM için **yükleme gerektirir**' a tıklayın. Makine bağımlılıklarını eşleştirebilmeniz için, her bir VM 'ye birkaç aracı yükleyebilirsiniz. [Daha fazla bilgi edinin](how-to-create-group-machine-dependencies.md).

        ![Bağımlılık eşlemesi Ekle](./media/how-to-create-a-group/add-dependency-mapping.png)

    - Bağımlılık eşlemesini zaten ayarladıysanız, Grup sayfasında **bağımlılıkları görüntüle** ' ye tıklayarak Grup bağımlılığı eşlemesini açın.

3. **Bağımlılıkları görüntüle**' ye tıkladıktan sonra Grup bağımlılığı eşlemesi aşağıdakileri gösterir:

    - Gelen (istemciler) ve giden (sunucular) bağımlılık aracılarının yüklendiği gruptaki tüm makinelere ve bu makinelerden gelen TCP bağlantıları.
    - Bağımlılık aracıları yüklü olmayan bağımlı makineler, bağlantı noktası numaralarına göre gruplandırılır.
    - Bağımlılık aracıları yüklü bağımlı makineler ayrı kutular olarak gösterilir.
    - Makine içinde çalışan süreçler. Her makine kutusunu genişleterek süreçlerini görüntüleyin.
    - Makine Özellikleri (FQDN, işletim sistemi, MAC adresi dahil). Ayrıntıları görüntülemek için her bir makine kutusuna tıklayın.

4. Bağımlılıkları seçtiğiniz bir zaman aralığında görüntülemek için başlangıç ve bitiş tarihlerini veya süreyi belirterek zaman aralığını (varsayılan olarak bir saat) değiştirin.

    > [!NOTE]
    > Zaman aralığı bir saate kadar sürebilir. Daha uzun bir aralığa ihtiyacınız varsa, bağımlı verileri daha uzun bir süre [sorgulamak Için Azure izleyici](how-to-create-group-machine-dependencies.md) 'yi kullanın.

5. Gruba eklemek veya gruptan kaldırmak istediğiniz bağımlılıkları tanımladıktan sonra grubu değiştirebilirsiniz. Grubu gruba eklemek veya gruptan makine kaldırmak için CTRL + tıklama tuşlarını kullanın.

    - Yalnızca keşfedilen makineler ekleyebilirsiniz.
    - Makine ekleme ve kaldırma, bir grup için geçmiş değerlendirmeleri geçersiz kılar.
    - İsterseniz grubu değiştirirken yeni bir değerlendirme oluşturabilirsiniz.


## <a name="next-steps"></a>Sonraki adımlar

Yüksek güvenilirlikli gruplar oluşturmak için [bağımlılık eşlemeyi](how-to-create-group-machine-dependencies.md) ayarlamayı ve kullanmayı öğrenin.

