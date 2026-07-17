---
{"dg-publish":true,"permalink":"/AtCoder/AtCoder Beginner Contest 413 Solution/","dg-note-properties":{}}
---

## A
直接计算总和并比较。
## B
直接处理出所有拼接出来的字符串丢进 `set` 里面输出大小。
## C
链表里面存某个数的重复次数和值，弹出的时候算乘法。
## D
将所有元素按绝对值升序排序，然后直接比较比值。比值比较建议转换为乘法避免精度问题。注意特判 $r=-1$ 的情况。
## E
从最大层级开始向下枚举，若最小值在右边就翻转。最终结果即为答案。
## F
可以从一个点走到其中一个终点当且仅当从该点出发存在至少两个方向能保证走到终点或者这个点本身就是终点。按照该思路从终点出发逆向广搜即可。
## G
不可达转化为右上墙壁与左下墙壁连通。直接使用并查集处理连通性即可。注意这里的墙壁是八连通的。
原题：[CodeChef Starters 121 / CHEFESCAPE](https://www.codechef.com/START121C/problems/CHEFESCAPE)