---
title: Sürüm notları-Özel Görüntü İşleme Hizmeti
titleSuffix: Azure Cognitive Services
description: Özel Görüntü İşleme ekibinden yeni yayınlar hakkında en son bilgileri alın.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: anroth
ms.openlocfilehash: b9606c63ec7b53fb0b69918c21bdd9206f34c555
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73647567"
---
# <a name="custom-vision-service-release-notes"></a>Özel Görüntü İşleme Hizmeti sürüm notları

## <a name="may-2-2019-and-may-10-2019"></a>2 Mayıs 2019 ve 10 Mayıs 2019

- Hata düzeltmeleri ve arka uç geliştirmeleri

## <a name="may-23-2019"></a>23 Mayıs 2019

- Azure abonelikleriyle ilgili geliştirilmiş Portal UX deneyimi, Azure dizinlerinizin kolayca görülmesini kolaylaştırır.

## <a name="april-18-2019"></a>18 Nisan 2019 

- Vision AI Dev Kit için nesne algılama dışarı aktarma eklendi.
- Proje arama dahil olmak üzere UI tnak 'leri.

## <a name="april-3-2019"></a>3 Nisan 2019

- 200 'e görüntü başına sınırlama kutusu sayısında artan sınır. 
- TensorFlow 'a aktarılmış modeller için önemli performans güncelleştirmesi dahil olmak üzere bugdüzeltmelerde. 

## <a name="march-26-2019"></a>26 Mart 2019

- Özel Görüntü İşleme Hizmeti Azure 'da genel kullanıma girdi!
- Özellikle zorlu veri kümelerinde ve hassas sınıflandırmada geliştirilmiş performans için yeni bir makine öğrenimi arka ucu ile gelişmiş eğitim özelliği eklendi. Gelişmiş eğitimle eğitim için bir işlem süresi bütçesini belirtebilir ve Özel Görüntü İşleme en iyi eğitim ve genişletme ayarlarını belirlemek experimentally olacaktır. Hızlı yinelemeler için, var olan hızlı eğitimi kullanmaya devam edebilirsiniz.
- 3,0 API 'Leri sunuldu. 1 Ekim 2019 ' de 3,0 öncesi API 'Leri kullanımdan kaldırma hakkında duyuruldu. Kullanmaya başlama hakkında örnekler için bkz. [.net](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/csharp-tutorial), [Python](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/python-tutorial), [node](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/node-tutorial), [Java](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/java-tutorial)veya [Go](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/go-tutorial) için hızlı başlangıç belgeleri.
- 3,0 API 'Lerinde Yayımla/yayımdan kaldırma ile "varsayılan yinelemeler" değiştirilmiştir.
- Yeni model dışarı aktarma hedefleri eklendi. Dockerfile dışa aktarma, Raspberry PI 3 için ARM 'yi destekleyecek şekilde yükseltildi. [VISION AI Dev Kit](https://visionaidevkit.com/)'e dışa aktarma desteği eklenmiştir.
- S0 katmanı için proje başına etiket sınırı 500 ' e kadar artar. S0 katmanı için proje başına görüntü sayısı üst sınırı 100.000 ' e kadar artar.
- Yetişkin etki alanı kaldırıldı. Bunun yerine genel etki alanı önerilir.
- Genel kullanılabilirlik için duyurulan [fiyatlandırma](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) .  

## <a name="february-25-2019"></a>25 Şubat 2019

- , Azure genel önizlemesine taşıma işleminin tamamlanması Özel Görüntü İşleme yaklaştığında, sınırlı deneme projelerinin (bir Azure kaynağıyla ilişkilendirilmemiş projeler) sonuna duyuruldu. CustomVision.ai sitesi, 25 Mart 2019 ' den itibaren, yalnızca ücretsiz Özel Görüntü İşleme kaynağı gibi bir Azure kaynağıyla ilişkili projeleri görüntülemeyi destekleyecektir. 1 Ekim 2019 ' e kadar, mevcut sınırlı deneme projelerinize Özel Görüntü İşleme API 'Leri aracılığıyla erişebilirsiniz. Bu, Özel Görüntü İşleme ile yazdığınız herhangi bir uygulama için API anahtarlarını güncelleştirme zamanı verir. 1 Ekim 2019 ' den sonra, Azure 'a taşınmadığınız tüm sınırlı deneme projeleri silinir.

## <a name="january-22-2019"></a>22 Ocak 2019

- Yeni Azure bölgeleri için destek eklendi: Batı ABD 2, Doğu ABD, Doğu ABD 2, Batı Avrupa, Kuzey Avrupa, Güneydoğu Asya, Avustralya Doğu, Orta Hindistan, UK Güney, Japonya Doğu ve Orta Kuzey ABD. Orta Güney ABD için destek devam etmektedir.

## <a name="december-12-2018"></a>12 Aralık 2018

- Nesne algılama modelleri için dışarı aktarma desteği (nesne algılama Compact etki alanı tanıtılan).
- İyileştirilmiş ekran okuyucu ve klavye gezintisi desteğiyle ilgili birçok erişilebilirlik sorunu düzeltildi.
- Image Viewer için UX güncelleştirmeleri ve daha hızlı etiketleme için geliştirilmiş nesne algılama etiketleme deneyimi.  
- Daha iyi kalite nesne algılaması için nesne algılama etki alanı için temel model güncelleştirildi.
- Hata düzeltmeleri.

## <a name="november-6-2018"></a>6 Kasım 2018

- Nesne algılamasında logo etki alanı için destek eklendi.

## <a name="october-9-2018"></a>9 Ekim 2018

- Nesne algılama, ücretli önizlemeye giriyor. Artık, bir Azure kaynağı ile nesne algılama projeleri oluşturabilirsiniz.
- Sınırlı bir deneme projesini Azure 'a bağlamak üzere yükseltmeyi kolaylaştırmak için Web sitesine "Azure 'a taşı" özelliği eklendi. Kaynak bağlantılı proje (F0 veya S0.) Bunu, ürününüzün ayarlar sayfasında bulabilirsiniz.  
- Windows 'un Windows 2018 Ekim güncelleştirme sürümünü desteklemek için ONNX 1,2 ' e dışarı aktarma eklendi.
Özel karakterlerle ONNX dışa aktarma için de dahil olmak üzere hata düzeltmeleri.

## <a name="august-14-2018"></a>14 Ağustos 2018

- Customvision.ai sitesine "Başlarken" pencere öğesi eklendi ve kullanıcılara proje eğitimi aracılığıyla rehberlik.
- Çok etiketli projelerle (yeni kayıp katmanı) faydalanmak için makine öğrenimi ardışık düzeninde daha fazla geliştirmeler.

## <a name="june-28-2018"></a>28 Haziran 2018

- Hata düzeltmeleri arka uç iyileştirmeleri &.
- Görüntülerin tam olarak bir etiketi olduğu projeler için birden çok Lass sınıflandırması etkinleştirildi. Birden çok Lass modunun tahminlerinde, olasılıklara bir tane toplanacak (tüm görüntüler belirtilen etiketleriniz arasında sınıflandırıldı).

## <a name="june-13-2018"></a>13 Haziran 2018

- UX yenileme, kullanım kolaylığı ve erişilebilirlik odaklı.
- Çok etiketli projelere çok sayıda etiket ile faydalanmak için makine öğrenimi ardışık düzeninde geliştirmeler yapılmıştır.
- TensorFlow dışarı aktarma sırasında hata düzeltildi. Devre dışı aktarılmış model sürümü oluşturma, yinelemeler birden çok kez verilebilirler.

## <a name="may-7-2018"></a>7 Mayıs 2018

- Sınırlı Deneme Sürümü projeleri için önizleme sürümündeki Nesne Algılama özelliği eklendi.
- 2\.0 API'lere yükseltme yapıldı
- S0 katmanı 250 etiket ve 50.000 görüntüye kadar genişletildi.
- Görüntü sınıflandırma projeleri için makine öğrenmesi işlem hattında önemli arka uç geliştirmeleri yapıldı. 27 Nisan 2018 sonrasında eğitilen projeler bu güncelleştirmeden faydalanabilecek.
- Windows ML ile kullanılmak üzere modelleri ONNX biçiminde dışarı aktarma özelliği eklendi.
- Dockerfile biçiminde model dışarı aktarma özelliği eklendi. Bu özellik yapıtları indirerek DockerFile, TensorFlow modeli ve hizmet kodu dahil olmak üzere kendi Windows veya Linux kapsayıcılarınızı oluşturmanızı sağlar.
- Genel (Compact) ve yer Işareti (Compact) etki alanlarında TensorFlow 'a verilen yeni eğitilen modeller için, tüm projeler genelinde tutarlılık sağlamak üzere [Ortalama değerler (0, 0, 0)](https://github.com/azure-samples/cognitive-services-android-customvision-sample).

## <a name="march-1-2018"></a>1 Mart 2018

- Azure portal ücretli önizleme ve eklendi. Projeler artık F0 (Ücretsiz) veya S0 (Standard) katmandaki Azure kaynaklarına eklenebilir. 100 etikete ve 25.000 görüntüye izin veren S0 katmanı projeleri eklendi.
- Makine öğrenmesi işlem hattı/normalleştirme parametresi için arka uç değişiklikleri. Bu sayede müşteriler Olasılık Eşiğini ayarlarken hassasiyet-geri çağırma durumlarını daha iyi denetleyebilecek. Bu değişikliklerle bağlantılı olarak CustomVision.ai portalındaki Olasılık Eşiği %50 olarak ayarlandı.

## <a name="december-19-2017"></a>19 Aralık 2017

- Daha önce iOS 'a dışarı aktarmaya (CoreML.) ek olarak, Android 'e (TensorFlow) dışa aktarma eklendi. Bu, eğitilen bir modelin bir uygulamada çevrimdışı çalışacak şekilde verilmesini sağlar.
- Perakende ve Yer İşareti "sıkıştırılmış" etki alanları eklendi ve bu etki alanları için model dışarı aktarma özellikleri etkinleştirildi.
- [1.2 Eğitim API'si](https://southcentralus.dev.cognitive.microsoft.com/docs/services/f2d62aa3b93843d79e948fe87fa89554/operations/5a3044ee08fa5e06b890f11f) ile [1.1 Tahmin API'si](https://southcentralus.dev.cognitive.microsoft.com/docs/services/57982f59b5964e36841e22dfbfe78fc1/operations/5a3044f608fa5e06b890f164) yayımlandı. API'ler model dışarı aktarma seçeneği, görüntüleri "Tahminlere" kaydetmeyen yeni Tahmin işlemi ile güncelleştirildi ve Eğitim API'sine toplu işlem eklendi.
- Yineleme eğitimi için kullanılan etki alanını görme dahil olmak üzere kullanıcı deneyimi ayarlamaları yapıldı.
- [C# SDK'sı ve örneği](https://github.com/Microsoft/Cognitive-CustomVision-Windows) güncelleştirildi.