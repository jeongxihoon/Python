```python
# 예제 코드를 응용해서 만들어보는 대기오염 정보 알아보기 코드!

# 직접 몇몇 정보들을 input해서 사용자가 원하는 결과를 찾아내도록!
```


```python
import requests


API_KEY = 'E6t%2F1IVGnBJTGDEqgp7X1AReWmpC1lxBWMqgV7xyCT8fzaWCCyTwJhqu%2FD2YZPj8X%2B7Dxw3bnc0agwVjyzsQYA%3D%3D'
API_KEY_Decode = requests.utils.unquote(API_KEY)

req_url1 = 'http://apis.data.go.kr/B552584/MsrstnInfoInqireSvc/getTMStdrCrdnt'

ServiceKey = API_KEY_Decode
return_type = 'json'
umd_name = input('읍/면/동의 이름을 입력하십시오. >>> ')

req_parameter1 = {
    'serviceKey':ServiceKey,
    'returnType':return_type,
    'umdName':umd_name
}


r1 = requests.get(req_url1, params = req_parameter1)
dict_data1 = r1.json()

import sys

def introduce():
    total_count = dict_data1['response']['body']['totalCount']
    print("\n입력한 읍/면/동 이름 : {0} \n".format(umd_name))
    if total_count == 0:
        sys.exit("[ 검색된 결과가 없습니다. ]")
    else:
        print("[ 검색된 결과 : 총 {0}건 ] \n ".format(total_count))
    for k in range(total_count):
        station_data = dict_data1['response']['body']['items'][k]
        print('[ {0} ]'.format(k + 1))
        print('[주소] : {0} {1} \n'.format(station_data['sggName'], station_data['umdName']))
    
    select_num = int(input('원하는 주소 정보의 번호를 입력하십시오. [ ex) 1 ] >>> '))
    select_station_info = dict_data1['response']['body']['items'][select_num - 1]
    return select_station_info



#내가 원하는 곳의 tmX, tmY 값을 변수에 저장하기

ms_data = introduce()

tmX_val = ms_data['tmX']
tmY_val = ms_data['tmY']



#근접 측정소 목록 조회 ㄱㄱ

req_url2 = 'http://apis.data.go.kr/B552584/MsrstnInfoInqireSvc/getNearbyMsrstnList'

req_parameter2 = {
    'serviceKey':ServiceKey,
    'returnType':return_type,
    'tmX':tmX_val,
    'tmY':tmY_val,
}

r2 = requests.get(req_url2, params = req_parameter2)
dict_data2 = r2.json()



#출력 해보자.

def choose_station():
    print("\n\n\n[ 선택한 지역의 측정소 목록 ]\n")

    for k in range(dict_data2['response']['body']['totalCount']):
    
        station_name = dict_data2['response']['body']['items'][k]['stationName']
        tm = dict_data2['response']['body']['items'][k]['tm']
        addr = dict_data2['response']['body']['items'][k]['addr']
        
        print('[ {0} ]'.format(k + 1))
        print('- 측정소 이름 : {0} / 거리 : {1}km'.format(station_name, tm))
        print('- 측정소 주소 : {0}'.format(addr), '\n')
        
    select_num = int(input('\n\n원하는 측정소의 번호를 입력하십시오. [ ex) 1 ] >>> '))
    select_station_name = dict_data2['response']['body']['items'][select_num - 1]['stationName']
    
    return select_station_name



#측정 정보 가져오기

req_url3 = 'http://apis.data.go.kr/B552584/ArpltnInforInqireSvc/getMsrstnAcctoRltmMesureDnsty'

station_name = choose_station()

date_term = 'DAILY'

version = 1.3

req_parameters3 = {
    'serviceKey':ServiceKey,
    'returnType':return_type,
    'ver':version,
    'stationName':station_name,
    'dataTerm':date_term,
}

r3 = requests.get(req_url3, params = req_parameters3)
dict_data3 = r3.json()



#측정 정보 결과 나타내기

data = dict_data3['response']['body']['items'][0]

dataTime = data['dataTime']

so2Grade = data['so2Grade']
so2Value = data['so2Value']
coGrade = data['coGrade']
coValue = data['coValue']
o3Grade = data['o3Grade']
o3Value = data['o3Value']
no2Grade = data['no2Grade']
no2Value = data['no2Value']

pm10Grade1h = data['pm10Grade1h']
pm10Value = data['pm10Value']
pm25Grade1h = data['pm25Grade1h']
pm25Value = data['pm25Value']

Grade = {'1':'좋음', '2':'보통', '3':'나쁨', '4':'매우나쁨'}


print('\n\n\n[ 측정소({0})에서 측정된 대기 오염 상태 ]\n'.format(station_name))
print('- 측정 시간 : {0}\n'.format(dataTime))
print('[지수] 아황산가스 : {0}, 일산화탄소 : {1}, 오존 : {2}, 이산화질소 : {3}'.format(Grade[so2Grade], Grade[coGrade], Grade[o3Grade], Grade[no2Grade]))
print('[농도] 아황산가스 : {0}ppm, 일산화탄소 : {1}ppm, 오존 : {2}ppm, 이산화질소 : {3}ppm\n'.format(so2Value, coValue, o3Value, no2Value))
print('[지수] 미세먼지 : {0}, 초미세 먼지 : {1}'.format(Grade[pm10Grade1h], Grade[pm25Grade1h]))
print('[농도] 미세먼지 : {0}㎍/㎥, 초미세 먼지 : {1}㎍/㎥'.format(pm10Value, pm25Value))
```

    읍/면/동의 이름을 입력하십시오. >>> 대방동
    
    입력한 읍/면/동 이름 : 대방동 
    
    [ 검색된 결과 : 총 4건 ] 
     
    [ 1 ]
    [주소] : 사천시 대방동 
    
    [ 2 ]
    [주소] : 창원시 성산구 대방동 
    
    [ 3 ]
    [주소] : 동작구 대방동 
    
    [ 4 ]
    [주소] : 동작구 신대방동 
    
    원하는 주소 정보의 번호를 입력하십시오. [ ex) 1 ] >>> 3
    
    
    
    [ 선택한 지역의 측정소 목록 ]
    
    [ 1 ]
    - 측정소 이름 : 관악구 / 거리 : 1.8km
    - 측정소 주소 : 서울 관악구 신림동길 14신림동 주민센터 
    
    [ 2 ]
    - 측정소 이름 : 영등포로 / 거리 : 2.9km
    - 측정소 주소 : 서울 영등포구 영중로 37(영등포시장사거리) 
    
    [ 3 ]
    - 측정소 이름 : 구로구 / 거리 : 3.6km
    - 측정소 주소 : 서울 구로구 가마산로 27길 45구로고등학교 
    
    
    
    원하는 측정소의 번호를 입력하십시오. [ ex) 1 ] >>> 2
    
    
    
    [ 측정소(영등포로)에서 측정된 대기 오염 상태 ]
    
    - 측정 시간 : 2021-08-14 14:00
    
    [지수] 아황산가스 : 좋음, 일산화탄소 : 좋음, 오존 : 보통, 이산화질소 : 좋음
    [농도] 아황산가스 : 0.003ppm, 일산화탄소 : 0.4ppm, 오존 : 0.041ppm, 이산화질소 : 0.017ppm
    
    [지수] 미세먼지 : 좋음, 초미세 먼지 : 좋음
    [농도] 미세먼지 : 17㎍/㎥, 초미세 먼지 : 9㎍/㎥

