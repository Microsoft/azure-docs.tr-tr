---
title: Azure 'da bir Jupyter Not defteri olarak slayt gösterisi sunun
description: Bir Jupyter not defterinde slayt gösterisi modu için hücreleri yapılandırma ve sonra da RISE uzantısını kullanarak slayt gösterisini sunma.
services: app-service
documentationcenter: ''
author: kraigb
manager: barbkess
ms.assetid: c372175b-beb5-4b45-b2f8-34cb06990117
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: 04612dc8892a544397251580b3494badc84e2ad5
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973108"
---
# <a name="run-a-notebook-slideshow"></a>Bir not defteri slayt gösterisi Çalıştır

Azure Notebooks, doğrudan slayt gösterisi olarak bir not defteri sunmanıza olanak tanıyan Jupyter/IPython slayt gösterisi uzantısı (yüksemle) ile önceden yapılandırılmıştır. Bir slayt gösterisinde, genellikle büyük ekranlarda sunum yapmak için uygun bir yazı tipi boyutu kullanılarak birer birer görüntülenir ve ayrı bir demo bilgisayara geçiş yerine kodu çalıştırmaya devam edebilirsiniz.

Aşağıdaki görüntüde, Markaşağı ve kod hücrelerini birlikte görebileceğiniz standart Not defteri görünümü gösterilmektedir:

![Standart görünümde bir not defteri](media/slideshow/slideshow-notebook-view.png)

Bir slayt gösterisi başlattığınızda, ilk hücre tarayıcıyı dolduracak şekilde genişletilir ve sol üst taraftaki **X** **, slayt** gösterisinin çıkar. sol alt köşede klavye kısayolları görüntülenir ve sağ alt taraftaki oklar slaytlar arasında gezinin:

![Slayt gösterisi modundaki bir not defteri](media/slideshow/slideshow-slide-view.png)

Bir slayt için bir not defteri hazırlama iki birincil etkinlik içerir:

1. Markaşağı hücreleri büyük yazı tipleriyle işlendiği için bazı içerikler slayt gösterisinde görünmeyebilir. Böylece, genellikle belirli bir hücredeki metin miktarını sınırlayabilirsiniz; dört ile altı satıra sahip bir üstbilgi genellikle en iyi şekilde kullanılır. Daha fazla metin varsa, bu bilgileri birden çok hücreye ayırın.

2. Slayt gösterisi hücresi araç çubuğunu kullanarak, slayt gösterisinde her hücrenin davranışını yapılandırın. Hücre türleri, gezinti düğmelerinin davranışını belirlenir.

## <a name="the-anatomy-of-a-slideshow"></a>Slayt gösterisinin anatomumu

Rastgele bir not defteri alıp bir slayt gösterisi için kullanıyorsanız, genellikle tüm hücrelerin bir arada olduğunu ve içeriğin büyük bir kısmını tarayıcı penceresinin alt tarafında gizlemiş olduğunu fark edersiniz. Etkili bir sunum yapmak için, slayt gösterisi hücresi araç çubuğunu kullanarak her hücreye bir slayt gösterisi türü atamanız gerekir:

1. **Görünüm** menüsünde **hücre araç çubuğunu**seçin  > **Slayt gösterisi**:

    ![Hücre slayt gösterisi araç çubuğunu açma](media/slideshow/slideshow-view-cell-toolbar.png)

1. Not defterindeki her hücrenin sağ üst köşesinde bir **slayt türü** açılır listesi görünür:

    ![Hücre slayt gösterisi araç çubuğu](media/slideshow/slideshow-cell-toolbar.png)

1. Her hücre için beş türden birini seçin:

    ![Hücre slayt gösterisi türleri](media/slideshow/slideshow-cell-slide-types.png)

    | Slayt türü | Davranış |
    | --- | --- |
    | -(ayarlı değil) | Hücre, genellikle bir slayt gösterisinde istenen etkileri olmayan bir önceki hücreyle birlikte görüntülenir. |
    | Inıza | Hücre, Gezinti denetiminin sol ve sağ okları kullanılarak gezindiği birincil bir slaydır. |
    | Alt slayt | Hücre, bir birincil slaydın "altında", Gezinti denetiminin aşağı okuna gidildiği. Yukarı ok, birincil slayda geri döner. Alt slaytlar, bir sununun ana yolunda atlayabilmeniz gereken ikincil malzemeler için kullanılır, ancak gerekirse kolayca kullanılabilir. |
    | Parçada | Aşağı gezinti oku kullanılırken hücre içeriği önceki slaytın veya alt slaydın bağlamında görünür (yukarı ok kullanılırken bir parça kaldırılır). Kodun bir slaytta görünmesini sağlamak için kod hücresi içeren bir parça kullanabilir veya birden çok parça kullanarak metin madde işaretlerinin tek bir tane görünmesini sağlayabilirsiniz (bir sonraki bölümde örneğe bakın). Parçalar geçerli slaytta oluşturulduğundan, aşırı parçalar tarayıcı penceresinin alt kısmında görünmez. |
    | Atla | Hücre, slayt gösterisinde gösterilmez. |
    | Notlar | Hücre, slayt gösterisinde görünmeyen konuşmacı notları içeriyor. |

1. Başlangıçta, her hücre için **Slayt** seçmek yararlıdır. Ardından, slayt gösterisini çalıştırabilir ve uygun ayarlamaları yapabilirsiniz.

### <a name="example-fragment-cells-for-bullet-items"></a>Örnek: madde işareti öğeleri için parçacık hücreleri

Bir slaytta yer alan madde işaretlerinin tek tek görünmesi için, slayt başlığını **Slayt** türüyle bir markaşağı hücresine yerleştirip, her bir madde işaretini **parça** türüyle ayrı bir markaşağı hücresine yerleştirin:

![Madde işareti öğeleri için birden çok Markaşağı hücre oluşturma örneği](media/slideshow/slideshow-fragments.png)

Slayt gösterisi, tüm madde işaretlerinin aynı hücrede olduğu tarihten daha fazla dikey aralığa sahip parçalar oluşturduğundan, çok sayıda madde işareti öğesi kullanmayabilir.

## <a name="run-the-slideshow"></a>Slayt gösterisini Çalıştır

1. Herhangi bir Markaşağı hücresini düzenlediyseniz, bunları HTML olarak işlemek için çalıştırdığınızdan emin olun, aksi takdirde slayt gösterisinde markaşağı *olarak* görünürler.

1. Her hücre için **Slayt türünü** yapılandırdıktan sonra, slayt gösterisinin başlatılacağı hücreyi seçin, sonra ana araç çubuğundaki **Slayt gösterisini gir/çık** düğmesini seçin:

    ![Ana araç çubuğundaki slayt gösterisini gir/çık düğmesi](media/slideshow/slideshow-start.png)

1. Parçaların yanı sıra parçalar arasında gezinmek için, gezinti denetimindeki sol ve sağ okları kullanın. Denetimdeki metin, *Slide. alt slaytı*temsil eden bir sayı gösterir.

    ![Slayt gösterisi gezinti denetimi](media/slideshow/slideshow-navigation-control.png)

1. Slaytlar ve alt slaytların yanı sıra parçalar arasında gezinmek için, etkinse yukarı ve aşağı okları kullanın:

    ![Alt slaytlar için slayt gösterisi gezinti denetimleri](media/slideshow/slideshow-navigation-control-subslide.png)

1. Kod hücresinde yürütme düğmesini kullanarak kodu çalıştırın; slaytta çıkış görüntülenir:

    ![Kod hücresini çalıştırmak için Yürüt düğmesi](media/slideshow/slideshow-run-code-cell.png)

    ![Slayt gösterisinde kod hücresi çıkışı görüntülenir](media/slideshow/slideshow-run-code-cell-output.png)

    > [!Tip]
    > Hücre çıktısı, bir slayt gösterisinde hücrenin bir parçası olarak değerlendirilir. Bir hücreyi not defterinde veya slayt gösterisi görünümünde çalıştırırsanız, çıkış diğer görünümde de görüntülenir. Çıktıyı temizlemek için  > **geçerli çıktıları** > **clear** komutu (geçerli hücre için **) veya @no__t**-6**tüm çıktılar** > **clear** (tüm hücreler için **) hücresini kullanın**.

1. Slayt gösterisine işiniz bittiğinde, **X** ' i kullanarak not defteri görünümüne geri dönün.

## <a name="next-steps"></a>Sonraki adımlar

- [Nasıl yapılır: projeleri yapılandırma ve yönetme](configure-manage-azure-notebooks-projects.md)
- [Nasıl yapılır: bir not defteri içinden paket yüklemesi](install-packages-jupyter-notebook.md)
- [Nasıl yapılır: veri dosyalarıyla çalışma](work-with-project-data-files.md)
- [Nasıl yapılır: veri kaynaklarına erişme](access-data-resources-jupyter-notebooks.md)
