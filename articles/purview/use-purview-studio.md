---
title: Azure purview Studio 'Yu kullanma
description: Bu makalede, Azure purview Studio 'Nun nasıl kullanılacağı açıklanır.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 04/02/2021
ms.openlocfilehash: ba22c322d47d8738b1d607597d6f93b8b8456616
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106283878"
---
# <a name="use-purview-studio"></a>Purview Studio'yu kullanma

Bu makale, Azure purview 'ın bazı ana özelliklerine genel bir bakış sunar.

## <a name="prerequisites"></a>Önkoşullar

* Azure portal 'de etkin bir purview hesabı zaten oluşturulmuş ve kullanıcının purview Studio 'ya erişme izinleri var.

## <a name="launch-purview-account"></a>Purview hesabını başlatın

* Purview hesabınızı başlatmak için, Azure portal ' de, Takiview hesaplarına gidin, başlatmak istediğiniz hesabı seçin ve hesabı başlatın.

  :::image type="content" source="./media/use-purview-studio/launch-from-portal.png" alt-text="Azure purview hesabı kataloğunu başlatmak için seçimin ekran görüntüsü.":::

* Purview hesabını başlatmaya yönelik başka bir yol `https://web.purview.azure.com` da hesabı başlatmak için **Azure Active Directory** ve hesap adı ' nı seçin.

## <a name="home-page"></a>Giriş sayfası

**Giriş** , Azure purview istemcisinin başlangıç sayfasıdır.

:::image type="content" source="./media/use-purview-studio/purview-homepage.png" alt-text="Giriş sayfasının ekran görüntüsü.":::

Aşağıdaki liste, ana **sayfanın** ana özelliklerini özetler. Listedeki her bir sayı, önceki ekran görüntüsünde vurgulanan bir sayıya karşılık gelir.

1. Kataloğun kolay adı. **Yönetim Merkezi**  >  **Hesap bilgilerinde** Katalog adı ayarlayabilirsiniz.

2. Katalog Analizi şu sayıyı gösterir:

   * Kullanıcılar, gruplar ve uygulamalar
   * Veri kaynakları
   * Varlıklar
   * Sözlük terimleri

3. Arama kutusu, veri kataloğu genelinde veri varlıkları aramanızı sağlar.

4. Hızlı erişim düğmeleri uygulamanın sık kullanılan işlevlerine erişim sağlar. Sunulan düğmeler, Kullanıcı hesabınıza atanan role göre değişir.

   * *Veri seçicisi* Için düğmeler **Bilgi Merkezi**, **varlıklara gözatıp**, **sözlüğü Yönet** ve **öngörüleri görüntüle**' dir.
   * *Veri okuyucu* için, öne çıkan düğmeler **Bilgi Merkezi**, **varlıklara gözatıp**, **sözlüğü görüntüle** ve **öngörüleri görüntüle**' dir.
   * *Veri kaynağı yönetici*  +  *verisi Seçicisi* için, öne çıkan düğmeler **Bilgi Merkezi**, **veri kaynaklarını kaydet**, **varlıklara gözatıp** ve **Sözlük yönetir**.
   * *Veri kaynağı Yöneticisi*  +  *veri okuyucusu* için, öne çıkan düğmeler **Bilgi Merkezi**, **veri kaynaklarını kaydetme**, **varlıklara gözatması** ve **Sözlük görünümü**' dir.

5. Sol gezinti çubuğu, uygulamanın ana sayfalarını bulmanıza yardımcı olur. Sunulan düğmeler, Kullanıcı hesabınıza atanan role göre değişir.

   * *Veri seçicisi* Için düğmeler **ana**, **Sözlük**, **Öngörüler** ve **Yönetim Merkezi**' dir.
   * *Veri okuyucu* Için düğmeler **ana**, **Sözlük**, **Öngörüler** ve **Yönetim Merkezi**' dir.
   * *Veri kaynağı Yöneticisi* veya *veri seçicisi/okuyucu* için düğmeler **ana**, **kaynak**, **Sözlük**, **Öngörüler** ve **Yönetim Merkezi**' dir.
  
6. **Son erişilen** sekme, son erişilen veri varlıklarının bir listesini gösterir. Varlıklara erişme hakkında daha fazla bilgi için bkz. [veri kataloğunu arama](how-to-search-catalog.md) ve [varlık türüne göre tarama](how-to-browse-catalog.md#browse-experience).  **Öğelerim** sekmesi, oturum açan kullanıcının sahip olduğu veri varlıklarının bir listesidir.
7. **Faydalı bağlantılar** bölge durumu, belgeler, fiyatlandırma, genel bakış ve takip durumu bağlantılarını içerir
8. Üst gezinti çubuğu sürüm notları/güncelleştirmeler, değişiklik takip hesabı, bildirimler, yardım ve geri bildirim bölümleri hakkında bilgi içerir.

## <a name="knowledge-center"></a>Bilgi Merkezi

Bilgi Merkezi, purview ile ilgili tüm videoları ve öğreticileri bulabileceğiniz yerdir.

## <a name="guided-tours"></a>Kılavuzlu Turlar

Azure purview Studio 'daki her UX, sayfaya genel bakış sağlamak için Kılavuzlu Turlar kullanacaktır. Kılavuzlu tura başlamak için üstteki çubukta **Yardım** ' ı seçin ve **Kılavuzlu Turları** seçin.

:::image type="content" source="./media/use-purview-studio/guided-tour.png" alt-text="Kılavuzlu turun ekran görüntüsü.":::

> [!Important]
> Tek başına *veri kaynağı Yöneticisi* rolünün, purview Studio 'ya erişimi yok.

## <a name="next-steps"></a>Sonraki adımlar

> [!div class="nextstepaction"]
> [Güvenlik sorumlusu ekleme](tutorial-scan-data.md)
