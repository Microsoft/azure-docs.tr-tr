---
title: Azure depolama tablo tasarım desenleri | Microsoft Docs
description: Azure Tablo hizmeti çözümleri için desenler kullanın.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/08/2019
ms.author: tamram
ms.subservice: tables
ms.openlocfilehash: d7d4d7b331198982f7c5513d23420bdde9455c66
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73796656"
---
# <a name="table-design-patterns"></a>Tablo tasarımı desenleri
Bu makalede, tablo hizmeti çözümleriyle kullanım için uygun bazı desenler açıklanmaktadır. Ayrıca, diğer tablo depolama Tasarım makalelerinde ele alınan bazı sorunları ve ilgili konuları nasıl uygulayakullanabileceğinizi öğreneceksiniz. Aşağıdaki diyagramda, farklı desenler arasındaki ilişkiler özetlenmektedir:  

![ilgili verileri aramak için](media/storage-table-design-guide/storage-table-design-IMAGE05.png)


Yukarıdaki desen haritasında, bu kılavuzda belgelenen desenler (mavi) ve kenar desenleri (turuncu) arasındaki bazı ilişkiler vurgulanmıştır. Dikkate değer veren birçok diğer desen vardır. Örneğin, tablo hizmeti için temel senaryolardan biri, [komut sorgu sorumluluğu ayırma (CQRS)](https://msdn.microsoft.com/library/azure/jj554200.aspx) deseninin [gerçekleştirilmiş görünüm modelini](https://msdn.microsoft.com/library/azure/dn589782.aspx) kullanmaktır.  

## <a name="intra-partition-secondary-index-pattern"></a>Bölüm içi ikincil dizin kalıbı
Farklı **rowkey değerleri kullanarak** hızlı ve verimli aramalar ve alternatif sıralama emirlerini etkinleştirmek için, farklı **rowkey** değerleri kullanarak her bir varlığın birden çok kopyasını depolayın (aynı bölümde). Kopyalar arasındaki güncelleştirmeler, yumurtalar kullanılarak tutarlı tutulabilir.  

### <a name="context-and-problem"></a>Bağlam ve sorun
Tablo hizmeti, **partitionkey** ve **rowkey** değerlerini kullanarak varlıkları otomatik olarak dizine ekler. Bu, bir istemci uygulamanın bu değerleri kullanarak bir varlığı etkin bir şekilde almasını sağlar. Örneğin, aşağıda gösterilen tablo yapısını kullanarak, bir istemci uygulaması, departman adı ve çalışan KIMLIĞI ( **partitionkey** ve **rowkey** değerleri) kullanarak bireysel bir çalışan varlığı almak için bir nokta sorgusu kullanabilir. Ayrıca, bir istemci her bir departman içindeki çalışan KIMLIĞINE göre sıralanmış varlıkları alabilir.

![Image06](media/storage-table-design-guide/storage-table-design-IMAGE06.png)

Ayrıca, e-posta adresi gibi başka bir özelliğin değerine bağlı olarak bir çalışan varlığı bulabilmek istiyorsanız, bir eşleşme bulmak için daha az verimli bir bölüm taraması kullanmanız gerekir. Bunun nedeni, tablo hizmetinin ikincil dizinler sağlamasunmamalıdır. Ayrıca, bir çalışan listesini **Rowkey** sırasına göre farklı bir sırada sıralanmış olarak isteme seçeneği yoktur.  

### <a name="solution"></a>Çözüm
İkincil dizinlerin eksikliğine geçici bir çözüm için, her bir varlığın birden çok kopyasını her bir kopya ile farklı bir **Rowkey** değeri kullanarak saklayabilirsiniz. Aşağıda gösterilen yapılarla bir varlık depoluysanız, e-posta adresini veya çalışan KIMLIĞINI temel alarak çalışan varlıklarını etkin bir şekilde alabilirsiniz. **Rowkey**, "empid_" ve "email_" için önek değerleri, bir dizi e-posta adresini veya çalışan kimliklerini kullanarak tek bir çalışan veya bir dizi çalışanı sorgulamanızı sağlar.  

![Çalışan varlıkları](media/storage-table-design-guide/storage-table-design-IMAGE07.png)

Aşağıdaki iki filtre ölçütü (bir çalışan KIMLIĞI ve bir e-posta adresine göre arama), her ikisi de nokta sorguları belirler:  

* $filter = (PartitionKey EQ ' Sales ') ve (RowKey EQ ' empid_000223 ')  
* $filter = (PartitionKey EQ ' Sales ') ve (RowKey EQ 'email_jonesj@contoso.com')  

Bir dizi çalışan varlığı için sorgulama yaparsanız, **Rowkey**içinde uygun öneki olan varlıkları sorgulayarak, çalışan kimliği sırasında sıralanmış bir Aralık veya e-posta adresi sırasında sıralanmış bir Aralık belirleyebilirsiniz.  

* Satış departmanındaki tüm çalışanları 000100:000199 ' de aralığında bir çalışan KIMLIĞI ile bulmak için: $filter = (PartitionKey EQ ' Sales ') ve (rowkey Ge ' empid_000100 ') ve (RowKey Le ' empid_000199 ')  
* Satış Departmanında, ' a ' kullanımı ile başlayan bir e-posta adresine sahip tüm çalışanları bulmak için: $filter = (PartitionKey EQ ' Sales ') ve (rowkey Ge ' email_a ') ve (RowKey lt ' email_b ')  
  
  Yukarıdaki örneklerde kullanılan filtre sözdizimi tablo hizmeti REST API. daha fazla bilgi için bkz. [Sorgu varlıkları](https://msdn.microsoft.com/library/azure/dd179421.aspx).  

### <a name="issues-and-considerations"></a>Sorunlar ve dikkat edilmesi gerekenler
Bu düzenin nasıl uygulanacağına karar verirken aşağıdaki noktaları göz önünde bulundurun:  

* Tablo Depolaması görece bir şekilde kullanılmak üzere, yinelenen verileri depolamanın maliyet ek yükünün önemli bir sorun olmaması gerekir. Bununla birlikte, tahmini depolama gereksinimlerinize göre tasarımınızın maliyetini her zaman değerlendirin ve yalnızca, istemci uygulamanızın yürütüleceği sorguları destekleyecek şekilde yinelenen varlıklar ekleyebilirsiniz.  
* İkincil dizin varlıkları özgün varlıklarla aynı bölümde depolandığından, tek bir bölüm için ölçeklenebilirlik hedeflerini aşmamak zorunda olmadığınızdan emin olmanız gerekir.  
* Varlığın iki kopyasını otomatik olarak güncelleştirmek için Yumurtları kullanarak, yinelenen varlıklarınızı birbirleriyle tutarlı tutabilirsiniz. Bu, bir varlığın tüm kopyalarını aynı bölümde depolamanız gerektiğini gösterir. Daha fazla bilgi için [varlık grubu Işlemlerini kullanma](table-storage-design.md#entity-group-transactions)bölümüne bakın.  
* **Rowkey** için kullanılan değer her varlık için benzersiz olmalıdır. Bileşik anahtar değerlerini kullanmayı düşünün.  
* **Rowkey** 'teki sayısal değerleri doldurma (örneğin, çalışan kimliği 000223), üst ve alt sınırlara göre doğru sıralamayı ve filtrelemeyi sunar.  
* Varlığınızın tüm özelliklerini yinelemek zorunda değilsiniz. Örneğin, **Rowkey** içindeki e-posta adresini kullanan varlıkların aranacağı sorguların, çalışanın kullanım ömrü hiçbir zaman gerekmiyorsa, bu varlıklar aşağıdaki yapıya sahip olabilir:

   ![Çalışan varlık yapısı](media/storage-table-design-guide/storage-table-design-IMAGE08.png)


* Yinelenen verileri depolamak ve bir varlık bulmak için bir sorgu kullanmak ve gerekli verileri aramak için tek bir sorgu ile ihtiyacınız olan tüm verileri almanızı sağlamak genellikle daha iyidir.  

### <a name="when-to-use-this-pattern"></a>Bu düzenin kullanılacağı durumlar
Bu düzeni, istemci uygulamanızın çeşitli farklı anahtarlar kullanarak varlıkları alması gerektiğinde, istemcinizin farklı sıralama emirlerindeki varlıkları alması gerektiğinde ve her bir varlığı çeşitli benzersiz değerler kullanarak tanımlayabilmeniz gerektiğinde kullanın. Ancak, farklı **Rowkey** değerlerini kullanarak varlık aramaları gerçekleştirirken bölüm ölçeklenebilirlik sınırlarını aştığınızdan emin olmalısınız.  

### <a name="related-patterns-and-guidance"></a>İlgili düzenler ve kılavuzlar
Bu düzeni uygularken aşağıdaki düzenler ve yönergeler de yararlı olabilir:  

* [Bölümler arası ikincil dizin kalıbı](#inter-partition-secondary-index-pattern)
* [Bileşik anahtar stili](#compound-key-pattern)
* Varlık grubu Işlemleri
* [Heterojen varlık türleriyle çalışma](#working-with-heterogeneous-entity-types)

## <a name="inter-partition-secondary-index-pattern"></a>Bölümler arası ikincil dizin kalıbı
Farklı **rowkey değerleri kullanarak** , hızlı ve verimli aramalar ve alternatif sıralama emirlerini etkinleştirmek üzere ayrı bölümlerde veya ayrı tablolarda farklı **rowkey** değerleri kullanarak her bir varlığın birden çok kopyasını saklayın.  

### <a name="context-and-problem"></a>Bağlam ve sorun
Tablo hizmeti, **partitionkey** ve **rowkey** değerlerini kullanarak varlıkları otomatik olarak dizine ekler. Bu, bir istemci uygulamanın bu değerleri kullanarak bir varlığı etkin bir şekilde almasını sağlar. Örneğin, aşağıda gösterilen tablo yapısını kullanarak, bir istemci uygulaması, departman adı ve çalışan KIMLIĞI ( **partitionkey** ve **rowkey** değerleri) kullanarak bireysel bir çalışan varlığı almak için bir nokta sorgusu kullanabilir. Ayrıca, bir istemci her bir departman içindeki çalışan KIMLIĞINE göre sıralanmış varlıkları alabilir.  

![Çalışan KIMLIĞI](media/storage-table-design-guide/storage-table-design-IMAGE09.png)

Ayrıca, e-posta adresi gibi başka bir özelliğin değerine bağlı olarak bir çalışan varlığı bulabilmek istiyorsanız, bir eşleşme bulmak için daha az verimli bir bölüm taraması kullanmanız gerekir. Bunun nedeni, tablo hizmetinin ikincil dizinler sağlamasunmamalıdır. Ayrıca, bir çalışan listesini **Rowkey** sırasına göre farklı bir sırada sıralanmış olarak isteme seçeneği yoktur.  

Bu varlıklara yönelik yüksek hacimli işlemlere benimsemeyi bekleme olursunuz ve tablo hizmeti 'nin istemcinizi azaltma riskini en aza indirmek istersiniz.  

### <a name="solution"></a>Çözüm
İkincil dizinlerin eksikliğine geçici bir çözüm bulmak için, her bir varlığın farklı **partitionkey** ve **rowkey** değerlerini kullanarak her bir kopyanın birden çok kopyasını saklayabilirsiniz. Aşağıda gösterilen yapılarla bir varlık depoluysanız, e-posta adresini veya çalışan KIMLIĞINI temel alarak çalışan varlıklarını etkin bir şekilde alabilirsiniz. "Empid_" ve "email_" **Partitionkey**için önek değerleri, bir sorgu için kullanmak istediğiniz dizini tanımlamanızı sağlar.  

![Birincil dizin ve ikincil dizin](media/storage-table-design-guide/storage-table-design-IMAGE10.png)


Aşağıdaki iki filtre ölçütü (bir çalışan KIMLIĞI ve bir e-posta adresine göre arama), her ikisi de nokta sorguları belirler:  

* $filter = (PartitionKey EQ ' empid_Sales ') ve (RowKey EQ ' 000223 ')
* $filter = (PartitionKey EQ ' email_Sales ') ve (RowKey EQ 'jonesj@contoso.com')  

Bir dizi çalışan varlığı için sorgulama yaparsanız, **Rowkey**içinde uygun öneki olan varlıkları sorgulayarak, çalışan kimliği sırasında sıralanmış bir Aralık veya e-posta adresi sırasında sıralanmış bir Aralık belirleyebilirsiniz.  

* Satış departmanındaki tüm çalışanları, çalışan KIMLIĞI sırasına göre **000100** ile **000199** arasında bir çalışan kimliği ile bulmak için: $Filter = (partitionkey EQ ' empid_Sales ') ve (rowkey Ge ' 000100 ') ve (rowkey Le ' 000199 ')  
* Satış departmanındaki tüm çalışanları e-posta adresi sırası kullanımı ' a ' ile başlayan bir e-posta adresiyle bulmak için: $filter = (PartitionKey EQ ' email_Sales ') ve (RowKey Ge ' a ') ve (RowKey lt ' b ')  

Yukarıdaki örneklerde kullanılan filtre sözdizimi tablo hizmeti REST API. daha fazla bilgi için bkz. [Sorgu varlıkları](https://msdn.microsoft.com/library/azure/dd179421.aspx).  

### <a name="issues-and-considerations"></a>Sorunlar ve dikkat edilmesi gerekenler
Bu düzenin nasıl uygulanacağına karar verirken aşağıdaki noktaları göz önünde bulundurun:  

* Birincil ve ikincil dizin varlıklarını sürdürmek için [sürekli tutarlı işlemler modelini](#eventually-consistent-transactions-pattern) kullanarak, yinelenen varlıklarınızın birbirleriyle sürekli tutarlı kalmasını sağlayabilirsiniz.  
* Tablo Depolaması görece bir şekilde kullanılmak üzere, yinelenen verileri depolamanın maliyet ek yükünün önemli bir sorun olmaması gerekir. Bununla birlikte, tahmini depolama gereksinimlerinize göre tasarımınızın maliyetini her zaman değerlendirin ve yalnızca, istemci uygulamanızın yürütüleceği sorguları destekleyecek şekilde yinelenen varlıklar ekleyebilirsiniz.  
* **Rowkey** için kullanılan değer her varlık için benzersiz olmalıdır. Bileşik anahtar değerlerini kullanmayı düşünün.  
* **Rowkey** 'teki sayısal değerleri doldurma (örneğin, çalışan kimliği 000223), üst ve alt sınırlara göre doğru sıralamayı ve filtrelemeyi sunar.  
* Varlığınızın tüm özelliklerini yinelemek zorunda değilsiniz. Örneğin, **Rowkey** içindeki e-posta adresini kullanan varlıkların aranacağı sorguların, çalışanın kullanım ömrü hiçbir zaman gerekmiyorsa, bu varlıklar aşağıdaki yapıya sahip olabilir:
  
   ![Çalışan varlığı (ikincil dizin)](media/storage-table-design-guide/storage-table-design-IMAGE11.png)

* Yinelenen verileri depolamak ve tek bir sorgu ile ihtiyacınız olan tüm verileri, ikincil dizini kullanarak bir varlığı bulmak için bir sorgu kullanmaktan, diğeri ise birincil dizinde gerekli verileri aramak için bir sorgu kullanmak zorunda olduğunuzdan emin olmak için daha iyi bir seçenektir.  

### <a name="when-to-use-this-pattern"></a>Bu düzenin kullanılacağı durumlar
Bu düzeni, istemci uygulamanızın çeşitli farklı anahtarlar kullanarak varlıkları alması gerektiğinde, istemcinizin farklı sıralama emirlerindeki varlıkları alması gerektiğinde ve her bir varlığı çeşitli benzersiz değerler kullanarak tanımlayabilmeniz gerektiğinde kullanın. Farklı **Rowkey** değerlerini kullanarak varlık aramaları gerçekleştirirken bölüm ölçeklenebilirlik sınırlarını aşmamak istiyorsanız bu stili kullanın.  

### <a name="related-patterns-and-guidance"></a>İlgili düzenler ve kılavuzlar
Bu düzeni uygularken aşağıdaki düzenler ve yönergeler de yararlı olabilir:  

* [Sonuçta tutarlı işlem kriteri](#eventually-consistent-transactions-pattern)  
* [Bölüm içi ikincil dizin kalıbı](#intra-partition-secondary-index-pattern)  
* [Bileşik anahtar stili](#compound-key-pattern)  
* Varlık grubu Işlemleri  
* [Heterojen varlık türleriyle çalışma](#working-with-heterogeneous-entity-types)  

## <a name="eventually-consistent-transactions-pattern"></a>Sonuçta tutarlı işlem kriteri
Azure kuyruklarını kullanarak bölüm sınırları veya depolama sistemi sınırları genelinde sonuçta tutarlı davranışı etkinleştirin.  

### <a name="context-and-problem"></a>Bağlam ve sorun
Yumurtları aynı bölüm anahtarını paylaşan birden çok varlıkta atomik işlemleri etkinleştirir. Performans ve ölçeklenebilirlik nedenleriyle, tutarlılık gereksinimlerine sahip varlıkları ayrı bölümlerde veya ayrı bir depolama sisteminde depolamaya karar verebilirsiniz: böyle bir senaryoda, tutarlılığı sağlamak için Yumurtları kullanamazsınız. Örneğin, arasında son tutarlılığı sürdürmek için bir gereksiniminize sahip olabilirsiniz:  

* Aynı tabloda, farklı tablolarda veya farklı depolama hesaplarında bulunan iki farklı bölümde depolanan varlıklar.  
* Tablo hizmetinde depolanan bir varlık ve BLOB hizmetinde depolanan bir BLOB.  
* Tablo hizmetinde depolanan bir varlık ve dosya sistemindeki bir dosya.  
* Tablo hizmetinde depolanan ve Azure Bilişsel Arama hizmeti kullanılarak dizini oluşturulmuş bir varlık.  

### <a name="solution"></a>Çözüm
Azure kuyruklarını kullanarak, iki veya daha fazla bölüm veya depolama sisteminde nihai tutarlılık sağlayan bir çözüm uygulayabilirsiniz.
Bu yaklaşımı göstermek için eski çalışan varlıklarını arşivlemek için bir gereksinimin olduğunu varsayalım. Eski çalışan varlıkları nadiren sorgulanır ve geçerli çalışanlarla ilgilenen etkinliklerden çıkarılmalıdır. Bu gereksinimi uygulamak için, **geçerli** tablodaki etkin çalışanları ve **Arşiv** tablosunda eski çalışanları depolarlar. Bir çalışanın arşivlenmesi için, varlığı **geçerli** tablodan silmeniz ve varlığı **Arşiv** tablosuna eklemeniz gerekir, ancak bu iki işlemi gerçekleştirmek için EGT kullanamazsınız. Bir başarısızlığın bir varlığın her iki tabloda veya hiç tablo halinde görünmesine neden olması riskini önlemek için Arşiv işleminin sonunda tutarlı olması gerekir. Aşağıdaki sıra diyagramı bu işlemdeki adımları özetler. Aşağıdaki metinde özel durum yolları için daha fazla ayrıntı sağlanır.  

![Azure Kuyrukları çözümü](media/storage-table-design-guide/storage-table-design-IMAGE12.png)

İstemci, Azure kuyruğuna bir ileti yerleştirerek, çalışan #456 arşivlemek için bu örnekte arşiv işlemini başlatır. Bir çalışan rolü yeni iletiler için kuyruğu yoklar; bir tane bulduğunda iletiyi okur ve kuyrukta gizli bir kopya bırakır. Çalışan rolü, **geçerli** tablodaki varlığın bir kopyasını getirir, **Arşiv** tablosuna bir kopya ekler ve ardından **geçerli** tablodan orijinali siler. Son olarak, önceki adımlardan bir hata yoksa, çalışan rolü gizli iletiyi kuyruktan siler.  

Bu örnekte, 4. adım çalışanı **Arşiv** tablosuna ekler. Bu, çalışanı blob hizmetindeki bir bloba veya bir dosya sistemindeki bir dosyaya ekleyebilir.  

### <a name="recovering-from-failures"></a>Hatalardan kurtarma
**4** ve **5** . adımlarda gerçekleştirilen işlemlerin, çalışan rolünün arşiv işlemini yeniden başlatması gereken durumlarda *ıdempotent* olması önemlidir. Tablo hizmetini kullanıyorsanız, **4** . adım için "Ekle veya Değiştir" işlemini kullanmanız gerekir. **5** . adımda, kullanmakta olduğunuz istemci kitaplığındaki "varsa sil" işlemini kullanmanız gerekir. Başka bir depolama sistemi kullanıyorsanız, uygun bir ıdempotent işlemi kullanmanız gerekir.  

Çalışan rolü hiçbir zaman **6**. adımı tamamlarsa, bir zaman aşımından sonra ileti tekrar işlemeyi denemek için çalışan rolü için ayrılan sırada yeniden görüntülenir. Çalışan rolü, sıradaki bir iletinin kaç kez okunduğunu denetleyebilir ve gerekirse, bu iletiyi ayrı bir kuyruğa göndererek araştırma için bir "Poison" mesajı olduğunu işaret edebilir. Sıra iletilerini okuma ve sıradan çıkarma sayısını denetleme hakkında daha fazla bilgi için bkz. [Iletileri alma](https://msdn.microsoft.com/library/azure/dd179474.aspx).  

Geçici hatalar tablo ve kuyruk hizmetlerine bazı hataları olan ve istemci uygulamanızı bunları işlemesi için uygun bir yeniden deneme mantığı içermelidir.  

### <a name="issues-and-considerations"></a>Sorunlar ve dikkat edilmesi gerekenler
Bu düzenin nasıl uygulanacağına karar verirken aşağıdaki noktaları göz önünde bulundurun:  

* Bu çözüm, işlem yalıtımı sağlamaz. Örneğin, bir istemci, çalışan rolü **4** ve **5**. adım arasında olduğunda **geçerli** ve **Arşiv** tablolarını okuyabilir ve verilerin tutarsız bir görünümünü görürsünüz. Veriler sonunda tutarlı olacaktır.  
* Nihai tutarlılığı sağlamak için 4 ve 5. adımlarda ıdempotent olduğundan emin olmanız gerekir.  
* Birden çok kuyruk ve çalışan rolü örneği kullanarak çözümü ölçeklendirebilirsiniz.  

### <a name="when-to-use-this-pattern"></a>Bu düzenin kullanılacağı durumlar
Farklı bölümlerde veya tablolarda bulunan varlıklar arasında nihai tutarlılığı garantilemek istediğinizde bu stili kullanın. Tablo hizmeti ve BLOB hizmeti ve veritabanı ya da dosya sistemi gibi diğer Azure dışı depolama veri kaynakları arasındaki işlemlerin nihai tutarlılığını sağlamak için bu kalıbı genişletebilirsiniz.  

### <a name="related-patterns-and-guidance"></a>İlgili düzenler ve kılavuzlar
Bu düzeni uygularken aşağıdaki düzenler ve yönergeler de yararlı olabilir:  

* Varlık grubu Işlemleri  
* [Birleştir veya Değiştir](#merge-or-replace)  

> [!NOTE]
> Çözümünüz için işlem yalıtımı önemliyse, Yumurtları kullanmanıza olanak tanımak için tablolarınızı yeniden tasarlamaya göz önünde bulundurmanız gerekir.  
> 
> 

## <a name="index-entities-pattern"></a>Dizin varlıkları kalıbı
Varlık listeleri döndüren etkili aramaları etkinleştirmek için Dizin varlıklarını koruyun.  

### <a name="context-and-problem"></a>Bağlam ve sorun
Tablo hizmeti, **partitionkey** ve **rowkey** değerlerini kullanarak varlıkları otomatik olarak dizine ekler. Bu, bir istemci uygulamanın bir nokta sorgusu kullanarak bir varlığı etkin bir şekilde almasını sağlar. Örneğin, aşağıda gösterilen tablo yapısını kullanarak, bir istemci uygulaması departman adını ve çalışan KIMLIĞINI ( **partitionkey** ve **rowkey**) kullanarak bireysel bir çalışan varlığını verimli bir şekilde alabilir.  

![Çalışan varlığı](media/storage-table-design-guide/storage-table-design-IMAGE13.png)

Ayrıca, son adı gibi, benzersiz olmayan başka bir özelliğin değerine göre çalışan varlıklarının bir listesini almak isterseniz, bunları doğrudan aramak için bir dizin kullanmak yerine eşleşmeleri bulmak için daha az verimli bir bölüm taraması kullanmanız gerekir. Bunun nedeni, tablo hizmetinin ikincil dizinler sağlamasunmamalıdır.  

### <a name="solution"></a>Çözüm
Yukarıda gösterilen varlık yapısıyla, son ada göre aramayı etkinleştirmek için, çalışan kimliklerinin listesini korumanız gerekir. Can gibi belirli bir soyadı olan çalışan varlıklarını almak isterseniz, ilk adı olarak Jones ile çalışanlar için çalışan kimlikleri listesini bulmanız ve ardından bu çalışan varlıklarını almanız gerekir. Çalışan kimliklerinin listesini depolamak için üç ana seçenek vardır:  

* Blob depolamayı kullanın.  
* Dizin varlıklarını, çalışan varlıklarıyla aynı bölümde oluşturun.  
* Ayrı bir bölümde veya tabloda Dizin varlıkları oluşturun.  

<u>Seçenek #1: blob depolamayı kullanma</u>  

İlk seçenek için, her benzersiz soyadı için bir blob oluşturun ve her bir blob 'da bu soyadı olan çalışanların **Partitionkey** (departman) ve **rowkey** (çalışan kimliği) değerlerinin bir listesini depolar. Bir çalışan eklediğinizde veya sildiğinizde, ilgili Blob içeriğinin son olarak çalışan varlıklarıyla tutarlı olduğundan emin olmanız gerekir.  

<u>Seçenek #2:</u> Aynı bölümde Dizin varlıkları oluşturma  

İkinci seçenek için aşağıdaki verileri depolayan Dizin varlıklarını kullanın:  

![Çalışan dizini varlığı](media/storage-table-design-guide/storage-table-design-IMAGE14.png)

**Employeeids** özelliği, **rowkey**içinde depolanan son ada sahip çalışanlar için çalışan kimliklerinin bir listesini içerir.  

Aşağıdaki adımlarda, ikinci seçeneği kullanıyorsanız yeni bir çalışan eklerken izlemeniz gereken işlem ana hatlarıyla verilmiştir. Bu örnekte, KIMLIĞI 000152 olan bir çalışan ve satış departmanında soyadı Jones olan bir ad veriyoruz:  

1. "Sales" **partitionkey** değerine ve "Jones" **rowkey** değerine sahip dizin varlığını alın. 2\. adımda kullanmak üzere bu varlığın ETag öğesini kaydedin.  
2. Yeni çalışan varlığını (**partitionkey** value "Sales" ve **rowkey** değeri "000152") ekleyen bir varlık grubu işlemi (bir toplu işlem) oluşturun ve Dizin varlığını (**partitionkey** değeri "Sales" ve **rowkey) güncelleştirir** "can"), yeni çalışan kimliğini Employeıdds alanındaki listeye ekleyerek. Varlık grubu işlemleri hakkında daha fazla bilgi için bkz. varlık grubu Işlemleri.  
3. Bir iyimser eşzamanlılık hatası nedeniyle varlık grubu işlemi başarısız olursa (başka biri dizin varlığını değiştirmiştir), 1. adımda tekrar başlatmanız gerekir.  

İkinci seçeneği kullanıyorsanız, bir çalışanı silmek için benzer bir yaklaşım kullanabilirsiniz. Üç varlığı güncelleştiren bir varlık grubu işlemi yürütmeniz gerektiğinden, çalışanın soyadını değiştirmek biraz daha karmaşıktır: çalışan varlığı, eski soyadı için Dizin varlığı ve yeni soyadı için Dizin varlığı. Daha sonra iyimser eşzamanlılık kullanarak güncelleştirmeleri gerçekleştirmek için kullanabileceğiniz ETag değerlerini almak için herhangi bir değişiklik yapmadan önce her bir varlığı almalısınız.  

Aşağıdaki adımlar, ikinci seçeneği kullanıyorsanız, bir departmandaki belirli bir soyadı olan tüm çalışanları aramanız gerektiğinde izlemeniz gereken işlemi özetler. Bu örnekte, satış departmanında en son adı Jones olan tüm çalışanları arıyorsunuz:  

1. "Sales" **partitionkey** değerine ve "Jones" **rowkey** değerine sahip dizin varlığını alın.  
2. Employeıdds alanındaki çalışan kimlikleri listesini ayrıştırın.  
3. Bu çalışanların (e-posta adresleri gibi) her biri hakkında daha fazla bilgiye ihtiyacınız varsa, 2. adımda elde ettiğiniz çalışanlar listesinden **partitionkey** değeri "Sales" ve **rowkey** değerlerini kullanarak çalışan varlıklarının her birini alın.  

<u>Seçenek #3:</u> Ayrı bir bölümde veya tabloda Dizin varlıkları oluşturma  

Üçüncü seçenek için aşağıdaki verileri depolayan Dizin varlıklarını kullanın:  

![Ayrı bir bölümde çalışan dizini varlığı](media/storage-table-design-guide/storage-table-design-IMAGE15.png)


**Employeeids** özelliği, **rowkey**içinde depolanan son ada sahip çalışanlar için çalışan kimliklerinin bir listesini içerir.  

Üçüncü seçenekle, Dizin varlıkları çalışan varlıklarından ayrı bir bölümde bulunduğundan tutarlılığı sürdürmek için Yumurtları kullanamazsınız. Dizin varlıklarının en sonunda çalışan varlıklarıyla tutarlı olduğundan emin olun.  

### <a name="issues-and-considerations"></a>Sorunlar ve dikkat edilmesi gerekenler
Bu düzenin nasıl uygulanacağına karar verirken aşağıdaki noktaları göz önünde bulundurun:  

* Bu çözüm, eşleşen varlıkları almak için en az iki sorgu gerektirir: bir tane, **Rowkey** değerlerinin listesini almak için Dizin varlıklarını sorgular ve ardından listedeki her varlığı almak için sorgular.  
* Tek bir varlığın en büyük boyutu 1 MB olduğunda, çözümde #3 seçenek #2 ve seçenek, herhangi bir son ad için çalışan kimliği listesinin 1 MB 'tan fazla olmadığı varsayılır. Çalışan kimlikleri listesinin boyutu 1 MB 'tan büyükse, seçenek #1 kullanın ve dizin verilerini BLOB depolama alanında depolayın.  
* Seçenek #2 kullanırsanız (çalışanları ekleme ve silme işlemlerini yapmak ve bir çalışanın soyadını değiştirmek), işlem hacmi belirli bir bölümdeki ölçeklenebilirlik sınırlarına Yaklaşarsa değerlendirmeniz gerekir. Bu durumda, güncelleştirme isteklerini işlemek için kuyrukları kullanan ve Dizin varlıklarınızı çalışan varlıklarından ayrı bir bölümde depolamanıza olanak sağlayan, sonunda tutarlı bir çözüm (seçenek #1 veya seçenek #3) göz önünde bulundurmanız gerekir.  
* Bu çözümde #2 seçenek, bir departman içindeki soyadı ile aramak istediğiniz varsayılır: Örneğin, satış departmanında soyadı Jones olan çalışanların bir listesini almak istiyorsunuz. Tüm kuruluş genelinde can soyadı olan tüm çalışanları aramak istiyorsanız, #1 veya seçenek #3 seçeneğini kullanın.
* Nihai tutarlılığı sunan kuyruk tabanlı bir çözüm uygulayabilirsiniz (daha fazla ayrıntı için [sonuçta tutarlı işlem düzenine](#eventually-consistent-transactions-pattern) bakın).  

### <a name="when-to-use-this-pattern"></a>Bu düzenin kullanılacağı durumlar
Tüm çalışanlar gibi ortak bir özellik değerini paylaşan bir varlık kümesini (can soyadı Jones olan tüm çalışanlar gibi) aramak istediğinizde bu stili kullanın.  

### <a name="related-patterns-and-guidance"></a>İlgili düzenler ve kılavuzlar
Bu düzeni uygularken aşağıdaki düzenler ve yönergeler de yararlı olabilir:  

* [Bileşik anahtar stili](#compound-key-pattern)  
* [Sonuçta tutarlı işlem kriteri](#eventually-consistent-transactions-pattern)  
* Varlık grubu Işlemleri  
* [Heterojen varlık türleriyle çalışma](#working-with-heterogeneous-entity-types)  

## <a name="denormalization-pattern"></a>Denormalleştirme stili
Tek bir nokta sorgusuyla ihtiyacınız olan tüm verileri almanızı sağlamak için ilgili verileri tek bir varlıkta birlikte birleştirin.  

### <a name="context-and-problem"></a>Bağlam ve sorun
İlişkisel bir veritabanında, verileri genellikle birden çok tablodan veri alan sorgulara neden olan çoğaltmayı kaldırmak için normalleştirin. Verilerinizi Azure tablolarında normalleştirin, ilişkili verilerinizi almak için istemciden sunucuya birden çok gidiş dönüş yapmanız gerekir. Örneğin, aşağıda gösterilen tablo yapısıyla, bir departmanın ayrıntılarını almak için iki gidiş dönüş gerekir: biri, yöneticinin KIMLIĞINI içeren departman varlığını getirmek ve sonra yöneticinin ayrıntılarını bir çalışan varlığında getirmek için bir istek.  

![Departman varlığı ve çalışan varlığı](media/storage-table-design-guide/storage-table-design-IMAGE16.png)

### <a name="solution"></a>Çözüm
Verileri iki ayrı varlıkta depolamak yerine, verileri yeniden oluşturup, Bölüm varlığındaki yöneticinin ayrıntılarının bir kopyasını saklayın. Örneğin:  

![Bölüm varlığı](media/storage-table-design-guide/storage-table-design-IMAGE17.png)

Bu özelliklerle depolanan departman varlıkları sayesinde, bir nokta sorgusu kullanarak bir departmanla ilgili olarak ihtiyaç duyduğunuz tüm ayrıntıları alabilirsiniz.  

### <a name="issues-and-considerations"></a>Sorunlar ve dikkat edilmesi gerekenler
Bu düzenin nasıl uygulanacağına karar verirken aşağıdaki noktaları göz önünde bulundurun:  

* Bazı verileri iki kez depolamaya ilişkin maliyet ek yükü vardır. Performans avantajı (depolama hizmetine daha az istek elde edilen) genellikle depolama maliyetlerinde marguinal artışı artırır (Bu maliyet, bir departmanın ayrıntılarını getirmek için ihtiyaç duyduğunuz işlem sayısında bir azalmayla kısmen denkleştirilir ).  
* Yöneticileriyle ilgili bilgileri depolayan iki varlığın tutarlılığını korumanız gerekir. Tek bir atomik işlemde birden fazla varlığı güncelleştirmek için Yumurtları kullanarak tutarlılık sorununu işleyebilirsiniz: Bu durumda, departman varlığı ve departman yöneticisinin çalışan varlığı aynı bölümde depolanır.  

### <a name="when-to-use-this-pattern"></a>Bu düzenin kullanılacağı durumlar
İlgili bilgileri sık sık aramanız gerektiğinde bu stili kullanın. Bu model, istemcinizin gerektirdiği verileri almak için yapması gereken sorgu sayısını azaltır.  

### <a name="related-patterns-and-guidance"></a>İlgili düzenler ve kılavuzlar
Bu düzeni uygularken aşağıdaki düzenler ve yönergeler de yararlı olabilir:  

* [Bileşik anahtar stili](#compound-key-pattern)  
* Varlık grubu Işlemleri  
* [Heterojen varlık türleriyle çalışma](#working-with-heterogeneous-entity-types)

## <a name="compound-key-pattern"></a>Bileşik anahtar stili
Bir istemcinin tek nokta sorgusuyla ilgili verileri araması için bileşik **Rowkey** değerlerini kullanın.  

### <a name="context-and-problem"></a>Bağlam ve sorun
İlişkisel bir veritabanında, ilgili veri parçalarını tek bir sorgudaki istemciye döndürmek için sorgularda birleştirmeleri kullanmak doğal bir veritabanıdır. Örneğin, ilgili varlıkların bir listesini aramak ve bu çalışana yönelik verileri gözden geçirmek için çalışan KIMLIĞINI kullanabilirsiniz.  

Aşağıdaki yapıyı kullanarak, çalışan varlıklarını tablo hizmetinde depoladığını varsayın:  

![Çalışan varlık yapısı](media/storage-table-design-guide/storage-table-design-IMAGE18.png)

Ayrıca, çalışanın kuruluşunuz için çalıştığı her bir yılda incelemeler ve performansla ilgili geçmiş verileri depolamanız gerekir ve bu bilgilere yıla göre erişebilmek için ihtiyacınız vardır. Bir seçenek, aşağıdaki yapıyla varlıkları depolayan başka bir tablo oluşturmaktır:  

![Alternatif çalışan varlık yapısı](media/storage-table-design-guide/storage-table-design-IMAGE19.png)

Bu yaklaşımda, verilerinizi tek bir istekle almanızı sağlamak için yeni varlıktaki bazı bilgileri (örneğin, ilk adı ve soyadı) çoğaltmaya karar verebilirsiniz. Ancak, bu iki varlığı otomatik olarak güncelleştirmek için EGT 'yi kullanamadığından güçlü tutarlılığı koruyamezsiniz.  

### <a name="solution"></a>Çözüm
Aşağıdaki yapıya sahip varlıkları kullanarak özgün tablonuzda yeni bir varlık türü depolayın:  

![Çalışan varlık yapısına yönelik çözüm](media/storage-table-design-guide/storage-table-design-IMAGE20.png)

**Rowkey** 'in artık çalışan kimliğinden ve tek bir varlık için tek bir istek ile verileri incelemenize olanak sağlayan İnceleme verilerinin yılından oluşan bir bileşik anahtar olduğuna dikkat edin.  

Aşağıdaki örnek, belirli bir çalışana ait tüm gözden geçirme verilerini (örneğin, satış departmanında çalışan 000123) nasıl alacağınızı özetler:  

$filter = (PartitionKey EQ ' Sales ') ve (RowKey Ge ' empid_000123 ') ve (RowKey lt ' empid_000124 ') & $select = RowKey, yönetici derecelendirmesi, eş derecelendirme, açıklamalar  

### <a name="issues-and-considerations"></a>Sorunlar ve dikkat edilmesi gerekenler
Bu düzenin nasıl uygulanacağına karar verirken aşağıdaki noktaları göz önünde bulundurun:  

* **Rowkey** değerini ayrıştırmayı kolaylaştıran uygun bir ayırıcı karakter kullanmanız gerekir: Örneğin, **000123_2012**.  
* Ayrıca, bu varlığı aynı çalışanla ilgili verileri içeren diğer varlıklarla aynı bölümde saklarsınız, bu da güçlü tutarlılığı sağlamak için Yumurtları kullanabileceğiniz anlamına gelir.
* Bu düzenin uygun olup olmadığını anlamak için verileri ne sıklıkta sorgulayacağınızı düşünmeniz gerekir.  Örneğin, İnceleme verilerine sık sık ve ana çalışan verilerine erişebiliyorsanız, bunları ayrı varlıklar olarak saklamanız gerekir.  

### <a name="when-to-use-this-pattern"></a>Bu düzenin kullanılacağı durumlar
Sık olarak Sorgulayabileceğiniz bir veya daha fazla ilgili varlığı depolamanız gerektiğinde bu stili kullanın.  

### <a name="related-patterns-and-guidance"></a>İlgili düzenler ve kılavuzlar
Bu düzeni uygularken aşağıdaki düzenler ve yönergeler de yararlı olabilir:  

* Varlık grubu Işlemleri  
* [Heterojen varlık türleriyle çalışma](#working-with-heterogeneous-entity-types)  
* [Sonuçta tutarlı işlem kriteri](#eventually-consistent-transactions-pattern)  

## <a name="log-tail-pattern"></a>Günlük kuyruk kalıbı
Ters tarih ve saat düzeninde sıralama yaparak bir **Rowkey** değeri kullanarak bir bölüme en son eklenen *n* varlıklarını alın.  

### <a name="context-and-problem"></a>Bağlam ve sorun
Yaygın bir gereksinim, en son oluşturulan varlıkları (örneğin, bir çalışan tarafından gönderilen en son 10 gider talebi) alabilmiştir. Tablo sorguları bir kümeden ilk *n* varlığı döndürmek için **$top** bir sorgu işlemini destekler: bir küme içindeki son n varlıkları döndürmek için eşdeğer bir sorgu işlemi yoktur.  

### <a name="solution"></a>Çözüm
Bir **Rowkey** kullanarak, Doğal Tarih/saat düzeninde sıralama yaparak, en son girişin her zaman tablodaki ilk bir değer olması için varlıkları saklayın.  

Örneğin, bir çalışan tarafından gönderilen en son 10 gider talebini alabilmesi için geçerli tarih/saatten türetilmiş bir ters değer değeri kullanabilirsiniz. Aşağıdaki C# kod örneğinde, en sonuncudan en eskiye doğru sıralayan bir **rowkey** için uygun bir "ters kullanım" değeri oluşturmanın bir yolu gösterilmektedir:  

`string invertedTicks = string.Format("{0:D19}", DateTime.MaxValue.Ticks - DateTime.UtcNow.Ticks);`  

Aşağıdaki kodu kullanarak tarih saat değerine geri dönebilirsiniz:  

`DateTime dt = new DateTime(DateTime.MaxValue.Ticks - Int64.Parse(invertedTicks));`  

Tablo sorgusu şöyle görünür:  

`https://myaccount.table.core.windows.net/EmployeeExpense(PartitionKey='empid')?$top=10`  

### <a name="issues-and-considerations"></a>Sorunlar ve dikkat edilmesi gerekenler
Bu düzenin nasıl uygulanacağına karar verirken aşağıdaki noktaları göz önünde bulundurun:  

* Dize değerinin beklenildiği şekilde sıralandığından emin olmak için ters değer değerini önünde sıfır ile ayarlamalısınız.  
* Bir bölüm düzeyinde ölçeklenebilirlik hedeflerini bilmeniz gerekir. Etkin nokta bölümleri oluşturmamaya dikkat edin.  

### <a name="when-to-use-this-pattern"></a>Bu düzenin kullanılacağı durumlar
Varlıklara ters tarih/saat düzeninde erişmeniz gerektiğinde veya en son eklenen varlıklara erişmeniz gerektiğinde bu düzeni kullanın.  

### <a name="related-patterns-and-guidance"></a>İlgili düzenler ve kılavuzlar
Bu düzeni uygularken aşağıdaki düzenler ve yönergeler de yararlı olabilir:  

* [Prepend/Append kenar yumuşatma](#prepend-append-anti-pattern)  
* [Varlıkları alma](#retrieving-entities)  

## <a name="high-volume-delete-pattern"></a>Yüksek hacimli silme kalıbı
Aynı anda tüm varlıkları kendi ayrı tablosunda eşzamanlı silme için depolayarak yüksek hacimli varlıkların silinmesini etkinleştirin; tabloyu silerek varlıkları silersiniz.  

### <a name="context-and-problem"></a>Bağlam ve sorun
Birçok uygulama, artık bir istemci uygulaması için kullanılabilir olmayan eski verileri siler ya da uygulamanın başka bir depolama ortamına arşivlenmesi gerekir. Genellikle bu verileri bir tarih ile belirlersiniz: Örneğin, 60 günden daha eski olan tüm oturum açma isteklerinin kayıtlarını silme gereksinimleriniz vardır.  

Olası bir tasarım, **Rowkey**içindeki oturum açma isteğinin tarih ve saatini kullanmaktır:  

![Oturum açma girişiminin tarih ve saati](media/storage-table-design-guide/storage-table-design-IMAGE21.png)

Bu yaklaşım, uygulama ayrı bir bölümdeki her bir kullanıcı için oturum açma varlıkları ekleyip silebildiğinden, bölüm etkin noktalarını önler. Ancak, çok sayıda varlık varsa bu yaklaşım maliyetli ve zaman alıcı olabilir, çünkü öncelikle silinecek tüm varlıkların tanımlanması için tablo taraması gerçekleştirmeniz gerekir, sonra da her bir eski varlığı silmeniz gerekir. Birden çok Delete isteğini bölümlere ayırarak eski varlıkları silmek için gereken sunucuya gidiş dönüş sayısını azaltabilirsiniz.  

### <a name="solution"></a>Çözüm
Her gün oturum açma girişimi için ayrı bir tablo kullanın. Varlıkları eklerken etkin noktalara engel olmak için yukarıdaki varlık tasarımını kullanabilirsiniz ve eski varlıkları silmek, yüzlerce ve binlerce bireyi bulmak ve silmek yerine yalnızca bir tablonun her gün (tek bir depolama işlemi) silinmesine yönelik bir sorudır. Her gün oturum açma varlıkları.  

### <a name="issues-and-considerations"></a>Sorunlar ve dikkat edilmesi gerekenler
Bu düzenin nasıl uygulanacağına karar verirken aşağıdaki noktaları göz önünde bulundurun:  

* Tasarımınız, uygulamanızın belirli varlıkları aramak, diğer verilerle bağlantı kurmak veya toplu bilgi oluşturmak gibi verileri kullanması için bu yolları destekliyor mu?  
* Yeni varlıkları eklerken tasarımınız etkin noktaları önmidir?  
* Aynı tablo adını sildikten sonra yeniden kullanmak istiyorsanız bir gecikme bekliyor. Her zaman benzersiz tablo adları kullanmak daha iyidir.  
* Tablo hizmeti erişim düzenlerini öğrenirken ve bölümleri düğümler arasında dağıttığı sırada yeni bir tablo kullandığınızda bazı azaltmalar beklenir. Yeni tablolar oluşturmanız gereken sıklığı göz önünde bulundurmanız gerekir.  

### <a name="when-to-use-this-pattern"></a>Bu düzenin kullanılacağı durumlar
Aynı anda silmeniz gereken yüksek bir varlık hacminin olması durumunda bu kalıbı kullanın.  

### <a name="related-patterns-and-guidance"></a>İlgili düzenler ve kılavuzlar
Bu düzeni uygularken aşağıdaki düzenler ve yönergeler de yararlı olabilir:  

* Varlık grubu Işlemleri
* [Varlıkları değiştirme](#modifying-entities)  

## <a name="data-series-pattern"></a>Veri serisi stili
Yaptığınız istek sayısını en aza indirmek için, tüm veri serisini tek bir varlıkta depolayın.  

### <a name="context-and-problem"></a>Bağlam ve sorun
Yaygın bir senaryo, bir uygulamanın, genellikle hepsini bir kez alması gereken veri serisini depolaması içindir. Örneğin, uygulamanız her bir çalışanın kaç tane ım iletisi göndereceğini kaydedebilir ve sonra bu bilgileri kullanarak her bir kullanıcının önceki 24 saat içinde kaç ileti gönderdiğini çizebilirsiniz. Tek bir tasarım, her çalışan için 24 varlık depolamak olabilir:  

![Her çalışan için 24 varlık depolayın](media/storage-table-design-guide/storage-table-design-IMAGE22.png)

Bu tasarım sayesinde, uygulamanın ileti sayısı değerini güncelleştirmesi gerektiğinde her bir çalışana yönelik varlık için kolayca bulma ve güncelleştirme yapabilirsiniz. Bununla birlikte, önceki 24 saat için etkinliğin bir grafiğini çizmek üzere bilgileri almak için, 24 varlık almanız gerekir.  

### <a name="solution"></a>Çözüm
Her saat için ileti sayısını depolamak üzere aşağıdaki tasarımı ayrı bir özellikle kullanın:  

![İleti istatistikleri varlığı](media/storage-table-design-guide/storage-table-design-IMAGE23.png)

Bu tasarımla, bir çalışana ait ileti sayısını belirli bir saat için güncelleştirmek üzere bir birleştirme işlemi kullanabilirsiniz. Şimdi, tek bir varlık için bir istek kullanarak grafiği çizmek için ihtiyacınız olan tüm bilgileri alabilirsiniz.  

### <a name="issues-and-considerations"></a>Sorunlar ve dikkat edilmesi gerekenler
Bu düzenin nasıl uygulanacağına karar verirken aşağıdaki noktaları göz önünde bulundurun:  

* Tüm veri seriniz tek bir varlığa uymuyorsa (bir varlık en fazla 252 özellik içerebilir), blob gibi alternatif bir veri deposu kullanın.  
* Bir varlığı aynı anda güncelleştiren birden fazla istemciniz varsa, iyimser eşzamanlılık uygulamak için **ETag** 'i kullanmanız gerekir. Çok sayıda istemciniz varsa, yüksek çekişme yaşayabilirsiniz.  

### <a name="when-to-use-this-pattern"></a>Bu düzenin kullanılacağı durumlar
Tek bir varlıkla ilişkili bir veri serisini güncelleştirmeniz ve almanız gerektiğinde bu stili kullanın.  

### <a name="related-patterns-and-guidance"></a>İlgili düzenler ve kılavuzlar
Bu düzeni uygularken aşağıdaki düzenler ve yönergeler de yararlı olabilir:  

* [Büyük varlıklar kalıbı](#large-entities-pattern)  
* [Birleştir veya Değiştir](#merge-or-replace)  
* [Sonuçta tutarlı işlem kriteri](#eventually-consistent-transactions-pattern) (veri serisini bir blob 'da depoluyorsanız)  

## <a name="wide-entities-pattern"></a>Geniş varlıklar kalıbı
252 'den fazla özelliği olan mantıksal varlıkları depolamak için birden çok fiziksel varlık kullanın.  

### <a name="context-and-problem"></a>Bağlam ve sorun
Tek bir varlık 252 'den fazla özelliğe sahip olamaz (zorunlu sistem özellikleri hariç) ve toplamda 1 MB 'den fazla veri depolayabilirler. İlişkisel bir veritabanında, genellikle yeni bir tablo ekleyerek ve aralarında 1 ' den fazla ilişki zorunlu tutarak bir satırın boyutu için herhangi bir sınırı yuvarlayacaktır.  

### <a name="solution"></a>Çözüm
Tablo hizmetini kullanarak, birden fazla varlığı, 252 'den fazla özelliği olan tek bir büyük ölçekli bir iş nesnesini temsil etmek üzere saklayabilirsiniz. Örneğin, son 365 gün boyunca her bir çalışan tarafından gönderilen anlık ileti iletilerinin sayısını depolamak istiyorsanız, farklı şemalarla iki varlık kullanan aşağıdaki tasarımı kullanabilirsiniz:  

![Birden çok varlık](media/storage-table-design-guide/storage-table-design-IMAGE24.png)

Her iki varlığın de birbirleriyle eşitlenmiş halde tutulması için güncelleştirilmesi gereken bir değişiklik yapmanız gerekiyorsa, bir EGT kullanabilirsiniz. Aksi takdirde, belirli bir güne ait ileti sayısını güncelleştirmek için tek bir birleştirme işlemi kullanabilirsiniz. Tek bir çalışana ait tüm verileri almak için her iki varlığı de almalısınız. Bu, hem **Partitionkey** hem de **rowkey** değeri kullanan iki verimli istek ile gerçekleştirebilirsiniz.  

### <a name="issues-and-considerations"></a>Sorunlar ve dikkat edilmesi gerekenler
Bu düzenin nasıl uygulanacağına karar verirken aşağıdaki noktaları göz önünde bulundurun:  

* Tüm mantıksal varlıkların alınması, en az iki depolama işlemi içerir: bir fiziksel varlığın alınması için bir tane.  

### <a name="when-to-use-this-pattern"></a>Bu düzenin kullanılacağı durumlar
Boyutu veya özellikleri olan varlıkların, tablo hizmetindeki tek bir varlık için sınırları aşan varlıkları depolaması gerektiğinde bu stili kullanın.  

### <a name="related-patterns-and-guidance"></a>İlgili düzenler ve kılavuzlar
Bu düzeni uygularken aşağıdaki düzenler ve yönergeler de yararlı olabilir:  

* Varlık grubu Işlemleri
* [Birleştir veya Değiştir](#merge-or-replace)

## <a name="large-entities-pattern"></a>Büyük varlıklar kalıbı
Büyük özellik değerlerini depolamak için BLOB depolama kullanın.  

### <a name="context-and-problem"></a>Bağlam ve sorun
Tek bir varlık, toplamda 1 MB 'den fazla veri saklayabilir. Özelliklerden biri veya birkaçı, varlığınızın toplam boyutunun bu değeri aşmasına neden olan değerleri depolu, tüm varlığın tablo hizmetinde depolayamamanız gerekir.  

### <a name="solution"></a>Çözüm
Bir veya daha fazla özellik büyük miktarda veri içerdiği için varlığınız 1 MB 'ı aşarsa, verileri blob hizmetinde saklayabilir ve sonra blob adresini varlıktaki bir özellikte saklayabilirsiniz. Örneğin, bir çalışanın fotoğrafını BLOB depolama alanında saklayabilir ve çalışan varlığınızın **fotoğraf** özelliğinde fotoğrafın bağlantısını kaydedebilirsiniz:  

![Foto özelliği](media/storage-table-design-guide/storage-table-design-IMAGE25.png)

### <a name="issues-and-considerations"></a>Sorunlar ve dikkat edilmesi gerekenler
Bu düzenin nasıl uygulanacağına karar verirken aşağıdaki noktaları göz önünde bulundurun:  

* Tablo hizmetindeki varlık ve BLOB hizmetindeki veriler arasında nihai tutarlılığı sürdürmek için, varlıklarınızı sürdürmek üzere [sonuçta tutarlı işlem düzenlerini](#eventually-consistent-transactions-pattern) kullanın.
* Tüm varlıkların alınması, en az iki depolama işlemi içerir: bir varlık ve blob verilerini almak için bir tane.  

### <a name="when-to-use-this-pattern"></a>Bu düzenin kullanılacağı durumlar
Boyutu, tablo hizmetindeki tek bir varlık için olan sınırları aşan varlıkları depolamanız gerektiğinde bu stili kullanın.  

### <a name="related-patterns-and-guidance"></a>İlgili düzenler ve kılavuzlar
Bu düzeni uygularken aşağıdaki düzenler ve yönergeler de yararlı olabilir:  

* [Sonuçta tutarlı işlem kriteri](#eventually-consistent-transactions-pattern)  
* [Geniş varlıklar kalıbı](#wide-entities-pattern)

<a name="prepend-append-anti-pattern"></a>

## <a name="prependappend-anti-pattern"></a>Prepend/Append kenar yumuşatma
Birçok bölüm arasında eklemeleri yayarak yüksek miktarda araya sahip olduğunuzda ölçeklenebilirliği artırın.  

### <a name="context-and-problem"></a>Bağlam ve sorun
Depolanan varlıklarınıza ön bekliyor veya varlıklar eklemek, genellikle uygulamanın bölüm dizisinin ilk veya son bölümüne yeni varlıklar eklenmesine neden olur. Bu durumda, belirli bir zaman içindeki tüm ekler aynı bölümde gerçekleşirken, tablo hizmeti 'nin birden çok düğümde yük dengelemesi yapmasına engel olan ve muhtemelen uygulamanızın ölçeklenebilirlik hedeflerine ulaşmasına neden olan bir etkin nokta oluşturulur. bölümünüzün. Örneğin, çalışanlar tarafından ağ ve kaynak erişimini kaydeden bir uygulamanız varsa, işlem hacmi bir için ölçeklenebilirlik hedefine ulaşırsa, aşağıda gösterildiği gibi bir varlık yapısı, geçerli saatin bölümünün bir etkin noktaya dönüşmesine neden olabilir. ayrı Bölüm:  

![Varlık yapısı](media/storage-table-design-guide/storage-table-design-IMAGE26.png)

### <a name="solution"></a>Çözüm
Aşağıdaki alternatif varlık yapısı, uygulama günlüğe olay günlüğü olarak belirli bir bölümdeki etkin noktayı önler:  

![Alternatif varlık yapısı](media/storage-table-design-guide/storage-table-design-IMAGE27.png)

Bu örneğe, hem **Partitionkey** hem de **rowkey** 'in nasıl bileşik anahtarlar olduğuna dikkat edin. **Partitionkey** , günlüğü birden çok bölüme dağıtmak için hem departmanı hem de çalışan kimliğini kullanır.  

### <a name="issues-and-considerations"></a>Sorunlar ve dikkat edilmesi gerekenler
Bu düzenin nasıl uygulanacağına karar verirken aşağıdaki noktaları göz önünde bulundurun:  

* Ekleme üzerinde etkin bölümler oluşturmayı önleyen alternatif anahtar yapısı, istemci uygulamanızın yaptığı sorguları verimli bir şekilde destekler mi?  
* Tahmini işlem hacmidir, tek bir bölüm için ölçeklenebilirlik hedeflerine ulaşmanıza ve depolama hizmeti tarafından kısıtlanacak.  

### <a name="when-to-use-this-pattern"></a>Bu düzenin kullanılacağı durumlar
Etkin bir bölüme eriştiğinizde, işlem hacminin depolama hizmeti tarafından azaltılmaya neden olduğu durumlarda, ön uç/ekleme önleme deseninin önüne kaçının.  

### <a name="related-patterns-and-guidance"></a>İlgili düzenler ve kılavuzlar
Bu düzeni uygularken aşağıdaki düzenler ve yönergeler de yararlı olabilir:  

* [Bileşik anahtar stili](#compound-key-pattern)  
* [Günlük kuyruk kalıbı](#log-tail-pattern)  
* [Varlıkları değiştirme](#modifying-entities)  

## <a name="log-data-anti-pattern"></a>Günlük verileri Anti-model
Genellikle, günlük verilerini depolamak için tablo hizmeti yerine blob hizmetini kullanmanız gerekir.  

### <a name="context-and-problem"></a>Bağlam ve sorun
Günlük verileri için yaygın kullanım örneği, belirli bir tarih/saat aralığı için günlük girişlerinin bir seçimini almadır: Örneğin, uygulamanızın 15:04 ve 15:06 arasında günlüğe kaydedildiği tüm hata ve kritik iletileri belirli bir tarihte bulmak istiyorsunuz. Günlük varlıklarının kaydedilmesini istediğiniz bölümü öğrenmek için günlük iletisinin tarih ve saatini kullanmak istemezsiniz: Bu, belirli bir zamanda, tüm günlük varlıklarının aynı **Partitionkey** değerini paylaşılacağını sağlar (bkz. [Bu bölüm, kenar yumuşatma ekleme](#prepend-append-anti-pattern)). Örneğin, bir günlük iletisi için aşağıdaki varlık şeması, uygulama tüm günlük iletilerini geçerli tarih ve saat için bölüme yazdığından, etkin bir bölüm ile sonuçlanır:  

![İleti varlığını günlüğe kaydet](media/storage-table-design-guide/storage-table-design-IMAGE28.png)

Bu örnekte, **Rowkey** , günlük iletilerinin tarih/saat düzeninde sıralanmış olarak depolanmasını sağlamak için günlük iletisinin tarih ve saatini içerir ve birden çok günlük iletisinin aynı tarih ve saati paylaştığı durumlarda BIR ileti kimliği içerir.  

Başka bir yaklaşım, uygulamanın iletileri bir dizi bölüm arasında yazmalarını sağlayan bir **Partitionkey** kullanmaktır. Örneğin, günlük iletisinin kaynağı birçok bölüm arasında ileti dağıtmak için bir yol sağlıyorsa aşağıdaki varlık şemasını kullanabilirsiniz:  

![Alternatif günlük iletisi varlığı](media/storage-table-design-guide/storage-table-design-IMAGE29.png)

Bununla birlikte, bu şemayla ilgili sorun belirli bir zaman dilimi için tüm günlük iletilerini almak için tablodaki her bölümde arama yapmanız gerekir.

### <a name="solution"></a>Çözüm
Önceki bölümde, günlük girişlerini depolamak için tablo hizmetini kullanma girişimi ve önerilen iki, yetersiz, tasarım sorunu vurgulanmıştır. Sık erişimli bir bölüme yönelik bir çözüm, günlük iletilerinin düşük performans yazma riskiyle birlikte çalışır; diğer çözüm, belirli bir zaman aralığı için günlük iletilerini almak üzere tablodaki her bölümü taramak için gerekli olan sorgu performansının düşmesine neden oldu. BLOB depolama, bu tür bir senaryo için daha iyi bir çözüm sunar ve Azure Depolama Analizi topladığı günlük verilerini depolar.  

Bu bölümde, genellikle aralığa göre sorgulayan verileri depolamak için bu yaklaşımın bir gösterimi olarak, Depolama Analizi günlük verilerini blob depolamada nasıl depolayacağı özetlenmektedir.  

Depolama Analizi, günlük iletilerini birden fazla blobda ayrılmış bir biçimde depolar. Ayrılmış biçim, bir istemci uygulamanın günlük iletisindeki verileri ayrıştırmayı kolaylaştırır.  

Depolama Analizi, arama yaptığınız günlük iletilerini içeren Blobu (veya Blobları) bulmanızı sağlayan Bloblar için bir adlandırma kuralı kullanır. Örneğin, "Queue/2014/07/31/1800/000001. log" adlı bir blob, 18:00 Temmuz 2014 tarihinde başlayan saat için kuyruk hizmetiyle ilgili günlük iletileri içerir. "000001", bu dönemin ilk günlük dosyası olduğunu gösterir. Depolama Analizi Ayrıca, blob 'un meta verilerinin bir parçası olarak dosyada depolanan ilk ve son günlük iletilerinin zaman damgalarını kaydeder. BLOB depolama için API, bir ad önekini temel alarak bir kapsayıcıdaki Blobları bulmanıza olanak sağlar: 18:00 ile başlayan saat için kuyruk günlüğü verilerini içeren tüm Blobları bulmak için, "Queue/2014/07/31/1800" önekini kullanabilirsiniz.  

Depolama Analizi günlük iletilerini dahili olarak arabelleğe alır ve uygun blobu düzenli olarak güncelleştirir veya en son günlük girişi toplu işlem ile yeni bir tane oluşturur. Bu, blob hizmetinde gerçekleştirmesi gereken yazma sayısını azaltır.  

Kendi uygulamanızda benzer bir çözüm uygularız, güvenilirlik (olduğu gibi BLOB depolamaya her günlük girişini yazma) ve maliyet ve ölçeklenebilirlik (uygulamanızdaki güncelleştirmeleri arabelleğe alma ve yazma gibi) arasında dengelemeyi yönetmeyi düşünmelisiniz Bunları toplu halde blob depolamaya atayabilirsiniz).  

### <a name="issues-and-considerations"></a>Sorunlar ve dikkat edilmesi gerekenler
Günlük verilerini nasıl depolayacağınıza karar verirken aşağıdaki noktaları göz önünde bulundurun:  

* Olası sık kullanılan bölümleri engelleyen bir tablo tasarımı oluşturursanız, günlük verilerinize verimli bir şekilde erişemeyebilirsiniz.  
* Günlük verilerini işlemek için bir istemcinin genellikle birçok kayıt yüklemesi gerekir.  
* Günlük verileri genellikle yapılandırılmış olsa da, BLOB depolama daha iyi bir çözüm olabilir.  

## <a name="implementation-considerations"></a>Uygulama konuları
Bu bölümde, önceki bölümlerde açıklanan desenleri uyguladığınızda göz önünde bulundurmanız gereken bazı noktalar açıklanmaktadır. Bu bölümün çoğu, depolama Istemci kitaplığını ( C# yazma sırasında sürüm 4.3.0) kullanan içinde yazılmış örnekleri kullanır.  

## <a name="retrieving-entities"></a>Varlıkları alma
Sorgulama için bölüm tasarımında anlatıldığı gibi, en verimli sorgu bir nokta sorgusudur. Ancak, bazı senaryolarda birden fazla varlık almanız gerekebilir. Bu bölümde, depolama Istemci kitaplığı kullanılarak varlıkların alınması için bazı yaygın yaklaşımlar açıklanmaktadır.  

### <a name="executing-a-point-query-using-the-storage-client-library"></a>Depolama Istemci kitaplığını kullanarak bir nokta sorgusu yürütme
Nokta sorgusu çalıştırmanın en kolay yolu, "Sales" değerli bir varlık **anahtarı** ve "212" değerli bir C# **rowkey** içeren bir varlığı alan aşağıdaki kod parçacığında gösterildiği gibi, tablo **Al** işlemini kullanmaktır:  

```csharp
TableOperation retrieveOperation = TableOperation.Retrieve<EmployeeEntity>("Sales", "212");
var retrieveResult = employeeTable.Execute(retrieveOperation);
if (retrieveResult.Result != null)
{
    EmployeeEntity employee = (EmployeeEntity)retrieveResult.Result;
    ...
}  
```

Bu örneğin, aldığı varlığın **employeeentity**türünde olmasını nasıl beklediğine dikkat edin.  

### <a name="retrieving-multiple-entities-using-linq"></a>LINQ kullanarak birden çok varlık alma
Microsoft Azure Cosmos tablo standart kitaplığıyla çalışırken tablo hizmetinden birden fazla varlık almak için LINQ kullanabilirsiniz. 

```cli
dotnet add package Microsoft.Azure.Cosmos.Table
```

Aşağıdaki örneklerin çalışmasını sağlamak için ad alanlarını dahil etmeniz gerekir:

```csharp
using System.Linq;
using Microsoft.Azure.Cosmos.Table;
using Microsoft.Azure.Cosmos.Table.Queryable;
```

EmployeeTable, bir TableQuery\<ıtableentity > döndüren bir CreateQuery\<ıtableentity > () yöntemi uygulayan bir CloudTable nesnesidir. Bu türden nesneler bir IQueryable uygular ve hem LINQ sorgu Ifadeleri hem de nokta gösterimi sözdizimi kullanılmasına izin verir.

**WHERE** yan tümcesi içeren bir sorgu belirtilerek birden çok varlık alınıyor ve elde edilecek. Tablo taramasını önlemek için WHERE yan tümcesinde **Partitionkey** değerini her zaman dahil etmeniz ve tablo ve bölüm taramalarından kaçınmak Için **rowkey** değeri olması gerekir. Tablo hizmeti WHERE yan tümcesinde kullanılmak üzere sınırlı sayıda karşılaştırma işleci (büyüktür, büyüktür veya eşittir, küçüktür, küçüktür veya eşittir, eşittir ve eşit değildir) kümesini destekler. 

Aşağıdaki C# kod parçacığı, son adı "B" ile başlayan tüm çalışanları bulur ( **rowkey** 'in soyadı **, Bölüm adını depoladığını** varsayarak).  

```csharp
TableQuery<EmployeeEntity> employeeQuery = employeeTable.CreateQuery<EmployeeEntity>();
var query = (from employee in employeeQuery
            where employee.PartitionKey == "Sales" &&
            employee.RowKey.CompareTo("B") >= 0 &&
            employee.RowKey.CompareTo("C") < 0
            select employee).AsTableQuery();
            
var employees = query.Execute();  
```

Daha iyi performans sağlamak için sorgunun hem **Rowkey** hem de **partitionkey** 'i nasıl belirttiğinden emin olun.  

Aşağıdaki kod örneği, LINQ söz dizimini kullanmadan eşdeğer işlevselliği gösterir:  

```csharp
TableQuery<EmployeeEntity> employeeQuery = 
    new TableQuery<EmployeeEntity>().Where(
        TableQuery.CombineFilters(
            TableQuery.CombineFilters(
                TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Sales"),
                TableOperators.And,
                TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThanOrEqual, "B")),
            TableOperators.And,
            TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, "C")));
            
var employees = employeeTable.ExecuteQuery(employeeQuery);  
```

> [!NOTE]
> Örnek, üç filtre koşullarını dahil etmek için birden çok **CombineFilters** yöntemi sağlar.  
> 
> 

### <a name="retrieving-large-numbers-of-entities-from-a-query"></a>Bir sorgudan çok sayıda varlık alma
En iyi sorgu, bir **Partitionkey** değerine ve **rowkey** değerine göre tek bir varlık döndürür. Ancak, bazı senaryolarda aynı bölümden veya birçok bölümden bile birçok varlık döndürme gereksinimiyle karşılaşabilirsiniz.  

Bu senaryolarda uygulamanızın performansını her zaman tam olarak test etmelisiniz.  

Tablo hizmetine yönelik bir sorgu, bir seferde en fazla 1.000 varlık döndürebilir ve en fazla beş saniye boyunca çalıştırılabilir. Sonuç kümesi 1.000 'den fazla varlık içeriyorsa, sorgu beş saniye içinde tamamlanmazsa veya sorgu Bölüm sınırını geçiyorsa, tablo hizmeti istemci uygulamanın bir sonraki varlık kümesini istemesini sağlamak için bir devamlılık belirteci döndürür. Devamlılık belirteçlerinin nasıl çalıştığı hakkında daha fazla bilgi için bkz. [sorgu zaman aşımı ve sayfalandırma](https://msdn.microsoft.com/library/azure/dd135718.aspx).  

Depolama Istemci kitaplığını kullanıyorsanız, tablo hizmetinden varlıkları döndürdüğünde, devamlılık belirteçlerini otomatik olarak işleyebilir. Aşağıdaki C# kod örneği, tablo hizmeti bunları bir yanıt halinde döndürürse, depolama istemci kitaplığı kullanılarak devam belirteçleri otomatik olarak işler:  

```csharp
string filter = TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Sales");
TableQuery<EmployeeEntity> employeeQuery = new TableQuery<EmployeeEntity>().Where(filter);

var employees = employeeTable.ExecuteQuery(employeeQuery);
foreach (var emp in employees)
{
    // ...
}  
```

Aşağıdaki C# kod, devamlılık belirteçlerini açıkça işler:  

```csharp
string filter = TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Sales");
TableQuery<EmployeeEntity> employeeQuery = new TableQuery<EmployeeEntity>().Where(filter);

TableContinuationToken continuationToken = null;
do
{
    var employees = employeeTable.ExecuteQuerySegmented(employeeQuery, continuationToken);
    foreach (var emp in employees)
    {
        // ...
    }
    
    continuationToken = employees.ContinuationToken;
} while (continuationToken != null);  
```

Devamlılık belirteçlerini açık bir şekilde kullanarak, uygulamanızın sonraki veri segmentini ne zaman alacağını denetleyebilirsiniz. Örneğin, istemci uygulamanız kullanıcıların bir tabloda depolanan varlıklar aracılığıyla sayfa oluşturmasını sağladığından, bir Kullanıcı sorgu tarafından alınan tüm varlıklarda sayfa eklemek için, uygulamanızın bir sonraki segmenti almak için yalnızca bir devamlılık belirteci kullanmasını sağlayacak şekilde, Kullanıcı, geçerli kesimdeki tüm varlıklar aracılığıyla disk belleğini bitirdi. Bu yaklaşımın çeşitli avantajları vardır:  

* Tablo hizmetinden alınacak ve ağ üzerinden taşıdığınız veri miktarını sınırlandırmanıza olanak sağlar.  
* .NET ' te zaman uyumsuz GÇ gerçekleştirmenize olanak sağlar.  
* Uygulama kilitlenmesinin durumunda devam edebilmeniz için devamlılık belirtecini kalıcı depolama alanına serileştirmenize olanak sağlar.  

> [!NOTE]
> Devamlılık belirteci genellikle 1.000 varlık içeren bir segmenti döndürür, ancak daha az olabilir. Bu Ayrıca, arama ölçütlerinizle eşleşen ilk n varlığı döndürmek için **Take** kullanarak bir sorgunun döndürdüğü giriş sayısını sınırlamanız durumunda da olur: tablo hizmeti, bir devamlılık belirteci ile birlikte n 'den az varlık içeren bir segment döndürebilir kalan varlıkları almanızı sağlar.  
> 
> 

Aşağıdaki C# kod, bir segment içinde döndürülen varlıkların sayısının nasıl değiştirileceğini göstermektedir:  

```csharp
employeeQuery.TakeCount = 50;  
```

### <a name="server-side-projection"></a>Sunucu tarafı projeksiyonu
Tek bir varlık en fazla 255 özelliğe sahip olabilir ve boyutu en fazla 1 MB olabilir. Tabloyu sorgulayıp varlıkları aldığınızda, tüm özelliklere ihtiyaç duymayabilir ve verilerin gereksiz yere aktarılmasını önleyebilirsiniz (gecikme süresini ve maliyeti azaltmaya yardımcı olmak için). Yalnızca ihtiyacınız olan özellikleri aktarmak için sunucu tarafı projeksiyonu kullanabilirsiniz. Aşağıdaki örnek, sorgu tarafından seçilen varlıklardan yalnızca **e-posta** özelliğini ( **partitionkey**, **Rowkey**, **timestamp**ve **ETag**ile birlikte) alır.  

```csharp
string filter = TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Sales");
List<string> columns = new List<string>() { "Email" };
TableQuery<EmployeeEntity> employeeQuery =
    new TableQuery<EmployeeEntity>().Where(filter).Select(columns);

var entities = employeeTable.ExecuteQuery(employeeQuery);
foreach (var e in entities)
{
    Console.WriteLine("RowKey: {0}, EmployeeEmail: {1}", e.RowKey, e.Email);
}  
```

Alınacak özellikler listesine dahil edilmese de **Rowkey** değerinin nasıl kullanılabilir olduğuna dikkat edin.  

## <a name="modifying-entities"></a>Varlıkları değiştirme
Depolama Istemci kitaplığı, varlıkları ekleyerek, silerek ve güncelleştirerek tablo hizmetinde depolanan varlıklarınızı değiştirmenize olanak sağlar. Gerekli gidiş dönüş sayısını azaltmak ve çözümünüzün performansını artırmak için, birden çok ekleme, güncelleştirme ve silme işlemini bir araya toplamak üzere EGTs 'yi kullanabilirsiniz.  

Depolama Istemci kitaplığı bir EGT çalıştırdığında oluşturulan özel durumlar genellikle toplu işin başarısız olmasına neden olan varlığın dizinini içerir. Bu, Yumurtları kullanan kod hata ayıklaması yaparken yararlıdır.  

Ayrıca, tasarımınızın, istemci uygulamanızın eşzamanlılık ve güncelleştirme işlemlerini nasıl işlediğini nasıl etkilediğini de göz önünde bulundurmanız gerekir.  

### <a name="managing-concurrency"></a>Eşzamanlılığı yönetme
Varsayılan olarak, tablo hizmeti, **ekleme**, **birleştirme**ve **silme** işlemleri için tek tek varlıkların düzeyinde iyimser eşzamanlılık denetimleri uygular, ancak bir istemcinin tablo hizmetini bunları atlayacak şekilde zorlaması mümkün olsa da denetleme. Tablo hizmetinin eşzamanlılık 'yi nasıl yönettiği hakkında daha fazla bilgi için bkz. [Microsoft Azure depolama eşzamanlılık yönetimi](../../storage/common/storage-concurrency.md).  

### <a name="merge-or-replace"></a>Birleştir veya Değiştir
**Tableoperation** sınıfının **Replace** yöntemi her zaman tablo hizmetindeki tüm varlığın yerini alır. Saklı varlıkta bu özellik varsa istek içine bir özellik eklemezseniz, istek bu özelliği saklı varlıktan kaldırır. Bir özelliği saklı bir varlıktan açıkça kaldırmak istemediğiniz müddetçe, istekteki her özelliği dahil etmeniz gerekir.  

Bir varlığı güncelleştirmek istediğinizde tablo hizmetine gönderilen veri miktarını azaltmak için **Tableoperation** sınıfının **merge** yöntemini kullanabilirsiniz. **Merge** yöntemi, saklı varlıktaki tüm özellikleri, istekte yer alan varlıktaki özellik değerleriyle değiştirir, ancak istekte bulunmayan saklı varlıktaki tüm özellikleri bozulmadan bırakır. Bu, büyük varlıklarınız varsa ve yalnızca bir istekteki az sayıda özelliği güncelleştirmeniz gerekiyorsa yararlıdır.  

> [!NOTE]
> Varlık yoksa, **Replace** ve **merge** yöntemleri başarısız olur. Alternatif olarak, mevcut değilse yeni bir varlık oluşturan **ınsertorreplace** ve **ınsertormerge** yöntemlerini kullanabilirsiniz.  
> 
> 

## <a name="working-with-heterogeneous-entity-types"></a>Heterojen varlık türleriyle çalışma
Tablo hizmeti, tek bir tablonun, tasarımınızda harika esneklik sağlayan birden çok türden varlıkları depolayabileceği anlamına gelen bir *şema daha az* tablo deposudur. Aşağıdaki örnek, hem çalışan hem de departman varlıklarını depolayan bir tabloyu göstermektedir:  

<table>
<tr>
<th>PartitionKey</th>
<th>RowKey</th>
<th>Zaman damgası</th>
<th></th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>firstName</th>
<th>Soyadı</th>
<th>Yaş</th>
<th>Email</th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>firstName</th>
<th>Soyadı</th>
<th>Yaş</th>
<th>Email</th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>DepartmentName</th>
<th>EmployeeCount</th>
</tr>
<tr>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>firstName</th>
<th>Soyadı</th>
<th>Yaş</th>
<th>Email</th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
</table>

Her varlık **Partitionkey**, **Rowkey**ve **timestamp** değerlerine sahip olmalıdır, ancak herhangi bir özellik kümesine sahip olabilir. Ayrıca, bu bilgileri bir yere depolamayı seçmediğiniz müddetçe bir varlığın türünü belirten bir şey yoktur. Varlık türünü belirlemek için iki seçenek vardır:  

* Varlık türünü **rowkey** (veya büyük olasılıkla **partitionkey**) öğesine ekleyin. Örneğin, **EMPLOYEE_000123** veya **DEPARTMENT_SALES** **rowkey** değerleri olarak.  
* Varlık türünü aşağıdaki tabloda gösterildiği gibi kaydetmek için ayrı bir özellik kullanın.  

<table>
<tr>
<th>PartitionKey</th>
<th>RowKey</th>
<th>Zaman damgası</th>
<th></th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>EntityType</th>
<th>firstName</th>
<th>Soyadı</th>
<th>Yaş</th>
<th>Email</th>
</tr>
<tr>
<td>Çalışan</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>EntityType</th>
<th>firstName</th>
<th>Soyadı</th>
<th>Yaş</th>
<th>Email</th>
</tr>
<tr>
<td>Çalışan</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>EntityType</th>
<th>DepartmentName</th>
<th>EmployeeCount</th>
</tr>
<tr>
<td>Bölüm</td>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>EntityType</th>
<th>firstName</th>
<th>Soyadı</th>
<th>Yaş</th>
<th>Email</th>
</tr>
<tr>
<td>Çalışan</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
</table>

Varlık türünü **Rowkey**olarak önceden bekleyen ilk seçenek, farklı türlerde iki varlığın aynı anahtar değerine sahip olabileceği bir olasılık olduğunda yararlıdır. Ayrıca, bölümünde aynı türdeki varlıkları birlikte gruplandırır.  

Bu bölümde ele alınan teknikler özellikle bu kılavuzda daha önce açıklanan tartışma [Devralma ilişkilerle](table-storage-design-modeling.md#inheritance-relationships) [ilgilidir.](table-storage-design-modeling.md)  

> [!NOTE]
> İstemci uygulamalarının POCO nesnelerini ve farklı sürümlerle çalışmasını sağlamak için varlık türü değerine bir sürüm numarası dahil etmeyi göz önünde bulundurmanız gerekir.  
> 
> 

Bu bölümün geri kalanında, aynı tablodaki birden çok varlık türüyle çalışmayı kolaylaştıran depolama Istemci kitaplığındaki bazı özellikler açıklanmaktadır.  

### <a name="retrieving-heterogeneous-entity-types"></a>Heterojen varlık türlerini alma
Depolama Istemci kitaplığını kullanıyorsanız, birden fazla varlık türüyle çalışmak için üç seçeneğiniz vardır.  

Belirli bir **rowkey** ve **partitionkey** değerleriyle depolanan varlığın türünü biliyorsanız, bu durumda, varlığı, employeeentity türünde varlıklar alan önceki iki örnekte gösterildiği gibi belirtebilirsiniz.: [Depolama istemci kitaplığını kullanarak bir nokta sorgusu yürütme](#executing-a-point-query-using-the-storage-client-library) ve [LINQ kullanarak birden çok varlık alma](#retrieving-multiple-entities-using-linq).  

İkinci seçenek somut bir POCO varlık türü yerine **Dynamictableentity** türü (bir özellik paketi) kullanmaktır (Bu seçenek, varlığı .net türlerine Serileştirmeye ve serisini kaldırma gereksinimi olmadığı için performansı da iyileştirebilir). Aşağıdaki C# kod, tablodaki farklı türlerin birden çok varlığını potansiyel olarak alır, ancak tüm varlıkları **dynamictableentity** örnekleri olarak döndürür. Ardından, her bir varlığın türünü anlamak için **EntityType** özelliğini kullanır:  

```csharp
string filter =
    TableQuery.CombineFilters(
        TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Sales"),
        TableOperators.And,
        TableQuery.CombineFilters(
            TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThanOrEqual, "B"),
            TableOperators.And,
            TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, "F")));
        
TableQuery<DynamicTableEntity> entityQuery =
    new TableQuery<DynamicTableEntity>().Where(filter);
    
var employees = employeeTable.ExecuteQuery(entityQuery);

IEnumerable<DynamicTableEntity> entities = employeeTable.ExecuteQuery(entityQuery);
foreach (var e in entities)
{
    EntityProperty entityTypeProperty;
    if (e.Properties.TryGetValue("EntityType", out entityTypeProperty))
    {
        if (entityTypeProperty.StringValue == "Employee")
        {
            // use entityTypeProperty, RowKey, PartitionKey, Etag, and Timestamp
        }
    }
}  
```

Diğer özellikleri almak için, **Dynamictableentity** sınıfının **Properties** özelliğinde **TryGetValue** yöntemini kullanmanız gerekir.  

Üçüncü bir seçenek de, **Dynamictableentity** türü ve bir **entityresolver** örneği kullanılarak birleştirilir. Bu, aynı sorgudaki birden çok POCO türüne çözüm yapmanızı sağlar. Bu örnekte, **Entityresolver** temsilcisi, sorgunun döndürdüğü iki varlık türü arasında ayrım yapmak için **EntityType** özelliğini kullanıyor. **Resolve** yöntemi, **Dynamictableentity** örneklerini **tableentity** örneklerine çözümlemek için **çözümleyici** temsilcisini kullanır.  

```csharp
EntityResolver<TableEntity> resolver = (pk, rk, ts, props, etag) =>
{
    TableEntity resolvedEntity = null;
    if (props["EntityType"].StringValue == "Department")
    {
        resolvedEntity = new DepartmentEntity();
    }
    else if (props["EntityType"].StringValue == "Employee")
    {
        resolvedEntity = new EmployeeEntity();
    }
    else 
    {
        throw new ArgumentException("Unrecognized entity", "props");
    }

    resolvedEntity.PartitionKey = pk;
    resolvedEntity.RowKey = rk;
    resolvedEntity.Timestamp = ts;
    resolvedEntity.ETag = etag;
    resolvedEntity.ReadEntity(props, null);
    return resolvedEntity;
};

string filter = TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Sales");
        
TableQuery<DynamicTableEntity> entityQuery = new TableQuery<DynamicTableEntity>().Where(filter);

var entities = employeeTable.ExecuteQuery(entityQuery, resolver);
foreach (var e in entities)
{
    if (e is DepartmentEntity)
    {
        // ...
    }
    else if (e is EmployeeEntity)
    {
        // ...
    }
}  
```

### <a name="modifying-heterogeneous-entity-types"></a>Heterojen varlık türlerini değiştirme
Silmek için bir varlığın türünü bilmeniz gerekmez ve onu eklediğinizde bir varlığın türünü her zaman bilirsiniz. Ancak, türünü bilmeksizin ve POCO varlık sınıfı kullanmadan bir varlığı güncelleştirmek için **Dynamictableentity** türünü kullanabilirsiniz. Aşağıdaki kod örneği tek bir varlık alır ve güncelleştirme yapmadan önce **Employeecount** özelliğinin var olduğunu denetler.  

```csharp
TableResult result = employeeTable.Execute(TableOperation.Retrieve(partitionKey, rowKey));
DynamicTableEntity department = (DynamicTableEntity)result.Result;

EntityProperty countProperty;
if (!department.Properties.TryGetValue("EmployeeCount", out countProperty))
{
    throw new InvalidOperationException("Invalid entity, EmployeeCount property not found.");
}

countProperty.Int32Value += 1;
employeeTable.Execute(TableOperation.Merge(department));
```

## <a name="controlling-access-with-shared-access-signatures"></a>Paylaşılan erişim Imzaları ile erişimi denetleme
Depolama hesabı anahtarınızı kodunuza dahil etmek zorunda kalmadan, istemci uygulamalarının tablo varlıklarını değiştirmesini (ve sorgulamasını) sağlamak için paylaşılan erişim Imzası (SAS) belirteçlerini kullanabilirsiniz. Genellikle, uygulamanızda SAS kullanmanın üç ana avantajı vardır:  

* Bu cihazın tablo hizmetindeki varlıklara erişmesine ve bunları değiştirmesine izin vermek için depolama hesabı Anahtarınızı güvenli olmayan bir platforma (bir mobil cihaz gibi) dağıtmanız gerekmez.  
* Web ve çalışan rollerinin, varlıklarınızı son kullanıcı bilgisayarları ve mobil cihazlar gibi istemci cihazlarına yönetirken gerçekleştirdiği bazı işleri devretmek için bu işlemleri çalıştırabilirsiniz.  
* Bir istemciye kısıtlı ve zaman sınırlı bir izin kümesi atayabilirsiniz (örneğin, belirli kaynaklara salt okuma erişimine izin verme).  

Tablo hizmetiyle SAS belirteçleri kullanma hakkında daha fazla bilgi için bkz. [paylaşılan erişim imzaları (SAS) kullanma](../../storage/common/storage-sas-overview.md).  

Ancak, yine de tablo hizmetindeki varlıklara bir istemci uygulaması veren SAS belirteçlerini oluşturmanız gerekir: bunu, depolama hesabı Anahtarlarınıza güvenli erişim sağlayan bir ortamda yapmanız gerekir. Genellikle, SAS belirteçlerini oluşturmak ve varlıklarınıza erişmesi gereken istemci uygulamalarına göndermek için bir Web veya çalışan rolü kullanırsınız. SAS belirteçlerini istemcilere oluşturma ve sunma konusunda hala bir ek yük olduğundan, özellikle de yüksek hacimli senaryolarda bu yükü azaltmanın en iyi şekilde göz önünde bulundurmanız gerekir.  

Bir tablodaki varlıkların bir alt kümesine erişim veren bir SAS belirteci oluşturmak mümkündür. Varsayılan olarak, tüm tablo için bir SAS belirteci oluşturursunuz, ancak SAS belirtecinin bir dizi **partitionkey** değeri ya da bir **Partitionkey** ve **rowkey** değeri aralığı için erişim izni vermesini belirtmek de mümkündür. Her kullanıcının SAS belirteci yalnızca tablo hizmetindeki kendi varlıklarına erişmelerine izin verecek şekilde, sisteminizin bireysel kullanıcıları için SAS belirteçleri oluşturmayı tercih edebilirsiniz.  

## <a name="asynchronous-and-parallel-operations"></a>Zaman uyumsuz ve paralel işlemler
İsteklerinizi birden çok bölüme yaymakta olduğunuz için, zaman uyumsuz veya paralel sorgular kullanarak aktarım hızını ve istemci yanıt hızını artırabilirsiniz.
Örneğin, tablolarınıza paralel olarak erişen iki veya daha fazla çalışan rolü örneği olabilir. Belirli bölüm kümelerinden sorumlu tek tek çalışan rolleriniz olabilir veya her biri bir tablodaki tüm bölümlere erişebilen birden fazla çalışan rolü örneğine sahip olabilirsiniz.  

İstemci örneği içinde, depolama işlemlerini zaman uyumsuz olarak yürüterek aktarım hızını artırabilirsiniz. Depolama Istemci kitaplığı, zaman uyumsuz sorguları ve değişiklikleri yazmayı kolaylaştırır. Örneğin, aşağıdaki C# kodda gösterildiği gibi bir bölümdeki tüm varlıkları alan zaman uyumlu yöntemle başlayabilirsiniz:  

```csharp
private static void ManyEntitiesQuery(CloudTable employeeTable, string department)
{
    string filter = TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, department);
    TableQuery<EmployeeEntity> employeeQuery = new TableQuery<EmployeeEntity>().Where(filter);

    TableContinuationToken continuationToken = null;
    do
    {
        var employees = employeeTable.ExecuteQuerySegmented(employeeQuery, continuationToken);
        foreach (var emp in employees)
        {
            // ...
        }
        
        continuationToken = employees.ContinuationToken;
    } while (continuationToken != null);
}  
```

Sorgunun zaman uyumsuz olarak çalışması için bu kodu kolayca değiştirebilirsiniz:  

```csharp
private static async Task ManyEntitiesQueryAsync(CloudTable employeeTable, string department)
{
    string filter = TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, department);
    TableQuery<EmployeeEntity> employeeQuery = new TableQuery<EmployeeEntity>().Where(filter);
    
    TableContinuationToken continuationToken = null;
    do
    {
        var employees = await employeeTable.ExecuteQuerySegmentedAsync(employeeQuery, continuationToken);
        foreach (var emp in employees)
        {
            // ...
        }
    
        continuationToken = employees.ContinuationToken;
    } while (continuationToken != null);
}  
```

Bu zaman uyumsuz örnekte, zaman uyumlu sürümden aşağıdaki değişiklikleri görebilirsiniz:  

* Yöntem imzası artık **zaman uyumsuz** değiştirici içeriyor ve bir **görev** örneği döndürüyor.  
* Sonuçları almak için **Executekesimli** yöntemi çağırmak yerine, yöntemi artık **ExecuteSegmentedAsync** yöntemini çağırır ve sonuçları zaman uyumsuz olarak almak için **await** değiştiricisini kullanır.  

İstemci uygulaması bu yöntemi birden çok kez çağırabilir ( **bölüm** parametresi için farklı değerlerle birlikte) ve her sorgu ayrı bir iş parçacığında çalışır.  

**IEnumerable** arabirimi zaman uyumsuz numaralandırmayı desteklemediğinden **Tablequery** sınıfında **Execute** yönteminin zaman uyumsuz sürümü yok.  

Ayrıca varlıkları zaman uyumsuz olarak ekleyebilir, güncelleştirebilir ve silebilirsiniz. Aşağıdaki C# örnek, bir çalışan varlığı eklemek veya değiştirmek için basit ve zaman uyumlu bir yöntemi gösterir:  

```csharp
private static void SimpleEmployeeUpsert(
    CloudTable employeeTable,
    EmployeeEntity employee)
{
    TableResult result = employeeTable.Execute(TableOperation.InsertOrReplace(employee));
    Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
}  
```

Güncelleştirme zaman uyumsuz olarak aşağıdaki gibi çalışacak şekilde bu kodu kolayca değiştirebilirsiniz:  

```csharp
private static async Task SimpleEmployeeUpsertAsync(
    CloudTable employeeTable,
    EmployeeEntity employee)
{
    TableResult result = await employeeTable.ExecuteAsync(TableOperation.InsertOrReplace(employee));
    Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
}  
```

Bu zaman uyumsuz örnekte, zaman uyumlu sürümden aşağıdaki değişiklikleri görebilirsiniz:  

* Yöntem imzası artık **zaman uyumsuz** değiştirici içeriyor ve bir **görev** örneği döndürüyor.  
* Yöntemi, varlığı güncelleştirmek için **Execute** metodunu çağırmak yerine, şimdi **ExecuteAsync** yöntemini çağırır ve sonuçları zaman uyumsuz olarak almak için **await** değiştiricisini kullanır.  

İstemci uygulaması, bu gibi birden çok zaman uyumsuz yöntem çağırabilir ve her yöntem çağrısı ayrı bir iş parçacığında çalışır.  

## <a name="next-steps"></a>Sonraki adımlar

- [Modelleme ilişkileri](table-storage-design-modeling.md)
- [Sorgulama tasarımı](table-storage-design-for-query.md)
- [Tablo verilerini şifreleme](table-storage-design-encrypt-data.md)
- [Veri değişikliği için tasarım](table-storage-design-for-modification.md)
