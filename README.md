# HeliBoard_th

**HeliBoard เวอร์ชันที่แก้ไขปัญหาการพิมพ์ภาษาไทย**

โปรเจกต์นี้เป็น fork ของ [HeliBoard](https://github.com/HeliBorg/HeliBoard) (ซึ่งพัฒนาต่อจาก [OpenBoard](https://github.com/openboard-team/openboard) และ [AOSP Keyboard](https://android.googlesource.com/platform/packages/inputmethods/LatinIME/)) ปรับแก้เพื่อให้พิมพ์ภาษาไทยได้ลื่นและใช้งานได้จริงมากขึ้น

ส่วนที่แก้ไขทำงานเฉพาะตอนพิมพ์ภาษาไทย (locale `th`) ภาษาอื่นทำงานเหมือน HeliBoard ต้นฉบับทุกประการ

> โปรเจกต์นี้ไม่ใช่โปรเจกต์ทางการของ HeliBoard และไม่ได้เกี่ยวข้องกับผู้พัฒนา HeliBoard ปัญหาที่เกี่ยวกับส่วนที่แก้ไขในเวอร์ชันนี้ ให้แจ้งที่ repository นี้ อย่าแจ้งที่ HeliBoard ต้นฉบับ

## ปัญหาภาษาไทยที่แก้ในเวอร์ชันนี้

**1. พิมพ์ประโยคยาว ๆ แล้วกระตุก**
ภาษาไทยไม่มีการเว้นวรรคคั่นคำ ทำให้ข้อความที่กำลังพิมพ์ (composing text) ยาวขึ้นเรื่อย ๆ และคีย์บอร์ดต้องค้นพจนานุกรมด้วยข้อความทั้งก้อนซ้ำทุกครั้งที่กดปุ่ม เวอร์ชันนี้ตัดข้อความเป็นชิ้นอัตโนมัติเมื่อยาวถึง 10 ตัวอักษร (ปรับได้ที่ค่า `THAI_MAX_COMPOSING_LENGTH`) โดยไม่แยกสระบน/ล่าง วรรณยุกต์ และสระหน้า (เ แ โ ไ ใ) ออกจากพยัญชนะ
ไฟล์ที่แก้: `InputLogic.java`

**2. ต้องกด space สองครั้ง**
เดิมการกด space ครั้งแรกเป็นเพียงการยืนยันข้อความที่กำลังพิมพ์ ยังไม่ใส่ช่องว่างจริง ต้องกดอีกครั้ง เวอร์ชันนี้ใส่ช่องว่างทันทีในการกดครั้งเดียว
ไฟล์ที่แก้: `InputLogic.java` (`handleSeparatorEvent`)

**3. Suggestion ภาษาไทยมั่ว เช่น "หน้า ที่", "กว่า ที่"**
ตัวถอดรหัสของคีย์บอร์ดเดาว่าผู้ใช้ลืมเว้นวรรค แล้วเสนอคำสองคำต่อกัน ซึ่งไม่เหมาะกับภาษาไทย เวอร์ชันนี้กรองคำแนะนำจากพจนานุกรมหลักที่มีช่องว่าง พยัญชนะเดี่ยว และตัวอักษรซ้ำล้วนทิ้ง โดยไม่แตะคำแนะนำจากพจนานุกรมส่วนตัวหรือคำที่ระบบเรียนรู้
ไฟล์ที่แก้: `Suggest.kt`

**4. ปิดการแก้คำอัตโนมัติสำหรับภาษาไทย**
ภาษาไทยไม่มีตัวแบ่งคำที่ชัดเจน การแก้คำอัตโนมัติจึงมักแก้ผิดจุด เวอร์ชันนี้ปิดการแก้คำอัตโนมัติเฉพาะภาษาไทย
ไฟล์ที่แก้: `SettingsValues.java`

## พจนานุกรมภาษาไทย

พจนานุกรมไทย (`main_th.dict`) ที่ใช้กับเวอร์ชันนี้สร้างจากคลังคำของ [PyThaiNLP](https://github.com/PyThaiNLP/pythainlp/tree/dev/pythainlp/corpus) (สัญญาอนุญาต CC0-1.0) มีประมาณ 87,000 คำ ทุกคำเป็นอักษรไทย

ข้อมูลต้นทางมีคำสกปรกปนอยู่บ้าง เช่น พยัญชนะเดี่ยวที่ถูกนับเป็นคำ และคำที่เป็นตัวอักษรซ้ำ ซึ่งแก้ด้วยตัวกรองในข้อ 3 ด้านบน

## ไฟล์ที่ต่างจาก HeliBoard ต้นฉบับ

สำหรับผู้ที่ต้อง merge การเปลี่ยนแปลงจาก upstream ไฟล์ที่ได้รับการแก้ไขคือ

- `app/src/main/java/helium314/keyboard/latin/inputlogic/InputLogic.java`
- `app/src/main/java/helium314/keyboard/latin/Suggest.kt`
- `app/src/main/java/helium314/keyboard/latin/settings/SettingsValues.java`

## เครดิตและสัญญาอนุญาต

โปรเจกต์นี้ดัดแปลงมาจาก HeliBoard โดย 9-tnk (แก้ไขเมื่อปี 2026) และเผยแพร่ภายใต้สัญญาอนุญาตเดียวกับต้นฉบับ คือ **GNU General Public License v3.0** ผลงานทั้งหมดเป็นของผู้พัฒนาต้นฉบับ ขอบคุณ

- [HeliBoard](https://github.com/HeliBorg/HeliBoard) และ [ผู้ร่วมพัฒนา](https://github.com/HeliBorg/HeliBoard/graphs/contributors) ทั้งหมด
- [OpenBoard](https://github.com/openboard-team/openboard)
- [AOSP Keyboard](https://android.googlesource.com/platform/packages/inputmethods/LatinIME/)
- [PyThaiNLP](https://github.com/PyThaiNLP/pythainlp) สำหรับคลังคำภาษาไทย

รายละเอียดเครดิตและสัญญาอนุญาตฉบับเต็มของต้นฉบับอยู่ด้านล่าง

---

# HeliBoard (upstream README)

ส่วนต่อไปนี้คือเนื้อหาจาก README ของ HeliBoard ต้นฉบับ คงไว้เพื่อรายละเอียดและเครดิต (รายการฟีเจอร์ทั้งหมดดูได้ที่ [หน้า repository ของ HeliBoard](https://github.com/HeliBorg/HeliBoard))

## Features (upstream, partial)

- Gesture typing (requires an external library)
  - library not included in the app, as there is no compatible open source library available
  - can be extracted from GApps packages ("*swypelibs*"), or downloaded [here](https://github.com/erkserkserks/openboard/tree/46fdf2b550035ca69299ce312fa158e7ade36967/app/src/main/jniLibs) (click on the file and then "raw" or the tiny download button)
- Clipboard history
- One-handed mode
- Split keyboard
- Number pad
- Backup and restore your settings and learned word / history data

For [FAQ](https://github.com/HeliBorg/HeliBoard/wiki/FAQ), [hidden features](https://github.com/HeliBorg/HeliBoard/wiki/9.-Hidden-features) and more information about the app and features, please visit the [wiki](https://github.com/HeliBorg/HeliBoard/wiki)

# Contributing ❤

## Reporting Issues

Whether you encountered a bug, or want to see a new feature in HeliBoard, you can contribute to the project by opening a new issue [here](https://github.com/HeliBorg/HeliBoard/issues). Your help is always welcome!

Before opening a new issue, be sure to check the following:
 - **Does the issue already exist?** Make sure a similar issue has not been reported by browsing [existing issues](https://github.com/HeliBorg/HeliBoard/issues?q=). Please search open and closed issues. In case of feature requests you could also check the [FAQ](https://github.com/HeliBorg/HeliBoard/wiki/FAQ) and [hidden features](https://github.com/HeliBorg/HeliBoard/wiki/9.-Hidden-features).
 - **Is the issue still relevant?** Make sure your issue is not already fixed in the latest version of HeliBoard.
 - **Is it a single topic?** If you want to suggest multiple things, open multiple issues.
 - **Did you use the issue template?** It is important to make life of our kind contributors easier by avoiding issues that miss key information to their resolution.
 - **Is it written by a human?** Do not use LLMs or similar to generate issues. Having LLMs help with translation or similar is acceptable, but must be disclosed. See also [AI_USAGE.md](AI_USAGE.md)
Note that issues that that ignore part of the issue template will likely get treated with very low priority, as often they are needlessly hard to read or understand (e.g. huge screenshots, not providing a proper description, or addressing multiple topics). Blatant violation of the guidelines may result in the issue getting closed.

If you're interested, you can read the following useful text about effective bug reporting (a bit longer read): https://www.chiark.greenend.org.uk/~sgtatham/bugs.html

## Translations
Translations can be added using [Weblate](https://translate.codeberg.org/projects/heliboard/). You will need an account to update translations and add languages. Add the language you want to translate to in Languages -> Manage translated languages in the top menu bar.
Updating translations in a PR will not be accepted, as it may cause conflicts with Weblate translations.

Some notes on translations
* when translating metadata, translating the changelogs is rather useless. It's available as it was requested by translators.
* the `hidden_features_message` is horrible to translate with Weblate, and serves little benefit as it's just a copy of what's already in the wiki: https://github.com/HeliBorg/HeliBoard/wiki/9.-Hidden-features. It's been made available in the app on user request/contribution.

## To Community
There is the [discussions on GitHub](https://github.com/HeliBorg/HeliBoard/discussions), or if you prefer a more open network there is [Lemmy](https://lemmy.world/c/Heliboard).
You can share your themes, layouts and dictionaries with other people:
* Themes can be saved and loaded using the menu on top-right in the _adjust colors_ screen
  * you can share custom colors in a separate [discussion section](https://github.com/HeliBorg/HeliBoard/discussions/categories/custom-colors)
  * there are theme collections available at [Star-Trowa/heliboard-themes](https://github.com/Star-Trowa/heliboard-themes) and [PickleHik3/droid-tings](https://github.com/PickleHik3/droid-tings)
* Custom keyboard layouts are text files whose content you can edit, copy and share
  * this applies to main keyboard layouts and to special layouts adjustable in advanced settings
  * see [layouts.md](layouts.md) for details
  * you can share custom layouts in a separate [discussion section](https://github.com/HeliBorg/HeliBoard/discussions/categories/custom-layout)
  * [Roccobot's Layout Maker](https://roccobot.github.io/HeliBoard-RLM/) is a browser-based editor for json layout files
* Creating dictionaries is a little more work
  * first you will need a wordlist, as described [here](https://codeberg.org/Helium314/aosp-dictionaries/src/branch/main/wordlists/sample.combined) and in the repository readme
  * the you need to compile the dictionary using [external tools](https://github.com/remi0s/aosp-dictionary-tools)
  * the resulting file (and ideally the wordlist too) can be shared with other users
  * note that there will not be any further dictionaries added to this app, but you can add dictionaries to the [dictionaries repository](https://codeberg.org/Helium314/aosp-dictionaries)

## Code Contribution
See [Contribution Guidelines](CONTRIBUTING.md)

# Links
* Info
  * [Wiki](https://github.com/HeliBorg/HeliBoard/wiki), including FAQ, help on customizing layouts, and gesture data gathering
  * [Layout documentation](layouts.md) (more technical info regarding layout customization)
  * [For creating custom dictionaries](https://codeberg.org/Helium314/aosp-dictionaries#wordlist-information) (see also top of the linked readme)
* Community
  * [Lemmy](https://lemmy.world/c/Heliboard)
  * [Reddit](https://www.reddit.com/r/HeliBoard)
  * GitHub [discussions](https://github.com/HeliBorg/HeliBoard/discussions)
* Other
  * [Translations](https://translate.codeberg.org/projects/heliboard/)
  * [Dictionaries](https://codeberg.org/Helium314/aosp-dictionaries)
  * [k3lp](https://codeberg.org/k3lp/k3lp) is a WIP library for keyboard layout parsing that will be implemented in HeliBoard when ready (created by [FlorisBoard](https://github.com/florisboard/florisboard/) maintainers)
  * [swipe-o-scope](https://codeberg.org/eclexic/swipe-o-scope) for visualizing gesture data as created when using gesture data gathering

# License

HeliBoard (as a fork of OpenBoard) is licensed under GNU General Public License v3.0.

 > Permissions of this strong copyleft license are conditioned on making available complete source code of licensed works and modifications, which include larger works using a licensed work, under the same license. Copyright and license notices must be preserved. Contributors provide an express grant of patent rights.

See repo's [LICENSE](/LICENSE) file.

Since the app is based on Apache 2.0 licensed AOSP Keyboard, an [Apache 2.0](LICENSE-Apache-2.0) license file is provided.
The icon is licensed under [Creative Commons BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/). A [license file](LICENSE-CC-BY-SA-4.0) is also included.

# Credits
- Icon by [Fabian OvrWrt](https://github.com/FabianOvrWrt) with contributions from [The Eclectic Dyslexic](https://github.com/the-eclectic-dyslexic)
- [OpenBoard](https://github.com/openboard-team/openboard)
- [AOSP Keyboard](https://android.googlesource.com/platform/packages/inputmethods/LatinIME/)
- [LineageOS](https://review.lineageos.org/admin/repos/LineageOS/android_packages_inputmethods_LatinIME)
- [Simple Keyboard](https://github.com/rkkr/simple-keyboard)
- [Indic Keyboard](https://gitlab.com/indicproject/indic-keyboard)
- [FlorisBoard](https://github.com/florisboard/florisboard/)
- Our [contributors](https://github.com/HeliBorg/HeliBoard/graphs/contributors)

## Funding

This project is funded through [NGI Mobifree Fund](https://nlnet.nl/mobifree), a fund established by [NLnet](https://nlnet.nl) with financial support from the European Commission's [Next Generation Internet](https://ngi.eu) program. Learn more at the [NLnet project page](https://nlnet.nl/project/GestureTyping).

[<img src="https://nlnet.nl/logo/banner.png" alt="NLnet foundation logo" width="20%" />](https://nlnet.nl)

Further the project benefits from donations provided by many users (thank you all!).
