# answer
开发工程师测试题
## 一
###
import pandas as pd
import requests


def getRes(page):
    headers = {
        'Accept': 'application/json, text/javascript, */*; q=0.01',
        'Accept-Language': 'zh-CN,zh;q=0.9,en;q=0.8,en-GB;q=0.7,en-US;q=0.6',
        'Cache-Control': 'no-cache',
        'Connection': 'keep-alive',
        'Content-Type': 'application/x-www-form-urlencoded; charset=UTF-8',
        'Origin': 'https://iftp.chinamoney.com.cn',
        'Pragma': 'no-cache',
        'Referer': 'https://iftp.chinamoney.com.cn/english/bdInfo/',
        'Sec-Fetch-Dest': 'empty',
        'Sec-Fetch-Mode': 'cors',
        'Sec-Fetch-Site': 'same-origin',
        'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/127.0.0.0 Safari/537.36 Edg/127.0.0.0',
        'X-Requested-With': 'XMLHttpRequest',
        'sec-ch-ua': '"Not)A;Brand";v="99", "Microsoft Edge";v="127", "Chromium";v="127"',
        'sec-ch-ua-mobile': '?0',
        'sec-ch-ua-platform': '"Windows"',
    }

    data = {
        'pageNo': page,
        'pageSize': '15',
        'isin': '',
        'bondCode': '',
        'issueEnty': '',
        'bondType': '100001',
        'couponType': '',
        'issueYear': '2023',
        'rtngShrt': '',
        'bondSpclPrjctVrty': '',
    }

    response = requests.post('https://iftp.chinamoney.com.cn/ags/ms/cm-u-bond-md/BondMarketInfoListEN', headers=headers,
                             data=data)
    return response.json()


if __name__ == '__main__':
    result = []
    for i in range(1, 9):
        print("正在爬取{}页".format(i))
        res = getRes(page=i)
        result += res['data']['resultList']
    df = pd.DataFrame(result)
    print(df)
    df = df[['isin', 'bondCode', 'entyFullName', 'bondType', 'issueStartDate', 'debtRtng']]
    df.to_csv("result.csv", index=False,
              header=['ISIN', 'Bond Code', 'Issuer', 'Bond Type', 'Issue Date', 'Latest Rating'])

###
## 二
###
import re

def reg_search(text, regex_list):
    result = []
    for regex_dict in regex_list:
        temp_result = {}
        for key, pattern in regex_dict.items():
            matches = re.findall(pattern, text)
            if matches:
                temp_result[key] = matches
        if temp_result:
            result.append(temp_result)
    return result

text = '''
标的证券：本期发行的证券为可交换为发行人所持中国长江电力股份有限公司股票（股票代码：600900.SH，股票简称：长江电力）的可交换公司债券。
换股期限：本期可交换公司债券换股期限自可交换公司债券发行结束之日满12个月后的第一个交易日起至可交换债券到期日止，即2023年6月7日至2027年6月3日止。
'''

regex_list = [{
    '标的证券': '股票代码：(.*?)\.SH',
    '换股期限': '即(.*?)至(.*?)止'
}]

print(reg_search(text, regex_list))

###
