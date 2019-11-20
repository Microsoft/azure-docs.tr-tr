---
title: Azure Sentinel 'de gelişmiş çok aşamalı saldırı algılama
description: Uyarı fatıg 'yi azaltmak ve gelişmiş çok aşamalı saldırı algılamasına dayalı eylem yapılabilir olaylar oluşturmak için Azure Sentinel 'de Fusion teknolojisini kullanın.
services: sentinel
documentationcenter: na
author: cabailey
manager: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 9/24/2019
ms.author: cabailey
ms.openlocfilehash: e6ddb1b01b705d2a7857682bd84e9482e064a8db
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240079"
---
# <a name="advanced-multistage-attack-detection-in-azure-sentinel"></a>Azure Sentinel 'de gelişmiş çok aşamalı saldırı algılama

Azure Sentinel, Machine Learning 'i temel alan Fusion teknolojisini kullanarak çok aşamalı saldırıları otomatik olarak algılayabilir ve bu da sonlandırma zincirinin çeşitli aşamalarında gözlemlenen anormal davranışları ve şüpheli etkinlikleri birleştirerek. Azure Sentinel, daha sonra yakalamak çok zor olan olaylar oluşturur. Bu olaylar iki veya daha fazla uyarıyı veya etkinliği büyük bir şekilde kaydeder. Tasarım yaparak, bu olaylar düşük hacimdir, yüksek uygunlukta ve yüksek öneme sahiptir.

Ortamınız için özelleştirilmiş, bu algılama yalnızca hatalı pozitif oranları azaltmıyor ancak sınırlı veya eksik bilgilere sahip saldırıları tespit edebilir.

Desteklenen senaryolara yönelik uyarılarla ilgili ayrıntılar için bu sayfadaki [çok aşamalı saldırı algılama için desteklenen senaryolar](#scenarios-supported-for-advanced-multistage-attack-detection) bölümüne bakın.

## <a name="configuration-for-advanced-multistage-attack-detection"></a>Gelişmiş çok aşamalı saldırı algılama yapılandırması

Bu algılama, Azure Sentinel 'de varsayılan olarak etkinleştirilmiştir. Durumu denetlemek veya birden çok uyarıyı temel alan olaylar oluşturmak için alternatif bir çözüm kullandığınız için belki de devre dışı bırakmak için aşağıdaki yönergeleri kullanın:

1. Önceden yapmadıysanız, [Azure portal](https://portal.azure.com)da oturum açın

2. **Azure Sentinel** > yapılandırmaAnalizi > ' ne gidin

3. **Etkin kurallar** ' ı seçin ve **ad** sütununda **Gelişmiş çok aşamalı saldırı algılamayı** bulun. Bu algılamanın etkin veya devre dışı olduğunu doğrulamak için **durum** sütununu kontrol edin.

4. Durumu değiştirmek için bu girişi seçin ve **Gelişmiş çok aşamalı saldırı algılama** dikey penceresinde **Düzenle**' yi seçin.

5. **Kural oluşturma Sihirbazı** dikey penceresinde durum değişikliği sizin için otomatik olarak seçilir, bu nedenle ileri ' yi seçin **: Gözden**geçirin ve sonra **kaydedin**. 

Kural şablonları gelişmiş çok aşamalı saldırı algılama için geçerli değildir.

## <a name="scenarios-supported-for-advanced-multistage-attack-detection"></a>Gelişmiş çok aşamalı saldırı algılama için desteklenen senaryolar

Azure Sentinel, gelişmiş çok aşamalı saldırı algılama kullanarak, Azure Active Directory Kimlik Koruması ve Microsoft Cloud App Security anomali olaylarını birleştiren aşağıdaki senaryoları destekler:

- [Anormal Office 365 etkinliğinin ardından gelen bir konuma imkansız seyahat](#impossible-travel-to-atypical-location-followed-by-anomalous-office-365-activity)
- [Bilinmeyen konum için oturum açma etkinliği ve anormal Office 365 etkinliği](#sign-in-activity-for-unfamiliar-location-followed-by-anomalous-office-365-activity)
- [Virüslü cihazdan sonra gelen oturum açma etkinliği, anormal Office 365 etkinliği](#sign-in-activity-from-infected-device-followed-by-anomalous-office-365-activity)
- [Anonim IP adresinden sonra, anormal Office 365 etkinliğinin oturum açma etkinliği](#sign-in-activity-from-anonymous-ip-address-followed-by-anomalous-office-365-activity)
- [Kullanıcı tarafından sızdırılan kimlik bilgilerine sahip ve anormal Office 365 etkinliğinin oturum açma etkinliği](#sign-in-activity-from-user-with-leaked-credentials-followed-by-anomalous-office-365-activity)

[Azure AD kimlik koruması Data Connector](connect-azure-ad-identity-protection.md) ve [Cloud App Security](connect-cloud-app-security.md) bağlayıcıları yapılandırılmış olmalıdır.

Aşağıdaki açıklamalarda, Azure Sentinel, bu sayfada temsil edilen verilerinizde bulunan gerçek değeri köşeli ayraç içindeki değişken olarak görüntüler. Örneğin, \<hesap *adı*> yerine hesabın gerçek görünen adı ve \< *sayı*> yerine gerçek sayı.

### <a name="impossible-travel-to-atypical-location-followed-by-anomalous-office-365-activity"></a>Anormal Office 365 etkinliğinin ardından gelen bir konuma imkansız seyahat

Microsoft Cloud App Security tarafından oluşturulan Azure AD Kimlik Koruması ve anormal Office 365 uyarılarından gelen olağan konuma karşı olası bir yolculuızı birleştiren yedi Azure Sentinel olayı vardır:

- **Office 365 posta kutusu için önde gelen konumlara yönelik imkansız seyahat**
    
    Bu uyarı, bir kullanıcının gelen kutusunda şüpheli bir konum >, \<olağan olmayan bir konum ve şüpheli bir gelen \<kutusu iletme kuralı ayarlanmış bir *konuma*> *hesap adına*göre oturum açma olayının göstergesidir.
    
    Bu, hesabın güvenliğinin aşıldığını ve bu posta kutusunun kuruluşunuzdaki bilgileri almak için kullanıldığını gösteriyor olabilir. Kullanıcı \< *hesabı adı*, gelen tüm e-postaları > dış adres \< *e-posta adresine*ileten bir gelen kutusu iletme kuralı oluşturdu veya güncelleştirmiş >.

- **Şüpheli bulut uygulaması yönetim etkinliğine yönelik olarak önde gelen konumlara imkansız seyahat**
    
    Bu \<uyarı, *hesap adına*göre bir oturum açma olayının, mümkün \<olmayan bir konuma >, genellikle tipik bir konuma > bir göstergesidir.
    
    Daha \<sonra hesap *hesabı adı*>, tek bir \<oturumdaki > çok *sayıda*yönetim etkinliği üzerinden gerçekleştirilir.

- **Toplu dosya silmeye yönelik olarak önde gelen konumlara imkansız seyahat**
    
    Bu uyarı, genel bir konum \<olan > *konuma*> \< *hesap adına*göre oturum açma olayının göstergesidir. 
    
    Daha \<sonra hesap *hesabı adı*>, tek \<bir oturumdaki > benzersiz dosya *sayısını*sildi.

- **Toplu dosyanın indirileceği sıradan konumlara yönelik imkansız seyahat**
    
    Bu \<uyarı, *hesap adına*göre bir oturum açma olayının, mümkün \<olmayan bir konuma >, genellikle tipik bir konuma > bir göstergesidir. 
    
    Daha \<sonra hesap *hesabı adı*, tek bir oturumdaki \<> benzersiz dosya *sayısı*üzerinden indirilir >.

- **Office 365 kimliğe bürünme için önde gelen konumlara imkansız seyahat**
    
    Bu \<uyarı, *hesap adına*göre bir oturum açma olayının, mümkün \<olmayan bir konuma >, genellikle tipik bir konuma > bir göstergesidir. 
    
    Daha \<sonra hesap *hesabı adı*>, tek bir oturumdaki kimliğe bürünme\<etkinliklerinin olağan dışı bir miktarını (*etkinlik > sayısı*) gerçekleştirdi.

- **Toplu dosya paylaşımına önde gelen konumlara imkansız seyahat**
    
    Bu \<uyarı, *hesap adına*göre bir oturum açma olayının, mümkün \<olmayan bir konuma >, genellikle tipik bir konuma > bir göstergesidir. 
    
    Daha \<sonra hesap *hesabı adı*, tek bir oturumdaki \<> benzersiz dosya *sayısı*üzerinden paylaşılır >.

- **Bulut uygulamasındaki fidye 'ya yönelik olarak önde gelen konumlara imkansız seyahat**
    
    Bu \<uyarı, *hesap adına*göre bir oturum açma olayının, mümkün \<olmayan bir konuma >, genellikle tipik bir konuma > bir göstergesidir. 
    
    Daha sonra \<hesap *hesabı adı*> > Dosya \< *sayısı*karşıya yüklendi ve toplam \<> Dosya *sayısını*sildi. 
    
    Bu etkinlik deseninin olası bir fidye yazılımı saldırısı göstergesi vardır.


### <a name="sign-in-activity-for-unfamiliar-location-followed-by-anomalous-office-365-activity"></a>Bilinmeyen konum için oturum açma etkinliği ve anormal Office 365 etkinliği

Azure AD Kimlik Koruması ve Microsoft Cloud App Security tarafından oluşturulan anormal Office 365 uyarılarından bilmediğiniz konum uyarıları için oturum açma etkinliğini birleştiren yedi Azure Sentinel olayı vardır.

- **Bilinmeyen bir konumdan Exchange Online posta kutusu için bir oturum açma olayı lider**
    
    Bu \<uyarı, *konum*> > \< *hesap adına*göre bir oturum açma olayının göstergesidir ve bir kullanıcının gelen kutusunda şüpheli bir gelen kutusu iletme kuralı, tanıdık bir konum.
    
    Bu, hesabın güvenliğinin aşıldığını ve bu posta kutusunun kuruluşunuzdaki bilgileri almak için kullanıldığını gösteriyor olabilir. Kullanıcı \< *hesabı adı*, gelen tüm e-postaları > dış adres \< *e-posta adresine*ileten bir gelen kutusu iletme kuralı oluşturdu veya güncelleştirmiş >. 

- **Bilinmeyen bir konumdan şüpheli bulut uygulaması yönetim etkinliğine yönelik oturum açma olayı**
    
    Bu uyarı, bilinmeyen bir \<konum olan *konum*> > \< *hesap adına*göre oturum açma olayının göstergesidir. 
    
    Daha \<sonra hesap *hesabı adı*>, tek bir \<oturumdaki > yönetim etkinliği *sayısına*göre gerçekleştirilir.

- **Bilmediğiniz bir konumdan toplu dosya silmeye kadar olan oturum açma etkinliği**
    
    Bu uyarı, bilinmeyen bir \<konum olan *konum*> > \< *hesap adına*göre oturum açma olayının göstergesidir. 
    
    Daha \<sonra hesap *hesabı adı*>, tek \<bir oturumdaki > benzersiz dosya *sayısını*sildi.

- **Bilinmeyen bir konumdan yığın dosya indirme için bir oturum açma olayı**
    
    Bu uyarı, bilinmeyen bir \<konum olan *konum*> > \< *hesap adına*göre oturum açma olayının göstergesidir. 
    
    Daha \<sonra hesap *hesabı adı*, tek bir oturumdaki \<> benzersiz dosya *sayısı*üzerinden indirilir >.

- **Bilmediğiniz bir konumdan Office 365 kimliğe bürünmeye önde gelen oturum açma olayı**
    
    Bu uyarı, bilinmeyen bir \<konum olan *konum*> > \< *hesap adına*göre oturum açma olayının göstergesidir.
    
    Daha \<sonra hesap *hesabı adı*, tek bir oturumdaki \<> farklı hesapların *sayısına*göre kimliğe >.

- **Bilinmeyen bir konumdan yığın dosya paylaşımına önde gelen oturum açma olayı**
    
    Bu uyarı, bilinmeyen bir \<konum olan *konum*> > \< *hesap adına*göre oturum açma olayının göstergesidir. 
    
    Daha \<sonra hesap *hesabı adı*, tek bir oturumdaki \<> benzersiz dosya *sayısı*üzerinden paylaşılır >.

- **Tanınmayan bir konumdan bulut uygulamasındaki fidye 'ya lider olarak oturum açma etkinliği**
    
    Bu uyarı, bilinmeyen bir \<konum olan *konum*> > \< *hesap adına*göre oturum açma olayının göstergesidir. 
    
    Daha sonra \<hesap *hesabı adı*> > Dosya \< *sayısı*karşıya yüklendi ve toplam \<> Dosya *sayısını*sildi. 
    
    Bu etkinlik deseninin olası bir fidye yazılımı saldırısı göstergesi vardır.

### <a name="sign-in-activity-from-infected-device-followed-by-anomalous-office-365-activity"></a>Virüslü cihazdan sonra gelen oturum açma etkinliği, anormal Office 365 etkinliği

Microsoft Cloud App Security tarafından oluşturulan Azure AD Kimlik Koruması ve anormal Office 365 uyarılarından etkilenen cihaz uyarılarından oturum açma etkinliğini birleştiren yedi Azure Sentinel olayı vardır:

- **Virüslü bir cihazdan Office 365 posta kutusu için lider olarak oturum açma etkinliği**
    
    Bu uyarı, kötü amaçlı yazılımlardan etkilenen bir cihazdan *hesap adına*> \<bir oturum açma olayının göstergesidir ve bir kullanıcının gelen kutusunda şüpheli bir gelen kutusu iletme kuralı ayarlanmış olabilir.
    
    Bu, hesabın güvenliğinin aşıldığını ve bu posta kutusunun kuruluşunuzdaki bilgileri almak için kullanıldığını gösteriyor olabilir. Kullanıcı \< *hesabı adı*, gelen tüm e-postaları > dış adres \< *e-posta adresine*ileten bir gelen kutusu iletme kuralı oluşturdu veya güncelleştirmiş >. 

- **Virüslü bir cihazdan şüpheli bulut uygulaması yönetim etkinliğine yönelik oturum açma etkinliği**
    
    Bu uyarı, kötü amaçlı yazılımlardan etkilenme olasılığı bulunan bir cihazdan \< *hesap adına*göre > bir oturum açma olayının göstergesidir.
    
    Daha \<sonra hesap *hesabı adı*>, tek bir \<oturumdaki > yönetim etkinliği *sayısına*göre gerçekleştirilir.

- **Virüslü bir cihazdan toplu dosya silmeye kadar lider olan oturum açma olayı**
    
    Bu uyarı, kötü amaçlı yazılımlardan etkilenme olasılığı bulunan bir cihazdan \< *hesap adına*göre > bir oturum açma olayının göstergesidir. 
    
    Daha \<sonra hesap *hesabı adı*>, tek \<bir oturumdaki > benzersiz dosya *sayısını*sildi.

- **Virüslü bir cihazdan toplu dosya indirme için lider olan oturum açma olayı**
    
    Bu uyarı, kötü amaçlı yazılımlardan etkilenme olasılığı bulunan bir cihazdan \< *hesap adına*göre > bir oturum açma olayının göstergesidir. 
    
    Daha \<sonra hesap *hesabı adı*, tek bir oturumdaki \<> benzersiz dosya *sayısı*üzerinden indirilir >.

- **Virüslü bir cihazdan Office 365 kimliğe bürünmeye önde gelen oturum açma etkinliği**
    
    Bu uyarı, kötü amaçlı yazılımlardan etkilenme olasılığı bulunan bir cihazdan \< *hesap adına*göre > bir oturum açma olayının göstergesidir. 
    
    Daha \<sonra hesap *hesabı adı*, tek bir oturumdaki \<> farklı hesapların *sayısına*göre kimliğe >.

- **Virüslü bir cihazdan toplu dosya paylaşımına lider olarak oturum açma etkinliği**
    
    Bu uyarı, kötü amaçlı yazılımlardan etkilenme olasılığı bulunan bir cihazdan \< *hesap adına*göre > bir oturum açma olayının göstergesidir. 
    
    Daha \<sonra hesap *hesabı adı*, tek bir oturumdaki \<> benzersiz dosya *sayısı*üzerinden paylaşılır >.

- **Etkilenen bir cihazdan, bulut uygulamasındaki fidye 'ya lider olarak oturum açma etkinliği**
    
    Bu uyarı, kötü amaçlı yazılımlardan etkilenme olasılığı bulunan bir cihazdan \< *hesap adına*göre > bir oturum açma olayının göstergesidir. 
    
    Daha sonra \<hesap *hesabı adı*> > Dosya \< *sayısı*karşıya yüklendi ve toplam \<> Dosya *sayısını*sildi. 
    
    Bu etkinlik deseninin olası bir fidye yazılımı saldırısı göstergesi vardır.

### <a name="sign-in-activity-from-anonymous-ip-address-followed-by-anomalous-office-365-activity"></a>Anonim IP adresinden sonra, anormal Office 365 etkinliğinin oturum açma etkinliği

Microsoft Cloud App Security tarafından oluşturulan Azure AD Kimlik Koruması ve anormal Office 365 uyarılarından anonim IP adresi uyarılarından oturum açma etkinliğini birleştiren yedi Azure Sentinel olayı vardır:

- **Anonim bir IP adresinden Office 365 posta kutusu için önde gelen oturum açma olayı**
    
    Bu uyarı, bir kullanıcının gelen kutusunda şüpheli bir gelen kutusu iletme \<kuralı > bir anonim proxy IP \<adresi *IP adresinden*> *hesap adına*göre oturum açma olayının göstergesidir.
    
    Bu, hesabın güvenliğinin aşıldığını ve bu posta kutusunun kuruluşunuzdaki bilgileri almak için kullanıldığını gösteriyor olabilir. Kullanıcı \< *hesabı adı*, gelen tüm e-postaları > dış adres \< *e-posta adresine*ileten bir gelen kutusu iletme kuralı oluşturdu veya güncelleştirmiş >. 

- **Anonim bir IP adresinden, şüpheli bulut uygulaması yönetim etkinliğine yönelik oturum açma olayı**
    
    Bu uyarı, anonim bir proxy IP adresi \< \< *IP adresi*> > *hesap adına*göre oturum açma olayının göstergesidir. 
    
    Daha \<sonra hesap *hesabı adı*>, tek bir \<oturumdaki > yönetim etkinliği *sayısına*göre gerçekleştirilir.

- **Anonim bir IP adresinden toplu dosya silmeye kadar bir oturum açma olayı**
    
    Bu uyarı, anonim bir proxy IP adresi \< \< *IP adresi*> > *hesap adına*göre oturum açma olayının göstergesidir. 
    
    Daha \<sonra hesap *hesabı adı*>, tek \<bir oturumdaki > benzersiz dosya *sayısını*sildi.

- **Anonim bir IP adresinden yığın dosya indirmelerine kadar bir oturum açma olayı**
    
    Bu uyarı, anonim bir proxy IP adresi \< \< *IP adresi*> > *hesap adına*göre oturum açma olayının göstergesidir. 
    
    Daha \<sonra hesap *hesabı adı*, tek bir oturumdaki \<> benzersiz dosya *sayısı*üzerinden indirilir >.

- **Anonim bir IP adresinden Office 365 kimliğe bürünmeye önde gelen oturum açma olayı**
    
    Bu uyarı, anonim bir proxy IP adresi \< \< *IP adresi*> > *hesap adına*göre oturum açma olayının göstergesidir. 
    
    Daha \<sonra hesap *hesabı adı*, tek bir oturumdaki \<> farklı hesapların *sayısına*göre kimliğe >.

- **Anonim bir IP adresinden yığın dosya paylaşımına önde gelen oturum açma olayı**
    
    Bu uyarı, anonim bir proxy IP adresi \< \< *IP adresi*> > *hesap adına*göre oturum açma olayının göstergesidir. 
    
    Daha \<sonra hesap *hesabı adı*, tek bir oturumdaki \<> benzersiz dosya *sayısı*üzerinden paylaşılır >.

- **Bulut uygulamasındaki fidye 'ya anonim IP adresinden oturum açma olayı**
    
    Bu uyarı, anonim bir proxy IP adresi \< \< *IP adresi*> > *hesap adına*göre oturum açma olayının göstergesidir. 
    
    Daha sonra \<hesap *hesabı adı*> > Dosya \< *sayısı*karşıya yüklendi ve toplam \<> Dosya *sayısını*sildi. 
    
    Bu etkinlik deseninin olası bir fidye yazılımı saldırısı göstergesi vardır.

### <a name="sign-in-activity-from-user-with-leaked-credentials-followed-by-anomalous-office-365-activity"></a>Kullanıcı tarafından sızdırılan kimlik bilgilerine sahip ve anormal Office 365 etkinliğinin oturum açma etkinliği

Azure AD Kimlik Koruması ve Microsoft Cloud App Security tarafından oluşturulan anormal Office 365 uyarılarından sızdırılan kimlik bilgileri uyarıları olan kullanıcıdan oturum açma etkinliğini birleştiren yedi Azure Sentinel olayı vardır:

- **Sızdırılan kimlik bilgilerine sahip kullanıcıdan Office 365 posta kutusu için oturum açma etkinliği**
    
    Bu uyarı, \< *hesap adına*göre oturum açma olayının, sızdırılan kimlik bilgilerini > kullanıldığını ve bir kullanıcının gelen kutusunda şüpheli bir gelen kutusu iletme kuralı ayarlandığını belirten bir göstergesidir. 
    
    Bu, hesabın güvenliğinin aşıldığını ve bu posta kutusunun kuruluşunuzdaki bilgileri almak için kullanıldığını gösteriyor olabilir. Kullanıcı \< *hesabı adı*, gelen tüm e-postaları > dış adres \< *e-posta adresine*ileten bir gelen kutusu iletme kuralı oluşturdu veya güncelleştirmiş >. 

- **Sızdırılan kimlik bilgilerine sahip kullanıcıdan gelen oturum açma olayı, şüpheli bulut uygulaması yönetim etkinliğine**
    
    Bu uyarı, \< *hesap adına*göre oturum açma olayının sızdırılan kimlik bilgilerini > kullandığının göstergesidir.
    
    Daha \<sonra hesap *hesabı adı*>, tek bir \<oturumdaki > yönetim etkinliği *sayısına*göre gerçekleştirilir.

- **Sızdırılan kimlik bilgilerine sahip kullanıcı tarafından yığın dosyası silinmeye birlikte oturum açma etkinliği**
    
    Bu uyarı, \< *hesap adına*göre oturum açma olayının sızdırılan kimlik bilgilerini > kullandığının göstergesidir.
    
    Daha \<sonra hesap *hesabı adı*>, tek \<bir oturumdaki > benzersiz dosya *sayısını*sildi.

- **Sızdırılan kimlik bilgilerine sahip kullanıcı tarafından yığın dosyası indirme için oturum açma etkinliği**
    
    Bu uyarı, \< *hesap adına*göre oturum açma olayının sızdırılan kimlik bilgilerini > kullandığının göstergesidir.
    
    Daha \<sonra hesap *hesabı adı*, tek bir oturumdaki \<> benzersiz dosya *sayısı*üzerinden indirilir >.

- **Sızdırılan kimlik bilgilerine sahip kullanıcıdan Office 365 kimliğe bürünme ile oturum açma etkinliği**
    
    Bu uyarı, \< *hesap adına*göre oturum açma olayının sızdırılan kimlik bilgilerini > kullandığının göstergesidir. 
    
    Daha \<sonra hesap *hesabı adı*, tek bir oturumdaki \<> farklı hesapların *sayısına*göre kimliğe >.

- **Sızdırılan kimlik bilgilerine sahip kullanıcıdan yığın dosya paylaşımına lider olarak oturum açma etkinliği**
    
    Bu uyarı, \< *hesap adına*göre oturum açma olayının sızdırılan kimlik bilgilerini > kullandığının göstergesidir.
    
    Daha \<sonra hesap *hesabı adı*, tek bir oturumdaki \<> benzersiz dosya *sayısı*üzerinden paylaşılır >.

- **Bulut uygulamasındaki fidye 'ya sızdırılan kimlik bilgilerine sahip kullanıcıdan oturum açma olayı**
    
    Bu uyarı, \< *hesap adına*göre oturum açma olayının sızdırılan kimlik bilgilerini > kullandığının göstergesidir. 
    
    Daha sonra \<hesap *hesabı adı*> > Dosya \< *sayısı*karşıya yüklendi ve toplam \<> Dosya *sayısını*sildi. 
    
    Bu etkinlik deseninin olası bir fidye yazılımı saldırısı göstergesi vardır.

## <a name="next-steps"></a>Sonraki adımlar

Artık gelişmiş çok aşamalı saldırı algılama hakkında daha fazla bilgi edindiniz, verilerinize ve olası tehditlere nasıl görünürlük alabileceğinizi öğrenmek için aşağıdaki hızlı başlangıç ile ilgileniyor olabilirsiniz: [Azure Sentinel ile çalışmaya](quickstart-get-visibility.md)başlayın.

Sizin için oluşturulan olayları araştırmaya hazırsanız aşağıdaki öğreticiye bakın: [Azure Sentinel ile olayları araştırın](tutorial-investigate-cases.md).

