---
title: Azure purview 'da Oracle kaynak ve kurulum taramalarını (Önizleme) kaydetme
description: Bu makalede, Oracle kaynağını Azure purview 'a kaydetme ve tarama ayarlama özetlenmektedir.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: overview
ms.date: 2/25/2021
ms.openlocfilehash: 40c5e0ff2c2301607f5a548ff05c742c5c5a948d
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/15/2021
ms.locfileid: "107517071"
---
# <a name="register-and-scan-oracle-source-preview"></a>Oracle kaynağını kaydetme ve tarama (Önizleme)

Bu makalede bir Oracle veri tabanının nasıl kaydedileceği ve tarama ayarlandığı özetlenmektedir.

## <a name="supported-capabilities"></a>Desteklenen özellikler

Oracle kaynağı bir Oracle veritabanından meta verileri ayıklamak için **tam taramayı** destekler ve veri varlıkları arasında **kökenini** getirir.

## <a name="prerequisites"></a>Önkoşullar

1.  En son [kendi kendine barındırılan tümleştirme çalışma zamanını](https://www.microsoft.com/download/details.aspx?id=39717)ayarlayın.
    Daha fazla bilgi için, bkz. [Şirket içinde barındırılan tümleştirme çalışma zamanı oluşturma ve yapılandırma](../data-factory/create-self-hosted-integration-runtime.md).

2.  Şirket içinde barındırılan tümleştirme çalışma zamanının yüklü olduğu sanal makinenizde [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) ' in yüklü olduğundan emin olun.

3.  \"Visual C++ Redistributable 2012 güncelleştirme 4 ' ün \" Şirket içinde barındırılan tümleştirme çalışma zamanı makinesinde yüklü olduğundan emin olun. \'Henüz yüklemediyseniz, [buradan](https://www.microsoft.com/download/details.aspx?id=30679)indirin.

4.  Şirket içinde barındırılan tümleştirme çalışma zamanının çalıştığı sanal makinenizde [buradan](https://www.oracle.com/database/technologies/appdev/jdbc-downloads.html) Oracle JDBC sürücüsünü el ile indirmeniz gerekir.

    > [!Note] 
    > Sürücü, VM 'deki tüm hesaplara erişebilmelidir. Bunu bir kullanıcı hesabına yüklemeyin.

5.  Desteklenen Oracle veritabanı sürümleri 6i ile 19c 'dir.

6.  Kullanıcı izni: sistem tablolarına yönelik salt okuma erişimi gereklidir. Kullanıcının bir oturum oluşturma izni ve rol seçme katalog rolü ' nü SEÇIN \_ \_ . Alternatif olarak, Kullanıcı Bu bağlayıcının meta verileri sorgulayan her bir sistem tablosu için SELECT izni verilmiş olabilir:
       > kullanıcıya oluşturma oturumu verme \[ \] ; \
        Tüm \_ kullanıcıları \[ kullanıcıya Seç \] ; \
        DBA \_ nesnelerinde kullanıcıya seçim izni verin \[ \] ; \
        kullanıcıya DBA sekmesi açıklamalarını seçme izni verin \_ \_ \[ \] ; \
        kullanıcıya DBA dış konumları üzerinde seçim izni verin \_ \_ \[ \] ; \
        DBA \_ için Kullanıcı için select izni \[ verme \] ; \
        \_Kullanıcı için DBA mgörünümlerini seçin \[ \] ; \
        \_Kullanıcı için DBA Clu sütunlarında Select seçeneği verin \_ \[ \] ; \
        \_Kullanıcı için DBA sekmesi sütunları seçimini verme \_ \[ \] ; \
        kullanıcıya DBA Col açıklamalarını seçme izni verin \_ \_ \[ \] ; \
        \_Kullanıcı için DBA kısıtlamalarına Select seçeneği \[ verin \] ; \
        \_Kullanıcı için DBA dezavantajları seçin \_ \[ \] ; \
        DBA \_ için Kullanıcı için select izni \[ ver \] ; \
        Kullanıcı için DBA/veya daha fazla \_ \_ sütun seçin \[ \] ; \
        kullanıcıya DBA yordamlarını seçin \_ \[ \] ; \
        DBA \_ için Kullanıcı eşanlamlılarını seçin \[ \] ; \
        \_Kullanıcı için DBA görünümlerinde Select seçeneği \[ verin \] ; \
        DBA \_ kaynağını \[ kullanıcıya Seç \] ; \
        \_Kullanıcı için DBA tetiklerinin seçimi için izin ver \[ \] ; \
        \_Kullanıcı için DBA bağımsız değişkenlerinde Select seçeneği verin \[ \] ; \
        \_Kullanıcı için DBA sıralarında Select seçeneği \[ verin \] ; \
        \_Kullanıcı için DBA bağımlılıklarını seçme izni \[ verin \] ; \
        V \_ \$ örneğinde Kullanıcı için select izni \[ ver \] ; \
        v \_ \$ veritabanında kullanıcıya seçim verme \[ \] ;
    
## <a name="setting-up-authentication-for-a-scan"></a>Tarama için kimlik doğrulamasını ayarlama

Bir Oracle kaynağı için desteklenen tek kimlik doğrulaması, **temel kimlik doğrulamadır**.

## <a name="register-an-oracle-source"></a>Oracle kaynağı kaydetme

Veri kataloğunuza yeni bir Oracle kaynağı kaydetmek için aşağıdakileri yapın:

1.  Purview hesabınıza gidin.
2.  Sol gezinti çubuğunda **kaynaklar** ' ı seçin.
3.  **Kaydol** ' u seçin
4.  Kayıt kaynakları üzerinde **Oracle**' ı seçin. **Devam**’ı seçin.

    :::image type="content" source="media/register-scan-oracle-source/register-sources.png" alt-text="Kaynakları Kaydet" border="true":::

**Kaynakları Kaydet (Oracle)** ekranında şunları yapın:

1.  Veri kaynağının Katalog içinde listeleneceği bir **ad** girin.

2.  Oracle kaynağına bağlanmak için **ana bilgisayar** adını girin. Bu, aşağıdakilerden biri olabilir:
    - JDBC tarafından veritabanı sunucusuna bağlanmak için kullanılan bir ana bilgisayar adı. Örneğin, MyDatabaseServer.com veya
    - IP adresi. Örneğin, 192.169.1.2 veya
    - Tam olarak uygun JDBC bağlantı dizesi. Örneğin, \
        JDBC: Oracle: ince: @ (Açıklama = (yük \_ Dengeleme = açık) (adres = (protokol = TCP) (Host = oracleserver1) (Port = 1521)) (adres = (protokol = TCP) (ana bilgisayar = oracleserver2) (Port = 1521)) (adres = (protokol = TCP) (ana bilgisayar = oracleserver3) (bağlantı noktası = 1521)) (Connect \_ Data = (hizmet \_ adı = ORCL)))

3.  Veritabanı sunucusuna bağlanmak için JDBC tarafından kullanılan **bağlantı noktası numarasını** girin (varsayılan olarak Oracle için 1521).

4.  Veritabanı sunucusuna bağlanmak için JDBC tarafından kullanılan **Oracle hizmet adını** girin.

5.  Bir koleksiyon seçin veya yeni bir tane oluşturun (Isteğe bağlı)

6.  Veri kaynağını kaydetmek için son ' a gidin.

    :::image type="content" source="media/register-scan-oracle-source/register-sources-2.png" alt-text="kaynakları kaydetme seçenekleri" border="true":::

## <a name="creating-and-running-a-scan"></a>Tarama oluşturma ve çalıştırma

Yeni bir tarama oluşturup çalıştırmak için aşağıdakileri yapın:

1.  Yönetim Merkezi 'nde tümleştirme çalışma zamanları ' na tıklayın. Şirket içinde barındırılan bir tümleştirme çalışma zamanının ayarlandığından emin olun. Ayarlanmamışsa, şirket içinde barındırılan bir tümleştirme çalışma zamanı oluşturmak için [burada](./manage-integration-runtimes.md) bahsedilen adımları kullanın.

2.  **Kaynaklara** gidin.

3.  Kayıtlı Oracle kaynağını seçin.

4.  **+ Yeni tarama**' yı seçin.

5.  Aşağıdaki ayrıntıları sağlayın:

    a.  **Ad**: taramanın adı

    b.  **Tümleştirme çalışma zamanı aracılığıyla Bağlan**: yapılandırılmış şirket içinde barındırılan tümleştirme çalışma zamanını seçin

    c.  **Kimlik bilgisi**: veri kaynağınıza bağlanacak kimlik bilgisini seçin. Şunları yaptığınızdan emin olun:
    - Kimlik bilgisi oluştururken temel kimlik doğrulaması ' nı seçin.        
    - Kullanıcı adı giriş alanındaki veritabanı sunucusuna bağlanmak için JDBC tarafından kullanılan Kullanıcı adını belirtin        
    - Gizli anahtar içindeki veritabanı sunucusuna bağlanmak için JDBC tarafından kullanılan Kullanıcı parolasını depolayın.

    d.  **Şema**: içeri aktarılacak, noktalı virgülle ayrılmış bir liste olarak ifade edilen şemaların alt kümesini listeleyin. Örneğin, Schema1; schema2. Tüm Kullanıcı şemaları, bu liste boşsa içeri aktarılır. Tüm sistem şemaları (örneğin, SysAdmin) ve nesneler varsayılan olarak yok sayılır. Liste boş olduğunda, kullanılabilir tüm şemalar içeri aktarılır.
        SQL LIKE ifadeleri sözdizimi kullanan kabul edilebilir şema adı desenleri, örneğin% kullanımını içerir. %; Kenarı % C%; TID
       -   veya ile başlayın        
       -   B veya ile bitir        
       -   C veya içerir        
       -   eşit D

    NOT ve özel karakterlerin kullanımı kabul edilemez.

6.  **Sürücü konumu**: sanal makinenizde kendi kendine barındırma tümleştirmesi çalışma ZAMANıNıN çalıştığı JDBC sürücü konumunun yolunu belirtin. Bu, geçerli JAR klasörü konumunun yolu olmalıdır.

7.  **Kullanılabilir maksimum bellek**: müşteri VM 'sinde, işlem tarama tarafından kullanılacak maksimum bellek (GB cinsinden). Bu, taranacak SAP S/4HANA kaynağının boyutuna bağlıdır.

    :::image type="content" source="media/register-scan-oracle-source/scan.png" alt-text="Oracle 'ı Tara" border="true":::

8.  **Devam**' a tıklayın.

9.  **Tarama tetikleyiciyi** seçin. Bir zamanlama ayarlayabilir veya taramayı bir kez çalıştırabilirsiniz.

10.  Taramayı gözden geçirin ve **Kaydet ve Çalıştır**' a tıklayın.

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