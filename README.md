# 🔓📱 Android-PIN-Bruteforce

Unlock an Android phone (or device) by bruteforcing the lockscreen PIN.

Turn your Kali Nethunter phone into a bruteforce PIN cracker for Android devices! 

## 📱 How it works

It uses a USB OTG cable to connect the locked phone to the Nethunter device. It emulates a keyboard, automatically tries PINs, and waits after trying too many wrong guesses.

![How to Connect Phones](https://user-images.githubusercontent.com/101783/91640968-b7d46280-ea64-11ea-8340-94e3bacb706e.png)

[Nethunter phone] <--> [USB cable] <--> [USB OTG adaptor] <--> [Locked Android phone]

The USB HID Gadget driver provides emulation of USB Human Interface Devices (HID). This enables an Android Nethunter device to emulate keyboard input to the locked phone. It's just like plugging a keyboard into the locked phone and pressing keys.

⏱ This takes just over 16.6 hours with a Samsung S5 to try all possible 4 digit PINs, but with the optimised PIN list it should take you much less time.

### You will need

- A locked Android phone
- A Nethunter phone (or any rooted Android with HID kernel support)
- USB OTG (On The Go) cable/adapter (USB male Micro-B to female USB A), and a standard charging cable (USB male Micro-B to male A).
- That's all!

## 🌟 Benefits

- Turn your NetHunter phone into an Android PIN cracking machine
- Unlike other methods, you do not need ADB or USB debugging enabled on the locked phone
- The locked Android phone does not need to be rooted
- You don't need to buy special hardware, e.g. Rubber Ducky, Teensy, Cellebrite, XPIN Clip, etc.
- You can easily modify the backoff time to crack other types of devices
- It works!

## ⭐ Features

- Crack PINs of any length from 1 to 10 digits
- Use config files to support different phones
- Optimised PIN lists for 3,4,5, and 6 digit PINs
- Bypasses phone pop-ups including the Low Power warning
- Detects when the phone is unplugged or powered off, and waits while retrying every 5 seconds
- Configurable delays of N seconds after every X PIN attempts
- Log file

## Installation

TBC

## Executing the script

If you installed the script to /sdcard/, you can execute it with the following command.

```bash ./android-pin-bruteforce``` 

Note that Android mounts /sdcard with the noexec flag. You can verify this with ```mount```.


## Usage

```

Android-PIN-Bruteforce (0.1) is used to unlock an Android phone (or device) by bruteforcing the lockscreen PIN.
  Find more information at: https://github.com/urbanadventurer/Android-PIN-Bruteforce

Commands:
  crack                Begin cracking PINs
  resume               Resume from a chosen PIN
  rewind               Crack PINs in reverse from a chosen PIN
  diag                 Display diagnostic information
  version              Display version information and exit

Options:
  -f, --from PIN       Resume from this PIN
  -m, --mask REGEX     Use a mask for known digits in the PIN
  -t, --type TYPE      Select PIN or PATTERN cracking
  -l, --length NUM     Crack PINs of NUM length
  -c, --config FILE    Specify configuration file to load
  -p, --pinlist FILE   Specify a custom PIN list
  -d, --dry-run        Dry run for testing. Doesn't send any keys.
  -v, --verbose        Output verbose logs

Usage:
  android-pin-bruteforce <command> [options]
```


## Supported Android Phones/Devices

This has been tested with Samsung phones including the S5, S7, S7 Edge, S8, J7, A5 and the LG Nexus 5.

It can unlock Android versions 6.0.1 through to 10.0, however it depends on device manufacturer's lockscreen.

Check the Phone Database for more details
https://github.com/urbanadventurer/Android-PIN-Bruteforce/wiki/Phone-Database

## 🎳 PIN Lists

Optimised PIN lists are used by default unless the user selects a custom PIN list.  

### Cracking PINs of different lengths

Use the `--length` commandline option.

Use this command to crack a 3 digit PIN, 
`./android-pin-bruteforce crack --length 3`

Use this command to crack a 6 digit PIN
`./android-pin-bruteforce crack --length 6`

### Where did the optimised PIN lists come from?

The optimised PIN lists were generated by extracting numeric passwords from database leaks then sorting by frequency. All PINs that did not appear in the password leaks were appended to the list. 

The optimised PIN lists were generated from *Ga$$Pacc DB Leak* (21GB decompressed, 688M Accounts, 243 Databases, 138920 numeric passwords).

#### The 4 digit PIN list

The reason that the 4 digit PIN list is used from a different source is because it gives better results than the generated list from *Ga$$Pacc DB Leak*.

`optimised-pin-length-4.txt` is an optimised list of all possible 4 digit PINs, sorted by order of likelihood.
It can be found with the filename `pinlist.txt` at https://github.com/mandatoryprogrammer/droidbrute

This list is used with permission from Justin Engler & Paul Vines from Senior Security Engineer, iSEC Partners,
and was used in their Defcon talk, [Electromechanical PIN Cracking with Robotic Reconfigurable Button Basher (and C3BO)](https://www.defcon.org/html/defcon-21/dc-21-speakers.html#Engler)

### Cracking with Masks

Masks use regular expressions with the standard grep extended format.

`./android-pin-bruteforce crack --mask "...[45]" --dry-run`

- To try all years from 1900 to 1999, use a mask of `19..`
- To try PINs that have a 1 in the first digit, and a 1 in the last digit, use a mask of `1..1`
- To try PINs that end in 4 or 5, use `...[45]`

## 📱 Configuration for different phones

Device manufacturers create their own lock screens that are different to the default or stock Android. 
To find out what keys your phone needs, plug a keyboard into the phone and try out different combinations.

Load a different configuration file, with the `--config FILE` commandline parameter.

Example:
`./android-pin-bruteforce --config ./config.samsung crack`

You can also edit the `config` file by customising the timing and keys sent.

The following configuration variables can be used to support a different phone's lockscreen.

```
# Timing
## DELAY_BETWEEN_KEYS is the period of time in seconds to wait after each key is sent
DELAY_BETWEEN_KEYS=0.25

## COOLDOWN_TIME is the period of time in seconds to wait after N attempts is reached, by default after 5 attempts it pauses for 30 seconds
COOLDOWN_TIME=30

## COOLDOWN_AFTER_N_ATTEMPTS is how many attemps to make before waiting for the COOLDOWN_TIME 
COOLDOWN_AFTER_N_ATTEMPTS=5

## PROGRESSIVE_COOLDOWN can be set to 1 for enabled or 0 disabled. When enabled, after 10 attempts it changes the COOLDOWN_AFTER_N_ATTEMPTS to 1.
PROGRESSIVE_COOLDOWN=0

## The PROGRESSIVE_COOLDOWN_ARRAY variables act as multi-dimensional array to customise the progressive cooldown
## PROGRESSIVE_ARRAY_ATTEMPT_COUNT__________ is the attempt number
## PROGRESSIVE_ARRAY_ATTEMPTS_UNTIL_COOLDOWN is how many attempts to try before cooling down
## PROGRESSIVE_ARRAY_COOLDOWN_IN_SECONDS____ is the cooldown in seconds

PROGRESSIVE_ARRAY_ATTEMPT_COUNT__________=(1  11 41)
PROGRESSIVE_ARRAY_ATTEMPTS_UNTIL_COOLDOWN=(5   1  1)
PROGRESSIVE_ARRAY_COOLDOWN_IN_SECONDS____=(30 30 60)

## COOLDOWN_TIME_AFTER_EACH_PIN is the period of time in seconds to wait after every PIN attempt in seconds. It is disabled if set to 0.
COOLDOWN_TIME_AFTER_EACH_PIN=0

## KEYS_BEFORE_EACH_PIN configures the keys that are sent to prompt the lock screen to appear. This is sent before each PIN.
## By default it sends "escape enter", but some phones will respond to other keys.

# Examples:
# KEYS_BEFORE_EACH_PIN="ctrl_escape enter"
# KEYS_BEFORE_EACH_PIN="escape space"
KEYS_BEFORE_EACH_PIN="escape enter"

## KEYS_STAY_AWAKE_DURING_COOLDOWN the keys that are sent during the cooldown period to keep the phone awake
KEYS_STAY_AWAKE_DURING_COOLDOWN="enter"

## SEND_KEYS_STAY_AWAKE_DURING_COOLDOWN_EVERY_N_SECONDS how often the keys are sent, in seconds
SEND_KEYS_STAY_AWAKE_DURING_COOLDOWN_EVERY_N_SECONDS=5

## DELAY_BEFORE_STARTING is the period of time in seconds to wait before the bruteforce begins
DELAY_BEFORE_STARTING=2
## KEYS_BEFORE_STARTING configures the keys that are sent before the bruteforce begins
KEYS_BEFORE_STARTING="enter"
```

### Popups

We send keys during the cooldown period. This is to keep the lockscreen app active and to dismiss any popups about the number of incorrect PIN attempts or a low battery warning.

## Test sending keys from the NetHunter phone

### Test sending keys from the terminal

Use ssh from your laptop to the NetHunter phone, and use this command to test sending keys:

In this example, the enter key is sent.

`echo "enter" | /system/xbin/hid-keyboard /dev/hidg0 keyboard`

In this example, ctrl-escape is sent. 

`echo "left-ctrl escape" | /system/xbin/hid-keyboard /dev/hidg0 keyboard`

Note: Sending combinations of keys in `config` file variables is different. Currently only `ctrl_escape` is supported.

In this example, keys a, b, c are sent.

`echo a b c | /system/xbin/hid-keyboard /dev/hidg0 keyboard`

### Test sending keys from an app

This Android app is a virtual USB Keyboard that you can use to test sending keys.

https://store.nethunter.com/en/packages/remote.hid.keyboard.client/

### How to send special keys

Use this list for the following variables:

- KEYS_BEFORE_EACH_PIN
- KEYS_STAY_AWAKE_DURING_COOLDOWN
- KEYS_BEFORE_STARTING

To send special keys use the following labels. 
This list can be found in the hid_gadget_test source code.

| Key label     | Key label     |
| ------------- | ------------- |
| left-ctrl     | f6            |
| right-ctrl    | f7            |
| left-shift    | f8            |
| right-shift   | f9            |
| left-alt      | f10           |
| right-alt     | f11           |
| left-meta     | f12           |
| right-meta    | insert        |
| return        | home          |
| esc           | pageup        |
| bckspc        | del           |
| tab           | end           |
| spacebar      | pagedown      |
| caps-lock     | right         |
| f1            | left          |
| f2            | down          |
| f3            | kp-enter      |
| f4            | up            |
| f5            | num-lock      |

To send more than one key at the same time, use the following list:

- ctrl_escape (This sends left-ctrl and escape)

If you need more key combinations please open a new issue in the GitHub issues list.

### Customising the Progressive Cooldown

The following section of the `config` file controls the progressive cooldown.


```
## PROGRESSIVE_COOLDOWN can be set to 1 for enabled or 0 disabled. When enabled, after 10 attempts it changes the COOLDOWN_AFTER_N_ATTEMPTS to 1.
PROGRESSIVE_COOLDOWN=1

## The PROGRESSIVE_COOLDOWN_ARRAY variables act as multi-dimensional array to customise the progressive cooldown
## PROGRESSIVE_ARRAY_ATTEMPT_COUNT__________ is the attempt number
## PROGRESSIVE_ARRAY_ATTEMPTS_UNTIL_COOLDOWN is how many attempts to try before cooling down
## PROGRESSIVE_ARRAY_COOLDOWN_IN_SECONDS____ is the cooldown in seconds

PROGRESSIVE_ARRAY_ATTEMPT_COUNT__________=(1  11 41)
PROGRESSIVE_ARRAY_ATTEMPTS_UNTIL_COOLDOWN=(5   1  1)
PROGRESSIVE_ARRAY_COOLDOWN_IN_SECONDS____=(30 30 60)

```

The array is the same as this table.

| attempt number   | attempts until cooldown  | cooldown   |
| ---------------- | ------------------------ | ---------- |
| 1                | 5                        | 30         |
| 11               | 1                        | 30         |
| 41               | 1                        | 60         |


### Why can't you use a laptop?

This works from an Android phone because the USB ports are not bidirectional, unlike the ports on a laptop.

### How Android emulates a keyboard

Keys are sent using `/system/xbin/hid-keyboard`. To test this and send the key 1 you can use `echo 1 | /system/xbin/hid-keyboard dev/hidg0 keyboard`

In Kali Nethunter, `/system/xbin/hid-keyboard` is a compiled copy of `hid_gadget_test.c`. This is a small program for testing the HID gadget driver that is included in the Linux Kernel. The source code for this file can be found at https://www.kernel.org/doc/html/latest/usb/gadget_hid.html and https://github.com/aagallag/hid_gadget_test.

## 🔧 Troubleshooting

### If it is not bruteforcing PINs

#### Check the orientation of the cables

The Nethunter phone should have a regular USB cable attached, while the locked phone should have an OTG adaptor attached.

The OTG cable should be connected to the locked Android phone. The regular USB cable should be connected to the Nethunter phone.

Refer to the graphic on how to connect the phones.

#### Check it is emulating a keyboard

You can verify that the NetHunter phone is succesfully emulating a keyboard by connecting it to a computer using a regular charging/data USB cable. Open a text editor like Notepad while it is cracking and you should see it entering PIN numbers into the text editor.

Note that you will not need an OTG cable for this. 

#### Try restarting the phones

Try powering off the phones and even taking out the batteries if that is possible.

#### Try new cables

Try using new cables/adaptors as you may have a faulty cable/adaptor.

### If it doesn't unlock the phone with a correct PIN

You might be sending keys too fast for the phone to process. Increase the DELAY_BETWEEN_KEYS variable in the config file.
💡 If you don't see 4 dots come up on the phone's screen then maybe it is not receiving 4 keys.

### 🔋 Managing Power Consumption

If your phone runs out of power too soon, follow these steps:

- Make sure both phones are fully charged to 100% before you begin
- Use a USB OTG cable with a Y splitter for an external power supply, to allow charging while cracking
- Reduce the screen brightness on both the victim phone and NetHunter phone
- Increase the SEND_KEYS_STAY_AWAKE_DURING_COOLDOWN_EVERY_N_SECONDS configuration option. This will cause the locked phone to wakes up less often during cooldown, and the screen will use less power.
- Take breaks to charge your devices. Pause the script with CTRL-Z and resume with the `fg` shell command.

### Check the Diagnostics Report

Use the command `diag` display diagnostic information.

```bash ./android-pin-bruteforce diag```

If you receive this message when the USB cable is plugged in then try taking the battery out of the locked Android phone and power cycling it.

```[FAIL] HID USB device not ready. Return code from /system/xbin/hid-keyboard was 5.```

### How the usb-devices command works

The diagnostics command uses the `usb-devices` script but it is only necessary as part of determining whether the USB cables are incorrectly connected. This can be downloaded from
https://github.com/gregkh/usbutils/blob/master/usb-devices

### Use verbose output

Use the `--verbose` option to check the configuration is as expected. This is especially useful when you are modifying the configuration.

### Use the dry-run

Use the `--dry-run` option to check how it operates without sending any keys to a device. This is especially useful when you are modifying the configuration or during development.

Dry run will:

- Not send any keys
- Will continue instead of aborting if the `KEYBOARD_DEVICE` or `HID_KEYBOARD` is missing.

### HID USB Mode

Try this command in a shell on the NetHunter phone:
```/system/bin/setprop sys.usb.config hid```

## 💣 Known Issues

- This cannot detect when the correct PIN is guessed and the phone unlocks.
- Your phones may run out of 🔋 battery before the correct PIN is found.
- Don't trust phone configuration files from unknown sources without reviewing them first. The configuration files are shell scripts and could include malicious commands.

## 🚀 Roadmap

- [DONE] Works
- [DONE] Detects USB HID failures
- [DONE] Improve Usage and commandline options/config files
- [DONE] Add bruteforce for n digit PINs
- [DONE] Mask for known digits
- [DONE] Crack PIN list in reverse (to find which recent PIN unlocked the device)
- [DONE] Implement configurable lockscreen prompt
- [DONE] Implement cooldown change after 10 attempts
- [WORKING] Find/test more devices to bruteforce
- Add progress bar
- Add ETA
- ASCII art
- Nicer GUI for NetHunter
- Implement for iPhone
- Detect when a phone is unlocked (Use Nethunter camera as a sensor?)
- Crack Android Patterns (try common patterns first)

## 🙋 Contributing

Pull requests are welcome. For major changes, please open an issue first to discuss what you would like to change.

Please make sure to update tests as appropriate.

## 😎 Authors and acknowledgment

Developed by Andrew Horton (urbanadventurer).

👏 The following people have been very helpful:

- Vlad Filatov: Testing many phones for the Wiki Phone Database 


### Motivation

My original motivation to develop this was to unlock a Samsung S5 Android phone. It had belonged to someone who had passed away, and their family needed access to the data on it. As I didn't have a USB Rubber Ducky or any other hardware handy, I tried using a variety of methods, and eventually realised I had to develop something new.

### Credit

The optimised PIN list is from Justin Engler (@justinengler) & Paul Vines from Senior Security Engineer, iSEC Partners
and was used in their Defcon talk, [Electromechanical PIN Cracking with Robotic Reconfigurable Button Basher (and C3BO).](https://www.defcon.org/html/defcon-21/dc-21-speakers.html#Engler).

### Graphics

Designed by Andrew Horton and gratefully using these free vector packs:

- [USB Ports Isometric Free Vector by VisionHeldup](https://www.vecteezy.com/vector-art/159576-usb-ports-isometric-free-vector)
- [HDMI and USB Vector Set by Mary Winkler](https://www.vecteezy.com/vector-art/107006-hdmi-and-usb-vector-set)
- [Isometric Data Security Illustration by Rizal.Medanguide](https://www.vecteezy.com/vector-art/661831-isometric-data-security-illustration)
- Kali NetHunter Logo

## 🗿 Comparison with other projects and methods to unlock a locked Android phone

### What makes this project unique? 

I've been asked what makes this project unique when there are other open-source Android PIN cracking projects.

Android-PIN-Bruteforce is unique because it cracks the PIN on Android phones from a NetHunter phone and it doesn't need the locked phone to be pre-hacked.

It works:
- Without having to buy special hardware, such as a Rubber Ducky, Celebrite, or XKEY.
- Without ADB or root access (the phone doesn't have to be pre-hacked).

| Project                                               | ADB/USB Debugging  | Requires root | Requires $ hardware | Commercial  |
------------------------------------------------------- | ------------------ | ------------- | ------------------- | ----------- | 
| ⭐ Android-PIN-Bruteforce                             | No                 | No            | Nethunter phone     | No          | 
| github.com/PentesterES/AndroidPINCrack                | Yes                | Yes           | No                  | No          |
| github.com/ByteRockstar1996/Cracking-Android-Pin-Lock | Yes                | Yes           | No                  | No          |
| github.com/sch3m4/androidpatternlock                  | Yes                | Yes           | No                  | No          |
| github.com/georgenicolaou/androidlockcracker          | Yes                | Yes           | No                  | No          |
| github.com/MGF15/P-Decode                             | Yes                | Yes           | No                  | No          |
| github.com/BitesFor/ABL                               | Yes                | Yes           | No                  | No          |
| github.com/wuseman/WBRUTER                            | Yes                | No            | No                  | No          |
| github.com/Gh005t/Android-BruteForce                  | Yes                | No            | No                  | No          |
| github.com/mandatoryprogrammer/droidbrute             | No                 | No            | Rubber Ducky $      | No          |
| github.com/hak5darren/USB-Rubber-Ducky                | No                 | No            | Rubber Ducky $      | Yes         |
| github.com/bbrother/stm32f4androidbruteforce          | No                 | No            | STM32F4 dev board $ | No          |
| hdb-team.com/product/hdbox/                           | No                 | No            | HDBOX  $$           | Yes         |
| xpinclip.com                                          | No                 | No            | XPINClip  $$        | Yes         |
| cellebrite.com/en/ufed/                               | No                 | No            | Cellebrite UFED $$$ | Yes         |

Some of these projects/products are really awesome but they achieve a different goal to Android-PIN-Bruteforce.

If a project requires a gestures.key or password.key, I've listed it as requiring root.
If a project requires a custom bootloader, I've listed that as requiring both ADB and root.
If you would like your project listed in this table then please open a new issue.
There are links to each of these projects in the 📚 Related Projects & Futher Reading section.

### 😭 Regular phone users

- Try the top 20 PINs from the [DataGenetics PIN analysis](https://datagenetics.com/blog/september32012/index.html) that apparently unlocks 26.83% of phones.
- Use an SMS lock-screen bypass app (requires app install before phone is locked)
- Use Samsung Find My Mobile (requires you set it up before phone is locked)
- Crash the Lock Screen UI (Android 5.0 and 5.1)
- Use the Google Forgot pattern, Forgot PIN, or Forgot password (Android 4.4 KitKat and earlier)
- Factory Reset (you lose all your data 😭)

### 🤖 Users who have already replaced their Android ROM

If the phone has already been rooted, has USB debugging enabled, or has adb enabled.

- Flash the `Pattern Password Disable` ZIP using a custom recovery (Requires TWRP, CMW, Xrec, etc.)
- Delete `/data/system/gesture.key` or `password.key` (requires root and adb on locked device)
- Crack `/data/system/gesture.key` and `password.key` (requires root and adb on locked device)
- Update sqlite3 database `settings.db` (requires root and adb on locked device)

### 🔬 Forensic Investigators

These methods can be expensive and are usually only used by specialised phone forensic investigators.

In order of difficulty and expense:

- Taking advantage of USB debugging being enabled (Oxygen Forensic Suite)
- Bruteforce with keyboard emulation (⭐ Android-PIN-Bruteforce, RubberDucky attack, XPIN Clip, HBbox)
- JTAG (Interface with TAPs (Test Access Ports) on the device board)
- In-System Programming (ISP) (Involves directly connecting to pins on flash memory chips on the device board)
- Chip Off (Desolder and remove flash memory chips from the device)
- Clock Glitching / Voltage Fault Injection (Hardware CPU timing attacks to bypass PIN restrictions)
- Bootloader exploits (Zero-day exploits that attack the bootloader. GrayKey from Grayshift and Cellebrite)

JTAG, ISP, and Chip Off techniques are less useful now because most devices are encrypted.
I don't know of any practical attacks on phone PINs that use clock glitching, if you know of a product that uses this technique please let me know so I can include it.

### 🕵 Security Professionals and Technical Phone Users

Use the USB HID Keyboard Bruteforce with some dedicated hardware.

- A RubberDucky and Darren Kitchen's Hak5 brute-force script
- Write a script for a USB Teensy
- Buy expensive forensic hardware
- Or you can use Android-PIN-Bruteforce with your NetHunter phone!

Attempts to use an otherwise awesome project Duck Hunter, to emulate a RubberDucky payload for Android PIN cracking did not work. It crashed the phone probably because of the payload length.

## 📚 Related Projects & Futher Reading

### USB HID Hardware without NetHunter

hak5 12x17: Hack Any 4-digit Android PIN in 16 hours with a USB Rubber Ducky 
https://archive.org/details/hak5_12x17

Hak5: USB Rubber Ducky
https://shop.hak5.org/products/usb-rubber-ducky-deluxe

USB-Rubber-Ducky Payloads
https://github.com/hak5darren/USB-Rubber-Ducky/wiki/Payloads

Teensy
https://www.pjrc.com/teensy/

Brute Forcing An Android Phone with a STM32F4Discovery Development Board
https://github.com/bbrother/stm32f4androidbruteforce
https://hackaday.com/2013/11/10/brute-forcing-an-android-phone/

Automated brute force attack against the Mac EFI PIN (Using a Teensy)
https://orvtech.com/atacar-efi-pin-macbook-pro-en.html
https://hackaday.io/project/2196-efi-bruteforcer

Droidbrute: An Android PIN cracking USB rubber ducky payload made efficient with a statistically generated wordlist.
https://github.com/mandatoryprogrammer/droidbrute

Discussion forum about the hak5 episode, and Android Brute Force 4-digit pin
https://forums.hak5.org/topic/28165-payload-android-brute-force-4-digit-pin/

### NetHunter HID keyboard attacks

NetHunter HID Keyboard Attacks
https://www.kali.org/docs/nethunter/nethunter-hid-attacks/

### Linux Kernel HID support

Human Interface Devices (HID)
https://www.kernel.org/doc/html/latest/hid/index.html#

Linux USB HID gadget driver and hid-keyboard program
https://www.kernel.org/doc/html/latest/usb/gadget_hid.html
https://github.com/aagallag/hid_gadget_test

The usb-devices script
https://github.com/gregkh/usbutils/blob/master/usb-devices

### Cracking Android PIN and Pattern files

AndroidPINCrack - bruteforce the Android Passcode given the hash and salt (requires root on the phone)
https://github.com/PentesterES/AndroidPINCrack

Android Pattern Lock Cracker - bruteforce the Android Pattern given an SHA1 hash (requires root on the phone)
https://github.com/sch3m4/androidpatternlock

### General Recovery Methods

[Android][Guide]Hacking And Bypassing Android Password/Pattern/Face/PI
https://forum.xda-developers.com/showthread.php?t=2620456

Android BruteForce using ADB & Shell Scripting
https://github.com/Gh005t/Android-BruteForce

### Forensic Methods and Hardware

PATCtech Digital Forensics: Getting Past the Android Passcode
http://patc.com/online/a/Portals/965/Android%20Passcode.pdf

XPIN Clip
https://xpinclip.com/

HDBox from HDB Team
https://hdb-team.com/product/hdbox/

Cellebrite UFED
https://www.cellebrite.com/en/ufed/

GrayKey from Grayshift
https://www.grayshift.com/graykey/

### PIN Analysis

Electromechanical PIN Cracking with Robotic Reconfigurable Button Basher (and C3BO)
https://www.defcon.org/html/defcon-21/dc-21-speakers.html#Engler

DataGenetics PIN analysis https://datagenetics.com/blog/september32012/index.html


