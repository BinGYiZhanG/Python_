```
import requests
from lxml import etree



class lpl_match(object):
    
    def __init__(self):
        self.headers={
                     'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/75.0.3770.142 Safari/537.36'
                      }
        self.url='http://lol.duowan.com/LPL/'
        self.score=[]
        self.matches=[]
        
    def get_info(self):
        r=requests.get(url=self.url,headers=self.headers)
        tree=etree.HTML(r.text)
        # 对阵情况
        vs=tree.xpath('//div[@class="match-container"]/div[@class="title"]/text()')
        # 对战时间
        dates=tree.xpath('//div[@class="match-container"]/div[@class="info"]/div[@class="date"]/text()')
        # 比赛状态
        status=tree.xpath('//div[@class="match-container"]/div[@class="info"]/div[contains(@class,"status")]/text()')
        # 比分
        scores=tree.xpath('//div[@class="score"]/span[contains(@class,"t")]/text()')
        # 视频链接
        vedio=tree.xpath('//div[@class="team-btn-wrap"]/a[1]/@href')  
        # 比赛报道
        report=tree.xpath('//div[@class="team-btn-wrap"]/a[2]/@href') 
        
        return vs,dates,status,scores,vedio,report
#score=tree.xpath('string(//div[@class="score"])')
#score_left=tree.xpath('//div[@class="score"]/span[@class="left"]/text() | //div[@class="score"]/span[@class="left light"]/text() | //div[@class="score"]/span[@class="left "]/text()')
#score_right=tree.xpath('//div[@class="score"]/span[@class="right light"]/text() | //div[@class="score"]/span[@class="right"]/text() | //div[@class="score"]/span[@class="right "]/text()')

#score_left=tree.xpath('//div[@class="score"]/span[contains(@class,"left")]/text()')
#score_right=tree.xpath('//div[@class="score"]/span[contains(@class,"right")]/text()')
    def process_info(self):
        
        vs,dates,status,scores,vedio,report=self.get_info()
       
        
        score_left=scores[::2] # 从0开始 隔2选取，也就是说选取下标为0,2,4,6,8,10,...
        score_right=scores[1::2] # 从1开始 隔2选取，也就是说选取下标为1,3,5,7,9,...
        for i in range(len(score_left)):
            self.score.append(str(score_left[i])+':'+str(score_right[i]))
        
        date=dates[::2] # 读取dates的下标是0,2,4,6偶数坐标是日期，
        time=dates[1::2] # 读取dates的下标是1,3,5,7奇数坐标是比赛时间
        # print(dates)
        
        for i in range(len(score_left)):
            match={
                  'title':vs[i],
                  'date':date[i],
                  'time':time[i],
                  'score':self.score[i],
                  'status':status[i],
                  'vedio':vedio[i],
                  'report':report[i]
           }
            self.matches.append(match)
        
            
if __name__=='__main__':
    
    lpl=lpl_match()
    lpl.process_info()  
    for match in lpl.matches:
        print(match)         
    # print(lpl.matches)

```


* 将带有汉字的字典输出到txt文件
```
import json

if __name__=='__main__':
    
    lpl=lpl_match()
    lpl.process_info()           
    print(lpl.matches)
    # dict_num = json.loads(lpl.matches)   
    with open("test.txt","w") as f:
        for line in lpl.matches:
            cline = json.dumps(line,sort_keys=True, indent=4, ensure_ascii=False)
            f.write(cline)
```

* python读写字典到文件的汉字兼容
```
#coding=utf-8

import codecs
import json

def load_json(json_file="test.json"):
    with open(json_file, "r") as f:
        return eval(f.read())

def save_json(json_dict, json_file="test.json"):
    with codecs.open(json_file, "w") as f:
        j = json.dumps(json_dict, sort_keys=True, indent=4, ensure_ascii=False)
        f.write(j)

if __name__ == "__main__":
    j = {"哈哈":1}
    save_json(j)
    print load_json().keys()[0]

```















