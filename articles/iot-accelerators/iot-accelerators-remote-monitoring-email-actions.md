---
title: Uzaktan Izleme içindeki e-posta eylemi-Azure | Microsoft Docs
description: Bu nasıl yapılır kılavuzunda, yeni veya mevcut bir kurala nasıl e-posta eylemi ekleyeceğiniz gösterilmektedir.
author: dominicbetts
manager: hegate
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/12/2018
ms.topic: conceptual
ms.openlocfilehash: c192ba73da0cfaf1832b6a1e572bd71b250a976b
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74168373"
---
# <a name="add-an-email-action"></a>E-posta eylemi ekleme

E-posta eylemleri, uyarıları hiç kaçırmadığınızdan emin olmanıza yardımcı olur. Var olan bir kurala e-posta eylemi ekleyebilir veya yeni bir kural oluşturabilirsiniz.

Bu nasıl yapılır kılavuzundaki adımları tamamlayabilmeniz için, Azure aboneliğinizde uzaktan Izleme çözümü hızlandırıcısının dağıtılan bir örneğine ihtiyacınız vardır.

Bir kural oluşturmak veya değiştirmek için bir [ **yönetici**olmanız ya da doğru izinlere sahip](iot-accelerators-remote-monitoring-rbac.md)olmanız gerekir.

## <a name="edit-an-existing-rule"></a>Var olan kuralı düzenleme

Mevcut bir kurala e-posta eylemi eklemek için aşağıdaki adımları izleyin:

1. Uzaktan Izleme çözümünüze gidin.

1. **Panodan**, **kurallar** sayfasına gidin:

    ![Kurallar sayfası](./media/iot-accelerators-remote-monitoring-email-actions/rules-email.png)

1. Değiştirilecek mevcut kuralın yanındaki onay kutusuna tıklayın ve ardından üst kısımdaki **Düzenle** ' ye tıklayın. Düzenlenebilir bir **kural** bölmesi görüntülenir.

1. **Eylem** bölümünde, **e-posta etkin** ' i **Açık**olarak değiştirin.

1. Çözüm hızlandırıcısında bir e-posta eylemini ilk kez etkinleştirdiğinizde, Outlook 'ta [oturum açmanız](#outlook)gerekir.

1. Alıcı kutusuna bir e-posta adresi girin ve eklenecek her bir e-posta adresi için **ENTER** tuşuna basın:

    ![Adres girdisi](./media/iot-accelerators-remote-monitoring-email-actions/address-email.png)

1. E-posta için bir konu girin.

1. E-posta alıcılarına yönelik ek notları düz metin olarak girin. [E-posta şablonunu DÜZENLERSENIZ](#htmledit)HTML biçimini kullanabilirsiniz.

1. **Kural durumunun** **etkin**olarak ayarlandığından emin olun.

1. **Uygula**'ya tıklayın.

## <a name="create-a-new-rule"></a>Yeni kural oluşturma

Yeni bir kural oluştururken e-posta eylemi eklemek için aşağıdaki adımları izleyin:

1. Uzaktan Izleme çözümünüze gidin.

1. **Panodan**, **kurallar** sayfasına gidin:

    ![Kurallar sayfası](./media/iot-accelerators-remote-monitoring-email-actions/rules-email.png)

1. [Kural oluşturma bölümündeki](iot-accelerators-remote-monitoring-automate.md#create-a-rule)adımları izleyin. [Gelişmiş kural oluşturma](iot-accelerators-remote-monitoring-automate.md#create-an-advanced-rule) bölümündeki adımları, **önem derecesi düzeyi**ayarladığınız noktaya kadar izleyin. Henüz **Uygula** ' yı tıklamayın.

1. **Eylem** bölümünde, **e-posta etkin** ' i **Açık**olarak değiştirin.

1. Çözüm hızlandırıcısında bir e-posta eylemini ilk kez etkinleştirdiğinizde, Outlook 'ta [oturum açmanız](#outlook)gerekir.

1. Alıcı kutusuna bir e-posta adresi girin ve eklenecek her bir e-posta adresi için **ENTER** tuşuna basın:

    ![Adres girdisi](./media/iot-accelerators-remote-monitoring-email-actions/address-email.png)

1. E-posta için bir konu girin.

1. E-posta alıcılarına yönelik ek notları düz metin olarak girin. [E-posta şablonunu DÜZENLERSENIZ](#htmledit)HTML biçimini kullanabilirsiniz.

1. **Kural durumunun** **etkin**olarak ayarlandığından emin olun.

1. **Uygula**'ya tıklayın.

Bir e-posta eylemiyle ilgili kuralınız artık etkinleştirilmiştir. Eylemin her tetiklenişinde alıcılara yeni bir e-posta gönderilir.

## Outlook 'ta oturum açın<a name="outlook"></a>

Çözüm hızlandırıcısında bir e-posta eylemini ilk kez etkinleştirdiğinizde, Outlook oturumu açmanız gerekir. Bu eylem, e-posta bildirimlerini gönderen e-posta hesabını ayarlar.

> [!NOTE]
> Yalnızca Çözüm Hızlandırıcısı bildirimleri için belirli bir Outlook hesabı oluşturmanız ve ilk e-posta eyleminizi etkinleştirdiğinizde bu hesabı kullanmanız gerekir.

### <a name="contributor-role-outlook-setup"></a>Katkıda bulunan rolü Outlook kurulumu

Abonelikte **katkıda** bulunan rolündeki birisi çözüm hızlandırıcıyı dağıttığında, uygulamanın, Web Kullanıcı arabirimi aracılığıyla e-posta eylemlerini ayarlamak ve doğrulamak için yeterli izni yoktur.

Başlamadan önce çözüm hızlandırıcıınızdan e-posta bildirimleri göndermek için kullanılacak bir Outlook hesabı oluşturun.

Aşağıdaki adımlarda, e-posta eylemlerini el ile ayarlama ve doğrulama işlemleri gösterilmektedir:

1. [Azure portalına](https://portal.azure.com) gidin.

1. Çözüm Hızlandırıcılarınızın kaynak grubuna gidin.

1. **Office365-Connector 'a**tıklayın:

    ![API bağlantısı](./media/iot-accelerators-remote-monitoring-email-actions/apiconnector1.png)

1. Yetkilendirme işlemini başlatmak için başlık ' a tıklayın:

    ![yetki](./media/iot-accelerators-remote-monitoring-email-actions/connector1.png)

1. **Yetkilendir**'e tıklayın. Oturum açmanız istenir. Oturum açmak için kullandığınız hesap, uygulamanın e-posta bildirimleri göndermek için kullandığı e-posta adresi olmalıdır:

    ![Yetkilendir düğmesi](./media/iot-accelerators-remote-monitoring-email-actions/authorize.png)

1. Alt kısımdaki **Kaydet** ' e tıklayın. Başlık kaybolmuşsa yetkilendirmenize başarılı olur.

1. Bildirimlerin gönderileceği e-posta adresini değiştirmek için **API bağlantısını Düzenle**' ye tıklayın.

    ![e-postayı değiştir](./media/iot-accelerators-remote-monitoring-email-actions/editemail1.png)

### <a name="owner-role-outlook-setup"></a>Sahip rolü Outlook kurulumu

Abonelik içindeki **sahip** rolündeki birisi çözüm hızlandırıcıyı dağıtrsa, uygulama e-posta eylemlerinin Web Kullanıcı arabirimi aracılığıyla doğru şekilde ayarlandığını doğrulayabilirler.

Başlamadan önce çözüm hızlandırıcıınızdan e-posta bildirimleri göndermek için kullanılacak bir Outlook hesabı oluşturun.

Aşağıdaki adımlar, oturum açmak ve e-posta eylemlerini ayarlamak için size yardımcı olur:

1. Outlook 'ta oturum açmak için tıklayın. Azure portal götürülüyorsunuz:

   ![Outlook 'ta oturum açın](./media/iot-accelerators-remote-monitoring-email-actions/owneroutlook-email.png)

1. **Yetkilendir**'e tıklayın. Oturum açmanız istenir. Oturum açmak için kullandığınız hesap, uygulamanın e-posta bildirimleri göndermek için kullandığı e-posta adresi olmalıdır:

1. **Save (Kaydet)** düğmesine tıklayın. Çözüm hızlandırıcısına dönün ve sayfayı yenileyin.

1. E-posta bildirimini başarıyla yapılandırdıysanız şu iletiyi görürsünüz:

   ![Outlook oturumu başarıyla açıldı](./media/iot-accelerators-remote-monitoring-email-actions/success-email.png)

## E-posta HTML 'sini özelleştirme<a name="htmledit"></a>

Hazır olmayan, uzaktan Izleme çözümü hızlandırıcısı, eylem e-postaları için temel bir HTML şablonu sağlar. E-posta şablonu, e-posta eylemi ayarlarından değerleri kullanır. Örnek bir e-posta aşağıda verilmiştir:

![e-posta örneği](./media/iot-accelerators-remote-monitoring-email-actions/emailtemplate1.png)

Aşağıdaki adımlarda, HTML e-posta şablonunun nasıl düzenleneceği gösterilmektedir. Örneğin, daha fazla bilgi içerebilir veya özel görüntüler ekleyebilirsiniz:

1. Java ya da .NET uzaktan Izleme GitHub deposunu kopyalayın:

1. E-posta şablonu konumuna gidin:
  
    `Dotnet: device-telemetry\ActionsAgent\data\EmailTemplate.html`
  
    `Java device-telemetry/app/resources/data/EmailTemplate.html`

1. İletiyi özelleştirmek için bu şablondaki parametreleri ekleyebilir veya kaldırabilirsiniz. Ayrıca, gerektiğinde çağrılar ekleyebilir, kaldırabilir veya değiştirebilirsiniz:

    Örneğin, .NET kodunda: `emailTemplate = emailTemplate.Replace("${subject}", emailAction.GetSubject());`

    Örneğin, Java kodunda: `this.emailTemplate.replace("${subject}", emailAction.GetSubject());`

1. Şablondaki parametreler `${...}`biçimini alır. Bir parametreyi silmek için gerekli satırı silin. Bir parametre eklemek için, eklenecek değere sahip bir satır ekleyin.

1. Görüntü veya özel metin eklemek için, EmailTemplate. HTML dosyasını doğrudan güncelleştirin.

## <a name="throttling"></a>Azaltma

Uzaktan Izleme Çözüm Hızlandırıcısı, e-posta bildirimleri göndermek için Outlook 'U kullanır. Outlook, [1 dakikada 30 e-posta](https://docs.microsoft.com/office365/servicedescriptions/exchange-online-service-description/exchange-online-limits#receiving-and-sending-limits)ile gönderilen e-posta sayısını sınırlar. E-postaları alan e-posta istemcileri, dakika başına alınan e-postaların sayısını da azaledebilir. Özel e-posta istemciniz hakkında sınırlamalar ile görüşün. Bir kural için e-posta bildirimi ayarladığınızda, kural ortalama değerleri en az bir dakikalık bir dönem boyunca hesaplamalı ve anlık değerler kullanmaz:

![Ortalama hesaplama](./media/iot-accelerators-remote-monitoring-email-actions/calculation-email.png)

## <a name="next-steps"></a>Sonraki adımlar

Bu kılavuzda, uzaktan Izleme çözümünde yeni veya var olan bir kurala e-posta eylemi ekleme işlemi gösterilmektedir. Kılavuz Ayrıca size ve ileti biçimini tanımlayan HTML 'i nasıl düzenleyirsiniz.

Önerilen sonraki adım, [uyarıları kullanmayı ve cihaz sorunlarını gidermeyi](iot-accelerators-remote-monitoring-maintain.md)öğrenmenizi sağlar.
