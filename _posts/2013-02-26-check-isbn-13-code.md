---
title:  ISBN-13 条形码校验方法
date:   2013-02-26 20:05:08  +0800
---

算是小常识了吧，不过直到今天我才从 Cambridge IGCSE Computer Studies Coursebook 中学到。

每个 ISBN-13 的最后一位数字是用来校验其正确性的，所以前 12 位如果出错一位，计算后所得的数字可能就会和最后一位数不符。

Barcodes: 978-0-521-17063-5

我们把它拆分为独立的个体：

9 7 8 0 5 2 1 1 7 0 6 3，这里我们除去了最后一位数字 5。

每一个数字都有对应的权重，按顺序分别是：

9 7 8 0 5 2 1 1 7 0 6 3

1 3 1 3 1 3 1 3 1 3 1 3

然后，我们要把每个数字乘以其权重的和求出来。

9x1 + 7x3 + 8x1 + 0x3 + 5x1 + 2x3 + 1x1 + 1x3 + 7x1 + 0x3 + 6x1 + 3x3

= 75

再用所得到的数字除以十，获得其余数：

75/10 = 7 . . . 5

最后，用 10 减去余数，所得的数和 ISBN-13 的最后一位的数相等。

10 - 5 = 5

当然，可以有更快的捷径算出校验数字，比如算出序数为奇数的所有数字之和的个位数以及序数为偶数的所有数字之和的个位数，用 10 减去得出的 2 个数字之和的个位数所得的数字就是校验数了。