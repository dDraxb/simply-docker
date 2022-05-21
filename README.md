## Mailhog with PHPmailer example

```shell
$ composer require phpmailer/phpmailer
```

```php
use PHPMailer\PHPMailer\PHPMailer;
use PHPMailer\PHPMailer\SMTP;

$mail = new PHPMailer();

$mail->SMTPDebug = SMTP::DEBUG_SERVER;                      
$mail->isSMTP();                                            
$mail->Host       = 'mailhog:1025';                     
$mail->SMTPAuth   = false;                                   
$mail->Port       = 1025;

$mail->setFrom('from@example.com', 'First Last');
$mail->addReplyTo('replyto@example.com', 'First Last');
$mail->addAddress('whoto@example.com', 'John Doe');
$mail->Subject = 'PHPMailer mail() test';
$mail->Body = 'This is a plain-text message body';

if (!$mail->send()) {
    echo 'Mailer Error: ' . $mail->ErrorInfo;
} else {
    echo 'Message sent!';
}
```