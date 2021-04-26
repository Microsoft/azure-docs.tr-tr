---
title: Bilinen sorunları Azure Media Player
description: Geçerli yayında aşağıdaki bilinen sorunlar var.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/05/2021
ms.openlocfilehash: 9d2289f1cec0d3679b0e9400f75c75cf9fece60b
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448840"
---
# <a name="known-issues"></a>Bilinen Sorunlar #

Geçerli yayında aşağıdaki bilinen sorunlar var:

## <a name="azure-media-player"></a>Azure Media Player ##

- Yanlış yapılandırılmış kodlayıcılar kayıttan yürütme ile ilgili sorunlara neden olabilir
- 2 ^ 53 ' ten büyük zaman damgalarına sahip akışlar kayıttan yürütme sorunlarına sahip olabilir.
  - Risk azaltma: 90-kHz video ve 44,1-kHz ses zaman dilimlerini kullanma
- Mobil cihazlarda Kullanıcı etkileşimi olmadan otomatik olarak oynatma yok. Platform tarafından engelleniyor.
- Neredeyse süreksizlik arama, kayıttan yürütme hatasına neden olabilir.
- Büyük sunuların indirilmesi, Kullanıcı arabiriminin kilitlenmesine neden olabilir.
- Sunum sona erdikten sonra otomatik olarak aynı kaynağı yeniden oynatabilir.
  - Bir kaynağı sonlandırdıktan sonra yeniden oynatmak için, kaynağı tekrar ayarlamanız gerekir.
- Boş bildirimler Player ile ilgili sorunlara neden olabilir.
  - Bu sorun, canlı bir akış başlatıldığında ve bildirimde yeterli sayıda öbek bulunamadığında ortaya çıkabilir.
- Kayıttan yürütme konumu UI SeekBar dışında olabilir.
- Olay sıralaması tüm Techs 'lerde tutarlı değil.
- Arabelleğe alınan özellik Techs genelinde tutarlı değil.
- "Object özelliği veya ' setControls ' metodunu desteklemediğinden, nativeControlsForTouch yanlış olmalıdır (varsayılan)
- Posterler artık mutlak URL 'ler olmalıdır
- Cihazın yüksek karşıtlık modu kullanılırken küçük Aesthetic Characteristics sorunları Kullanıcı arabiriminde oluşabilir
- Tam olarak çözülen dizede "%" veya "+" içeren URL 'Ler, kaynağı ayarlarken sorunlarla karşılaşabilir

## <a name="ad-insertion"></a>Ad ekleme ##

- Tarayıcıya bir ad engelleyicisinin yüklendiği durumlarda reklamları (isteğe bağlı veya canlı) ekleme sorunları olabilir
- Mobil cihazlarda reklamları kayıttan yürütme sorunları olabilir.
- MP4 Mıdroll reklamları şu anda Azure Media Player tarafından desteklenmemektedir.

## <a name="azurehtml5js"></a>AzureHtml5JS ##

- Canlı içeriğin DVR penceresinde, içerik tamamlandığında zaman çizelgesi alana ulaşılıncaya veya sununun sonuna ulaşana kadar büyümeye devam edecektir.
- MSE 'nin etkin olduğu canlı sunularda bazı sorunlar vardır

- Yalnızca ses olan varlıklar AzureHtml5JS Tech aracılığıyla kayıttan yürütülmeyecektir.
  - Varlıkları ses olmadan oynatmak isterseniz, [Azure Media Services Explorer aracını](https://aka.ms/amse) kullanarak boş ses ekleyerek bunu yapabilirsiniz
  - Sessiz ses ekleme yönergeleri [burada](../previous/media-services-advanced-encoding-with-mes.md#silent_audio) bulunabilir

## <a name="flash"></a>In ##

- Adobe 'un önbelleğe alma mantığındaki bir hata nedeniyle, AES içeriği Flash Version 30.0.0.134 'da kayıttan çalınmıyor. Adobe sorunu düzeltti ve 30.0.0.154 içinde yayımladı
- Teknik ve http arızaları (404 ağ zaman aşımları gibi), Player diğer Techs 'den kurtarmak için daha uzun sürer.
- FlashSS Tech ile video alanına tıkladığınızda Player çalınmaz/duraklatılır.
- Kullanıcı Flash yüklüyse ancak sitede yükleme izni vermezse, sonsuz bir dönme meydana gelebilir. Bunun nedeni, eklentinin, eklentinin yüklü ve kullanılabilir olduğunu ve eklentinin içeriği çalıştırmakta olduğunu düşündüğü bir eklentidir. JavaScript kodu gönderildi, ancak tarayıcı ayarları, Kullanıcı eklentiye izin verme isteğini kabul edene kadar eklentinin yürütülmesini engelledi. Bu, tüm tarayıcılarda gerçekleşebilir.  

## <a name="silverlight"></a>Silverlight ##

- Eksik Özellikler
- Teknik ve http arızaları (404 ağ zaman aşımları gibi), Player diğer Techs 'den kurtarmak için daha uzun sürer.
- Silverlight ile Mac 'te yürütülen Safari ve Firefox `"http://` `https://` , kaynak için açıkça tanımlamayı gerektirir.
- Bu teknik için bir API eksikse genellikle null döndürülür.
- Kullanıcı Flash yüklüyse ancak sitede yükleme izni vermezse, sonsuz bir dönme meydana gelebilir. Bunun nedeni, eklentinin, eklentinin yüklü ve kullanılabilir olduğunu ve eklentinin içeriği çalıştırmakta olduğunu düşündüğü bir eklentidir. JavaScript kodu gönderildi, ancak tarayıcı ayarları, Kullanıcı eklentiye izin verme isteğini kabul edene kadar eklentinin yürütülmesini engelledi. Bu, tüm tarayıcılarda gerçekleşebilir.  

## <a name="native-html5"></a>Yerel HTML5 ##

- HTML5 Tech yalnızca ilk küme kaynağı için canplaythrough olay gönderiyor.
- Bu teknik yalnızca tarayıcının uygulandığını destekler.  Bu teknik, bazı özellikler eksik olabilir.  
- Bu teknik için bir API eksikse genellikle null döndürülür.
- Bu teknoloji üzerinde açıklamalı alt yazılar ve alt yazılar ile ilgili sorunlar var. Bu teknoloji, kullanılabilir veya kullanılamıyor olabilir.
- HLS/HTML5 teknik senaryosunda sınırlı bant genişliğine sahip olmak, video olmadan ses çalmaya neden olur.

### <a name="microsoft"></a>Microsoft ###

- IE8 oynatma, ECMAScript 5 ile uyumsuzluk nedeniyle şu anda çalışmıyor
- IE ve bazı Edge sürümlerinde, tam ekran, düğmeye sekmeyle göre ve seçilerek ya da F/f kısayol tuşu kullanılarak girilemez.

## <a name="google"></a>Google ##

- Aynı bildirimde bulunan birden çok kodlama profili Chrome 'da bazı kayıttan yürütme sorunları yaşıyor ve önerilmez.
- Chrome, AzureHtml5JS ile Back-AAC 'yi yürütemiyor. [Hata İzleyicisi](https://bugs.chromium.org/p/chromium/issues/detail?id=534301)hakkındaki ayrıntıları izleyin.
- Chrome V58 itibariyle wıdevine içeriği https://protokolü aracılığıyla yüklenmelidir/çalınmalıdır, aksi halde kayıttan yürütme başarısız olur.

## <a name="mozilla"></a>Mozilla ##

- Ses akışı anahtarı, AzureHtml5JS kullanırken aynı codec bileşeninin özel verilerine sahip olmak için ses akışları gerektirir. Firefox platformu için bu gereklidir.

## <a name="apple"></a>Apple ##

- Mac üzerinde Safari, genellikle "güç tasarrufu yapmak için eklentileri Durdur" ayarıyla birlikte varsayılan olarak güç tasarrufu modunu etkinleştirerek, Kullanıcı tarafından kullanım dışı olduğuna inandıklarında Flash ve Silverlight gibi eklentileri engeller. Bu blok, eklentinin var olan yalnızca yeteneklerini engellemez. Varsayılan techOrder verildiğinde, bu durum kayıttan oynatılmaya çalışıldığında sorunlara neden olabilir
  - Hafifletme 1: video oynatıcı ' ön ve Merkez ' ise (bir 3000 x 3000 piksel sınırının içinde, belgenin sol üst köşesinden başlayarak), yine de oynamalıdır.
  - Risk azaltma 2: Safari için techOrder öğesini ["azureHtml5JS", "HTML5"] olacak şekilde değiştirin. Bu hafifletme, FlashSS Tech ' de kullanılabilen tüm özellikleri elde etmez.
- Silverlight aracılığıyla PlayReady içeriği Safari 'de kayıttan yürütme sorunları yaşıyor olabilir.
- AES ve kısıtlı belirteç içeriği iOS ve daha eski Android cihazları kullanılarak kayıttan yürütülmez.
  - Bu senaryoya ulaşmak için hizmetinize bir ara sunucu eklenmelidir.
- İOS telefonu için varsayılan dış görünüm aracılığıyla gösterilir.
- iPhone oynatma, her zaman yerel oynatıcı tam ekran üzerinden gerçekleşir.
  - Resim yazıları dahil özellikler, bu satır içi olmayan Kayıttan yürütmede kalıcı olmayabilir.
- Canlı sunum sona erdiğinde, iOS cihazları sunuyu düzgün bir şekilde sonlandırmaz.
- iOS, DVR özelliklerine izin vermez.
- iOS ses akışı anahtarı yalnızca iOS yerel oynatıcı kullanıcı arabiriminden yapılabilir ve aynı codec bileşeninin özel verilerine sahip olmak için ses akışları gerekir
- Safari 'nin eski sürümlerinde canlı akışlar kayıttan yürütme sorunları olabilir.

## <a name="older-android"></a>Daha eski Android ##

- AES ve kısıtlı belirteç içeriği iOS ve daha eski Android cihazları kullanılarak kayıttan yürütülmez.
  - Bu senaryoya ulaşmak için hizmetinize bir ara sunucu eklenmelidir.

## <a name="next-steps"></a>Sonraki adımlar ##

- [Hızlı başlangıç Azure Media Player](azure-media-player-quickstart.md)
