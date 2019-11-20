---
title: 'K-, kümeleme: modül başvurusu'
titleSuffix: Azure Machine Learning
description: Kümeleme modellerini eğitmek için Azure Machine Learning K-anlamı kümeleme modülünü nasıl kullanacağınızı öğrenin.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/06/2019
ms.openlocfilehash: 4634519f55582a3184472d28acfd98fa849be86a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73497759"
---
# <a name="module-k-means-clustering"></a>Modül: K-kümeleme anlamına gelir

Bu makalede, eğitilen bir kümeleme modeli oluşturmak için Azure Machine Learning tasarımcısında (Önizleme) *k-bit kümeleme* modülünün nasıl kullanılacağı açıklanır. 
 
K-anlamı en basit ve en *iyi bilinen öğrenme* algoritmalarından biridir. Algoritmayı çeşitli makine öğrenimi görevleri için kullanabilirsiniz, örneğin: 

* [Olağan dışı veriler algılanıyor](https://msdn.microsoft.com/magazine/jj891054.aspx).
* Kümeleme metin belgeleri.
* Diğer sınıflandırma veya regresyon yöntemlerini kullanmadan önce veri kümelerini çözümleme. 

Bir kümeleme modeli oluşturmak için şunları yapın:

* Bu modülü ardışık düzene ekleyin.
* Bir veri kümesini bağlayın.
* Tahmin ettiğiniz küme sayısı, küme oluştururken kullanılacak mesafe ölçümü ve benzeri parametreleri ayarlayın. 
  
Modül hiper parametrelerini yapılandırdıktan sonra, eğitilen modeli [tren Kümelemesi modeline](train-clustering-model.md)bağlayın. K-anlamı algoritması, denetimli bir öğrenme yöntemi olduğundan, bir etiket sütunu isteğe bağlıdır. 

+ Verileriniz bir etiket içeriyorsa, kümelerin seçimine kılavuzluk etmek ve modeli iyileştirmek için etiket değerlerini kullanabilirsiniz. 

+ Verilerinizde etiket yoksa, algoritma yalnızca verileri temel alan olası kategorileri temsil eden kümeler oluşturur.  

##  <a name="understand-k-means-clustering"></a>K-anlama, kümeleme
 
Genellikle kümeleme, bir veri kümesindeki durumları benzer özelliklere sahip kümeler halinde gruplamak için yinelemeli teknikler kullanır. Bu gruplandırmalar, verileri keşfetmek, verilerdeki bozuklukları belirlemek ve tahmin yapmak için son olarak faydalıdır. Kümeleme modelleri Ayrıca, göz atma veya basit gözlemlemeye göre mantıksal olarak türeteceğiniz bir veri kümesindeki ilişkileri tanımlamanızı da yardımcı olabilir. Bu nedenlerden dolayı kümeleme genellikle makine öğrenimi görevlerinin erken aşamalarında kullanılır, verileri keşfeder ve beklenmeyen correlations 'ı bulur.  
  
 K-Ortalamalar yöntemini kullanarak bir kümeleme modeli yapılandırdığınızda, modelde istediğiniz *centroıd* sayısını gösteren bir hedef numarası ( *k* ) belirtmeniz gerekir. Centroıd, her kümenin temsili olan bir noktasıdır. K-Ortalamalar algoritması, her bir gelen veri noktasını kümelerden birine atar ve bu da kare içi Karelerin toplamını en aza indirir. 
 
Eğitim verilerini işlediğinde, K-Ortalamalar algoritması rastgele seçilmiş centroıd 'ler kümesiyle başlar. Centroıds, kümeler için başlangıç noktaları olarak görev yapar ve konumlarını yinelemeli olarak daraltmak için Lloyd algoritması uygular. K-anlamı algoritması, bu koşullardan bir veya daha fazlasını karşıladığında kümeleri oluşturmayı ve iyileştirmasını durduruyor:  
  
-   Centroıds, tek tek noktaların küme atamalarının artık değişmeyeceği ve algoritmanın bir çözüme yakınsamış olduğu anlamına gelir.  
  
-   Algoritma, belirtilen sayıda yineleme çalıştırmayı tamamladı.  
  
 Eğitim aşamasını tamamladıktan sonra, K-ortalamalar algoritmasını kullanarak bulduğunuz kümelerden birine yeni durumlar atamak için [verileri kümeye ata](assign-data-to-clusters.md) modülünü kullanırsınız. Yeni durum ve her kümenin centroıd arasındaki mesafeyi hesaplayarak küme atamasını gerçekleştirirsiniz. Her yeni durum, en yakın centroıd ile kümeye atanır.  

## <a name="configure-the-k-means-clustering-module"></a>K-anlamı kümeleme modülünü yapılandırma
  
1.  İşlem hattınızda **K-anlamı kümeleme** modülünü ekleyin.  
  
2.  Modelin nasıl eğitilme etmek istediğinizi belirtmek için, **eğitmen modunu oluştur** seçeneğini belirleyin.  
  
    -   **Tek parametre**: kümeleme modelinde kullanmak istediğiniz tam parametreleri biliyorsanız bağımsız değişken olarak belirli bir değer kümesi sağlayabilirsiniz.  
  
3.  **Centroıds sayısı**için, algoritmanın başlamasını istediğiniz küme sayısını yazın.  
  
     Modelin tam olarak bu sayıda küme üretmesi garanti edilmez. Algoritma, bu sayıda veri noktasıyla başlar ve en iyi yapılandırmayı bulmak için yinelenir.  
  
4.  Özellikler **başlatma** , ilk küme yapılandırmasını tanımlamak için kullanılan algoritmayı belirtmek için kullanılır.  
  
    -   **Ilk N**: veri noktalarının bazı ilk sayısı, veri kümesinden seçilir ve ilk anlamı olarak kullanılır. 
    
         Bu yöntem, *Forgy yöntemi*olarak da adlandırılır.  
  
    -   **Rastgele**: algoritma bir kümeye rastgele bir veri noktası koyar ve sonra kümenin Rastgele atanan noktalarının centroıd değeri olacak şekilde ilk ortalaması hesaplar. 

         Bu yöntem, *rastgele bölüm* yöntemi olarak da adlandırılır.  
  
    -   **K-Ortalamalar + +** : Bu, kümeleri başlatmak için varsayılan yöntemdir.  
  
         **K-ortalamalar + +** algoritması, standart K-Ortalamalar algoritması tarafından zayıf kümelemeyi önlemek Için David Arthur ve Sergei Vassilvıtskii tarafından 2007 ' de önerdi. **K-** ilk küme merkezlerini seçmek için farklı bir yöntem kullanarak standart K-Ortalamalar üzerinde + + artar.  
  
    
5.  **Rastgele sayı çekirdek**için, isteğe bağlı olarak, küme başlatma için çekirdek olarak kullanılacak bir değer yazın. Bu değer, küme seçiminde önemli bir etkiye sahip olabilir.  
  
6.  **Ölçüm**için, küme vektörlerine veya yeni veri noktalarıyla rastgele seçilmiş centroıd arasındaki mesafeyi ölçmek için kullanılacak işlevi seçin. Azure Machine Learning aşağıdaki küme uzaklık ölçümlerini destekler:  
  
    -   **Euclidean**: Euclidean, genellikle K-anlamı Kümelemesi için küme dağılım ölçüsü olarak kullanılır. Bu ölçüm, noktaları ve centroıd 'ler arasındaki ortalama mesafeyi en aza indirecek için tercih edilir.
  
7.  **Yinelemeler**için, algoritmanın, centroıd 'leri seçimini sonlandırmadan önce eğitim verilerini kaç kez yinelemelidir? yazın.  
  
     Bu parametreyi, eğitim süresine göre doğruluğu dengelemek için ayarlayabilirsiniz.  
  
8.  **Etiket Atama modu**için, veri kümesinde varsa, bir etiket sütununun nasıl işleneceğini belirten bir seçenek belirleyin.  
  
     K-kümeleme, denetimli bir makine öğrenimi yöntemi olduğu için Etiketler isteğe bağlıdır. Ancak, veri kümeniz zaten bir etiket sütunu içeriyorsa, kümelerin seçimine yol göstermek için bu değerleri kullanabilir veya değerlerin yok sayılmasını belirtebilirsiniz.  
  
    -   **Etiket sütununu yoksay**: etiket sütunundaki değerler yok sayılır ve model oluşturulurken kullanılmaz.
  
    -   **Eksik değerleri doldur**: Etiket sütun değerleri, kümeleri oluşturmaya yardımcı olacak özellikler olarak kullanılır. Herhangi bir satırda etiket eksikse, bu değer diğer özellikler kullanılarak belirlenir.  
  
    -   **En yakın üzerine yaz**: Etiket sütun değerleri, geçerli centroıd 'ye en yakın noktanın etiketi kullanılarak tahmin edilen etiket değerleriyle değiştirilmiştir.  

8.  Eğitim öncesi özellikleri normalleştirmek istiyorsanız **özellikleri Normalleştir** seçeneğini belirleyin.
  
     Normalleştirme uygularsanız, öğreticmadan önce veri noktaları MinMaxNormalizer tarafından `[0,1]` normalleştirilir.

10. Modeli eğitme.  
  
    -   **Tek parametreye** **oluşturma eğitmen modu** ' nu ayarlarsanız, etiketli bir veri kümesi ekleyin ve modeli [eğitme model](train-clustering-model.md) modülünü kullanarak modeli eğitme.  
  
### <a name="results"></a>Sonuçlar

Modeli yapılandırmayı ve eğitimi tamamladıktan sonra, puanlar oluşturmak için kullanabileceğiniz bir modeliniz vardır. Ancak, modeli eğitmek için birden çok yol ve sonuçları görüntülemenin ve kullanmanın birden çok yolu vardır: 

#### <a name="capture-a-snapshot-of-the-model-in-your-workspace"></a>Çalışma alanınızda modelin anlık görüntüsünü yakalama

[Tren kümeleme modeli](train-clustering-model.md) modülünü kullandıysanız:

1. **Tren kümeleme modeli** modülüne sağ tıklayın.

2. **Eğitim modeli**' ni seçin ve ardından **eğitilen model olarak kaydet**' i seçin.

Kaydedilen model, modeli kaydettiğiniz sırada eğitim verilerini temsil eder. İşlem hattında kullanılan eğitim verilerini daha sonra güncelleştirirseniz, kaydedilen modeli güncelleştirmez. 

#### <a name="see-the-clustering-result-dataset"></a>Kümeleme sonucu veri kümesine bakın 

[Tren kümeleme modeli](train-clustering-model.md) modülünü kullandıysanız:

1. **Tren kümeleme modeli** modülüne sağ tıklayın.

2. **Sonuçlar veri kümesini**seçin ve ardından **Görselleştir**' i seçin.

### <a name="tips-for-generating-the-best-clustering-model"></a>En iyi kümeleme modelini oluşturmaya yönelik ipuçları  

Kümeleme sırasında kullanılan *dengeli dağıtım* işleminin modeli önemli ölçüde etkileyebileceğini bilinir. Dengeli dağıtım, noktaların ilk olarak olası centroıds 'a yerleştirilme anlamına gelir.
 
Örneğin, veri kümesi çok sayıda mantıksal dizi içeriyorsa ve kümelerin çekirdeğini oluşturmak için bir mantıksal değer seçilirse, başka hiçbir veri noktası bu kümeye uygun olmaz ve küme tek bir değer olabilir. Yani, tek bir noktası olabilir.  
  
Bu sorundan birkaç yolla kaçınabilirsiniz:  
  
-   Centroıds sayısını değiştirin ve birden çok çekirdek değeri deneyin.  
  
-   Ölçüyü değiştirerek veya daha fazlasını yineleirken birden çok model oluşturun.  
  
Genellikle, kümeleme modelleriyle, belirli bir yapılandırmanın yerel olarak iyileştirilmiş kümeler kümesine neden olacağı olasıdır. Diğer bir deyişle, model tarafından döndürülen kümeler kümesi yalnızca geçerli veri noktalarına uygun değildir ve diğer verilere genelleştirilebilir. Farklı bir başlangıç yapılandırması kullanırsanız, K-Ortalamalar yöntemi farklı, belki de farklı bir yapılandırma bulabilir. 
