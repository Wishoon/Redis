# Redis

1. 레디스란
  - Remote Dictionary Server의 약자
  - '휘발성'이면서 '영속성'을 가진 Key-Value 저장소
  
2. Collection의 중요성
  - 레디스는 'In-Memory' 데이터베이스. 즉, 데이터를 메모리에 저장하고 조회한다.
  - 다양한 자료구조를 통해 'Key-Value' 형태로 저장 -> '개발의 편의성과 난이도 하락'
 
3. 기본적인 스프링에서의 레디스 설정
  - Reids용 설정클래스
    - 
  
  
4. 레디스의 자료구조(+ Create)
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
  - Set
    - Value가 Set 자료구조가 되므로, 하나의 Key 값에 여러 값을 Set 자료구조를 통해 저장가능
    - 방법 1. SetOperations 사용
    ```java
    ```
  - Hash
    - Value가 Map 자료구조와 같은 Key/Value 형태가 됨
    - 하나의 Key에 여러개의 필드를 갖는 구조가 됨.
    - 방법 1. HashOperations 사용
    ``` java
    RedisTemplate<String, Object> redisTemplate = (RedisTemplate<String, Object>)ctx.getBean("redisTemplate");
			
    Map<String, String> data1 = new HashMap<String, String>();
    data1.put("name", "Bob");
    data1.put("age", "26");
    data1.put("id", "02");
			
    Map<String, String> empJohnMap = new HashMap<String, String>();
    data2.put("name", "John");
    data2.put("age", "16");
    data2.put("id", "03");
			
    // Hash Operation
	
    HashOperations<String, String, String> hash = redisTemplate.opsForHash();
    String data1Key = "Data1";
    String data2Key = "Data2";
			
    hash.putAll(data1Key, data1);
    hash.putAll(data2Keyy, data2);
    ```
