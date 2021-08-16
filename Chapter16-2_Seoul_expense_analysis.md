```python
# 실전 데이터(서울시 업무추진비) 분석
```


```python
    # Data 수집
```


```python
mkdir Chapter16_Seoul_Data
```


```python
import requests
import os
import pathlib


def get_seoul_expense_list(extension, year, data_folder): # 입력 변수 : 확장자, 연도, 내려받을 폴더
    expense_list_year_url = 'https://github.com/seoul-opengov/opengov/raw/master/expense_list{0}/'.format(str(year))
    
    expense_list_year_dir = data_folder + str(year) + '/'
    
    if (os.path.isdir(expense_list_year_dir)): # 존재하면 True 반환
        print('데이터 폴더({0})가 이미 있습니다. {0}년 데이터의 다운로드를 시작합니다.'.format(year))
    else:
        print('데이터 폴더({0})가 없어서 생성했습니다. {0}년 데이터의 다운로드를 시작합니다.'.format(year))
        pathlib.Path(expense_list_year_dir).mkdir(parents = True, exist_ok = True)
        
    for k in range(12):
        file_name = '{0}{1:02d}_expense_list.{2}'.format(year, k + 1, extension)
        url = expense_list_year_url + file_name
        print(url)
        r = requests.get(url)
        with open(expense_list_year_dir + file_name, 'wb') as f:
            f.write(r.content)
            
    print('{0}년 데이터의 다운로드가 완료되었습니다.'.format(year))
```


```python
extension = 'csv'
years = ['2017', '2018', '2019', '2020']
data_folder = 'Chapter16_Seoul_Data/seoul_expense/'

for year in years:
    get_seoul_expense_list(extension, year, data_folder)
    
```


```python
import glob


for year in years:
    
    path_name = 'Chapter16_Seoul_Data/seoul_expense/{0}/'.format(year)
    file_name_for_glob = path_name + '*list.csv'
    
    csv_files = []

    for csv_file in glob.glob(file_name_for_glob):
        csv_files.append(csv_file.split('/')[-1])
        csv_files.sort()

    print('[폴더 이름]', path_name)
    print('* CSV 파일:', csv_files)
    print('')
```


```python
print(file_name_for_glob)
glob.glob(file_name_for_glob)
```


```python
    # Data 처리
```


```python
data_file = 'Chapter16_Seoul_Data/seoul_expense/2017/201701_expense_list.csv'

with open(data_file, 'r', encoding = 'utf-8') as f:
    line1 = f.readline()
    line2 = f.readline()
    line3 = f.readline()
    
    print(line1) # csv 파일의 열 이름
    print(line2) # 2번째 줄부터는 Data 값들...
    print(line3)
```


```python
line1_len = len(line1.split(','))
line2_len = len(line2.split(','))
line3_len = len(line3.split(','))

print('[각 줄의 Data 값의 개수]')
print("첫째 줄:{}, 둘째 줄:{}, 셋째 줄:{}".format(line1_len, line2_len, line3_len))
```


```python
def change_csv_file_first_line_value(old_file_name, new_file_name):
    with open(old_file_name, 'r', encoding = 'utf-8') as f:
        lines = f.read().splitlines()
        
    lines[0] = 'nid,제목,url,부서레벨1,부서레벨2,부서레벨3,부서레벨4,부서레벨5,집행연도,집행월,예산,집행,구분,부서명,집행일시,집행장소,집행목적,대상인원,결제방법,집행금액'
    
    with open(new_file_name, 'w', encoding = 'utf-8') as f:
        f.write('\n'.join(lines))
```


```python
old_file_name = 'Chapter16_Seoul_Data/seoul_expense/2017/201701_expense_list.csv'

new_file_name = 'Chapter16_Seoul_Data/seoul_expense/2017/201701_expense_list_new.csv'

change_csv_file_first_line_value(old_file_name, new_file_name)
```


```python
for year in years:
    
    print("{}년 데이터의 첫 번째 줄의 열 이름을 변경해서 새 파일에 저장합니다.".format(year))
    
    for i in range(12):
        
        old_file_name = 'Chapter16_Seoul_Data/seoul_expense/{0}/{1}{2:02d}_expense_list.csv'.format(year, year, i + 1)
        new_file_name = 'Chapter16_Seoul_Data/seoul_expense/{0}/{1}{2:02d}_expense_list_new.csv'.format(year, year, i + 1)
        
        change_csv_file_first_line_value(old_file_name, new_file_name)
        
print("모든 데이터의 첫 번째 줄의 열 이름을 변경해서 새 파일로 저장했습니다.")

# 이건 내 머리에서 나온 방법인데,,,
```


```python
def change_year_csv_file_first_line_value(year, data_folder):
    
    expense_list_year_dir = data_folder + str(year) + '/'
    extension = 'csv'
    
    for k in range(12):
        
        old_file_name = expense_list_year_dir + '{0}{1:02d}_expense_list.{2}'.format(year, i + 1, extension)
        new_file_name = expense_list_year_dir + '{0}{1:02d}_expense_list_new.{2}'.format(year, i + 1, extension)
        
        change_csv_file_first_line_value(old_file_name, new_file_name)
        
        

# 함수 적용하는 부분

data_folder = 'Chapter16_Seoul_Data/seoul_expense/'
years = [2017, 2018, 2019, 2020]

for year in years:
    print('{}년 데이터의 첫 번째 줄의 열 이름을 변경해서 새 파일에 저장합니다.'.format(year))
    change_year_csv_file_first_line_value(year, data_folder)
    
print('모든 데이터의 첫 번재 줄의 열 이름을 변경해서 새 파일로 저장했습니다.')

# 이게 책에서 함수로 구현해서 만든 부분...!
```


```python
import glob


data_folder = 'Chapter16_Seoul_Data/seoul_expense/'
years = [2017, 2018, 2019, 2020]

for year in years:
    path_name = data_folder + str(year)
    print('[폴더 이름] {}'.format(path_name))
    
    new_csv_files = []
    
    file_name_for_glob = path_name + '/*_new.csv'
    
    for new_csv_file in glob.glob(file_name_for_glob):
        new_csv_files.append(new_csv_file.split('/')[-1])
        new_csv_files.sort()     
        
    print('* 새롭게 생성된 csv 파일:', new_csv_files)
    print('')
```


```python
    # Data의 구조 및 결측치 살펴보기
```


```python
import pandas as pd

expense_list2017_dir = 'Chapter16_Seoul_Data/seoul_expense/2017/'
file_name = '201701_expense_list_new.csv'

df = pd.read_csv(expense_list2017_dir + file_name)
```


```python
df.head(5)
```


```python
# 한 해 전체 Data를 DataFrame 형식으로 가져오는 코드

import pandas as pd

year = 2017
expense_list_year_dir = 'Chapter16_Seoul_Data/seoul_expense/{}/'.format(year)

df_year = pd.DataFrame()

for k in range(12):
    
    file_name = '{0}{1:02d}_expense_list_new.csv'.format(year, k + 1)
    
    df_month = pd.read_csv(expense_list_year_dir + file_name, encoding = 'utf-8')
    
    df_year = df_year.append(df_month, ignore_index = True)
```


```python
df_year.head(2)
```


```python
df_year.tail(2)
```


```python
df_year.info()
```


```python
df_year.isna().sum()
```


```python
df_year_drop = df_year.drop(columns = ['nid', 'url', '부서레벨3', '부서레벨4', 
                                       '부서레벨5', '예산', '집행', '구분'])

df_year_drop.head(2)
```


```python
year = 2017
expense_list_year_dir = 'Chapter16_Seoul_Data/seoul_expense/{}/'.format(year)

expense_list_tidy_file = '{}_expense_list_tidy.csv'.format(year)

df_year_drop.to_csv(expense_list_year_dir + expense_list_tidy_file, index = False)
```


```python
import os

file_name = expense_list_year_dir + expense_list_tidy_file
print(file_name)

os.path.isfile(file_name)
```


```python
# 연도 별로 원하는 열의 Data만 선택해서 새로운 파일로 저장하는 함수

import pandas as pd

def select_columns_save_file(year, data_folder, drop_columns_list):
    
    expense_list_year_dir = data_folder + '{}/'.format(year)
    expense_list_tidy_file = '{}_expense_list_tidy.csv'.format(year)
    
    df_year = pd.DataFrame()
    
    for k in range(12):
        
        file_name = '{0}{1:02d}_expense_list_new.csv'.format(year, k + 1)
        
        df_month = pd.read_csv(expense_list_year_dir + file_name, encoding = 'utf-8', index_col = False)
        
        df_year = df_year.append(df_month, ignore_index = True)
        
    df_year_drop = df_year.drop(columns = drop_columns_list)
    
    new_file_name = expense_list_year_dir + expense_list_tidy_file
    df_year_drop.to_csv(new_file_name, index = False)
    
    print('==> {} 파일을 생성했습니다.'.format(expense_list_tidy_file))
```


```python
data_folder = 'Chapter16_Seoul_Data/seoul_expense/'
years = [2017, 2018, 2019, 2020]
drop_columns_list = [
    'nid', 'url', '부서레벨3', '부서레벨4', '부서레벨5', '예산', '집행', '구분'
]


for year in years:
    
    print('{}년 Data를 정리해서 저장하고 있습니다.'.format(year))
    select_columns_save_file(year, data_folder, drop_columns_list)
    
print('모든 연도의 Data를 정리해서 파일로 저장했습니다.')
```


```python
df = pd.read_csv('Chapter16_Seoul_Data/seoul_expense/2020/2020_expense_list_tidy.csv')
df
```


```python
import os

years = [2017, 2018, 2019, 2020]

for year in years:
    expense_list_year_dir = data_folder + '{}/'.format(year)
    expense_list_tidy_file = '{}_expense_list_tidy.csv'.format(year)
    
    file_name = expense_list_year_dir + expense_list_tidy_file
    
    print(file_name, '==>', end = "")
    print(os.path.isfile(file_name))
```


```python
# 생성한 파일에 대한 상세한 정보를 알려주는 함수
# 파일 생성 시간, 파일 크기 등...
```


```python
import os
from datetime import datetime


def get_file_info(year, data_folder):
    
    expense_list_year_dir = data_folder + '{}/'.format(year)
    expense_list_tidy_file = '{}_expense_list_tidy.csv'.format(year)
    
    path_file_name = expense_list_year_dir + expense_list_tidy_file
    print(path_file_name)
    result = os.path.isfile(path_file_name)
    
    # 파일 수정 시간
    modified_time = datetime.fromtimestamp(os.path.getmtime(path_file_name))
    
    # 파일 생성 시간
    created_time = datetime.fromtimestamp(os.path.getctime(path_file_name))
    
    # 파일 크기
    file_size = os.path.getsize(path_file_name)
    
    if result == True:
        print('[생성한 CSV 데이터 파일의 정보]')
        print('* 폴더 위치 : {}'.format(expense_list_year_dir))
        print('* 파일 이름 : {}'.format(expense_list_tidy_file))
        print('* 수정 시간 : {:%Y-%m-%d %H:%M:%S}'.format(modified_time))
        print('* 생성 시간 : {}'.format(created_time.strftime('%Y-%m-%d %H:%M:%S')))
        print('* 파일 크기 : {0:,} 바이트'.format(file_size))
```


```python
years = [2017, 2018, 2019, 2020]
data_folder = 'Chapter16_Seoul_Data/seoul_expense/'

for year in years:
    get_file_info(year, data_folder)
    print('')
```


```python
    # Data 분석
```


```python
import pandas as pd


data_folder = 'Chapter16_Seoul_Data/seoul_expense/'
years = [2017, 2018, 2019, 2020]

df_expense_all = pd.DataFrame()

for year in years:
    expense_list_year_dir = data_folder + '{}/'.format(year)
    expense_list_tidy_file = '{}_expense_list_tidy.csv'.format(year)
    
    path_file_name = expense_list_year_dir + expense_list_tidy_file
    
    df_expense = pd.read_csv(path_file_name, low_memory = False, index_col = False)
    if year == 2020:
        for k, row in df_expense.iterrows():
            if df_expense.at[k, '부서레벨1'] == '사업소,':
                df_expense.at[k, '부서레벨1'] = '사업소'
    df_expense_all = df_expense_all.append(df_expense, ignore_index = True)
```


```python
df_expense_all.info()
```


```python
df_expense_all['부서레벨1'].value_counts()
```


```python
## 연도별 추이 분석
```


```python
### 연도별 업무추진비 진행 횟수

year_expense = df_expense_all['집행연도'].value_counts()
print(type(year_expense))
year_expense
```


```python
import pandas as pd
import matplotlib.pyplot as plt
import matplotlib


matplotlib.rcParams['font.family'] = 'UnDotum'
matplotlib.rcParams['axes.unicode_minus'] = False

plt.bar(year_expense.index, year_expense.values, tick_label = year_expense.index, width = 0.5)
plt.title("연도별 업무추진비 집행 횟수")
plt.xlabel("연도")
plt.ylabel("집행 횟수")
plt.show()
```


```python
from matplotlib import font_manager as fm

font_list = fm.findSystemFonts(fontpaths=None, fontext='ttf')
font_list
```


```python
### 집행연도 별 집행금액의 합
```


```python
import pandas as pd

year_total = pd.pivot_table(df_expense_all, index = ['집행연도'], values = ['집행금액'], aggfunc = 'sum')
year_total
```


```python
import pandas as pd
import matplotlib.pyplot as plt
import matplotlib


eok_won = 100000000

(year_total/eok_won).plot.bar(rot = 0)
plt.ylabel("집행금액(억원)")
plt.show()
```


```python
### 월별 집행금액 분석
```


```python
year_month_total = pd.pivot_table(df_expense_all, index = ['집행월'], columns = ['집행연도'], values = ['집행금액'], aggfunc = 'sum')
year_month_total
```


```python
import pandas as pd
import matplotlib.pyplot as plt
import matplotlib


eok_won = 100000000

(year_month_total/eok_won).plot.bar(rot = 0)
plt.title("업무추진비의 월별 집행금액")
plt.xlabel('집행월')
plt.ylabel("집행금액(억원)")
plt.legend(['2017년', '2018년', '2019년', '2020년'])
plt.show()
```


```python
### 부서별 집행 내역 분석
```


```python
dept_level1_total = pd.pivot_table(df_expense_all, index = ['부서레벨1'], values = ['집행금액'], aggfunc = 'sum')
dept_level1_total
```


```python
dept_level2_total = pd.pivot_table(df_expense_all, index = ['부서레벨2'], values = ['집행금액'], aggfunc = 'sum')
dept_level2_total
```


```python
dept_level2_total_top10 = dept_level2_total.sort_values(by = ['집행금액'], ascending = False)[0:10]
dept_level2_total_top10
```


```python
(dept_level2_total_top10 / eok_won).plot.bar(rot = 80)
plt.ylabel('집행금액(억원)')
plt.title('업무추진비 집행금액이 높은 상위 10개 부서')
plt.show()
```


```python
import matplotlib.pyplot as plt
from wordcloud import WordCloud

korean_font_path = 'NanumGothic.ttf'

wc = WordCloud(font_path = korean_font_path, background_color = 'white', width = 800, height = 600)
frequencies = dept_level2_total['집행금액']

wordcloud_image = wc.generate_from_frequencies(frequencies)

plt.figure(figsize = (12, 9))
plt.axis('off')
plt.imshow(wordcloud_image, interpolation = 'bilinear')
plt.show()
```


```python
### 요일별 및 시간대별 집행 내역 분석
```


```python
df_expense_all['집행일시'].values
```


```python
expense_date_time = pd.to_datetime(df_expense_all['집행일시'])
expense_date_time
```


```python
week_day_name = ['월', '화', '수', '목', '금', '토', '일']

df_expense_all['집행일시_요일'] = [week_day_name[weekday] for weekday in expense_date_time.dt.weekday]
```


```python
df_expense_all['집행일시_시간'] = [hour for hour in expense_date_time.dt.hour]
```


```python
df_expense_all.head(3)
```


```python
expense_weekday = df_expense_all['집행일시_요일'].value_counts()
expense_weekday
```


```python
expense_weekday = expense_weekday.reindex(index = week_day_name)
expense_weekday
```


```python
expense_weekday.plot.bar(rot = 0)
plt.title('요일별 업무추진비 집행 횟수')
plt.xlabel('집행 횟수')
plt.ylabel('요일')
plt.show()
```


```python
expense_hour_num = df_expense_all['집행일시_시간'].value_counts()
expense_hour_num
```


```python
work_hour = [ ((k + 8) % 24) for k in range(24)]

expense_hour_num = expense_hour_num.reindex(index = work_hour)
expense_hour_num
```


```python
expense_hour_num.plot.bar(rot = 0)
plt.title("시간별 업무추진비 집행 횟수")
plt.xlabel("집행 시간")
plt.ylabel("집행 횟수")
plt.show()
```


```python
expense_hour_total = pd.pivot_table(df_expense_all, index = ['집행일시_시간'], values = ['집행금액'], aggfunc = 'sum')
expense_hour_total
```


```python
eok_won = 100000000
expense_hour_total = expense_hour_total.reindex(index = work_hour)


(expense_hour_total / eok_won).plot.bar(rot = 0)
plt.title("시간대별 업무추진비 집행금액")
plt.xlabel("집행일시_시간")
plt.ylabel("집행금액(억원)")
plt.show()
```


```python

```
