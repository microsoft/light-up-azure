# 애저 스트림 애널리틱스 기반 실시간 스트리밍 데이터 분석하기



## 실습 주제



## 리소스 그룹 생성

![image00](image00-167600859459237.png)

![image02](image02.png)





## 템플릿 다운로드

[TollApp Azure Template 배포](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-stream-analytics%2Fmaster%2FSamples%2FTollApp%2FVSProjects%2FTollAppDeployment%2Fazuredeploy.json)



![image03](image03-167600793124333.png)



## 스토리지 계정 생성

![image04](image04-167600862692739.png)

![image05](image05-167600866885641.png)

![image06](image06-167600874306843.png)

![image07](image07-167600879726345.png)

## 

![image08](image08-167601217967947.png)

![image09](image09-167601295043749.png)





## Azure Synapse Analytics생성



![image10](image10-167601309386851.png)

![image11](image11-167601312425253.png)

![image12](image12-167601317749255.png)

![image13](image13-167601321161057.png)

![image14](image14-167601332178959.png)

![image26](image26.png)

![image27](image27.png)







![image24](image24.png)



![image25](image25.png)



![image28](image28.png)



```SQL
CREATE TABLE carsummary   
(  
Make nvarchar(20),  
CarCount int,
  times datetime
  )
WITH ( CLUSTERED COLUMNSTORE INDEX ) ;
```

![image29](image29.png)





## 생성한 리소스 점검

![image15](image15-167601347058961.png)



## EventHub 

![image16](image16-167601388936063.png)

![image17](image17.png)

![image18](image18.png)

![image19](image19.png)

![image20](image20.png)



![image21](image21.png)

![image22](image22.png)



![image23](image23.png)



![image30](image30.png)

![image31](image31.png)

![image32](image32.png)

![image33](image33.png)





![image34](image34.png)



![image35](image35.png)

![image36](image36.png)

![image37](image37.png)

![image38](image38.png)



![image39](image39.png)





![image40](image40-167601888539691.png)



![image41](image41.png)



![image42](image42.png)



![image43](image43.png)



![image44](image44.png)

![image46](image46.png)



![image56](image56.png)









## (Optional)Power bi

![image47](image47.png)

![image48](image48.png)

![image49](image49.png)

![image50](image50.png)

![image51](image51.png)

![image52](image52.png)



![image53](image53.png)



![image54](image54.png)

![image55](image55.png)



![image57](image57.png)

![image58](image58.png)

![image59](image59.png)

![image60](image60.png)
