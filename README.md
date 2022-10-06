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

- [calculatefromkey](https://github.com/CookLand/ecctools#calculatefromkey)
- [calculatefrompublickey](https://github.com/CookLand/ecctools#calculatefrompublickey)
- [keymath](https://github.com/CookLand/ecctools#keymath)
- [modmath](https://github.com/CookLand/ecctools#modmath)

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


