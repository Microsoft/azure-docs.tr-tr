---
title: Örnek veri kümelerini kullanma
titleSuffix: ML Studio (classic) - Azure
description: Machine Learning Studio (klasik) ' de bulunan örnek modellerde kullanılan veri kümelerinin açıklamaları. Bu örnek veri kümelerini, denemeleri için kullanabilirsiniz.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 01/19/2018
ms.openlocfilehash: 84ac2b43c9ec2e46bcd2967cf411f1bb1f60f42a
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73839402"
---
# <a name="use-the-sample-datasets-in-azure-machine-learning-studio-classic"></a>Azure Machine Learning Studio (klasik) içinde örnek veri kümelerini kullanma
[top]: #machine-learning-sample-datasets

Azure Machine Learning Studio (klasik) içinde yeni bir çalışma alanı oluşturduğunuzda, varsayılan olarak bir dizi örnek veri kümesi ve denemeleri eklenir. Bu örnek veri kümelerinin birçoğu [Azure yapay zeka Galerisi](https://gallery.azure.ai/)örnek modeller tarafından kullanılır. Diğer bir deyişle, makine öğreniminde genellikle kullanılan çeşitli veri türlerine örnek olarak dahildir.

Bu veri kümelerinden bazıları Azure Blob depolama alanında kullanılabilir. Bu veri kümeleri için, aşağıdaki tablo doğrudan bir bağlantı sağlar. Bu veri kümelerini, [verileri Içeri aktarma][import-data] modülünü kullanarak denemeleri uygulamanızda kullanabilirsiniz.

Bu örnek veri kümelerinin geri kalanı, **kayıtlı veri**kümeleri altında çalışma alanınızda bulunur. Bunu, Machine Learning Studio (klasik) ' de deneme tuvalinin solunda bulunan modül paletinde bulabilirsiniz.
Deneme Tuvalinize sürükleyerek bu veri kümelerinden herhangi birini kendi denemenize göre kullanabilirsiniz.

## <a name="datasets"></a>Veri kümeleri

<table>

<tr>
  <th>Veri kümesi adı</th>
  <th>Veri kümesi açıklaması</th>
</tr>

<tr>
  <td>Yetişkin Census geliri Ikili sınıflandırma veri kümesi</td>
  <td>
> 100 ' nin ayarlanmış bir gelir dizini ile 16 yaşın üzerinde çalışan yetişkinler kullanılarak 1994 Census veritabanının bir alt kümesi.
<p></p>
<b>Kullanım:</b> Bir kişinin 50 bin üzerinde bir yıl boyunca mi olduğunu tahmin etmek için demografik kullanan kişileri sınıflandırın.
<p></p>
<b>Ilgili araştırma:</b> Kohavi, R., Becker, B., (1996). UCı Machine Learning depo <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>. Irvine, CA: California Üniversitesi, bilgi Okulu ve bilgisayar bilimi </td>
</tr>

<tr>
  <td>Havaalanı kodları veri kümesi</td>
  <td>
ABD Havaalanı kodları.
<p></p>
Bu veri kümesi her bir ABD Havaalanı için bir satır içerir. Bu, Havaalanı KIMLIK numarası ve adı şehir ve eyalet ile birlikte sağlar.
  </td>
</tr>

<tr>
  <td>Otomobil fiyat verileri (ham)</td>
  <td>
, Bir sigorta riski puanı ve bu fiyat dahil olmak üzere, marka ve model bazında otomobil hakkında bilgiler.
<p></p>
Risk puanı başlangıçta otomatik fiyatla ilişkilendirilir. Daha sonra, söz konusu bir işlemdeki bir işlem için, symboling olarak bilinen bir işlemde gerçek risk için ayarlanır. \+ 3 değeri, otomatik olarak riskli olduğunu ve büyük olasılıkla güvenli olduğunu belirten-3 değerini gösterir.
<p></p>
<b>Kullanım:</b> Gerileme veya çok sayıda sınıflandırma kullanarak, özellik ile risk Puanını tahmin edin. 
<p></p>
<b>Ilgili araştırma:</b> Schliyoya, J.C. (1987). UCı Machine Learning depo <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>. Irvine, CA: California Üniversitesi, bilgi Okulu ve bilgisayar bilimi </td>
</tr>

<tr>
  <td>Bisiklet Kiralama UCı veri kümesi</td>
  <td>
Washington DC 'de Bisiklet Kiralama ağını tutan büyük Bıkespaylaşma şirketinin gerçek verilerine dayanan UCı Bisiklet Kiralama veri kümesi.
<p></p>
Veri kümesinde, toplam 17.379 satır için her günün her bir saati için bir satır (2011 ve 2012) bulunur. Saatlik Bisiklet salonları aralığı 1 ile 977 arasındadır.

  </td>
</tr>

<tr>
  <td>Bill Gates RGB resmi</td>
  <td>
Genel olarak kullanılabilir görüntü dosyası CSV verilerine dönüştürüldü.
<p></p>
Görüntüyü dönüştürme kodu, <strong>K-bit kümeleme modeli ayrıntısı kullanılarak renk satışlarının bir yolu</strong> olarak sağlanır.
  </td>
</tr>

<tr>
  <td>Kan bağış verileri</td>
  <td>
Hsin-Chu City, Tayvan 'ın kan veri merkezi hizmet merkezinin kan bağış yapan veritabanından alınan verilerin bir alt kümesi.
<p></p>
Bağış yapan veriler, son bağandan itibaren ayları, sıklığı veya toplam bağış sayısını, son bağandan itibaren geçen süreyi ve kanlı miktarı içerir.
<p></p>
<b>Kullanım:</b> Amaç, bir sınıflandırmanın 2007 Mart 'ta mi yoksa, 1 ' de hedef dönemde bir bağış yapana ve 0 ' ın bir bağış yapana sahip olup olmadığını sınıflandırma aracılığıyla tahmin etmek için kullanılır. 
<p></p>
<b>Ilgili araştırma:</b> Yeh, I.C., (2008). UCı Machine Learning depo <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>. Irvine, CA: California Üniversitesi, bilgi Okulu ve bilgisayar bilimi
<p></p>
Yeh, I-Cheng, Yang, King-ocg ve dişli, Tao-tatçi, "System ile uzman sistemleri, 2008 <a href="https://dx.doi.org/10.1016/j.eswa.2008.07.018">https://dx.doi.org/10.1016/j.eswa.2008.07.018</a>
  </td>
</tr>

<tr>
  <td>Breakst kanseri verileri</td>
  <td>
Makine öğrenimi belgelerinde sık görülen, Onkolombiya gy ile ilgili üç veri kümelerinden biri. Tanılama bilgilerini yaklaşık 300 dokulu örnek için laboratuvar analizinden özelliklerle birleştirir.
<p></p>
<b>Kullanım:</b> , Bazıları doğrusal ve bazıları kategorik olan 9 özniteliğe göre Cancer türünü sınıflandırın. 
<p></p>
<b>Ilgili araştırma:</b> Wohlberg, W.H., Street, W.N., & Mangasarian, O.L. (1995). UCı Machine Learning depo <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>. Irvine, CA: California Üniversitesi, bilgi Okulu ve bilgisayar bilimi </td>
</tr>

<tr>
  <td>Breakst Cancer özellikleri <td>
Veri kümesi, her biri 117 özellik tarafından tanımlanan, 10. Özellikler özeldir ve anlamları veri kümesi oluşturucuları (Siemens sağlık) tarafından ortaya çıkarmaz. 
  </td>
</tr>

<tr>
  <td>Breakst Cancer bilgileri</td>
  <td>
Veri kümesi, X-Ray görüntüsünün her şüpheli bölgesi için ek bilgiler içerir. Her örnek, Breakst Cancer özellikleri veri kümesindeki karşılık gelen satır numarası hakkında bilgi (örneğin, etiket, hasta ID, tüm görüntüye göre düzeltme eki koordinatları) sağlar. Her hasta birkaç örnek içerir. Cancer olan hastalar için bazı örnekler pozitif ve bazıları negatiftir. Cancer olmayan hastalar için tüm örnekler negatiftir. Veri kümesinde 102K örnekleri bulunur. Veri kümesi taraflı, noktaların% 0,6 ' i pozitif, geri kalanı negatif. Veri kümesi Siemens sağlık hizmeti tarafından kullanılabilir hale getirilir.
  </td>
</tr>

<tr>
  <td>CRM uygulama paylaşılan etiketleri</td>
  <td>
KDD Fin2009 müşteri ilişkisi tahmin sınamasına ait Etiketler (<a href="http://www.sigkdd.org/site/2009/files/orange_small_train_appetency.labels">orange_small_train_appetency. Etiketler</a>).
  </td>
</tr>

<tr>
  <td>Paylaşılan CRM dalgalanması etiketleri</td>
  <td>
KDD Fin2009 müşteri ilişkisi tahmin sınamasına ait Etiketler (<a href="http://www.sigkdd.org/site/2009/files/orange_small_train_churn.labels">orange_small_train_churn. Etiketler</a>).
  </td>
</tr>

<tr>
  <td>CRM veri kümesi paylaşıldı</td>
  <td>
Bu veriler, KDD Kupa 2009 müşteri ilişkisi tahmin Challenge (<a href="http://www.sigkdd.org/site/2009/files/orange_small_train.data.zip">orange_small_train. Data. zip</a>) ' den gelir.
<p></p>
Veri kümesi, Fransız telekomünikasyon şirketi turuncu 'dan 50K müşterileri içerir. Her müşterinin 230 anonim özelliği vardır, bunlar sayısal ve 40 kategorik olan 190 ' dir. Özellikler çok seyrek.
  </td>
</tr>

<tr>
  <td>Paylaşılan CRM satış etiketleri</td>
  <td>
KDD Fin2009 müşteri ilişkisi tahmin sınamasına ait Etiketler (<a href="http://www.sigkdd.org/site/2009/files/orange_large_train_upselling.labels">orange_large_train_upselling. Etiketler</a>).
  </td>
</tr>

<tr>
  <td>Enerji verimliliği regresyon verileri</td>
  <td>
12 farklı bina şekline dayanan bir sanal enerji profilleri koleksiyonu. Binalar sekiz özelliğe göre farklılaştırılabilir. Bu, GLA, alan dağıtımını ve yönlendirmeyi içerir.
<p></p>
<b>Kullanım:</b> İki gerçek değerli yanıtlardan biri olarak enerji verimliliği derecelendirmesini tahmin etmek için regresyon veya sınıflandırma kullanın. Çok sınıf sınıflandırması için, yanıt değişkenini en yakın tamsayıya yuvarlar. 
<p></p>
<b>Ilgili araştırma:</b> Xifara, A. & Tsanas, A. (2012). UCı Machine Learning depo <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>. Irvine, CA: California Üniversitesi, bilgi Okulu ve bilgisayar bilimi </td>
</tr>

<tr>
  <td>Uçuş gecikmeleri verileri</td>
  <td>
Passenger, ABD 'nin ulaşım bölümünün (<a href="https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time">zamanında</a>) geçiş Bakanlığı veri koleksiyonundan alınan zaman içindeki performans verilerini alır.
<p></p>
Veri kümesi Nisan-Ekim 2013 ' de geçen süreyi içerir. Azure Machine Learning Studio klasik sürümüne yüklemeden önce, veri kümesi şu şekilde işlenir:
<ul>
  <li>Veri kümesi, kıst ABD 'de yalnızca 70 ortalamanızı havaalanları kapsayacak şekilde filtrelenmiştir</li>
  <li>İptal edilen fışıkları 15 dakikadan uzun bir süre gecikti olarak etiketlendi</li>
  <li>Ayrılan fışıkların ölçeği filtrelendi</li>
  <li>Şu sütunlar seçildi: Year, month, DayofMonth, DayOfWeek, taşıyıcı, Originairportıd, Destairportıd, CRSDepTime, DepDelay, DepDel15, CRSArrTime, ArrDelay, ArrDel15, Iptal edildi</li>
</ul>
</td>
</tr>

<tr>
  <td>Uçuş süresi performansı (ham)</td>
  <td>
Uçak kayıt kayıtları, 2011 Ekim ayının Birleşik Devletler dahilinde.
<p></p>
<b>Kullanım:</b> Uçuş gecikmelerini tahmin edin. 
<p></p>
<b>Ilgili araştırma:</b> . Nakliye <a href="https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time">https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time</a>.
  </td>
</tr>

<tr>
  <td>Orman verileri tetikleyen</td>
  <td>
Sıcaklık ve nem dizini ve rüzgar hızı gibi hava durumu verilerini içerir. Veriler, Kuzey Doğu Portekiz 'nin bir alanından alınır ve ormanın kayıtlarıyla birleştirilir.
<p></p>
<b>Kullanım:</b> Bu zor bir gerileme görevidir ve bu, hedefin, ormanın yazılan alanını tahmin etmek için tasarlanmıştır. 
<p></p>
<b>Ilgili araştırma:</b> Cortez, P. & moraa, A. (2008). UCı Machine Learning depo <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>. Irvine, CA: California Üniversitesi, bilgi Okulu ve bilgisayar bilimi
<p></p>
[Cortez ve morao, 2007] P. Cortez ve. Morasıs. Bir veri araştırma yaklaşımı, türüdür verisi kullanılarak ateşlenir. J. netes, a. F. Santos ve J. ve ADO eds., yapay zeka 'daki yeni eğilimler, 6. EPıA 2007-yapay zeka 'daki Portekizce Konferansı, Aralık, Guimarães, Portekiz, PP. 512-523, 2007. APPIA, ıSBN-13 978-989-95618-0-9. Şu adreste bulunabilir: <a href="http://www.dsi.uminho.pt/~pcortez/fires.pdf">http://www.dsi.uminho.pt/~pcortez/fires.pdf</a>.
  </td>
</tr>

<tr>
  <td>Alman kredi kartı UCı veri kümesi</td>
  <td>
Alman. Data dosyasını kullanarak UCı Statgünlüğü (Almanya kredi kartı) veri kümesi (<a href="https://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)">Statlog + Almanya + kredi + veri</a>).
<p></p>
Veri kümesi, bir öznitelik kümesiyle tanımlanan kişileri, düşük veya yüksek kredi riskleri olarak sınıflandırır. Her örnek bir kişiyi temsil eder. Hem sayısal hem de kategorik ve bir ikili etiketi (kredi risk değeri) 20 özellik vardır. Yüksek kredi risk girişlerinde etiket = 2, düşük kredi risk girişlerinde etiket = 1 vardır. Düşük riskli bir örneği yüksek olarak sınıflandırın maliyeti 1 ' dir, ancak yüksek riskli örnek 5 ' i yanlış sınıflandırma maliyeti 5 ' tir.
  </td>
</tr>

<tr>
  <td>IMDB film başlıkları</td>
  <td>
Veri kümesi Twitter 'da derecelendirilen filmler hakkında bilgiler içerir: ıMDB film KIMLIĞI, film adı, tarz ve üretim yılı. Veri kümesinde 17K filmler vardır. Veri kümesi "S" kağıdına tanıtılmıştı. Çizeneler, T. de Pessemier ve L. Maronlar. MovieTweetings: Twitter 'Dan toplanan bir film derecelendirmesi veri kümesi. Öneren sistemleri için Crowdsour, ve ınsan hesaplamasında Workshop, RecSys 2013. "
  </td>
</tr>

<tr>
  <td>Iris iki sınıf verisi</td>
  <td>
Bu, büyük olasılıkla, model tanıma belgelerinde bulunan en iyi bilinen veritabanıdır. Veri kümesi görece küçüktür, bu da üç Iris tarafından bulunan her türlü Petal ölçümünden 50 örnek içerir.
<p></p>
<b>Kullanım:</b> Iris türünü ölçülerden tahmin edin.  
<p></p>
<b>Ilgili araştırma:</b> Fisher, R.A. (1988). UCı Machine Learning depo <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>. Irvine, CA: California Üniversitesi, bilgi Okulu ve bilgisayar bilimi </td>
</tr>

<tr>
  <td>Film arası</td>
  <td>
Veri kümesi, film arası doldurulabilir veri kümesinin genişletilmiş bir sürümüdür. Veri kümesi, filmler için 170K derecelendirmesine sahiptir ve Twitter 'daki iyi yapılandırılmış ara kaynaklardan ayıklanır. Her örnek bir tweet temsil eder ve bir tanımlama grubu: Kullanıcı KIMLIĞI, ıMDB film KIMLIĞI, derecelendirme, zaman damgası, bu tweet için sık kullanılanlar sayısı ve bu tweet yeniden dengelendirme sayısı. DataSet, öneren Systems Challenge 2014 için bir. diyor, S. Çizms, B. Loni ve D. Tikk tarafından kullanılabilir hale getirilir.
  </td>
</tr>

<tr>
  <td>Çeşitli otomobil 'leri için MPG verileri</td>
  <td>
Bu veri kümesi, Carnegie Mellon University 'in StatLib kitaplığı tarafından sağlanmış olan veri kümesinin kısmen değiştirilmiş bir sürümüdür. Veri kümesi 1983 American Istatistiksel Ilişki Exposition 'da kullanıldı.
<p></p>
Veriler, her galon için mil cinsinden çeşitli otomobil 'leri için yakıt tüketimini listeler. Ayrıca, silindir sayısı, motor öteleme, horseüs, toplam ağırlık ve hızlandırma gibi bilgileri de içerir.
<p></p>
<b>Kullanım:</b> Üç değerli farklı özniteliğe ve beş sürekli özniteliğe göre yakıt ekonomisi tahmin edin. 
<p></p>
<b>Ilgili araştırma:</b> StatLib, Carnegie Mellon University, (1993). UCı Machine Learning depo <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>. Irvine, CA: California Üniversitesi, bilgi Okulu ve bilgisayar bilimi </td>
</tr>

<tr>
  <td>Pima Indians diabetes Ikili sınıflandırma veri kümesi</td>
  <td>
Ulusal şekilde diabetes ve digestive ve Kidney Dimevsimses veritabanı veri kümesi. Veri kümesi Pima Hint mirasın kadın hastalarını odaklanmak üzere filtrelenmiştir. Veriler, düzeylerdeki ve yaşam stili faktörlerinin yanı sıra, Levels gibi tıbbi verileri içerir.
<p></p>
<b>Kullanım:</b> Konunun diabtes (ikili sınıflandırma) olup olmadığını tahmin edin. 
<p></p>
<b>Ilgili araştırma:</b> Sigilto, V. (1990). UCI Machine Learning deposu <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml"</a>. Irvine, CA: California Üniversitesi, bilgi Okulu ve bilgisayar bilimi </td>
</tr>

<tr>
  <td>Restoran müşteri verileri</td>
  <td>
Demografik ve Tercihler dahil olmak üzere müşteriler hakkında bir meta veri kümesi.
<p></p>
<b>Kullanım:</b> Bu veri kümesini diğer iki restoran veri kümesiyle birlikte kullanarak bir öneren sistemi eğitme ve test edin. 
<p></p>
<b>Ilgili araştırma:</b> Bache, K. ve Lichman, M. (2013). UCı Machine Learning depo <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>. Irvine, CA: California Üniversitesi, bilgi Okulu ve bilgisayar bilimi.
  </td>
</tr>

<tr>
  <td>Restoran özellik verileri</td>
  <td>
Restoran ve bunların özellikleri (yiyecek türü, dinleme stili ve konum gibi) için bir meta veri kümesi.
<p></p>
<b>Kullanım:</b> Bu veri kümesini diğer iki restoran veri kümesiyle birlikte kullanarak bir öneren sistemi eğitme ve test edin. 
<p></p>
<b>Ilgili araştırma:</b> Bache, K. ve Lichman, M. (2013). UCı Machine Learning depo <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>. Irvine, CA: California Üniversitesi, bilgi Okulu ve bilgisayar bilimi.
  </td>
</tr>

<tr>
  <td>Restoran derecelendirmeleri</td>
  <td>
Kullanıcılar tarafından, 0 ' dan 2 ' ye kadar bir ölçeğe göre restoranlar için verilen derecelendirmeleri içerir.
<p></p>
<b>Kullanım:</b> Bu veri kümesini diğer iki restoran veri kümesiyle birlikte kullanarak bir öneren sistemi eğitme ve test edin. 
<p></p>
<b>Ilgili araştırma:</b> Bache, K. ve Lichman, M. (2013). UCı Machine Learning depo <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>. Irvine, CA: California Üniversitesi, bilgi Okulu ve bilgisayar bilimi.
  </td>
</tr>

<tr>
  <td>Çelik benzetilmiş çok sınıflı veri kümesi</td>
  <td>
Bu veri kümesi, çelik benzetilmiş denemelerinden bir dizi kayıt içerir. Bu, ortaya çıkan çelik türlerin fiziksel özniteliklerini (Width, kalınlığı, Type (bobin, sheet, vb.) içerir.
<p></p>
<b>Kullanım:</b> İki sayısal Sınıf özniteliği tahmin edin; sertlik veya güç. Ayrıca, öznitelikler arasında bağıntıları analiz edebilirsiniz.
<p></p>
Çelik dereceleri, SAE ve diğer kuruluşlar tarafından tanımlanan bir küme standardını izler. Belirli bir ' sınıf ' (sınıf değişkeni) arıyor ve gereken değerleri anlamak istiyorsunuz. 
<p></p>
<b>Ilgili araştırma:</b> Sterling, D. & Buntine, W. (NA). UCı Machine Learning depo <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>. Irvine, CA: California Üniversitesi, bilgi Okulu ve bilgisayar bilimi
<p></p>
Daha fazla çelik için faydalı bir kılavuz bulabilirsiniz: <a href="https://otk-sitecore-prod-v2-cdn.azureedge.net/-/media/from-sharepoint/documents/product/outokumpu-steel-grades-properties-global-standards.pdf">https://otk-sitecore-prod-v2-cdn.azureedge.net/-/media/from-sharepoint/documents/product/outokumpu-steel-grades-properties-global-standards.pdf</a>
  </td>
</tr>

<tr>
  <td>Telescope verileri</td>
  <td>
Hem bir Monte Carlo işlemi kullanılarak hem de benzetilen yüksek enerji gama parçacık, arka plan gürültüsünü ile birlikte kayıt.
<p></p>
Benzetim amacı, taban tabanlı atmosfer Cherenkov gama Telescopes doğruluğunu geliştirmesiydi. Bu işlem, istenen sinyal (Cherenkov Radiation showers) ve arka plan gürültüsü (hadronic showers tarafından başlatılan ve üst atmoson küredeki cosmik ışınları) arasında ayrım yapmak için istatistiksel yöntemler kullanılarak yapılır.
<p></p>
Veriler, kamera merkezine yönlendirilmekte olan uzun eksen ile bir elongeçişli küme oluşturmak için önceden işlenir. Bu elipsin özellikleri (genellikle Teplas parametreleri olarak adlandırılır), ayırt edici için kullanılabilecek görüntü parametrelerinin arasındadır.
<p></p>
<b>Kullanım:</b> Bir hediyeleri partisi görüntüsünün sinyal veya arka plan paraziti temsil ettiğini tahmin edin.
<p></p>
<b>Notlar:</b> Bir arka plan olayını sinyal olarak sınıflandırırken bir sinyal olayını arka plan olarak sınıflandırmadan daha kötü olduğundan, bu veriler için basit sınıflandırma doğruluğu anlamlı değildir. Farklı sınıflandırıcımaların karşılaştırılması için ROC grafiğinin kullanılması gerekir. Bir arka plan olayını sinyal olarak kabul etmenin olasılığı Şu eşiklerden birinin altında olmalıdır: 0,01, 0,02, 0,05, 0,1 veya 0,2.
<p></p>
Ayrıca, arka plan olayı sayısının (hadronic showers için h) yeterince tahmin edilir olduğunu unutmayın. Gerçek ölçümlerde h veya gürültü sınıfı olayların çoğunu temsil eder. 
<p></p>
<b>Ilgili araştırma:</b> Bock, R.K. (1995). UCı Machine Learning depo <a href="https://archive.ics.uci.edu/ml">https://archive.ics.uci.edu/ml</a>. Irvine, CA: California Üniversitesi, bilgi Okulu </td>
</tr>

<tr>
  <td>Hava durumu veri kümesi</td>
  <td>
NOAA 'den saatlik Land tabanlı hava durumu gözlemlerini (<a href="https://az754797.vo.msecnd.net/data/WeatherDataset.csv">201304 ile 201310 arasında Birleşik veriler</a>).
<p></p>
Hava durumu verileri, Havaalanı hava durumu istasyonlarından yapılan ve Nisan-Ekim 2013 zaman dilimini kapsayan gözlemlerini kapsamaktadır. Azure Machine Learning Studio klasik sürümüne yüklemeden önce, veri kümesi şu şekilde işlenir:
<ul>
  <li>Hava durumu istasyonu kimlikleri ilgili Havaalanı kimliklerine eşlendi</li>
  <li>70 ortalamanızı Havaalanı ile ilişkilendirilmemiş Hava durumu istasyonları filtrelendi</li>
  <li>Tarih sütunu ayrı yıl, ay ve gün sütunlarına bölündü</li>
  <li>Şu sütunlar seçildi: Airportıd, Year, month, Day, Time, TimeZone, ufuk koşulunun, görünürlük, dalgalı bir tür, DryBulbFarenheit, Drybulbgrat, WetBulbFarenheit, Wetbulbgrat, DewPointFarenheit, Dewpointsanti, Relativenem, PressureTendency Speed, WınPressureChange Direction, Valueforwınpr karakteri, Stationbasınç,,, deniz Levelbasınç, RecordType, Hourlyprecıp, Altimeter</li>
</ul>
  </td>
</tr>

<tr>
  <td>Vikipedi SP 500 veri kümesi</td>
  <td>
Veriler, XML verileri olarak depolanan her bir S & P 500 şirketinin makalelerini temel alan Vikipden (<a href="https://www.wikipedia.org/">https://www.wikipedia.org/</a>) türetilir.
<p></p>
Azure Machine Learning Studio klasik sürümüne yüklemeden önce, veri kümesi şu şekilde işlenir:
<ul>
  <li>Belirli bir şirkete ait metin içeriğini Ayıkla</li>
  <li>Wiki biçimlendirmesini kaldır</li>
  <li>Alfasayısal olmayan karakterleri kaldır</li>
  <li>Tüm metni küçük harfe Dönüştür</li>
  <li>Bilinen şirket kategorileri eklendi</li>
</ul>
<p></p>
Bazı şirketlerde bir makalenin bulunamadığını, bu nedenle kayıt sayısının 500 ' den küçük olduğunu unutmayın.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/direct_marketing.csv">direct_marketing. csv</a></td>
  <td>
Veri kümesi, müşteri verileri ve doğrudan posta kampanyasına olan yanıtları hakkında göstergeler içerir. Her satır bir müşteriyi temsil eder. Veri kümesi, Kullanıcı demografları ve geçmiş davranışı ve üç etiket sütunu (ziyaret, dönüştürme ve harcamayı) hakkında dokuz özellik içerir.  Web sitesini ziyaret eden bir müşterinin pazarlama kampanyadan sonra ziyaret edildiğini gösteren bir ikili sütundur. Dönüştürme bir müşterinin bir şeyi satın aldığını gösterir. Harcama, harcanan tutardır.  Veri kümesi, Minetatdata e-posta Analizi ve veri araştırma sınaması için Kevin Teplstrom tarafından kullanılabilir hale getirilir.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/lyrl2004_tokens_test.csv">lyrl2004_tokens_test. csv</a></td>
  <td>
RCV1-v2 Rebilgisayarlarda Haberler veri kümesindeki test örneklerinin özellikleri. Veri kümesinde, 50.000 1k haber makaleleri ve kimlikleri (veri kümesinin ilk sütunu) birlikte bulunur. Her makale simgeleştirilmiş, soluk ve stemmed 'dir. Veri kümesi David tarafından kullanılabilir hale getirilir. D. Lewis.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/lyrl2004_tokens_train.csv">lyrl2004_tokens_train. csv</a></td>
  <td>
RCV1-v2 yeniden bilgisayarları \ \ çalışanlar haber veri kümesindeki eğitim örneklerinin özellikleri. Veri kümesinde, kendi kimlikleri (veri kümesinin ilk sütunu) ile birlikte 23K haberleri makaleleri vardır. Her makale simgeleştirilmiş, soluk ve stemmed 'dir. Veri kümesi David tarafından kullanılabilir hale getirilir. D. Lewis.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/network_intrusion_detection.csv">network_intrusion_detection. csv</a><br></td>
  <td>
KDD Fin1999 bilgi bulma ve veri araştırma araçları yarışmasının (<a href="https://kdd.ics.uci.edu/databases/kddcup99/kddcup99.html">kddcup99. html</a>) veri kümesi.
<p></p>
Veri kümesi, Azure Blob depolama (<a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/network_intrusion_detection.csv">network_intrusion_detection. csv</a>) ' de indirildi ve depolandı ve hem eğitim hem de test veri kümelerini içerir. Eğitim veri kümesinde, Etiketler dahil olmak üzere yaklaşık 126K satır ve 43 sütun vardır. Üç sütun etiket bilgisinin bir parçasıdır ve sayısal ve dize/kategorik özelliklerden oluşan 40 sütun, modeli eğitmek için kullanılabilir. Test verilerinde, eğitim verileriyle aynı 43 sütunlu, yaklaşık 22.5 K test örnekleri vardır.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/rcv1-v2.topics.qrels.csv">RCV1-v2. konular. qrels. csv</a></td>
  <td>
RCV1-v2 Rebilgisayarlarda Haberler veri kümesindeki haber makalelerine yönelik konu atamaları. Bir haber makalesi, birkaç konuya atanabilir. Her satırın biçimi, "&lt;konu adı&gt; &lt;belge kimliği&gt; 1". Veri kümesi, 2.6/konu atamalarını içerir. Veri kümesi David tarafından kullanılabilir hale getirilir. D. Lewis.
  </td>
</tr>

<tr>
  <td><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/student_performance.txt">student_performance. txt</a></td>
  <td>
Bu veriler, KDD Fin2010 öğrenci performansı değerlendirme zorluğuyla (<a href="https://www.kdd.org/kdd-cup/view/kdd-cup-2010-student-performance-evaluation">öğrenci performansı değerlendirmesi</a>) gelir. Kullanılan veriler Algebra_2008_2009 eğitim kümesidir (Stamper, J., Niculescu-mizil, A., Riur, S., Gordon, G.J., & Koednik, K.R. (2010). Algeköşeli ı 2008-2009. KDD Fin2010 eğitim veri madenciliği zorluğuyla sınama veri kümesi. <a href="https://pslcdatashop.web.cmu.edu/KDDCup/downloads.jsp">Downloads. jsp</a>' de bulun.
<p></p>
Veri kümesi, Azure Blob depolama (<a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/student_performance.txt">student_performance. txt</a>) ' de indirildi ve depolandı ve bir öğrenci eğitim sisteminden günlük dosyaları içeriyor. Sağlanan özellikler sorun KIMLIĞI ve kısa açıklaması, öğrenci KIMLIĞI, zaman damgası ve sorunu doğru şekilde çözmeden önce kaç tane deneme yaptığını içerir. Özgün veri kümesinde 8.9 d kayıtları vardır; Bu veri kümesi, ilk 100K satırına örneklenir. Veri kümesinde, farklı türlerde 23 sekmeyle ayrılmış sütun vardır: sayısal, kategorik ve zaman damgası.
  </td>
</tr>

</table>

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Kickdenemeleri ile örnek başlatma](sample-experiments.md)

<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
