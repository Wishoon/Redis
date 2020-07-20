# Redis

1. 레디스란
  - Remote Dictionary Server의 약자
  - '휘발성'이면서 '영속성'을 가진 Key-Value 저장소
  
2. Collection의 중요성
  - 레디스는 'In-Memory' 데이터베이스. 즉, 데이터를 메모리에 저장하고 조회한다.
  - 다양한 자료구조를 통해 'Key-Value' 형태로 저장 -> '개발의 편의성과 난이도 하락'
  - Redis는 자료구조가 'Atomic' 하기 때문에 'Race Condition'을 피할 수 있음.
  - Reids 사용처
  	- Remote Data Store(A, B, C에서 데이터를 공유하고 싶을때)
  	- 인증 토큰 등을 저장
	- 랭킹
	- 유저 API limit
	
 
3. 기본적인 스프링에서의 레디스 설정
  - Reids용 설정클래스
    - Java Config
    ```java
    @Configuration
    public class RedisConfig {

		 @Bean
		 public JedisConnectionFactory jedisConnectionFactory() {

		  JedisConnectionFactory jedisConnectionFactory = new JedisConnectionFactory();
		  jedisConnectionFactory.setHostName("192.168.1.10");
		  jedisConnectionFactory.setPort(6379);
		  jedisConnectionFactory.setTimeout(0);
		  jedisConnectionFactory.setUsePool(true);

		  return jedisConnectionFactory;
		 }
		 
		 // 스프링은 RedisTemplate 클래스를 제공하는데, 이는 Redis 데이터에 쉽게 접근하기 위한 코드를 제공합니다.
		 @Bean
		 public StringRedisTemplate redisTemplate(JedisConnectionFactory jedisConnectionFactory) {
		   StringRedisTemplate stringRedisTemplate = new StringRedisTemplate();
		   // redisTemplate을 jedisConnectionFactory와 연결 하고, 주어진 객체와 Redis 데이터간의 직렬화, 역직렬화를 자동으로 수행 
		   stringRedisTemplate.setConnectionFactory(jedisConnectionFactory);
		  
		   return stringRedisTemplate;
		 }
    }
  
4. 레디스의 자료구조
  - Create
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
	    - 중복 제거, 순서 없음, find가 빠르다.
	    - 방법 1. SetOperations 사용
	    ```java
	    RedisTemplate<String, Object> redisTemplate = (RedisTemplate<String, Object>) ctx.getBean("redisTemplate");
	    
	    SetOperation<String, Object> setlist = redisTemplate.opsForSet();
	    
	    setList.add("key", "value");
	    	    
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
	    hash.putAll(data2Key, data2);
	    ```
	  - List
	    - Redis의 list는 일반적인 linked list의 특징을 가지고 있다. 즉, 노드를 하나 추가할때 동일한 시간이 소요
	    - 특정 값이나 인덱스로 데이터를 찾거나 삭제
  - Read	   
	  - List
	    - 방법 1. ListOperations 사용 (현재 에러 발생)
	    ``` java
	    public class RedisService {

	    @Autowired
		private RedisTemplate<String, String> template;
		@Resource(name="redisTemplate")
		private ListOperations<String, String> listOps;

		public List<Vo> getList() {
			RedisOperations<String, String> reids = listOps.getOperations();

			Set<String> keys = redis.keys("key");
			Iterator<String> iter = keys.iterator();

			List<Vo> list = new ArrayList<Vo>();
			
			while(iter.hasNext()) {
				String key = iter.next().toString();
				int size = (int)(long)redis.opsForList().size(key);
				
				for(int i=0; i<size; i++){
					Vo vo = new Vo();
					String[] value = redis.opsForList().leftPop(key).split("_");
					
					vo.setName(key);
					vo.setValue(value[0]);
					vo.setTime(value[1]);
					
					list.add(vo);
				}
			}
		}
	    ```
	  - Hash
	  	- 방법 1. 
		``` java
		// Controller
	   	@GetMapping("/select/{keys}")
		public String select(@PathVariable("keys") final String keys){
			SpringRedisExample ex = new SpringRedisExample();
			ex.select(keys);

			return "home";
		}
		```
		``` java
		// RedisExample.class
		public void select(String key) {
			ConfigurableApplicationContext ctx = new AnnotationConfigApplicationContext(SpringRedisConfig.class);
			try {
				@SuppressWarnings("unchecked")
				RedisTemplate<String, Object> redisTemplate = (RedisTemplate<String, Object>)ctx.getBean("redisTemplate");

				User result = new User();
				result.setName((String)redisTemplate.opsForHash().get(key, "name"));
				System.out.println(result.getName());
			}
			catch(Exception e) {
				e.printStackTrace();
			}
			finally {
				ctx.close();
			}
		}
  - Delete
  	- 방법 1.
	``` java
	public void delete(String key) {
		ConfigurableApplicationContext ctx = new AnnotationConfigApplicationContext(SpringRedisConfig.class);
		try {
			@SuppressWarnings("unchecked")
			RedisTemplate<String, Object> redisTemplate = (RedisTemplate<String, Object>)ctx.getBean("redisTemplate");

			redisTemplate.delete(key);
		}
		catch(Exception e) {
			e.printStackTrace();
		}
		finally {
			ctx.close();
		}
	}
	```
		
		
		
