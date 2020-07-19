# Redis

1. 레디스란
  - Remote Dictionary Server의 약자
  - '휘발성'이면서 '영속성'을 가진 Key-Value 저장소
  
2. Collection의 중요성
  - 레디스는 'In-Memory' 데이터베이스. 즉, 데이터를 메모리에 저장하고 조회한다.
  - 다양한 자료구조를 통해 'Key-Value' 형태로 저장 -> '개발의 편의성과 난이도 하락'
  
3. 레디스의 자료구조
  - String
    - 레디스의 String은 키와 연결할 수 있는 가장 간단한 유형의 값
    - 모든 종류와 문자열을 정리할 수 있다.
    - 방법 1. ValueOperations 사용
    ```java
    RedisTemplate<String, Object> redisTemplate = (RedisTemplate<String, Object>) ctx.getBean("redisTemplate");
    ValueOperations<String, Object> values = redisTemplate.opsForValue();

    // set1 (Key와 Value로 구성)
    values.set("Key 값", "data");
    // set2 (Key와 class를 이용해 value를 구성)
    values.set("Key 값", new User("data1", "data2")); 
    ```
    - 방법 2. ValueOperations 없이 사용
    ```java
    RedisTemplate<String, Object> redisTemplate = (RedisTemplate<String, Object>) ctx.getBean("redisTemplate");
    
    redisTemplate.opsForValue().set("Key", new User("data1", "data2));
    ```
