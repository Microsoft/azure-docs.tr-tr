---
title: Drone canlandırın al
description: İş ortaklarından drtek canlandırın 'nin nasıl alınacağını açıklar
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 6ee4391369744f966ce273697e5ba9e7f0590edd
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73890988"
---
# <a name="get-drone-imagery-from-drone-partners"></a>Drone iş ortaklarından drone canlandırın al

Bu makalede, içindeki IBir Imagery iş ortakınızdan ormozaik verileri Azure Farmtts veri merkezine nasıl getirebileceğinizi açıklanmaktadır. Ormozaik, geometrik olarak düzeltilen ve drone tarafından toplanan verilerden alınan bir havadan oluşan bir çizim/görüntüdür.

Şu anda aşağıdaki Imagery iş ortakları desteklenmektedir.

  ![Proje grubu ları](./media/get-drone-imagery-from-drone-partner/drone-partner-1.png)

Azure farmile drone canlandırın verilerini tümleştirmek, grubunuzda veri hub 'ında yaptığınız drone fışıklardan ormozaik veri almanıza yardımcı olur. Veriler kullanılabilir olduktan sonra, bu verileri Farmtts hızlandırıcısında görüntüleyebilir ve Data Fusion ve AI/ML model oluşturma için de kullanılabilir.

## <a name="before-you-begin"></a>Başlamadan önce

  - Azure Farmtempts 'yi dağıttığınızdan emin olun. Dağıtmak için, bkz. [Farmtts dağıtımı](prepare-for-deployment.md).
  - Farmtts sisteminizde tanımlanmış gruba (drbir imagery istediğiniz) sahip olduğunuzdan emin olun.

## <a name="enable-drone-imagery-integration-with-farmbeats"></a>Farmıts ile drone Imagery tümleştirmesini etkinleştirme   

Farmtempts ile tümleştirmeyi etkinleştirmek için cihaz sağlayıcınıza aşağıdaki bilgileri sağlamanız gerekir:  
 - API Uç Noktası  
 - Kiracı Kimliği  
 - İstemci Kimliği  
 - İstemci parolası  

Aşağıdaki adımları kullanın:

1. Bu [betiği](https://aka.ms/farmbeatspartnerscript) indirip yerel sürücünüzde ayıklayın. Bu ZIP dosyası içinde iki dosya bulacaksınız.  
2. [Azure Portal](https://portal.azure.com/) oturum açın ve Cloud Shell açın (Bu seçenek portalın sağ üst çubuğunda bulunur).   

    ![Proje grubu ları](./media/get-drone-imagery-from-drone-partner/navigation-bar-1.png)

3. Ortamın **PowerShell** olarak ayarlandığından emin olun

    ![Proje grubu ları](./media/get-drone-imagery-from-drone-partner/power-shell-new-1.png)

4. Cloud Shell indirdiğiniz iki dosyayı karşıya yükleyin (yukarıdaki 1. adım).  

    ![Proje grubu ları](./media/get-drone-imagery-from-drone-partner/power-shell-two-1.png)

5. Dosyaların karşıya yüklendiği dizine gidin. (Varsayılan olarak, > Kullanıcı adı giriş dizinine yüklenir.)  
6. Şu betiği çalıştırın:

    ```azurepowershell-interactive 

    ./generateCredentials.ps1   

    ```

7. API uç noktası, kiracı KIMLIĞI, Istemci KIMLIĞI, Istemci gizli anahtarı ve EventHub bağlantı dizesinin değerlerini yakalamak için ekran yönergelerini izleyin.

    Gerekli kimlik bilgilerini ortağın drone yazılım sistemine girdikten sonra, tüm grupları Farmtts sisteminden içeri aktarabilir ve Grup ayrıntılarını kullanarak uçuş yolu planlamasını ve drone görüntü koleksiyonunuzu gerçekleştirebilirsiniz.

    Ham görüntüler, drone sağlayıcılarının yazılımı tarafından işlendikten sonra, drone yazılım sistemi, veri merkezine, bulunan ormozaik ve diğer işlenen görüntüleri yükler.

## <a name="view-drone-imagery"></a>Drone canlandırın 'yi görüntüle

Veriler Farmtts veri merkezine gönderildikten sonra, Farmtts veri merkezi API 'Lerini kullanarak sahne mağazasını sorgulayabilmelisiniz.

Alternatif olarak, **Grup ayrıntıları** sayfasında en son drone görüntüsünü görüntüleyebilmelisiniz. Görüntülemek için şu adımları izleyin:  

1. Canlandırın 'nizin karşıya yüklendiği grubu seçin. **Grup** ayrıntıları sayfası görüntülenir.
2. En son **duyarlık haritaları** bölümüne gidin.
3. Görüntüyü **drone Imagery** bölümünde görüntüleyebilmelisiniz.

    ![Proje grubu ları](./media/get-drone-imagery-from-drone-partner/drone-imagery-1.png)

## <a name="download-drone-imagery"></a>Drone canlandırın 'yi indirin

Drone Imagery bölümünü seçtiğinizde, drone diklik 'in yüksek çözünürlüklü görüntüsünü göstermek için bir açılır pencere açılır.

![Proje grubu ları](./media/get-drone-imagery-from-drone-partner/download-drone-imagery-1.png)

## <a name="view-all-drone-maps"></a>Tüm drone haritalarını görüntüle

Drone sağlayıcısı tarafından karşıya yüklenen dosyalar ve görüntüler haritalar bölümünde görünür. **Haritalar** bölümünü seçin, **gruba** göre filtreleyin ve görüntülemek ve indirmek için uygun dosyaları seçin:

  ![Proje grubu ları](./media/get-drone-imagery-from-drone-partner/view-drone-maps-1.png)

## <a name="next-steps"></a>Sonraki adımlar

Tek bir imagerinizi almak için Farmtts veri merkezi [API 'lerini](references-for-farmbeats.md#rest-api) nasıl kullanacağınızı öğrenin.
