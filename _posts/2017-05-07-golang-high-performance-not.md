---
published: true
title: golang高性能取反操作
---
在写rkproxy这个项目的时候，想做一个高性能的数据流传输，做一个简单的数据流“封装”（数据全部取反0010变成1101）。

我觉得这样的数据从墙流过时，不会被检测到，并且速度快（客户端和服务端都不需要吃过多的cpu来加密解密）。

所以写一个高性能的取反方法，就显得异常重要。

其实开始就写过好几个不同的方法实现，但是性能都几乎没有差别。

所以这里就只写典型的第一版和最后一版的代码了。

### 第一版的取反代码

buff = 10000+ byte字符串

```
		l := len(buff)
		for i := 0; i < l; i++ {
			*(&buff[i]) ^= 0xFF
		}
```

第一版的代码最简单，看似问题不大，但是for会循环非常多次，这种是非常慢的（即便如此，应该还是比那些加密解密的快~）

### 最后的优化

思路是如何减少for循环。golang中可以取反的最大的变量类型是uint64，所以：

```
		for i := 0; i < len(buff); i += 8 {
			if i+8 > len(buff) {
				for x := i; x < len(buff); x++ {
					*(&buff[x]) ^= 0xFF
				}
				break
			}
			buf := buff[i : i+8]
			uint64buf := uint64(binary.LittleEndian.Uint64(buf))
			uint64buf ^= 0xFFFFFFFFFFFFFFFF

			binary.LittleEndian.PutUint64(buf, uint64(uint64buf))
		}
```

每次处理8个byte，理论上速度回提升8倍，但是最后的性能测试却只提升了2倍，我估计转换操作这块浪费了比较多的cpu

