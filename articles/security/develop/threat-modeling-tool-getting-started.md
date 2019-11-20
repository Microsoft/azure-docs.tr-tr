---
title: Başlarken-Microsoft Threat Modeling Tool-Azure | Microsoft Docs
description: Bu, Threat Modeling Tool eyleminde vurgulamanın daha derin bir genel bakıştır.
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: jegeib
ms.openlocfilehash: 1454826095bcced9b20935405c0befd5a1ed1ddd
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 08/01/2019
ms.locfileid: "68728298"
---
# <a name="getting-started-with-the-threat-modeling-tool"></a>Threat Modeling Tool kullanmaya başlama

Microsoft Threat Modeling Tool 2018, ücretsiz **[tıklama-indirme](https://aka.ms/threatmodelingtool)** olarak 2018 Eylül 'de GA olarak yayımlanmıştır. Teslim mekanizmasında değişiklik, aracı her açtıklarında en son geliştirmeleri ve hata düzeltmelerini sunmamıza olanak tanır. bu sayede bakım ve kullanım kolaylığı kolaylaşır.
Bu makale, Microsoft SDL tehdit modelleme yaklaşımını kullanmaya başlama sürecinde size kılavuzluk sağlar ve aracın, güvenlik sürecinizin bir omurgası olarak harika tehdit modellerini geliştirmek için nasıl kullanılacağını gösterir.

Bu makalede, SDL tehdit modelleme yaklaşımının var olan bilgileri açıklanır. Hızlı bir inceleme için, 2006 ' de yayımlanan yaklaşım MSDN makalesini kullanarak **[tehdit modellemesi Web uygulamaları](https://msdn.microsoft.com/library/ms978516.aspx)** ' na ve **[güvenlik kusurlarını](https://docs.google.com/viewer?a=v&pid=sites&srcid=ZGVmYXVsdGRvbWFpbnxzZWN1cmVwcm9ncmFtbWluZ3xneDo0MTY1MmM0ZDI0ZjQ4ZDMy)** açığa çıkarmak için arşivlenmiş bir sürüme bakın.

Bu yaklaşım hızlı bir şekilde özetlemek için bir diyagram oluşturmayı, tehditleri tanımlamayı ve her bir azalmayı doğrulamayı içerir. Bu işlemi vurgulayan bir diyagram aşağıda verilmiştir:

![SDL Işlemi](./media/threat-modeling-tool-getting-started/sdlapproach.png)

## <a name="starting-the-threat-modeling-process"></a>Tehdit modelleme işlemi başlatılıyor

Threat Modeling Tool başlattığınızda resimde görüldüğü gibi birkaç şey fark edersiniz:

![Boş başlangıç sayfası](./media/threat-modeling-tool-feature-overview/tmtstart.png)

### <a name="threat-model-section"></a>Tehdit modeli bölümü

| Bileşen                                   | Ayrıntılar                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| ------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Geri bildirim, öneriler ve sorunlar düğmesi** | Tüm nesnelerin her türlü için **[MSDN forumundan](https://social.msdn.microsoft.com/Forums/en-US/home?forum=sdlprocess)** yararlanır. Diğer kullanıcıların ne yaptığını, geçici çözümler ve önerilerle birlikte okumaya yönelik bir fırsat sunar. Aradığınız şeyi yine de bulamıyorsanız, destek ekibimizin size yardımcı olması tmtextsupport@microsoft.com için e-posta gönderin                                                                                                                            |
| **Model oluşturma**                          | Diyagramınızı çizmeniz için boş bir tuval açar. Modeliniz için kullanmak istediğiniz şablonu seçtiğinizden emin olun                                                                                                                                                                                                                                                                                                                                                                       |
| **Yeni modeller için şablon**                 | Model oluşturmadan önce kullanılacak şablonu seçmelisiniz. Ana şablonumuz, Azure 'a özgü Şablonlar, tehditler ve azaltmaları içeren Azure tehdit modeli şablonudur. Genel modeller için, açılan menüden SDL TM Bilgi Bankası ' nı seçin. Kendi şablonunuzu oluşturmak veya tüm kullanıcılar için yeni bir tane göndermek mı istiyorsunuz? Daha fazla bilgi edinmek için **[şablon deposu](https://github.com/Microsoft/threat-modeling-templates)** GitHub sayfamıza göz atın                              |
| **Model açma**                            | <p>Daha önce kaydedilen tehdit modellerini açar. Son açılan modeller özelliği, en son dosyalarınızı açmanız gerekiyorsa harika. Seçimin üzerine geldiğinizde, modelleri açmak için 2 yol bulacaksınız:</p><p><ul><li>Yerel depolama kullanarak bir dosya açan klasik yoldan bu bilgisayardan aç</li><li>OneDrive 'dan aç – takımlar, OneDrive 'daki klasörleri kullanarak üretkenlik ve işbirliğinin artırılmasına yardımcı olmak için tüm tehdit modellerini tek bir konumda kaydedebilir ve paylaşabilir</li></ul></p> |
| **Başlarken Kılavuzu**                   | Ana sayfayı **[Microsoft Threat Modeling Tool](threat-modeling-tool.md)** açar                                                                                                                                                                                                                                                                                                                                                                                            |

### <a name="template-section"></a>Şablon bölümü

| Bileşen               | Ayrıntılar                                                                                                                                                          |
| ----------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Yeni Şablon Oluştur** | Üzerinde derlemenize yönelik boş bir şablon açar. Sıfırdan şablon oluşturma konusunda kapsamlı bilgi sahibi değilseniz, var olan olanlardan derleme yapmanızı öneririz |
| **Şablon aç**       | Üzerinde değişiklik yapmanız için mevcut şablonları açar                                                                                                              |

Threat Modeling Tool ekibi, araç işlevlerini ve deneyimini geliştirmek için sürekli çalışmaktadır. Yılın uzından birkaç küçük değişiklik gerçekleşebilir, ancak tüm büyük değişiklikler kılavuzda yeniden yazar gerektirir. En son duyuruları aldığınızdan emin olmak için sık sık başvurun.

## <a name="building-a-model"></a>Model oluşturma

Bu bölümde aşağıdaki adımları izliyoruz:

- Cristina (bir geliştirici)
- Ricardo (bir program Yöneticisi) ve
- Ashsi (sınayıcı)

İlk tehdit modellerini geliştirme sürecinde ilerleyeceğiz.

> Ricardo Merhaba Cristina, tehdit modeli diyagramı üzerinde çalıştım ve Ayrıntılar hakkı geldiğinden emin olmak istiyor. Üzerinde görünmeme yardımcı olabilir mi?
> Cristina: Kesinlikle. Şimdi bir göz atalım.
> Ricardo, aracı açar ve ekranı Cristina ile paylaşır.

![Temel tehdit modeli](./media/threat-modeling-tool-feature-overview/basictmt.png)

> Cristina: Tamam, basit görünüyor, ancak bana yol açabilir misiniz?
> Ricardo Olmadığından! Döküm şöyledir:
> - İnsan kullanıcımız bir dış varlık olarak çizilir — bir kare
> - Web sunucunuza komut gönderirler — daire
> - Web sunucusu bir veritabanına danışmanlık (iki paralel çizgi)

Yalnızca Cristina gösterilen Ricardo, **[veri akışı diyagramı](https://en.wikipedia.org/wiki/Data_flow_diagram)** için kısa olan bir dfd. Threat Modeling Tool, kullanıcıların, farklı varlıkların denetimde olduğunu göstermek için kırmızı noktalı çizgilerle belirtilen güven sınırlarını belirtmesini sağlar. Örneğin, BT yöneticileri kimlik doğrulama amacıyla bir Active Directory sistemi gerektirir, bu nedenle Active Directory denetimleri dışında olur.

> Cristina: Hemen bana bakar. Tehditler hakkında ne var?
> Ricardo Bunu göstermeme izin ver.

## <a name="analyzing-threats"></a>Tehditler çözümleniyor

Simge menü seçiminden (büyüteç ile dosya) analiz görünümü ' ne tıkladıktan sonra, bir oluşturulan tehditler listesine alınır ve bu, bir dizi Threat Modeling Tool, bu, **[ilerleme, değişiklik, Bilgi açıklaması, Red, hizmet reddi ve ayrıcalık yükselmesi)](https://en.wikipedia.org/wiki/STRIDE_(security))** . Bu, yazılımın bu 6 kategori kullanılarak bulunan öngörülebilir bir tehdit kümesi altına geldiği fikrdir.

Bu yaklaşım, her bir kapı ve pencerenin, bir alarm sistemi eklemeden veya Thief 'ten sonra bir kilitlenme mekanizmasına sahip olmasını sağlamak için evinizin güvenliğini sağlamaya benzer.

![Temel tehditler](./media/threat-modeling-tool-getting-started/basicthreats.png)

Ricardo, listedeki ilk öğeyi seçerek başlar. Şöyle olur:

İlk olarak, iki kalıp arasındaki etkileşim geliştirilmiştir

![Etkileşimi](./media/threat-modeling-tool-getting-started/interaction.png)

İkinci olarak tehdit hakkında ek bilgiler tehdit Özellikler penceresi görünür.

![Etkileşim bilgileri](./media/threat-modeling-tool-getting-started/interactioninfo.png)

Oluşturulan tehdit potansiyel tasarım kusurları anlamanıza yardımcı olur. Ilerleme kategorisi, olası saldırı vektörlerine fikir verir. ek açıklama, tam olarak neyin yanlış olduğunu ve bunu hafifletmenin olası yollarını söyler. Düzenleme ayrıntılarına Not yazmak veya kuruluşun hata çubuğuna bağlı olarak öncelik derecelendirmelerini değiştirmek için düzenlenebilir alanları kullanabilir.

Azure şablonlarının, kullanıcıların yalnızca neyin yanlış olduğunu anlamalarına yardımcı olmak için ek ayrıntıları, ancak Azure 'a özgü belgelere açıklamalar, örnekler ve köprüler ekleyerek bunu nasıl giderebileceğinizi öğrenin.

Açıklama, kullanıcıların sızmasını engellemek için bir kimlik doğrulama mekanizması eklemenin önemini fark etmekte ve üzerinde çalışılacak ilk tehdidi açığa sunmaktadır. Cristina ile tartışmada birkaç dakika, erişim denetimi ve rollerinin uygulanması önemini anlamıştır. Ricardo, bunların uygulandığından emin olmak için bazı hızlı notlara doldurulmuştur.

Bilgi açığa çıkması kapsamında tehditlere karşı bir sorun oluştuğundan, erişim denetimi planının denetim ve rapor oluşturma için bazı salt okunurdur hesaplar gerektirmişti. Bunun yeni bir tehdit olup olmayacağını merak etmişti, ancak hafifletmeleri aynı olduğunu ve bu nedenle tehdidi buna uygun olarak Not etmişti.
Ayrıca, bilgilerin açığa çıkmasını biraz daha da düşündük ve yedekleme bantlarının, işlemler ekibi için bir iş olan şifrelemeye ihtiyaç duymasıyla ilgili olduğunu fark eteceğiz.

Mevcut azaltma işlemleri veya güvenlik garantisi nedeniyle, durum açılan listesinden "uygulanamaz" olarak değiştirilebiliyorsa tehditler tasarıma uygulanmaz. Üç farklı seçenek vardır: Başlatılmamış – varsayılan seçim, üzerinde tam olarak çalışmaya başladıktan sonra, öğeleri izlemek için kullanılır.

## <a name="reports--sharing"></a>& Paylaşım raporları

Ricardo, Cristina ile listedeki ilerledikten sonra önemli notlar, azaltmalar/gerekçeler, öncelik ve durum değişiklikleri ekler, raporları seçer > tam rapor oluştur ve iş arkadaşlarınızla devam etmek için hoş bir rapor yazdıran > raporu Kaydet uygun güvenlik işinin uygulandığından emin olmak için.

![Etkileşim bilgileri](./media/threat-modeling-tool-feature-overview/report.png)

Bunun yerine Ricardo dosyayı paylaştırmak istiyorsa, kuruluşun OneDrive hesabına kaydederek kolayca bunu yapabilirsiniz. Bunu yaptıktan sonra belge bağlantısını kopyalayabilir ve iş arkadaşlarınızla paylaşabilir. 

## <a name="threat-modeling-meetings"></a>Tehdit modelleme toplantıları

Ricardo, OneDrive kullanarak iş arkadaşınıza tehdit modelini gönderdikten sonra, sınayıcı, underwhelmed idi. , Ricardo ve Cristina gibi, çok az sayıda önemli köşe durumu kaçırılmış ve bu da kolayca tehlikeye girebilir. Kendi iskeleştirme, tehdit modellerinin bir tamamlayıcısı.

Bu senaryoda, bir tehdit modelini aşdıktan sonra iki tehdit modelleme toplantısı için çağırılır: süreç üzerinde eşitlenmek üzere bir toplantı ve daha sonra tehdit incelemesi ve oturum kapatma için ikinci bir toplantı.

İlk toplantıda, SDL tehdit modelleme işlemi aracılığıyla herkesin 10 dakika harcadığını aşmıştır. Daha sonra tehdit modeli diyagramını çekti ve ayrıntılı olarak açıklanmaya başladı. Beş dakika içinde, önemli bir eksik bileşen tanımlandı.

Birkaç dakika sonra, aşım ve Ricardo, Web sunucusunun nasıl oluşturulduğunu gösteren genişletilmiş bir tartışmaya alındı. Bir toplantının devam etmesi için ideal bir yöntem değildi, ancak sonunda daha erken bir tutarsızlık bulmaktan önce kabul edilen herkes bu süreyi daha sonra kaydetmekle devam ediyor.

İkinci toplantıda, ekip tehditler aracılığıyla ele alınmıştır, bu, bunları ele almanın ve tehdit modelinde oturumu kapatan bazı yollarla ele alınmıştır. Belgeyi kaynak denetimine denetledi ve geliştirmeyle devam ettirildi.

## <a name="thinking-about-assets"></a>Varlıklar hakkında düşünce

Tehdit modellenen bazı okuyucular, varlıklar hakkında hiçbir şey yapmadığımızda fark edebilir. Birçok yazılım mühendisinin yazılımlarını, varlıkların kavramını ve bir saldırganın ilgilenebilecek varlıkları anladıkları öğrendiklerinden daha iyi anladık.

Bir evye tehdit modelleyecekseniz, Aile, izin verirli fotoğraflar veya değerli resminiz hakkında düşünerek başlayabilirsiniz. Belki de kimin ve geçerli güvenlik sisteminin ne kadar kesintiye başlayabileceğini düşünerek başlayabilirler. Ya da havuz veya ön Porch gibi fiziksel özellikleri göz önünde bulundurarak başlayabilirsiniz. Bunlar, varlıklar, saldırganlar veya yazılım tasarımı hakkında düşünmeye benzer. Bu üç yaklaşımdan herhangi biri çalışır.

Burada sunduğumuz tehdit modelleme yaklaşımı, Microsoft 'un geçmişte yaptığından çok daha kolay. Yazılım tasarımı yaklaşımını birçok ekip için iyi bir şekilde çalıştığımız tespit ettik. Sizinkinizi dahil etmemiz dileğiyle.

## <a name="next-steps"></a>Sonraki Adımlar

Sorularınızı, yorumlarınızı ve kaygılarınızı tmtextsupport@microsoft.comile gönderin. Başlamak için Threat Modeling Tool **[indirin](https://aka.ms/threatmodelingtool)** .
