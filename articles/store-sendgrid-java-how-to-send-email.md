---
title: A SendGrid e-mail szolgáltatás (Java) használata | Microsoft Docs
description: Ismerje meg, hogyan küldhet e-mailt az Azure SendGrid e-mail szolgáltatásával. A Java nyelven írt példák.
services: ''
documentationcenter: java
author: thinkingserious
manager: sendgrid
editor: mollybos
ms.assetid: cc75c43e-ede9-492b-98c2-9147fcb92c21
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 10/30/2014
ms.author: erikre
ms.reviewer: elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork
ms.openlocfilehash: 8ae948e9c79cff4cd0c896b250743fd9dc521752
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/15/2019
ms.locfileid: "67876506"
---
# <a name="how-to-send-email-using-sendgrid-from-java"></a>E-mailek küldése a SendGrid használatával Java-ból
Ez az útmutató bemutatja, hogyan hajthat végre általános programozási feladatokat az Azure SendGrid e-mail szolgáltatásával. A mintákat Java nyelven írták. A tárgyalt forgatókönyvek közé tartozik például az **e-mailek**létrehozása, az **e-mail küldése**, a **mellékletek hozzáadása**, **a szűrők használata és a** **Tulajdonságok frissítése**. További információt a SendGrid és az e-mailek küldéséről a [következő lépések](#next-steps) című szakaszban talál.

## <a name="what-is-the-sendgrid-email-service"></a>Mi a SendGrid E-mail szolgáltatás?
A SendGrid egy [felhőalapú e-mail-szolgáltatás] , amely megbízható [tranzakciós e-mail]-kézbesítést, skálázhatóságot és valós idejű elemzéseket biztosít, valamint rugalmas API-kat, amelyek egyszerűvé teszik az egyéni integrációt. Az általános SendGrid-használati forgatókönyvek a következők:

* Nyugták automatikus küldése az ügyfeleknek
* A terjesztési listán szereplő ügyfelek havi e-szórólapok és különleges ajánlatok küldésének felügyelete
* Valós idejű mérőszámok gyűjtése a letiltott e-mailekhez és az ügyfelekre való válaszadáshoz
* Jelentések létrehozása a trendek azonosításához
* Ügyfelekkel kapcsolatos kérdések továbbítása
* Az alkalmazás e-mail-értesítései

További információ: <https://sendgrid.com>.

## <a name="create-a-sendgrid-account"></a>SendGrid-fiók létrehozása
[!INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="how-to-use-the-javaxmail-libraries"></a>Útmutató: A javax. mail kódtárak használata
Szerezze be a javax. mail kódtárakat, például <https://www.oracle.com/technetwork/java/javamail> a-ból, és importálja őket a kódra. Magas szinten a javax. mail függvénytár használatának folyamata az SMTP használatával történő e-mailek küldéséhez az alábbiakat kell tennie:

1. Itt adhatja meg az SMTP-értékeket, beleértve az SMTP-kiszolgálót, amely a SendGrid smtp.sendgrid.net.

```
        import java.util.Properties;
        import javax.mail.*;
        import javax.mail.internet.*;

        public class MyEmailer {
           private static final String SMTP_HOST_NAME = "smtp.sendgrid.net";
           private static final String SMTP_AUTH_USER = "your_sendgrid_username";
           private static final String SMTP_AUTH_PWD = "your_sendgrid_password";

           public static void main(String[] args) throws Exception{
               new MyEmailer().SendMail();
           }

           public void SendMail() throws Exception
           {
              Properties properties = new Properties();
                 properties.put("mail.transport.protocol", "smtp");
                 properties.put("mail.smtp.host", SMTP_HOST_NAME);
                 properties.put("mail.smtp.port", 587);
                 properties.put("mail.smtp.auth", "true");
                 // …
```

1. Terjessze ki a *javax. mail. hitelesítő* osztályt, és a *getPasswordAuthentication* metódus implementációjában adja meg a SendGrid felhasználónevét és jelszavát.  

       private class SMTPAuthenticator extends javax.mail.Authenticator {
       public PasswordAuthentication getPasswordAuthentication() {
          String username = SMTP_AUTH_USER;
          String password = SMTP_AUTH_PWD;
          return new PasswordAuthentication(username, password);
       }
2. Hozzon létre egy hitelesített e-mail-munkamenetet egy *javax. mail. Session* objektumon keresztül.  

       Authenticator auth = new SMTPAuthenticator();
       Session mailSession = Session.getDefaultInstance(properties, auth);
3. Hozza létre az üzenetet, és rendelje hozzá a, **a, a** **Tárgy** és a tartalom értékét. Ez a [következő témakörben látható: Hozzon létre](#how-to-create-an-email) egy e-mail-szakaszt.
4. Küldje el az üzenetet egy *javax. mail. Transport* objektumon keresztül. Ez a [How to: E-mail küldése] [#how – Send-an-email] szakasz.

## <a name="how-to-create-an-email"></a>Útmutató: E-mail létrehozása
Az alábbiakban bemutatjuk, hogyan lehet megadni az e-mailek értékeit.

    MimeMessage message = new MimeMessage(mailSession);
    Multipart multipart = new MimeMultipart("alternative");
    BodyPart part1 = new MimeBodyPart();
    part1.setText("Hello, Your Contoso order has shipped. Thank you, John");
    BodyPart part2 = new MimeBodyPart();
    part2.setContent(
        "<p>Hello,</p>
        <p>Your Contoso order has <b>shipped</b>.</p>
        <p>Thank you,<br>John</br></p>",
        "text/html");
    multipart.addBodyPart(part1);
    multipart.addBodyPart(part2);
    message.setFrom(new InternetAddress("john@contoso.com"));
    message.addRecipient(Message.RecipientType.TO,
       new InternetAddress("someone@example.com"));
    message.setSubject("Your recent order");
    message.setContent(multipart);

## <a name="how-to-send-an-email"></a>Útmutató: E-mail küldése
Az alábbi ábrán egy e-mail küldését láthatja.

    Transport transport = mailSession.getTransport();
    // Connect the transport object.
    transport.connect();
    // Send the message.
    transport.sendMessage(message, message.getAllRecipients());
    // Close the connection.
    transport.close();

## <a name="how-to-add-an-attachment"></a>Útmutató: Melléklet hozzáadása
A következő kód bemutatja, hogyan adhat hozzá mellékletet.

    // Local file name and path.
    String attachmentName = "myfile.zip";
    String attachmentPath = "c:\\myfiles\\";
    MimeBodyPart attachmentPart = new MimeBodyPart();
    // Specify the local file to attach.
    DataSource source = new FileDataSource(attachmentPath + attachmentName);
    attachmentPart.setDataHandler(new DataHandler(source));
    // This example uses the local file name as the attachment name.
    // They could be different if you prefer.
    attachmentPart.setFileName(attachmentName);
    multipart.addBodyPart(attachmentPart);

## <a name="how-to-use-filters-to-enable-footers-tracking-and-analytics"></a>Útmutató: Szűrők használata a láblécek, a követés és az elemzés engedélyezéséhez
A SendGrid további e-mail-funkciókat biztosít a *szűrők*használatával. Ezek olyan beállítások, amelyek hozzáadhatók egy e-mail-üzenethez, amely lehetővé teszi bizonyos funkciók használatát, például a követést, a Google Analyticset, az előfizetés nyomon követését stb. A szűrők teljes listáját a [szűrési beállítások][Filter Settings]című témakörben tekintheti meg.

* Az alábbiakban bemutatjuk, hogyan szúrhat be olyan lábléc-szűrőt, amely az elküldött e-mailek alján megjelenő HTML-szöveget eredményez.

      message.addHeader("X-SMTPAPI",
          "{\"filters\":
          {\"footer\":
          {\"settings\":
          {\"enable\":1,\"text/html\":
          \"<html><b>Thank you</b> for your business.</html>\"}}}}");
* Egy másik példa egy szűrőre a követés gombra kattintva. Tegyük fel, hogy az e-mail-szövege hiperhivatkozást tartalmaz, például a következőt, és nyomon szeretné követni a kattintások arányát:

      messagePart.setContent(
          "Hello,
          <p>This is the body of the message. Visit
          <a href='http://www.contoso.com'>http://www.contoso.com</a>.</p>
          Thank you.",
          "text/html");
* A kattintás követésének engedélyezéséhez használja a következő kódot:

      message.addHeader("X-SMTPAPI",
          "{\"filters\":
          {\"clicktrack\":
          {\"settings\":
          {\"enable\":1}}}}");

## <a name="how-to-update-email-properties"></a>Útmutató: E-mail-tulajdonságok frissítése
Néhány e-mail-tulajdonság felülírható a **set tulajdonsággal** , vagy hozzáfűzéssel a **Hozzáadás tulajdonsággal**.

Például **ReplyTo** -címek megadásához használja a következőt:

    InternetAddress addresses[] =
        { new InternetAddress("john@contoso.com"),
          new InternetAddress("wendy@contoso.com") };

    message.setReplyTo(addresses);

**CC** -címzett hozzáadásához használja a következőt:

    message.addRecipient(Message.RecipientType.CC, new
    InternetAddress("john@contoso.com"));

## <a name="how-to-use-additional-sendgrid-services"></a>Útmutató: További SendGrid-szolgáltatások használata
A SendGrid olyan webes API-kat kínál, amelyek segítségével további SendGrid funkciókat alkalmazhat az Azure-alkalmazásból. További részletekért tekintse meg a [SENDGRID API dokumentációját][SendGrid API documentation].

## <a name="next-steps"></a>További lépések
Most, hogy megismerte a SendGrid E-mail szolgáltatás alapjait, kövesse az alábbi hivatkozásokat további információért.

* Minta, amely bemutatja a SendGrid használatát az Azure-beli üzembe helyezésben: [E-mailek küldése a SendGrid a Java használatával Azure-beli üzemelő példányban](store-sendgrid-java-how-to-send-email-example.md)
* SendGrid Java SDK:<https://sendgrid.com/docs/Code_Examples/java.html>
* SendGrid API-dokumentáció:<https://sendgrid.com/docs/API_Reference/index.html>
* SendGrid Speciális ajánlat az Azure-ügyfelek számára:<https://sendgrid.com/windowsazure.html>

[https://sendgrid.com]: https://sendgrid.com
[https://sendgrid.com/pricing.html]: https://sendgrid.com/pricing.html
[http://www.sendgrid.com/azure.html]: https://www.sendgrid.com/windowsazure.html
[https://sendgrid.com/features]: https://sendgrid.com/features
[https://www.oracle.com/technetwork/java/javamail]: https://www.oracle.com/technetwork/java/javamail/index.html
[Filter Settings]: https://sendgrid.com/docs/API_Reference/Web_API/filter_settings.html
[SendGrid API documentation]: https://sendgrid.com/docs/API_Reference/index.html
[https://sendgrid.com/azure.html]: https://sendgrid.com/windowsazure.html
[felhőalapú e-mail-szolgáltatás]: https://sendgrid.com/email-solutions
[tranzakciós e-mail]: https://sendgrid.com/transactional-email
