---
title: Azure haritalar sözlüğü | Microsoft Docs
description: Azure haritalar, Location-Based Hizmetleri ve GıS ile ilişkili yaygın olarak kullanılan koşulların bir sözlüğü.
author: rbrundritt
ms.author: richbrun
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 05071be4fc1705787a649d331613e8d9399f983f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92897186"
---
# <a name="glossary"></a>Sözlük

Aşağıdaki listede, Azure haritalar hizmetleriyle kullanılan yaygın sözcükler açıklanmaktadır.

## <a name="a"></a>A

<a name="address-validation"></a>**Adres doğrulaması**: bir adresin varlığını doğrulama işlemi.

<a name="advanced-routing"></a>**Gelişmiş yönlendirme**: yol yönlendirme verilerini kullanarak ön işlemler gerçekleştiren hizmetlerin bir koleksiyonu; Örneğin, erişilebilir aralıkları (izde), uzaklık matrislerini ve toplu yol isteklerini hesaplama.

<a name="aerial-imagery"></a>**Havadan görüntü**: bkz. [uydu Imagery](#satellite-imagery). 

<a name="along-a-route-search"></a>**Bir yol araması boyunca**: belirtilen bir tur süresi içinde veya bir yol yolundan uzaklığın içindeki verileri gösteren bir uzamsal sorgu.

<a name="altitude"></a>**Yükseklik**: bir başvuru yüzeyi üzerindeki bir noktanın yükseklik veya dikey yükselmesi. Yükseklik ölçümleri, ortalama deniz düzeyi gibi belirli bir başvuru veri tabansına dayanır. Ayrıca bkz. yükseltme.

<a name="ambiguous"></a>**Belirsiz**: bir nesne belirli bir öznitelik için uygun şekilde iki veya daha fazla değere atanabileceği zaman, veri sınıflandırmasında belirsizliğin bir durumu. Örneğin, coğrafi kodlama "CA" olduğunda iki belirsiz sonuç döndürülür: "Kanada" ve "California". "CA", "Kanada" ve "California" için sırasıyla bir ülke/bölge ve bir durum kodudur. 

<a name="annotation"></a>**Ek açıklama**: kullanıcıya bilgi sağlamak için haritada görüntülenecek metin veya grafikler. Ek açıklama belirli bir harita varlığını tanımlayabilir veya tanımlayabilir, haritadaki bir alanla ilgili genel bilgiler sağlayabilir veya haritanın kendisi hakkında bilgi sağlayabilir.

<a name="antimeridian"></a>**Antimeriyen**<sup>: 180.</sup> meridyen olarak da bilinir. Bu nokta,-180 derece ve 180 derece uygun olan noktasıdır. Bu, dünyanın her yerindeki ana meridyeninin tersidir.

<a name="application-programming-interface-api"></a>**Uygulama programlama arabirimi (API)**: geliştiricilerin uygulama oluşturmalarına olanak tanıyan bir belirtim.

<a name="api-key"></a>**API anahtarı**: bkz. [paylaşılan anahtar kimlik doğrulaması](#shared-key-authentication).

<a name="area-of-interest-aoi"></a>**Ilgilendiğiniz alan (AOI)**: bir harita veya veritabanı üretimi için bir odak alanı tanımlamak için kullanılan uzantı.

<a name="asset-tracking"></a>**Varlık izleme**: kişi, araç veya başka bir nesne gibi bir varlığın konumunu izleme işlemi.

<a name="asynchronous-request"></a>**Zaman uyumsuz istek**: bağlantıyı açan ve zaman uyumsuz istek için bir tanımlayıcı döndüren sunucuya istek yapan bir http isteği, ardından bağlantıyı kapatır. Sunucu isteği işlemeye devam eder ve Kullanıcı tanımlayıcıyı kullanarak durumu denetleyebilir. İsteğin işlenmesi tamamlandığında, Kullanıcı yanıtı indirebilir. Bu tür bir istek, uzun süre çalışan süreçler için kullanılır.

<a name="autocomplete"></a>**Otomatik tamamlama**: bir uygulamanın, Kullanıcı tarafından yazılan bir sözcüğün geri kalanını tahmin eden bir özellik. 

<a name="autosuggest"></a>**Otomatik öneri**: bir uygulamada, kullanıcının yazdıklarınız için mantıksal olasılıkları tahmin eden bir özellik.

<a name="azure-location-based-services-lbs"></a>**Azure konum tabanlı hizmetler (lbs)**: Önizlemedeki Azure Maps 'in eski adı.

<a name="azure-active-directory"></a>**Azure Active Directory (Azure AD)**: Azure AD, Microsoft 'un bulut tabanlı kimlik ve erişim yönetimi hizmetidir. Azure haritalar Azure AD tümleştirmesi, tüm Azure Maps API 'Leri için önizleme aşamasında şu anda kullanılabilir. Azure AD, Azure Maps kaynaklarına ayrıntılı erişim sağlamak için Azure rol tabanlı erişim denetimi 'ni (Azure RBAC) destekler. Azure haritalar Azure AD tümleştirmesi hakkında daha fazla bilgi edinmek için bkz. Azure Maps [ve Azure AD](azure-maps-authentication.md) ve [kimlik doğrulamasını Azure haritalar 'da yönetme](how-to-manage-authentication.md).

<a name="azure-maps-key"></a>**Azure haritalar anahtarı**: [paylaşılan anahtar kimlik doğrulamasına](#shared-key-authentication)bakın.

## <a name="b"></a>B

<a name="base-map"></a>**Temel harita**: yollar, yer işaretleri ve politik sınırlar gibi arka plan başvuru bilgilerini görüntüleyen bir harita uygulamasının parçası.

<a name="batch-request"></a>**Toplu istek**: birden çok isteği tek bir istek halinde birleştirme işlemi.

<a name="bearing"></a>**Pul**: bir noktanın başka bir noktaya göre yatay yönü. Bu, 0 derecenin, saat yönünde bir yönde 360 derecenin arasında, Kuzey 'e göre bir açı olarak ifade edilir. 

<a name="boundary"></a>**Sınır**: ülkeler/bölgeler, bölgelerler ve özellikler gibi bitişik siyasi varlıkların ayrılması için çizgi veya Çokgen. Sınır, Rivers, mountaıns veya duvarlar gibi fiziksel özellikleri takip edebilir veya izleyemeyebilir.

<a name="bounds"></a>**Sınırlar**: bkz. [sınırlama kutusu](#bounding-box).

<a name="bounding-box"></a>**Sınırlayıcı kutusu**: haritadaki dikdörtgen bir alanı temsil etmek için kullanılan bir koordinat kümesi. 

## <a name="c"></a>C

<a name="cadastre"></a>**Cadadstre**: kayıtlı bir Land ve özelliklerin kaydı. Ayrıca bkz. [paket](#parcel).

<a name="camera"></a>**Kamera**: Etkileşimli harita denetimi bağlamında bir kamera, görünümün haritalar alanını tanımlar. Kameranın Görünüm penceresi, birkaç eşleme parametresine göre belirlenir: Orta, yakınlaştırma düzeyi, sıklık, pul. 

<a name="centroid"></a>**Centroıd**: bir özelliğin geometrik ortası. Bir çizginin centroıd 'si, bir çokgenin centroıd 'si alanın ortası olduğu sürece orta nokta olur.

<a name="choropleth-map"></a>**Choroptath Haritası**: alanların istatistiksel bir değişken ölçüsünün oranıyla orantılı şekilde gölgelendirilmiş olması. Bu istatistiksel değişken haritada görüntülenir. Örneğin, her bir ABD durumunun kenarlığını, göreli popülasyonunu diğer tüm durumlara göre renklendirmesini sağlar.

<a name="concave-hull"></a>**İçbükey kabuk**: belirtilen veri kümesindeki tüm şekilleri kapsayan olası bir İçve geometriyi temsil eden bir şekil. Oluşturulan Şekil, verileri plastik Sarla sarmalama ve sonra ısıtma sağlar ve bu sayede noktaları arasındaki büyük yayılmaları diğer veri noktalarına doğru bir şekilde gösterir.

<a name="consumption-model"></a>**Tüketim modeli**: bir aracın yakıt veya elektrik tükettiği oranı tanımlayan bilgiler. Ayrıca bkz. [tüketim modeli belgeleri](consumption-model.md).

<a name="control"></a>**Denetim**: arabirim için bir davranış kümesi tanımlayan bir grafik kullanıcı arabiriminden oluşan, kendi içinde veya yeniden kullanılabilir bir bileşen. Örneğin, bir harita denetimi genellikle kullanıcı arabiriminin etkileşimli bir harita yükleyen bölümüdür.

<a name="convex-hull"></a>**Convex kabuk**: A convex kabuk, belirtilen veri kümesindeki tüm şekilleri kapsayan minimum convex geometrisini temsil eden bir şekildir. Oluşturulan Şekil, veri kümesinin etrafında esnek bir bant kaydırmasına benzer.

<a name="coordinate"></a>**Koordinat**: bir haritanın konumunu temsil etmek için kullanılan boylam ve enlem değerlerinden oluşur.

<a name="coordinate-system"></a>**Koordinat sistemi**: iki veya üç boyutta bir boşluk içinde noktaların konumlarını tanımlamak için kullanılan bir başvuru çerçevesi.

<a name="country-code"></a>**Ülke kodu**: ISO standardına dayalı bir ülke/bölge için benzersiz bir tanımlayıcı. ISO2, bir ülke/bölge (örneğin, ABD) için iki karakterli bir koddur. Bu, ISO3 üç karakterli bir kodu temsil eder (örneğin, ABD).

<a name="country-subdivision"></a>**Ülke alt bölümü**: bir ülkenin/bölgenin, genellikle eyalet veya il olarak bilinen ilk düzey alt bölümü.

<a name="country-secondary-subdivision"></a>**Ülke ikincil alt bölümü**: bir ülkenin/bölgenin, genellikle ilçe olarak bilinen ikinci düzey alt bölümü.

<a name="country-tertiary-subdivision"></a>**Country üçüncül alt bölümü**: bir ülkenin/bölgenin üçüncü düzey alt bölümü, genellikle bir Ward gibi adlandırılmış bir alandır.

<a name="cross-street"></a>**Çapraz cadde**: iki veya daha fazla cadde kesiştiğinde bir nokta.

<a name="cylindrical-projection"></a>**Silindirik projeksiyonu**: bir kürede veya Sphere öğesinden bir tanjantı veya Sekant silindiri üzerine noktaları dönüştüren projeksiyon. Silindir daha sonra yukarıdan aşağıya dilimdir ve düzleme düzleştirilir.

## <a name="d"></a>D

<a name="datum"></a>**Veri**: bir ölçüm sisteminin başvuru belirtimleri, bir yüzey üzerinde (yatay bir veri) veya bir yüzey üzerindeki veya altındaki (dikey bir veri) bir veya daha yüksek olan yükseklikleri konumlandırır.

<a name="dbf-file"></a>**DBF dosyası**: Shapefiles (shp) ile birlikte kullanılan bir veritabanı dosya biçimi.

<a name="degree-minutes-seconds-dms"></a>**Derece dakika saniye (DMS)**: Enlem ve Boylam açıklamak için ölçü birimi. Bir<sup>derece, dairenin 1/360 '</sup> dir. Bir derece 60 dakikaya bölünür ve bir dakika 60 saniyeye bölünür.

<a name="delaunay-triangulation"></a>**Delaunay üç taraflı**: bir noktadan oluşan bir veri kümesinden bitişik, çakışmayan üçgenler bir kafes oluşturmaya yönelik bir tekniktir. Her bir üçgenin atlama çemberi, iç öğesinden gelen veri kümesinden işaret yok.

<a name="demographics"></a>**Demografik**: bir insan popülasyonu için istatistiksel Özellikler (yaş, Doğum oranı ve gelir gibi).

<a name="destination"></a>**Hedef**: birisinin yolculukta olduğu bir uç nokta veya konum.

<a name="digital-elevation-model-dem"></a>**Dijital yükseltme modeli (DEM)**: ortak bir veri kullanılarak düzenli aralıklarla bir alan üzerinde yakalanan bir yüzey ile ilgili yükseltme değerlerinin veri kümesi. DEMs 'ler genellikle teryağmur rahatını temsil etmek için kullanılır.

<a name="dijkstra's-algorithm"></a>**Dijkstra 'ın algoritması**: iki noktası arasındaki en kısa yolu bulmak için bir ağın bağlantısını inceleyen bir algoritma.

<a name="distance-matrix"></a>**Uzaklık matrisi**: bir çıkış ve hedef kümesi arasındaki seyahat süresini ve uzaklık bilgilerini içeren bir matris. 

## <a name="e"></a>E

<a name="elevation"></a>**Yükseltme**: bir noktanın veya bir başvuru yüzeyi ya da veri alanının üzerinde veya altında bulunan dikey uzaklık. Genellikle, başvuru yüzeyi ortalama deniz düzeyidir. Yükseltme genellikle kara 'nin dikey yüksekliğini ifade eder.

<a name="envelope"></a>**Zarf**: bkz. [sınırlayıcı kutusu](#bounding-box).

<a name="extended-postal-code"></a>**Genişletilmiş posta kodu**: ek bilgi içerebilen bir posta kodu. Örneğin, ABD 'de, ZIP kodlarında beş basamak vardır. Ancak, ZIP + 4 olarak bilinen bir genişletilmiş ZIP kodu dört ek basamak içerir. Bu ek rakamlar, şehir bloğu, bir grup grubu veya bir posta kutusu gibi beş basamaklı teslim alanı içinde coğrafi bir segmenti belirlemek için kullanılır. Coğrafi segmentin bilinmesi, etkili posta sıralaması ve teslimi konusunda yardımcı olur.

<a name="extent"></a>**Kapsam**: bkz. [sınırlama kutusu](#bounding-box).

## <a name="f"></a>F

<a name="federated-authentication"></a>**Federal kimlik doğrulaması**: birden çok Web ve mobil uygulama genelinde tek bir oturum açma/kimlik doğrulama mekanizmasının kullanılmasına izin veren bir kimlik doğrulama yöntemi. 

<a name="feature"></a>**Özellik**: bir geometriyi ek meta veri bilgileriyle birleştiren bir nesne. 

<a name="feature-collection"></a>**Özellik koleksiyonu**: özellik nesnelerinin bir koleksiyonu.

<a name="find-along-route"></a>**Yol boyunca bul**: belirtilen bir tur süresi içinde veya bir yol yolundan uzaklığın içinde olan verileri gösteren bir uzamsal sorgu.

<a name="find-nearby"></a>**Yakında bul**: bir noktadan sonra sabit bir düz çizgi mesafesini (Crow uçın olarak) arayan uzamsal bir sorgu.

<a name="fleet-management"></a>**Fleet yönetimi**: araba, structuralks, sevk ve düzler gibi ticari araçlar 'ın yönetimi. Fleet yönetimi, araç finansma, bakım, telematik (izleme ve tanılama) ve sürücü, hız, yakıt ve sistem durumu ve güvenlik yönetimi gibi bir dizi işlev içerebilir. Fleet yönetimi, işletmelerini kullanan şirketler tarafından kullanılan bir işlemdir. Şirketler riskleri en aza indirmek ve genel taşıma ve personel maliyetlerini azaltmak ve kamu mevzuatı ile uyumluluğu güvence altına almak istiyor.

<a name="free-flow-speed"></a>**Ücretsiz akış hızı**: ideal koşullarda beklenen ücretsiz akış hızı. Genellikle hız sınırı.

<a name="free-form-address"></a>**Ücretsiz form adresi**: tek satırlık bir metin olarak temsil edilen tam bir adres.

<a name="fuzzy-search"></a>**Benzer arama**: bir adres veya bir ilgi noktası olabilecek, metnin serbest biçimli dizesini alan bir arama. 

## <a name="g"></a>G

<a name="geocode"></a>**Geocode**: bir haritada bu konumu göstermek için kullanılabilecek bir koordinat içine dönüştürülmüş bir adres veya konum. 

<a name="geocoding"></a>**Coğrafi kodlama**: ileri coğrafi kodlama olarak da bilinen, konum verilerinin adresini koordinatlara dönüştürme işlemidir. 

<a name="geodesic-path"></a>**Coğrafi yol**: eğri yüzeyde iki punto arasındaki en kısa yol. Azure 'da işlendiğinde bu yol, Mercator projeksiyonu nedeniyle eğri bir çizgi olarak görünür.

<a name="geofence"></a>**Bölge sınırı: bir** cihaz bölge girdiğinde veya varsa olayları tetiklemek için kullanılabilen tanımlı bir coğrafi bölge.

<a name="geojson"></a>**Geojson**: noktaları, çizgileri ve çokgenler gibi coğrafi vektör verilerini depolamak için kullanılan, ortak bir JSON tabanlı dosya biçimidir. **Note**: Azure Maps, [burada belgelendiği](extend-geojson.md)gibi, GeoJSON 'ın genişletilmiş bir sürümünü kullanır.

<a name="geometry"></a>**Geometri**: nokta, çizgi veya Çokgen gibi bir uzamsal nesneyi temsil eder.

<a name="geometrycollection"></a>**GeometryCollection**: geometri nesnelerinin bir koleksiyonu.

<a name="geopol"></a>**Geopol**: tartışmalı Kenarlıklar ve yer adları gibi coğrafi olarak hassas verilere başvurur.

<a name="georeference"></a>**Georeference**: coğrafi verileri veya Imagery 'yi bilinen bir koordinat sistemine hizalama işlemi. Bu işlem, verileri kaydırma, döndürme, ölçekleme veya eğme içerebilir.

<a name="georss"></a>**GeoRSS**: RSS akışlarına uzamsal veri eklemek IÇIN bir xml uzantısı.

<a name="gis"></a>**GIS**: "coğrafi bilgi sistemi" için bir kısaltma. Eşleme sektörünü anlatmak için kullanılan ortak bir terim.

<a name="gml"></a>**GML**: Coğrafya biçimlendirme dili olarak da bilinir. Uzamsal verileri depolamak için bir XML dosyası uzantısı.

<a name="gps"></a>**GPS**: Global konumlandırma sistemi olarak da bilinen, dünya üzerindeki bir cihaz konumunun belirlenmesi için kullanılan bir uydu sistemidir. Büyük bir yerde, dünyanın herhangi bir yerindeki bir GPS alıcıya, kendi konumunu daha da hesaplamak için, dünyanın herhangi bir yerinde olan uydu gönderme sinyalleri iletir.

<a name="gpx"></a>**GPX**: GPS Exchange biçimi olarak da BILINEN, GPS cihazlarından yaygın olarak oluşturulan bir XML dosyası biçimidir.  

<a name="great-circle-distance"></a>**Harika daire uzaklığı**: bir Sphere yüzeyinin iki noktası arasındaki en kısa uzaklık.

<a name="greenwich-mean-time-gmt"></a>**Greenwich saati (GMT)**: Greenwich, England 'Da Royal Rasathanesi aracılığıyla çalıştırılan ana meridyendeki zaman.

<a name="guid"></a>**GUID**: genel benzersiz tanımlayıcı. Bir arabirim, sınıf, tür kitaplığı, bileşen kategorisi veya kaydı benzersiz bir şekilde tanımlamak için kullanılan dize.

## <a name="h"></a>H

<a name="haversine-formula"></a>**Haversinüs formülü**: bir Sphere üzerindeki iki noktaya ait büyük daire mesafesini hesaplamak için kullanılan ortak bir denklem.

<a name="hd-maps"></a>**HD Maps**: yüksek tanımlı haritalar olarak da bilinen, otonom bir şekilde yönlendirme için gereken Lane işaretleri, imza ve yön ışıkları gibi yüksek uygunlukta trafik ağ bilgilerinden oluşur.

<a name="heading"></a>**Başlık**: bir şeyin işaret ettiği veya karşılaştığı yönün yönü. Ayrıca bkz [..](#heading)

<a name="heatmap"></a>**Heatmap**: belirli bir alandaki noktaların yoğunluğunu temsil eden bir renk aralığının bir veri görselleştirmesi. Ayrıca bkz. tek bir eşleme.

<a name="hybrid-imagery"></a>**Hibrit Hibrit**: yol verileri ve üzerine eklenen Etiketler içeren uydu veya hava imakisi.

## <a name="i"></a>I

<a name="iana"></a>**IANA**: Internet atanmış numaralar yetkilisi için bir kısaltma. Genel IP adresi ayırmayı fazla görülen kar olmayan bir grup.

<a name="isochrone"></a>**Isovaone**: bir ısovaone, belirli bir konumdan herhangi bir yönde bir ulaşım modu için, birisinin belirli bir süre içinde geçebileceği alanı tanımlar. Ayrıca bkz. [erişilebilir Aralık](#reachable-range).

<a name="isodistance"></a>**İzde**: bir konum verildiğinde, bir ısovaone, birinin bir ulaşım modu için herhangi bir yönde belirli bir mesafe dahilinde dolaşabileceğini tanımlayan alanı tanımlar. Ayrıca bkz. [erişilebilir Aralık](#reachable-range).

## <a name="k"></a>K

<a name="kml"></a>**KML**: anahtar deliği biçimlendirme dili olarak da bilinen, noktaları, çizgileri ve çokgenler gibi coğrafi vektör verilerini depolamak için ortak bir XML dosyası biçimidir. 

## <a name="l"></a>L

<a name="landsat"></a>**Landısat**: çok Spectral, NASA tarafından geliştirilen ve arazi tarafından geliştirilen dünya dışı uydu. Bu Imagery, Agric, Ormancılık ve çizgi gibi birçok sektörde kullanılır.

<a name="latitude"></a>**Enlem**: bir Kuzey veya Güney yönünde kıtada 'tan derece olarak ölçülen angular uzaklığı.

<a name="level-of-detail"></a>**Ayrıntı düzeyi**: yakınlaştırma düzeyi ' ne bakın.

<a name="lidar"></a>**Lidar**: hafif algılama ve Aralık kısaltması. Yansıtma yüzeylerine uzaklıkları ölçmek için balıklarını kullanan uzaktan algılama tekniği.

<a name="linear-interpolation"></a>**Doğrusal ilişkilendirme**: bilinen değerler arasındaki doğrusal mesafeyi kullanarak bilinmeyen bir değerin tahmini.

<a name="linestring"></a>**LineString**: bir çizgiyi temsil etmek için kullanılan bir geometri. Çoklu çizgi olarak da bilinir. 

<a name="localization"></a>**Yerelleştirme**: farklı diller ve kültürler için destek.

<a name="logistics"></a>**Lojistik**: kişileri, gereçleri, malzemeleri veya varlıkları eşgüdümlü bir şekilde taşıma işlemi.

<a name="longitude"></a>**Boylam**: bir Doğu veya Batı yönünde asal meridyenlerden derece cinsinden ölçülen angular uzaklığı.

## <a name="m"></a>M

<a name="map-tile"></a>**Harita kutucuğu**: harita tuvalinin bir bölümünü temsil eden dikdörtgen bir resim. Daha fazla bilgi için [yakınlaştırma düzeyleri ve kutucuk Kılavuzu belgelerine](zoom-levels-and-tile-grid.md)bakın.

<a name="marker"></a>**İşaretleyici**: PIN veya raptiye olarak da bilinen, bir harita üzerindeki nokta konumunu temsil eden bir simgedir.

<a name="mercator-projection"></a>**Mercator projeksiyonu**: Rhumb Lines olarak bilinen ve meridyenlerin açılarından tasarruf eden düz parçalar olarak bilinen sabit kurs satırlarını temsil etme yeteneği nedeniyle, nadeniz amacıyla standart harita projeksiyonu yapan bir silindirik harita projeksiyonu. Tüm düz harita projeksiyonları, Dünya yüzeyinin doğru düzenine kıyasla haritanın şekillerini veya boyutlarını deforme edebilir. Mercator projeksiyonu, diğer yandan da siz de yaklaşımda daha küçük alanların Haritada daha büyük görünmesine benzer şekilde, eşlerden uzakta olan bölgeleri ayırır. 

<a name="multilinestring"></a>**MultiLineString**: bir LineString nesnelerinin koleksiyonunu temsil eden bir geometri. 

<a name="multipoint"></a>**MultiPoint**: nokta nesneleri koleksiyonunu temsil eden bir geometri.

<a name="multipolygon"></a>**MultiPolygon**: Çokgen nesnelerinin bir koleksiyonunu temsil eden bir geometri. Örneğin, Hawaii sınırını göstermek için her Adası bir çokgen ile ana hatlarıyla gösterilir. Bu nedenle, Hawaii 'nin sınırı bir MultiPolygon olacaktır.

<a name="municipality"></a>**Municipsellik**: bir şehir veya Town. 

<a name="municipality-subdivision"></a>**Municipitesi alt bölümü**: bir semtin veya "Downtown" gibi yerel alan adı gibi bir municipitesi alt bölümü.

## <a name="n"></a>N

<a name="navigation-bar"></a>**Gezinti çubuğu**: bir haritadaki yakınlaştırma düzeyini ayarlamak için kullanılan denetim kümesi, sıklık, döndürme ve temel harita katmanını değiştirme.

<a name="nearby-search"></a>**Yakın arama**: bir noktadan sonra sabit bir düz çizgi mesafesini (Crow uçın olarak) arayan uzamsal bir sorgu.

<a name="neutral-ground-truth"></a>**Nötr taban Truth**: etiketleri, temsil ettiği bölgenin resmi dilinde ve varsa yerel betiklerinde işleyen bir harita.

## <a name="o"></a>O

<a name="origin"></a>**Kaynak**: kullanıcının bulunduğu bir başlangıç noktası veya konumu.

## <a name="p"></a>P

<a name="panning"></a>**Yatay kaydırma**: bir sabit yakınlaştırma düzeyi koruyarak Haritayı herhangi bir yöne taşıma işlemi.

<a name="parcel"></a>**Paket**: bir Land veya özellik sınırı çizimi.

<a name="pitch"></a>**Sıklık**: eşlemenin, haritada 0 ' ın doğru bir şekilde gezineceği dikey görünüme göreli olan eğim miktarı.

<a name="point"></a>**Point**: haritadaki tek bir konumu temsil eden bir geometri. 

<a name="points-of-interest-poi"></a>**Ilgi noktaları (POı)**: bir iş, yer işareti veya genel ilgi çekici bir yer.

<a name="polygon"></a>**Çokgen**: haritadaki bir alanı temsil eden bir düz geometri. 

<a name="polyline"></a>**Çoklu çizgi**: bir çizgiyi temsil etmek için kullanılan bir geometri. Ayrıca, LineString olarak da bilinir. 

<a name="position"></a>**Konum**: bir noktanın boylam, enlem ve yüksekliği (x, y, z koordinatları).

<a name="post-code"></a>Posta **kodu**: [posta kodunu](#postal-code)görüntüleyin.

<a name="postal-code"></a>**Posta kodu**: belirli bir biçimde bir dizi harf veya sayı ya da her ikisi. Posta kodu, postanın teslimini basitleştirmek amacıyla coğrafi alanları bölgelere bölmek için bir ülkenin/bölgenin posta hizmeti tarafından kullanılır.

<a name="primary-key"></a>**Birincil anahtar**: Azure Maps paylaşılan anahtar kimlik doğrulaması için belirtilen iki abonelik anahtarından ilki. Bkz. [paylaşılan anahtar kimlik doğrulaması](#shared-key-authentication).

<a name="prime-meridian"></a>**Ana meridyen**: 0 derece boylam 'yi temsil eden bir Boylam çizgisi. Genellikle, boylam değerleri 180 derece kadar bir yöne doğru hareket ederken ve hareket halindeyken-180 derece yönlerine kadar artdıkça artar. 

<a name="prj"></a>**PRJ**: genellikle veri kümesinin bulunduğu tahmini koordinat sistemi hakkında bilgi Içeren bir şekil dosyası dosyasına eşlik eden bir metin dosyası.

<a name="projection"></a>**Projeksiyon**: enine Mercator, albümler eşittir alanı ve Robinson gibi harita projeksiyonunu temel alan, tasarlanan koordinat sistemi. Bu, dünya küresel yüzeyinin haritalarını iki boyutlu bir Kartezyen koordinat düzlemi üzerine proje yeteneği sağlar. Tasarlanan koordinat sistemleri bazen harita projeksiyonları olarak adlandırılır.

## <a name="q"></a>Q

<a name="quadkey"></a>**Quadkey**: bir quadtree döşeme sistemi içindeki bir kutucuk için Base-4 adres dizini. Daha fazla bilgi için bkz. [yakınlaştırma düzeyleri ve kutucuk Kılavuzu](zoom-levels-and-tile-grid.md) belgeleri.

<a name="quadtree"></a>**Quadtree**: her bir düğümün tam olarak dört çocuğu olan veri yapısı. Azure haritalar 'da kullanılan döşeme sistemi, bir kullanıcı için bir düzey yakınlaştırıp her harita kutucuğunun dört alt kutucuğa bölünmesinin ardından bir quadtree yapısı kullanır.  Daha fazla bilgi için bkz. [yakınlaştırma düzeyleri ve kutucuk Kılavuzu](zoom-levels-and-tile-grid.md) belgeleri.

<a name="queries-per-second-qps"></a>**Saniyedeki sorgu sayısı (QPS)**: bir saniyede bir hizmette veya platformda yapılabilecek sorgu veya istek sayısı. 

## <a name="r"></a>R

<a name="radial-search"></a>**Radyal arama**: bir noktadan sonra sabit bir düz çizgi mesafesini (Crow uçın olarak) arayan uzamsal bir sorgu. 

<a name="raster-data"></a>**Raster verileri**: her hücrenin sıcaklık gibi bilgileri temsil eden bir değer içerdiği, satırlar ve sütunlar (veya bir ızgara) halinde düzenlenmiş bir hücre (veya piksel) matrisi. Raster, uydu, dijital resim ve taranmış haritalardan oluşan dijital havadan fotoğraf fotoğraflarını kapsar.

<a name="raster-layer"></a>**Raster katmanı**: raster görüntülerden oluşan döşeme katmanı.

<a name="reachable-range"></a>**Erişilebilir Aralık**: erişilebilir bir Aralık, birisinin, bir konuma ulaşım modu için belirli bir süre veya mesafe içinde dolaşabilir alanı tanımlar. Ayrıca bkz. [ısovaone](#isochrone) ve [izdistance](#isodistance).

<a name="remote-sensing"></a>**Uzaktan algılama**: algılayıcı verilerinin bir uzaklıktan toplanması ve yorumlanması işlemi.

<a name="rest-service"></a>**REST hizmeti**: Rest, temsili durum aktarımı için temsil eder. REST hizmeti, HTTP GET ve POST istekleri olmak üzere en yaygın yöntemlerle iletişim kurmak için temel Web teknolojisine dayanan bir URL tabanlı Web hizmetidir. Bu tür hizmetler, geleneksel SOAP tabanlı hizmetlerden çok daha hızlı ve daha küçük bir şekilde oluşur.

<a name="reverse-geocode"></a>**Ters coğrafi kod**: bir koordinatı alma ve ' de bir haritada temsil eden adresi belirleme işlemi.

<a name="reproject"></a>**Yeniden proje**: bkz. [dönüştürme](#transformation).

<a name="rest-service"></a>**REST hizmeti**: temsili durum aktarımı için kısaltma. Merkezi olmayan, dağıtılmış bir ortamda eşler arasında bilgi alışverişi için bir mimari. REST, farklı bilgisayarlardaki programların bir işletim sistemi veya platformundan bağımsız olarak iletişim kurmasına olanak tanır. Bir hizmet, Tekdüzen Kaynak Bulucusu 'na (URL) bir Köprü Metni Aktarım Protokolü (HTTP) isteği gönderebilir ve verileri geri alabilir.

<a name="route"></a>**Route**: iki veya daha fazla konum arasındaki yol, yol boyunca işaret noktaları için yönergeler gibi ek bilgiler de içerebilir.

<a name="requests-per-second-rps"></a>**Saniye başına istek (RPS)**: bkz. [saniyedeki sorgu sayısı (QPS)](#queries-per-second-qps). 

<a name="rss"></a>**RSS**: kaynağa bağlı olarak, gerçekten basit dağıtım, Resource Description Framework (RDF) Site Özeti veya zengin site özeti için kısaltma. Farklı Web siteleri arasında içerik paylaşmak için basit, yapılandırılmış bir XML biçimi. RSS belgeleri yazar, tarih, başlık, kısa bir açıklama ve köprü metni bağlantısı gibi anahtar meta veri öğelerini içerir. Bu bilgiler, bir kullanıcının (veya bir RSS yayımcı hizmeti) hangi malzemelere daha fazla araştırma olduğuna karar vermenize yardımcı olur.

## <a name="s"></a>S

<a name="satellite-imagery"></a>**Uydu canlandırın**: düzler ve uydular tarafından yakalanan, düz aşağı işaret eden canlandırın.

<a name="secondary-key"></a>**İkincil anahtar**: Azure Maps paylaşılan anahtar kimlik doğrulaması için belirtilen iki abonelik anahtarının ikincisi. Bkz. [paylaşılan anahtar kimlik doğrulaması](#shared-key-authentication).

<a name="shapefile-shp"></a>**Shapefile (shp)**: Esri şekil dosyası olarak da bilinen coğrafi özelliklerin konumunu, şeklini ve özniteliklerini depolamak için bir vektör veri depolama biçimidir. Bir şekil dosyası bir dizi ilgili dosya içinde depolanır.

<a name="shared-key-authentication"></a>**Paylaşılan anahtar kimlik doğrulaması**: paylaşılan anahtar kimlik doğrulaması, her Azure Maps Isteğiyle Azure Maps hesabı tarafından oluşturulan anahtarları geçirmeyi kullanır. Bu anahtarlara genellikle abonelik anahtarları denir. Anahtarların güvenlik için düzenli olarak yeniden oluşturulması önerilir. Diğerini yeniden oluştururken bir anahtar kullanarak devam edebilmeniz için iki anahtar sağlanır. Anahtarlarınızı yeniden oluşturduğunuzda, yeni anahtarları kullanmak için bu hesaba erişen tüm uygulamaları güncelleştirmeniz gerekir. Azure haritalar kimlik doğrulaması hakkında daha fazla bilgi edinmek için bkz. Azure Maps [ve Azure AD](azure-maps-authentication.md) ve [kimlik doğrulamasını Azure haritalar 'da yönetme](how-to-manage-authentication.md).

<a name="software-development-kit-sdk"></a>**Yazılım geliştirme seti (SDK)**: bir geliştiricinin uygulama derlemek IÇIN bir API kullanmasına yardımcı olmak için bir belge, örnek kod ve örnek uygulama koleksiyonu.

<a name="spherical-mercator-projection"></a>**Küresel Mercator projeksiyonu**: bkz. [Web Mercator](#web-mercator). 

<a name="spatial-query"></a>**Uzamsal sorgu**: uzamsal bir işlem gerçekleştiren bir hizmete yapılan istek. Bir radyal arama veya bir rota arama hakkında.

<a name="spatial-reference"></a>**Uzamsal başvuru**: coğrafi varlıkları tam olarak bulmak için kullanılan koordinat tabanlı yerel, bölgesel veya küresel bir sistem. Harita koordinatlarını gerçek dünyadaki konumlara ilişkilendirmek için kullanılan koordinat sistemini tanımlar. Uzamsal başvurular, farklı katmanlardan veya kaynaklardan uzamsal verilerin doğru şekilde görüntülenmesi veya çözümlenmesi için tümleştirilebilmesini sağlamaktır. Azure Maps, giriş geometrisi verileri için [EPSG: 3857](https://epsg.io/3857) koordinat başvuru sistemi ve wgs 84 kullanır.

<a name="sql-spatial"></a>**SQL uzamsal**: SQL Azure ve SQL Server 2008 ve üzeri sürümlerde yerleşik olan uzamsal işlevselliğe başvurur. Bu uzamsal işlevsellik, SQL Server bağımsız olarak kullanılabilecek bir .NET kitaplığı olarak da kullanılabilir. Daha fazla bilgi için bkz. [uzamsal veriler (SQL Server) belgeleri](/sql/relational-databases/spatial/spatial-data-sql-server) .

<a name="subscription-key"></a>**Abonelik anahtarı**: bkz. [paylaşılan anahtar kimlik doğrulaması](#shared-key-authentication).

<a name="synchronous-request"></a>**Zaman uyumlu istek**: bir http isteği bir bağlantı açar ve yanıt bekler. Tarayıcılar, bir sayfadan yapılabilecek eşzamanlı HTTP isteklerinin sayısını sınırlar. Aynı anda birden çok uzun süre çalışan zaman uyumlu istek yapılırsa, bu sınıra ulaşılırsa. Diğer isteklerden biri tamamlanana kadar istekler gecikecek.

## <a name="t"></a>T

<a name="telematics"></a>**Telematik**: uzak nesnelerde efekt denetimi ile birlikte iletişim cihazları aracılığıyla bilgi gönderme, alma ve depolama. 

<a name="temporal-data"></a>Zamana bağlı **veri**: özellikle saatlere veya tarihlere başvuran veriler. Zamana bağlı veriler, şimşek gibi ayrı olaylara başvurabilir. nesneleri taşıma; Örneğin, traıns; ya da trafik algılayıcılarından sayımlar gibi yinelenen gözlemler.

<a name="terrain"></a>**Teryağmur**: kumlu termi veya dağımız terdiği gibi belirli bir özelliğe sahip olan bir alan.

<a name="thematic-maps"></a>**Tek tek haritalar**: bir Özet eşleme, bir coğrafi alanla ilgili bir temayı yansıtmak için yapılan basit bir eşlemedir. Bu tür bir harita için yaygın bir senaryo, bazı veri ölçümlerine bağlı olarak ülkeler/bölgeler gibi yönetim bölgelerini renkledir.

<a name="tile-layer"></a>**Döşeme katmanı**: harita kutucukları (dikdörtgen bölümler) sürekli bir katmana ekleyerek bir katman gösterilir. Kutucuklar, raster görüntü kutucukları veya vektör kutucukları olabilir. Raster kutucuk katmanları genellikle zamandan önce işlenir ve bir sunucuda görüntü olarak depolanır. Raster döşeme katmanları, büyük bir depolama alanı kullanabilir. Vektör döşeme katmanları, istemci uygulaması içinde gerçek zamanlı olarak işlenir. Bu nedenle, sunucu tarafı depolama gereksinimleri vektör döşeme katmanları için küçüktür.

<a name="time-zone"></a>**Saat dilimi**: yasal, ticari ve sosyal amaçlar için tek bir standart zaman sunan dünya bölgesi. Zaman dilimleri, ülkelerin/bölgelerin ve alt bölümlerinin sınırlarını takip etmek için eğilimlidir.

<a name="transaction"></a>**İşlem**: Azure Maps; bir işlem lisanslama modeli kullanır;

- Her 15 eşleme veya istenen trafik kutucukları için bir işlem oluşturulur.
- Azure haritalar 'daki hizmetlerden birine yönelik her API çağrısı için bir işlem oluşturulur. Arama ve yönlendirme, Azure haritalar hizmeti örnekleridir.

<a name="transformation"></a>**Dönüştürme**: farklı coğrafi koordinat sistemleri arasında veri dönüştürme işlemi. Örneğin, Birleşik Krallık 'ta yakalanan ve OSGB 1936 coğrafi koordinat sistemine bağlı bazı verileriniz olabilir. Azure Maps, WGS84 'in [EPSG: 3857](https://epsg.io/3857) koordinat başvuru sistemi türevini kullanır. Verileri doğru şekilde göstermek için, koordinatlarının bir sistemden diğerine dönüştürülmesi gerekir.

<a name="traveling-salesmen-problem-tsp"></a>**Seyahat satış sorunu (TSP)**: bir satışçının bir diziyi ziyaret etmek için en verimli yolu bulması ve başlangıç konumuna geri dönmesi gereken bir Hamiltonian devresi sorunu.  

<a name="trilateration"></a>**Üç nokta: iki** diğer noktaya göre, her üç nokta arasındaki uzaklıkları ölçerek dünya yüzeyine bir noktanın konumunu belirleme işlemi.

<a name="turn-by-turn-navigation"></a>Aç **-Kapat: bir** sonraki maneuver 'e yaklaşırsa, bir yolun her adımı için yol yönergeleri sağlayan bir uygulamadır.

## <a name="v"></a>V

<a name="vector-data"></a>**Vektör verileri**: noktaları, çizgileri veya poligonları temsil eden koordinat tabanlı verileri koordine edin.

<a name="vector-tile"></a>**Vektör kutucuğu**: harita denetimiyle aynı kutucuk sistemini kullanarak Jeo-uzamsal vektör verilerini depolamaya yönelik açık veri belirtimi. Ayrıca bkz. [döşeme katmanı](#tile-layer).

<a name="vehicle-routing-problem-vrp"></a>**Araç yönlendirme sorunu (VRP)**: bir tek bir araçlar için sıralı yollar kümesinin, kısıtlama kümesi olarak dikkate alınması sırasında hesaplandığı bir sorun sınıfı. Bu kısıtlamalar teslim süresi pencerelerini, birden çok rota kapamasını ve seyahat süresi kısıtlamalarını içerebilir.

<a name="voronoi-diagram"></a>**Voronoı diyagramı**: bir geometrik nesneler kümesini, genellikle nokta özelliklerini çevreleyen alanlara veya hücrelere boşluk bölümü. Bu hücreler veya çokgenler, Delaunay üçgenler ölçütlerini karşılamalıdır. Bir alandaki tüm konumlar, küme içindeki diğer herhangi bir nesneyle çevrelediği nesneye daha yakındır. Voronoı diyagramları genellikle coğrafi özellikler etrafında etki alanlarının sayısını belirtmek için kullanılır. 

## <a name="w"></a>W

<a name="waypoint"></a>**Güzergah noktası**: bir güzergah noktası, ' nin, ve ' de gezinme amaçları için kullanılan, boylam ve Enlem tarafından tanımlanan belirli bir coğrafi konumdur. Genellikle, birisinin yol üzerinden gittiği bir noktayı temsil etmek için kullanılır.

<a name="waypoint-optimization"></a>**Waypoint iyileştirmesi**: bir dizi geçiş süresini en aza indirmek için, belirtilen tüm geçiş noktalarından geçiş yapmak için gereken uzaklığı veya mesafeyi yeniden sıralama işlemi. En iyi duruma getirme karmaşıklığına bağlı olarak, bu iyileştirme genellikle [seyahat satış sorunu](#traveling-salesmen-problem-tsp) veya [araç yönlendirme sorunu](#vehicle-routing-problem-vrp)olarak adlandırılır.

<a name="web-map-service-wms"></a>**Web harita hizmeti (WMS)**: WMS, görüntü tabanlı eşleme hizmetlerini tanımlayan bir açık coğrafi konsorsiyum (ogc) standardıdır. WMS Hizmetleri, bir harita içindeki belirli alanlara istek üzerine harita görüntüleri sağlar. Görüntüler, önceden işlenmiş symbology içerir ve hizmet tarafından tanımlanmışsa birkaç adlandırılmış stilden birinde oluşturulabilir.

<a name="web-mercator"></a>**Web Mercator**: küresel Mercator projeksiyonu olarak da bilinir. Bu, birincil olarak Web tabanlı eşleme programlarında kullanılan Mercator projeksiyonun hafif bir türevi. Küçük ölçekli haritalar için kullanılan standart Mercator projeksiyonu ile aynı formülleri kullanır. Ancak, Web Mercator her ölçekteki küresel formülleri kullanır, ancak büyük ölçekli Mercator haritaları normalde projeksiyonun ellipsoidal formunu kullanır. Tutarsızlık küresel ölçekte çok az olsa da, yerel alanların eşlemelerinin aynı ölçekte gerçek ellipsoidal Mercator haritalarından biraz farklı olmasına neden olur.

<a name="wgs84"></a>**WGS84**: uzamsal koordinatları eşlemenin yüzeyindeki konumlara ilişkilendirmek için kullanılan sabitler kümesi. WGS84 Datum, çoğu çevrimiçi eşleme sağlayıcısı ve GPS cihazı tarafından kullanılan standart bir standarttır. Azure Maps, WGS84 'in [EPSG: 3857](https://epsg.io/3857) koordinat başvuru sistemi türevini kullanır.

## <a name="z"></a>Z

<a name="z-coordinate"></a>**Z koordinatı**: bkz. [Yükseklik](#altitude). 

<a name="zip-code"></a>**Posta kodu**: [posta kodunu](#postal-code)görüntüleyin.

<a name="Zoom level"></a>**Yakınlaştırma düzeyi**: ayrıntı düzeyini ve haritanın ne kadarının görünür olduğunu belirtir. 0 düzeyinde tüm yollar yakınlaştırıldığında, tam dünya haritası genellikle görünür olur. Ancak haritada ülke/bölge adları, kenarlıklar ve okyanus adları gibi sınırlı ayrıntılar gösterilir. 17. düzeye yaklaşarak haritada, ayrıntılı yol bilgileri olan birkaç şehir blobunun bir alanı görüntülenir. Azure haritalar 'da en yüksek yakınlaştırma düzeyi 22 ' dir. Daha fazla bilgi için [yakınlaştırma düzeyleri ve kutucuk Kılavuzu](zoom-levels-and-tile-grid.md) belgelerine bakın.