docker run -d \
  --name grafana \
  --network spring-net \
  -p 3000:3000 \
  grafana/grafana-oss


http://localhost:3000 실행 -> connection -> data source -> prometheus -> 


┌─────────────────────────────────────┐  ===> 메모리 저장 구조를 알아두면 좋다 면접에 나왔었음
│        JVM Memory (전체)             │
└─────────────────────────────────────┘
          │
          ├─── Heap (힙 메모리)      ===> 3가지 힙메모리 영역
          │      ├─ Young Generation
          │      │    ├─ Eden Space
          │      │    └─ Survivor Space (S0, S1)
          │      └─ Old Generation (Tenured)    ====> 오래된 것들이 저장되어있음
          │
          └─── Non-Heap (비힙 메모리)
                 ├─ Metaspace (클래스 메타데이터)
                 ├─ Code Cache (JIT 컴파일된 코드)
                 └─ Compressed Class Space


Garbage Collection(GC) : 객체를 해제해달라 등의 요청은 할수없으나 아무도 레퍼런스 하지 않는 객체들을 가져간다(?)
작동 방식 ====> 구글 닥 사진 보기
1. Eden에 객체 생성
   [Eden: ████████] [Survivor: ] [Old: ]

2. Eden 가득 참 → Minor GC 발생
   [Eden: ████████] → GC → [Eden: ] [Survivor: ██] [Old: ]

3. 여러 번 살아남은 객체 → Old로 승격
   [Eden: ] [Survivor: ██] → [Old: ████]

4. Old도 가득 참 → Full GC 발생
   [Old: ████████] → GC → [Old: ██]


// headump : 
http://localhost:8080/actuator/heapdump

management:
  endpoints:
    web:
      exposure:
        include:
          - heapdump

===> 누가 비정상적으로 많이 쓰고 있나? 알수있다던지....(.hprof) 지표가 된다 ===> byte[], string이 1등임

//
