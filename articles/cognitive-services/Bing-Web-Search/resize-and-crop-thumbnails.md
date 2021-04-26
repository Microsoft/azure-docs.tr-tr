---
title: Görüntü küçük resimlerini yeniden boyutlandırma ve kırpma-Bing Web Araması API'si
titleSuffix: Azure Cognitive Services
description: Bing Arama API'leri bazı yanıtlar Bing tarafından sunulan küçük resim görüntülerinin URL 'Lerini içerir ve bu, yeniden boyutlandırabilir ve kırpabilir ve sorgu parametreleri içerebilir.
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: 05A08B01-89FF-4781-AFE7-08DA92F25047
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: aahi
ms.openlocfilehash: a85c5b2333418367742678a529b69c95164eda53
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "96350492"
---
# <a name="resize-and-crop-thumbnail-images"></a>Küçük resimleri yeniden boyutlandır ve Kırp

> [!WARNING]
> Bing Arama API'leri bilişsel hizmetlerden Bing Arama hizmetlere taşınıyor. **30 ekim 2020 ' den** itibaren, [burada](/bing/search-apis/bing-web-search/create-bing-search-service-resource)belgelenen işlem sonrasında Bing arama yeni örneklerin sağlanması gerekir.
> Bilişsel hizmetler kullanılarak sağlanan Bing Arama API'leri, sonraki üç yıl boyunca veya Kurumsal Anlaşma sonuna kadar, hangisi önce gerçekleşene kadar desteklenecektir.
> Geçiş yönergeleri için bkz. [Bing arama Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Bing Arama API'leri bazı yanıtlar Bing tarafından sunulan küçük resim görüntülerinin URL 'Lerini içerir ve bu, yeniden boyutlandırabilir ve kırpabilir ve sorgu parametreleri içerebilir. Örnek:

`https://<host>/th?id=AMMS_92772df988...&w=110&h=73&rs=1&qlt=80&cdv=1&pid=16.1`

Bu küçük resimlerin bir alt kümesini görüntülediğinizde, kalan görüntüleri görüntülemek için bir seçenek belirtin.

> [!NOTE]
> Küçük resim görüntülerinin kırpılması ve yeniden boyutlandırılması, Bing Arama API [kullanımı ve görüntüleme gereksinimlerinin](use-display-requirements.md)gerektirdiği şekilde üçüncü taraf haklarını karşılayan bir arama senaryosu sunduğunuzdan emin olun.

## <a name="resize-a-thumbnail"></a>Küçük resmi yeniden boyutlandırma 

Bir küçük resmi yeniden boyutlandırmak için Bing, `w` küçük RESMIN URL 'sinde yalnızca bir (Width) veya `h` (Height) sorgu parametrelerini belirtmenizi önerir. Yalnızca yükseklik veya Genişlik belirtme, Bing 'in görüntünün özgün en boy oranını korumasını sağlar. Genişliği ve yüksekliği piksel cinsinden belirtin. 

Örneğin, özgün küçük resim 480x620 ise:

`https://<host>/th?id=JN.5l3yzwy%2f%2fHj59U6XhssIQ&pid=Api&w=480&h=620`

Ve boyutunu azaltmak istiyorsanız, `w` parametreyi yeni bir değere ayarlayın (örneğin `336` ) ve `h`  parametresini kaldırın:

`https://<host>/th?id=JN.5l3yzwy%2f%2fHj59U6XhssIQ&pid=Api&w=336`

Küçük resmin yalnızca yüksekliğini veya genişliğini belirtirseniz, resmin özgün en boy oranı korunur. Her iki parametreyi de belirtirseniz ve en boy oranı korunmazsa, Bing görüntünün kenarlığına beyaz doldurma ekler.

Örneğin, bir 480x359 görüntüsünü kırpmadan 200x200 ' e yeniden boyutlandırırsanız, tam genişlik görüntüyü içerir, ancak yükseklik görüntünün üst ve alt kısmında 25 piksel beyaz doldurma içerecektir. Görüntü 359x480 ise, sol ve sağ kenarlıklar beyaz doldurma içerecektir. Görüntüyü kırpmanız halinde beyaz doldurma eklenmez.  

Aşağıdaki resimde, küçük resim görüntüsünün orijinal boyutu gösterilmektedir (480x300).  
  
![Orijinal yatay görüntü](./media/resize-crop/bing-resize-crop-landscape.png)  
  
Aşağıdaki resimde, 200x200 olarak yeniden boyutlandırılmış görüntü gösterilmektedir. En boy oranı korunur ve üst ve alt kenarlıklar beyaz ile doldurulur (Bu, doldurmayı göstermek için buradaki siyah kenarlık dahil edilir).  
  
![Yatay görüntü yeniden boyutlandırılmış](./media/resize-crop/bing-resize-crop-landscape-resized.png)  

Görüntünün orijinal genişlik ve yüksekliğinden daha büyük boyutlar belirtirseniz, Bing sol ve üst kenarlıklara beyaz doldurma ekler.  

## <a name="request-different-thumbnail-sizes"></a>Farklı küçük resim boyutları isteme

Farklı bir küçük resim resmi boyutu istemek için, ve parametreleri dışında, küçük resmin URL 'sindeki tüm sorgu parametrelerini `id` kaldırın `pid` . Daha sonra `&w` `&h` , istenen görüntü boyutu olan (Width) veya (Height) sorgu parametresini her ikisi de değil, piksel olarak ekleyin. Bing görüntünün özgün en boy oranını koruyacaktır. 

Yukarıdaki URL ile belirtilen görüntünün genişliğini 165 piksel olarak artırmak için aşağıdaki URL 'YI kullanın:

`https://<host>/th?id=AMMS_92772df988...&w=165&pid=16.1`

Görüntünün özgün boyutundan daha büyük bir görüntü isteğinde bulunmanız, Bing görüntünün etrafında beyaz doldurma ekler. Örneğin, görüntünün özgün boyutu 474x316 ise ve `&w` 500 olarak ayarlarsanız, Bing 500 x333 görüntüsünü döndürür. Bu görüntüde, üst ve alt kenarlar üzerinde 8,5 piksel beyaz doldurma ve sol ve sağ kenarlar üzerinde 13 piksel doldurma olacaktır.

İstenen boyut görüntünün özgün boyutundan büyükse Bing 'in beyaz doldurma eklemesini engellemek için `&p` sorgu parametresini 0 olarak ayarlayın. Örneğin, `&p=0` parametreyi yukarıdaki URL 'ye eklerseniz, Bing 500 x333 görüntüsü yerine 474x316 görüntüsünü döndürür:

`https://<host>/th?id=AMMS_92772df988...&w=500&p=0&pid=16.1`

Hem hem de `&w` `&h` sorgu parametrelerini belirtirseniz, Bing görüntünün en boy oranını korur ve gerektiğinde beyaz doldurma ekler. Örneğin, görüntünün özgün boyutu 474x316 ise ve Width ve Height parametrelerini 200x200 () olarak ayarlarsanız `&w=200&h=200` , Bing, üst ve alt üzerinde 33 piksel beyaz doldurma içeren bir görüntü döndürür. `&p`Sorgu parametresini eklerseniz, Bing bir 200x134 görüntüsü döndürür.

## <a name="crop-a-thumbnail"></a>Küçük resim kırpma 

Bir görüntüyü kırpmak için `c` (Kırp) sorgu parametresini ekleyin. Aşağıdaki değerleri kullanabilirsiniz:
  
- `4`&mdash;Kör oran  
- `7`&mdash;Akıllı oran  

### <a name="smart-ratio-cropping"></a>Akıllı oran kırpma

Akıllı oran kırpma isteğinde bulunursa ( `c` parametresini öğesine ayarlayarak `7` ), Bing, görüntünün en boy oranını koruyarak bir görüntüyü dışarıya doğru ilgi alanına göre kırpacaktır. İlgilendiğiniz bölge, görüntünün en fazla içeri aktarma bölümünü içerdiğini belirleyen görüntüdür. Aşağıda, ilgilendiğiniz örnek bir bölge gösterilmektedir.  
  
![İlgilendiğiniz bölge](./media/resize-crop/bing-resize-crop-regionofinterest.png)

Bir görüntüyü yeniden boyutlandırabilir ve akıllı oran kırpma isteğinde bulunursa, Bing en boy oranını koruyarak görüntüyü istenen boyuta küçültür. Bing daha sonra yeniden boyutlandırılmış boyutlara göre görüntüyü kırpar. Örneğin, yeniden boyutlandırılan genişlik yükseklikten küçükse veya yüksekliğe eşitse, Bing resmi ilgilendiğiniz bölgenin merkezinin soluna ve sağına kırpar. Aksi halde, Bing onu ilgilendiğiniz bölgenin merkezine ve sonuna kırpar.  
  
 
Aşağıda, akıllı oran kırpma kullanılarak 200x200 ' e küçültülmüş olan görüntü gösterilmektedir. Bing, görüntüyü sol üst köşesinden ölçtüğünden görüntünün alt kısmı kırpılır. 
  
![200 X200 ' e kırpılan yatay görüntü](./media/resize-crop/bing-resize-crop-landscape200x200c7.png) 
  
Aşağıda, akıllı oran kırpılması kullanılarak 200x100 ' e küçültülmüş olan görüntü gösterilmektedir. Bing, görüntüyü sol üst köşesinden ölçtüğünden görüntünün alt kısmı kırpılır. 
   
![200 x100 ' e kırpılan yatay görüntü](./media/resize-crop/bing-resize-crop-landscape200x100c7.png)
  
Aşağıda, akıllı oran kırpılması kullanılarak 100x200 ' e küçültülmüş görüntü gösterilmektedir. Bing görüntüyü merkezden ölçtiğinden görüntünün sol ve sağ kısımları kırpılır.
  
![100x200 ' e kırpılan yatay resim](./media/resize-crop/bing-resize-crop-landscape100x200c7.png) 

Bing görüntünün ilgilendiğiniz bölgesini belirleyeleyemiyorsa, hizmet, kör oran kırpmadan önce kullanılır.  

### <a name="blind-ratio-cropping"></a>Kör oran kırpma

Görünmeyen oran kırpma isteğinde bulunursa ( `c` parametresini olarak ayarlayarak `4` ), Bing Görüntüyü kırpmak için aşağıdaki kuralları kullanır.  
  
- Eğer `(Original Image Width / Original Image Height) < (Requested Image Width / Requested Image Height)` görüntü, sol üst köşeden ölçülür ve alt kısımdaki kırpılır.  
- `(Original Image Width / Original Image Height) > (Requested Image Width / Requested Image Height)`, Görüntü merkezden ölçülür ve sola ve sağa kırpılır.  

Aşağıda, 225x300 olan dikey bir görüntü gösterilmektedir.  
  
![Orijinal güneş çiçeği resmi](./media/resize-crop/bing-resize-crop-sunflower.png)
  
Aşağıda, görüntü, görme oranından sonra 200x200 ' e indirgenecek şekilde gösterilmiştir. Resim, kırpılmakta olan görüntünün alt kısmına neden olan sol üst köşesinden ölçülür.  
  
![200 X200 ' e kırpılan güneş çiçeği resmi](./media/resize-crop/bing-resize-crop-sunflower200x200c4.png)
  
Aşağıda, görme oranının kırpılması kullanılarak 200x100 ' e küçültülmüş olan görüntü gösterilmektedir. Resim, kırpılmakta olan görüntünün alt kısmına neden olan sol üst köşesinden ölçülür.  
  
![200 x100 ' e kırpılmış sunçiçeği resmi](./media/resize-crop/bing-resize-crop-sunflower200x100c4.png)
  
Aşağıda, görme oranının kırpılması kullanılarak 100x200 ' e küçültülmüş görüntü gösterilmektedir. Görüntü, kırpılmakta olan görüntünün sol ve sağ bölümlerine neden olan merkezden ölçülür.  
  
![100 X200 ' e kırpılan güneş çiçeği resmi](./media/resize-crop/bing-resize-crop-sunflower100x200c4.png)

## <a name="next-steps"></a>Sonraki adımlar

* [Bing Arama API'leri nedir?](bing-api-comparison.md)
* [Bing Arama API’si kullanım ve görüntüleme gereksinimleri](use-display-requirements.md)