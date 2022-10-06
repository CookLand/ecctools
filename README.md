# ecctools
Небольшая коллекция инструментов, написанных на C для выполнения математических операций с приватными и публичными ключами.

## Предупреждение
- Этот репозиторий включает небольшую библиотеку криптографии ECDSA.
- Эта библиотека была сделана по документации, но в ней могут быть ошибки.
- Используйте его на свой страх и риск.

```
THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
```

## Список инструментов в этом репозитории

- [keygen](https://github.com/CookLand/ecctools#keygen)
- [sharedsecret](https://github.com/CookLand/ecctools#sharedsecret)
- [rehashaddress](https://github.com/CookLand/ecctools#rehashaddress)
- [calculatefromkey](https://github.com/CookLand/ecctools#calculatefromkey)
- [calculatefrompublickey](https://github.com/CookLand/ecctools#calculatefrompublickey)
- [keydivision](https://github.com/CookLand/ecctools#keydivision)
- [keymath](https://github.com/CookLand/ecctools#keymath)
- [modmath](https://github.com/CookLand/ecctools#modmath)
- [addr2rmd](https://github.com/CookLand/ecctools#addr2rmd)

## Как скачать и запустить код?

Для пользователей Termux вам необходимо установить некоторые утилиты 

```
apt update && apt upgrade
apt install root-repo
apt install git -y
apt install build-essential -y
apt install libssl-dev
apt install libgcrypt20-dev
apt install libgmp-dev

```

Клонировать этот репозиторий:

```
git clone https://github.com/CookLand/ecctools.git
```

Скомпилировать командой:

```
make
```

## keygen

Этот инструмент генерирует один частный и его адрес / открытый ключ, сжатый и несжатый по запросу..

Вы можете указать размер в битах от 1 до 256 с помощью `-b значение`, где `значение` это количество битов в вашем ключе.
если вы не укажете это значение бит может варьироваться от 1 до 256, это будет зависеть только от RNG

Также вы можете выбрать случайный источник для этого ключа `-s источник`, где `источник` это одно из следующих значений.
Источник по умолчанию `urandom`

- `urandom` использовать Linux-устройство `/dev/urandom`
- `random` использовать Linux-устройство `/dev/random`
- `openssl` использовать RND собрать в библиотеке openssl
- `getrandom` использовать функцию ядра Linux getrandom, это может быть эквивалентно urandom в некоторых ядрах
- `gcrypt` использовать сборку RND в библиотеке gcrypt

Пример:

```
CookLand $ ./keygen
KEY (Secret: DON'T SHARE THIS VALUE): 18f619c4d16057d362ddfce772f551d46a2390517698226cff6e5bf67ac4b324
publickey uncompressed: 04af6e50db92ce378c29df0ed9a04d3431bc06762aa37ec3ab42af14708363059616992ab8abb4bd8cfcc7d8c8d40331d419f426d7fc8490efb1af47b6316d7a1b
address uncompressed 1CXHD79mKUxUDruD2iEJxs8XBwiJwYGmTR
publickey compressed: 03af6e50db92ce378c29df0ed9a04d3431bc06762aa37ec3ab42af147083630596
address compressed 1PTA5NaUfu5xU5pEmEfobsZbRuWvs4Sik3

```

## sharedsecret

В криптографии важно иметь общий секрет с человеком, с которым вы общаетесь.
Но также очень важно иметь способ передать эту тайну, не раскрывая ее всему миру.

Этот инструмент вычисляет этот общий секрет с вашим закрытым ключом и открытым ключом другого человека.

Алиса (вы) хотите отправить зашифрованный файл Бобу (другому человеку)
У Алисы есть свой закрытый ключ, и Алиса знает открытый ключ Боба.

Алиса использует этот инструмент для вычисления общего секрета, который будет использоваться для шифрования файла.

Alice example Keys:
Private key: 18f619c4d16057d362ddfce772f551d46a2390517698226cff6e5bf67ac4b324 (Unknow for Bob and everyone else)
Public key : 03af6e50db92ce378c29df0ed9a04d3431bc06762aa37ec3ab42af147083630596 (Public)

Bob example Keys:
Private key: bc9e78f140a76cbdcdbecc5ab0ec38b4db710edfa40dea342712c8a695fe8b22 (Unknow for Alice and everyone else)
Public key : 022c8191049a3f2816bc95077b91caed87900d0cd2af3757004531face9c3b6082 (Public)

Алиса запускает программу:

```
./sharedsecret
A: private key (hex): 18f619c4d16057d362ddfce772f551d46a2390517698226cff6e5bf67ac4b324
B: public key : 022c8191049a3f2816bc95077b91caed87900d0cd2af3757004531face9c3b6082
Secret between A and B: 22fb667f3bc1a2153e3cef75df0ce757a1c86051c07ace6b0c30dc87f3358511 (DON'T SHARE, THIS IS SECRET)
```

Bob run the program:

```
./sharedsecret
A: private key (hex): bc9e78f140a76cbdcdbecc5ab0ec38b4db710edfa40dea342712c8a695fe8b22
B: public key : 03af6e50db92ce378c29df0ed9a04d3431bc06762aa37ec3ab42af147083630596
Secret between A and B: 22fb667f3bc1a2153e3cef75df0ce757a1c86051c07ace6b0c30dc87f3358511 (DON'T SHARE, THIS IS SECRET)
```
Алиса и Боб получают один и тот же общий секрет, только делясь друг с другом своими ОТКРЫТЫМИ ключами.

Алиса зашифровала свой секретный файл `input.txt` с паролем `22fb667f3bc1a2153e3cef75df0ce757a1c86051c07ace6b0c30dc87f3358511`

openssl aes-256-cbc -salt -pbkdf2 -in input.txt -out input.txt.enc

Боб расшифровывает секретный файл командой, используя тот же пароль `22fb667f3bc1a2153e3cef75df0ce757a1c86051c07ace6b0c30dc87f3358511`:

openssl aes-256-cbc -d -salt -pbkdf2 -in input.txt.enc -out message.txt

Примечание для криптографов, это просто базовое доказательство концепции, я знаю, что канал связи может быть скомпрометирован,
есть атаки mitm, нет аутентификации или целостности данных и т.д...

## rehashaddress

НЕстандартный, детерминированный генератор закрытых ключей из пароля или кодовой фразы.

какова цель этого инструмента? просто предоставьте простой способ генерировать детерминированные закрытые ключи из выбранного пароля или фразы-пароля и количества повторных хэшей

Advice: USE A SECURE PASSWORD

Пример использования:

Сгенерировать 2 приватных ключа и его адрес после 100 тысяч повторений

```./rehashaddress -p "}78~Et=jPQP5}MVVj2fc0X38{~I}?c" -n 2 -m 100000```

Выход:

```
[I] Password: }78~Et=jPQP5}MVVj2fc0X38{~I}?c
[I] n: 2
[I] m: 100000
Privatekey: ca12010ce2daaf02611d440acd42e8bc791881375c58197cf56995059b28e797
Compress publickey: 035f149fb58e6eb5a7bcc812c1f72e5c9a3ee7ea151991a31a4d3e98e5ace25568
Compress address: 16Ln3w1JLD8s7rNni37A7SiVmVPHXEHFWF
Uncompress publickey: 045f149fb58e6eb5a7bcc812c1f72e5c9a3ee7ea151991a31a4d3e98e5ace2556850fc0b339809eda8cc9f74ee698ed018049b0cfbefe72e66dbc256622f1662d7
Uncompress address: 1NKUnTCtN3Pbm3wEVsFZFuotKkjPPi4g3B
Privatekey: 1037ea7cc723e34f3d81d9e01b5b1df081d47312a3d549b67fd635424446c1b0
Compress publickey: 026813442784ee62c0d69dcf244f91518e85f4a796a1b967758aef1b7d88abd23b
Compress address: 1JWH4qYemWFJAULUtcx1QiboDAD64Bb718
Uncompress publickey: 046813442784ee62c0d69dcf244f91518e85f4a796a1b967758aef1b7d88abd23bd7e12a189fa7d6a80154cbf29999b74bece7bfcca8a70e3df92d8ece238e8700
Uncompress address: 1Ho9VcvHcdu5Xrkv3pcME5zaPvyje1AWnM
```

Пожалуйста, проверьте комментарии в исходном коде `rehashaddress.c`


## calculatefromkey

A easy way to check the address and publickeys from a privatekey 

what is the objective of this tool? just to check a privatekey and his address and publickey

Example of use

```./calculatefromkey ca12010ce2daaf02611d440acd42e8bc791881375c58197cf56995059b28e797```

Output:

```
privatekey: ca12010ce2daaf02611d440acd42e8bc791881375c58197cf56995059b28e797
publickey compressed: 035f149fb58e6eb5a7bcc812c1f72e5c9a3ee7ea151991a31a4d3e98e5ace25568
public address compressed 16Ln3w1JLD8s7rNni37A7SiVmVPHXEHFWF
publickey uncompressed: 045f149fb58e6eb5a7bcc812c1f72e5c9a3ee7ea151991a31a4d3e98e5ace2556850fc0b339809eda8cc9f74ee698ed018049b0cfbefe72e66dbc256622f1662d7
public address uncompressed 1NKUnTCtN3Pbm3wEVsFZFuotKkjPPi4g3B
```

## calculatefrompublickey

A easy way to check the address from a publickey

What is the objetive of this tool? just check wha are the address from a publickey.

Example of use

```./calculatefromkey ca12010ce2daaf02611d440acd42e8bc791881375c58197cf56995059b28e797```

Output:

```
address 16Ln3w1JLD8s7rNni37A7SiVmVPHXEHFWF
```

## keydivision

This is just a proof of concept program just to show how we can divide publickeys.

The result from exact divisions is the same like any decimal number but if the division is not exact the result will be in an unknow place in the curve

Example of use: Divide a publickey bewteen 4 three times in a row:


```./keydivision -p 03a301697bdfcd704313ba48e51d567543f2a182031efd6915ddc07bbcc4e16070 -n 3 -d 4```

Output:

```
03f694cbaf2b966c1cc5f7f829d3a907819bc70ebcc1b229d9e81bda2712998b10
02e80fea14441fb33a7d8adab9475d7fab2019effb5156a792f1a11778e3c0df5d
033ab6bde10cd3ac0cd06883fa66f0b0e3eb1309c0534b812286e2a30ca540db99
```

explanation:
the target publickey `03a301697bdfcd704313ba48e51d567543f2a182031efd6915ddc07bbcc4e16070` have privatekey `0x1000000000000000000000000000000`

each line of the output are the result of divide the publickey by 4 lets to check this example with keymath

## keymath

Keymath is basically an arithmetic calculator por publickeys with only four operations:

- addition
- subtration
- multiplication by an scalar number
- division by an scalar number

previous example:

```./keymath 03a301697bdfcd704313ba48e51d567543f2a182031efd6915ddc07bbcc4e16070 / 4```

output:
```Result: 03f694cbaf2b966c1cc5f7f829d3a907819bc70ebcc1b229d9e81bda2712998b10```


this is the first line of the keydivision example, their privatekey is `0x1000000000000000000000000000000` bewteen 4 equals to `0x400000000000000000000000000000`


```./keymath 03f694cbaf2b966c1cc5f7f829d3a907819bc70ebcc1b229d9e81bda2712998b10 / 4```

output:
```Result: 02e80fea14441fb33a7d8adab9475d7fab2019effb5156a792f1a11778e3c0df5d```

from here you can do your calculations by your self

### multiplication by an scalar number

The multiplication and the division only can be done with a number you can't multiply or divide two publickeys

```./keymath 033ab6bde10cd3ac0cd06883fa66f0b0e3eb1309c0534b812286e2a30ca540db99 x 64```


```Result: 03a301697bdfcd704313ba48e51d567543f2a182031efd6915ddc07bbcc4e16070```

## modmath

this is an arithmetic calculator but all operations are modulo N, this is Modulo `0xFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFEBAAEDCE6AF48A03BBFD25E8CD0364141`

Did you remember that i write before: "not exact the result will be in an unknow place in the curve" I lie, we can know more or less where those result are but even with this information we can't find values when we don't know the PrivateKey value.

```./modmath 1 / 2```

Output:

```Result: 7fffffffffffffffffffffffffffffff5d576e7357a4501ddfe92f46681b20a1```

```./modmath 3 / 2```

Output:

```Result: 7fffffffffffffffffffffffffffffff5d576e7357a4501ddfe92f46681b20a2```

if you see those privatekeys are sequentially, Lest to divide the publickeys of `1` and `3` by `2`

```./keymath 0279be667ef9dcbbac55a06295ce870b07029bfcdb2dce28d959f2815b16f81798 / 2```

output:
```Result: 0200000000000000000000003b78ce563f89a0ed9414f5aa28ad0d96d6795f9c63```


```./keymath 02f9308a019258c31049344f85f89d5229b531c845836f99b08601f113bce036f9 / 2```

output:
```Result: 02c62c910e502cb615a27c58512b6cc2c94f5742f76cb3d12ec993400a3695d413```

Know you can comprobate that the publickeys of `7fffffffffffffffffffffffffffffff5d576e7357a4501ddfe92f46681b20a1` and `7fffffffffffffffffffffffffffffff5d576e7357a4501ddfe92f46681b20a2` are

`0200000000000000000000003b78ce563f89a0ed9414f5aa28ad0d96d6795f9c63`
`02c62c910e502cb615a27c58512b6cc2c94f5742f76cb3d12ec993400a3695d413`

can you calcualte what is the publickey of `7fffffffffffffffffffffffffffffff5d576e7357a4501ddfe92f46681b20a0` with keymath?

```./keymath 0200000000000000000000003b78ce563f89a0ed9414f5aa28ad0d96d6795f9c63 - 1```

output:
```Result: 0300000000000000000000003b78ce563f89a0ed9414f5aa28ad0d96d6795f9c63```

## addr2rmd

This tool decode legacy address to rmd160 hashes

Example of use:

```./addr2rmd -i examples/unsolvedpuzzles.txt  -o examples/unsolvedpuzzles.rmd```

`-i` input file `-o` output file, if the -o option is omited the

