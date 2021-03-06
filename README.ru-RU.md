## Оглавление
 - [Описание](#Описание)
 - [Usage] (#usage)
 - [Чаво] (#чаво)
 - [Обзор сборок Windows] (#Windows-сборки-обзор)
 - [Advanced usage] (#advanced-usage)
 - [Поддержание собственных вилок](#поддержание собственных вилок)
 - [Руководящие принципы] (#вклад-руководящие принципы)

&nbsp;

## Описание

Это сценарий PowerShell для автоматизации рутинных задач, выполненных после новых установок Windows 10 и Windows Server 2016 / 2019. Это ни в коем случае не полный набор всех существующих настроек Windows, и ни это другой "антишпионский" тип сценария. Это просто настройка, которую я люблю использовать и которая, на мой взгляд, делает систему менее навязчивой.

&nbsp;

## Использование
Если вы просто хотите запустить сценарий с предустановкой по умолчанию, скачайте и распакуйте [последний релиз](https://github.com/Disassembler0/Win10-Initial-Setup-Script/releases), а затем просто дважды щелкните на *по умолчанию.cmd * file и confirm *User Account Control* prompt. Убедитесь, что ваша учетная запись является членом *Administrators* group, поскольку скрипт пытается работать с повышенными привилегиями.

Сценарий поддерживает параметры и параметры командной строки, которые могут помочь вам настроить выбор настройки или даже добавить свои собственные настройки, однако эти функции требуют некоторых базовых знаний об использовании командной строки и сценариев PowerShell. Дополнительные сведения см. В разделе [Advanced usage](#advanced-usage).

&nbsp;

## ЧаВо

**Вопрос:* * Можно ли безопасно запустить скрипт? 
** A: * * определенно нет. Вы должны понять, что делают функции и какие последствия будут для вас, если вы их запустите. Некоторые функции снижают уровень безопасности, скрывают элементы управления или удаляют приложения. **Если вы не уверены, что делает скрипт, не пытайтесь его запустить!**

**Вопрос:* * Можно ли повторно запустить сценарий? 
**A:* * Да! На самом деле сценарий был написан, чтобы поддержать именно это, так как это не редкость, что большие обновления Windows сбросить некоторые настройки.

**Вопрос: * * какие версии и выпуски Windows поддерживаются? 
** A: * * скрипт стремится быть полностью совместимым с самой последней 64-битной версией Windows 10, получающей обновления из полугодового канала, однако, если вы создадите свой собственный пресет и исключите несовместимые настройки, он будет работать также на LTSB/LTSC и, возможно, также на 32-битных системах. Подавляющее большинство настроек будет работать на всех выпусках Windows. Некоторые из них зависят от параметров групповой политики, поэтому для выпусков Home и Education может быть несколько ограничений.

**Вопрос: * * Можно ли запустить сценарий на Windows Server 2016 или 2019? 
**утвердительный ответ. Начиная с версии 2.5, поддерживается Windows Server. Есть даже несколько настроек, характерных для серверной среды. Имейте в виду, хотя, что сценарий по-прежнему в первую очередь предназначен для Windows 10, так что вы должны создать свой собственный набор настроек.

**Вопрос: * * Можно ли запустить сценарий в Windows 7, 8, 8.1 или в других версиях Windows? 
**отсутствие. Хотя некоторые настройки могут работать также на более старых версиях Windows, сценарий разработан только для Windows 10 и Windows Server 2016 / 2019. Поддержка более старых версий не планируется.

**Вопрос:* * Можно ли запустить сценарий в многопользовательской среде? 
**A: * * да, в определенной степени. Некоторые настройки (в частности, настройки пользовательского интерфейса) устанавливаются только для пользователя, выполняющего скрипт. Как было сказано выше, скрипт можно запускать несколько раз, поэтому его можно запускать несколько раз, каждый раз, как отдельный пользователь. Из-за характера механизмов проверки подлинности и повышения привилегий в Windows, большинство настроек могут быть успешно применены только пользователями, принадлежащими к *Administrators* group. Стандартные пользователи получат приглашение UAC, запрашивающее учетные данные администратора, которые затем заставляют настройки быть примененными к данной учетной записи администратора, а не к исходной непривилегированной учетной записи. Есть несколько способов, как это можно обойти программно, но я не планирую включать любой, поскольку это отрицательно повлияет на сложность кода и читабельность. Если вы все еще хотите попробовать использовать скрипт в многопользовательской среде, проверьте [этот ответ в выпуске #29] (https://github.com/Disassembler0/Win10-Initial-Setup-Script/issues/29#issuecomment-333040591) для некоторых указателей.

**Q: * * вы тестировали скрипт? 
**утвердительный ответ. Я тестирую новые дополнения на современных выпусках 64bit Home и Enterprise в VMs. Я также регулярно использую его для всех моих домашних установок после всех больших обновлений.

**Q**: я запустил сценарий, и он сделал что-то, что мне не нравится, как я могу его отменить? 
**A:** для каждой настройки есть также соответствующая функция, которая восстанавливает настройки по умолчанию. Значение по умолчанию считается только что установленной Windows 10 или Windows Server 2016 без каких-либо корректировок во время или после установки. Используйте настройки для создания и запуска нового пресета. В качестве альтернативы, поскольку некоторые функции являются просто автоматизацией для действий, которые могут быть сделаны с помощью GUI, найти соответствующий контроль и изменить его вручную.

**Q:* * я запустил сценарий, и некоторые элементы управления теперь серые и отображают сообщение " *некоторые настройки скрыты или управляются вашей организацией*", почему? 
**A:* * чтобы гарантировать, что общесистемные настройки применяются гладко и надежно, некоторые из них используют *объекты групповой политики* (*GPO*). Такой же механизм используется и в компаниях, управляющих своими компьютерами в больших масштабах, поэтому пользователи без прав администратора не могут изменять настройки. Если вы хотите изменить параметр, заблокированный GPO, примените соответствующую настройку восстановления, и элемент управления снова станет доступным.

** Q: * * я запустил сценарий, и он сломал мой компьютер / убил соседскую собаку / вызвал мировую войну 3. 
** A:* * мне все равно. Кроме того, это не вопрос.

** Q:* * я использую настройку для & lt;feature & gt; В моей установке, можете ли вы добавить ее? 
**A: * * отправить PR, создать проблему запроса функции или напишите мне сообщение. Если я нахожу функциональность простой, полезной и не зависящей от каких-либо сторонних модулей или исполняемых файлов (включая также *Chocolatey*, *NuGet*, *Ninite* или другие решения автоматизации), я мог бы добавить ее.

**Q: * * могу ли я использовать скрипт или модифицировать его для нужд моей / моей компании? 
** A: * * конечно, нокаутировать себя. Просто не забудьте включить уведомление об авторских правах в соответствии с требованиями лицензии MIT. Я бы также предложил включить ссылку на этот репозиторий GitHub, поскольку очень вероятно, что что-то будет изменено, добавлено или улучшено, чтобы отслеживать будущие версии Windows 10.

**Q: * * почему в некоторых функциях есть повторяющиеся фрагменты кода? 
**A:** таким образом, вы можете непосредственно взять функциональный блок или линию из внутри функции и использовать ее в другом месте, не детализируя какие-либо зависимости.

**Q:* * Как долго вы собираетесь поддерживать скрипт? 
**A: * * до тех пор, пока я использую Windows 10.

**Q:* * мне очень нравится скрипт. Могу ли я отправить пожертвование? 
**A: * * отправляйте пожертвования через [PayPal](https://www.paypal.me/Disassembler). Любая сумма приветствуется, но имейте в виду, что пожертвования полностью добровольны, и я не обязан вносить какие-либо корректировки в ваш сценарий, независимо от пожертвованной суммы. Вы также можете отправить мне письмо, чтобы обсудить альтернативный способ.

&nbsp;

## Обзор сборок Windows

/ Version / Code name / название маркетинга / Build |
| :-----: | ----------------------- | ---------------------- | :---: |
/ 1507 / порог 1 (TH1 / RTM) / N | A / 10240 |
/ 1511 / порог 2 (TH2) | ноябрь Update / 10586 |
| 1607 | Redstone 1 (RS1) / Anniversary Update / 14393 |
| 1703 / Редстоун 2 (RS2) / Creators Update / 15063 |
| 1709 | Редстоун 3 (RS3) / Fall Creators Update / 16299 |
/ 1803 / Редстоун 4 (RS4) / апрель 2018 Update / 17134 |
/ 1809 / Редстоун 5 (RS5) / октябрь 2018 Update / 17763 |

&nbsp;

## Расширенное использование

 powershell.exe-NoProfile-ExecutionPolicy Bypass-файл Win10.ps1 [- включить имя файла] [- предустановленное имя файла] [[!] tweakname]

 - включить модуль загрузки имени файла с пользовательскими настройками
 - предустановленное имя файла загрузить предустановку с именами tweak, чтобы применить
 tweakname применить tweak с этим конкретным именем
 !tweakname remove tweak with this specific name from selection

### Задает

Библиотека tweak состоит из отдельных идемпотентных функций, содержащих по одному. Функции могут быть сгруппированы в * presets*. Preset - это просто список имен функций, которые должны вызываться. Любая функция, которая не присутствует или комментируется в пресете, не будет вызвана, поэтому соответствующая настройка не будет применена. Для того, чтобы скрипт что-то сделал, вам нужно предоставить по крайней мере одну библиотеку tweak через `- include` и по крайней мере одно имя tweak, либо через ` - preset` или непосредственно в качестве аргумента командной строки.

Имена tweak могут быть префиксом с восклицательным знаком (`!') что вместо этого приведет к удалению настройки из выбора. Это полезно в тех случаях, когда вы хотите применить весь набор настроек, но опустить несколько конкретных настроек в текущем запуске. Кроме того, вы можете иметь пресет, который "патчи" другой пресет, добавив и удалив небольшое количество настроек.

Чтобы предоставить настроенный набор настроек, можно передать имена функций непосредственно в качестве аргументов.

 powershell.exe-NoProfile-ExecutionPolicy Bypass-файл Win10.ps1-включить Win10.psm1 EnableFirewall EnableDefender

Или вы можете создать файл, где вы пишете имена функций (одно имя функции в строке, без запятых или кавычек, пробелов, комментариев, начиная с`#`), а затем передать имя файла с помощью параметра `-preset`. 
Пример предустановленного файла ' mypreset.формат txt`:

 # Настройки безопасности
 EnableFirewall
 EnableDefender

 # UI настройки
 ShowKnownExtensions
 Showhiddenfiles # только скрытый, не система

Команда с помощью предустановленного файла выше:

 powershell.exe-NoProfile-ExecutionPolicy Bypass-файл Win10.ps1-включить Win10.psm1-предустановка mypreset.формат txt

### Включает

Скрипт также поддерживает включение пользовательских настроек из пользовательских модулей, переданных через параметр '- include'. Содержимое модуля, поставляемого пользователем, полностью зависит от пользователя, однако настоятельно рекомендуется разделять настройки в соответствующих функциях, как и в основной библиотеке настроек. Пользовательские скрипты загружаются в основной скрипт через "Import-Module", поэтому библиотека должна быть идеальной.модуль PowerShell psm1. 
Пример пользовательской библиотеки tweak ' mytweaks.psm1`:

'powershell
Функция MyTweak1 {
 Write-Output " Запуск MyTweak1..."
 # Сделать нечто
}

Функция MyTweak2 {
 Write-Output " Запуск MyTweak2..."
 # Сделай что-нибудь другое
}
```

Использование скрипта выше:

 powershell.exe-NoProfile-ExecutionPolicy Bypass-файл Win10.ps1-включить mytweaks.psm1 MyTweak1 MyTweak2

### Сочетание

Все характеристики описанные выше можно совместить. Вы можете иметь предустановку, которая включает в себя как твики из оригинального сценария и ваших личных. Оба`- include `и` - preset ' опции могут быть использованы более одного раза, так что вы можете разделить ваши настройки на группы, а затем объединить их в зависимости от ваших текущих потребностей. Модули` - include `всегда импортируются до применения первой настройки, поэтому порядок параметров командной строки не имеет значения, как и порядок настроек (за исключением` RequireAdmin`, который всегда должен быть вызван первым и` Restart', который должен всегда называться последним). Может случиться так, что некоторые настройки применяются более одного раза во время запуска singe, потому что у вас есть несколько пресетов. Это не должно вызвать никаких проблем, поскольку настройки идемпотентны. 
Пример предустановленного файла ' otherpreset.формат txt`:

 MyTweak1
 MyTweak2
 !Showhiddenfiles # удалит настройку из выбора
 WaitForKey

Команда, использующая все три объединенных примера:

 powershell.exe-NoProfile-ExecutionPolicy Bypass-файл Win10.ps1-включить Win10.psm1-включая mytweaks.psm1-предустановка mypreset.txt-пресет otherpreset.перезапуск txt

&nbsp;

## Поддержание собственных вилок

Самый простой способ настроить параметры сценария это создать свой собственный набор настроек и, при необходимости, свои собственные скрипты настройки, как описано выше. Для легкого запуска вы можете основывать модификации на *Default.cmd* и *по умолчанию.preset* и поддерживать только что. Если вы все равно решите раскошелиться на сценарий, вам не нужно комментировать или удалять фактические функции в *Win10.psm1*, потому что если они не вызываются, они не используются.

Если вы хотите сделать более сложные изменения базового сценария и включить некоторые личные настройки или корректировки, то я предлагаю сделать это следующим образом:

1. Раскройте хранилище на GitHub (очевидно).
2. Клонируйте вилку на компьютере.

 ```
 клон git https://github.com/<yournamehere> / Win10-Initial-Setup-Script
 cd Win10-начальная настройка-скрипт
 ```

3. Добавьте исходный репозиторий в качестве удаленного (*upstream*).

 ```
 git remote добавить вверх https://github.com/Disassembler0/Win10-Initial-Setup-Script
 ```

4. Внесите изменения по своему усмотрению.
5. Как только в вышестоящей ветке появятся новые дополнения, создайте временную ветвь, извлеките изменения и сбросьте ветвь, чтобы она была идентична этому репозиторию.

 ```
 ветвь git вверх по течению
 git checkout upstream
 git fetch upstream
 git reset -- hard upstream / master
 ```

6. Когда у вас есть восходящая ветвь до даты, проверьте назад вашего хозяина и перебазируйте его на основе восходящей ветви. Если между наборами изменений есть конфликты, их необходимо разрешить вручную.

 ```
 git checkout master
 git rebase upstream
 ```

7. В конце концов, удалите ветку вверх по течению и заставьте свои изменения вернуться на GitHub.

 ```
 ветвь git-D вверх по течению
 git push-F master
 ```

** Слово предупреждения: * * Rebasing и Force-pushing изменит историю ваших коммитов. Плюс в том, что ваши корректировки всегда будут оставаться на вершине истории фиксации. Недостатком является то, что все удаленное отслеживание вашего репозитория всегда придется перебазировать и принудительно нажимать, иначе их история коммитов не будет соответствовать вашей.

&nbsp;

## Руководство по взносам

Ниже приведен список правил, которые я пытаюсь применить в этом проекте. Правила не являются обязательными, и я принимаю запросы pull, даже если они не придерживаются их, пока их цель и содержание ясны. В тех случаях, когда существует слишком много нарушений правил, я мог бы просто переделать весь функционал и отклонить PR, все еще кредитовая вас. Если вы хотите сделать мою работу проще, пожалуйста, подумайте о соблюдении следующих правил.

### Именование функций
Попытайтесь дать функции осмысленное имя длиной до 25 символов, которое отдает назначение функции. Используйте такие команды, как`Enable`/` Disable`,`Show`/` Hide`,`Install`/` Uninstall`,`Add`/` Remove ' в начале имени функции. Если функция не соответствует ни одной из этих глаголов, придумайте другое имя, начиная с глагола "Set", который указывает, что делает функция, например "SetCurrentNetworkPrivate" и "SetCurrentNetworkPublic".

### Отменить функции
Всегда добавляйте функцию с противоположным именем (или эквивалентом), которая возвращает поведение по умолчанию. Значение по умолчанию считается только что установленной Windows 10 или Windows Server 2016 / 2019 без каких-либо корректировок во время или после установки. Если у вас нет доступа к любому из них, создайте функцию revert, насколько вам известно, и я при необходимости заполню остальные.

### Сходства функций
Проверьте, нет ли уже функции, аналогичной той, которую вы пытаетесь добавить. Пока имя и цель существующей функции не изменяются, не стесняйтесь добавлять свою настройку к этой функции, а не создавать новую.

### Группировка функций
Попробуйте группировать функции тематически. Есть уже несколько основных групп (конфиденциальность, безопасность, услуги и т.д.), но даже внутри них некоторые настройки могут быть связаны друг с другом. В таком случае добавьте новый tweak ниже существующего и не до конца всей группы.

### По умолчанию
Всегда добавляйте ссылку на tweak и его функцию revert в *Default.заданный.* Добавьте ссылки на обе функции в одной строке (запомните пробелы)и всегда закомментируйте функцию revert. Следует ли комментировать также tweak в заданной по умолчанию является вопросом личных предпочтений. Эмпирическое правило заключается в том, что если настройка делает систему более быстрой, гладкой, более безопасной и менее навязчивой, она должна быть включена по умолчанию. Юзабилити имеет предпочтение перед производительностью (поэтому, например, индексирование поддерживается).

### Повторяемость
Если не применяется к неподдерживаемой системе, все функции должны применяться повторно без каких-либо ошибок. Когда вы создаете раздел реестра, всегда проверяйте сначала, если ключ не существует. При удалении значения реестра всегда добавляйте '- ErrorAction SilentlyContinue ' для предотвращения ошибок при удалении уже удаленных значений.

### Скрытие ввода / вывода
Подавите все выходные данные, сгенерированные командами и командлетами, используя `| Out-Null` или `-ErrorAction SilentlyContinue`, где это применимо. Когда требуется ввод, используйте соответствующие аргументы для подавления запроса и программно укажите значения для команды для запуска (например, с помощью `-Confirm:$false`). Единственный приемлемый выход - из командлетов` Write-Output ' в начале каждой функции и из непосильных командлетов типа `Remove-AppxPackage`.

### Реестр
Создайте разделы реестра, только если они не существуют при новой установке Windows 10 или Windows Server 2016 / 2019. При удалении реестра удаляйте только значения реестра, а не все ключи. Когда вы устанавливаете значения реестра, всегда используйте 'Set-ItemProperty 'вместо' New-ItemProperty`. При удалении значений реестра выберите "Set-ItemProperty" или "Remove-ItemProperty", чтобы восстановить ту же ситуацию, что и при чистой установке. Опять же, если вы не знаете, что было первоначальное состояние, дайте мне знать в описании PR, и я заполню пробелы. Если вам нужно использовать куст реестра `HKEY_USERS`, всегда добавляйте следующий фрагмент перед изменением реестра, чтобы обеспечить переносимость.

'powershell
Если (!(Test-Path " HKU:")) {
 New-PSDrive-Name HKU-PSProvider Registry-Root HKEY_USERS / Out-Null
}
```

### Использование силы
Star Wars шутит в сторону, не используйте опцию `-Force`, если это абсолютно необходимо. Единственный допустимый случай, когда вы создаете новый раздел реестра (не значение), и вы должны убедиться, что все родительские ключи будут созданы, а также. В этом случае всегда проверяйте сначала, если ключ еще не существует, иначе вы удалите все его существующие значения.

### Примечание
Всегда добавляйте простой комментарий над функцией, кратко описывая, что делает функция, особенно если она имеет неоднозначное имя или если под капотом скрыта какая-то логика. Если вы знаете, что tweak не работает в некоторых выпусках Windows 10 или Windows Server, укажите его в комментарии. Добавьте командлет` Write-Output ' с кратким описанием действия также в первую строку тела функции, чтобы пользователь мог видеть, что выполняется и какая функция является проблемной при возникновении ошибки. Комментарий написан в настоящем простом времени, "запись-выход" в настоящем непрерывном с многоточием (ОТВ. три точки) в конце.

### Стиль кодирования
Отступ с помощью вкладок, заключите все строковые значения в двойные кавычки ( ` ") и строго используйте "PascalCase", где это возможно. Поместите открывающуюся фигурную скобку на ту же строку, что и имя функции или условие, но оставьте закрывающую скобку на отдельной строке для удобства чтения.

### Образцы

** Пример именования**: рассмотрим функцию 'EnableFastMenu'. Что он делает? Какое меню? Как быстро *быстро*? Лучшим именем может быть "EnableFastMenuFlyout", поэтому немного яснее, что мы говорим о задержках меню. Но функция-аналог была бы "DisableFastMenuFlyouts", что не совсем так. Мы не *отключение* ничего, мы просто делаем это медленно снова. Поэтому еще лучше было бы назвать их "SetFastMenuFlyouts" и "SetSlowMenuFlyouts". Или еще лучше, просто добавьте функциональность к уже существующему "SetVisualFXPerformance" / "SetVisualFXAppearance". Несмотря на то, что имена не совпадают на 100%, они стремятся настроить похожие аспекты и работать в рамках тех же разделов реестра.

** Пример кодирования:* * следующий код применяет большинство правил, упомянутых выше (именование, скрытие вывода, повторяемость, использование силы, комментарии и стиль кодирования).

'powershell
# Включить некоторые функции
Функция EnableSomeFeature {
 Запись-вывод " включение некоторых функций..."
 Если (!(Тест-путь " HKLM:\some\Registry\Key")) {
 New-Item-Path "HKLM:\Some\Registry\Key" - Force / Out-Null
 }
 Set-ItemProperty-Path "HKLM:\some\Registry\Key "- Name "SomeValueName" - type String-Value " SomeValue"
}

# Отключить некоторые функции
Функция DisableSomeFeature {
 Write-Output " отключение какой-либо функции..."
 Remove-ItemProperty-путь "HKLM:\some\Registry\Key "- имя "SomeValueName" - ErrorAction SilentlyContinue
}
```
