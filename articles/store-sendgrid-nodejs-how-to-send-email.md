---
title: SendGrid e-posta hizmetini kullanma (Node.js) | Microsoft Docs
description: Azure 'da SendGrid e-posta hizmeti ile e-posta gönderme hakkında bilgi edinin. Node.js API kullanılarak yazılan kod örnekleri.
services: ''
documentationcenter: nodejs
author: erikre
manager: wpickett
editor: ''
ms.assetid: cac444b4-26b0-45ea-9c3d-eca28d57dacb
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 01/05/2016
ms.author: erikre
ms.openlocfilehash: f2d653441598a47986913d525057672eed24b435
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96015441"
---
# <a name="how-to-send-email-using-sendgrid-from-nodejs"></a>Node.js 'den SendGrid kullanarak e-posta gönderme

Bu kılavuzda, Azure 'da SendGrid e-posta hizmetiyle ortak programlama görevlerinin nasıl gerçekleştirileceği gösterilmektedir. Örnekler Node.js API kullanılarak yazılır. Kapsanan senaryolar, **e-posta** oluşturma **, e-posta gönderme**, **ekleri ekleme**, **filtreleri kullanma** ve **Özellikleri güncelleştirme** içerir. SendGrid ve e-posta gönderme hakkında daha fazla bilgi için [sonraki adımlar](#next-steps) bölümüne bakın.

## <a name="what-is-the-sendgrid-email-service"></a>SendGrid e-posta hizmeti nedir?

SendGrid, özel tümleştirmeyi kolaylaştıran esnek API 'lerle birlikte güvenilir [işlem e-posta teslimi], ölçeklenebilirlik ve gerçek zamanlı çözümlemeler sağlayan [bulut tabanlı bir e-posta hizmetidir] . Ortak SendGrid kullanım senaryoları şunları içerir:

* Müşterilere alındıları otomatik olarak gönderme
* Aylık e-Fliers ve özel teklifler göndermek için dağıtım listelerini yönetme
* Engellenen e-posta ve müşteri yanıt verme gibi şeyler için gerçek zamanlı ölçümler toplama
* Eğilimleri belirlemesine yardımcı olmak için raporlar oluşturma
* Müşteri sorgularını iletme
* Uygulamanızdan e-posta bildirimleri

Daha fazla bilgi için bkz. [https://sendgrid.com](https://sendgrid.com).

## <a name="create-a-sendgrid-account"></a>SendGrid hesabı oluşturma

[!INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="reference-the-sendgrid-nodejs-module"></a>SendGrid Node.js modüle başvurma

Node.js için SendGrid modülü, aşağıdaki komut kullanılarak düğüm Paket Yöneticisi (NPM) üzerinden yüklenebilir:

```bash
npm install sendgrid
```

Yüklemeden sonra, aşağıdaki kodu kullanarak uygulamanızda modüle ihtiyacınız olabilir:

```javascript
var sendgrid = require('sendgrid')(sendgrid_username, sendgrid_password);
```

SendGrid modülü **SendGrid** ve **email** işlevlerini dışarı aktarır.
**SendGrid** Web API aracılığıyla e-posta göndermekten sorumludur, **e-** posta bir e-posta iletisi kapsüller.

## <a name="how-to-create-an-email"></a>Nasıl yapılır: e-posta oluşturma

SendGrid modülünü kullanarak bir e-posta iletisi oluşturmak, önce e-posta işlevini kullanarak bir e-posta iletisi oluşturulmasını ve sonra SendGrid işlevini kullanarak gönderilmesini içerir. Aşağıda, e-posta işlevini kullanarak yeni bir ileti oluşturma örneği verilmiştir:

```javascript
var email = new sendgrid.Email({
    to: 'john@contoso.com',
    from: 'anna@contoso.com',
    subject: 'test mail',
    text: 'This is a sample email message.'
});
```

Ayrıca, HTML özelliğini ayarlayarak, destekleyen istemciler için bir HTML iletisi de belirtebilirsiniz. Örnek:

```javascript
html: This is a sample <b>HTML<b> email message.
```

Metin ve HTML özelliklerinin her ikisi de ayarlandığında, HTML iletilerini destekleyebilen istemciler için metin içeriğine düzgün bir geri dönüş sağlanır.

E-posta işlevi tarafından desteklenen tüm özellikler hakkında daha fazla bilgi için bkz. [SendGrid-NodeJS][sendgrid-nodejs].

## <a name="how-to-send-an-email"></a>Nasıl yapılır: e-posta gönderme

E-posta işlevini kullanarak bir e-posta iletisi oluşturduktan sonra, SendGrid tarafından belirtilen Web API 'sini kullanarak gönderebilirsiniz. 

### <a name="web-api"></a>Web API

```javascript
sendgrid.send(email, function(err, json){
    if(err) { return console.error(err); }
    console.log(json);
});
```

> [!NOTE]
> Yukarıdaki örneklerde e-posta nesnesi ve geri arama işlevinde geçen örnekler gösterilmektedir, ancak e-posta özelliklerini doğrudan belirterek gönder işlevini doğrudan çağırabilirsiniz. Örnek:  
> 
> ```javascript
> sendgrid.send({
> to: 'john@contoso.com',
> from: 'anna@contoso.com',
> subject: 'test mail',
> text: 'This is a sample email message.'
> });
> ```
>

## <a name="how-to-add-an-attachment"></a>Nasıl yapılır: ek ekleme
**Dosyalar** özelliğindeki dosya adları ve yolları belirtilerek ekler bir iletiye eklenebilir. Aşağıdaki örnek, bir ekin gönderilmesini göstermektedir:

```javascript
sendgrid.send({
    to: 'john@contoso.com',
    from: 'anna@contoso.com',
    subject: 'test mail',
    text: 'This is a sample email message.',
    files: [
        {
            filename:     '',           // required only if file.content is used.
            contentType:  '',           // optional
            cid:          '',           // optional, used to specify cid for inline content
            path:         '',           //
            url:          '',           // == One of these three options is required
            content:      ('' | Buffer) //
        }
    ],
});
```

> [!NOTE]
> **Dosyalar** özelliği kullanılırken, dosyanın [FS. readFile](https://nodejs.org/docs/v0.6.7/api/fs.html#fs.readFile)aracılığıyla erişilebilir olması gerekir. İliştirmek istediğiniz dosya, blob kapsayıcısında olduğu gibi Azure depolama 'da barındırılıyorsa, **dosyalar** özelliği kullanılarak bir ek olarak gönderilmeden önce dosyayı yerel depolama alanına veya bir Azure sürücüsüne kopyalamanız gerekir.
> 
> 

## <a name="how-to-use-filters-to-enable-footers-and-tracking"></a>Nasıl yapılır: altbilgileri ve Izlemeyi etkinleştirmek için filtreleri kullanma

SendGrid, filtrelerin kullanımı aracılığıyla ek e-posta işlevselliği sağlar. Bunlar, tıklama izleme, Google Analytics, abonelik izleme gibi belirli işlevleri etkinleştirmek için bir e-posta iletisine eklenebilen ayarlardır. Filtrelerin tam listesi için bkz. [filtre ayarları][Filter Settings].

Filtreler, **Filtreler** özelliği kullanılarak bir iletiye uygulanabilir.
Her filtre filtreye özgü ayarları içeren bir karmayla belirtilir.
Aşağıdaki örnekler, alt bilgiyi gösterir ve izleme filtrelerini tıklama:

### <a name="footer"></a>Alt bilgi

```javascript
var email = new sendgrid.Email({
    to: 'john@contoso.com',
    from: 'anna@contoso.com',
    subject: 'test mail',
    text: 'This is a sample email message.'
});

email.setFilters({
    'footer': {
        'settings': {
            'enable': 1,
            'text/plain': 'This is a text footer.'
        }
    }
});

sendgrid.send(email);
```

### <a name="click-tracking"></a>Izleme ' ye tıklayın

```javascript
var email = new sendgrid.Email({
    to: 'john@contoso.com',
    from: 'anna@contoso.com',
    subject: 'test mail',
    text: 'This is a sample email message.'
});

email.setFilters({
    'clicktrack': {
        'settings': {
            'enable': 1
        }
    }
});

sendgrid.send(email);
```

## <a name="how-to-update-email-properties"></a>Nasıl yapılır: e-posta özelliklerini güncelleştirme

Bazı e-posta özelliklerinin, **SetProperty** kullanılarak veya **AddProperty** kullanılarak eklenmiş olarak üzerine yazılabilir. Örneğin, kullanarak daha fazla alıcı ekleyebilirsiniz

```javascript
email.addTo('jeff@contoso.com');
```

veya kullanarak bir filtre ayarlayın

```javascript
email.addFilter('footer', 'enable', 1);
email.addFilter('footer', 'text/html', '<strong>boo</strong>');
```

Daha fazla bilgi için bkz. [SendGrid-NodeJS][sendgrid-nodejs].

## <a name="how-to-use-additional-sendgrid-services"></a>Nasıl yapılır: ek SendGrid Hizmetleri kullanma

SendGrid, Azure uygulamanızdan ek SendGrid işlevlerinden yararlanmak için kullanabileceğiniz web tabanlı API 'Ler sunar. Tüm ayrıntılar için [SendGrid API belgelerine][SendGrid API documentation]bakın.

## <a name="next-steps"></a>Sonraki Adımlar

SendGrid e-posta hizmetinin temellerini öğrendiğinize göre, daha fazla bilgi edinmek için bu bağlantıları izleyin.

* SendGrid Node.js modül deposu: [SendGrid-NodeJS][sendgrid-nodejs]
* SendGrid API belgeleri: <https://sendgrid.com/docs>
* Azure müşterileri için SendGrid özel teklifi: [http://sendgrid.com/azure.html](https://sendgrid.com/windowsazure.html)

[special offer]: https://sendgrid.com/windowsazure.html
[sendgrid-nodejs]: https://github.com/sendgrid/sendgrid-nodejs
[Filter Settings]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html
[SendGrid API documentation]: https://sendgrid.com/docs
[bulut tabanlı e-posta hizmeti]: https://sendgrid.com/email-solutions
[işlem e-posta teslimi]: https://sendgrid.com/transactional-email
