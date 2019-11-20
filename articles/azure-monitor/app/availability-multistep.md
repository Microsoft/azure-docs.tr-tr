---
title: Web uygulamanızı çok adımlı Web testleri ve Azure Application Insights izleyin | Microsoft Docs
description: Web uygulamalarınızı Azure Application Insights izlemek için çok adımlı Web testleri ayarlayın
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 10/23/2019
ms.reviewer: sdash
ms.openlocfilehash: 80a39151a3d40c9b9d7cb49c6ab41aab602c5991
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72817395"
---
# <a name="multi-step-web-tests"></a>Çok adımlı web testleri

Çok adımlı Web testleri aracılığıyla bir Web sitesiyle kaydedilmiş bir URL ve etkileşim dizisini izleyebilirsiniz. Bu makale, Visual Studio Enterprise ile çok adımlı bir Web testi oluşturma sürecinde size yol gösterecektir.

> [!NOTE]
> Çok adımlı Web testleri, Visual Studio WebTest dosyalarına bağımlıdır. Visual Studio 2019 ' nin, WebTest işlevselliğiyle ilgili son sürüm olacağı [duyurulmuştur](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/) . Yeni özellik eklenmadığında, Visual Studio 2019 ' deki WebTest işlevinin hala desteklenmekte olduğunu ve ürünün destek yaşam döngüsü sırasında desteklenmeye devam edecek olduğunu anlamak önemlidir. Azure Izleyici ürün ekibi, [buradaki](https://github.com/MicrosoftDocs/azure-docs/issues/26050#issuecomment-468814101)çok adımlı kullanılabilirlik testlerinin geleceği hakkında sorular buldu.  

## <a name="pre-requisites"></a>Önkoşullar

* Visual Studio 2017 Enterprise veya üzeri.
* Visual Studio Web performansı ve yük testi araçları.

Test araçlarını önkoşul olarak belirlemek için. **Web performans ve yük testi araçları** > **hata ayıklama ve test etme**  > **bireysel bileşenleri**  >  **Visual Studio yükleyicisi** başlatın.

![Web performans ve yük testi araçları için öğenin yanında bir onay kutusu ile seçili tek bileşenlere sahip Visual Studio Installer Kullanıcı arabiriminin ekran görüntüsü](./media/availability-multistep/web-performance-load-testing.png)

> [!NOTE]
> Çok adımlı Web testlerinde bunlarla ilişkili ek maliyetler vardır. Daha fazla bilgi edinmek için [resmi fiyatlandırma kılavuzuna](https://azure.microsoft.com/pricing/details/application-insights/)başvurun.

## <a name="record-a-multi-step-web-test"></a>Çok adımlı bir Web testini kaydetme 

> [!WARNING]
> Artık multi-step kaydedicisinin kullanılması önerilmez. Kaydedici, temel etkileşimleri olan statik HTML sayfaları için geliştirilmiştir ve modern web sayfaları için işlevsel bir deneyim sağlamıyor.

Visual Studio Web testleri oluşturma konusunda rehberlik için [resmi Visual studio 2019 belgelerine](https://docs.microsoft.com/visualstudio/test/how-to-create-a-web-service-test?view=vs-2019)bakın.

## <a name="upload-the-web-test"></a>Web testini karşıya yükleyin

1. Kullanılabilirlik bölmesindeki Application Insights portalında,**çok adımlı Web testi** >  test  > **test türü** **Oluştur** ' u seçin.

2. Test konumlarını, sıklığını ve uyarı parametrelerini ayarlayın.

### <a name="frequency--location"></a>Sıklık & konumu

|Ayar| Açıklama
|----|----|----|
|**Sınama sıklığı**| Testin her test konumundan ne sıklıkla çalıştırılacağını ayarlar. Beş dakikalık varsayılan sıklıkta ve beş test konumuyla, siteniz ortalama olarak dakikada bir test edilir.|
|**Test konumları**| , Sunucularımızın URL 'nize Web istekleri gönderdiğimiz yerdir. Web sitenizdeki sorunları ağ sorunlarından ayırabilmeniz için **en az önerilen test konumu sayısı beş ' dir** . En fazla 16 konum seçebilirsiniz.

### <a name="success-criteria"></a>Başarı ölçütleri

|Ayar| Açıklama
|----|----|----|
| **Test zaman aşımı** |Yavaş yanıtlar hakkında uyarı almak için bu değeri azaltın. Yanıtlar sitenizden bu süre içinde alınmadıysa test başarısız sayılır. **Bağımlı istekleri ayrıştır**’ı seçtiyseniz; tüm görüntüler, stil dosyaları, betikler ve diğer bağımlı kaynaklar bu süre içinde alınmış olmalıdır.|
| **HTTP yanıtı** | Başarılı olarak sayılan döndürülen durum kodu. 200, normal web sayfası döndürüldüğünü belirten koddur.|
| **İçerik eşleşmesi** | "Hoş geldiniz!" gibi bir dize Her yanıtta büyük küçük harfe duyarlı bir tam eşleşme oluştuğunu test edebiliriz. Joker karakter bulunmayan düz bir dize olmalıdır. Sayfanızın içeriği değişirse bunu güncelleştirmeniz gerektiğini unutmayın. **İçerik eşleşmesi ile yalnızca Ingilizce karakterler desteklenir** |

### <a name="alerts"></a>Uyarılar

|Ayar| Açıklama
|----|----|----|
|**Neredeyse gerçek zamanlı (Önizleme)** | Neredeyse gerçek zamanlı uyarıların kullanılması önerilir. Bu tür bir uyarının yapılandırılması, kullanılabilirlik testiniz oluşturulduktan sonra yapılır.  |
|**Klasik** | Artık yeni kullanılabilirlik testleri için klasik uyarıların kullanılması önerilmez.|
|**Uyarı konum eşiği**|En az 3/5 konum önerilir. Uyarı konumu eşiği ve test konumlarının sayısı arasındaki en iyi ilişki,**en az beş test konumu ile, test konumu sayısı-2**  =  **Uyarı konum eşiği** ' dir.|

## <a name="configuration"></a>Yapılandırma

### <a name="plugging-time-and-random-numbers-into-your-test"></a>Testinize zaman ve rastgele sayılar takma

Dış bir kaynağa ait stoklar gibi zamana bağımlı veriler alan bir aracı test ettiğinizi varsayalım. Web testinizi kaydettiğinizde, belirli zamanları kullanmanız gerekse de, bunları testin parametreleri (StartTime ve EndTime) olarak ayarlarsınız.

![Başar hisse senedi uygulaması ekran görüntüsü](./media/availability-multistep/app-insights-72webtest-parameters.png)

Testi çalıştırdığınızda, EndTime her zaman geçerli zaman, StartTime da 15 dakika öncesi olmalıdır.

Web testi tarih saat eklentisi Parametreleştirme süresini işlemek için bir yol sağlar.

1. İstediğiniz her değişken parametre değeri için bir web testi eklentisi ekleyin. Web testi araç çubuğunda, **Web Testi Eklentisi Ekle**’yi seçin.
    
    ![Web testi eklentisi Ekle](./media/availability-multistep/app-insights-72webtest-plugin-name.png)
    
    Bu örnekte, Tarih Saat Eklentisinin iki örneğini kullanacağız. Bir örnek "15 dakika önce" için, bir örnek de "şimdi" için.

2. Her eklentinin özelliklerini açın. Buna bir ad verip geçerli saat olarak kullanılmak üzere ayarlayın. Bunlardan birini Dakika Ekle = 15 olarak ayarlayın.

    ![Bağlam parametreleri](./media/availability-multistep/app-insights-72webtest-plugin-parameters.png)

3. Web testi parametrelerinde, eklenti adına başvurmak için {{plug-in name}} kullanın.

    ![StartTime](./media/availability-multistep/app-insights-72webtest-plugins.png)

Artık testi portala yükleyin. Testin her çalıştırılışında dinamik değerler kullanacaktır.

### <a name="dealing-with-sign-in"></a>Oturum açmayla ilgilenme

Kullanıcılarınız uygulamanızda oturum açarsa, oturum açma benzetimi için bir dizi seçeneğiniz vardır; böylece, oturum açmanın ötesinde sayfaları test edebilirsiniz. Kullandığınız yaklaşım, uygulamanın sağladığı güvenlik türüne bağlıdır.

Her durumda, uygulamanızda yalnızca test amacıyla bir hesap oluşturmalısınız. Mümkünse, web testlerinin gerçek kullanıcıları etkileme olasılığını önlemek için test hesabının izinlerini kısıtlayın.

**Basit Kullanıcı adı ve parola** Bir Web testini her zamanki şekilde kaydedin. Önce tanımlama bilgilerini silin.

**SAML kimlik doğrulaması**

|Özellik adı| Açıklama|
|----|-----|
| Hedef kitle Uri 'Si | SAML belirteci için hedef kitle URI 'SI.  Bu, ACS ad alanı ve ana bilgisayar adı da dahil olmak üzere Access Control Service (ACS) URI 'sidir. |
| Sertifika parolası | Katıştırılmış özel anahtara erişim sağlayacak istemci sertifikası parolası. |
| İstemci sertifikası  | Base64 kodlamalı biçimde özel anahtara sahip istemci sertifikası değeri. |
| Ad tanımlayıcısı | Belirtecin ad tanımlayıcısı |
| Sonra değil | Belirtecin geçerli olacağı TimeSpan.  Varsayılan değer 5 dakikadır. |
| Önce değil | Geçmişte oluşturulan belirtecin geçerli olacağı TimeSpan değeri geçerli olacaktır (zaman eğilerini gidermek için).  Varsayılan değer (negatif) 5 dakikadır. |
| Hedef bağlam parametresi adı | Oluşturulan onayı alacak bağlam parametresi. |


**İstemci parolası** Uygulamanızda, bir istemci gizli anahtarı içeren bir oturum açma yolu varsa, bu yolu kullanın. Azure Active Directory (AAD), gizli anahtarla oturum açmayı sağlayan bir hizmet örneğidir. AAD’de gizli anahtar, Uygulama Anahtarı’dır.

Aşağıda uygulama anahtarı kullanan bir Azure web uygulaması için web testi örneği verilmiştir:

![Örnek ekran görüntüsü](./media/availability-multistep/client-secret.png)

Gizli anahtar (AppKey) kullanarak AAD’den belirteç alın.
Yanıttan taşıyıcı belirteci ayıklayın.
Yetkilendirme üst bilgisinde taşıyıcı belirteç kullanarak API çağırın.
Web testinin gerçek bir istemci olduğundan emin olun; diğer bir deyişle, AAD 'de kendi uygulamasına sahiptir ve Clıtıd + uygulama anahtarını kullanın. Test kapsamındaki hizmetiniz de AAD 'de kendi uygulamasına sahiptir: Bu uygulamanın AppID URI 'SI, kaynak alanındaki Web testinde yansıtılır.

### <a name="open-authentication"></a>Açık Kimlik Doğrulaması
Microsoft veya Google hesabınızla oturum açma, bir açık kimlik doğrulaması örneğidir. OAuth kullanan çok sayıda uygulama, alternatif gizli anahtar da sağlar; bu nedenle ilk taktiğiniz bu olasılığın incelenmesi olmalıdır.

Testinizde OAuth kullanılarak oturum açılması gerekiyorsa, genel yaklaşım şöyledir:

Web tarayıcınız, kimlik doğrulama sitesi ve uygulamanız arasındaki trafiği incelemek için Fiddler gibi bir araç kullanın.
Farklı makineler veya tarayıcılar kullanarak veya uzun aralıklarla (süresi dolacak şekilde belirteçleri izin vermek için) iki veya daha fazla oturum açın.
Farklı oturumları karşılaştırarak, kimlik doğrulama sitesinden geri geçirilen belirteci tanımlayın; başka bir deyişle oturum açıldıktan sonra uygulama sunucunuza geçirilen belirteç.
Visual Studio’yu kullanarak web testini kaydetme
Belirteçleri parametreleyin; belirteç kimlik doğrulayıcıdan döndürüldüğünde ve sitede sorgu sırasında kullanıldığında parametre ayarı. (Visual Studio testi parametrelemeyi dener, ancak belirteçleri doğru parametrelemez.)

## <a name="troubleshooting"></a>Sorun giderme

Adanmış [sorun giderme makalesi](troubleshoot-availability.md).

## <a name="next-steps"></a>Sonraki adımlar

* [Kullanılabilirlik uyarıları](availability-alerts.md)
* [URL ping Web testleri](monitor-web-app-availability.md)
