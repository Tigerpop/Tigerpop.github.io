---
layout: posts
title: "Jack-Cui基于RSRS的量化代码"
date: 2021-9-27 20:00:36
description: "这是文章开头，显示在主页面，详情请点击此处。"
categories: 
- "浙大算法与数据结构课程(python实现)"
tags:
- "量化"
---
'''
策略思路：
选股：财务指标选股
择时：RSRS择时
持仓：有开仓信号时持有10只股票，不满足时保持空仓

'''
# 导入函数库
import statsmodels.api as sm
from pandas.stats.api import ols


# 初始化函数，设定基准等等
def initialize(context):
    # 开启动态复权模式(真实价格)
    set_option('use_real_price', True)
    # 过滤掉order系列API产生的比error级别低的log
    # log.set_level('order', 'error')
    set_parameter(context)
    ### 股票相关设定 ###
    # 股票类每笔交易时的手续费是：买入时佣金万分之三，卖出时佣金万分之三加千分之一印花税, 每笔交易佣金最低扣5块钱
    set_order_cost(OrderCost(close_tax=0.001, open_commission=0.0003, close_commission=0.0003, min_commission=5),
                   type='stock')

    ## 运行函数（reference_security为运行时间的参考标的；传入的标的只做种类区分，因此传入'000300.XSHG'或'510300.XSHG'是一样的）
    # 开盘前运行
    run_daily(before_market_open, time='before_open', reference_security='000300.XSHG')
    # 开盘时运行
    run_daily(market_open, time='open', reference_security='000300.XSHG')
    # 收盘后运行
    # run_daily(after_market_close, time='after_close', reference_security='000300.XSHG')


'''
==============================参数设置部分================================
'''


def set_parameter(context):
    # 设置RSRS指标中N, M的值
    # 统计周期
    g.N = 18
    # 统计样本长度
    g.M = 1100
    # 首次运行判断
    g.init = True
    # 持仓股票数
    g.stock_num = 10
    # 风险参考基准
    g.security = '000300.XSHG'
    # 设定策略运行基准
    set_benchmark(g.security)
    # 记录策略运行天数
    g.days = 0
    # set_benchmark(g.stock)
    # 买入阈值
    g.buy = 0.7
    g.sell = -0.7
    # 用于记录回归后的beta值，即斜率
    g.ans = []
    # 用于计算被决定系数加权修正后的贝塔值
    g.ans_rightdev = []

    # 计算2005年1月5日至回测开始日期的RSRS斜率指标
    prices = get_price(g.security, '2005-01-05', context.previous_date, '1d', ['high', 'low'])
    highs = prices.high
    lows = prices.low
    g.ans = []
    for i in range(len(highs))[g.N:]:
        data_high = highs.iloc[i - g.N + 1:i + 1]
        data_low = lows.iloc[i - g.N + 1:i + 1]
        X = sm.add_constant(data_low)
        model = sm.OLS(data_high, X)
        results = model.fit()
        g.ans.append(results.params[1])
        # 计算r2
        g.ans_rightdev.append(results.rsquared)


## 开盘前运行函数
def before_market_open(context):
    # 输出运行时间
    # log.info('函数运行时间(before_market_open)：'+str(context.current_dt.time()))
    g.days += 1
    # 给微信发送消息（添加模拟交易，并绑定微信生效）
    send_message('策略正常，运行第%s天~' % g.days)


## 开盘时运行函数
def market_open(context):
    security = g.security
    # 填入各个日期的RSRS斜率值
    beta = 0
    r2 = 0
    if g.init:
        g.init = False
    else:
        # RSRS斜率指标定义
        prices = attribute_history(security, g.N, '1d', ['high', 'low'])
        highs = prices.high
        lows = prices.low
        X = sm.add_constant(lows)
        model = sm.OLS(highs, X)
        beta = model.fit().params[1]
        g.ans.append(beta)
        # 计算r2
        r2 = model.fit().rsquared
        g.ans_rightdev.append(r2)

    # 计算标准化的RSRS指标
    # 计算均值序列
    section = g.ans[-g.M:]
    # 计算均值序列
    mu = np.mean(section)
    # 计算标准化RSRS指标序列
    sigma = np.std(section)
    zscore = (section[-1] - mu) / sigma
    # 计算右偏RSRS标准分
    zscore_rightdev = zscore * beta * r2

    # 如果上一时间点的RSRS斜率大于买入阈值, 则全仓买入
    if zscore_rightdev > g.buy:
        # 记录这次买入
        log.info("市场风险在合理范围")
        # 满足条件运行交易
        trade_func(context)
    # 如果上一时间点的RSRS斜率小于卖出阈值, 则空仓卖出
    elif (zscore_rightdev < g.sell) and (len(context.portfolio.positions.keys()) > 0):
        # 记录这次卖出
        log.info("市场风险过大，保持空仓状态")
        # 卖出所有股票,使这只股票的最终持有量为0
        for s in context.portfolio.positions.keys():
            order_target(s, 0)


# 策略选股买卖部分
def trade_func(context):
    # 获取股票池
    df = get_fundamentals(query(valuation.code, valuation.pb_ratio, indicator.roe))
    # 进行pb,roe大于0筛选
    df = df[(df['roe'] > 0) & (df['pb_ratio'] > 0)].sort('pb_ratio')
    # 以股票名词作为index
    df.index = df['code'].values
    # 取roe倒数
    df['1/roe'] = 1 / df['roe']
    # 获取综合得分
    df['point'] = df[['pb_ratio', '1/roe']].rank().T.apply(f_sum)
    # 按得分进行排序，取指定数量的股票
    df = df.sort('point')[:g.stock_num]
    pool = df.index
    log.info('总共选出%s只股票' % len(pool))
    # 得到每只股票应该分配的资金
    cash = context.portfolio.total_value / len(pool)
    # 获取已经持仓列表
    hold_stock = context.portfolio.positions.keys()
    # 卖出不在持仓中的股票
    for s in hold_stock:
        if s not in pool:
            order_target(s, 0)
    # 买入股票
    for s in pool:
        order_target_value(s, cash)


# 打分工具
def f_sum(x):
    return sum(x)


## 收盘后运行函数
def after_market_close(context):
    # 得到当天所有成交记录
    trades = get_trades()
    for _trade in trades.values():
        log.info('成交记录：' + str(_trade))
    # 打印账户总资产
    log.info('今日账户总资产：%s' % round(context.portfolio.total_value, 2))
    # log.info('##############################################################')