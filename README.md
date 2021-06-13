import requests
import csv
import time
keyword='定期报告'


template='http://query.sse.com.cn//search/getSearchResult.do?search=qwjs&page={page}&searchword={keyword}&orderby=-CRELEASETIME&perpage=10&_=1623505118853'
url=template.format(page=1,keyword=keyword)

headers={'User-Agent':'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_14_6) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/91.0.4472.77 Safari/537.36',
        'Referer':'http://www.sse.com.cn/'}

cookies={'Cookie':'yfx_c_g_u_id_10000042=_ck21061221375718471618310680774; yfx_f_l_v_t_10000042=f_t_1623505077833__r_t_1623505077833__v_t_1623505077833__r_c_0; yfx_mr_10000042=%3A%3Amarket_type_free_search%3A%3A%3A%3Abaidu%3A%3A%3A%3A%3A%3A%3A%3Awww.baidu.com%3A%3A%3A%3Apmf_from_free_search; yfx_mr_f_10000042=%3A%3Amarket_type_free_search%3A%3A%3A%3Abaidu%3A%3A%3A%3A%3A%3A%3A%3Awww.baidu.com%3A%3A%3A%3Apmf_from_free_search; yfx_key_10000042=; VISITED_MENU=%5B%228307%22%5D'}

with open ('上海report.csv','w',encoding='utf-8',newline='') as csvf:
    fieldnames=['title','date','link']
    writer=csv.DictWriter(csvf,fieldnames=fieldnames)
    writer.writeheader()
    
    resp=requests.get (url,headers=headers,cookies=cookies)
    max_page_num=int(resp.json()['countPage'])
    
    for page in range(1,max_page_num+1):
        url=template.format(page=page,keyword=keyword)
        resp=requests.get (url,headers=headers,cookies=cookies)
        for company in resp.json()['data']:
            row=dict()
            row['title']=company['CTITLE_TXT']
            row['date']=company['CRELEASETIME']
            row['link']= 'http://www.sse.com.cn'+company['CURL']
            writer.writerow(row)
            print(row)
        
        time.sleep(1)
        
