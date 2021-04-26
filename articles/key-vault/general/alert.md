---
title: İzleme ve uyarma Azure Key Vault | Microsoft Docs
description: Anahtar kasanızın durumunu izlemek ve uyarıları yapılandırmak için bir pano oluşturun.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 03/31/2021
ms.author: mbaldwin
ms.openlocfilehash: f8f9dd6d51b974ebd31804daf0402ca5535ffc92
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751589"
---
# <a name="monitoring-and-alerting-for-azure-key-vault"></a>Azure Key Vault için izleme ve uyarma

## <a name="overview"></a>Genel Bakış

Üretim sırlarınızı depolamak için Anahtar Kasası 'nı kullanmaya başladıktan sonra, hizmetinizin istendiği gibi çalıştığından emin olmak için anahtar kasasının sistem durumunu izlemek önemlidir. Hizmetinizi ölçeklendirmeye başladığınızda, anahtar kasanıza gönderilen isteklerin sayısı artacaktır. Bu, isteklerinizin gecikme süresini artırmak ve olağanüstü durumlarda, hizmetinizin performansını etkileyecek olan isteklerinizin kısıtlanmasına neden olur. Ayrıca, anahtar kasanızın olağan dışı sayıda hata kodu göndermesi durumunda uyarı almanız gerekir, bu sayede herhangi bir erişim ilkesi veya güvenlik duvarı yapılandırma sorunu hakkında hızlı bir bildirim alabilirsiniz. Bu belgede aşağıdaki konular ele alınacaktır:

+ İzlenecek temel Key Vault ölçümleri
+ Ölçümleri yapılandırma ve pano oluşturma
+ Belirtilen eşikte uyarı oluşturma

Key Vault için Azure Izleyici, genel bir izleme çözümü sağlamak için hem günlükleri hem de ölçümleri birleştirir. [Key Vault için Azure Izleyici hakkında daha fazla bilgi edinin](https://docs.microsoft.com/azure/azure-monitor/insights/key-vault-insights-overview#introduction-to-azure-monitor-for-key-vault)

## <a name="basic-key-vault-metrics-to-monitor"></a>İzlenecek temel Key Vault ölçümleri

+ Kasa kullanılabilirliği  
+ Kasa doygunluğu 
+ Hizmet API 'SI gecikmesi 
+ Toplam hizmet API 'SI Isabet sayısı (etkinlik türüne göre filtrele) 
+ Hata kodları (durum koduna göre filtrele) 

**Kasa kullanılabilirliği** -Bu ölçüm her zaman %100 olmalıdır. Bu, anahtar kasanızın kesintiye neden olduğunu hızlıca gösterebildiklerinden, izlemeniz gereken önemli bir ölçümdür. 

**Kasa doygunluğu** : bir anahtar kasasının, saniye başına kullanılabileceği istek sayısı, gerçekleştirilen işlem türüne bağlıdır. Bazı kasa işlemlerinde, saniye başına daha düşük bir istek vardır. Bu ölçüm, geçerli Anahtar Kasası kullanımınızı gösteren bir yüzde değeri ile birlikte olmak üzere tüm işlem türlerinde anahtar kasaınızın toplam kullanımını toplar. Anahtar Kasası hizmeti limitlerinin tam listesi için aşağıdaki belgeye bakın. [Hizmet sınırlarını Azure Key Vault](service-limits.md)

**Hizmet API 'Si gecikmesi** -Bu ölçüm, hizmette ölçülen Anahtar Kasası çağrılarının ortalama gecikme süresini gösterir. İstemci veya istemci ile hizmet arasında ağ tarafından tüketilen zamanı içermez.

**Toplam API isabet** sayısı-bu ölçüm, anahtar kasanıza yapılan tüm çağrıları gösterir. Bu, anahtar kasanızı hangi uygulamaların çağırıyor olduğunu belirlemenize yardımcı olur. 

**Hata kodları** – Bu ölçüm, anahtar kasanızın olağan dışı bir hata miktarına sahip olup olmadığını gösterir. Hata kodlarının tam listesi ve sorun giderme kılavuzu için aşağıdaki belgeye bakın. [Azure Key Vault REST API hata kodları](rest-error-codes.md)

## <a name="how-to-configure-metrics-and-create-a-dashboard"></a>Ölçümleri yapılandırma ve pano oluşturma

1. Azure portalında oturum açın
2. Key Vault gidin
3. **İzleme** altındaki **ölçümleri** seçin 

> [!div class="mx-imgBorder"]
> ![Izleme bölümü altındaki ölçümler seçeneğini vurgulayan ekran görüntüsü.](../media/alert-1.png)

4. Grafiğin başlığını panonuzda görmek istediğiniz şekilde güncelleştirin. 
5. Kapsamı seçin. Bu örnekte tek bir Anahtar Kasası seçeceğiz. 
6. **Genel kasa kullanılabilirliği** ve toplam **Ort** ölçüsünü seçin 
7. Zaman aralığını son 24 saat olarak güncelleştirin ve zaman parçalı yapısını 1 dakika olarak güncelleştirin. 

> [!div class="mx-imgBorder"]
> ![Genel kasa kullanılabilirliği ölçüsünü gösteren ekran görüntüsü.](../media/alert-2.png)

8. Kasa doygunluğu ve hizmet API 'SI gecikme ölçümleri için yukarıdaki adımları tekrarlayın. Ölçümleri bir panoya kaydetmek için **panoya sabitle ' yi** seçin. 

> [!IMPORTANT]
> "Panoya sabitle" seçeneğini belirleyin ve yapılandırdığınız her ölçümü kaydedin. Sayfadan ayrıldığınızda ve kaydetmeden buna geri dönerseniz, yapılandırma değişiklikleriniz kaybedilir. 

9. Anahtar kasasındaki tüm işlem türlerini izlemek için, **Total SERVICE API Isabetlerinin** ölçüsünü kullanın ve **etkinlik türüne göre bölmeyi Uygula** ' yı seçin.

> [!div class="mx-imgBorder"]
> ![Bölmeyi Uygula düğmesini gösteren ekran görüntüsü.](../media/alert-3.png)

10. Anahtar kasasındaki hata kodlarını izlemek için, **toplam hizmet API 'Si sonuçları** ölçümünü kullanın ve **etkinlik türüne göre bölmeyi Uygula** ' yı seçin.

> [!div class="mx-imgBorder"]
> ![Seçilen Total Service API sonuçları ölçümünü gösteren ekran görüntüsü.](../media/alert-4.png)

Artık şuna benzeyen bir panonuz olacak. Her kutucuğun sağ üst köşesinde 3 noktaya tıklayabilirsiniz ve kutucukları gerektiği gibi yeniden düzenleyebilir ve yeniden boyutlandırabilirsiniz. 

Panoyu kaydettikten ve yayımladıktan sonra Azure aboneliğinizde yeni bir kaynak oluşturur. "Paylaşılan Pano" öğesini arayarak dilediğiniz zaman görebilirsiniz. 

> [!div class="mx-imgBorder"]
> ![Yayınlanan panoyu gösteren ekran görüntüsü.](../media/alert-5.png)

## <a name="how-to-configure-alerts-on-your-key-vault"></a>Key Vault uyarıları yapılandırma 

Bu bölümde, anahtar kasanızın durumu iyi olmayan bir durumdaysa takımınızın işlem yapması için sizi uyarabilmeniz için anahtar kasasında uyarıların nasıl yapılandırılacağı gösterilir. E-posta gönderen, tercihen bir ekip DL, bir olay Kılavuzu bildirimi tetiklemesi veya telefon numarası çağrısı veya metin gönderme gibi uyarıları yapılandırabilirsiniz. Ayrıca, bir sabit değere göre statik uyarıları veya izlenen bir ölçüm, anahtar kasanızın ortalama limitini, tanımlı bir zaman aralığında belirli bir sayıda aştığında size uyarı verecek dinamik bir uyarı seçebilirsiniz. 

> [!IMPORTANT]
> Yeni yapılandırılmış uyarıların bildirim göndermeye başlaması için en fazla 10 dakika sürebileceğini lütfen unutmayın. 

### <a name="configure-an-action-group"></a>Bir eylem grubunu yapılandırma 

Eylem grubu, bildirimlerin ve özelliklerin yapılandırılabilir bir listesidir.

1. Azure portalında oturum açın
2. Arama kutusunda **Uyarı** arayın
3. **Işlemleri Yönet** ' i seçin

> [!div class="mx-imgBorder"]
> ![Eylemleri Yönet düğmesini vurgulayan ekran görüntüsü.](../media/alert-6.png)

4. **+ Eylem grubu Ekle** ' yi seçin

> [!div class="mx-imgBorder"]
> ![+ Eylem grubu Ekle düğmesini vurgulayan ekran görüntüsü.](../media/alert-7.png)

5. Eylem grubunuz için **eylem türünü** seçin. Bu örnekte, bir e-posta uyarısı oluşturacağız.

> [!div class="mx-imgBorder"]
> ![Bir eylem grubu eklemek için gereken alanları vurgulayan ekran görüntüsü.](../media/alert-8.png)

> [!div class="mx-imgBorder"]
> ![E-posta veya SMS ileti uyarısı eklemek için gerekenleri gösteren ekran görüntüsü.](../media/alert-9.png)

6. Sayfanın altından **Tamam**’a tıklayın. Bir eylem grubunu başarıyla oluşturdunuz. 

Bir eylem grubu yapılandırdığınıza göre, Anahtar Kasası uyarı eşiklerini yapılandıracağız. 

### <a name="configure-alert-thresholds"></a>Uyarı eşiklerini yapılandırma 

1. Azure portal Anahtar Kasası kaynağını seçin ve **izleme** altında **uyarıları** seçin

> [!div class="mx-imgBorder"]
> ![Izleme bölümü altında uyarılar menü seçeneğini gösteren ekran görüntüsü.](../media/alert-10.png)

2. **Yeni uyarı kuralı** Seç

> [!div class="mx-imgBorder"]
> ![+ Yeni uyarı kuralı düğmesini gösteren ekran görüntüsü.](../media/alert-11.png)

3. Uyarı kuralınızın kapsamını seçin. Tek bir kasa veya birden çok seçim yapabilirsiniz. 

> [!IMPORTANT]
> Uyarılarınızın kapsamı için birden çok kasa seçerken, tüm seçili kasaların aynı bölgede olması gerektiğini unutmayın. Farklı bölgelerde kasaların ayrı uyarı kuralları yapılandırmanız gerekecektir. 

> [!div class="mx-imgBorder"]
> ![Kasanın nasıl kullanılabileceğini gösteren ekran görüntüsü.](../media/alert-12.png)

4. Uyarılarınız için koşulları seçin. Aşağıdaki sinyallerden herhangi birini seçebilir ve uyarı için mantığınızı tanımlayabilirsiniz. Key Vault takım aşağıdaki uyarı eşiklerini yapılandırmayı öneriyor. 

    + Key Vault kullanılabilirliği %100 (statik eşik) altına düştir
    + Key Vault gecikme 500 MS 'den büyük (statik eşik) 
    + Genel kasa doygunluğu %75 değerinden büyük (statik eşik) 
    + Genel kasa doygunluğu ortalama (Dinamik Eşik) değerini aşıyor
    + Ortalamanın üstünde toplam hata kodları (Dinamik Eşik) 

> [!div class="mx-imgBorder"]
> ![Uyarılar için koşulları seçtiğiniz yeri gösteren ekran görüntüsü.](../media/alert-13.png)

### <a name="example-1-configuring-a-static-alert-threshold-for-latency"></a>Örnek 1: gecikme için statik bir uyarı eşiği yapılandırma

Sinyal adı olarak **genel hizmet API 'Si gecikmesini** seçin
> [!div class="mx-imgBorder"]
> ![Genel hizmet API 'SI gecikme sinyali adını gösteren ekran görüntüsü.](../media/alert-14.png)

Lütfen aşağıdaki yapılandırma parametrelerine bakın.

+ Eşiği **statik** olarak ayarla 
+ Işleci **şundan büyük** olacak şekilde ayarlayın
+ Toplama türünü **Ortalama** olarak ayarlayın
+ Eşik değerini **500** olarak ayarlayın
+ Toplama dönemini **5 dakikaya** ayarla
+ Değerlendirme sıklığını **1 dakika** olarak ayarlayın
+ **Bitti** 'yi seçin  

> [!div class="mx-imgBorder"]
> ![Yapılandırılan uyarı mantığını vurgulayan ekran görüntüsü.](../media/alert-15.png)

### <a name="example-2-configuring-a-dynamic-alert-threshold-for-vault-saturation"></a>Örnek 2: kasa doygunluğu için dinamik bir uyarı eşiği yapılandırma 

Dinamik bir uyarı kullandığınızda, seçtiğiniz anahtar kasasının geçmiş verilerini görebilirsiniz. Mavi alan, anahtar kasaınızın ortalama kullanımını temsil eder. Kırmızı alanda, uyarı yapılandırmasındaki diğer ölçütler sağlandığında bir uyarının tetiklendiği ani artışlar gösterilmektedir. Kırmızı noktalar, uyarı ölçütlerinin toplanmış zaman penceresinde karşılandığı ihlallerin örneklerini gösterir. Belirlenen süre içinde belirli bir sayıdaki ihlalden sonra tetiklemek üzere bir uyarı ayarlayabilirsiniz. Geçmiş verileri dahil etmek istemiyorsanız, Gelişmiş ayarlarda aşağıdaki eski verileri dışarıda bırakma seçeneği vardır. 

> [!div class="mx-imgBorder"]
> ![Genel kasa doygunluğunun grafiğini gösteren ekran görüntüsü.](../media/alert-16.png)

Lütfen aşağıdaki yapılandırma parametrelerine bakın.

+ Eşiği **dinamik** olarak ayarla 
+ Işleci **şundan büyük** olacak şekilde ayarlayın
+ Toplama türünü **Ortalama** olarak ayarlayın
+ Eşik duyarlılığını **Orta** olarak ayarla
+ Toplama dönemini **5 dakikaya** ayarla
+ Değerlendirme sıklığını **1 dakika** olarak ayarlayın
+ **Isteğe bağlı** Gelişmiş ayarları Yapılandır 
+ **Bitti** 'yi seçin

> [!div class="mx-imgBorder"]
> ![Azure portal ekran görüntüsü](../media/alert-17.png)

5. Yapılandırdığınız eylem grubunu ekleyin

> [!div class="mx-imgBorder"]
> ![Bir eylem grubunun nasıl ekleneceğini gösteren ekran görüntüsü.](../media/alert-18.png)

6. Uyarıyı etkinleştirme ve önem derecesi atama

> [!div class="mx-imgBorder"]
> ![Uyarının nerede etkinleştirileceğini ve önem derecesi atanacağını gösteren ekran görüntüsü.](../media/alert-19.png)

7. Uyarı oluşturma 

### <a name="example-email-alert"></a>Örnek e-posta uyarısı 

> [!div class="mx-imgBorder"]
> ![Bir e-posta uyarısını yapılandırmak için gereken bilgileri vurgulayan ekran görüntüsü.](../media/alert-20.png)

## <a name="next-steps"></a>Sonraki adımlar

Tebrikler, artık bir izleme panosunu başarıyla oluşturdunuz ve anahtar kasanıza yönelik uyarıları yapılandırdınız! Yukarıdaki adımların tamamını tamamladıktan sonra, anahtar kasanızın yapılandırdığınız uyarı ölçütlerini karşılaması durumunda e-posta uyarıları almalısınız. Aşağıda bir örnek gösterilmiştir. Anahtar kasalarınızın sistem durumunu etkin bir şekilde izlemek için bu makalede ayarladığınız araçları kullanın. 


