---
title: 动态规划
date: 2024-07-26 18:34:53
tags:
---

# 买卖股票的最佳时机（dp问题）

## 文章目录

- [0.问题概述](#0问题概述)
- [1.只能进行一次交易](#1只能进行一次交易)
- [2.可以进行无限次交易（含冷冻期）](#2可以进行无限次交易含冷冻期)
- [3.可以进行无限次交易（含手续费）](#3可以进行无限次交易含手续费)
- [4.可以进行两次交易](#4可以进行两次交易)
- [5.可以进行k次交易](#5可以进行k次交易)
- [6.总结](#6总结)
  
### 0.问题概述

给定一段时间内股票的价格，求解如何买卖股票可以获得最大收益

这是一类典型的动态规划问题

### 1.只能进行一次交易
<!-- 1 -->
[原题链接](https://leetcode.cn/problems/best-time-to-buy-and-sell-stock/description/)

[返回目录](#文章目录)

#### **题目描述：**

给定一个数组 **prices** ，它的第 **i** 个元素 **prices[i]** 表示一支给定股票第 **i** 天的价格。

你只能选择 某一天 买入这只股票，并选择在 未来的某一个不同的日子 卖出该股票。

设计一个算法来计算你所能获取的最大利润。

返回你可以从这笔交易中获取的最大利润。如果你不能获取任何利润，返回 **0** 。

#### **分析：**

交易股票过程中，当我们想卖出股票时，我们总会想在这之前以最低价格买入股票，

假设第**i**天我们卖出股票，在什么情况下收益最大呢，

只要我们在前**i-1**天的最低点买入这支股票，这样在第i天卖出时就能保证获得最大利润，

因此我们可以定义一个变量**minprice**来记录前i-1天股票的最低价格，

**maxprofit**来记录在第i天卖出股票的最大收益，

接下来遍历price数组，更新最低价格和最大利润：
**maxprofit=max(maxprofit,price[i]-minprofit)**
**minprice=min(minprice,price[i])**

这样就能求得最大利润

```cpp
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int n=prices.size();
        int minprice = INT_MAX, maxprofit = 0;
        for (int i=0;i<n;++i) {
            maxprofit = max(maxprofit, price[i] - minprice);
            minprice = min(price[i], minprice);
        }
        return maxprofit;
    }
};

```
<!-- 1 -->

### 2.可以进行无限次交易（含冷冻期）
<!-- 2 -->
[原题链接](https://leetcode.cn/problems/best-time-to-buy-and-sell-stock-with-cooldown/description/)
#### **题目描述：**

[返回目录](#文章目录)

给定一个整数数组prices，其中第  prices[i] 表示第 i 天的股票价格 。​

设计一个算法计算出最大利润。在满足以下约束条件下，你可以尽可能地完成更多的交易（多次买卖一支股票）:

卖出股票后，你无法在第二天买入股票 (即冷冻期为 1 天)。

**注意**：你不能同时参与多笔交易（你必须在再次购买前出售掉之前的股票）。

#### **分析：**

**重点**:因为可以买卖多次股票，所以不能只用一个变量来记录最大收益，

应该使用动态规划，即假设前i-1天的最大收益已知，

则可通过比较求得前i天的最大收益。

建立状态机并对第i天的情况进行分类讨论，然后进行动态规划

在交易股票过程中，有三种状态：

**持有股票、没有股票且不处于冷冻期、没有股票且处于冷冻期，**

本题求解的是最大利润，故我们可以用一个二维数组**dp**

来存储每天结束后持有股票和没有股票的最大利润，

其中dp[i][0]表示第i天结束后持有股票情况下的最大利润，

dp[i][1]表示第i天结束后没有股票且处于冷冻期的最大利润，

dp[i][2]表示第i天结束后没有股票且不处于冷冻期的最大利润，

若第i天持有股票：之前没有股票买了今天的股票或之前就有股票
dp[i][0] = max(dp[i - 1][0], dp[i - 1][2] - prices[i])

第i天没有股票且处于冷冻期：昨天有股票，今天把股票卖了
dp[i][1] = dp[i - 1][0] + prices[i]

第i天没有股票且不处于冷冻期：前一天可能处于冷冻期也可能不处于
dp[i][2] = max(dp[i - 1][1], dp[i - 1][2])

```cpp
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int days = prices.size();
        if (days <= 1)
            return 0;
        vector<vector<int>>dp(days, {0,0,0});
        //初始化第0天的情况
        dp[0][0] = -prices[0];
        dp[0][1] = 0;
        dp[0][2] = 0;
        for (int i = 1; i < days; ++i)
        {
            dp[i][0] = max(dp[i - 1][0], dp[i - 1][2] - prices[i]);
            dp[i][1] = dp[i - 1][0] + prices[i];
            dp[i][2] = max(dp[i - 1][1], dp[i - 1][2]);
        }
        return max(max(dp[days - 1][0], dp[days - 1][1]), dp[days - 1][2]);
    }
};
```

<!-- 2 -->

### 3.可以进行无限次交易（含手续费）
<!-- 3 -->
[原题链接](https://leetcode.cn/problems/best-time-to-buy-and-sell-stock-with-transaction-fee/)

[返回目录](#文章目录)

#### **题目描述：**

给定一个整数数组 prices，其中 prices[i]表示第 i 天的股票价格 ；整数 fee 代表了交易股票的手续费用。

你可以无限次地完成交易，但是你每笔交易都需要付手续费。如果你已经购买了一个股票，在卖出它之前你就不能再继续购买股票了。

返回获得利润的最大值。

注意：这里的一笔交易指买入持有并卖出股票的整个过程，每笔交易你只需要为支付一次手续费。

#### **分析:**
本题与上一题没有本质区别，只是多了手续费且没有冷冻期，

所以只有两个状态：持有股票和没有股票

第i天没有股票的最大利润和持有股票的最大利润分别用dp[i][0]和dp[i][1]表示

```cpp
class Solution {
public:
    int maxProfit(vector<int>& prices, int fee) {
        int days = prices.size();
        if (days <= 1)
            return 0;
        vector<vector<int>>dp(days,{0,0});
        dp[0][0] = 0;
        dp[0][1] = -prices[0];
        for (int i = 1; i < days; ++i)
        {
            dp[i][0] = max(dp[i - 1][0], dp[i - 1][1] + prices[i] - fee);
            dp[i][1] = max(dp[i - 1][1], dp[i - 1][0] - prices[i]);
        }
        return max(dp[days-1][0], dp[days-1][1]);
    }
};
```
<!-- 3 -->

### 4.可以进行两次交易
<!-- 4 -->
[原题链接](https://leetcode.cn/problems/best-time-to-buy-and-sell-stock-iii/)

[返回目录](#文章目录)

#### **题目描述：**

给定一个数组，它的第 i 个元素是一支给定的股票在第 i 天的价格。

设计一个算法来计算你所能获取的最大利润。你最多可以完成 两笔 交易。

**注意**：你不能同时参与多笔交易（你必须在再次购买前出售掉之前的股票）。

#### **分析:**
本题的难点在于可以进行两次交易，所以状态机数量增多，第i天共有5个状态：

0.从来没有买卖过股票，用dp[i][0]表示（始终为0）

1.持有一张股票，从没有卖过，用dp[i][1]表示

2.持有并卖出一张股票，用dp[i][2]表示

3.持有一张股票，之前持有并卖出过一张股票，用dp[i][3]表示

4.持有并卖出过两张股票，用dp[i][4]表示

将五个状态列出来之后，各状态之间的关系是不是瞬间清晰明了了，

接下来我就不分析了，直接进入代码

```cpp
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int days = prices.size();
        if (days <= 1)return 0;
        vector<vector<int>>dp(days,vector<int>(5,INT_MIN));
        dp[0][1] = -prices[0];
        for (int i = 1; i < days; ++i)
        {
            dp[i][1] = max(dp[i-1][0] - prices[i], dp[i - 1][1]);
            dp[i][2] = max(dp[i][1] + prices[i], dp[i - 1][2]);
            if(i>=2)
                dp[i][3] = max(dp[i-1][2] - prices[i], dp[i - 1][3]);
            if(i>=3)
                dp[i][4] = max(dp[i][3] + prices[i], dp[i - 1][4]);
        }
        int max_profits = 0;
        for (int i = 0; i < 5; ++i)
            if (dp[days-1][i] > max_profits)
                max_profits = dp[days-1][i];
        return  max_profits;
    }
};
```
<!-- 4 -->

### 5.可以进行k次交易
<!-- 5 -->
[原题链接](https://leetcode.cn/problems/best-time-to-buy-and-sell-stock-iv/)

[返回目录](#文章目录)

#### **题目描述：**

给你一个整数数组 prices 和一个整数 k ，其中 prices[i] 是某支给定的股票在第 i 天的价格。

设计一个算法来计算你所能获取的最大利润。你最多可以完成 k 笔交易。也就是说，你最多可以买 k 次，卖 k 次。

注意：你不能同时参与多笔交易（你必须在再次购买前出售掉之前的股票）。

#### **分析：**

经过上一题的解析，我们注意到，每增加一个买卖次数都会产生两种新状态，

所以很容易想到k次交易总共有**2*k+1**种状态

其中dp[i][2*x]表示买卖过x次股票，当前没有股票

dp[i][2*x+1]表示持有一张股票，之前买卖过x次股票

接下来问题就变得简单了，只需要对天数遍历的过程，再对状态机遍历，

就能得到每种状态下的最大利润。

话不多说，上代码
```cpp
class Solution {
public:
    int maxProfit(int k, vector<int>& prices) {
        int days = prices.size();
        if (days <= 1)return 0;
        vector<vector<int>>dp(days, vector<int>(2 * k + 1, INT_MIN));
        for (int i = 0; i < 2*k + 1; ++i)
            dp[i][0] = 0;
        dp[0][1] = -prices[0];

        for (int i = 1; i < days; ++i){
            for (int j = 1; j <= i + 1; ++j){//第i天最多买卖i+1次
                if (j % 2 == 1)//持有股票
                    dp[i][j] = max(dp[i - 1][j - 1] - prices[i], dp[i - 1][j]);
                else//没有股票
                    dp[i][j] = max(dp[i][j - 1] + prices[i], dp[i - 1][j]);
            }
        }
        int max_profits = 0;
        for (int i = 0; i < 2 * k + 1; ++i)
            if (dp[days - 1][i] > max_profits)
                max_profits = dp[days - 1][i];
        return  max_profits;
    }
};
```
<!-- 5 -->

### 6.总结
<!-- 6 -->

[返回目录](#文章目录)

动态规划是一类经典问题

它通常用于解决具有重叠子问题和最优子结构性质的问题，

通过将问题分解为相互依赖的子问题，

然后通过求解这些子问题的最优解来得到原问题的最优解。

以下是动态规划问题的一般特征和解决步骤的总结：

**最优子结构**：问题的最优解包含了其子问题的最优解。通过求解子问题的最优解，可以得到原问题的最优解。

**重叠子问题**：问题可以被分解为许多相同的子问题。为了避免重复计算，可以使用记忆化搜索或自底向上的方法来存储已解决的子问题的解，以便以后使用。

**状态转移方程**：动态规划问题通常可以通过状态转移方程来描述，即当前状态的解如何依赖于之前状态的解。

**自底向上或自顶向下**：解决动态规划问题的方法通常分为自底向上和自顶向下两种。自底向上是从最底层的子问题开始逐步求解，直到得到原问题的解；自顶向下是从原问题开始，通过递归地求解子问题来得到解。

**状态压缩**：在一些问题中，可以通过状态压缩来减少空间复杂度。这意味着只存储必要的状态，而不是全部状态。

**边界条件**：在解决动态规划问题时，需要注意边界条件，即最小的子问题可能需要特殊处理。

在本类股票交易问题中，求第i天最大利润的子问题即为前i-1天的最大利润，

一旦找到与子问题的关系，列出状态转移方程，这类问题就能够迎刃而解

<!-- 6 -->