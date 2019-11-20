---
title: 'Öğretici: Azure IoT Edge Machine Learning ayrıntılı yolu'
description: Uç senaryosunda uçtan uca bir makine öğrenimi oluşturmak için gereken çeşitli görevleri adım adım gösteren üst düzey bir öğretici.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/11/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 965c420fa29c4cf82517148c01e17d6d7dd6ea97
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74106499"
---
# <a name="tutorial-an-end-to-end-solution-using-azure-machine-learning-and-iot-edge"></a>Öğretici: Azure Machine Learning ve IoT Edge kullanarak uçtan uca bir çözüm

IoT uygulamaları, genellikle akıllı buluttan ve akıllı kenarından faydalanmak istiyor. Bu öğreticide, bulutta IoT cihazlarından toplanan verilerle bir makine öğrenimi modeline eğitim vererek, bu modeli IoT Edge için dağıtmakta ve modeli düzenli aralıklarla koruyup iyileştirirken size kılavuzluk ederiz.

Bu öğreticinin birincil amacı, özellikle de kenarda, Machine Learning ile IoT verilerinin işlenmesini tanıtmaktadır. Genel makine öğrenimi iş akışının pek çok yönüyle iletişime geçtiğimiz için, bu öğretici Machine Learning 'e derinlemesine bir giriş olarak tasarlanmamıştır. Bu noktada, kullanım örneği için yüksek düzeyde iyileştirilmiş bir model oluşturmaya çalışıyoruz; IoT veri işleme için uygun bir model oluşturma ve kullanma sürecini göstermek için yeterlidir.

## <a name="target-audience-and-roles"></a>Hedef kitle ve roller

Bu makale kümesi, IoT geliştirme veya makine öğrenimi konusunda önceki deneyim olmadan geliştiricilere yöneliktir. Makine öğrenimini kenarda dağıtmak, çok çeşitli teknolojilerin nasıl bağlanabilmesinin bilgisini gerektirir. Bu nedenle, bu öğreticide bir IoT çözümü için bu teknolojilerin bir araya katılmasını gösteren bir uçtan uca senaryonun tamamı ele alınmaktadır. Gerçek dünyada bir ortamda, bu görevler farklı uzmanlık özelliklerine sahip birkaç kişi arasında dağıtılabilir. Örneğin, geliştiriciler, cihaz veya bulut koduna odaklanarak veri bilimcileri analiz modellerini tasarlamıştır. Bireysel bir geliştiricinin bu öğreticiyi tamamlamasını sağlamak için öngörülerle birlikte ek rehberlik ve ne olduğunu ve ne olduğunu öğrenmemiz gereken daha fazla bilgi için bağlantıları sunuyoruz.

Alternatif olarak, Öğreticiyi bir şekilde izlemek, tam uzmanlığınızı bir araya getirmek ve işlerin nasıl bir araya getireceğinizi öğrenmek için farklı rollerin iş arkadaşlarınızla ekip oluşturabilirsiniz.

Her iki durumda da, okuyucu (ler) i yönlendirmek için bu öğreticideki her makale kullanıcının rolünü gösterir. Bu roller şunları içerir:

* Bulut geliştirme (DevOps kapasitesinde çalışan bir bulut geliştiricisi dahil)
* Veri analizi

## <a name="use-case-predictive-maintenance"></a>Kullanım örneği: tahmine dayalı bakım

Bu senaryoyu, 2008 'deki Prognostics ve Sağlık Yönetimi (PHM08) üzerinde konferansta sunulan kullanım örneğine dayandırdık. Amaç, bir dizi türbofan uçak altyapısının kalan faydalı ömrünü (RUL) tahmin etmek için kullanılır. Bu veriler, MAPSS 'nin ticari sürümü (modüler Aero-Prodarbelerte sistem simülasyonu) yazılımının C-MAPSS kullanılarak oluşturulmuştur. Bu yazılım, sistem durumu, denetim ve altyapı parametrelerinin kolay benzetimini yapmak için esnek bir Turban altyapı simülasyonu ortamı sağlar.

Bu öğreticide kullanılan veriler, [turbofan altyapısının düşme simülasyonu veri kümesinden](https://ti.arc.nasa.gov/tech/dash/groups/pcoe/prognostic-data-repository/#turbofan)alınmıştır.

Benioku dosyasından:

***Deneysel senaryo***

*Veri kümeleri birden fazla çok değişkenli zaman serisinden oluşur. Her veri kümesi, eğitim ve test alt kümelerine bölünmüştür. Her bir serinin farklı bir altyapıdan olması, yani verilerin aynı türdeki bir çok sayıda altyapıdan olduğu kabul edilebilir. Her motor, Kullanıcı tarafından bilinmeyen, farklı ilk aşınma ve üretim çeşitlemesi ile başlar. Bu giyme ve değişim normal olarak değerlendirilir, yani bir hata koşulu olarak kabul edilmez. Altyapı performansına önemli bir etkisi olan üç işlemsel ayar vardır. Bu ayarlar verilere da dahildir. Veriler sensörle gürültü ile ayrılmış.*

*Motor her bir zaman serisinin başlangıcında normal şekilde çalışır ve seri sırasında bir noktada bir hata geliştirir. Eğitim kümesinde hata, sistem hatasına kadar büyüklüğü artar. Test kümesinde, zaman serisi sistem hatasından önce bir süre sona erer. Yarışmanın amacı, test kümesinde hatadan önce kalan işlem döngülerinin sayısını tahmin etmek, yani, altyapının çalışmaya devam etmesi için son döngüden sonra işlem döngüsü sayısı. Ayrıca, test verileri için doğru kalan kullanım ömrü (RUL) değerlerinin vektörünün sağlanması.*

Veriler bir yarışma yönelik yayımlandığından, makine öğrenimi modellerini türetmeye yönelik birkaç yaklaşım bağımsız olarak yayımlandı. Bu örnek, belirli bir makine öğrenimi modelinin oluşturulmasına dahil olan işlem ve nedenlerinizi anlamak için yararlı olduğunu bulduk. Bkz. Örneğin:

[Uçak altyapısı hata tahmin modeli](https://github.com/jancervenka/turbofan_failure) GitHub kullanıcısı, occervenka.

GitHub kullanıcısı hankroark tarafından oluşan [turbofan altyapısı](https://github.com/hankroark/Turbofan-Engine-Degradation) .

## <a name="process"></a>İşleme

Aşağıdaki resimde, bu öğreticide izlediğimiz kaba adımlar gösterilmektedir:

![İşlem adımları için mimari diyagramı](media/tutorial-machine-learning-edge-01-intro/tutorial-steps-overview.png)

1. **Eğitim verilerini toplayın**: süreç eğitim verileri toplanarak başlar. Bazı durumlarda veriler zaten toplanmıştır ve bir veritabanında ya da veri dosyası biçiminde kullanılabilir. Diğer durumlarda, özellikle IoT senaryolarında verilerin IoT cihazlarından ve sensörlerinden toplanması ve bulutta depolanması gerekir.

   Türbofan altyapılarının bir koleksiyonu olmadığı varsayıyoruz. proje dosyaları, NASA cihaz verilerini buluta gönderen basit bir cihaz simülatörü içerir.

1. **Verileri hazırlayın**. Çoğu durumda, cihazlardan ve sensörlerden toplanan ham verilerin makine öğrenimine hazırlanması gerekir. Bu adım, veri temizleme, veri yeniden biçimlendirme ya da daha fazla bilgi eklemek için bir ön işleme içerebilir.

   Uçak motoru makine verilerimiz için veri hazırlama, verilerdeki gerçek gözlemlere bağlı olarak örnekteki her veri noktası için açık arıza süresi sürelerinin hesaplanmasını içerir. Bu bilgiler, makine öğrenimi algoritmasının gerçek algılayıcı veri desenleri ve altyapının beklenen kalan yaşam süresi arasındaki bağıntıları bulmasına olanak tanır. Bu adım son derece etki alanına özgüdür.

1. **Machine Learning modeli oluşturun**. Hazırlanan verileri temel alarak, modelleri eğmek ve sonuçları birbiriyle karşılaştırmak için farklı makine öğrenimi algoritmalarından ve parametreetmelere artık denemeler yapabilirsiniz.

   Bu durumda, test için, model tarafından hesaplanan tahmini sonucu, bir altyapı kümesi üzerinde gözlemlendi gerçek sonucuyla karşılaştırıyoruz. Azure Machine Learning, bir model kayıt defterinde oluşturduğumuz modellerden farklı yinelemeleri yönetebiliriz.

1. **Modeli dağıtın**. Başarı ölçütlerinizi karşılayan bir modelimiz varsa dağıtıma taşıyabiliriz. Bu, modeli REST çağrıları ve geri dönüş analizi sonuçları kullanılarak verilerle beslenebilir bir Web hizmeti uygulamasına sarmalamayı içerir. Web hizmeti uygulaması daha sonra bir Docker kapsayıcısına paketlenir, bu da bulutta veya bir IoT Edge modülü olarak dağıtılabilir. Bu örnekte, IoT Edge dağıtıma odaklanıyoruz.

1. **Modeli koruyun ve daraltın**. Model dağıtıldıktan sonra çalışmamız yapılmaz. Birçok durumda, verileri toplamaya ve verileri düzenli aralıklarla buluta yüklemeye devam etmek istiyoruz. Daha sonra bu verileri, modelimizi yeniden eğitmek ve iyileştirmek için kullanabiliriz. daha sonra IoT Edge için yeniden dağıtırsınız.

## <a name="prerequisites"></a>Önkoşullar

Öğreticiyi tamamlayabilmeniz için, kaynak oluşturma haklarınız olan bir Azure aboneliğine erişmeniz gerekir. Bu öğreticide kullanılan hizmetlerden bazıları Azure ücretlerine tabi olacaktır. Henüz bir Azure aboneliğiniz yoksa [ücretsiz bir Azure hesabı](https://azure.microsoft.com/offers/ms-azr-0044p/)kullanmaya başlamanızı sağlayabilirsiniz.

Ayrıca, geliştirme makineniz olarak bir Azure sanal makinesini kurmak üzere betikleri çalıştırabileceğiniz PowerShell yüklü bir makineye ihtiyacınız vardır.

Bu belgede, aşağıdaki araç kümesini kullanırız:

* Veri yakalama için bir Azure IoT Hub 'ı

* Veri hazırlama ve makine öğrenimi deneme için ana ön uçmız olarak Azure Notebooks. Örnek verilerin bir alt kümesindeki bir not defterinde Python kodu çalıştırmak, veri hazırlama sırasında hızla yinelemeli ve etkileşimli bir şekilde yük açmak için harika bir yoldur. Jupi Not defterleri, komut dosyalarını bir işlem arka ucunda ölçeklendirerek çalışacak şekilde hazırlamak için de kullanılabilir.

* Makine öğrenimi için ölçek ve makine öğrenimi görüntü oluşturma için arka uç olarak Azure Machine Learning. Jupi not defterlerinde hazırlanan ve test edilen betikleri kullanarak Azure Machine Learning arka ucunu sunuyoruz.

* Makine öğrenimi görüntüsünün bulut dışı uygulaması için Azure IoT Edge

Açıktır, başka seçenekler de mevcuttur. Örneğin, IoT Central, IoT cihazlarından ilk eğitim verilerini yakalamaya yönelik kod olmayan bir alternatif olarak kullanılabilir.

## <a name="next-steps"></a>Sonraki adımlar

Bu öğretici aşağıdaki bölümlere ayrılmıştır:

1. Geliştirme makinenizi ve Azure hizmetlerini ayarlayın.
2. Machine Learning modülü için eğitim verileri oluşturun.
3. Machine Learning modülünü eğitme ve dağıtma.
4. Bir IoT Edge cihazı, saydam bir ağ geçidi olarak davranacak şekilde yapılandırın.
5. IoT Edge modülleri oluşturun ve dağıtın.
6. IoT Edge cihazınıza veri gönderme.

Bir geliştirme makinesi kurmak ve Azure kaynakları sağlamak için bir sonraki makaleye geçin.

> [!div class="nextstepaction"]
> [IoT Edge makine öğrenimi için bir ortam ayarlama](tutorial-machine-learning-edge-02-prepare-environment.md)
