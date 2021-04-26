---
title: Azure Active Directory B2C ile N8 kimliğini yapılandırma öğreticisi
titleSuffix: Azure AD B2C
description: Müşteri hesapları geçişi ve müşteri hizmetleri Istekleri (CSR) yönetimi için Azure Active Directory B2C ile Accesshub yönetici aracını yapılandırma öğreticisi.
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/26/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 9fee7965e7834f0e05dba91f8652a0e155087b14
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 04/09/2021
ms.locfileid: "107257883"
---
# <a name="tutorial-for-configuring-theaccesshub-admin-tool-with-azure-active-directory-b2c"></a>Azure Active Directory B2C ile Accesshub yönetici aracını yapılandırma öğreticisi

Bu örnek öğreticide, N8 Identity 'tan [Accesshub yönetim aracı](https://n8id.com/products/theaccesshub-admintool/) ile Azure ACTIVE DIRECTORY (ad) B2C 'nin nasıl tümleştirileceğini gösteren yönergeler sağlıyoruz. Bu çözüm, müşteri hesaplarının geçişini ve müşteri hizmeti Istekleri (CSR) yönetimini ele alınmaktadır.  

Aşağıdaki gereksinimlerinizin bir veya daha fazlası varsa, bu çözüm sizin için uygundur:

- Mevcut bir siteniz var ve Azure AD B2C geçirmek istiyorsunuz. Ancak, parolalar da dahil olmak üzere müşteri hesaplarınızın geçişine karşı bir sorun yaşıyorsanız

- CSR 'nizin Azure AD B2C hesaplarını yönetmesine yönelik bir araç gerekir.

- CSR 'niz için Temsilcili yönetim kullanma gereksiniminiz vardır.

- Çok sayıda depolardaki verilerinizi Azure AD B2C olarak eşitleyip birleştirmek istiyorsunuz.

## <a name="pre-requisites"></a>Ön koşullar

Başlamak için şunlar gerekir:

- Bir Azure AD aboneliği. Aboneliğiniz yoksa [ücretsiz bir hesap](https://azure.microsoft.com/free/)alabilirsiniz.

- [Azure AD B2C kiracısı](./tutorial-create-tenant.md). Kiracının Azure aboneliğinize bağlı olması gerekir.

- Bir TheAccessHub yönetici aracı ortamı: yeni bir ortam sağlamak için [N8 Identity](https://n8id.com/contact/) Ile iletişim kurun.

- Seçim Müşteri verilerini geçirmek istediğiniz veritabanları veya Basit Dizin erişim protokolleri (LDAPs) için bağlantı ve kimlik bilgileri.

- Seçim [Özel ilkeleri](./tutorial-create-user-flows.md?pivots=b2c-custom-policy)kullanmak için Azure AD B2C ortamı, bu ilkeyi kaydolma ilkesi akışınızla bütünleştirmek istiyorsanız kullanın.

## <a name="scenario-description"></a>Senaryo açıklaması

TheAccessHub Yönetim Aracı, Azure 'daki diğer tüm uygulamalar gibi çalışır. Bu, N8 Identity 'ın Azure aboneliğinde veya müşterinin aboneliğinde çalıştırılabilir. Aşağıdaki mimari diyagram uygulamayı gösterir.

![N8identity mimari diyagramını gösteren resim](./media/partner-n8identity/n8identity-architecture-diagram.png)

|Adım | Description |
|:-----| :-----------|
| 1. | Kullanıcı bir oturum açma sayfasına ulaştı. Kullanıcılar yeni bir hesap oluşturmak ve sayfaya bilgi girmek için kaydol ' u seçer. Azure AD B2C Kullanıcı özniteliklerini toplar.
| 2. | Azure AD B2C, TheAccessHub yönetim aracını çağırır ve Kullanıcı özniteliklerine geçirir
| 3. | TheAccessHub yönetim aracı mevcut kullanıcı bilgileri için mevcut veritabanınızı denetler.  
| 4. | Kullanıcı kaydı veritabanından Accesshub yönetim aracına eşitlenir.
| 5. | TheAccessHub Yönetici Aracı, verileri temsilci olarak verilen CSR/yardım masası Yöneticisi ile paylaşır.
| 6. | TheAccessHub Yönetim Aracı, Kullanıcı kayıtlarını Azure AD B2C eşitler.
| 7. |TheAccessHub yönetici aracından başarı/başarısızlık yanıtına bağlı olarak, Azure AD B2C kullanıcıya özelleştirilmiş bir hoş geldiniz e-postası gönderir.

## <a name="create-a-global-admin-in-your-azure-ad-b2c-tenant"></a>Azure AD B2C kiracınızda genel yönetici oluşturma

TheAccessHub admin Aracı, Kullanıcı bilgilerini okumak ve Azure AD B2C kiracınızda değişiklikler yapmak için bir genel yönetici adına hareket etmek için izinler gerektirir. Düzenli yöneticilerinizdeki değişiklikler kazanıldı; t, Accesshub yönetim aracının kiracı ile etkileşim kurma yeteneğini etkiler.

Genel yönetici oluşturmak için aşağıdaki adımları izleyin:

1. Azure portal, Azure AD B2C kiracınızda yönetici olarak oturum açın. **Azure Active Directory**  >  **kullanıcılara** git
2. **Yeni Kullanıcı** Seç
3. Bir müşteri değil normal Dizin kullanıcısı oluşturmak için **Kullanıcı oluştur** ' a tıklayın
4. Kimlik bilgileri formunu doldurun

   a. ' Theaccesshub ' gibi kullanıcı adını girin

   b. ' TheAccessHub Service Account ' gibi bir ad girin

5. **Parolayı göster** ' i seçin ve daha sonra kullanmak üzere ilk parolayı kopyalayın
6. Genel yönetici rolünü atama

   a. Kullanıcının geçerli rol **kullanıcısını** değiştirmesini seçin

   b. Genel yönetici kaydını denetleme

   c. **Seç**  >  **Oluştur**

## <a name="connect-theaccesshub-admin-tool-with-your-azure-ad-b2c-tenant"></a>Azure AD B2C kiracınızla birlikte Accesshub yönetici aracını bağlayın

TheAccessHub Yönetim Aracı, dizininizde değişiklik yapmak ve bunları okumak için Microsoft 'un Graph API kullanır. Kiracınızda genel yönetici işlevi görür. Araç içinden izin verdiğiniz The Accesshub yönetim aracı için ek izin gerekir.

Dizininize erişmek için Accesshub yönetici aracını yetkilendirmek için aşağıdaki adımları izleyin:

1. Size N8 Identity tarafından sunulan kimlik bilgilerini kullanarak Accesshub yönetici aracında oturum açın

2. **System Admin**  >  **Azure AD B2C config** 'e gidin

3. **Yetkilendir bağlantısını** seçin

4. Yeni pencerede, genel Yönetici hesabınızla oturum açın. Yeni hizmet hesabı ile ilk kez oturum açıyorsanız parolanızı sıfırlamanız istenebilir.

5. Komut istemlerini izleyin **ve izin ver ' i seçerek** Accesshub yönetici aracına istenen izinleri verin.

## <a name="configure-a-new-csr-user-using-your-enterprise-identity"></a>Kurumsal kimliğinizi kullanarak yeni bir CSR kullanıcısı yapılandırma

Mevcut kurumsal Azure Active Directory kimlik bilgilerini kullanarak Accesshub yönetim aracına erişen bir CSR/yardım masası kullanıcısı oluşturun.

CSR/yardım masası kullanıcısını çoklu oturum açma (SSO) ile yapılandırmak için aşağıdaki adımlar önerilir:

1. N8 Identity tarafından belirtilen kimlik bilgilerini kullanarak Accesshub yönetici aracında oturum açın.

2. **Yönetici araçları**  >  **iş arkadaşlarınızı Yönet** ' e gidin

3. **Iş arkadaşı ekle** 'yi seçin

4. **Iş arkadaşı türünü seçin Azure Yöneticisi**

5. İş arkadaşınızın profil bilgilerini girin

   a. Bir giriş kuruluşunun seçilmesi, bu kullanıcıyı yönetme iznine sahip olduğunu denetler.

   b. Oturum açma KIMLIĞI/Azure AD Kullanıcı adı için kullanıcının Azure Active Directory hesabından Kullanıcı asıl adını sağlayın.

   c. TheAccessHub rolleri sekmesinde yönetilen rol Yardım Masası ' nı işaretleyin. Kullanıcının iş arkadaşlarınızı yönetme görünümüne erişmesine izin verir. Kullanıcının yine de bir gruba yerleştirilmesi veya bir kuruluş sahibinden müşteriler üzerinde işlem yapması gerekir.

6. **Gönder**’i seçin.

## <a name="configure-a-new-csr-user-using-a-new-identity"></a>Yeni bir kimlik kullanarak yeni bir CSR kullanıcısı yapılandırma

Accesshub yönetim aracına özel yeni bir kimlik bilgileriyle erişen bir CSR/yardım masası kullanıcısı oluşturun. Bu, temel olarak kurumsal için bir Azure AD kullanmayan kuruluşlar tarafından kullanılır.

SSO olmadan [BIR CSR/yardım masası kullanıcısı ayarlamak](https://youtu.be/iOpOI2OpnLI) için şu adımları izleyin:

1. N8 Identity tarafından belirtilen kimlik bilgilerini kullanarak Accesshub yönetici aracında oturum açın

2. **Yönetici araçları**  >  **iş arkadaşlarınızı Yönet** ' e gidin

3. **Iş arkadaşı ekle** 'yi seçin

4. **Iş arkadaşı türü yerel yönetici** seçin

5. İş arkadaşınızın profil bilgilerini girin

   a. Bir giriş kuruluşunun seçilmesi, bu kullanıcıyı yönetme iznine sahip olduğunu denetler.

   b. **Theaccesshub rolleri** sekmesinde yönetilen rol **Yardım Masası**' nı seçin. Kullanıcının iş arkadaşlarınızı yönetme görünümüne erişmesine izin verir. Kullanıcının yine de bir gruba yerleştirilmesi veya bir kuruluş sahibinden müşteriler üzerinde işlem yapması gerekir.

6. **Oturum açma kimliği/e-posta** ve **bir kerelik parola** özniteliklerini kopyalayın. Bunu yeni kullanıcıya sağlayın. Bu kimlik bilgilerini kullanarak Accesshub yönetici aracında oturum açın. Kullanıcıdan ilk oturum açmada yeni bir parola girmesi istenir.

7. **Gönder** ' i seçin

## <a name="configure-partitioned-csr-administration"></a>Bölümlenmiş CSR yönetimini yapılandırma

Müşteri ve CSR/yardım masası kullanıcılarını, TheAccessHub yönetici aracında yönetme izinleri bir kuruluş hiyerarşisi kullanımıyla yönetilir. Tüm iş arkadaşları ve müşterilerin bulundukları bir ev organizasyonu vardır. Belirli iş arkadaşları veya iş arkadaşları grupları, kuruluşların sahipleri olarak atanabilir.  Kuruluş sahipleri, kuruluşlardaki iş arkadaşlarınızla kullanıcıları veya alt kuruluşları yönetebilir (değişiklik yapabilir). Birden çok iş arkadaşın bir kullanıcı kümesini yönetmesine izin vermek için, bir grup birçok üye ile oluşturulabilir. Grup daha sonra bir kuruluş sahibi olarak atanabilir ve tüm grup üyeleri kuruluştaki iş arkadaşlarınızı ve müşterileri yönetebilir.

### <a name="create-a-new-group"></a>Yeni grup oluşturma

1. Size N8 Identity tarafından sunulan **kimlik bilgilerini** kullanarak Accesshub yönetici aracında oturum açın

2. Kuruluşa git **> Grupları Yönet**

3. **Grup eklemek** > seçin

4. **Grup adı**, **Grup açıklaması** ve **Grup sahibi** girin

5. Grubun üyesi olmak istediğiniz iş arkadaşları üzerinde arama yapın ve onay kutularını işaretleyin >**Ekle** ' yi seçin.

6. Sayfanın en altında, grubun tüm üyelerini görebilirsiniz.

7. Gerekli Üyeler, satırın sonundaki **x** seçilerek kaldırılabileceği zaman

8. **Gönder** ' i seçin

### <a name="create-a-new-organization"></a>Yeni bir kuruluş oluşturma

1. Size N8 Identity tarafından sunulan kimlik bilgilerini kullanarak Accesshub yönetici aracında oturum açın

2. Kuruluşa git > **kuruluşları yönetme**

3. **Kuruluş eklemek** > seçin

4. **Kuruluş adı**, **kuruluş sahibi** ve **üst kuruluş** sağlayın.

    a. Kuruluş adı ideal olarak Müşteri verilerinize karşılık gelen bir değerdir. İş arkadaşı ve müşteri verileri yüklenirken, yük içinde kuruluşun adını sağlarsanız, iş arkadaşı kuruluşa otomatik olarak yerleştirilebilir.

    b. Sahibi, bu kuruluştaki müşterileri ve iş arkadaşlarınızı ve içindeki herhangi bir alt kuruluşu yönetecek kişiyi veya grubu temsil eder.

    c. Üst kuruluş, bu kuruluştan sorumlu olan diğer kuruluşun hangi diğer kuruluşa ait olduğunu gösterir.

5. **Gönder**’i seçin.

### <a name="modify-the-hierarchy-via-the-tree-view"></a>Hiyerarşiyi ağaç görünümü aracılığıyla değiştirme

1. Size N8 Identity tarafından sunulan kimlik bilgilerini kullanarak Accesshub yönetici aracında oturum açın

2. **Yönetici araçları**  >  **ağaç görünümüne** git

3. Bu gösterimde hangi iş arkadaşlarınızın ve grupların hangi kuruluşların yönetebileceğini görselleştirebilirsiniz.

4. Hiyerarşiler, kendileri tarafından üst üste olmasını istediğiniz kuruluşlar fazla olan kuruluşlar sürüklenerek değiştirilebilir.

5. Hiyerarşiyi değiştirmeyi bitirdiğinizde **Kaydet** ' i seçin.

## <a name="customize-welcome-notification"></a>Hoş geldiniz bildirimini özelleştirme

Kullanıcıları önceki bir kimlik doğrulama çözümünden Azure AD B2C geçirmek için, Accesshub kullanırken, geçiş sırasında kullanıcıya gönderilen Kullanıcı hoş geldiniz bildirimini özelleştirmek isteyebilirsiniz. Bu ileti normalde Azure AD B2C dizininde yeni bir parola ayarlamak için müşterinin bağlantısını içerir.

Bildirimi özelleştirmek için:

1. Size N8 Identity tarafından sunulan kimlik bilgilerini kullanarak Accesshub 'da oturum açın

2. **Sistem Yöneticisi**  >  **bildirimlerine** git

3. **Iş arkadaşı şablonu oluştur ' u** seçin

4. **Düzenle** 'yi seçin

5. Ileti ve şablon alanlarını gereken şekilde değiştirin. Şablon alanı HTML ile uyumlu değildir ve müşteri e-postalarına HTML biçimli bildirimler gönderebilir.

6. Bittiğinde **Kaydet** ' i seçin.

## <a name="migrate-data-from-external-data-sources-to-azure-ad-b2c"></a>Dış veri kaynaklarından verileri Azure AD B2C 'ye geçirme

Accesshub yönetim aracını kullanarak çeşitli veritabanlarındaki verileri, LDAPs ve CSV dosyalarını içeri aktarabilir ve ardından bu verileri Azure AD B2C kiracınıza gönderebilirsiniz. Bu işlem, verileri Azure AD B2C Kullanıcı iş arkadaşı türüne yükleyerek yapılır.  Verilerin kaynağı Azure 'un kendisi değilse, veriler hem The Accesshub yönetim aracına hem de Azure AD B2C yerleştirilir. Dış verilerinizin kaynağı makinenizde basit bir. csv dosyası değilse veri yükünü yapmadan önce bir veri kaynağı ayarlayın. Aşağıdaki adımlarda, veri kaynağı oluşturma ve veri yükleme işlemleri açıklanır.

### <a name="configure-a-new-data-source"></a>Yeni bir veri kaynağı yapılandırma

1. Size N8 Identity tarafından sunulan kimlik bilgilerini kullanarak Accesshub yönetici aracında oturum açın

2. **Sistem Yöneticisi**  >  **veri kaynaklarına** git

3. **Veri kaynağı Ekle** ' yi seçin

4. Bu veri kaynağı için bir **ad** ve **tür** belirtin

5. Veri kaynağı türüne bağlı olarak form verilerini girin:

   **Veritabanları için**

   a. **Tür** – veritabanı

   b. **Veritabanı türü** : desteklenen veritabanı türlerinden birindeki bir veritabanını seçin.

   c. **Bağlantı URL 'si** : iyi BIÇIMLENDIRILMIŞ bir JDBC bağlantı dizesi girin. Örneğin: ``jdbc:postgresql://myhost.com:5432/databasename``

   d. **Kullanıcı adı** – veritabanına erişmek için Kullanıcı adını girin

   e. **Parola** – veritabanına erişmek için parolayı girin

   f. **Sorgu** – müşteri ayrıntılarını AYıKLAMAK için SQL sorgusunu girin. Örneğin: ``SELECT * FROM mytable;``

   örneğin: Bağlantıyı **Sına**' yı seçin, bağlantının çalıştığından emin olmak için verilerinizin bir örneğini görürsünüz.

   **LDAPs için**

   a. **Tür** – LDAP

   b. **Ana bilgisayar** – LDAP sunucusunun çalıştığı makine için ana bilgisayar adı veya IP 'yi girin. Örneğin: ``mysite.com``

   c. **Bağlantı noktası** : LDAP sunucusunun dinlediği bağlantı noktası numarasını girin.

   d. **SSL** – Accesshub YÖNETIM aracının SSL kullanarak LDAP ile güvenli bir şekilde iletişim kurması gerekiyorsa kutuyu işaretleyin. SSL kullanmak kesinlikle önerilir.

   e. **Oturum açma DN** – oturum açmak için Kullanıcı hesabının DN 'sini gırın ve LDAP aramasını yapın

   f. **Parola** – kullanıcının parolasını girin

   örneğin: **Temel DN** – arama yapmak istediğiniz hiyerarşinin en üstünde DN 'yi girin

   h. **Filtre** – müşteri KAYıTLARıNıZı alacak LDAP Filtre dizesini girin

   i. **Öznitelikler** – Accesshub yönetim aracına geçirilecek müşteri kayıtlarınızda özniteliklerin virgülle ayrılmış bir listesini girin

   j. **Sınama bağlantısını** seçin, bağlantının çalıştığından emin olmak için verilerinizin bir örneğini görürsünüz.

   **OneDrive için**

   a. **Tür** – OneDrive iş

   b. **Yetkilendir bağlantısını** seçin

   c. OneDrive hesabınıza yönelik okuma erişimi olan bir kullanıcıyla oturum açmak için yeni bir pencere, **OneDrive**'da oturum açmanız istenir. TheAccessHub admin Aracı, bu kullanıcının CSV yükleme dosyalarını okumasını sağlayacak.

   d. Komut istemlerini izleyin **ve izin ver ' i seçerek** Accesshub yönetici aracına istenen izinleri verin.

6. Bittiğinde **Kaydet** ' i seçin.  

### <a name="synchronize-data-from-your-data-source-into-azure-ad-b2c"></a>Veri kaynağınızdaki verileri Azure AD B2C ile eşitler

1. Size N8 Identity tarafından sunulan kimlik bilgilerini kullanarak Accesshub yönetici aracında oturum açın

2. **Sistem Yöneticisi**  >  **veri eşitlemesine** git

3. **Yeni Yük** Seç

4. Kullanıcı Azure AD B2C **Iş arkadaşı türünü** seçin

5. **Kaynak**' ı seçin, açılır iletişim kutusunda veri kaynağınızı seçin. OneDrive veri kaynağı oluşturduysanız, dosyayı da seçin.

6. Bu yük ile yeni müşteri hesapları oluşturmak istemiyorsanız, ilk ilkeyi değiştirin: **iş arkadaşından daha sonra herhangi** bir **şey yapma**

7. Var olan müşteri hesaplarını bu yük ile güncelleştirmek istemiyorsanız, **kaynak ve ardından Accesshub verilerinin eşleşmemesi durumunda** ikinci Ilkeyi değiştirip **hiçbir şey yapmayın**

8. **İleri**’yi seçin

9. **Arama eşleme yapılandırmasında**, yük kayıtlarını Accesshub yönetici aracına zaten yüklenmiş müşterilerle nasıl ilişkilendirirsiniz. Kaynakta bir veya daha fazla tanımlayıcı öznitelik seçin. Öznitelikleri, aynı değerleri tutan TheAccessHub yönetici aracında bir özniteliğiyle eşleştirin. Bir eşleşme bulunursa, var olan kayıt geçersiz kılınır; Aksi takdirde, yeni bir müşteri oluşturulur. Bu denetimlerin sayısını sıraya alabilirsiniz. Örneğin, önce e-postayı, sonra adı ve soyadı ' nı kontrol edebilirsiniz.

10. Sol taraftaki menüde **veri eşleme**' yi seçin.

11. Data-Mapping yapılandırmasında, hangi TheAccessHub yönetici aracı özniteliklerinin kaynak özniteliklerinden doldurulması gerektiğini atayın. Tüm özniteliklerin eşlenme gereksinimi yoktur. Eşlenmemiş öznitelikler, mevcut müşteriler için değişmeden kalacak.

12. Varolan bir kuruluşun adı olan bir değerle org_name özniteliğiyle eşleme yaparsanız oluşturulan yeni müşteriler bu kuruluşa yerleştirilir.

13. **İleri**’yi seçin

14. Bu yük yeniden oluşturulması gerekiyorsa günlük/haftalık veya aylık zamanlama belirtilebilir. Aksi **takdirde varsayılan değerini** koruyun.

15. **Gönder** ' i seçin

16. **Şimdi zamanlama** seçildiyse, veri eşitlemeleri ekranına hemen yeni bir kayıt eklenecektir. Doğrulama aşaması %100 değerine ulaştığında, yük için beklenen sonucu görmek üzere **yeni kaydı** seçin. Zamanlanan yüklemeler için, bu kayıtlar yalnızca zamanlanan süreden sonra görüntülenir.

17. Hata yoksa, değişiklikleri kaydetmek için **Çalıştır** ' ı seçin. Aksi takdirde, yükü kaldırmak için **daha fazla** menüden **Kaldır** ' ı seçin. Daha sonra kaynak verileri düzeltebilir veya eşlemeleri yükleyip yeniden deneyebilirsiniz. Bunun yerine, hata sayısı küçükse, düzeltmeleri yapmak için kayıtları el ile güncelleştirebilir ve her kayıtta **Güncelleştir** ' i seçebilirsiniz. Son olarak, herhangi bir hatayla devam edebilir ve daha sonra, bu dosyaları daha sonra, Accesshub yönetici aracında **müdahale** etmeyi daha sonra çözebilirsiniz.

18. **Veri eşitleme** kaydı yük aşamasında %100 olduğunda, yüklemeden kaynaklanan tüm değişiklikler başlatılacaktır. Müşteriler Azure AD B2C ' de görünmeli veya değişiklikleri alacak şekilde başlamalıdır.

## <a name="synchronize-azure-ad-b2c-customer-data"></a>Azure AD B2C Müşteri verilerini eşitler 

Tek seferlik veya devam eden bir işlem olarak, The Accesshub admin Aracı, tüm müşteri bilgilerini Azure AD B2C 'den Accesshub yönetim aracına eşitleyebilir. Bu, CSR/yardım masası yöneticilerinin güncel müşteri bilgilerini görmesini sağlar.

Azure AD B2C verileri ' den Accesshub yönetim aracı ile eşzamanlı hale getirmek için:

1. Size N8 Identity tarafından sunulan kimlik bilgilerini kullanarak Accesshub yönetici aracında oturum açın

2. **Sistem Yöneticisi**  >  **veri eşitlemesine** git

3. **Yeni Yük** Seç

4. Kullanıcı Azure AD B2C **Iş arkadaşı türünü** seçin

5. **Seçenekler** adımı için varsayılan değerleri bırakın.

6. **İleri**’yi seçin

7. **Veri eşleme & arama** adımı için varsayılan değerleri bırakın. Öznitelik **org_name** mevcut bir kuruluşun adı olan bir değerle eşleştirdiyseniz, oluşturulan yeni müşteriler bu kuruluşa yerleştirilir.

8. **İleri**’yi seçin

9. Bu yük yeniden oluşturulması gerekiyorsa günlük/haftalık veya aylık zamanlama belirtilebilir. Aksi takdirde, varsayılan olarak kalsın: **Şimdi**. Azure AD B2C, düzenli aralıklarla eşitleme yapmanızı öneririz.

10. **Gönder** ' i seçin

11. **Şimdi** zamanlama seçildiyse, veri eşitlemeleri ekranına hemen yeni bir kayıt eklenecektir. Doğrulama aşaması %100 değerine ulaştığında, yük için beklenen sonucu görmek üzere yeni kaydı seçin. Zamanlanan yüklemeler için, bu kayıtlar yalnızca zamanlanan süreden sonra görüntülenir.

12. Hata yoksa, değişiklikleri kaydetmek için **Çalıştır** ' ı seçin. Aksi takdirde, yükü kaldırmak için daha fazla menüden **Kaldır** ' ı seçin. Daha sonra kaynak verileri düzeltebilir veya eşlemeleri yükleyip yeniden deneyebilirsiniz. Bunun yerine, hata sayısı küçükse, düzeltmeleri yapmak için kayıtları el ile güncelleştirebilir ve her kayıtta **Güncelleştir** ' i seçebilirsiniz. Son olarak, herhangi bir hatayla devam edebilir ve daha sonra, bu dosyaları daha sonra, Accesshub yönetici aracında müdahale etmeyi daha sonra çözebilirsiniz.

13. **Veri eşitleme** kaydı yük aşamasında %100 olduğunda, yüklemeden kaynaklanan tüm değişiklikler başlatılacaktır.

## <a name="configure-azure-ad-b2c-policies"></a>Azure AD B2C ilkelerini yapılandırma

Bazen Accesshub yönetim aracının eşitlenmesi, Azure AD B2C ile durumunu güncel tutabilme özelliği ile sınırlıdır. Accesshub yönetim aracının API ve Azure AD B2C ilkelerinden yararlanarak, bu değişiklikleri yaptığı gibi, erişen tarafından yapılan değişiklikleri bilgilendirebiliriz. Bu çözüm, [özel ilkeler Azure AD B2C](./user-flow-overview.md)için teknik bilgi gerektirir. Bir sonraki bölümde, yeni hesapların Sign-Up özel ilkeleriniz içindeki Accesshub yönetim aracına bildirimde bulunan bir örnek ilke adımları ve güvenli bir sertifika sunacağız.

### <a name="create-a-secure-credential-to-invoke-theaccesshub-admin-tools-api"></a>The Accesshub yönetim aracının API 'sini çağırmak için güvenli bir kimlik bilgisi oluşturun

1. Size N8 Identity tarafından sunulan kimlik bilgilerini kullanarak Accesshub yönetici aracında oturum açın

2. **Sistem Yöneticisi**  >  **Yönetim Araçları**  >  **API güvenliği** ' ne gidin

3. **Oluştur** ' u seçin

4. **Sertifika parolasını** kopyalama

5. İstemci sertifikasını almak için **İndir** ' i seçin.

6. İstemci sertifikasını Azure AD B2C eklemek için bu [öğreticiyi](./secure-rest-api.md#https-client-certificate-authentication ) izleyin.

### <a name="retrieve-your-custom-policy-examples"></a>Özel ilke örneklerinizi alma

1. Size N8 Identity tarafından sunulan kimlik bilgilerini kullanarak Accesshub yönetici aracında oturum açın

2. **System Admin**  >  **Yönetim Araçları**  >  **Azure B2C ilkeleri** ' ne gidin

3. Kimlik deneyimi çerçevesi yapılandırmanızda Azure AD B2C kiracı etki alanınızı ve iki kimlik deneyimi çerçevesi kimliğini sağlayın

4. **Kaydet**’i seçin

5. Müşterilerin kaydolup oturum açması için kullanıcıları, Accesshub yönetim aracına ekleyen temel ilkelerle bir ZIP dosyası almak için **İndir** ' i seçin.

6. Azure AD B2C ' de özel ilke tasarlamaya başlamak için bu [öğreticiyi](./tutorial-create-user-flows.md?pivots=b2c-custom-policy) izleyin.

## <a name="next-steps"></a>Sonraki adımlar

Daha fazla bilgi için aşağıdaki makaleleri gözden geçirin:

- [Azure AD B2C'deki özel ilkeler](./custom-policy-overview.md)

- [Azure AD B2C özel ilkeleri kullanmaya başlama](tutorial-create-user-flows.md?pivots=b2c-custom-policy)