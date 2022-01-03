+++
title = "OATH"
date =  2022-01-03T18:59:12+08:00
weight = 35
+++

OATH is [an organization](https://openauthentication.org/) who provides open authentication standards: Time-based One Time Password (TOTP) and HMAC-based One Time Password (HOTP).

HOTP and TOTP are both implemented in CanoKey Pigeon and Canokey epoxy editions.

## Firmware version 1.5 and newer (For example, CanoKey Pigeon).

You should use `ykman` command to configure OATH and read OATH token.
### Setting up

If your authentication provider provides you with a URI `otpauth://totp/username@EXAMPLE.COM:12345678-90ab-cdef-1234-567890abcdef?digits=6&secret=SOMESECRET&period=30&algorithm=SHA1&issuer=username%40EXAMPLE.COM`, you should configure it by
```
$ ykman -r "Canokeys" oath accounts uri "otpauth://totp/username@EXAMPLE.COM:12345678-90ab-cdef-1234-567890abcdef?digits=6&secret=SOMESECRET&period=30&algorithm=SHA1&issuer=username%40EXAMPLE.COM"
```

Or if you are offered the fields separately, including the base32 encoded secret (in this example, it's `SOMESECRET`), the algorithm (in this example, it's SHA1), the length of digits, you can setup by

```
ykman -r "Canokeys" oath accounts add -o TOTP -d 6 -a SHA1 -i 'username@EXAMPLE.COM' -P 30 USERNAME SOMESECRET
```

### Read OATH token

You can use [Yubico Authenticator](https://www.yubico.com/products/yubico-authenticator/) to read the OTP.
* Open Yubico Authenticator, click the button on the top left corner to fire up the side menu.
* Click 'Settings' and then click 'Custom reader'
* Select 'Enable custom reader' and fill in 'Canokey' in the 'Custom reader filter'
* Click 'Save'
* Click the '<' on the top to go back to Settings.
* Unplug and plug in your CanoKey
* Click the top left button to switch to 'Authenticator'

Then you'll see all your OATH tokens listed. 

## Firmware version older than 1.5 (for example, CanoKey epoxy editions)

You should use [CanoKey Web Console](https://console.canokeys.org) on a Chromium-based web browser to configure OATH.

### Setup

* Go to [OATH Applet](https://console.canokeys.org/oath) of the web console and connect your CanoKey.
* Click 'CONNECT' on the top right corner
* Select your CanoKey from the prompt dialog
* Click the '+' sign on the down left of the box, then the 'Add credential to OATH Applet' section will show up on the web page.
* Fill in the details, **or** copy and click 'IMPORT OTPAUTH FROM CLIPBOARD'.
* Click ADD
* If there is no error, there will be a green box with 'Add OATH credential success' shown in the bottom left of your web page.

### Read OATH TOTP token

* Go to [OATH Applet](https://console.canokeys.org/oath) of the web console and connect your CanoKey.
* Click 'CONNECT' on the top right corner
* Select your CanoKey from the prompt dialog
* Click the 'Calculate TOTP' button on the line of TOTP you want to calculate.
* If there is no error, there will be a green box with 'TOTP code is xxxxxx' shown in the bottom left of your web page.


## Optionally: Enable touch to input for HOTP

If you are using HOTP and you want to make your CanoKey input your HOTP token every time you touch the key, you should 
* Go to [Admin Applet](https://console.canokeys.org/admin) of the web console and connect your CanoKey.
* If you haven't connected your CanoKey to the web console, click 'CONNECT' on the top right corner and select your CanoKey from the prompt dialog.
* Click AUTHENTICATE and input your admin applet password to authenticate as admin user
* Enable 'HOTP on touch' in the Config section, then you'll see a green box with 'HOTP on touch is on' shown in the bottom left of the web page.
* Go to [OATH Applet](https://console.canokeys.org/oath) of the web console
* Click the star icon '٭' on the line of HOTP you want to use. This will make the HOTP token default.
* Click DISCONNECT on the top right corner
* Unplug and plug in your CanoKey again.
Now you can press the touch area to input your default HOTP token.

