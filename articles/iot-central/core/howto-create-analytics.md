---
title: Azure IoT Central uygulamanızda cihaz Verilerinizi çözümleyin | Microsoft Docs
description: Bu makalede sorguları ve görselleştirmeleri kullanarak Azure IoT Central uygulamanızda cihaz verilerinizi nasıl analiz edileceği açıklanmaktadır.
author: lmasieri
ms.author: lmasieri
ms.date: 06/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: a467e0e6e8967cf963ad099f83de6718330aa43f
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827972"
---
# <a name="how-to-use-analytics-to-analyze-your-device-data"></a>Analiz kullanarak cihaz verilerinizi çözümleme

*Bu makale, işleçler, oluşturucular ve yöneticiler için geçerlidir.*

Azure IoT Central, cihazlarınızdan büyük miktarda verileri anlamlı hale getirmek için zengin analiz özellikleri sağlar. Başlamak için sol bölmedeki **analiz** ' i ziyaret edin.

## <a name="querying-your-data"></a>Verilerinizi sorgulama

Bir **cihaz kümesi**seçmeniz, **filtre** eklemeniz (isteğe bağlı) ve başlamak için bir **zaman aralığı** seçmeniz gerekir. İşiniz bittiğinde, verileri görselleştirmeye başlamak için **sonuçları göster** ' i seçin.

* **Cihaz kümeleri:** [Cihaz kümesi](howto-use-device-sets.md) , cihazlarınızın Kullanıcı tanımlı grubudur. Örneğin, Oakland 'deki tüm Soğutıcılar veya tüm Rev 2,0 rüzgar türbines.

* **Filtreler:** İsteğe bağlı olarak, verilerinize odaklanmak için filtre ekleyebilirsiniz. Tek seferde en fazla 10 filtre ekleyebilirsiniz. Örneğin, Oakland 'deki tüm soğutma dahilinde sıcaklığın 60 derecenin üzerinde olduğunu fark etmiş olun.
* **Zaman aralığı:** Varsayılan olarak, son 10 dakikadan verileri alacağız. Bu değeri, önceden tanımlanmış zaman aralıklarından biriyle değiştirebilir veya özel bir zaman aralığı seçebilirsiniz.

  ![Analiz sorgusu](media/howto-create-analytics/analytics-query.png)

## <a name="visualizing-your-data"></a>Verilerinizi görselleştirme

Verilerinizi sorguladıktan sonra, görselleştirmeyi başlatabileceksiniz. Ölçümleri gösterebilir/gizleyebilir, verilerin toplanma şeklini değiştirebilir ve verileri farklı cihaz özelliklerine göre daha fazla bölebilirsiniz.  

* **Bölme ölçütü:** Verileri cihaz özelliklerine göre bölmek verilerinize daha fazla ayrıntıya erişmenizi sağlar. Örneğin, sonuçlarınızı cihaz KIMLIĞINE veya konuma göre bölebilirsiniz.

* **Ölçümler:** Tek seferde cihazlarınızın rapor ettiği 10 adede kadar farklı telemetri öğesini göstermeyi/gizlemeyi seçebilirsiniz. Ölçümler sıcaklık ve nem gibi şeylerdir.

* **Toplama:** Varsayılan olarak, verileri ortalamasına göre topladık, ancak veri toplamayı gereksinimlerinize uyacak şekilde başka bir şeye değiştirmeyi de seçebilirsiniz.

   ![Analiz görselleştirmesi bölünmüş](media/howto-create-analytics/analytics-splitby.png)

## <a name="interacting-with-your-data"></a>Verilerinizle etkileşim kurma

Görsel öğeler gereksinimlerinizi karşılayacak sorgu sonuçlarınızı değiştirmek için çeşitli yöntemlere sahipsiniz. Grafik görünümü ve kılavuz görünümü arasında gidip, veri kümesini yenileyebilir ve satırların nasıl gösterileceğini değiştirebilirsiniz.

* **Kılavuzu göster:** Sonuçlarınız, her bir veri noktası için belirli bir değeri görüntülemenize olanak sağlayan bir tablo biçiminde kullanılabilir. Bu görünüm erişilebilirlik standartlarını de karşılar.
* **Grafiği göster:** Sonuçlarınız, yukarı veya aşağı eğilimleri ve anormallikleri belirlemenize yardımcı olmak için bir satır biçiminde görüntülenir.

  ![Analizlerinizin kılavuz görünümünü gösterme](media/howto-create-analytics/analytics-showgrid.png)

Yakınlaştırma, verilerinize giriş yapmanızı sağlar. Sonuç kümesi içinde odaklanmak istediğiniz bir zaman dilimi bulursanız, imlecinizi kullanarak yakınlaştırmak istediğiniz alanı alın ve aşağıdaki eylemlerden birini gerçekleştirmek için kullanılabilir denetimleri kullanın:

* **Yakınlaştır:** Bir zaman aralığı seçtikten sonra yakınlaştırma etkin olur ve verilerinize yakınlaştırmanızı sağlar.
* **Uzaklaştır:** Bu denetim, son yakınlaştırmadan bir düzey büyütme yapmanızı sağlar. Örneğin, verilerinizi üç kez yakınlaştırdıysanız, ölçeği bir seferde bir adım geri götürür.
* **Yakınlaştırma sıfırlandı:** Çeşitli yakınlaştırma düzeylerini gerçekleştirdikten sonra, özgün sonuç kümesine dönmek için yakınlaştırma sıfırlama denetimini kullanabilirsiniz.

  ![Verilerinize yakınlaştırma yapma](media/howto-create-analytics/analytics-zoom.png)

Satır stilini gereksinimlerinizi karşılayacak şekilde değiştirebilirsiniz. Dört seçeneğiniz vardır:

* **Satır:** Her veri noktası arasında düz bir çizgi.
* **Düzgünleştir:** Her nokta arasında eğri çizgi.
* **Adım:** Grafikteki her nokta arasındaki çizgi bir adımdır.
* **Dağılım:** Tüm noktaları, hiçbir satır bağlamadan grafiğe işaret etmeden çizilir.

  ![Analiz içinde farklı satır türleri var](media/howto-create-analytics/analytics-linetypes.png)

Son olarak, üç moddan birini seçerek verilerinizi Y ekseni üzerinde düzenleyebilirsiniz:

* **Yığılmış:** Her ölçüm için bir grafik yığılmış ve grafiklerin her birinin kendi Y ekseni vardır. Yığın grafikler, birden fazla ölçüm seçildiğinde ve bu ölçümlerin ayrı bir görünümünü kullanmak istediğinizde faydalıdır.
* **Yığın olmayan:** Her ölçü için bir grafik bir Y eksenine göre çizilir, ancak Y ekseninin değerleri vurgulanan ölçüye göre değiştirilir. Yığın olmayan grafikler, birden çok ölçüyü kaplaması istediğinizde ve aynı zaman aralığı için bu ölçülerdeki desenleri görmek istediğinizde yararlıdır.
* **Paylaşılan Y ekseni:** Tüm grafikler aynı Y eksenini paylaşır ve eksenin değerleri değişmez. Paylaşılan Y ekseni grafikleri, verileri bölünmüş bir şekilde Dilimleme sırasında tek bir ölçüye bakmak istediğinizde faydalıdır.

  ![Farklı görselleştirme modlarıyla y ekseni genelinde veri düzenleme](media/howto-create-analytics/analytics-yaxis.png)

## <a name="next-steps"></a>Sonraki adımlar

Azure IoT Central uygulamanız için özel analiz oluşturmayı öğrendiğinize göre, önerilen sonraki adım aşağıda verilmiştir:

> [!div class="nextstepaction"]
> [Node. js uygulaması hazırlama ve bağlama](howto-connect-nodejs.md)