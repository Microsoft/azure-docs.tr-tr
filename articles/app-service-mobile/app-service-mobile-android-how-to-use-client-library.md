---
title: Android için Azure Mobile Apps SDK 'sını kullanma | Microsoft Docs
description: Android için Azure Mobile Apps SDK 'sını kullanma
services: app-service\mobile
documentationcenter: android
author: elamalani
manager: crdun
ms.assetid: 5352d1e4-7685-4a11-aaf4-10bd2fa9f9fc
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 991f3c8939c0f9e270423ff30282b02f110eb39e
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388911"
---
# <a name="how-to-use-the-azure-mobile-apps-sdk-for-android"></a>Android için Azure Mobile Apps SDK 'sını kullanma

> [!NOTE]
> Visual Studio App Center mobil uygulama dağıtımında merkezi konumdaki uçtan uca ve tümleşik hizmetleri destekler. Geliştiriciler Sürekli Tümleştirme ve Teslim işlem hattını ayarlamak için **Oluşturma**, **Test** ve **Dağıtım** hizmetlerini kullanabilir. Uygulama dağıtıldıktan sonra, geliştiriciler **Analiz** ve **Tanılama** hizmetlerini kullanarak uygulamanın durumunu ve kullanımını izleyebilir, **Gönderme** hizmetini kullanarak kullanıcılarla etkileşim kurabilir. Geliştiriciler ayrıca kullanıcıların kimliğini doğrulamak için **Kimlik Doğrulaması**'ndan ve uygulama verilerini bulutta kalıcı hale getirmek ve eşitlemek için **Veri** hizmetinden yararlanabilir.
>
> Mobil uygulamanızda bulut hizmetlerini tümleştirmek istiyorsanız bugün [App Center](https://appcenter.ms/signup?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) kaydolun.

Bu kılavuzda, aşağıdaki gibi yaygın senaryoları uygulamak için Android istemci SDK 'sının Mobile Apps nasıl kullanılacağı gösterilmektedir:

* Veri sorgulama (ekleme, güncelleştirme ve silme).
* Yetkilendirmesi.
* Hataları işleme.
* İstemciyi özelleştirme.

Bu kılavuz, istemci tarafı Android SDK odaklanır.  Mobile Apps yönelik sunucu tarafı SDK 'Ları hakkında daha fazla bilgi edinmek için bkz. [.net arka uç SDK 'sı Ile çalışma][10] veya [Node. js arka uç SDK 'sını kullanma][11].

## <a name="reference-documentation"></a>Başvuru Belgeleri

GitHub 'da Android istemci kitaplığı için [Javadocs API başvurusunu][12] bulabilirsiniz.

## <a name="supported-platforms"></a>Desteklenen platformlar

Android için Azure Mobile Apps SDK, telefon ve tablet form faktörleri için 19 ile 24 arasında (KitKat ile Nougat) API düzeylerini destekler.  Kimlik doğrulaması, özellikle kimlik bilgilerini toplamak için ortak bir Web çerçevesi yaklaşımını kullanır.  Sunucu akışı kimlik doğrulaması, izlemeler gibi küçük form faktörü cihazları ile çalışmaz.

## <a name="setup-and-prerequisites"></a>Kurulum ve Önkoşullar

[Mobile Apps hızlı başlangıç](app-service-mobile-android-get-started.md) öğreticisini doldurun.  Bu görev, Azure Mobile Apps geliştirmeye yönelik tüm önkoşulların karşılanmasını sağlar.  Hızlı başlangıç, hesabınızı yapılandırmanıza ve ilk mobil uygulama arka ucunuzu oluşturmanıza de yardımcı olur.

Hızlı başlangıç öğreticisini tamamlamamaya karar verirseniz, aşağıdaki görevleri doldurun:

* Android uygulamanızla birlikte kullanmak için [bir mobil uygulama arka ucu oluşturun][13] .
* Android Studio, [Gradle Build dosyalarını güncelleştirin](#gradle-build).
* [İnternet Iznini etkinleştirin](#enable-internet).

### <a name="gradle-build"></a>Gradle derleme dosyasını güncelleştirme

**Build. Gradle** dosyalarını değiştirin:

1. Bu kodu *Proje* düzeyi **Build. Gradle** dosyasına ekleyin:

    ```gradle
    buildscript {
        repositories {
            jcenter()
            google()
        }
    }

    allprojects {
        repositories {
            jcenter()
            google()
        }
    }
    ```

2. Bu kodu *Bağımlılıklar* etiketi içindeki *module uygulama* düzeyi **Build. Gradle** dosyasına ekleyin:

    ```gradle
    implementation 'com.microsoft.azure:azure-mobile-android:3.4.0@aar'
    ```

    Şu anda en son sürüm 3.4.0. Desteklenen sürümler [bintepsi üzerinde][14]listelenmiştir.

### <a name="enable-internet"></a>İnternet iznini etkinleştir

Azure 'a erişmek için uygulamanızda Internet izninin etkinleştirilmiş olması gerekir. Zaten etkinleştirilmemişse, **AndroidManifest. xml** dosyanıza aşağıdaki kod satırını ekleyin:

```xml
<uses-permission android:name="android.permission.INTERNET" />
```

## <a name="create-a-client-connection"></a>Istemci bağlantısı oluşturma

Azure Mobile Apps, mobil uygulamanıza dört işlev sağlar:

* Azure Mobile Apps hizmetiyle veri erişimi ve çevrimdışı eşitleme.
* Azure Mobile Apps Server SDK ile yazılmış özel API 'Leri çağırın.
* Azure App Service kimlik doğrulaması ve yetkilendirme ile kimlik doğrulama.
* Notification Hubs ile anında iletme bildirimi kaydı.

Bu işlevlerin her biri öncelikle `MobileServiceClient` nesnesi oluşturmanızı gerektirir.  Mobil istemciniz içinde yalnızca bir `MobileServiceClient` nesnesi oluşturulmalıdır (yani, tek bir model olmalıdır).  @No__t-0 nesnesi oluşturmak için:

```java
MobileServiceClient mClient = new MobileServiceClient(
    "<MobileAppUrl>",       // Replace with the Site URL
    this);                  // Your application Context
```

@No__t-0, mobil arka ucunuza işaret eden bir dize ya da URL nesnesidir.  Mobil arka ucunuzu barındırmak için Azure App Service kullanıyorsanız, URL 'nin güvenli `https://` sürümünü kullandığınızdan emin olun.

İstemci Ayrıca örnekteki Activity veya Context-`this` parametresine erişim gerektirir.  MobileServiceClient oluşturma, `AndroidManifest.xml` dosyasında başvurulan etkinliğin `onCreate()` yönteminde gerçekleşmelidir.

En iyi uygulama olarak, sunucu iletişimini kendi (Singleton-model) sınıfına soyutmalısınız.  Bu durumda, hizmeti uygun şekilde yapılandırmak için etkinliğini Oluşturucu içinde geçirmeniz gerekir.  Örnek:

```java
package com.example.appname.services;

import android.content.Context;
import com.microsoft.windowsazure.mobileservices.*;

public class AzureServiceAdapter {
    private String mMobileBackendUrl = "https://myappname.azurewebsites.net";
    private Context mContext;
    private MobileServiceClient mClient;
    private static AzureServiceAdapter mInstance = null;

    private AzureServiceAdapter(Context context) {
        mContext = context;
        mClient = new MobileServiceClient(mMobileBackendUrl, mContext);
    }

    public static void Initialize(Context context) {
        if (mInstance == null) {
            mInstance = new AzureServiceAdapter(context);
        } else {
            throw new IllegalStateException("AzureServiceAdapter is already initialized");
        }
    }

    public static AzureServiceAdapter getInstance() {
        if (mInstance == null) {
            throw new IllegalStateException("AzureServiceAdapter is not initialized");
        }
        return mInstance;
    }

    public MobileServiceClient getClient() {
        return mClient;
    }

    // Place any public methods that operate on mClient here.
}
```

Artık ana etkinliğinizin `onCreate()` yönteminde `AzureServiceAdapter.Initialize(this);` ' a çağrı yapabilirsiniz.  İstemciye erişmesi gereken diğer yöntemler, hizmet bağdaştırıcısına yönelik bir başvuru almak için `AzureServiceAdapter.getInstance();` kullanır.

## <a name="data-operations"></a>Veri Işlemleri

Azure Mobile Apps SDK 'nın çekirdeği, mobil uygulama arka ucuna SQL Azure içinde depolanan verilere erişim sağlamaktır.  Bu verilere, türü kesin belirlenmiş sınıflar (tercih edilen) veya türsüz sorgular kullanarak erişebilirsiniz (önerilmez).  Bu bölümün toplu kısmı, kesin olarak belirlenmiş sınıfları kullanmayla ilgilidir.

### <a name="define-client-data-classes"></a>İstemci veri sınıflarını tanımlama

SQL Azure tablolarından verilere erişmek için, mobil uygulama arka ucunda bulunan tablolara karşılık gelen istemci veri sınıflarını tanımlayın. Bu konudaki örneklerde, aşağıdaki sütunlara sahip **Mydatatable**adlı bir tablo varsayılır:

* id
* metin
* Tamam

Karşılık gelen türü belirtilmiş istemci tarafı nesnesi **myDataTable. Java**adlı bir dosyada yer alır:

```java
public class ToDoItem {
    private String id;
    private String text;
    private Boolean complete;
}
```

Eklediğiniz her alan için alıcı ve ayarlayıcı yöntemleri ekleyin.  SQL Azure tablonuz daha fazla sütun içeriyorsa, bu sınıfa karşılık gelen alanları eklersiniz.  Örneğin, DTO (veri aktarımı nesnesi) bir tamsayı öncelik sütunu içeriyorsa bu alanı, alıcı ve ayarlayıcı yöntemleriyle birlikte ekleyebilirsiniz:

```java
private Integer priority;

/**
* Returns the item priority
*/
public Integer getPriority() {
    return mPriority;
}

/**
* Sets the item priority
*
* @param priority
*            priority to set
*/
public final void setPriority(Integer priority) {
    mPriority = priority;
}
```

Mobile Apps arka ucunuzdaki ek tablolar oluşturmayı öğrenmek için bkz. [nasıl yapılır: tablo denetleyicisi tanımlama][15] (.net arka ucu) veya dinamik şema (node. js arka ucu) [kullanarak tabloları tanımlama][16] .

Azure Mobile Apps arka uç tablosu, istemcilerinin dördü olan beş özel alanı tanımlar:

* `String id`: kayıt için genel benzersiz KIMLIK.  En iyi uygulama olarak, kimliği bir [UUID][17] nesnesinin dize gösterimine getirin.
* `DateTimeOffset updatedAt`: son güncelleştirmenin tarihi/saati.  UpdatedAt alanı sunucu tarafından ayarlanır ve istemci kodunuz tarafından hiçbir şekilde ayarlanmamalıdır.
* `DateTimeOffset createdAt`: nesnenin oluşturulduğu tarih/saat.  CreatedAt alanı sunucu tarafından ayarlanır ve istemci kodunuz tarafından hiçbir şekilde ayarlanmamalıdır.
* `byte[] version`: normal olarak bir dize olarak temsil edildiğinde, sürüm sunucu tarafından da ayarlanır.
* `boolean deleted`: kaydın silindiğini ancak henüz temizlenmediğini belirtir.  @No__t-0 ' i sınıfınızın bir özelliği olarak kullanmayın.

`id` alanı gereklidir.  @No__t-0 alanı ve `version` alanı, çevrimdışı eşitleme için kullanılır (sırasıyla artımlı eşitleme ve çakışma çözümü için).  @No__t-0 alanı bir başvuru alanıdır ve istemci tarafından kullanılmaz.  Adlar, özelliklerin "hat genelinde" adlarıdır ve ayarlanamaz.  Ancak, [gson][3] kitaplığını kullanarak nesneniz ve "hat genelinde" adları arasında bir eşleme oluşturabilirsiniz.  Örnek:

```java
package com.example.zumoappname;

import com.microsoft.windowsazure.mobileservices.table.DateTimeOffset;

public class ToDoItem
{
    @com.google.gson.annotations.SerializedName("id")
    private String mId;
    public String getId() { return mId; }
    public final void setId(String id) { mId = id; }

    @com.google.gson.annotations.SerializedName("complete")
    private boolean mComplete;
    public boolean isComplete() { return mComplete; }
    public void setComplete(boolean complete) { mComplete = complete; }

    @com.google.gson.annotations.SerializedName("text")
    private String mText;
    public String getText() { return mText; }
    public final void setText(String text) { mText = text; }

    @com.google.gson.annotations.SerializedName("createdAt")
    private DateTimeOffset mCreatedAt;
    public DateTimeOffset getCreatedAt() { return mCreatedAt; }
    protected void setCreatedAt(DateTimeOffset createdAt) { mCreatedAt = createdAt; }

    @com.google.gson.annotations.SerializedName("updatedAt")
    private DateTimeOffset mUpdatedAt;
    public DateTimeOffset getUpdatedAt() { return mUpdatedAt; }
    protected void setUpdatedAt(DateTimeOffset updatedAt) { mUpdatedAt = updatedAt; }

    @com.google.gson.annotations.SerializedName("version")
    private String mVersion;
    public String getVersion() { return mVersion; }
    public final void setVersion(String version) { mVersion = version; }

    public ToDoItem() { }

    public ToDoItem(String id, String text) {
        this.setId(id);
        this.setText(text);
    }

    @Override
    public boolean equals(Object o) {
        return o instanceof ToDoItem && ((ToDoItem) o).mId == mId;
    }

    @Override
    public String toString() {
        return getText();
    }
}
```

### <a name="create-a-table-reference"></a>Tablo başvurusu oluşturma

Bir tabloya erişmek için önce [MobileServiceClient][9]üzerinde **GetTable** metodunu çağırarak bir [mobileservicetable][8] nesnesi oluşturun.  Bu yöntemin iki aşırı yüklemesi vardır:

```java
public class MobileServiceClient {
    public <E> MobileServiceTable<E> getTable(Class<E> clazz);
    public <E> MobileServiceTable<E> getTable(String name, Class<E> clazz);
}
```

Aşağıdaki kodda, **Mclient** , MobileServiceClient nesneniz için bir başvurudur.  İlk aşırı yükleme, sınıf adı ve tablo adının aynı olduğu ve hızlı başlangıçta kullanıldığı bir şekilde kullanılır:

```java
MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable(ToDoItem.class);
```

İkinci aşırı yükleme, tablo adı sınıf adından farklıysa kullanılır: ilk parametre tablo adıdır.

```java
MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable("ToDoItemBackup", ToDoItem.class);
```

## <a name="query"></a>Arka uç tablosunu sorgulama

İlk olarak, bir tablo başvurusu elde edin.  Ardından tablo başvurusunda bir sorgu yürütün.  Sorgu herhangi bir birleşimidir:

* @No__t-0 [filtresi yan tümcesi](#filtering).
* @No__t-0 [sıralama yan tümcesi](#sorting).
* @No__t-0 [alan seçim tümcesi](#selection).
* @No__t-0 ve [Disk bellekli sonuçlar](#paging)için `.top()`.

Yan tümceler önceki sırada sunulmalıdır.

### <a name="filter"></a>Sonuçları filtreleme

Bir sorgunun genel formu:

```java
List<MyDataTable> results = mDataTable
    // More filters here
    .execute()          // Returns a ListenableFuture<E>
    .get()              // Converts the async into a sync result
```

Yukarıdaki örnek, tüm sonuçları (sunucu tarafından ayarlanan maksimum sayfa boyutuna kadar) döndürür.  @No__t-0 yöntemi arka uçta sorguyu yürütür.  Sorgu, Mobile Apps arka uca iletilmeyen bir [OData v3][19] sorgusuna dönüştürülür.  Alındı, Mobile Apps arka ucu sorguyu SQL Azure örneğinde yürütmeden önce SQL ifadesine dönüştürür.  Ağ etkinliği biraz zaman alacağından `.execute()` yöntemi [`ListenableFuture<E>`][18]döndürür.

### <a name="filtering"></a>Döndürülen verileri filtrele

Aşağıdaki sorgu yürütmesi, **TodoItem** **tablosundan tüm öğeleri** döndürür ve bunun tümü eşittir **false**şeklindedir.

```java
List<ToDoItem> result = mToDoTable
    .where()
    .field("complete").eq(false)
    .execute()
    .get();
```

**mToDoTable** , daha önce oluşturduğumuz mobil hizmet tablosuna başvurudur.

Tablo başvurusunda **WHERE** yöntemi çağrısını kullanarak bir filtre tanımlayın. **WHERE** yönteminin ardından bir **field** yöntemi ve ardından mantıksal koşulu belirten bir yöntem gelir. Olası koşul yöntemlerinde **EQ** (eşittir), **ne** (eşit değildir), **gt** (büyüktür), **Ge** (büyüktür veya eşittir), **lt** (küçüktür), **Le** (küçüktür veya eşittir) bulunur. Bu yöntemler, sayı ve dize alanlarını belirli değerlerle karşılaştırmanızı sağlar.

Tarihlere filtre uygulayabilirsiniz. Aşağıdaki yöntemler tarih alanını veya tarihin tüm parçalarını karşılaştırmanızı sağlar: **yıl**, **ay**, **gün**, **saat**, **dakika**ve **saniye**. Aşağıdaki örnek, *son tarihi* 2013 'e eşit olan öğeler için bir filtre ekler.

```java
List<ToDoItem> results = MToDoTable
    .where()
    .year("due").eq(2013)
    .execute()
    .get();
```

Aşağıdaki yöntemler dize alanlarında karmaşık filtreleri destekler: **StartsWith**, **EndsWith**, **Concat**, **subString**, **IndexOf**, **Replace**, **ToLower**, **ToUpper**, **trim**ve **length** . Aşağıdaki örnek, *metin* SÜTUNUNUN "PRI0" ile başladığı tablo satırları için filtre uygular.

```java
List<ToDoItem> results = mToDoTable
    .where()
    .startsWith("text", "PRI0")
    .execute()
    .get();
```

Şu işleç yöntemleri sayı alanlarında desteklenir: **Add**, **Sub**, **MUL**, **div**, **mod**, **Floor**, **tavan**ve **yuvarlak**. Aşağıdaki örnek, **sürenin** çift sayı olduğu tablo satırları için filtre uygular.

```java
List<ToDoItem> results = mToDoTable
    .where()
    .field("duration").mod(2).eq(0)
    .execute()
    .get();
```

Koşulları şu mantıksal yöntemlerle birleştirebilirsiniz: **ve**, **veya** **.** Aşağıdaki örnek, Yukarıdaki örneklerin ikisini de birleştirir.

```java
List<ToDoItem> results = mToDoTable
    .where()
    .year("due").eq(2013).and().startsWith("text", "PRI0")
    .execute()
    .get();
```

Grup ve iç içe mantıksal işleçler:

```java
List<ToDoItem> results = mToDoTable
    .where()
    .year("due").eq(2013)
    .and(
        startsWith("text", "PRI0")
        .or()
        .field("duration").gt(10)
    )
    .execute().get();
```

Daha ayrıntılı tartışmalar ve filtreleme örnekleri için bkz. [Android istemci sorgu modelinin zenginliğini keşfetme][20].

### <a name="sorting"></a>Döndürülen verileri Sırala

Aşağıdaki kod, *metin* alanına göre artan sıralanmış bir **todoıtems** tablosundan tüm öğeleri döndürür. *mToDoTable* , daha önce oluşturduğunuz arka uç tablosuna yapılan başvurudur:

```java
List<ToDoItem> results = mToDoTable
    .orderBy("text", QueryOrder.Ascending)
    .execute()
    .get();
```

**OrderBy** yönteminin ilk parametresi, sıralanacak alanın adına eşit bir dizedir. İkinci parametre **QueryOrder** sabit listesini kullanarak artan veya azalan sıralama yapılıp yapılmayacağını belirtir.  ***WHERE*** yöntemini kullanarak filtrelerken, ***OrderBy*** yönteminden önce ***WHERE*** yöntemi çağrılması gerekir.

### <a name="selection"></a>Belirli sütunları seç

Aşağıdaki **kod, bir** **todoıtems**tablosundan tüm öğelerin nasıl döndürüldiğini gösterir, ancak yalnızca tüm ve **metin** alanlarını görüntüler. **mToDoTable** , daha önce oluşturduğumuz arka uç tablosuna yapılan başvurudur.

```java
List<ToDoItemNarrow> result = mToDoTable
    .select("complete", "text")
    .execute()
    .get();
```

Select işlevine yönelik parametreler, döndürmek istediğiniz tablo sütunlarının dize adlarıdır.  **Select** yönteminin **WHERE** ve **OrderBy**gibi yöntemleri izlemesi gerekir. Bu, **Skip** ve **top**gibi sayfalama yöntemleri tarafından izlenebilir.

### <a name="paging"></a>Sayfalarda verileri döndürme

Veriler **her zaman** sayfalarda döndürülür.  Döndürülen en fazla kayıt sayısı sunucu tarafından ayarlanır.  İstemci daha fazla kayıt isterse, sunucu en fazla kayıt sayısını döndürür.  Varsayılan olarak, sunucudaki en büyük sayfa boyutu 50 kayıtlardır.

İlk örnek, bir tablodan en üstteki beş öğenin nasıl görüntüleneceğini gösterir. Sorgu, öğeleri bir **todoıtems**tablosundan döndürür. **mToDoTable** , daha önce oluşturduğunuz arka uç tablosuna yapılan başvurudur:

```java
List<ToDoItem> result = mToDoTable
    .top(5)
    .execute()
    .get();
```

İlk beş öğeyi atlayan bir sorgu, sonra da sonraki beş öğeyi döndürür:

```java
List<ToDoItem> result = mToDoTable
    .skip(5).top(5)
    .execute()
    .get();
```

Bir tablodaki tüm kayıtları almak isterseniz, tüm sayfalar üzerinde yinelemek için kodu uygulayın:

```java
List<MyDataModel> results = new ArrayList<>();
int nResults;
do {
    int currentCount = results.size();
    List<MyDataModel> pagedResults = mDataTable
        .skip(currentCount).top(500)
        .execute().get();
    nResults = pagedResults.size();
    if (nResults > 0) {
        results.addAll(pagedResults);
    }
} while (nResults > 0);
```

Bu yöntemi kullanan tüm kayıtlar için bir istek, Mobile Apps arka uca en az iki istek oluşturur.

> [!TIP]
> Doğru sayfa boyutunu seçme, istek gerçekleştiği sırada bellek kullanımı, bant genişliği kullanımı ve verileri tamamen alma gecikmesi arasındaki bir dengedir.  Varsayılan (50 kayıt) tüm cihazlar için uygundur.  Daha büyük bellek cihazlarında özel olarak çalışıyorsanız, 500 'e kadar artırın.  Sayfa boyutunu 500 kaydın ötesine artırmanın kabul edilemez gecikmelere ve büyük bellek sorunlarına neden olduğunu bulduk.

### <a name="chaining"></a>Nasıl yapılır: sorgu yöntemlerini birleştirme

Arka uç tablolarını sorgulamak için kullanılan yöntemler birleştirilebilir. Sorgu yöntemleri zincirleme, sıralanmış ve disk belleğine alınmış filtrelenmiş satırlardaki belirli sütunları seçmenizi sağlar. Karmaşık mantıksal filtreler oluşturabilirsiniz.  Her sorgu yöntemi bir sorgu nesnesi döndürür. Yöntem dizisini sonlandırmak ve sorguyu çalıştırmak için **Execute** metodunu çağırın. Örnek:

```java
List<ToDoItem> results = mToDoTable
        .where()
        .year("due").eq(2013)
        .and(
            startsWith("text", "PRI0").or().field("duration").gt(10)
        )
        .orderBy(duration, QueryOrder.Ascending)
        .select("id", "complete", "text", "duration")
        .skip(200).top(100)
        .execute()
        .get();
```

Zincirleme sorgu yöntemleri aşağıdaki gibi sıralanmalıdır:

1. Filtreleme (**WHERE**) yöntemleri.
2. Sıralama (**OrderBy**) yöntemleri.
3. Seçim (**Select**) yöntemleri.
4. sayfalama (**atlama** ve **üst**) yöntemleri.

## <a name="binding"></a>Verileri Kullanıcı arabirimine bağlama

Veri bağlama üç bileşenden oluşur:

* Veri kaynağı
* Ekran düzeni
* İkisini birlikte birbirine bağlayan bağdaştırıcı.

Örnek kodumuzda, verileri bir diziye Mobile Apps SQL Azure Tablo **TodoItem** geri döndürüyoruz. Bu etkinlik, veri uygulamaları için ortak bir modeldir.  Veritabanı sorguları genellikle istemcinin bir liste veya dizide aldığı bir satır koleksiyonu döndürür. Bu örnekte, dizi veri kaynağıdır.  Kod, cihazda görüntülenen verilerin görünümünü tanımlayan bir ekran düzeni belirtir.  İkisi, bu kodda **arrayadapter @ no__t-1ToDoItem @ no__t-2** sınıfının bir uzantısı olan bir bağdaştırıcı ile birbirine bağlanır.

#### <a name="layout"></a>Düzeni tanımlama

Düzen, XML kodunun çeşitli parçacıkları tarafından tanımlanır. Var olan bir düzen verildiğinde, aşağıdaki kod sunucu verilerimizden doldurmak istediğimiz **ListView** 'u temsil eder.

```xml
    <ListView
        android:id="@+id/listViewToDo"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        tools:listitem="@layout/row_list_to_do" >
    </ListView>
```

Yukarıdaki kodda, *ListItem* özniteliği listedeki tek bir satır için düzenin kimliğini belirtir. Bu kod, bir onay kutusunu ve ilişkili metnini belirtir ve listedeki her öğe için bir kez oluşturulur. Bu düzen **ID** alanını göstermez ve daha karmaşık bir düzen, ekranda ek alanlar belirtmektir. Bu kod **row_list_to_do. xml** dosyasıdır.

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="horizontal">
    <CheckBox
        android:id="@+id/checkToDoItem"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/checkbox_text" />
</LinearLayout>
```

#### <a name="adapter"></a>Bağdaştırıcıyı tanımlama
Görünümümüzde bulunan veri kaynağı bir **TodoItem**dizisi olduğundan, Bağdaştırıcımızı bir **arrayadapter @ no__t-2ToDoItem @ no__t-3** sınıfından alt sınıflıyoruz. Bu alt sınıf, **row_list_to_do** mizanpajını kullanarak her **TodoItem** için bir görünüm üretir.  Kodlarımızda, **Arrayadapter @ no__t-1e @ no__t-2** sınıfının bir uzantısı olan aşağıdaki sınıfı tanımlayacağız:

```java
public class ToDoItemAdapter extends ArrayAdapter<ToDoItem> {
}
```

Bağdaştırıcıları **GetView** metodunu geçersiz kılın. Örnek:

```java
    @Override
    public View getView(int position, View convertView, ViewGroup parent) {
        View row = convertView;

        final ToDoItem currentItem = getItem(position);

        if (row == null) {
            LayoutInflater inflater = ((Activity) mContext).getLayoutInflater();
            row = inflater.inflate(R.layout.row_list_to_do, parent, false);
        }
        row.setTag(currentItem);

        final CheckBox checkBox = (CheckBox) row.findViewById(R.id.checkToDoItem);
        checkBox.setText(currentItem.getText());
        checkBox.setChecked(false);
        checkBox.setEnabled(true);

        checkBox.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View arg0) {
                if (checkBox.isChecked()) {
                    checkBox.setEnabled(false);
                    if (mContext instanceof ToDoActivity) {
                        ToDoActivity activity = (ToDoActivity) mContext;
                        activity.checkItem(currentItem);
                    }
                }
            }
        });
        return row;
    }
```

Etkinlikimizde bu sınıfın bir örneğini aşağıda gösterildiği gibi oluşturacağız:

```java
    ToDoItemAdapter mAdapter;
    mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);
```

ToDoItemAdapter oluşturucusunun ikinci parametresi, düzene yönelik bir başvurudur. Artık **ListView** 'u örneklendiriyoruz ve bu bağdaştırıcıyı **ListView**öğesine atayabiliriz.

```java
    ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
    listViewToDo.setAdapter(mAdapter);
```

#### <a name="use-adapter"></a>Kullanıcı arabirimine bağlamak için bağdaştırıcıyı kullanın

Artık veri bağlamayı kullanmaya hazırsınız. Aşağıdaki kod, tablodaki öğelerin nasıl alınacağını ve yerel bağdaştırıcıyı döndürülen öğelerle nasıl doldurduğunu gösterir.

```java
    public void showAll(View view) {
        AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>(){
            @Override
            protected Void doInBackground(Void... params) {
                try {
                    final List<ToDoItem> results = mToDoTable.execute().get();
                    runOnUiThread(new Runnable() {

                        @Override
                        public void run() {
                            mAdapter.clear();
                            for (ToDoItem item : results) {
                                mAdapter.add(item);
                            }
                        }
                    });
                } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
                }
                return null;
            }
        };
        runAsyncTask(task);
    }
```

**TodoItem** tablosunu her değiştirdiğiniz zaman bağdaştırıcıyı çağırın. Değişiklikler kayıt temelinde kayıt üzerinde yapıldığından, bir koleksiyon yerine tek bir satır işleyebilirsiniz. Bir öğe eklediğinizde, bağdaştırıcıdaki **Add** metodunu çağırın; silme sırasında **Remove** metodunu çağırın.

[Android hızlı başlangıç projesinde][21]tamamen bir örnek bulabilirsiniz.

## <a name="inserting"></a>Arka uca veri ekleme

*TodoItem* sınıfının bir örneğini oluşturun ve özelliklerini ayarlayın.

```java
ToDoItem item = new ToDoItem();
item.text = "Test Program";
item.complete = false;
```

Sonra **Insert ()** kullanarak bir nesne ekleyin:

```java
ToDoItem entity = mToDoTable
    .insert(item)       // Returns a ListenableFuture<ToDoItem>
    .get();
```

Döndürülen varlık, arka uçta ayarlanan KIMLIĞI ve diğer değerleri (`createdAt`, `updatedAt` ve `version` alanları) dahil olmak üzere arka uç tablosuna eklenen verilerle eşleşir.

Mobile Apps tabloları, **ID**adlı bir birincil anahtar sütunu gerektirir. Bu sütun bir dize olmalıdır. KIMLIK sütununun varsayılan değeri bir GUID 'dir.  E-posta adresleri veya Kullanıcı adları gibi diğer benzersiz değerler de sağlayabilirsiniz. Ekli bir kayıt için bir dize KIMLIĞI değeri sağlanmazsa, arka uç yeni bir GUID oluşturur.

Dize KIMLIĞI değerleri aşağıdaki avantajları sağlar:

* Kimlikler, veritabanına gidiş dönüş yapılmadan oluşturulabilir.
* Kayıtlar farklı tablolardan veya veritabanlarından birleştirmek daha kolaydır.
* KIMLIK değerleri bir uygulamanın mantığı ile daha iyi tümleşir.

Çevrimdışı eşitleme desteği için dize KIMLIĞI değerleri **gereklidir** .  Bir kimliği, arka uç veritabanında depolandıktan sonra değiştiremezsiniz.

## <a name="updating"></a>Mobil uygulamadaki verileri güncelleştirme

Bir tablodaki verileri güncelleştirmek için yeni nesneyi **Update ()** metoduna geçirin.

```java
mToDoTable
    .update(item)   // Returns a ListenableFuture<ToDoItem>
    .get();
```

Bu örnekte, *öğe* , üzerinde yapılan bazı değişiklikler içeren *TodoItem* tablosundaki bir satıra başvurudur.  Aynı **kimliğe** sahip satır güncelleştirildi.

## <a name="deleting"></a>Mobil uygulamadaki verileri silme

Aşağıdaki kod, veri nesnesini belirterek tablodaki verilerin nasıl silineceğini gösterir.

```java
mToDoTable
    .delete(item);
```

Ayrıca, silinecek satırın **ID** alanını belirterek bir öğeyi silebilirsiniz.

```java
String myRowId = "2FA404AB-E458-44CD-BC1B-3BC847EF0902";
mToDoTable
    .delete(myRowId);
```

## <a name="lookup"></a>Kimliğe göre belirli bir öğe ara

**LookUp ()** yöntemiyle belirli bir **ID** alanı olan bir öğe arar:

```java
ToDoItem result = mToDoTable
    .lookUp("0380BAFB-BCFF-443C-B7D5-30199F730335")
    .get();
```

## <a name="untyped"></a>Nasıl yapılır: türsüz verilerle çalışma

Türsüz programlama modeli, JSON serileştirme üzerinde tam denetim sağlar.  Türsüz bir programlama modeli kullanmak isteyebileceğiniz bazı yaygın senaryolar vardır. Örneğin, arka uç tablonuz çok sayıda sütun içeriyorsa ve yalnızca sütunların bir alt kümesine başvurulmasına ihtiyaç duyuyorsanız.  Türü belirtilmiş model, veri sınıfınıza Mobile Apps arka uçta tanımlanan tüm sütunları tanımlamanızı gerektirir.  Verilere erişmek için API çağrılarının çoğu, yazılan programlama çağrılarına benzerdir. Asıl fark, türü belirsiz bir modelde **Mobileservicetable** nesnesi yerine **mobileservicejsontable** nesnesinde çağırmamanızdır.

### <a name="json_instance"></a>Türsüz bir tablonun örneğini oluşturma

Yazılı modele benzer şekilde, bir tablo başvurusu alarak başlar, ancak bu durumda bir **Mobileservicesjsontable** nesnesidir. İstemcinin bir örneğinde **GetTable** metodunu çağırarak başvuruyu edinin:

```java
private MobileServiceJsonTable mJsonToDoTable;
//...
mJsonToDoTable = mClient.getTable("ToDoItem");
```

**Mobileservicejsontable**'ın bir örneğini oluşturduktan sonra, bu, türü belirlenmiş programlama MODELIYLE aynı API 'ye sahiptir. Bazı durumlarda, yöntemler türü belirlenmiş bir parametre yerine türsüz bir parametre alır.

### <a name="json_insert"></a>Türsüz tabloya ekle
Aşağıdaki kod, bir eklemenin nasıl yapılacağını gösterir. İlk adım, [gson][3] kitaplığının bir parçası olan bir [JsonObject][1]oluşturmaktır.

```java
JsonObject jsonItem = new JsonObject();
jsonItem.addProperty("text", "Wake up");
jsonItem.addProperty("complete", false);
```

Ardından, tabloya türsüz nesneyi eklemek için **Insert ()** kullanın.

```java
JsonObject insertedItem = mJsonToDoTable
    .insert(jsonItem)
    .get();
```

Eklenecek nesnenin KIMLIĞINI almanız gerekiyorsa, **Getasjsonilkel ()** yöntemini kullanın.

```java
String id = insertedItem.getAsJsonPrimitive("id").getAsString();
```
### <a name="json_delete"></a>Türsüz bir tablodan Sil
Aşağıdaki kod, önceki *Insert* örneğinde oluşturulmuş bir **JsonObject** örneği olan bu örnekte bir örneğin nasıl silineceğini gösterir. Kod, yazılan durum ile aynıdır, ancak bir **JsonObject**öğesine başvurduğundan yöntemin farklı bir imzası vardır.

```java
mToDoTable
    .delete(insertedItem);
```

Ayrıca, KIMLIĞINI kullanarak doğrudan bir örneği silebilirsiniz:

```java
mToDoTable.delete(ID);
```

### <a name="json_get"></a>Türsüz bir tablodan tüm satırları döndür
Aşağıdaki kod, tüm bir tablonun nasıl alınacağını gösterir. JSON tablosu kullandığınızdan, tablonun sütunlarından yalnızca birini seçerek alabilirsiniz.

```java
public void showAllUntyped(View view) {
    new AsyncTask<Void, Void, Void>() {
        @Override
        protected Void doInBackground(Void... params) {
            try {
                final JsonElement result = mJsonToDoTable.execute().get();
                final JsonArray results = result.getAsJsonArray();
                runOnUiThread(new Runnable() {

                    @Override
                    public void run() {
                        mAdapter.clear();
                        for (JsonElement item : results) {
                            String ID = item.getAsJsonObject().getAsJsonPrimitive("id").getAsString();
                            String mText = item.getAsJsonObject().getAsJsonPrimitive("text").getAsString();
                            Boolean mComplete = item.getAsJsonObject().getAsJsonPrimitive("complete").getAsBoolean();
                            ToDoItem mToDoItem = new ToDoItem();
                            mToDoItem.setId(ID);
                            mToDoItem.setText(mText);
                            mToDoItem.setComplete(mComplete);
                            mAdapter.add(mToDoItem);
                        }
                    }
                });
            } catch (Exception exception) {
                createAndShowDialog(exception, "Error");
            }
            return null;
        }
    }.execute();
}
```

Türsüz model için kullanılabilen aynı filtreleme, filtreleme ve sayfalama yöntemleri kümesi vardır.

## <a name="offline-sync"></a>Çevrimdışı eşitleme Uygula

Azure Mobile Apps Istemci SDK 'Sı, sunucu verilerinin bir kopyasını yerel olarak depolamak için bir SQLite veritabanı kullanarak verilerin çevrimdışı eşitlemesini da uygular.  Çevrimdışı bir tabloda gerçekleştirilen işlemler, mobil bağlantının çalışmasını gerektirmez.  Çevrimdışı eşitleme, çakışma çözümüne yönelik daha karmaşık mantık masrafına esnekliği ve performans konusunda yardımcı olur.  Azure Mobile Apps Istemci SDK 'Sı aşağıdaki özellikleri uygular:

* Artımlı eşitleme: yalnızca güncelleştirilmiş ve yeni kayıtlar indirilir, bant genişliği ve bellek tüketimi kaydedilir.
* İyimser eşzamanlılık: Işlemlerin başarılı olduğu varsayılır.  Sunucu üzerinde güncelleştirmeler gerçekleştirilene kadar çakışma çözümü ertelenir.
* Çakışma çözümü: SDK, sunucuda çakışan bir değişikliğin ne zaman yapıldığını algılar ve kullanıcıyı uyarmak için kancalar sağlar.
* Geçici silme: silinen kayıtlar silinmiş olarak işaretlenir ve diğer cihazların çevrimdışı önbelleklerini güncelleştirmesine izin verilir.

### <a name="initialize-offline-sync"></a>Çevrimdışı Eşitlemeyi Başlat

Her çevrimdışı tablonun kullanılmadan önce çevrimdışı önbellekte tanımlanması gerekir.  Normalde, tablo tanımı istemci oluşturulduktan hemen sonra yapılır:

```java
AsyncTask<Void, Void, Void> initializeStore(MobileServiceClient mClient)
    throws MobileServiceLocalStoreException, ExecutionException, InterruptedException
{
    AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>() {
        @Override
        protected void doInBackground(Void... params) {
            try {
                MobileServiceSyncContext syncContext = mClient.getSyncContext();
                if (syncContext.isInitialized()) {
                    return null;
                }
                SQLiteLocalStore localStore = new SQLiteLocalStore(mClient.getContext(), "offlineStore", null, 1);

                // Create a table definition.  As a best practice, store this with the model definition and return it via
                // a static method
                Map<String, ColumnDataType> toDoItemDefinition = new HashMap<String, ColumnDataType>();
                toDoItemDefinition.put("id", ColumnDataType.String);
                toDoItemDefinition.put("complete", ColumnDataType.Boolean);
                toDoItemDefinition.put("text", ColumnDataType.String);
                toDoItemDefinition.put("version", ColumnDataType.String);
                toDoItemDefinition.put("updatedAt", ColumnDataType.DateTimeOffset);

                // Now define the table in the local store
                localStore.defineTable("ToDoItem", toDoItemDefinition);

                // Specify a sync handler for conflict resolution
                SimpleSyncHandler handler = new SimpleSyncHandler();

                // Initialize the local store
                syncContext.initialize(localStore, handler).get();
            } catch (final Exception e) {
                createAndShowDialogFromTask(e, "Error");
            }
            return null;
        }
    };
    return runAsyncTask(task);
}
```

### <a name="obtain-a-reference-to-the-offline-cache-table"></a>Çevrimdışı önbellek tablosuna bir başvuru alın

Çevrimiçi bir tablo için `.getTable()` ' ı kullanırsınız.  Çevrimdışı bir tablo için `.getSyncTable()` kullanın:

```java
MobileServiceSyncTable<ToDoItem> mToDoTable = mClient.getSyncTable("ToDoItem", ToDoItem.class);
```

Çevrimiçi tablolarda (filtreleme, sıralama, sayfalama, veri ekleme, verileri güncelleştirme ve verileri silme dahil) tüm yöntemler, çevrimiçi ve çevrimdışı tablolar üzerinde de aynı şekilde çalışır.

### <a name="synchronize-the-local-offline-cache"></a>Yerel çevrimdışı önbelleği eşitler

Eşitleme, uygulamanızın denetimi içindedir.  Örnek bir eşitleme yöntemi aşağıda verilmiştir:

```java
private AsyncTask<Void, Void, Void> sync(MobileServiceClient mClient) {
    AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>(){
        @Override
        protected Void doInBackground(Void... params) {
            try {
                MobileServiceSyncContext syncContext = mClient.getSyncContext();
                syncContext.push().get();
                mToDoTable.pull(null, "todoitem").get();
            } catch (final Exception e) {
                createAndShowDialogFromTask(e, "Error");
            }
            return null;
        }
    };
    return runAsyncTask(task);
}
```

@No__t-0 yöntemine bir sorgu adı sağlanmışsa, yalnızca son başarılı çekme tamamlandıktan sonra oluşturulmuş veya değiştirilmiş kayıtları döndürmek için artımlı eşitleme kullanılır.

### <a name="handle-conflicts-during-offline-synchronization"></a>Çevrimdışı eşitleme sırasında çakışmaları işle

@No__t-0 işlemi sırasında bir çakışma oluşursa, bir `MobileServiceConflictException` atılır.   Sunucu tarafından verilen öğe özel duruma katıştırılır ve özel durum üzerinde `.getItem()` tarafından alınabilir.  Aşağıdaki öğeleri MobileServiceSyncContext nesnesinde çağırarak gönderimi ayarlayın:

*  `.cancelAndDiscardItem()`
*  `.cancelAndUpdateItem()`
*  `.updateOperationAndItem()`

Tüm çakışmalar istediğiniz gibi işaretlendikten sonra tüm çakışmaları çözmek için `.push()` ' ı yeniden çağırın.

## <a name="custom-api"></a>Özel API çağırma

Özel bir API, bir INSERT, Update, DELETE veya Read işlemiyle eşleşmeyen sunucu işlevlerini açığa çıkaran özel uç noktalar tanımlamanızı sağlar. Özel bir API kullanarak ileti üzerinde daha fazla denetime sahip olabilirsiniz ve HTTP ileti üst bilgilerini okumak ve ayarlamak ve JSON dışında bir ileti gövdesi biçimi tanımlamak dahil olabilirsiniz.

Android istemcisinden, özel API uç noktasını çağırmak için **ınvokeapı** metodunu çağırabilirsiniz. Aşağıdaki örnek, **Markallresult**adlı bir koleksiyon sınıfı döndüren **COMPLETEALL**adlı bir API uç noktasının nasıl çağrılacağını gösterir.

```java
public void completeItem(View view) {
    ListenableFuture<MarkAllResult> result = mClient.invokeApi("completeAll", MarkAllResult.class);
    Futures.addCallback(result, new FutureCallback<MarkAllResult>() {
        @Override
        public void onFailure(Throwable exc) {
            createAndShowDialog((Exception) exc, "Error");
        }

        @Override
        public void onSuccess(MarkAllResult result) {
            createAndShowDialog(result.getCount() + " item(s) marked as complete.", "Completed Items");
            refreshItemsFromTable();
        }
    });
}
```

**Invokeapı** yöntemi, istemci üzerinde çağrılır, bu da yenı özel API 'ye bir post isteği gönderir. Özel API tarafından döndürülen sonuç, herhangi bir hata gibi bir ileti iletişim kutusunda görüntülenir. **Invokeapı** 'nin diğer sürümleri isteğe bağlı olarak istek gövdesinde bir nesne GÖNDERMENIZI, http yöntemini belirtmenizi ve istekle birlikte sorgu parametreleri göndermenizi sağlar. **Invokeapı** 'nin türsüz sürümleri de sağlanmaktadır.

## <a name="authentication"></a>Uygulamanıza kimlik doğrulaması ekleme

Öğreticiler, bu özelliklerin nasıl ekleneceğini ayrıntılı olarak anlatmaktadır.

App Service, farklı dış kimlik sağlayıcıları kullanarak [Uygulama kullanıcılarının kimlik doğrulamasını](app-service-mobile-android-get-started-users.md) destekler: Facebook, Google, Microsoft hesabı, Twitter ve Azure Active Directory. Belirli işlemlere erişimi yalnızca kimliği doğrulanmış kullanıcılarla kısıtlamak için tablolar üzerinde izinler belirleyebilirsiniz. Ayrıca, arka ucunuzdaki yetkilendirme kurallarını uygulamak için kimliği doğrulanmış kullanıcıların kimliğini de kullanabilirsiniz.

İki kimlik doğrulama akışı desteklenir: **sunucu** akışı ve **istemci** akışı. Sunucu akışı, kimlik sağlayıcıları web arabirimine dayandığı için en basit kimlik doğrulama deneyimini sağlar.  Sunucu akışı kimlik doğrulamasını uygulamak için ek SDK gerekli değildir. Sunucu akışı kimlik doğrulaması, mobil cihaza derin bir tümleştirme sağlamaz ve yalnızca kavram kanıtı senaryoları için önerilir.

İstemci akışı, kimlik sağlayıcısı tarafından belirtilen SDK 'Ları temel aldığından, çoklu oturum açma gibi cihaza özgü yetenekler ile daha derin tümleştirme sağlar.  Örneğin, Facebook SDK 'sını Mobil uygulamanızla tümleştirebilirsiniz.  Mobil istemci Facebook uygulamasında takas eder ve mobil uygulamanıza geri dönerek oturum açma bilgilerinizi onaylar.

Uygulamanızda kimlik doğrulamasını etkinleştirmek için dört adım gereklidir:

* Bir kimlik sağlayıcısıyla kimlik doğrulaması için uygulamanızı kaydedin.
* App Service arka ucunuzu yapılandırın.
* Tablo izinlerini yalnızca App Service arka uçta kimliği doğrulanmış kullanıcılarla kısıtlayın.
* Uygulamanıza kimlik doğrulama kodu ekleyin.

Belirli işlemlere erişimi yalnızca kimliği doğrulanmış kullanıcılarla kısıtlamak için tablolar üzerinde izinler belirleyebilirsiniz. İstekleri değiştirmek için kimliği doğrulanmış bir kullanıcının SID 'sini de kullanabilirsiniz.  Daha fazla bilgi için [kimlik doğrulaması ile çalışmaya başlama] ve sunucu SDK 'sı belgelerini inceleyin.

### <a name="caching"></a>Kimlik doğrulaması: sunucu akışı

Aşağıdaki kod, Google sağlayıcısını kullanarak bir sunucu akışı oturum açma işlemi başlatır.  Google sağlayıcısı için güvenlik gereksinimleri nedeniyle ek yapılandırma gereklidir:

```java
MobileServiceUser user = mClient.login(MobileServiceAuthenticationProvider.Google, "{url_scheme_of_your_app}", GOOGLE_LOGIN_REQUEST_CODE);
```

Ayrıca, ana etkinlik sınıfına aşağıdaki yöntemi ekleyin:

```java
// You can choose any unique number here to differentiate auth providers from each other. Note this is the same code at login() and onActivityResult().
public static final int GOOGLE_LOGIN_REQUEST_CODE = 1;

@Override
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
    // When request completes
    if (resultCode == RESULT_OK) {
        // Check the request code matches the one we send in the login request
        if (requestCode == GOOGLE_LOGIN_REQUEST_CODE) {
            MobileServiceActivityResult result = mClient.onActivityResult(data);
            if (result.isLoggedIn()) {
                // login succeeded
                createAndShowDialog(String.format("You are now logged in - %1$2s", mClient.getCurrentUser().getUserId()), "Success");
                createTable();
            } else {
                // login failed, check the error message
                String errorMessage = result.getErrorMessage();
                createAndShowDialog(errorMessage, "Error");
            }
        }
    }
}
```

Ana etkinliğinizdeki `GOOGLE_LOGIN_REQUEST_CODE` `login()` yöntemi ve `onActivityResult()` yöntemi için kullanılır.  @No__t-0 yönteminde ve `onActivityResult()` yönteminde aynı sayı kullanıldığı sürece herhangi bir benzersiz sayı seçebilirsiniz.  İstemci kodunu bir hizmet bağdaştırıcısına (daha önce gösterildiği gibi) soyutlamak isterseniz, hizmet bağdaştırıcısında uygun yöntemleri çağırmanız gerekir.

Ayrıca, projesini customtab için de yapılandırmanız gerekir.  İlk olarak bir Redirect-URL belirtin.  @No__t-0 ' a aşağıdaki kod parçacığını ekleyin:

```xml
<activity android:name="com.microsoft.windowsazure.mobileservices.authentication.RedirectUrlActivity">
    <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />
        <data android:scheme="{url_scheme_of_your_app}" android:host="easyauth.callback"/>
    </intent-filter>
</activity>
```

Uygulama için **Redirecturischeme** `build.gradle` dosyasına ekleyin:

```gradle
android {
    buildTypes {
        release {
            // … …
            manifestPlaceholders = ['redirectUriScheme': '{url_scheme_of_your_app}://easyauth.callback']
        }
        debug {
            // … …
            manifestPlaceholders = ['redirectUriScheme': '{url_scheme_of_your_app}://easyauth.callback']
        }
    }
}
```

Son olarak, `build.gradle` dosyasındaki bağımlılıklar listesine `com.android.support:customtabs:28.0.0` ekleyin:

```gradle
dependencies {
    implementation fileTree(dir: 'libs', include: ['*.jar'])
    implementation 'com.google.code.gson:gson:2.3'
    implementation 'com.google.guava:guava:18.0'
    implementation 'com.android.support:customtabs:28.0.0'
    implementation 'com.squareup.okhttp:okhttp:2.5.0'
    implementation 'com.microsoft.azure:azure-mobile-android:3.4.0@aar'
    implementation 'com.microsoft.azure:azure-notifications-handler:1.0.1@jar'
}
```

**GetUserID** metodunu kullanarak, bir **mobileserviceuser** 'DAN oturum açmış kullanıcının kimliğini edinin. Zaman uyumsuz oturum açma API 'Lerini çağırmak için vadeli Işlem kullanmanın bir örneği için bkz. [Kimlik doğrulaması ile çalışmaya başlama].

> [!WARNING]
> Bahsedilen URL şeması, büyük/küçük harfe duyarlıdır.  @No__t-0 eşleşme durumunun tüm oluşumlarının bulunduğundan emin olun.

### <a name="caching"></a>Önbellek kimlik doğrulaması belirteçleri

Önbelleğe alma kimlik doğrulaması belirteçleri, kullanıcı KIMLIĞINI ve kimlik doğrulama belirtecini cihazda yerel olarak depolamanızı gerektirir. Uygulamanın bir sonraki açılışında önbelleği kontrol edersiniz ve bu değerler varsa, oturum açma yordamını atlayabilir ve istemciyi bu verilerle yeniden girebilirsiniz. Ancak bu veriler duyarlıdır ve telefonun çalınması durumunda güvenlik için şifrelenmiş olarak depolanması gerekir.  [Önbellek kimlik doğrulaması belirteçleri bölümünde][7]kimlik doğrulama belirteçlerinin nasıl önbelleğe alınacağını gösteren bir örnek görürsünüz.

Kullanım dışı bir belirteci kullanmaya çalıştığınızda, *401 Yetkisiz* bir yanıt alırsınız. Filtreleri kullanarak kimlik doğrulama hatalarını işleyebilirsiniz.  Filtreler App Service arka uca istekleri durdurur. Filtre kodu bir 401 için yanıtı sınar, oturum açma işlemini tetikler ve ardından 401 ' yi oluşturan isteği sürdürür.

### <a name="refresh"></a>Yenileme belirteçlerini kullan

Azure App Service kimlik doğrulaması ve yetkilendirme tarafından döndürülen belirtecin bir saat tanımlı bir yaşam süresi vardır.  Bu dönemden sonra, kullanıcıyı yeniden kimlik doğrulaması yapmanız gerekir.  İstemci akışı kimlik doğrulaması aracılığıyla aldığınız uzun süreli bir belirteç kullanıyorsanız, aynı belirteci kullanarak Azure App Service kimlik doğrulaması ve yetkilendirmeyle yeniden kimlik doğrulama yapabilirsiniz.  Başka bir Azure App Service belirteci yeni bir yaşam süresi ile oluşturulur.

Ayrıca, sağlayıcıyı yenileme belirteçleri kullanacak şekilde kaydedebilirsiniz.  Yenileme belirteci her zaman kullanılabilir değildir.  Ek yapılandırma gereklidir:

* **Azure Active Directory**Için Azure Active Directory uygulaması için bir istemci gizli anahtarı yapılandırın.  Azure Active Directory kimlik doğrulamasını yapılandırırken Azure App Service istemci parolasını belirtin.  @No__t-0 ' ı çağırırken `response_type=code id_token` ' i bir parametre olarak geçirin:

    ```java
    HashMap<String, String> parameters = new HashMap<String, String>();
    parameters.put("response_type", "code id_token");
    MobileServiceUser user = mClient.login
        MobileServiceAuthenticationProvider.AzureActiveDirectory,
        "{url_scheme_of_your_app}",
        AAD_LOGIN_REQUEST_CODE,
        parameters);
    ```

* **Google**için `access_type=offline` ' i bir parametre olarak geçirin:

    ```java
    HashMap<String, String> parameters = new HashMap<String, String>();
    parameters.put("access_type", "offline");
    MobileServiceUser user = mClient.login
        MobileServiceAuthenticationProvider.Google,
        "{url_scheme_of_your_app}",
        GOOGLE_LOGIN_REQUEST_CODE,
        parameters);
    ```

* **Microsoft hesabı**için `wl.offline_access` kapsamını seçin.

Bir belirteci yenilemek için `.refreshUser()` ' ı çağırın:

```java
MobileServiceUser user = mClient
    .refreshUser()  // async - returns a ListenableFuture<MobileServiceUser>
    .get();
```

En iyi uygulama olarak, sunucudan 401 yanıtını algılayan ve kullanıcı belirtecini yenilemeye çalışan bir filtre oluşturun.

## <a name="log-in-with-client-flow-authentication"></a>Istemci akışı kimlik doğrulamasıyla oturum açın

İstemci akışı kimlik doğrulamasıyla oturum açmak için genel süreç aşağıdaki gibidir:

* Sunucu Flow kimlik doğrulamasında Azure App Service kimlik doğrulaması ve yetkilendirme yapılandırın.
* Erişim belirteci üretmek için kimlik doğrulama sağlayıcısı SDK 'sını kimlik doğrulama için tümleştirin.
* @No__t-0 yöntemini aşağıdaki gibi çağırın (`result` ' in `AuthenticationResult` olması gerekir):

    ```java
    JSONObject payload = new JSONObject();
    payload.put("access_token", result.getAccessToken());
    ListenableFuture<MobileServiceUser> mLogin = mClient.login("{provider}", payload.toString());
    Futures.addCallback(mLogin, new FutureCallback<MobileServiceUser>() {
        @Override
        public void onFailure(Throwable exc) {
            exc.printStackTrace();
        }
        @Override
        public void onSuccess(MobileServiceUser user) {
            Log.d(TAG, "Login Complete");
        }
    });
    ```

Sonraki bölümde tüm kod örneğine bakın.

@No__t-0 yöntemini, başarılı bir oturum açma işlemi üzerinde kullanmak istediğiniz kod ile değiştirin.  @No__t-0 dizesi geçerli bir sağlayıcıdır: **AAD** (Azure Active Directory), **Facebook**, **Google**, **MicrosoftAccount**veya **Twitter**.  Özel kimlik doğrulaması uyguladıysanız, özel kimlik doğrulama sağlayıcısı etiketini de kullanabilirsiniz.

### <a name="adal"></a>Active Directory Authentication Library (ADAL) ile kullanıcıların kimliğini doğrulama

Azure Active Directory kullanarak uygulamanızdaki kullanıcıları imzalamak için Active Directory Authentication Library (ADAL) kullanabilirsiniz. Daha yerel bir UX sunan ve ek özelleştirmeye izin verdiği için, istemci akışı oturum açma, genellikle `loginAsync()` yöntemlerinin kullanılması tercih edilir.

1. [Active Directory oturum açma öğreticisini App Service nasıl yapılandıracağınızı][22] izleyerek AAD oturum açma için mobil uygulama arka ucunuzu yapılandırın. Yerel istemci uygulamasını kaydetme işleminin isteğe bağlı adımını tamamladığınızdan emin olun.
2. Build. Gradle dosyanızı aşağıdaki tanımları içerecek şekilde değiştirerek ADAL 'yi oluşturun:

    ```gradle
    repositories {
        mavenCentral()
        flatDir {
            dirs 'libs'
        }
        maven {
            url "YourLocalMavenRepoPath\\.m2\\repository"
        }
    }
    packagingOptions {
        exclude 'META-INF/MSFTSIG.RSA'
        exclude 'META-INF/MSFTSIG.SF'
    }
    dependencies {
        implementation fileTree(dir: 'libs', include: ['*.jar'])
        implementation('com.microsoft.aad:adal:1.16.1') {
            exclude group: 'com.android.support'
        } // Recent version is 1.16.1
        implementation 'com.android.support:support-v4:28.0.0'
    }
    ```

3. Aşağıdaki kodu uygulamanıza ekleyin ve aşağıdaki değişiklikleri yapın:

    * Eklentiyi, uygulamanızı sağladığınız kiracının **adıyla değiştirin.** Biçim https://login.microsoftonline.com/contoso.onmicrosoft.com olmalıdır.
    * **Insert-Resource-ID-burada** , mobil uygulama arka ucunuzun istemci kimliği ile değiştirin. İstemci KIMLIĞINI, portalda **Azure Active Directory ayarlar** ' ın altında bulunan **Gelişmiş** sekmesinden elde edebilirsiniz.
    * **Ekle-ISTEMCI kimliği-** ' ni yerel istemci uygulamasından KOPYALADıĞıNıZ istemci kimliğiyle değiştirin.
    * {1 & gt; **Insert-REDIRECT-URI** & lt; 1} ÖĞESINI, https şemasını kullanarak sitenizin */.Auth/login/done* uç noktasıyla değiştirin Bu değer *https://contoso.azurewebsites.net/.auth/login/done* ' e benzer olmalıdır.

```java
private AuthenticationContext mContext;

private void authenticate() {
    String authority = "INSERT-AUTHORITY-HERE";
    String resourceId = "INSERT-RESOURCE-ID-HERE";
    String clientId = "INSERT-CLIENT-ID-HERE";
    String redirectUri = "INSERT-REDIRECT-URI-HERE";
    try {
        mContext = new AuthenticationContext(this, authority, true);
        mContext.acquireToken(this, resourceId, clientId, redirectUri, PromptBehavior.Auto, "", callback);
    } catch (Exception exc) {
        exc.printStackTrace();
    }
}

private AuthenticationCallback<AuthenticationResult> callback = new AuthenticationCallback<AuthenticationResult>() {
    @Override
    public void onError(Exception exc) {
        if (exc instanceof AuthenticationException) {
            Log.d(TAG, "Cancelled");
        } else {
            Log.d(TAG, "Authentication error:" + exc.getMessage());
        }
    }

    @Override
    public void onSuccess(AuthenticationResult result) {
        if (result == null || result.getAccessToken() == null
                || result.getAccessToken().isEmpty()) {
            Log.d(TAG, "Token is empty");
        } else {
            try {
                JSONObject payload = new JSONObject();
                payload.put("access_token", result.getAccessToken());
                ListenableFuture<MobileServiceUser> mLogin = mClient.login("aad", payload.toString());
                Futures.addCallback(mLogin, new FutureCallback<MobileServiceUser>() {
                    @Override
                    public void onFailure(Throwable exc) {
                        exc.printStackTrace();
                    }
                    @Override
                    public void onSuccess(MobileServiceUser user) {
                        Log.d(TAG, "Login Complete");
                    }
                });
            }
            catch (Exception exc){
                Log.d(TAG, "Authentication error:" + exc.getMessage());
            }
        }
    }
};

@Override
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
    super.onActivityResult(requestCode, resultCode, data);
    if (mContext != null) {
        mContext.onActivityResult(requestCode, resultCode, data);
    }
}
```

## <a name="filters"></a>Istemci-sunucu Iletişimini ayarlama

Istemci bağlantısı normalde Android SDK ile sağlanan temel HTTP kitaplığını kullanan temel bir HTTP bağlantısıdır.  Bunu değiştirmek istemenizin birkaç nedeni vardır:

* Zaman aşımlarını ayarlamak için alternatif bir HTTP kitaplığı kullanmak istiyorsunuz.
* Bir ilerleme çubuğu sağlamak istiyorsunuz.
* API Management işlevselliğini desteklemek için özel bir üst bilgi eklemek istiyorsunuz.
* Yeniden kimlik doğrulamayı uygulayabilmeniz için, başarısız bir yanıtı kesmeye devam etmek istiyorsunuz.
* Arka uç isteklerini bir analiz hizmetine kaydetmek istiyorsunuz.

### <a name="using-an-alternate-http-library"></a>Alternatif bir HTTP Kitaplığı kullanma

İstemci başvurunuz oluşturduktan hemen sonra `.setAndroidHttpClientFactory()` yöntemini çağırın.  Örneğin, bağlantı zaman aşımını 60 saniye olarak ayarlamak için (varsayılan 10 saniye yerine):

```java
mClient = new MobileServiceClient("https://myappname.azurewebsites.net");
mClient.setAndroidHttpClientFactory(new OkHttpClientFactory() {
    @Override
    public OkHttpClient createOkHttpClient() {
        OkHttpClient client = new OkHttpClient();
        client.setReadTimeout(60, TimeUnit.SECONDS);
        client.setWriteTimeout(60, TimeUnit.SECONDS);
        return client;
    }
});
```

### <a name="implement-a-progress-filter"></a>Ilerleme filtresi uygulama

@No__t-0 uygulayarak her istek için bir kesme uygulayabilirsiniz.  Örneğin, aşağıdaki bir önceden oluşturulmuş ilerleme çubuğunu güncelleştirir:

```java
private class ProgressFilter implements ServiceFilter {
    @Override
    public ListenableFuture<ServiceFilterResponse> handleRequest(ServiceFilterRequest request, NextServiceFilterCallback next) {
        final SettableFuture<ServiceFilterResponse> resultFuture = SettableFuture.create();
        runOnUiThread(new Runnable() {
            @Override
            public void run() {
                if (mProgressBar != null) mProgressBar.setVisibility(ProgressBar.VISIBLE);
            }
        });

        ListenableFuture<ServiceFilterResponse> future = next.onNext(request);
        Futures.addCallback(future, new FutureCallback<ServiceFilterResponse>() {
            @Override
            public void onFailure(Throwable e) {
                resultFuture.setException(e);
            }
            @Override
            public void onSuccess(ServiceFilterResponse response) {
                runOnUiThread(new Runnable() {
                    @Override
                    public void run() {
                        if (mProgressBar != null)
                            mProgressBar.setVisibility(ProgressBar.GONE);
                    }
                });
                resultFuture.set(response);
            }
        });
        return resultFuture;
    }
}
```

Bu filtreyi istemciye aşağıdaki şekilde ekleyebilirsiniz:

```java
mClient = new MobileServiceClient(applicationUrl).withFilter(new ProgressFilter());
```

### <a name="customize-request-headers"></a>Istek üstbilgilerini özelleştirme

Aşağıdaki `ServiceFilter` ' i kullanın ve filtreyi `ProgressFilter` ile aynı şekilde ekleyin:

```java
private class CustomHeaderFilter implements ServiceFilter {
    @Override
    public ListenableFuture<ServiceFilterResponse> handleRequest(ServiceFilterRequest request, NextServiceFilterCallback next) {
        runOnUiThread(new Runnable() {
            @Override
            public void run() {
                request.addHeader("X-APIM-Router", "mobileBackend");
            }
        });
        SettableFuture<ServiceFilterResponse> result = SettableFuture.create();
        try {
            ServiceFilterResponse response = next.onNext(request).get();
            result.set(response);
        } catch (Exception exc) {
            result.setException(exc);
        }
    }
}
```

### <a name="conversions"></a>Otomatik serileştirme yapılandırma

[Gson][3] API kullanarak her sütun için geçerli olan bir dönüştürme stratejisi belirtebilirsiniz. Android istemci kitaplığı, veriler Azure App Service gönderilmeden önce Java nesnelerini JSON verilerine seri hale getirmek için arka planda [gson][3] 'yı kullanır.  Aşağıdaki kod, stratejiyi ayarlamak için **Setfieldnamingstrateji ()** yöntemini kullanır. Bu örnek, ilk karakteri (bir "a") silecek ve sonra her alan adı için bir sonraki karakteri küçük harfe düşüracaktır. Örneğin, "PARÇAAL" öğesini "ID" olarak etkinleştirebilir.  Birçok alanda `SerializedName()` ek açıklaması gereksinimini azaltmak için bir dönüştürme stratejisi uygulayın.

```java
FieldNamingStrategy namingStrategy = new FieldNamingStrategy() {
    public String translateName(File field) {
        String name = field.getName();
        return Character.toLowerCase(name.charAt(1)) + name.substring(2);
    }
}

client.setGsonBuilder(
    MobileServiceClient
        .createMobileServiceGsonBuilder()
        .setFieldNamingStrategy(namingStrategy)
);
```

Bu kod, **MobileServiceClient**kullanarak bir mobil istemci başvurusu oluşturmadan önce yürütülmelidir.

<!-- URLs. -->
[Get started with Azure Mobile Apps]: app-service-mobile-android-get-started.md
[ASCII control codes C0 and C1]: https://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[Mobile Services SDK for Android]: https://go.microsoft.com/fwlink/p/?LinkID=717033
[Azure portal]: https://portal.azure.com
[Kimlik doğrulaması ile çalışmaya başlama]: app-service-mobile-android-get-started-users.md
[1]: https://static.javadoc.io/com.google.code.gson/gson/2.8.5/com/google/gson/JsonObject.html
[2]: https://hashtagfail.com/post/44606137082/mobile-services-android-serialization-gson
[3]: https://www.javadoc.io/doc/com.google.code.gson/gson/2.8.5
[4]: https://go.microsoft.com/fwlink/p/?LinkId=296840
[5]: app-service-mobile-android-get-started-push.md
[6]: ../notification-hubs/notification-hubs-push-notification-overview.md#integration-with-app-service-mobile-apps
[7]: app-service-mobile-android-get-started-users.md#cache-tokens
[8]: https://azure.github.io/azure-mobile-apps-android-client/com/microsoft/windowsazure/mobileservices/table/MobileServiceTable.html
[9]: https://azure.github.io/azure-mobile-apps-android-client/com/microsoft/windowsazure/mobileservices/MobileServiceClient.html
[10]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[11]: app-service-mobile-node-backend-how-to-use-server-sdk.md
[12]: https://azure.github.io/azure-mobile-apps-android-client/
[13]: app-service-mobile-android-get-started.md#create-a-new-azure-mobile-app-backend
[14]: https://go.microsoft.com/fwlink/p/?LinkID=717034
[15]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#define-table-controller
[16]: app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations
[17]: https://developer.android.com/reference/java/util/UUID.html
[18]: https://github.com/google/guava/wiki/ListenableFutureExplained
[19]: https://www.odata.org/documentation/odata-version-3-0/
[20]: https://hashtagfail.com/post/46493261719/mobile-services-android-querying
[21]: https://github.com/Azure-Samples/azure-mobile-apps-android-quickstart
[22]: ../app-service/configure-authentication-provider-aad.md
[Future]: https://developer.android.com/reference/java/util/concurrent/Future.html
[AsyncTask]: https://developer.android.com/reference/android/os/AsyncTask.html
