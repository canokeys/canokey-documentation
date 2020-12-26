+++
title = "Setup"
date =  2020-07-04T15:16:45+08:00
weight = 5
+++

This section describes what you need to do before you get started using your CanoKey.

## Linux

### udev

In order to allow non-root user use the key, you need to add a udev rule into /etc/udev/rules.d/69-canokeys.rules

```
SUBSYSTEM!="usb", GOTO="canokeys_rules_end"
ACTION!="add|change", GOTO="canokeys_rules_end"
ATTRS{idVendor}=="0483", ATTRS{idProduct}=="0007", ENV{ID_SMARTCARD_READER}="1"
ATTRS{idVendor}=="16d0", ATTRS{idProduct}=="0e80", ENV{ID_SMARTCARD_READER}="1"
ATTRS{idVendor}=="20a0", ATTRS{idProduct}=="42d4", ENV{ID_SMARTCARD_READER}="1"
LABEL="canokeys_rules_end"

# make this usb device accessible for users, used in WebUSB
# change the mode so unprivileged users can access it, insecure rule, though
SUBSYSTEMS=="usb", ATTR{idVendor}=="20a0", ATTR{idProduct}=="42d4", MODE:="0666"
# if the above works for WebUSB (web console), you may change into a more secure way
# choose one of the following rules
# note if you use "plugdev", make sure you have this group and the wanted user is in that group
#SUBSYSTEMS=="usb", ATTR{idVendor}=="20a0", ATTR{idProduct}=="42d4", GROUP="plugdev", MODE="0660"
#SUBSYSTEMS=="usb", ATTR{idVendor}=="20a0", ATTR{idProduct}=="42d4", TAG+="uaccess"
```
After add this file, run the follow commands to apply changes.

```
udevadm control --reload-rules && udevadm trigger
```

### ccid

You can install the newest version of `ccid` as CanoKey has already been included in the [upstream](https://salsa.debian.org/rousseau/CCID/-/commit/7a306c8da4872617dbc9a2cf6a8f7e827a6b3c38).

You may check your `/etc/libccid_Info.plist` wheter `canokey` is inside.

If not, or you do not bother install the newest version of `ccid`, you should make the following changes to `/etc/libccid_Info.plist`.

For each row, append one vid, pid and product name in each column, like the following `diff`

```diff
diff --git a/libccid_Info.plist b/libccid_Info.plist
index 05c0208..33a1779 100644
--- a/libccid_Info.plist
+++ b/libccid_Info.plist
@@ -576,6 +576,7 @@
                <string>0x08C3</string>
                <string>0x15E1</string>
                <string>0x062D</string>
+               <string>0x20A0</string>
        </array>
 
        <key>ifdProductID</key>
@@ -1054,6 +1055,7 @@
                <string>0x0402</string>
                <string>0x2007</string>
                <string>0x0001</string>
+               <string>0x42D4</string>
        </array>
 
        <key>ifdFriendlyName</key>
@@ -1532,6 +1534,7 @@
                <string>Precise Biometrics Precise 200 MC</string>
                <string>RSA RSA SecurID (R) Authenticator</string>
                <string>THRC Smart Card Reader</string>
+               <string>Canokey</string>
        </array>
 
        <key>Copyright</key>
``` 
