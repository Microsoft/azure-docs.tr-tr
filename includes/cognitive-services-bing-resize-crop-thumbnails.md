---
author: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/09/2018
ms.author: nitinme
ms.openlocfilehash: f96c3a693ce8fc099374c998b35ce2fa90f4bb3f
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/15/2019
ms.locfileid: "67868987"
---
Bazı Bing yanıtları, Bing tarafından sunulan küçük resim görüntülerinin URL 'Lerini içerir. Küçük resim görüntülerini yeniden boyutlandırabilir ve kırpabilir. 

> [!NOTE]
> Bing Arama API kullanımı ve görüntüleme gereksinimlerinin gerektirdiği şekilde, küçük resmin boyutunun ve kırpılması için bir arama senaryosu ve üçüncü taraf haklarının dikkate aldığından emin olun.


Bir görüntüyü yeniden boyutlandırmak için, w (Width) sorgu parametresini, h (Height) sorgu parametresini veya her ikisini de küçük resmin URL 'sini ekleyin. Genişliği ve yüksekliği piksel cinsinden belirtin. Örneğin:  
  
`https://<host>/th?id=JN.5l3yzwy%2f%2fHj59U6XhssIQ&pid=Api&w=200&h=200`  
  
Yalnızca Width veya Height sorgu parametresini belirtirseniz Bing görüntünün en boy oranını korur. Hem genişlik hem de yükseklik belirtirseniz ve görüntünün özgün en boy oranını korumazsanız, Bing görüntünün kenarlığına beyaz doldurma ekler. Örneğin, bir 480x359 görüntüsünü kırpmadan 200x200 ' e yeniden boyutlandırırsanız, tam genişlik görüntüyü içerir, ancak yükseklik görüntünün üst ve alt kısmında 25 piksel beyaz doldurma içerir. Görüntü 359x480 ise, sol ve sağ kenarlıkların beyaz doldurma içermesi durumunda aynı değer true olur. Görüntüyü kırpmanız halinde beyaz doldurma eklenmez.  

 
Aşağıdaki resimde, küçük resim görüntüsünün orijinal boyutu gösterilmektedir (480x300).  
  
![Orijinal yatay görüntü](./media/cognitive-services-bing-resize-crop/bing-resize-crop-landscape.PNG)  
  
Aşağıdaki resimde, 200x200 olarak yeniden boyutlandırılmış görüntü gösterilmektedir. En boy oranı korunur ve üst ve alt kenarlıklar beyaz ile doldurulur (dolguyu göstermek için siyah kenarlık dahil edilir).  
  
![Yatay görüntü yeniden boyutlandırılmış](./media/cognitive-services-bing-resize-crop/bing-resize-crop-landscape-resized.PNG)  



Görüntünün orijinal genişlik ve yüksekliğinden daha büyük boyutlar belirtirseniz, görüntü sol ve üst kenarlarda beyaz ile doldurulur.  
  
Bir görüntüyü kırpmak için c (Kırp) sorgu parametresini ekleyin. Belirtebileceğiniz olası değerler aşağıda verilmiştir.  
  
- 4&mdash;gizli oran  
- 7&mdash;akıllı oran  
  
Akıllı oran kırpma (c = 7) isteğinde bulunursa, görüntü, görüntünün en boy oranını koruyarak görüntünün, dışarı doğru ilgi alanının merkezinden kırpılır. İlgilendiğiniz bölge, görüntünün en fazla içeri aktarma bölümünü içerdiğini belirleyen görüntüdür. Aşağıda, ilgilendiğiniz örnek bir bölge gösterilmektedir.  
  
![İlgilendiğiniz bölge](./media/cognitive-services-bing-resize-crop/bing-resize-crop-regionofinterest.PNG)

Bir görüntüyü yeniden boyutlandırırsanız ve akıllı oran kırpma isteğinde bulunursa, en boy oranını koruyarak görüntü istenen boyuta düşürülür. Daha sonra resim yeniden boyutlandırılmış boyutlara göre kırpılır. Örneğin, yeniden boyutlandırılan genişlik yükseklikten küçükse veya yükseklikle eşitse, görüntü, ilgilendiğiniz bölgenin merkezinin soluna ve sağına kırpılır. Aksi halde, görüntü, ilgilendiğiniz bölgenin merkezine ve sonuna kırpılır.  
  
 
Aşağıda, akıllı oran kırpma kullanılarak 200x200 ' e küçültülmüş olan görüntü gösterilmektedir.  
  
![200 X200 ' e kırpılan yatay görüntü](./media/cognitive-services-bing-resize-crop/bing-resize-crop-landscape200x200c7.PNG)
  
Aşağıda, akıllı oran kırpılması kullanılarak 200x100 ' e küçültülmüş olan görüntü gösterilmektedir.  
   
![200 x100 ' e kırpılan yatay görüntü](./media/cognitive-services-bing-resize-crop/bing-resize-crop-landscape200x100c7.PNG)
  
Aşağıda, akıllı oran kırpılması kullanılarak 100x200 ' e küçültülmüş görüntü gösterilmektedir.  
  
![100x200 ' e kırpılan yatay resim](./media/cognitive-services-bing-resize-crop/bing-resize-crop-landscape100x200c7.PNG)



Bing görüntünün ilgilendiğiniz bölgesini belirleyeleyemiyorsa, Bing görünmeyen oran kırpmadan yararlanır.  
  
Görünmeyen oran kırpma (c = 4) isteğinde bulunursa, Bing Görüntüyü kırpmak için aşağıdaki kuralları kullanır.  
  
- (Orijinal görüntü genişliği/özgün resim yüksekliği) < (Istenen görüntü genişliği/Istenen görüntü yüksekliği) varsa, görüntü sol üst köşeden ölçülür ve en altta kırpılacak.  
- (Orijinal görüntü genişliği/özgün resim yüksekliği) > (Istenen görüntü genişliği/Istenen görüntü yüksekliği) varsa, görüntü merkezden ölçülür ve sola ve sağa kırpılır.  



Aşağıda, 225x300 olan dikey bir görüntü gösterilmektedir.  
  
![Orijinal güneş çiçeği resmi](./media/cognitive-services-bing-resize-crop/bing-resize-crop-sunflower.PNG)
  
Aşağıda, görüntü, görme oranından sonra 200x200 ' e indirgenecek şekilde gösterilmiştir. Resim, kırpılmakta olan görüntünün alt kısmına neden olan sol üst köşesinden ölçülür.  
  
![200 X200 ' e kırpılan güneş çiçeği resmi](./media/cognitive-services-bing-resize-crop/bing-resize-crop-sunflower200x200c4.PNG)
  
Aşağıda, görme oranının kırpılması kullanılarak 200x100 ' e küçültülmüş olan görüntü gösterilmektedir. Resim, kırpılmakta olan görüntünün alt kısmına neden olan sol üst köşesinden ölçülür.  
  
![200 x100 ' e kırpılmış sunçiçeği resmi](./media/cognitive-services-bing-resize-crop/bing-resize-crop-sunflower200x100c4.PNG)
  
Aşağıda, görme oranının kırpılması kullanılarak 100x200 ' e küçültülmüş görüntü gösterilmektedir. Görüntü, kırpılmakta olan görüntünün sol ve sağ bölümlerine neden olan merkezden ölçülür.  
  
![100 X200 ' e kırpılan güneş çiçeği resmi](./media/cognitive-services-bing-resize-crop/bing-resize-crop-sunflower100x200c4.PNG)

