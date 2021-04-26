---
title: Azure 'da paylaşılan görüntülerle ilgili sorunları giderme
description: Paylaşılan görüntü galerileriyle ilgili sorunları nasıl giderebileceğinizi öğrenin.
author: olayemio
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: troubleshooting
ms.workload: infrastructure
ms.date: 10/27/2020
ms.author: olayemio
ms.reviewer: cynthn
ms.openlocfilehash: 9652e940674ec7580b006cd38df2a7d17014f939
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107309994"
---
# <a name="troubleshoot-shared-image-galleries-in-azure"></a>Azure 'da paylaşılan görüntü galerilerine sorun giderme

Paylaşılan görüntü galerileri, görüntü tanımları ve görüntü sürümleri üzerinde herhangi bir işlem gerçekleştirirken sorun yaşıyorsanız, hata ayıklama modunda başarısız olan komutu yeniden çalıştırın. `--debug`Anahtarı Azure CLI ile geçirerek PowerShell ile anahtar hata ayıklama modunu etkinleştirirsiniz `-Debug` . Hatayı bulduktan sonra sorunu gidermek için bu makaleyi izleyin.


## <a name="creating-or-modifying-a-gallery"></a>Galeri oluşturma veya değiştirme ##

**İleti**: *Galeri adı geçersiz. İzin verilen karakterler, en fazla 80 karakter uzunluğunda olan Ingilizce alfasayısal karakter, alt çizgi ve nokta ile kullanılabilir. Tireler dahil diğer tüm özel karakterlere izin verilmez.*  
**Neden**: galerinin adı, adlandırma gereksinimlerini karşılamıyor.  
**Geçici çözüm**: aşağıdaki koşullara uyan bir ad seçin: 
- 80 karakter sınırına sahiptir
- Yalnızca Ingilizce harfler, rakamlar, alt çizgiler ve noktalar içerir
- Ingilizce harfler veya sayılarla başlar ve biter

**İleti**: *' gallername ' varlık adı, kendi doğrulama kuralına göre geçersiz: ^ [^ \_ \w] [\w-. \_ ] {0,79} (? <! [-.]) $.*  
**Neden**: Galeri adı adlandırma gereksinimlerini karşılamıyor.  
**Geçici çözüm**: Galeri için aşağıdaki koşullara uyan bir ad seçin: 
- 80 karakter sınırına sahiptir
- Yalnızca Ingilizce harfler, rakamlar, alt çizgiler ve noktalar içerir
- Ingilizce harfler veya sayılarla başlar ve biter

**İleti**: *belirtilen kaynak adı <gallername \> Şu geçersiz karakterleri içeriyor: <karakteri \> . Ad şu karakterlerle bitemez: <karakteri \>*  
**Neden**: galerinin adı nokta veya alt çizgi ile sona erer.  
**Geçici çözüm**: Galeri için aşağıdaki koşullara uyan bir ad seçin: 
- 80 karakter sınırına sahiptir
- Yalnızca Ingilizce harfler, rakamlar, alt çizgiler ve noktalar içerir
- Ingilizce harfler veya sayılarla başlar ve biter

**İleti**: *sağlanan konum <bölgesi \> ' Microsoft. COMPUTE/galeriler ' kaynak türü için kullanılamıyor. Kaynak türü için kullanılabilir bölgelerin listesi..* .  
**Neden**: Galeri için belirtilen bölge yanlış veya bir erişim isteği gerektiriyor.  
**Geçici çözüm**: bölge adının doğru olup olmadığını denetleyin. Bölge adı doğru ise, bölge için [bir erişim isteği](/troubleshoot/azure/general/region-access-request-process) gönderir.

**İleti**: *iç içe geçmiş kaynaklar silinmeden önce kaynak silinemiyor.*  
**Neden**: var olan en az bir görüntü tanımı içeren bir galeriyi silmeye çalıştınız. Bir galeri silinmeden önce boş olmalıdır.  
**Geçici çözüm**: galerinin içindeki tüm görüntü tanımlarını silin ve sonra galeriyi silmeye devam edin. Görüntü tanımı görüntü sürümlerini içeriyorsa, görüntü tanımlarını silmeden önce görüntü sürümlerini silmeniz gerekir.

**İleti**: *' <gallername ' Galeri adı \> ' ' aboneliği içinde benzersiz değil <subscriptionID> . Lütfen başka bir galeri adı seçin.*  
**Neden**: aynı adlı bir galeriniz var ve aynı ada sahip başka bir galeri oluşturmaya çalıştınız.  
**Geçici çözüm**: Galeri için farklı bir ad seçin.

**İleti**: *<gallername kaynak \> \_ \> <resourceGroup kaynak grubundaki <Region 1 konumunda zaten var \> . <bölge 2 konumunda aynı ada sahip bir kaynak oluşturulamıyor \_ \> . Lütfen yeni bir kaynak adı seçin.*  
**Neden**: aynı adlı bir galeriniz var ve aynı ada sahip başka bir galeri oluşturmaya çalıştınız.  
**Geçici çözüm**: Galeri için farklı bir ad seçin.

## <a name="creating-or-modifying-image-definitions"></a>Görüntü tanımları oluşturma veya değiştirme ##

**İleti**: *' gallerımage. Properties. <Property ' özelliğinin değiştirilmesine \> izin verilmez.*  
**Neden**: işletim sistemi türü, işletim sistemi durumu, Hyper-V oluşturma, teklif, yayımcı veya SKU 'yu değiştirmeye çalıştınız. Bu özelliklerden herhangi birini değiştirmeye izin verilmez.  
**Geçici çözüm**: bunun yerine yeni bir görüntü tanımı oluşturun.

**İleti**: *<Gallername/ımagedefinitionname kaynak \> \_ \> <resourceGroup kaynak grubundaki <Region 1 konumunda zaten var \> . <bölge 2 konumunda aynı ada sahip bir kaynak oluşturulamıyor \_ \> . Lütfen yeni bir kaynak adı seçin.*  
**Neden**: aynı Galeri ve kaynak grubunda aynı ada sahip bir görüntü tanımınız var. Aynı ada ve aynı galeride ancak farklı bir bölgede bulunan başka bir görüntü tanımı oluşturmaya çalıştınız.  
**Geçici çözüm**: görüntü tanımı için farklı bir ad kullanın veya görüntü tanımını farklı bir galeri veya kaynak grubuna yerleştirin.

**İleti**: *belirtilen kaynak adı <gallername \> /<ımagedefinitionname \> Şu geçersiz sondaki karakterleri içeriyor: <karakter \> . Ad şu karakterlerle bitemez: <karakteri \>*  
**Neden**: <ımagedefinitionname \> adı nokta veya alt çizgi ile bitiyor.  
**Geçici çözüm**: aşağıdaki koşullara uyan görüntü tanımı için bir ad seçin: 
- 80 karakter sınırına sahiptir
- Yalnızca Ingilizce harf, sayı, alt çizgi, kısa çizgi ve nokta içerir
- Ingilizce harfler veya sayılarla başlar ve biter.

**İleti**: *<ımagedefinitionname varlık adı, \> kendi doğrulama kuralına göre geçersiz: ^ [^ \_ \\ W] [ \\ W-. \_ ] {0,79} (? <! [-.]) $"*  
**Neden**: <ımagedefinitionname \> adı nokta veya alt çizgi ile bitiyor.  
**Geçici çözüm**: aşağıdaki koşullara uyan görüntü tanımı için bir ad seçin: 
- 80 karakter sınırına sahiptir
- Yalnızca Ingilizce harf, sayı, alt çizgi, kısa çizgi ve nokta içerir
- Ingilizce harfler veya sayılarla başlar ve biter

**İleti**: bir *varlık adı gallerımage. Properties. Identifier. <özelliği \> geçerli değil. Boş olamaz. İzin verilen karakterler büyük veya küçük harfler, rakamlar, kısa çizgi (-), nokta (.), alt çizgi ( \_ ). Adların nokta (.) ile bitme izni yok. Adın uzunluğu <sayı \> karakteri aşamaz.*  
**Neden**: Yayımcı, TEKLIF veya SKU değeri, adlandırma gereksinimlerini karşılamıyor.  
**Geçici çözüm**: aşağıdaki koşullara uyan bir değer seçin: 
- , Teklif ve SKU için 128 karakterlik bir sınırlamaya veya 64 karakterlik sınıra sahiptir
- Yalnızca Ingilizce harf, sayı, kısa çizgi, alt çizgi ve nokta içerir
- Bir noktayla bitmez

**İleti**: *iç içe kaynak üzerinde istenen işlem gerçekleştirilemiyor. Gallermadı <üst kaynak \> bulunamadı.*  
**Neden**: \> geçerli abonelikte ve kaynak grubunda <gallername adlı bir galeri yok.  
**Geçici çözüm**: Galeri, abonelik ve kaynak grubu adlarının doğru olup olmadığını denetleyin. Aksi takdirde, <Gallername adlı yeni bir galeri oluşturun \> .

**İleti**: *sağlanan konum <bölgesi \> ' Microsoft. COMPUTE/galeriler ' kaynak türü için kullanılamıyor. Kaynak türü için kullanılabilir bölgelerin listesi..* .  
**Neden**: <bölge \> adı yanlış veya erişim isteği gerektiriyor.  
**Geçici çözüm**: bölge adının doğru yazıldığından emin olun. Hangi bölgeleri erişiminizin olduğunu görmek için bu komutu çalıştırabilirsiniz. Bölge listede yoksa, [erişim isteği](/troubleshoot/azure/general/region-access-request-process)gönderebilirsiniz.

**İleti**: *değer: <değer seri hale getirilemiyor \> : ' ISO-8601 '., ISO8601Error: ISO 8601 zaman göstergesi 't ' eksik. DateTime dize <değeri \> ayrıştırılamıyor*  
**Neden**: özelliğe girilen değer doğru şekilde bir tarih olarak biçimlendirilmedi.  
**Geçici çözüm**: yyyy-aa-gg, YYYY-AA-Gg't'hh: mm: sszzz veya [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)-geçerli biçiminde bir tarih girin.

**İleti**: *dize DateTimeOffset: <Value olarak dönüştürülemedi \> . ' Properties. <Property \> ' yolu*  
**Neden**: özelliğe girilen değer doğru şekilde bir tarih olarak biçimlendirilmedi.  
**Geçici çözüm**: yyyy-aa-gg, YYYY-AA-Gg't'hh: mm: sszzz veya [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)-geçerli biçiminde bir tarih girin.

**İleti**: *endofyaşam tarihi gelecek bir tarih olarak ayarlanmalıdır.*  
**Neden**: yaşam bitiş tarihi özelliği, bugünün tarihinden sonra gelen bir tarih olarak düzgün biçimlendirilmedi.  
**Geçici çözüm**: yyyy-aa-gg, YYYY-AA-Gg't'hh: mm: sszzz veya [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)-geçerli biçiminde bir tarih girin.

**İleti**: *argument--<özelliği \> : geçersiz int değeri: <değeri \>*  
**Neden**: <özellik \> yalnızca tamsayı değerlerini kabul ediyor ve <değeri \> bir tamsayı değil.  
**Geçici çözüm**: bir tamsayı değeri seçin.

**İleti**: *<özelliğinin en küçük değeri \> <özelliğinin en büyük değerinden büyük olmamalıdır \> .*  
**Neden**: <özelliği için belirtilen minimum değer \> <özelliği için verilen en büyük değerden daha yüksek \> .  
**Geçici çözüm**: değerleri en düşük değerden küçük veya en büyük değere eşit olacak şekilde değiştirin.

**İleti**: *galeri görüntüsü: <ımagedefinitionname \> tarafından tanımlanan (Yayımcı: <yayımcı \> , teklif: <TEKLIF \> , SKU: <SKU \> ) zaten var. Farklı bir yayımcı, teklif, SKU birleşimi seçin.*  
**Neden**: aynı galeride var olan bir görüntü tanımı olarak aynı yayımcı, teklif ve SKU 'ya sahip yeni bir görüntü tanımı oluşturmaya çalıştınız.  
**Geçici çözüm**: bir galeri içinde tüm görüntü tanımlarının benzersiz bir yayımcı, TEKLIF ve SKU birleşimine sahip olması gerekir. Benzersiz bir bileşim seçin veya yeni bir galeri seçin ve görüntü tanımını yeniden oluşturun.

**İleti**: *iç içe geçmiş kaynaklar silinmeden önce kaynak silinemiyor.*  
**Neden**: görüntü sürümlerini içeren bir görüntü tanımını silmeye çalıştınız. Bir görüntü tanımının silinebilmesi için önce boş olması gerekir.  
**Geçici çözüm**: görüntü tanımının içindeki tüm görüntü sürümlerini silin ve ardından görüntü tanımını silmeye devam edin.

**İleti**: *parametre <özelliği bağlanamıyor \> . Değer <değeri \> <propertyType türüne dönüştürülemiyor \> . Tanımlayıcı adı <değeri \> geçerli bir Numaralandırıcı adı ile eşleşmiyor. Aşağıdaki Numaralandırıcı adlarından birini belirtip yeniden deneyin: <seçim \_ 1 \> , <seçimi \_ 2 \> ,.* ..  
**Neden**: özellikte olası değerler listesi bulunur ve <değeri \> bunlardan biri değil.  
**Geçici çözüm**: olası <seçim değerlerinden birini seçin \> .

**İleti**: *parametre <özelliği bağlanamıyor \> . Değer <değeri \> &quot; System. DateTime &quot; türüne dönüştürülemiyor*  
**Neden**: özelliğe girilen değer doğru şekilde bir tarih olarak biçimlendirilmedi.  
**Geçici çözüm**: yyyy-aa-gg, YYYY-AA-Gg't'hh: mm: sszzz veya [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)-geçerli biçiminde bir tarih girin.

**İleti**: *parametre <özelliği bağlanamıyor \> . Değer <değeri \> &quot; System. Int32 &quot; türüne dönüştürülemiyor*  
**Neden**: <özellik \> yalnızca tamsayı değerlerini kabul ediyor ve <değeri \> bir tamsayı değil.  
**Geçici çözüm**: bir tamsayı değeri seçin.

**İleti**: *ZRS depolama hesabı türü bu bölgede desteklenmiyor.*  
**Neden**: henüz desteklemeyen bir bölgede standart bölge yedekli depolama (ZRS) seçtiniz.  
**Geçici çözüm**: depolama hesabı türünü **Premium \_ LRS** veya **Standart \_ LRS** olarak değiştirin. ZRS önizlemesinin etkinleştirildiği bölgelerin en son [listesi](../storage/common/storage-redundancy.md#zone-redundant-storage) için belgelerimizi kontrol edin.

## <a name="creating-or-updating-image-versions"></a>Görüntü sürümlerini oluşturma veya güncelleştirme ##

**İleti**: *sağlanan konum <bölgesi \> ' Microsoft. COMPUTE/galeriler ' kaynak türü için kullanılamıyor. Kaynak türü için kullanılabilir bölgelerin listesi..* .  
**Neden**: <bölge \> adı yanlış veya erişim isteği gerektiriyor.  
**Geçici çözüm**: bölge adının doğru yazıldığından emin olun. Hangi bölgeleri erişiminizin olduğunu görmek için bu komutu çalıştırabilirsiniz. Bölge listede yoksa, [erişim isteği](/troubleshoot/azure/general/region-access-request-process)gönderebilirsiniz.

**İleti**: *iç içe kaynak üzerinde istenen işlem gerçekleştirilemiyor. Gallername/ımagedefinitionname <üst kaynak \> bulunamadı.*  
**Neden**: \> geçerli abonelikte ve kaynak grubunda <Gallername/ımagedefinitionname adlı bir galeri yok.  
**Geçici çözüm**: Galeri, abonelik ve kaynak grubu adlarının doğru olup olmadığını denetleyin. Aksi halde, \> belirtilen kaynak grubunda <gallername ve/veya <ımagedefinitionname adlı bir görüntü tanımına sahip yeni bir galeri oluşturun \> .

**İleti**: *parametre <özelliği bağlanamıyor \> . Değer <değeri \> &quot; System. DateTime &quot; türüne dönüştürülemiyor*  
**Neden**: özelliğe girilen değer doğru şekilde bir tarih olarak biçimlendirilmedi.  
**Geçici çözüm**: yyyy-aa-gg, YYYY-AA-Gg't'hh: mm: sszzz veya [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)-geçerli biçiminde bir tarih girin.

**İleti**: *parametre <özelliği bağlanamıyor \> . Değer <değeri \> &quot; System. Int32 &quot; türüne dönüştürülemiyor*  
**Neden**: <özellik \> yalnızca tamsayı değerlerini kabul ediyor ve <değeri \> bir tamsayı değil.  
**Geçici çözüm**: bir tamsayı değeri seçin.

**İleti**: *Galeri görüntüsü sürüm yayımlama profili bölgeleri <publishingregion \> , görüntü sürümü <\> sourceregilocation 'ın konumunu içermelidir*  
**Neden**: kaynak görüntünün konumu (<sourceRegion \> ) <publishingregion \> listesine eklenmelidir.  
**Geçici çözüm**: \> <publishingregion listesine <sourceregidahil edin \> .

**İleti**: *\> parametre <özelliğinin değer <değeri \> Aralık dışında. Değer <minValue \> ile <MaxValue \> (dahil) arasında olmalıdır.*  
**Neden**: <değeri \> <özelliği için olası değerler aralığının dışında \> .  
**Geçici çözüm**: dahil <minValue \> ve <MaxValue aralığı içinde bir değer seçin \> .

**İleti**: *kaynak <RESOURCEID \> bulunamadı. Lütfen kaynak olup olmadığını denetleyin ve oluşturulan Galeri görüntüsü sürümü ile aynı bölgede.*  
**Neden**: <RESOURCEID konumunda kaynak yok \> ya da <RESOURCEID konumundaki kaynak, \> oluşturulmakta olan Galeri görüntüsüyle aynı bölgede değil.  
**Geçici çözüm**: <RESOURCEID \> değerinin doğru olup olmadığını ve Galeri görüntüsü sürümünün kaynak bölgesinin <RESOURCEID değerinin bölgesiyle aynı olduğunu denetleyin \> .

**İleti**: *' gallerımageversion. Properties. storageprofile. <diskımage \> . Source.id ' özelliğinin değiştirilmesine izin verilmez.*  
**Neden**: bir galeri görüntüsü sürümünün kaynak kimliği, oluşturulduktan sonra değiştirilemez.  
**Geçici çözüm**: kaynak kimliğinin mevcut kaynak kimliğiyle aynı olduğundan emin olun, görüntü sürümünün sürüm numarasını değiştirin veya geçerli görüntü sürümünü silip yeniden deneyin.

**İleti**: *giriş veri disklerinde yinelenen LUN numaraları algılandı. LUN numarası her bir veri diski için benzersiz olmalıdır.*  
**Neden**: bir disk ve/veya disk anlık görüntülerinin listesini kullanarak bir görüntü sürümü oluştururken, iki veya daha fazla disk ya da disk anlık görüntüsü aynı LUN 'a sahiptir.  
**Geçici çözüm**: herhangi bir yinelenen LUN 'yi kaldırın veya değiştirin.

**İleti**: *yinelenen kaynak kimlikleri giriş disklerinde bulunur. Kaynak kimliği her disk için benzersiz olmalıdır.*  
**Neden**: bir disk ve/veya disk anlık görüntülerinin listesini kullanarak bir görüntü sürümü oluştururken, iki veya daha fazla disk ya da disk anlık görüntüsünün aynı kaynak kimliği vardır.  
**Geçici çözüm**: yinelenen disk kaynağı kimliklerini kaldırın veya değiştirin.

**İleti**: *\> ' Properties. Storageprofile. <diskımages. Source.id ' yolundaki <ResourceId Özellik kimliği \> geçersiz. '/Subscriptions/ <subscriptionID> ' veya '/Providers/ <resourceProviderNamespace> /' ile başlayan tam kaynak kimliği beklenmelidir.*  
**Neden**: <RESOURCEID \> değeri hatalı biçimlendirilmiş.  
**Geçici çözüm**: kaynak kimliğinin doğru olduğundan emin olun.

**İleti**: *kaynak kimliği: <RESOURCEID, \> yönetilen bir görüntü, sanal makine ya da başka bir galeri görüntüsü sürümü olmalıdır*  
**Neden**: <RESOURCEID \> değeri hatalı biçimlendirilmiş.  
**Geçici çözüm**: kaynak görüntü olarak bir VM, yönetilen görüntü veya Galeri görüntüsü sürümü KULLANıYORSANıZ, VM 'nin, yönetilen görüntünün veya Galeri görüntüsü sürümünün kaynak kimliğinin doğru olduğundan emin olun.

**İleti**: *kaynak kimliği: <RESOURCEID, \> yönetilen bir disk veya anlık görüntü olmalıdır.*  
**Neden**: <RESOURCEID \> değeri hatalı biçimlendirilmiş.  
**Geçici çözüm**: görüntü sürümü için diskler ve/veya disk anlık görüntülerini kullanıyorsanız, disklerin ve/veya disk anlık görüntülerinin kaynak kimliklerinin doğru olduğundan emin olun.

**İleti**: *\> üst Galeri görüntüsündeki (<OSSTATE 1) Işletim sistemi durumu \_ \> <Osstate 2 olmadığından, \_ \> Bu öğeden Galeri görüntüsü sürümü oluşturulamıyor: <RESOURCEID.*  
**Neden**: işletim sistemi durumu (Genelleştirilmiş veya özel) görüntü tanımında belirtilen işletim sistemi durumuyla eşleşmiyor.  
**Geçici çözüm**: <osstate 1 işletim sistemi durumuna sahıp bir VM 'yi temel alan bir kaynak seçin \_ \> ya da <osstate 2 ' ye göre VM 'ler için yeni bir görüntü tanımı oluşturun \_ \> .

**İleti**: *' <RESOURCEID ' kimliğine sahip kaynak, \> \_ \> üst Galeri görüntüsü hiper yönetici üretimi [' <v # \_ 2 \> '] dışında farklı bir hiper yönetici üretimi [' <V # 1 '] içeriyor*  
**Neden**: görüntü sürümünün hiper yönetici nesli, görüntü tanımında belirtilen hiper yönetici oluşturma ile eşleşmiyor. Görüntü tanımı işletim sistemi <V # \_ 1 \> ve görüntü sürümü işletim sistemi <v # 2 ' dir \_ \> .  
**Geçici çözüm**: görüntü tanımıyla aynı hiper yönetici oluşturmaya sahip bir kaynak seçin ya da görüntü sürümüyle aynı hiper yönetici oluşturmaya sahip yeni bir görüntü tanımı oluşturun/seçin.

**İleti**: *' <RESOURCEID ' kimliğine sahip kaynak, \> \_ \> üst Galeri görüntüsü işletim sistemi türü oluşturma [' <OSType \_ 2 \> '] farklı bir işletim sistemi türü [' <OSType 1 '] sahip.*  
**Neden**: görüntü sürümünün hiper yönetici nesli, görüntü tanımında belirtilen hiper yönetici oluşturma ile eşleşmiyor. Görüntü tanımı işletim sistemi <OsType \_ 1 \> ve görüntü sürümü işletim sistemi <OSType 2 ' dir \_ \> .  
**Geçici çözüm**: görüntü tanımıyla aynı işletim sistemine (Linux/Windows) sahip bir kaynak seçin ya da görüntü sürümü ile aynı işletim sistemi oluşturmaya sahip yeni bir görüntü tanımı oluşturun/seçin.

**İleti**: *kaynak sanal makine <RESOURCEID \> , kısa ömürlü bir işletim sistemi diski içeremez.*  
**Neden**: RESOURCEID <kaynak, \> kısa ömürlü bir işletim sistemi diski içeriyor. Paylaşılan görüntü Galerisi Şu anda kısa ömürlü işletim sistemi disklerini desteklemiyor.  
**Geçici çözüm**: kısa ömürlü işletim sistemi diski kullanmayan bir VM 'yi temel alan farklı bir kaynak seçin.

**İleti**: *kaynak sanal makine <RESOURCEID \> , \> bir UltraSSD hesabı türünde depolanan disk [' <DiskID '] içeremez.*  
**Neden**: disk <diskıd \> Ultra SSD bir disktir. Paylaşılan görüntü Galerisi Şu anda Ultra SSD diskleri desteklemiyor.  
**Geçici çözüm**: yalnızca Premium SSD, standart SSD ve/veya standart HDD yönetilen diskleri içeren bir kaynak kullanın.

**İleti**: *yönetilen disklerden kaynak sanal makine <RESOURCEID \> oluşturulması gerekir.*  
**Neden**: RESOURCEID <sanal makine \> yönetilmeyen diskler kullanıyor.  
**Geçici çözüm**: yalnızca Premium SSD, standart SSD ve/veya standart HDD yönetilen diskler IÇEREN bir VM 'yi temel alan bir kaynak kullanın.

**İleti**: *' <RESOURCEID ' kaynağında çok fazla istek yok \> . Lütfen yeniden denemeden önce kaynaktaki istek sayısını azaltın veya bir süre bekleyin.*  
**Neden**: çok fazla istek olduğundan, bu görüntü sürümünün kaynağı şu anda kısıtlanıyor.  
**Geçici çözüm**: görüntü sürümünü daha sonra oluşturmayı deneyin.

**İleti**: *' <Diskencryptionsetıd ' disk şifreleme kümesi, \> \> galeri kaynağı olarak aynı ' <SubscriptionID ' aboneliğinde olmalıdır.*  
**Neden**: disk şifreleme kümeleri yalnızca oluşturuldukları abonelikte ve bölgede kullanılabilir.  
**Geçici çözüm**: görüntü sürümüyle aynı abonelikte ve bölgede bir şifreleme kümesi oluşturun veya kullanın.

**İleti**: *' <RESOURCEID ' şifrelenmiş kaynağı, \> geçerli Galeri görüntüsü sürümü aboneliğinden (' <SubscriptionID 1 ') farklı bir abonelik \_ kimliğinde \> . \_ \> Galeri görüntüsü sürümünü oluşturmak için lütfen şifrelenmemiş bir kaynakla yeniden deneyin veya kaynağın ' <Subcriptionıd 2 ' aboneliğini kullanın.*  
**Neden**: paylaşılan görüntü Galerisi, kaynak görüntü şifrelenirse başka bir kaynak görüntüden başka bir abonelikte görüntü sürümlerini oluşturmayı desteklememektedir.  
**Geçici çözüm**: şifrelenmemiş bir kaynak kullanın veya kaynak ile aynı abonelikte görüntü sürümünü oluşturun.

**İleti**: *<Diskencryptionsetıd adlı disk şifreleme kümesi \> bulunamadı.*  
**Neden**: disk şifrelemesi yanlış olabilir.  
**Geçici çözüm**: disk şifreleme KÜMESININ kaynak kimliğinin doğru olduğundan emin olun.

**İleti**: *görüntü sürümü adı geçersiz. Görüntü sürümü adı büyük (int) olmalıdır. Küçük (int). Patch (int) biçimi, örneğin: 1.0.0, 2018.12.1 vb.*  
**Neden**: bir görüntü sürümü için geçerli biçim bir noktayla ayrılmış üç tamsayılardır. Görüntü sürümü adı geçerli biçime uymuyor.  
**Geçici çözüm**: Ana (int) biçimini izleyen bir görüntü sürümü adı kullanın. Küçük (int). Patch (int). Örneğin: 1.0.0. or 2018.12.1.

**İleti**: *galleryArtifactVersion. Properties. Publishingprofile. targetregion. encryption. Datadiskımages. Diskencryptionsetıd parametresinin değeri geçersiz*  
**Neden**: bir veri diski görüntüsünde kullanılan disk şifrelemesi KÜMESININ kaynak kimliği geçersiz bir biçim kullanıyor.  
**Geçici çözüm**: disk şifreleme KÜMESININ kaynak kimliğinin/Subscriptions/<SubscriptionID \> /ResourceGroups/<resourcegroupname \> /providers/Microsoft.COMPUTE/<diskencryptionsetname biçimini izlediği emin olun \> .

**İleti**: *galleryArtifactVersion. Properties. Publishingprofile. targetregion. encryption. Osdiskımage. Diskencryptionsetıd parametresinin değeri geçersiz.*  
**Neden**: işletim sistemi diski görüntüsünde kullanılan disk şifrelemesi KÜMESININ kaynak kimliği geçersiz bir biçim kullanıyor.  
**Geçici çözüm**: disk şifreleme KÜMESININ kaynak kimliğinin/Subscriptions/<SubscriptionID \> /ResourceGroups/<resourcegroupname \> /providers/Microsoft.COMPUTE/<diskencryptionsetname biçimini izlediği emin olun \> .

**İleti**: *\> \> güncelleştirme Galerisi görüntü sürümü isteği için [<Region] bölgesinde bir disk şifrelemesi kümesine sahip yeni veri diski görüntü şifreleme LUN [<Number] belirtilemiyor. Bu sürümü güncelleştirmek için yeni LUN 'yi kaldırın. Veri diski görüntü şifreleme ayarlarını değiştirmeniz gerekiyorsa, doğru ayarlarla yeni bir galeri görüntüsü sürümü oluşturmanız gerekir.*  
**Neden**: var olan bir görüntü sürümünün veri diskine şifreleme eklediniz. Var olan bir görüntü sürümüne şifreleme ekleyemezsiniz.  
**Geçici çözüm**: yeni bir galeri görüntüsü sürümü oluşturun veya eklenen şifreleme ayarlarını kaldırın.

**İleti**: *Galeri yapıt sürüm kaynağı yalnızca doğrudan storageprofile veya bağımsız işletim sistemi ya da veri diskleri içinde belirtilebilir. Tek bir kaynak türü (Kullanıcı görüntüsü, anlık görüntü, disk, sanal makine) belirtilebilir.*  
**Neden**: kaynak kimliği eksik.  
**Geçici çözüm**: KAYNAĞıN kaynak kimliğinin mevcut olduğundan emin olun.

**İleti**: *kaynak bulunamadı: RESOURCEID <\> . Lütfen kaynağın mevcut olduğundan emin olun.*  
**Neden**: KAYNAĞıN kaynak kimliği yanlış olabilir.  
**Geçici çözüm**: KAYNAĞıN kaynak kimliğinin doğru olduğundan emin olun.

**İleti**: ' *\_ \> \> <Region \_ 2 \> ' bölgesindeki karşılık gelen disk için ' <diskencryptionsetıd ' adlı disk şifreleme kümesi kullanıldığından, ' <Region ' ' hedef bölgesindeki ' GalleryArtifactVersion. Properties. Publishingprofile. Targetbölgeleriyle. encryption. osdiskımage. diskencryptionsetıd ' diski için bir disk şifreleme kümesi gerekir*  
**Neden**: şifreleme, <bölge 2 ' deki işletim sistemi diskinde kullanılmıştır \_ \> , ancak <bölge 1 ' de \_ kullanılamaz \> .  
**Geçici çözüm**: işletim sistemi diskinde şifreleme kullanıyorsanız, tüm bölgelerde şifreleme kullanın.

**İleti**: ' *\> \_ \> \> <Region \_ 2 \> ' bölgesindeki karşılık gelen disk için ' <diskencryptionsetıd ' disk şifreleme kümesi kullanıldığından, ' <Region 1 ' hedef bölgesindeki ' LUN <numarası ' diski için bir disk şifreleme kümesi gerekir*  
**Neden**: şifreleme \> \_ \> , <bölge 1 ' de değil, <bölge 2 ' deki LUN <numaralı veri diskinde kullanılır \_ \> .  
**Geçici çözüm**: bir veri diskinde şifreleme kullanıyorsanız, tüm bölgelerde şifreleme kullanın.

**İleti**: *\> ENCRYPTION. datadiskımages içinde geçersiz bir LUN [<Number] belirtildi. LUN, şu değerlerden biri olmalıdır [' 0, 9 '].*  
**Neden**: şifreleme IÇIN belirtilen LUN, VM 'ye bağlı disklere yönelik LUN 'lara uymuyor.  
**Geçici çözüm**: ŞIFRELEME içindeki LUN 'yi VM 'de bulunan bir veri diskinin LUN 'una değiştirin.

**İleti**: ' *\> <Region \> ' şifreleme. datadiskımages ' ' <number ' yinelenen LUN 'lar belirtildi.*  
**Neden**: <bölgede kullanılan şifreleme ayarları \> en az ıkı kez bir LUN belirtti.  
**Geçici çözüm**: \> tüm LUN 'ların <bölgesinde benzersiz olduğundan emin olmak için <bölgesindeki LUN 'u değiştirin \> .

**İleti**: *osdiskımage ve datadiskımage <SourceId \> ile aynı blob 'a işaret edemez*  
**Neden**: işletim sistemi diskinin kaynakları ve en az bir veri diski benzersiz değil.  
**Geçici çözüm**: işletim sistemi diskinin ve/veya veri disklerinin kaynağını değiştirerek, her bir veri diskinin da benzersiz olduğundan emin olun.

**İleti**: *hedef yayımlama bölgelerinde yinelenen bölgelere izin verilmez.*  
**Neden**: bölge, yayımlama bölgeleri arasında birden fazla kez listeleniyor.  
**Geçici çözüm**: yinelenen bölgeyi kaldırın.

**İleti**: *Yeni veri diskleri ekleme veya var olan bir GÖRÜNTÜDEKI veri diskinin LUN 'unu değiştirmeye izin verilmiyor.*  
**Neden**: görüntü sürümüne yönelik bir güncelleştirme çağrısı yeni bir veri diski içeriyor ya da bir disk için yeni bir LUN 'a sahip.  
**Geçici çözüm**: mevcut görüntü sürümünün LUN 'ları ve veri disklerini kullanın.

**İleti**: *<Diskencryptionsetıd disk şifreleme kümesi, \> \> Galeri kaynağıyla aynı abonelikte <aynı abonelikte olmalıdır.*  
**Neden**: paylaşılan görüntü Galerisi, farklı bir abonelikte bir disk şifrelemesi kümesini kullanmayı Şu anda desteklemiyor.  
**Geçici çözüm**: aynı abonelikte görüntü sürümünü ve disk şifreleme kümesini oluşturun.

**İleti**: *' gallerımageversion kaynak boyutu 2048, desteklenen en büyük boyut olan 1024 ' i aştığından bu bölgede çoğaltma başarısız oldu. '*  
**Neden**: kaynaktaki bir veri diski 1 TB 'den büyük.  
**Geçici çözüm**: veri DISKINI 1 TB altında olacak şekilde yeniden boyutlandırın.

## <a name="creating-or-updating-a-vm-or-scale-sets-from-an-image-version"></a>Görüntü sürümünden VM veya ölçek kümesi oluşturma veya güncelleştirme ##

**İleti**: *"<ımagedefinitionresourceıd \> " için en son görüntü sürümü yok*  
**Neden**: sanal makineyi dağıtmak için kullandığınız görüntü tanımı, en son içinde yer alan herhangi bir görüntü sürümü içermiyor.  
**Geçici çözüm**: ' en sonuncuyu hariç tut ' değeri false olarak ayarlanmış en az bir görüntü sürümü olduğundan emin olun. 

**İleti**: */subscriptions/<SubscriptionID \> /ResourceGroups/<ResourceGroup \> /providers/Microsoft.COMPUTE/Galleries/<gallername \> /images/<Gename \> /Versions/<versionNumber adlı Galeri görüntüsü \> <bölge \> bölgesinde kullanılamaz. Lütfen bu bölgeye çoğaltmak için görüntü sahibine başvurun veya istediğiniz bölgeyi değiştirin.*  
**Neden**: dağıtım için seçilen sürüm yok ya da belirtilen bölgede bir çoğaltma yok.  
**Geçici çözüm**: görüntü kaynağı adının doğru olduğundan ve belirtilen bölgede en az bir çoğaltma olduğundan emin olun. 

**İleti**: */Subscriptions/<SubscriptionID \> /ResourceGroups/<ResourceGroup \> /providers/Microsoft.COMPUTE/Galleries/<gallername \> /images/<GörüntüAdı \> <bölge \> bölgesinde kullanılamaz. Lütfen bu bölgeye çoğaltmak için görüntü sahibine başvurun veya istediğiniz bölgeyi değiştirin.*  
**Neden**: dağıtım için seçilen görüntü tanımında en son ve ayrıca belirtilen bölgede yer alan herhangi bir görüntü sürümü yok.  
**Geçici çözüm**: ' en sonuncusun dışında tut ' değeri false olarak ayarlanmış bölgede en az bir görüntü sürümü olduğundan emin olun. 

**İleti**: *istemci, RESOURCEID <kapsamında ' Microsoft. COMPUTE/galeriler/Images/Versions/Read ' eylemini gerçekleştirme iznine sahip \> ancak geçerli kiracı <tenantıd, \> bağlı aboneliğe <SubscriptionID 'ye erişim yetkisine sahip değil \> .*  
**Neden**: sanal makine veya ölçek kümesi, başka bir KIRACıDAKI bir SIG görüntüsü kullanılarak oluşturulmuştur. Sanal makinede veya ölçek kümesinde değişiklik yapmayı denediniz, ancak görüntünün sahibi olan aboneliğe erişiminiz yok.  
**Geçici çözüm**: görüntü sürümüne okuma erişimi sağlamak için görüntü sürümüne ait aboneliğin sahibine başvurun.

**İleti**: *<RESOURCEID, \> <Region \> bölgesinde kullanılamaz. Lütfen bu bölgeye çoğaltmak için görüntü sahibine başvurun veya istediğiniz bölgeyi değiştirin.*  
**Neden**: VM, Galeri görüntüsü için yayımlanan bölgelerin listesi arasından olmayan bir bölgede oluşturuluyor.  
**Geçici çözüm**: görüntüyü bölgeye çoğaltın ya da Galeri görüntüsünün yayımlama bölgelerindeki bölgelerden bırınde bir VM oluşturun.

**İleti**: *' osprofile ' parametresine izin verilmiyor.*  
**Neden**: özelleştirilmiş bir görüntü sürümünden oluşturulmuş bir VM için Yönetici Kullanıcı adı, parola veya SSH anahtarları sağlandı.  
**Geçici çözüm**: Bu GÖRÜNTÜDEN bir VM oluşturmak istiyorsanız yönetici kullanıcı adını, parolayı veya SSH anahtarlarını eklemeyin. Aksi takdirde, genelleştirilmiş bir görüntü sürümü kullanın ve Yönetici Kullanıcı adı, parola veya SSH anahtarlarını sağlayın.

**İleti**: *gerekli ' osprofile ' parametresi eksik (null).*  
**Neden**: VM genelleştirilmiş bir görüntüden oluşturulur ve Yönetici Kullanıcı adı, parola veya SSH anahtarları eksiktir. Genelleştirilmiş görüntüler Yönetici Kullanıcı adı, parola veya SSH anahtarlarını korumadığından, bu alanların bir VM veya ölçek kümesi oluşturulurken belirtilmesi gerekir.  
**Geçici çözüm**: yönetici kullanıcı adını, parolayı veya SSH anahtarlarını belirtin veya özel bir görüntü sürümü kullanın.

**İleti**: *\> VM Ölçek kümesinin geçerli işletim sistemi durumu Genelleştirilmiş olduğundan, sanal makine ölçek kümesi <vmssname güncelleştirilemiyor.*  
**Neden**: ölçek kümesi için geçerli kaynak görüntüsü genelleştirilmiş bir kaynak görüntüdür, ancak özelleştirilmiş bir kaynak görüntüyle güncelleştiriliyor. Geçerli kaynak görüntüsü ve bir ölçek kümesi için yeni kaynak görüntüsü aynı durumda olmalıdır.  
**Geçici çözüm**: ölçek kümesini güncelleştirmek için genelleştirilmiş bir görüntü sürümü kullanın.

**İleti**: *paylaşılan görüntü galerisinde <Diskencryptionsetıd adlı Disk şifreleme kümesi \> <VersionId, \> SubscriptionID 1 <aboneliğe \_ aittir \> ve abonelik <abonelik kimliği ' \_ \> ' olan ' ' kaynağıyla kullanılamaz*  
**Neden**: görüntü sürümünü şifrelemek için kullanılan disk şifreleme kümesi, görüntü sürümünü barındırmak için abonelikten farklı bir abonelikte bulunuyor.  
**Geçici çözüm**: görüntü sürümü ve disk şifreleme kümesi için aynı aboneliği kullanın.

**İleti**: *VM veya sanal makine ölçek kümesi oluşturma uzun bir süre sürer.*  
**Geçici çözüm**: VM 'yi ya da sanal makine ölçek kümesini oluşturmaya çalıştığınız görüntü sürümünün **OSType** öğesinin görüntü sürümünü oluşturmak Için kullandığınız kaynağın aynı **OSType** öğesine sahip olduğunu doğrulayın. 

**İleti**: *kimliği <VMID olan kaynak, \> \" \" \" <name> \" \" üst Galeri görüntüsü planı [' null '] olan farklı bir plana [' {Name:, Publisher \" : \" <publisher> \" , \" Product \" : \" <product> \" , \" promotioncode \" : \" <promotionCode> \" } '] sahip.*  
**Neden**: dağıtılmakta olan görüntü sürümü için üst görüntü tanımının bir satın alma planı bilgisi yok.  
**Geçici çözüm**: hata iletisinden aynı satın alma planı ayrıntılarına sahip bir görüntü tanımı oluşturun ve görüntü tanımı içinde görüntü sürümünü oluşturun.


## <a name="creating-a-disk-from-an-image-version"></a>Görüntü sürümünden disk oluşturma ##

**İleti**: *ImageReference parametresinin değeri geçersiz.*  
**Neden**: bir SIG görüntü sürümünden diske dışarı aktarmaya çalıştınız, ancak görüntüde bulunmayan bir LUN konumu kullandınız.    
**Geçici çözüm**: hangi LUN konumlarının kullanımda olduğunu görmek için görüntü sürümünü denetleyin.

## <a name="sharing-resources"></a>Kaynakları paylaşma

[Azure rol tabanlı erişim denetimi (Azure RBAC)](../role-based-access-control/rbac-and-directory-admin-roles.md)kullanılarak, görüntü Galerisi, görüntü tanımı ve görüntü sürümü kaynakları aboneliklerde paylaşılır. 

## <a name="replication-speed"></a>Çoğaltma hızı

Belirtilen tüm hedef bölgelere yönelik çoğaltmanın tamamlanıp bitmediğini denetlemek için, ' **i Genişlet ReplicationStatus** bayrağını kullanın. Aksi takdirde, işin tamamlanabilmesi için altı saate kadar bekleyin. Başarısız olursa, görüntü sürümünü oluşturmak ve çoğaltmak için komutu yeniden tetikleyin. Görüntü sürümünün çoğaltılacağı çok sayıda hedef bölge varsa, çoğaltmayı aşamalar halinde yapmayı göz önünde bulundurun.

## <a name="azure-limits-and-quotas"></a>Azure limitleri ve kotaları 

[Azure Limitleri ve kotaları](../azure-resource-manager/management/azure-subscription-service-limits.md) tüm paylaşılan görüntü Galerisi, görüntü tanımı ve görüntü sürümü kaynakları için geçerlidir. Aboneliklerinizin sınırları dahilinde olduğunuzdan emin olun. 


## <a name="next-steps"></a>Sonraki adımlar

[Paylaşılan görüntü galerileri](./shared-image-galleries.md)hakkında daha fazla bilgi edinin.