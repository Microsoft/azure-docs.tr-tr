---
title: Azure Sentinel 'de arama özellikleri | Microsoft Docs
description: Bu makalede, Azure Sentinel arama yeteneklerini kullanma açıklanmaktadır.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 6aa9dd27-6506-49c5-8e97-cc1aebecee87
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/10/2019
ms.author: rkarlin
ms.openlocfilehash: edba2a18e3b086a132f0bba7622df5df389671c3
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73489189"
---
# <a name="hunt-for-threats-with-azure-sentinel"></a>Azure Sentinel ile tehditler için Hunt

Güvenlik tehditlerini arama konusunda öngörülü bir araştırmacısı, Azure, kuruluşunuzun veri kaynakları genelinde güvenlik tehditleri aramak için arama ve sorgu araçlarını güçlü bir şekilde ele almak isteyen bir. Ancak, sistemleriniz ve güvenlik gereçleriniz, anlamlı olaylara ayrıştırma ve filtreleme zor olabilecek veri Dağları oluşturur. Güvenlik analistlerinin güvenlik uygulamalarınız tarafından algılanmayan yeni anormaller için güvenli bir şekilde görünmesine yardımcı olmak için, Azure Sentinel ' yerleşik arama sorguları, ağınızda zaten bulunan verilerde sorunları bulmak için doğru soruları sormaya kılavuzluk eder. 

Örneğin, bir yerleşik sorgu, altyapınız üzerinde çalışan en sık kullanılan süreçler hakkında veri sağlar. her çalıştırıldıklarında bir uyarı istemezsiniz, tamamen masum olabilir, ancak bir süre içinde sorguya göz atmak isteyebilirsiniz. her şeyi olağan dışı bir şeydir. 



Azure Sentinel ile aşağıdaki özelliklerden yararlanabilirsiniz:

- Yerleşik sorgular: Başlangıç sayfası başlamanıza başlamak için tasarlanan önceden yüklenmiş sorgu örnekleri sağlar ve tabloları ve sorgu dilini öğrenirsiniz. Bu yerleşik arama sorguları, Microsoft güvenlik araştırmacıları tarafından sürekli olarak geliştirilmiştir, yeni sorgular ekliyor ve yeni algılamaları aramak için bir giriş noktası sağlamak üzere mevcut sorgular üzerinde ince ayar yapar ve Yeni saldırıları Beginnings. 

- IntelliSense ile güçlü sorgu dili: bir sorgu dilinin üzerine kurulmuştur ve bu sayede bir sonraki düzeye kadar erişmeniz gereken esnekliği elde edersiniz.

- Kendi yer işaretlerinizi oluşturun: ara işlem sırasında, tüm eşleşmeler, panolar veya olağandışı ya da şüpheli görünen etkinlikler arasında gelebiliriz. Gelecekte bunlara geri dönebilmeniz için bu öğeleri işaretlemek üzere yer işareti işlevini kullanın. Yer işaretleri, araştırma için bir olay oluşturmak üzere kullanılacak öğeleri daha sonra kaydetmenizi sağlar. Yer işaretleri hakkında daha fazla bilgi için bkz. arama [sırasında yer Işaretlerini kullanma](hunting.md).
- Araştırmayı otomatikleştirmek için not defterlerini kullanın: Not defterleri, araştırma ve hunın adımlarında gezinmek için oluşturabileceğiniz adım adım PlayBook 'lardır.  Not defterleri, kuruluşunuzdaki diğer kişilerle paylaşılabilen, yeniden kullanılabilir bir PlayBook 'ta bulunan tüm adımları kapsüller. 
- Depolanan verileri sorgulama: veri, sorgulamanızı sağlamak için tablolarda erişilebilir. Örneğin, işlem oluşturma, DNS olayları ve diğer birçok olay türünü sorgulayabilirsiniz.

- Topluluk bağlantıları: ek sorgular ve veri kaynakları bulmak için daha fazla topluluğun gücünden yararlanın.
 
## <a name="get-started-hunting"></a>Kullanmaya başlayın

1. Azure Sentinel portalında, **hunme**' ye tıklayın.
  ![Azure Sentinel, aramaya](media/tutorial-hunting/hunting-start.png) başlıyor

2. **Hunme** sayfasını açtığınızda, tüm arama sorguları tek bir tabloda görüntülenir. Tabloda Microsoft 'un Güvenlik analistlerinin ekibi tarafından yazılan tüm sorgular ve oluşturduğunuz veya değiştirdiğiniz ek sorgular listelenir. Her sorgu, ne araydıklarından ve üzerinde ne tür verilerin çalıştığı hakkında bir açıklama sağlar. Bu şablonlar çeşitli taktiklerinde gruplandırılır. sağ taraftaki simgeler, tehdit türünü kategorilere ayırarak, ilk erişim, kalıcılık ve exfiltrame gibi. Bu arama şablonlarına, alanlardan herhangi birini kullanarak filtre uygulayabilirsiniz. Sık kullanılanlarınıza herhangi bir sorgu kaydedebilirsiniz. Bir sorguyu sık kullanılanlarınıza **kaydederek, arama sayfasına her** erişildiğinde sorgu otomatik olarak çalışır. Kendi arama sorgunuzu oluşturabilir veya var olan bir sorgu şablonunu kopyalayabilir ve özelleştirebilirsiniz. 
 
2. Arama sayfasını kapatmadan herhangi bir sorgu çalıştırmak için sorgu ayrıntıları sayfasında **Sorguyu Çalıştır** ' a tıklayın.  Eşleşme sayısı tablo içinde görüntülenir. Sorguların ve bunların eşleşmelerin listesini gözden geçirin. Eşleşmenin ilişkili olduğu sonlandırma zincirindeki aşamayı göz atın.

3. Sorgu Ayrıntıları bölmesinde temel alınan sorgunun hızlı bir şekilde gözden geçirilmesini gerçekleştirin veya sorguyu Log Analytics açmak için **sorgu sonucunu görüntüle** ' ye tıklayın. En altta, sorguyla ilgili eşleşmeleri gözden geçirin.

4.  Satırı tıklatın ve Araştırılması gereken satırları eklemek için **yer Işareti Ekle** ' yi seçin. bunu, şüpheli görünen her şey için yapabilirsiniz. 

5. Ardından, ana **hunme** sayfasına dönün ve tüm şüpheli etkinlikleri görmek Için **yer işaretleri** sekmesine tıklayın. 

6. Bir yer işareti seçin ve araştırma deneyimini açmak için **Araştır** ' a tıklayın. Yer işaretlerine filtre uygulayabilirsiniz. Örneğin, bir kampanyayı araştırıyorsanız kampanya için bir etiket oluşturabilir ve sonra tüm yer imlerini kampanyaya göre filtreleyebilirsiniz.

1. Hangi arama sorgusunun olası saldırılara karşı yüksek değerli Öngörüler sağladığını belirledikten sonra, sorgunuza göre özel algılama kuralları oluşturabilir ve bu öngörüleri güvenlik olay yanıtlamalarınıza uyarı olarak yüzeyden bırakabilirsiniz.

 

## <a name="query-language"></a>Sorgu dili 

Azure Sentinel 'de arama, kusto sorgu dilini temel alır. Sorgu dili ve desteklenen işleçler hakkında daha fazla bilgi için bkz. [sorgu dili başvurusu](https://docs.loganalytics.io/docs/Language-Reference/).

## <a name="public-hunting-query-github-repository"></a>Ortak arama GitHub deposu

[Sorgu deposuna](https://github.com/Azure/Orion)göz atın. Müşterilerimiz tarafından paylaşılan örnek sorguları katkıda bulun ve kullanın.

 

## <a name="sample-query"></a>Örnek sorgu

Tipik bir sorgu, tablo adı ile başlar ve ardından \|ile ayrılmış bir dizi işleç izler.

Yukarıdaki örnekte, SecurityEvent adlı tablo adı ' nı başlatın ve gerektiği gibi, ifklu öğeler ekleyin.

1. Yalnızca önceki yedi günün kayıtlarını gözden geçirmek için bir zaman filtresi tanımlayın.

2. Yalnızca olay KIMLIĞI 4688 ' i göstermek için sorguya bir filtre ekleyin.

3. Komut satırında sorguya bir filtre ekleyerek yalnızca cscript. exe örnekleri bulunur.

4. Yalnızca araştırırken İlgilendiğiniz sütunları ve sonuçları 1000 olarak sınırlamak ve **Sorguyu Çalıştır**' a tıklayın.
5. Yeşil üçgene tıklayıp sorguyu çalıştırın. Sorguyu test edebilir ve anormal davranışları aramak için çalıştırabilirsiniz.

## <a name="useful-operators"></a>Yararlı işleçler

Sorgu dili güçlüdür ve birçok kullanılabilir operatör bulunur, bazı yararlı işleçler burada listelenmiştir:

**WHERE** -bir koşulu karşılayan satır alt kümesiyle bir tablo filtreleyin.

**özetleme** -giriş tablosunun içeriğini toplayan bir tablo oluşturur.

**birleştirme** -her tablodan belirtilen sütunların değerlerini eşleştirerek yeni bir tablo oluşturmak için iki tablo satırını birleştirin.

**Count** -giriş kayıt kümesindeki kayıt sayısını döndürür.

**top** -belirtilen sütunlara göre sıralanan ilk N kaydı döndürür.

**limit** -belirtilen sayıda satıra kadar döndürün.

**Proje** -dahil edilecek, yeniden adlandırılacak veya bırakılacak sütunları seçin ve yeni hesaplanan sütunları ekleyin.

**Genişlet** -hesaplanmış sütunları oluşturun ve sonuç kümesine ekleyin.

**makeset** -ifadenin grupta aldığı ayrı değerler kümesinin dınamık (JSON) dizisini döndürür

**bul** -bir tablo kümesi genelinde bir koşulla eşleşen satırları bulun.

## <a name="save-a-query"></a>Sorgu kaydetme

Bir sorgu oluşturabilir veya değiştirebilir, kendi sorgunuz olarak kaydedebilir veya aynı Kiracıdaki kullanıcılarla paylaşabilirsiniz.

   ![Sorguyu Kaydet](./media/tutorial-hunting/save-query.png)

Yeni bir hunme sorgusu oluşturun:

1. **Yeni sorgu** ' ya tıklayın ve **Kaydet**' i seçin.
2. Tüm boş alanları doldurup **Kaydet**' i seçin.

   ![Yeni sorgu](./media/tutorial-hunting/new-query.png)

Var olan bir arama sorgusunu kopyalayın ve değiştirin:

1. Değiştirmek istediğiniz tabloda bulunan sorguyu seçin.
2. Değiştirmek istediğiniz sorgunun satırındaki üç nokta (...) simgesini seçin ve **sorguyu Kopyala**' yı seçin.

   ![sorguyu Kopyala](./media/tutorial-hunting/clone-query.png)
 

3. Sorguyu değiştirin ve **Oluştur**' u seçin.

   ![Özel sorgu](./media/tutorial-hunting/custom-query.png)

## <a name="next-steps"></a>Sonraki adımlar
Bu makalede, Azure Sentinel ile bir araştırma araştırması çalıştırmayı öğrendiniz. Azure Sentinel hakkında daha fazla bilgi edinmek için aşağıdaki makalelere bakın:


- [Otomatik arama kampanyalarını çalıştırmak için not defterlerini kullanın](notebooks.md)
- [Avlarken ilginç bilgileri kaydetmek için yer işaretlerini kullanın](bookmarks.md)
