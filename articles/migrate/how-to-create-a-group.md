---
title: Azure geçişi ile değerlendirme için sunucuları gruplayın | Microsoft Docs
description: Azure geçişi hizmeti ile bir değerlendirme çalıştırmadan önce sunucuların nasıl gruplandırılacağını açıklar.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 07/17/2019
ms.openlocfilehash: 0570ed73b86223025b250e269d7e2f358473f004
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104780863"
---
# <a name="create-a-group-for-assessment"></a>Değerlendirme için bir grup oluşturma

Bu makalede, Azure geçişi: bulma ve değerlendirme ile değerlendirme için sunucu gruplarının nasıl oluşturulacağı açıklanır.

[Azure geçişi](migrate-services-overview.md) , Azure 'a geçiş yapmanıza yardımcı olur. Azure geçişi, şirket içi altyapıyı, uygulamaları ve verileri Azure 'a bulmayı, değerlendirmeyi ve geçirmeyi izlemek için merkezi bir merkez sağlar. Hub, değerlendirme ve geçiş için Azure araçları ve ayrıca üçüncü taraf bağımsız yazılım satıcısı (ISV) tekliflerini sağlar. 

## <a name="grouping-servers"></a>Sunucuları gruplandırma

Azure 'a geçiş için uygun olup olmadığını değerlendirmek ve bunlara yönelik Azure boyutlandırma ve maliyet tahminleri almak için sunucuları gruplar halinde toplayın. Grupları oluşturmanın birkaç yolu vardır:

- Birlikte geçirilmesi gereken sunucuları biliyorsanız, Azure geçişi 'nde grubu el ile oluşturabilirsiniz.
- Birlikte gruplanmanız gereken sunucular hakkında emin değilseniz, gruplar oluşturmak için Azure geçişi 'ndeki bağımlılık görselleştirme işlevini kullanabilirsiniz. 

> [!NOTE]
> Bağımlılık görselleştirme işlevselliği Azure Kamu 'da kullanılamaz.

## <a name="create-a-group-manually"></a>El ile bir grup oluşturun

Bir [değerlendirme oluşturduğunuz](how-to-create-assessment.md)sırada bir grup oluşturabilirsiniz.

Bir değerlendirme oluşturma dışında el ile bir grup oluşturmak istiyorsanız aşağıdakileri yapın:

1. Azure geçişi projesi > **genel bakış**' da, **sunucuları değerlendir ve geçir**' e tıklayın. **Azure geçişi: bulma ve değerlendirme** bölümünde, **gruplar** ' a tıklayın.
    - Henüz Azure geçişi: bulma ve değerlendirme aracını eklemediyseniz eklemek için tıklayın. [Daha fazla bilgi edinin](how-to-assess.md).
    - Henüz bir Azure geçişi projesi oluşturmadıysanız [daha fazla bilgi edinin](./create-manage-projects.md).

    ![Grupları seçin](./media/how-to-create-a-group/select-groups.png)

2. **Grup** simgesine tıklayın.
3. **Grup Oluştur**' da bir grup adı belirtin ve **Gereç adı**' nda, sunucu keşfi için kullandığınız Azure geçiş gerecini seçin.
4. Sunucu listesinden > **Oluştur** grubuna eklemek istediğiniz sunucuları seçin.

    ![Grup oluşturma](./media/how-to-create-a-group/create-group.png)

Artık [bir Azure VM değerlendirmesi](how-to-create-assessment.md) veya bir Azure [VMware çözümü (AVS) değerlendirmesi](how-to-create-azure-vmware-solution-assessment.md) veya [bir Azure SQL değerlendirmesi](how-to-create-azure-sql-assessment.md)oluştururken bu grubu kullanabilirsiniz.

## <a name="refine-a-group-with-dependency-mapping"></a>Bağımlılık eşleme ile bir grubu daraltın

Bağımlılık eşleme, sunucular arasında bağımlılıkları görselleştirmenize yardımcı olur. Daha yüksek güvenilirliğe sahip sunucu gruplarını değerlendirmek istediğinizde genellikle bağımlılık eşlemesini kullanırsınız.
- Bir değerlendirme çalıştırmadan önce sunucu bağımlılıklarını çapraz denetlemenize yardımcı olur. 
- Ayrıca, herhangi bir şeyin geri ayrılmamasını sağlayarak ve geçiş sırasında beklenmedik kesintilerden kaçınarak Azure 'a geçişinizi etkin bir şekilde planlamaya de yardımcı olur.
- Birlikte geçirilmesi gereken bağımlı sistemleri bulabilir ve çalışan bir sistemin hala kullanıcılara hizmet verip vermediğini ya da geçiş yerine yetki alma için bir aday olduğunu belirlemeniz gerekir.

Zaten [bağımlılık eşlemeyi ayarladıysanız](how-to-create-group-machine-dependencies.md)ve var olan bir grubu iyileştirmek istiyorsanız aşağıdakileri yapın:

1. **Sunucular** sekmesinde, **Azure geçişi: bulma ve değerlendirme** kutucuğunda, **gruplar**' a tıklayın.
2. İyileştirmek istediğiniz gruba tıklayın.
    - Henüz bağımlılık eşlemesini ayarlamadıysanız, **Bağımlılıklar** sütununda bir **yükleme durumu olması gerekir** . Bağımlılıklarını görselleştirmek istediğiniz her sunucu için **yükleme gerektirir**' a tıklayın. Sunucu bağımlılıklarını eşleştirebilmeniz için, her sunucuya birkaç aracı yükleyebilirsiniz. [Daha fazla bilgi edinin](how-to-create-group-machine-dependencies.md).

        ![Bağımlılık eşlemesi Ekle](./media/how-to-create-a-group/add-dependency-mapping.png)

    - Bağımlılık eşlemesini zaten ayarladıysanız, Grup sayfasında **bağımlılıkları görüntüle** ' ye tıklayarak Grup bağımlılığı eşlemesini açın.

3. **Bağımlılıkları görüntüle**' ye tıkladıktan sonra Grup bağımlılığı eşlemesi aşağıdakileri gösterir:

    - Gelen (istemciler) ve giden (sunucular) bağımlılık aracılarının yüklendiği gruptaki tüm sunuculara ve bu sunuculardan gelen TCP bağlantıları.
    - Bağımlılık aracıları yüklü olmayan bağımlı sunucular, bağlantı noktası numaralarına göre gruplandırılır.
    - Bağımlılık aracıları yüklü bağımlı sunucular ayrı kutular olarak gösterilir.
    - Sunucu içinde çalışan süreçler. İşlem görüntülemek için her bir sunucu kutusunu genişletin.
    - Sunucu Özellikleri (FQDN, işletim sistemi, MAC adresi dahil). Ayrıntıları görüntülemek için her bir sunucu kutusuna tıklayın.

4. Bağımlılıkları seçtiğiniz bir zaman aralığında görüntülemek için başlangıç ve bitiş tarihlerini veya süreyi belirterek zaman aralığını (varsayılan olarak bir saat) değiştirin.

    > [!NOTE]
    > Zaman aralığı bir saate kadar sürebilir. Daha uzun bir aralığa ihtiyacınız varsa, bağımlı verileri daha uzun bir süre [sorgulamak Için Azure izleyici](how-to-create-group-machine-dependencies.md) 'yi kullanın.

5. Gruba eklemek veya gruptan kaldırmak istediğiniz bağımlılıkları tanımladıktan sonra grubu değiştirebilirsiniz. Sunucuyu gruba eklemek veya gruptan kaldırmak için CTRL + tıklama tuşlarını kullanın.

    - Yalnızca keşfedilen sunucuları ekleyebilirsiniz.
    - Sunucu ekleme ve kaldırma, bir grup için geçmiş değerlendirmeleri geçersiz kılar.
    - İsterseniz grubu değiştirirken yeni bir değerlendirme oluşturabilirsiniz.


## <a name="next-steps"></a>Sonraki adımlar

Yüksek güvenilirlikli gruplar oluşturmak için [bağımlılık eşlemeyi](how-to-create-group-machine-dependencies.md) ayarlamayı ve kullanmayı öğrenin.