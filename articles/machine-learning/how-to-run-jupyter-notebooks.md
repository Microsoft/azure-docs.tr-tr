---
title: Çalışma alanınızda Jupyıter not defterlerini çalıştırma
titleSuffix: Azure Machine Learning
description: Jupyter Not defterini Azure Machine Learning Studio 'da çalışma alanınızdan çıkmadan nasıl çalıştıracağınızı öğrenin.
services: machine-learning
author: abeomor
ms.author: osomorog
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.date: 01/19/2021
ms.openlocfilehash: 953b987e908736df703c354067ee27fc27d56073
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106220717"
---
# <a name="run-jupyter-notebooks-in-your-workspace"></a>Çalışma alanınızda Jupyıter not defterlerini çalıştırma

Jupyıter not defterlerinizi Azure Machine Learning Studio 'daki çalışma alanınızda doğrudan çalıştırmayı öğrenin. [Jupi](https://jupyter.org/) veya [Jupyıterlab](https://jupyterlab.readthedocs.io)'ı başlatabilmeniz sırasında, çalışma alanından çıkmadan not defterlerinizi düzenleyebilir ve çalıştırabilirsiniz.

Not defterleri dahil olmak üzere dosyaları oluşturma ve yönetme hakkında bilgi için bkz. [çalışma alanınızdaki dosyaları oluşturma ve yönetme](how-to-manage-files.md).

> [!IMPORTANT]
> (Önizleme) olarak işaretlenen Özellikler bir hizmet düzeyi sözleşmesi olmadan sağlanır ve üretim iş yükleri için önerilmez. Bazı özellikler desteklenmiyor olabileceği gibi özellikleri sınırlandırılmış da olabilir. Daha fazla bilgi için bkz. [Microsoft Azure Önizlemeleri için Ek Kullanım Koşulları](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Önkoşullar

* Azure aboneliği. Azure aboneliğiniz yoksa başlamadan önce [ücretsiz bir hesap](https://aka.ms/AMLFree) oluşturun.
* Machine Learning çalışma alanı. Bkz. [Azure Machine Learning çalışma alanı oluşturma](how-to-manage-workspace.md).

## <a name="edit-a-notebook"></a>Not defterini düzenleme

Bir not defteri düzenlemek için, çalışma alanınızın **Kullanıcı dosyaları** bölümünde yer alan herhangi bir not defterini açın. Düzenlemek istediğiniz hücreye tıklayın.  Bu bölümde hiç not defteriniz yoksa, bkz. çalışma alanınızdaki [dosyaları oluşturma ve yönetme](how-to-manage-files.md).

Bir işlem örneğine bağlanmadan Not defterini düzenleyebilirsiniz.  Not defterindeki hücreleri çalıştırmak istediğinizde, bir işlem örneği seçin veya oluşturun.  Durdurulmuş bir işlem örneği seçerseniz, ilk hücreyi çalıştırdığınızda otomatik olarak başlatılır.

Bir işlem örneği çalışırken, herhangi bir Python not defterinde [IntelliSense](https://code.visualstudio.com/docs/editor/intellisense)tarafından desteklenen kod tamamlamayı da kullanabilirsiniz.

Ayrıca, Not defteri araç çubuğundan Jupyter veya JupyterLab ' i de başlatabilirsiniz.  Azure Machine Learning, Microsoft Desteği sınırının dışında açık kaynak ürünleri olduklarından jupi veya jupi 'nın hata ve hatalarını düzeltir.

## <a name="focus-mode"></a>Odak modu

Etkin sekmelerinize odaklanabilmeniz için, geçerli görünümünüzü genişletmek üzere odak modunu kullanın. Odak modu, Not defterleri dosya gezginini gizler.

1. Odak modunu açmak için Terminal penceresi araç çubuğunda **odak modu** ' nu seçin. Pencere genişenize bağlı olarak araç, araç çubuğinizdeki **...** menü öğesi altında bulunabilir.
1. Odak modundayken **Standart Görünüm '** ü seçerek standart görünüme geri dönün.

    :::image type="content" source="media/how-to-run-jupyter-notebooks/focusmode.gif" alt-text="Odak modunu/standart görünümü değiştirme":::

## <a name="code-completion-intellisense"></a>Kod tamamlama (IntelliSense)

[IntelliSense](https://code.visualstudio.com/docs/editor/intellisense) birçok özellik içeren bir kod tamamlama yardımıdır: liste üyeleri, parametre bilgileri, hızlı bilgi ve tam sözcük. Yalnızca birkaç tuş vuruşu ile şunları yapabilirsiniz:
* Kullanmakta olduğunuz kod hakkında daha fazla bilgi edinin
* Yazmakta olduğunuz parametreleri takip edin
* Özelliklere ve yöntemlere çağrılar ekleyin 

### <a name="insert-code-snippets-preview"></a>Kod parçacıkları Ekle (Önizleme)

IntelliSense kod parçacıklarını tetiklemek için **Ctrl + Space** tuşlarını kullanın.  Eklemek istediğiniz kodu bulmak için önerilere ilerleyin veya yazmaya başlayın.  Kodu ekledikten sonra, kodu kendi kullanıma yönelik olarak özelleştirmek için bağımsız değişkenler aracılığıyla sekmesine geçin.

:::image type="content" source="media/how-to-run-jupyter-notebooks/insert-snippet.gif" alt-text="Kod parçacığı Ekle":::

Bu kod parçacıkları, VS Code not defterinizi açtığınızda kullanılabilir. Kullanılabilir kod parçacıklarının tüm listesi için bkz. [Azure Machine Learning vs Code parçacıkları](https://github.com/Azure/azureml-snippets/blob/main/snippets/snippets.md).

Kod parçacığı panelini açmak için Not defteri araç çubuğunu kullanarak kod parçacıkları listesine gözatabilir ve arama yapabilirsiniz.

:::image type="content" source="media/how-to-run-jupyter-notebooks/open-snippet-panel.png" alt-text="Not defteri araç çubuğunda Kod parçacığı paneli aracını aç":::

Kod parçacıkları panelinden yeni kod parçacıkları eklemek için de bir istek gönderebilirsiniz.

:::image type="content" source="media/how-to-run-jupyter-notebooks/propose-new-snippet.png" alt-text="Kod parçacığı paneli yeni bir kod parçacığı önerme olanağı sağlar":::

## <a name="clean-your-notebook-preview"></a>Not defterinizi Temizleme (Önizleme)

Bir not defteri oluşturma sırasında, genellikle veri araştırma veya hata ayıklama için kullandığınız hücrelerle birlikte sona erdir demektir. *Toplama* özelliği, bu yabancı hücreler olmadan temiz bir not defteri oluşturmanıza yardımcı olur.

1. Tüm Not defteri hücrelerinizi çalıştırın.
1. Yeni Not defterinin çalıştırmasını istediğiniz kodu içeren hücreyi seçin. Örneğin, bir deneme gönderen kod veya belki de bir modeli kaydeden kod.
1. Hücre araç çubuğunda görüntülenen **topla** simgesini seçin.
    :::image type="content" source="media/how-to-run-jupyter-notebooks/gather.png" alt-text="Ekran görüntüsü: toplama simgesini seçin":::
1. Yeni "toplanan" Not defteriniz için ad girin.  

Yeni Not defteri, toplama için seçtiğiniz hücreyle aynı sonuçları üretmek için gereken tüm hücrelere sahip yalnızca kod hücrelerini içerir.

## <a name="save-and-checkpoint-a-notebook"></a>Not defteri kaydetme ve kontrol noktası

Azure Machine Learning, bir *ıpynb* dosyası oluşturduğunuzda bir denetim noktası dosyası oluşturur.

Not defteri araç çubuğunda, menüyü seçin ve ardından not defterini el ile kaydetmek için **&gt; Kaydet ve kontrol noktası dosya** ve not defteriyle ilişkili bir kontrol noktası dosyası eklenir.

:::image type="content" source="media/how-to-run-jupyter-notebooks/file-save.png" alt-text="Not defteri araç çubuğunda kaydetme aracının ekran görüntüsü":::

Her bir not defteri, her 30 saniyede bir otomatik kaydedilir. Otomatik kaydetme, denetim noktası dosyasını değil yalnızca ilk *ipynb* dosyasını güncelleştirir.
 
Adlandırılmış bir kontrol noktası oluşturmak ve Not defterini kaydedilmiş bir denetim noktasına dönüştürmek için Not defteri menüsünde **kontrol noktaları** ' nı seçin.

## <a name="export-a-notebook"></a>Bir not defterini dışarı aktarma

Not defteri araç çubuğunda, menüyü seçin ve ardından **olarak dışa aktar** ' ı desteklenen türlerden herhangi biri olarak dışa aktarın:

* Not Defteri
* Python
* HTML
* LaTeX

:::image type="content" source="media/how-to-run-jupyter-notebooks/export-notebook.png" alt-text="Bir not defterini bilgisayarınıza aktarma":::

İçe aktarılmış dosya bilgisayarınıza kaydedilir.

## <a name="run-a-notebook-or-python-script"></a>Bir not defteri veya Python betiği çalıştırma

Bir not defteri veya Python betiği çalıştırmak için önce çalışan bir [işlem örneğine](concept-compute-instance.md)bağlanırsınız.

* İşlem örneğiniz yoksa, oluşturmak için aşağıdaki adımları kullanın:

    1. Not defteri veya betik araç çubuğunda, Işlem açılan menüsünde **+ Yeni işlem**' i seçin. Ekran boyutunuza bağlı olarak, bu, bir **...** menüsü altında bulunabilir.
        :::image type="content" source="media/how-to-run-jupyter-notebooks/new-compute.png" alt-text="Yeni işlem oluştur":::
    1. Işlem adını adlandırın ve bir **sanal makine boyutu** seçin. 
    1. **Oluştur**’u seçin.
    1. İşlem örneği dosyaya otomatik olarak bağlanır.  Artık işlem örneğinin solundaki aracı kullanarak not defteri hücrelerini veya Python betiğini çalıştırabilirsiniz.

* Durdurulmuş bir işlem örneğiniz varsa, işlem açılan listesinin sağ tarafındaki  **Işlem Başlat** ' ı seçin. Ekran boyutunuza bağlı olarak, bu, bir **...** menüsü altında bulunabilir.

    :::image type="content" source="media/how-to-run-jupyter-notebooks/start-compute.png" alt-text="İşlem örneğini Başlat":::

Yalnızca sizin oluşturduğunuz işlem örneklerini görebilir ve kullanabilirsiniz.  **Kullanıcı DOSYALARıNıZ** VM 'den ayrı olarak depolanır ve çalışma alanındaki tüm işlem örnekleri arasında paylaşılır.

### <a name="view-logs-and-output"></a>Günlükleri ve çıktıyı görüntüleme

Çalışma ve günlüklerin ilerlemesini görüntülemek için [Not defteri pencere](/python/api/azureml-widgets/azureml.widgets) öğelerini kullanın. Pencere öğesi zaman uyumsuzdur ve eğitim bitene kadar güncelleştirmeler sağlar. Azure Machine Learning pencere öğeleri Jupyıter ve JupterLab içinde de desteklenir.

:::image type="content" source="media/how-to-run-jupyter-notebooks/jupyter-widget.png" alt-text="Ekran görüntüsü: Jupyter Not defteri pencere öğesi ":::

## <a name="explore-variables-in-the-notebook"></a>Not defterindeki değişkenleri keşfet

Not defteri araç çubuğunda, Not defterinizde oluşturulmuş tüm değişkenlerin adını, türünü, uzunluğunu ve örnek değerlerini göstermek için **değişken gezgin** aracını kullanın.

:::image type="content" source="media/how-to-run-jupyter-notebooks/variable-explorer.png" alt-text="Ekran görüntüsü: değişken gezgin aracı":::

Değişken Gezgini penceresini göstermek için aracı seçin.

:::image type="content" source="media/how-to-run-jupyter-notebooks/variable-explorer-window.png" alt-text="Ekran görüntüsü: değişken Gezgin penceresi":::

## <a name="navigate-with-a-toc"></a>TOC ile gezinme

Not defteri araç çubuğunda, içindekiler tablosunu göstermek veya gizlemek için  **Içindekiler tablosu** aracını kullanın.  İçindekiler tablosuna eklemek için başlık ile bir markaşağı hücresi başlatın. Not defterindeki bir hücreye kaydırmak için tablodaki bir girdiye tıklayın.  

:::image type="content" source="media/how-to-run-jupyter-notebooks/table-of-contents.png" alt-text="Ekran görüntüsü: not defterindeki içindekiler tablosu":::

## <a name="change-the-notebook-environment"></a>Not defteri ortamını değiştirme

Not defteri araç çubuğu, not defterinizin çalıştırıldığı ortamı değiştirmenize izin verir.  

Bu eylemler Not defteri durumunu veya not defterindeki değişkenlerin değerlerini değiştirmez:

|Eylem  |Sonuç  |
|---------|---------| --------|
|Çekirdeği durdur     |  Çalışan tüm hücreleri sonlandırır. Bir hücreyi çalıştırmak, çekirdeği otomatik olarak yeniden başlatır. |
|Başka bir çalışma alanı bölümüne git     |     Çalışan hücreler durdurulur. |

Bu eylemler, Not defteri durumunu sıfırlayacaktır ve not defterindeki tüm değişkenleri sıfırlar.

|Eylem  |Sonuç  |
|---------|---------| --------|
| Çekirdeği değiştirme | Not defteri yeni çekirdek kullanır |
| İşlem değiştirme    |     Not Defteri otomatik olarak yeni işlem kullanır. |
| İşlem sıfırlama | Bir hücreyi çalıştırmaya çalıştığınızda yeniden başlar |
| İşlem durdur     |    Hiçbir hücre çalıştırılmayacak  |
| Jupyter veya JupyterLab 'da Not defteri 'ni açın     |    Yeni bir sekmede açılan not defteri.  |

## <a name="add-new-kernels"></a>Yeni çekirdekler Ekle

Yeni çekirdekler oluşturmak ve işlem örneğinize eklemek için [terminali kullanın](how-to-access-terminal.md#add-new-kernels) . Not defteri, bağlı işlem örneğinde yüklü olan tüm Jupyter çekirdekler 'leri otomatik olarak bulur.

Yüklü olan çekirdekler üzerinde değişiklik yapmak için sağdaki çekirdek açılan listesini kullanın.  


### <a name="status-indicators"></a>Durum göstergeleri

**İşlem** açılan listesinin yanındaki gösterge durumunu gösterir.  Durum, açılan listede de gösterilir.  

|Renk |İşlem durumu |
|---------|---------| 
| Yeşil | İşlem çalışıyor |
| Kırmızı |İşlem başarısız oldu | 
| Siyahi | İşlem durduruldu |
|  Açık mavi |İşlem oluşturma, başlatma, yeniden başlatma, kurulum |
|  Tonlamalı |İşlem silme, durdurma |

**Çekirdek** açılan listesinin yanındaki gösterge durumunu gösterir.

|Renk |Çekirdek durumu |
|---------|---------|
|  Yeşil |Çekirdek bağlı, boşta, meşgul|
|  Tonlamalı |Çekirdek bağlı değil |

## <a name="find-compute-details"></a>İşlem ayrıntılarını bul

[Studio](https://ml.azure.com)'daki **işlem** sayfasında işlem örneklerinizin ayrıntılarını bulun.

## <a name="useful-keyboard-shortcuts"></a>Faydalı klavye kısayolları
Jupi not defterlerine benzer şekilde, Azure Machine Learning Studio Not defterleri de kalıcı bir kullanıcı arabirimine sahiptir. Klavye, Not defteri hücresinin bulunduğu moda bağlı olarak farklı şeyler yapar. Azure Machine Learning Studio Not defterleri, belirli bir kod hücresi için aşağıdaki iki modu destekler: komut modu ve düzenleme modu.

### <a name="command-mode-shortcuts"></a>Komut modu kısayolları

Bir hücre, yazmanızı isteyen bir metin imleci olmadığında komut modundadır. Bir hücre komut modundayken, Not defterini tek tek hücrelere değil, bir bütün olarak düzenleyebilirsiniz. `ESC`Bir hücrenin düzenleyici alanının dışında seçim yapmak için fare tuşuna basarak veya fareyi kullanarak komut moduna girin.  Etkin hücrenin sol kenarlığı mavi ve Solid, **Çalıştır** düğmesi ise mavi.

   :::image type="content" source="media/how-to-run-jupyter-notebooks/command-mode.png" alt-text="Komut modundaki Not defteri hücresi ":::

| Kısayol                      | Description                          |
| ----------------------------- | ------------------------------------|
| Enter                         | Düzenleme moduna girme             |        
| Shift + Enter                 | Hücreyi Çalıştır, aşağıdan seçin         |     
| Denetim/komut + ENTER       | Çalışma hücresi                            |
| Alt + Enter                   | Hücreyi Çalıştır, aşağıya kod hücresi Ekle    |
| Denetim/komut + alt + ENTER | Hücreyi Çalıştır, aşağı doğru markbelow hücresi Ekle|
| Alt + R                       | Tümünü Çalıştır      |                       
| Y                             | Hücreyi koda Dönüştür    |                         
| M                             | Hücreyi markaşağı Dönüştür  |                       
| Yukarı/K                          | Üstteki hücreyi seçin    |               
| Aşağı/J                        | Aşağıdan hücre seçin    |               
| A                             | Yukarıdaki kod hücresini Ekle  |            
| B                             | Aşağıya kod hücresi Ekle   |           
| Denetim/komut + Shift + A   | Yukarıya markaşağı hücresi Ekle    |      
| Denetim/komut + SHIFT + B   | Aşağı markbelow hücresi Ekle   |       
| X                             | Seçili hücreyi kes    |               
| C                             | Seçili hücreyi Kopyala   |               
| SHIFT + V                     | Seçili hücreyi yukarıya Yapıştır           |
| V                             | Seçili hücreyi aşağı Yapıştır    |       
| D D                           | Seçili hücreyi Sil|                
| O                             | Çıkışı geç         |              
| SHIFT + O                     | Çıkış kaydırmayı aç   |          
| Ben                           | Kesme çekirdeği |                   
| 0 0                           | Çekirdeği yeniden Başlat |                     
| SHIFT + boşluk                 | Yukarı kaydır  |                         
| Alan                         | Aşağı kaydır|
| Tab                           | Odağı sonraki odaksız öğeye Değiştir (sekme tuzağı devre dışı bırakıldığında)|
| Denetim/komut + S           | Not defterini Kaydet |                      
| 1                             | H1 olarak değiştir|                       
| 2                             | H2 olarak değiştir|                        
| 3                             | H3 olarak değiştir|                        
| 4                             | H4 olarak değiştir |                       
| 5                             | H5 olarak değiştir |                       
| 6                             | H6 olarak değiştir |                       

### <a name="edit-mode-shortcuts"></a>Düzenleme modu kısayolları

Düzenleme modu, düzenleyici alanına yazmanızı isteyen bir metin imlece belirtilir. Bir hücre düzenleme modundayken, hücreye yazabilirsiniz. `Enter`Fareyle sürükleyerek veya bir hücrenin düzenleyici alanında seçim yaparak düzenleme modunu girin. Etkin hücrenin sol kenarlığı yeşil ve taranmış ve **Çalıştır** düğmesi yeşil olur. Ayrıca, imleç isteminin hücrede düzenleme modunda görürsünüz.

   :::image type="content" source="media/how-to-run-jupyter-notebooks/edit-mode.png" alt-text="Düzenleme modundaki Not defteri hücresi":::

Aşağıdaki tuş vuruşu kısayollarını kullanarak, düzenleme modundayken Azure Machine Learning Not defterlerindeki kodu daha kolay bir şekilde gezinebilir ve çalıştırabilirsiniz.

| Kısayol                      | Description|                                     
| ----------------------------- | ----------------------------------------------- |
| Esc                        | Komut moduna gir|  
| Denetim/komut + boşluk       | IntelliSense 'i etkinleştir |
| Shift + Enter                 | Hücreyi Çalıştır, aşağıdan seçin |                         
| Denetim/komut + ENTER       | Çalışma hücresi  |                                      
| Alt + Enter                   | Hücreyi Çalıştır, aşağıya kod hücresi Ekle  |              
| Denetim/komut + alt + ENTER | Hücreyi Çalıştır, aşağı doğru markbelow hücresi Ekle  |          
| Alt + R                       | Tüm hücreleri Çalıştır     |                              
| Yukarı                            | İmleci yukarı veya önceki hücreyi taşıyın    |             
| Aşağı                          | İmleci aşağı veya sonraki hücreyi taşı |                  
| Denetim/komut + S           | Not defterini Kaydet   |                                
| Denetim/komut + yukarı          | Hücre başlangıcına git   |                             
| Denetim/komut + aşağı        | Hücre sonuna git |                                 
| Tab                           | Kod tamamlama veya girintileme (sekme tuzağı etkinse) |
| Denetim/komut + d           | Sekme yakalamayı etkinleştir/devre dışı bırak  |                       
| Denetim/komut +]           | Leyebilirsiniz |                                         
| Denetim/komut + [           | Girintiyi geri al  |                                        
| Control/Command + A           | Tümünü seç|                                      
| Denetim/komut + Z           | Geri Al |                                           
| Denetim/komut + SHIFT + Z   | Yinele |                                           
| Denetim/komut + Y           | Yinele |                                           
| Denetim/komut + giriş sayfası        | Hücre başlangıcına git|                                
| Denetim/komut + End         | Hücre sonuna git   |                               
| Denetim/komut + sol        | Bir kelimeyi sola git |                               
| Denetim/komut + sağ       | Bir sözcüğe sağ git |                              
| Denetim/komut + geri al   | Önceki kelimeyi Sil |                             
| Denetim/komut + Sil      | Sonra sözcüğü Sil |                              
| Denetim/komut +/           | Hücrede açıklama aç

## <a name="troubleshooting"></a>Sorun giderme

* Bir not defterine bağlanamıyorsanız, Web yuva iletişiminin devre dışı **bırakılmadığından** emin olun. İşlem örneği jupi işlevinin çalışması için Web yuva iletişiminin etkinleştirilmesi gerekir. Ağınızın, *. instances.azureml.net ve *. instances.azureml.ms ile WebSocket bağlantılarına izin verdiğinden emin olun. 
* İşlem örneği bir özel bağlantı çalışma alanında dağıtıldığında, yalnızca [sanal ağ içinden erişilebilir](./how-to-secure-training-vnet.md#compute-instance). Özel DNS veya hosts dosyası kullanıyorsanız lütfen < örnek adı >. < > bölgesi için özel IP adresi çalışma alanı özel uç noktası ile bir giriş ekleyin. Daha fazla bilgi için bkz. [özel DNS](./how-to-custom-dns.md?tabs=azure-cli) makalesi.

    
## <a name="next-steps"></a>Sonraki adımlar

* [İlk denemenizi çalıştırın](tutorial-1st-experiment-sdk-train.md)
* [Dosya depolama alanınızı anlık görüntülerle yedekleyin](../storage/files/storage-snapshots-files.md)
* [Güvenli ortamlarda çalışma](./how-to-secure-training-vnet.md#compute-instance)