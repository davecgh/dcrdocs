# Ошибки и типичные проблемы 

## Paymetheus 

#### 1. Я только начал использовать Paymetheus, но, похоже, он завис. 

Когда Вы запускаете Paymetheus в первый раз, он загружает блокчейн. Это может занять
до часа времени и будет казаться, что Paymetheus ничего не делает.

#### 2.  Недопустимая кодовая фраза для главного приватного ключа

Это длинный способ сказать "Неверный пароль". Вы неверно ввели пароль своего 
кошелька.

#### 3. "Невозможно купить тикеты: недостаточно средств..." но у меня в кошельке достаточно. 

В Paymetheus присутствует известная ошибка, когда незрелые средства всчитываются, как доступные. После голосования тикета существует
окно в 256 блоков, когда средства все еще заблокированы. В этом состоянии они известны как незрелые. Когда данный период истекает,
ими снова можно пользоваться.

#### 4. Paymetheus отображает неправильный баланс. 

> Данные инструкции действительны для версии 0.8.x и могут не работать с более поздними версиями.

Если Paymetheus отображает неправильный баланс, Вы можете это исправить, используя утилиту командной строки, чтобы переписать некоторые файлы.
Кое-что из этого может быть Вам непонятно, если Вы не знаете, как пользоваться командной строкой, просто следуете инструкциям по строка за строкой, и все будет хорошо. Когда Вы видите команды `look like this`, просто скопируйте их и вставьте, как есть, в командную строку.
Не забудьте нажимать <ENTER> после каждой команды. Если Вы в тупике, зайдите на наш [Slack channel](https://decred.slack.com) и кто-то Вам поможет, но сначала проверьте команду и убедитесь, что Вы ввели ее правильно. Вам понадобится seed Вашего кошелька. Этот процесс займет около 15 минут.

1. Мы собираемся открыть три окна PowerShell. Нажмите клавишу Window. Введите 'powershell' (не вводите кавычки здесь или в следующих командах) и нажмите ENTER.
2. Сделайте это еще два раза.
3. Разместите окна так, чтобы видеть их все.
4. Скопируйте и вставьте следующую команду: `cd $env:programfiles/decred/paymetheus` (Примечание, для вставки в PowerShell нажмите CTRL+V или правый клик). Нажмите ENTER.
5. Выполните ту же команду в двух других окнах.
6. Откройте Windows Explorer.
7. Вставьте `%localappdata%/decred/paymetheus` в адресную строку. Нажмите ENTER.
8. Удалите папку 'mainnet'.
9. Перейдите в одно из окон PowerShell и вставьте `./dcrd -u <username> -P <password>`. Нажмите ENTER.
10. Перейдите в одно из других окон Powershell и вставьте `./dcrwallet --appdata=$env:localappdata/decred/paymetheus --create`
11. Следуйте инструкциям и импортируйте seed. Скажите нет, когда Вас спросят о дополнительном слое шифрования, и да, когда Вас спросят, есть ли у вас seed.
12. В командной строке введите свои seed words и дважды нажмите ENTER.
13. Вставьте следующую команду в то же окно: `./dcrwallet -u <username> -P <password> --appdata=$env:localappdata/decred/paymetheus`. Нажмите ENTER.
14. Введите кодовую фразу, которую Вы использовали при создании кошелька.
15. Перейдите в третье окно PowerShell и вставьте `./dcrctl -u <username> -P <password> --wallet -c $env:localappdata/decred/paymetheus/rpc.cert getbalance`. Нажмите ENTER.
16. Нажмите CTRL+C в первых двух окнах, чтобы закрыть программы (dcrd и dcrwallet).
17. Вы можете закрыть все три окна PowerShell.
18. Вернитесь в окно Explorer. Удалите два файла: rpc.cert и rpc.key.
19. Запустите программу Decred, чтобы снова использовать Paymetheus.

-----

## Proof-of-Stake 

#### 1. SНекоторые из моих пропущенных/истекших тикетов по-прежнему заблокированы более, чем на один день. 

1. Запустите процесс кошелька с флагом `--enablevoting`. Без этого он не подаст на аннуляцию.
2. Разблокируйте кошелек с помощью `dcrctl --wallet walletpassphrase <yourpassphrase> 0`. Для того, чтобы создавать аппеляции и подписывать их, кошелек должен быть разблокирован.
3. Задайте инструкции dcrd уведомлять кошелек о пропущенных тикетах, чтобы он подавал на аппеляции с помощью `dcrctl rebroadcastmissed`.


На этом этапе Вы должны увидеть некоторые подробности об аннулировании транзакций в журнале кошелька.
Как только эти транзакции будут "добыты" в блок (который будет являться следующим блоком),
Вы увидите, как средства переходят в категорию immaturestakegeneration в  `dcrctl --wallet`
getbalance output. Наконец, после 256 блоков, они перейдут в категорию расходуемые и, следовательно, будут доступны для истрачивания.