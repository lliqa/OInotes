# Test_20210323_Summary_LC

|      题目      |          circle          |          coins           | stone |
| :------------: | :----------------------: | :----------------------: | :---: |
| 期望/实际 用时 | $1\text{h}/45\text{min}$ | $ 1\text{h}30\text{min}$ |  $0$  |
| 期望/实际 得分 |          $60/0$          |         $55/45$          |  $0$  |

* 考试策略：
	* 顺序：**coins** -> **circle**



### **circle**

写了 $60\text{pts}$ 的做法，没想到细节错误一堆：数组名写错、循环边界 $1$ 写成 $2$

### **coins**

写的 $55\text{pts}$ 的主席树，又丢了几个细节。

改完后竟然有 $80\text{pts}$ ，不可思议。

### **stone**

没思路，弃了。



### 总结

本场考试挂分挂的实在是太严重了。细节方面以后一定要严谨考证。 **circle** 循环边界的错误是因为我想省个 $1$ 的常数，本来就是画蛇添足的做法还送掉了一堆分。以后一切以正确性优先。