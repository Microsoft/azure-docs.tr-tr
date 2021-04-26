---
title: Azure purview 'da SAP S/4HANA kaynak ve kurulum taramalarını (Önizleme) kaydetme
description: Bu makalede, SAP S/4HANA kaynağının Azure 'da nasıl kaydedileceği ve bir tarama ayarlandığı özetlenmektedir.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: overview
ms.date: 2/25/2021
ms.openlocfilehash: 7e00332eca076b55a884682240ef26ea3a4546d1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105046071"
---
# <a name="register-and-scan-a-sap-s4hana-source-preview"></a>SAP S/4HANA kaynağını kaydetme ve tarama (Önizleme)

Bu makalede bir SAP S/4HANA kaynağını Takiview 'a kaydetme ve tarama ayarlama özetlenmektedir.

## <a name="supported-capabilities"></a>Desteklenen özellikler

SAP S/4HANA kaynağı, bir SAP S/4HANA örneğinden meta verileri ayıklamak üzere **tam taramayı** destekler ve veri varlıkları arasında **kökenini** getirir.

## <a name="prerequisites"></a>Önkoşullar

1.  En son [kendi kendine barındırılan tümleştirme çalışma zamanını](https://www.microsoft.com/download/details.aspx?id=39717)ayarlayın.
    Daha fazla bilgi için, bkz. [Şirket içinde barındırılan tümleştirme çalışma zamanı oluşturma ve yapılandırma](../data-factory/create-self-hosted-integration-runtime.md).

2.  Şirket içinde barındırılan tümleştirme çalışma zamanının yüklü olduğu sanal makinenizde [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) ' in yüklü olduğundan emin olun.

3.  \"Visual C++ Redistributable 2012 güncelleştirme 4 ' ün \" Şirket içinde barındırılan tümleştirme çalışma zamanı makinesinde yüklü olduğundan emin olun. \'Henüz yüklemediyseniz, [buradan](https://www.microsoft.com/download/details.aspx?id=30679)indirin.

4.  [Microsoft .NET 3,0 için](https://support.sap.com/en/product/connectors/msnet.html) 64-bit SAP BAĞLAYıCıSıNı, SAP \' s Web sitesinden indirin ve şirket içinde barındırılan tümleştirme çalışma zamanı makinesine yükleyin. Yükleme sırasında, **Isteğe bağlı kurulum adımları** penceresindeki **GAC 'ye derlemeleri yükleme** seçeneğini seçtiğinizden emin olun.

    :::image type="content" source="media/register-scan-saps4hana-source/requirement.png" alt-text="Önkoşul" border="true":::

5.  Bağlayıcı, Java Bağlayıcısı (JCo) 3,0 API 'sini kullanarak SAP 'den meta verileri okur. Bu nedenle, şirket içinde barındırılan tümleştirme çalışma zamanının yüklü olduğu sanal makinenizde Java bağlayıcısının kullanılabildiğinden emin olun.
    Ortamınız için doğru JCo dağıtımını kullandığınızdan emin olun. Örneğin, bir Microsoft Windows makinesinde, sapjco3. jar ve sapjco3.dll dosyalarının kullanılabildiğinden emin olun.

    > [!Note] 
    >Sürücü, VM 'deki tüm hesaplara erişebilmelidir. Bunu bir kullanıcı hesabına yüklemeyin.

6.  [ABAP Functions dağıtım kılavuzunda](abap-functions-deployment-guide.md)bahsedilen ADıMLARı izleyerek SAP sunucusunda meta VERI ayıklama ABAP işlev modülünü dağıtın. SAP sunucusunda RFC işlevi modülünü oluşturmak için bir ABAP Developer hesabına ihtiyacınız olacaktır. Kullanıcı hesabı, SAP sunucusuna bağlanmak ve aşağıdaki RFC işlev modüllerini yürütmek için yeterli izinlere sahip olmalıdır:
    -   STFC_CONNECTION (bağlantıyı denetle)
    -   RFC_SYSTEM_INFO (sistem bilgilerini denetle)

## <a name="setting-up-authentication-for-a-scan"></a>Tarama için kimlik doğrulamasını ayarlama

SAP S/4HANA kaynağı için desteklenen tek kimlik doğrulaması **temel kimlik doğrulamasıdır**

## <a name="register-sap-s4hana-source"></a>SAP S/4HANA kaynağını Kaydet

Veri kataloğunuza yeni bir SAP S/4HANA kaynağı kaydetmek için aşağıdakileri yapın:

1.  Purview hesabınıza gidin.
2.  Sol gezinti çubuğunda **kaynaklar** ' ı seçin.
3.  **Kaydol** ' u seçin
4.  Kayıt kaynakları üzerinde **SAP S/4HANA** ' yı seçin. **Devam**'ı seçin

    :::image type="content" source="media/register-scan-saps4hana-source/register-saps-4-hana.png" alt-text="SAPS/4Hana seçeneklerini Kaydet" border="true":::

**Kaynakları Kaydet (SAP S/4HANA)** ekranında şunları yapın:

1.  Veri kaynağının Katalog içinde listeleneceği bir **ad** girin.

2.  SAP S/4HANA kaynağına bağlanmak için **uygulama sunucusu** adını girin. SAP uygulama sunucusu konağının bir IP adresi de olabilir.

3.  SAP **sistem numarasını** girin. Bu, 00 ile 99 arasında iki basamaklı bir tamsayıdır.

4.  Bir koleksiyon seçin veya yeni bir tane oluşturun (Isteğe bağlı)

5.  Veri kaynağını kaydetmek için son ' a gidin.

    :::image type="content" source="media/register-scan-saps4hana-source/register-saps-4-hana-2.png" alt-text="SAP S/4HANA 'yı Kaydet" border="true":::

## <a name="creating-and-running-a-scan"></a>Tarama oluşturma ve çalıştırma

Yeni bir tarama oluşturup çalıştırmak için aşağıdakileri yapın:

1.  Yönetim Merkezi 'nde tümleştirme çalışma zamanları ' na tıklayın. Şirket içinde barındırılan bir tümleştirme çalışma zamanının ayarlandığından emin olun. Ayarlanmamışsa, şirket içinde barındırılan tümleştirme çalışma zamanı oluşturmak için [burada](./manage-integration-runtimes.md) bahsedilen adımları kullanın

2.  Kaynaklara gidin **.**

3.  Kayıtlı SAP S/4HANA kaynağını seçin.

4.  **+ Yeni tarama** seçin

5.  Aşağıdaki ayrıntıları sağlayın:

    a.  **Ad**: taramanın adı

    b.  **Tümleştirme çalışma zamanı aracılığıyla Bağlan**: yapılandırılmış şirket içinde barındırılan tümleştirme çalışma zamanını seçin.

    c.  **Kimlik bilgisi**: veri kaynağınıza bağlanacak kimlik bilgisini seçin. Şunları yaptığınızdan emin olun:

    -   Kimlik bilgisi oluştururken temel kimlik doğrulaması ' nı seçin.
    -   Kullanıcı adı giriş alanındaki SAP sunucusuna bağlanmak için bir kullanıcı KIMLIĞI sağlayın.
    -   Gizli anahtar içinde SAP sunucusuna bağlanmak için kullanılan Kullanıcı parolasını depolayın.

    d.  **ISTEMCI kimliği:** SAP sistem istemci KIMLIĞINI buraya girin. İstemci, 000 ile 999 arasında üç basamaklı sayısal sayıyla tanımlanır.

    e.  **JCO kitaplığı yolu**: JCO kitaplıklarının bulunduğu klasörün yolunu belirtin.

    f.  **Kullanılabilir maksimum bellek:** Müşterinin VM 'sinde, işlem tarama tarafından kullanılacak maksimum bellek (GB cinsinden). Bu, taranacak SAP S/4HANA kaynağının boyutuna bağlıdır.

    :::image type="content" source="media/register-scan-saps4hana-source/scan-saps-4-hana.png" alt-text="SAP S/4HANA Tara" border="true":::

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