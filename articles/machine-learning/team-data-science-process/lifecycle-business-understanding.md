---
title: Ekip veri bilimi sürecinde iş anlama
description: Ekip veri bilimi Işlemindeki veri bilimi projelerinizin iş anlama aşamasına yönelik hedefler, görevler ve teslim edilebilirler.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: a7aaed519f8f97a9be77a263568aeed5257c16d6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "76710340"
---
# <a name="the-business-understanding-stage-of-the-team-data-science-process-lifecycle"></a>Team Data Science Işlem yaşam döngüsünün iş anlama aşaması

Bu makalede, Team Data Science Işleminin (TDSP) iş anlama aşamasına ilişkin hedefler, görevler ve teslim edilebilirler özetlenmektedir. Bu işlem, veri bilimi projelerinizi yapılandırmak için kullanabileceğiniz önerilen bir yaşam döngüsü sağlar. Yaşam döngüsü, projelerin tipik olarak yürütülen ana aşamaları özetler, genellikle yinelemeli olarak:

   1. **Kurumsal yaklaşım**
   2. **Veri edinme ve anlama**
   3. **Modelleme**
   4. **Dağıtım**
   5. **Müşteri kabulü**

TDSP yaşam döngüsünün görsel bir gösterimi aşağıda verilmiştir: 

![TDSP yaşam döngüsü](./media/lifecycle/tdsp-lifecycle2.png) 


## <a name="goals"></a>Hedefler
* Model hedefi olarak kullanılacak ve ilgili ölçümler projenin başarısını belirleyen anahtar değişkenlerini belirtin.
* İşletmenin erişimi olan veya alması gereken ilgili veri kaynaklarını belirler.

## <a name="how-to-do-it"></a>Nasıl yapılır?
Bu aşamada belirtilen iki ana görev vardır: 

   * **Hedefleri tanımlama**: iş sorunlarını anlamak ve tanımlamak için müşterinizden ve diğer hissedarlarla çalışın. Veri bilimi tekniklerinin hedeflenebilme iş hedeflerini tanımlayan soruları formüle koyun.
   * **Veri kaynaklarını tanımlama**: projenin amaçlarını tanımlayan soruları yanıtlamanıza yardımcı olacak ilgili verileri bulun.

### <a name="define-objectives"></a>Amaçları tanımla
1. Bu adımın merkezi amacı, çözümlemenin tahmin edilmesi gereken temel iş değişkenlerini belirlemektir. *Model hedefleri* olarak bu değişkenlere başvurduk ve projenin başarısını tespit etmek için bunlarla ilişkili ölçümleri kullanırız. Bu iki hedefe örnek olarak satış tahminleri veya bir siparişin sahte olma olasılığı verilebilir.

2. İlgili, özel ve belirsiz "keskin" sorular isteyerek ve inceleyerek proje hedeflerini tanımlayın. Veri bilimi, bu tür sorulara yanıt vermek için adları ve sayıları kullanan bir işlemdir. Genellikle, veri bilimi veya makine öğrenimini kullanarak beş tür soruyu cevaplayabilirsiniz:
 
   * Kaç veya daha fazla? regresyon
   * Hangi kategori? sınıflandırmaya
   * Hangi Grup? lenmesi
   * Bu tuhaf mi? (anomali algılama)
   * Hangi seçenek gerçekleştirilmelidir? Önerilen

   Hangi sorulardan hangilerinin sorduğunuzu ve bu soruların iş hedeflerinize nasıl yanıt verme şeklini belirleyin.

3. Üyelerinin rollerini ve sorumluluklarını belirterek proje ekibini tanımlayın. Daha fazla bilgi bulduğunuzda, yineletiğiniz üst düzey bir kilometre taşı planı geliştirin. 

4. Başarı ölçümlerini tanımlayın. Örneğin, bir müşteri dalgalanma tahmini elde etmek isteyebilirsiniz. Bu üç aylık projenin sonunda% x "doğruluk oranına sahip olmanız gerekir. Bu verilerle, karmaşıklığı azaltmak için müşteri yükseltmeleri sunabilirsiniz. Ölçümler **akıllı** olmalıdır: 

   * **S** elirli 
   * **A** eaperable
   * Tek **bir** 
   * **R** elevant 
   * **T** IME-bağlantılı 

### <a name="identify-data-sources"></a>Veri kaynaklarını belirleme
Keskin sorularınızın yanıtlarının bilinen örneklerini içeren veri kaynaklarını belirler. Aşağıdaki verileri arayın:

* Soru ile ilgili veriler. Hedefle ilgili hedef ve özelliklerin ölçüleriniz var mı?
* Model hedefi ve ilgilendiğiniz özellikler için doğru ölçüm olan veriler.

Örneğin, var olan sistemlerin sorunu gidermek ve proje hedeflerine ulaşmak için ek veri türlerini toplayıp günlüğe kaydetmek gerektiğini fark edebilirsiniz. Bu durumda, dış veri kaynaklarını aramak veya sistemlerinizi yeni veri toplamak üzere güncelleştirmek isteyebilirsiniz.

## <a name="artifacts"></a>Artifacts
Bu aşamada teslim edilebilirler aşağıda verilmiştir:

   * [Charter belgesi](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Project/Charter.md): TDSP proje yapısı tanımında standart bir şablon sağlanır. Kurucu belgesi, yaşayan bir belgedir. Projenin tamamında, yeni bulmalar yaptığınızda ve iş gereksinimleri değiştikçe şablonu güncelleştirebilirsiniz. Bu anahtar, bulma sürecinde ilerlemeniz sırasında daha fazla ayrıntı ekleyerek bu belgeyi yinelemek için kullanılır. Müşterilerin ve diğer paydaşların değişiklikleri yapmasına ve bunlara yönelik değişiklikleri açık bir şekilde iletmesinin nedenlerini koruyun.  
   * [Veri kaynakları](https://github.com/Azure/Azure-TDSP-ProjectTemplate/blob/master/Docs/Data_Report/Data%20Defintion.md#raw-data-sources): TDSP proje **verileri rapor** klasöründe bulunan **veri tanımları** raporunun **ham veri kaynakları** bölümü veri kaynaklarını içerir. Bu bölüm ham verilerin orijinal ve hedef konumlarını belirtir. Sonraki aşamalarda, verileri analitik ortamınıza taşımak için betikler gibi ek ayrıntılar doldurursunuz.  
   * [Veri sözlükleri](https://github.com/Azure/Azure-TDSP-ProjectTemplate/tree/master/Docs/Data_Dictionaries): Bu belge, istemcisi tarafından sağlanan verilerin açıklamalarını sağlar. Bu açıklamalarda şema (varsa, doğrulama kuralları hakkında veri türleri ve bilgiler) ve varlık ilişkisi diyagramları hakkında bilgiler yer alır.

## <a name="next-steps"></a>Sonraki adımlar

TDSP yaşam döngüsüyle her adımın bağlantıları aşağıda verilmiştir:

   1. [Kurumsal yaklaşım](lifecycle-business-understanding.md)
   2. [Veri edinme ve anlama](lifecycle-data.md)
   3. [Modelleme](lifecycle-modeling.md)
   4. [Dağıtım](lifecycle-deployment.md)
   5. [Müşteri kabulü](lifecycle-acceptance.md)

Belirli senaryolar için işlemdeki tüm adımları gösteren tam izlenecek yollar sunuyoruz. [Örnek yönergeler](walkthroughs.md) makalesi, bağlantılar ve küçük resim açıklamaları olan senaryoların bir listesini sağlar. İzlenecek yollar, akıllı bir uygulama oluşturmak için bulut, şirket içi araçların ve hizmetlerin bir iş akışı veya işlem hattına nasıl birleştirileceğini gösterir. 
