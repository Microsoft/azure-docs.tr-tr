---
title: Azure purview 'da Teradata kaynağını kaydetme ve tarama (Önizleme)
description: Bu makalede, Azure purview 'a bir Teradata kaynağı kaydetme ve tarama ayarlama özetlenmektedir.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: overview
ms.date: 2/25/2021
ms.openlocfilehash: 8f300f214ed36b7a5257b7276364027b91edc746
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105048111"
---
# <a name="register-and-scan-teradata-source-preview"></a>Teradata kaynağını kaydetme ve tarama (Önizleme)

Bu makalede, bir Teradata kaynağını Takiview 'a kaydetme ve tarama ayarlama özetlenmektedir.

## <a name="supported-capabilities"></a>Desteklenen özellikler

Teradata kaynağı bir Teradata veritabanından meta verileri ayıklamak için **tam taramayı** destekler ve veri varlıkları arasında **kökenini** getirir.

## <a name="prerequisites"></a>Önkoşullar

1.  En son [kendi kendine barındırılan tümleştirme çalışma zamanını](https://www.microsoft.com/download/details.aspx?id=39717)ayarlayın.
    Daha fazla bilgi için, bkz. [Şirket içinde barındırılan tümleştirme çalışma zamanı oluşturma ve yapılandırma](../data-factory/create-self-hosted-integration-runtime.md).

2.  Şirket içinde barındırılan tümleştirme çalışma zamanının yüklü olduğu sanal makinenizde [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) ' in yüklü olduğundan emin olun.

3.  \"Visual C++ Redistributable 2012 güncelleştirme 4 ' ün \" Şirket içinde barındırılan tümleştirme çalışma zamanı makinesinde yüklü olduğundan emin olun. \'Henüz yüklemediyseniz, [buradan](https://www.microsoft.com/download/details.aspx?id=30679)indirin.

4.  Şirket içinde barındırılan tümleştirme çalışma zamanının çalıştığı sanal makinenizde Teradata 'un JDBC sürücüsünü el ile indirmeniz gerekir.
    Yürütülebilir JAR dosyası, Teradata [Web sitesinden](https://downloads.teradata.com/)indirilebilir.

    > [!Note]
    > Sürücü, VM 'deki tüm hesaplara erişebilmelidir. Lütfen bir kullanıcı hesabına yüklemeyin.

5.  Desteklenen Teradata veritabanı sürümleri 12. x ile 16. x arasında. Taranmakta olan Teradata kaynağına okuma erişiminizin olduğundan emin olun.

## <a name="setting-up-authentication-for-a-scan"></a>Tarama için kimlik doğrulamasını ayarlama

Bir Teradata kaynağı için desteklenen tek kimlik doğrulaması **temel kimlik doğrulamadır**.

## <a name="register-a-teradata-source"></a>Teradata kaynağını kaydetme

Veri kataloğunuza yeni bir Teradata kaynağı kaydetmek için aşağıdakileri yapın:

1.  Purview hesabınıza gidin.
2.  Sol gezinti çubuğunda **kaynaklar** ' ı seçin.
3.  **Kaydol** ' u seçin
4.  Kayıt kaynakları üzerinde **Teradata**' yi seçin. **Devam**'ı seçin

    :::image type="content" source="media/register-scan-teradata-source/register-sources.png" alt-text="Teradata seçeneklerini kaydetme" border="true":::

**Kaynakları Kaydet (Teradata)** ekranında şunları yapın:

1.  Veri kaynağının katalogda listelenecek bir **ad** girin.

2.  Bir Teradata kaynağına bağlanmak için **ana bilgisayar** adını girin. Ayrıca sunucuya bir IP adresi veya tam bir bağlantı dizesi de olabilir.

3.  Bir koleksiyon seçin veya yeni bir tane oluşturun (Isteğe bağlı)

4.  Veri kaynağını kaydetmek için son ' a gidin.

    :::image type="content" source="media/register-scan-teradata-source/register-sources-2.png" alt-text="Teradata 'yi Kaydet" border="true":::

## <a name="creating-and-running-a-scan"></a>Tarama oluşturma ve çalıştırma

Yeni bir tarama oluşturup çalıştırmak için aşağıdakileri yapın:

1.  Yönetim Merkezi 'nde **tümleştirme çalışma zamanları**' na tıklayın. Şirket içinde barındırılan bir tümleştirme çalışma zamanının ayarlandığından emin olun. Ayarlanmamışsa, şirket içinde barındırılan bir tümleştirme çalışma zamanı ayarlamak için [burada](./manage-integration-runtimes.md) bahsedilen adımları kullanın

2.  **Kaynaklara** gidin

3.  Kayıtlı Teradata kaynağını seçin.

4.  **+ Yeni tarama** seçin

5.  Aşağıdaki ayrıntıları sağlayın:

    a.  **Ad**: taramanın adı

    b.  **Tümleştirme çalışma zamanı aracılığıyla Bağlan**: yapılandırılmış şirket içinde barındırılan tümleştirme çalışma zamanını seçin.

    c.  **Kimlik bilgisi**: veri kaynağınıza bağlanacak kimlik bilgisini seçin. Şunları yaptığınızdan emin olun:

    -   Kimlik bilgisi oluştururken temel kimlik doğrulaması ' nı seçin.
    -   Kullanıcı adı giriş alanındaki veritabanı sunucusuna bağlanmak için bir Kullanıcı adı belirtin
    -   Veritabanı sunucusu parolasını gizli anahtar olarak depolayın.

        Kimlik bilgileri hakkında daha fazla bilgi edinmek için [buradaki](./manage-credentials.md) bağlantıya bakın

6.  **Şema**: içeri aktarılacak, noktalı virgülle ayrılmış bir liste olarak ifade edilen şemaların alt kümesini listeleyin. ör., Schema1; schema2. Tüm Kullanıcı şemaları, bu liste boşsa içeri aktarılır. Tüm sistem şemaları (örneğin, SysAdmin) ve nesneler varsayılan olarak yok sayılır. Liste boş olduğunda, kullanılabilir tüm şemalar içeri aktarılır.

    SQL LIKE ifadeleri sözdizimi kullanan kabul edilebilir şema adı desenleri%, örneğin%; Kenarı % C%; TID
    - veya ile başlayın    
    - B veya ile bitir    
    - C veya içerir    
    - eşit D

    NOT ve özel karakterlerin kullanımı kabul edilemez

7.  **Sürücü konumu**: sanal makinenizde kendi kendine barındırma tümleştirmesi çalışma ZAMANıNıN çalıştığı JDBC sürücü konumunun yolunu belirtin. Bu, geçerli JAR klasörü konumunun yolu olmalıdır.

8.  **Kullanılabilir maksimum bellek:** Müşterinin VM 'sinde, işlem tarama tarafından kullanılacak maksimum bellek (GB cinsinden). Bu, taranacak Teradata kaynağının boyutuna bağlıdır.

    > [!Note] 
    > Thumb kuralı olarak, lütfen her 1000 tablo için 2GB bellek sağlayın

    :::image type="content" source="media/register-scan-teradata-source/setup-scan.png" alt-text="Kurulum taraması" border="true":::

6.  **Devam**' a tıklayın.

7.  **Tarama tetikleyiciyi** seçin. Bir zamanlama ayarlayabilir veya taramayı bir kez çalıştırabilirsiniz.

8.  Taramayı gözden geçirin ve **Kaydet ve Çalıştır**' a tıklayın.

## <a name="viewing-your-scans-and-scan-runs"></a>Taramalarınızı ve tarama çalıştırmalarını görüntüleme

1. Yönetim merkezine gidin. **Kaynaklar ve tarama** bölümünde **veri kaynakları** ' nı seçin.

2. İstediğiniz veri kaynağını seçin. Bu veri kaynağındaki mevcut taramaların bir listesini görürsünüz.

3. Sonuçları görüntülemeye ilgilendiğiniz taramayı seçin.

4. Bu sayfada, her bir tarama çalışmasının ölçümlerinin ve durumlarının yanı sıra önceki tüm tarama çalışmalarının hepsi gösterilecektir. Ayrıca, taramalarınızın zamanlandığını veya el ile, kaç varlık sınıflandırdığını, kaç tane varlık olduğunu, taramanın başlangıç ve bitiş zamanını ve toplam tarama süresini de görüntüler.

## <a name="manage-your-scans"></a>Taramalarınızı yönetin

Bir taramayı yönetmek veya silmek için aşağıdakileri yapın:

1. Yönetim merkezine gidin. **Kaynaklar ve tarama** bölümünde **veri kaynakları** ' nı seçin ve ardından istediğiniz veri kaynağını seçin.

2. Yönetmek istediğiniz taramayı seçin. Taramayı **Düzenle** seçeneğini belirleyerek düzenleyebilirsiniz.

3. , **Sil**' i seçerek taramayı silebilirsiniz.

## <a name="next-steps"></a>Sonraki adımlar

- [Azure purview veri kataloğuna gözatın](how-to-browse-catalog.md)
- [Azure purview Veri Kataloğu 'Nda arama](how-to-search-catalog.md)