在一个背包中，所有物品的重量和为$m$，问能不能从中挑出任意个，满足总重量和为$k$，如果存在3个相同重量的元素$a,a,a$，那么我们把这三个元素换成$a,2a$得到的效果是一样的，但元素的个数减少了。这样一直合并，可以将元素的个数减到$O(\sqrt m)$个，可以减小$DP$的复杂度。

类似的一个结论，如果所有元素和为$m$，那么值不同的元素最多$O(\sqrt m)$ 个。
