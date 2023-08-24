---
title: "Threading"
date: 2023-08-15T16:20:01+08:00
tags: [python]
---


```
"""
http_proxy=http:... https_proxy=http:... /opt/.pyenv/versions/py39/bin/python t.py
"""
import os
import json
import math
import logging
from threading import Thread, Lock


import pandas as pd
from googletrans import Translator
from httpcore._exceptions import ConnectError, ConnectTimeout, ReadTimeout


logging.basicConfig(level=logging.INFO)
ROOT = os.path.dirname(os.path.abspath(__file__))


# 插件ID 漏洞名称 漏洞描述  解决办法 漏洞来源 漏洞编号 漏洞评分 CVSS 漏洞级别 公开时间 漏洞类型
class TrExec:
    def __init__(self, filename, jobs=3):
        self.jobs = jobs
        self.filename = filename
        self.data_frame = None  # pandas.core.frame.DataFrame
        self.rows = 0
        self.headers = []
        self.lock = Lock()
        self.ferror = open(f'{ROOT}/ttt/err.txt', 'w')

    def read_xlsx(self):
        self.data_frame = data_frame = pd.read_excel(self.filename, sheet_name=0, usecols=list(range(3, 11)), engine='openpyxl')
        self.headers = data_frame.keys()
        self.rows = len(data_frame)

    def _run(self, idx, row_start, row_stop):
        print(idx, row_start, row_stop)
        translator = Translator()
        fjson = open(f'{ROOT}/ttt/{row_start:05d}-{row_stop:05d}.json', 'wb')
        fjson.write(b'[\n')
        for row in range(row_start, row_stop):
            logging.info('【%05d】', row)
            dct = {"row": row}
            flag = True
            for header in self.headers:
                # print('【{:05d}】:{:-^40}'.format(row, header))
                content = self.data_frame[header][row]
                if header in ['漏洞描述', '解决办法']:
                    if isinstance(content, float) and math.isnan(content):
                        text = ''
                    else:
                        try:
                            to = translator.translate(content, src='en', dest='zh-cn')
                        except (ConnectError, ConnectTimeout, ReadTimeout) as e:
                            logging.warning("%s", e)
                            flag = False
                        else:
                            text = to.text
                else:
                    if isinstance(content, float) and math.isnan(content):
                        text = ''
                    else:
                        text = content
                dct[header] = text
            # end iter headers
            if flag:
                byte_data = json.dumps(dct, ensure_ascii=False).encode("utf-8")
                fjson.write(byte_data)
                fjson.write(b',\n')
            else:
                self.lock.acquire()
                self.ferror.write(f'{row}\n')
                self.lock.release()
        # end iter rows
        fjson.seek(-2, os.SEEK_CUR)
        fjson.write(b'\n]\n')
        fjson.close()

    def run(self):
        threads = []
        rows_per_job = self.rows // self.jobs
        for i in range(self.jobs - 1):
            t = Thread(target=self._run, args=(i, i * rows_per_job, (i+1) * rows_per_job,))
            threads.append(t)
        t = Thread(target=self._run, args=(self.jobs - 1, (self.jobs - 1) * rows_per_job, self.rows,))
        threads.append(t)

        for t in threads:
            t.start()
        for t in threads:
            t.join()
        self.ferror.close()


if __name__ == '__main__':
    filename_ = "XXX.xlsx"
    trexec = TrExec(filename_, 30)
    trexec.read_xlsx()
    trexec.run()
```

