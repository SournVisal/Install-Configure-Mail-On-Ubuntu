# Install-Configure-Mail-On-Ubuntu
Complete guide for install prerequisites and configure Sendmail  service on Ubuntu.

Make sure you are a root user.
```sh
$ sudo su
```
1.Install prerequisites
----------
```sh
$ apt-get install sendmail mailutils sendmail-bin sensible-mda procmail
```

2.SMTP Authentication
----------
In the next step, we need to create a file to store the credentials Gmail to access to the smtp server.
Create the /etc/mail/auth directory:
```sh
$ mkdir -m 700 -p /etc/mail/auth
```

3.Create auth-info file to store the Gmail credentials:
Open the auth-info file in your text-editor and copy & paste the follow lines. Replace <USERNAME> and <PASSWORD> placeholders with your credentials:
  ```sh
$ touch /etc/mail/auth/auth-info
```
AuthInfo:smtp.gmail.com "U:root" "I:<USERNAME>@gmail.com" "P:<PASSWORD>"

4.Save the file and make the database-map:
  ```sh
$ cd /etc/mail
$ makemap hash /etc/mail/auth/auth-info < /etc/mail/auth/auth-info
```
At the end of execution will be created auth-info.db file.

For strong security, set the read-write permission only for root user:
```sh
$ chmod 0600 /etc/mail/auth/*
```

5.Configure your sendmail.mc
Open /etc/mail/sendmail.mc file in your text-editor and copy the follow lines and paste before the first MAILER line:
```hs
define(`SMART_HOST',`smtp.gmail.com')dnl
define(`RELAY_MAILER_ARGS', `TCP $h 587')dnl
define(`ESMTP_MAILER_ARGS', `TCP $h 587')dnl
define(`confAUTH_MECHANISMS', `EXTERNAL GSSAPI DIGEST-MD5 CRAM-MD5 LOGIN PLAIN')dnl
FEATURE(`authinfo',`hash /etc/mail/auth/auth-info')dnl
TRUST_AUTH_MECH(`EXTERNAL DIGEST-MD5 CRAM-MD5 LOGIN PLAIN')
```
6.Save the file and make the sendmail.cf:
```sh
$ cd /etc/mail
$ m4 sendmail.mc > sendmail.cf
```
Send the test email

7.Restart sendmail service:
```sh
$ sh /etc/rc.d/rc.sendmail restart
```

