# Springboot 使用Redis中ZSetOperations实现博客访问量功能

## 1.在application.yml中Redis配置信息

~~~
spring:
  redis:
    host: 127.0.0.1
    port: 6379
    password: 12345678
~~~

## 2.在pom.xml中加载依赖

~~~
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
~~~

## 3.配置Redis Template

~~~java
@Configuration
public class RedisConfig {
  @Bean("redisTemplate")
  public RedisTemplate<String, Object> redisTemplate(LettuceConnectionFactory lettuceConnectionFactory) {
      // 1.创建RedisTemplate对象
      RedisTemplate<String, Object> redisTemplate = new RedisTemplate<String, Object>();
      // 2.加载Redis配置
      redisTemplate.setConnectionFactory(lettuceConnectionFactory);
      // 3.配置key序列化
      RedisSerializer<?> stringRedisSerializer = new StringRedisSerializer();
      redisTemplate.setKeySerializer(stringRedisSerializer);
      redisTemplate.setHashKeySerializer(stringRedisSerializer);
      // 4.配置Value序列化
      Jackson2JsonRedisSerializer<Object> jackson2JsonRedisSerializer = new Jackson2JsonRedisSerializer<Object>(Object.class);
      ObjectMapper objMapper = new ObjectMapper();
      objMapper.setVisibility(PropertyAccessor.ALL, JsonAutoDetect.Visibility.ANY);
      objMapper.activateDefaultTyping(objMapper.getPolymorphicTypeValidator(), ObjectMapper.DefaultTyping.NON_FINAL);
      jackson2JsonRedisSerializer.setObjectMapper(objMapper);
      redisTemplate.setValueSerializer(jackson2JsonRedisSerializer);
      redisTemplate.setHashValueSerializer(jackson2JsonRedisSerializer);
      // 5.初始化RedisTemplate
      redisTemplate.afterPropertiesSet();
      return redisTemplate;
  }
  @Bean
  public ZSetOperations<String, Object> zSetOperations(RedisTemplate<String, Object> redisTemplate) {
    return redisTemplate.opsForZSet();
  }
}
~~~

## 4.操作说明

在Spring环境中，可以@Autowired自动注入方式注入操作Redis对象。比如：RedisTemplate、ZSetOperations。

~~~java
 @Autowired
  private RedisTemplate redisTemplate;
 @Autowired
  private ZSetOperations zSetOperations;
~~~

### 更新浏览量(不用去更新到mysql了)

~~~java
     //这个完全采用redis设计就可以 不用查询数据库 把阅读量完全加入到缓存中  
    public void updateReadCount(Integer blogId) {
        zSetOperations.incrementScore(BLOG_KEY, blogId, 1);
        //blogMapper.updateReadCount(blogId); 不用在mysql数据库更新了
    }
~~~

### 取出榜单TopK(效率提高不用在数据库中查出所有的然后取前20)

~~~java
    public List<Blog> selectTop() {
        List<Blog> blogList = new ArrayList<>();
        Long size = zSetOperations.size(BLOG_KEY);
        Set<Object> zset;
        if(size > TopN){
            zset = zSetOperations.reverseRange(BLOG_KEY, 0, TopN - 1);
        }else{
            zset = zSetOperations.reverseRange(BLOG_KEY, 0, - 1);
        }
        for(Object x:zset){
            blogList.add(blogMapper.selectById((int)x));
        }
        return blogList;
    }
~~~

### 显示浏览量(直接设定到mysql属性就好)

~~~java
        Double score = zSetOperations.score(BLOG_KEY, blog.getId());
        int readCount = (score != null) ? score.intValue() : 0;
        blog.setReadCount(readCount);
~~~

## 总结

如果要效率提高，必须得牺牲空间，本操作还可以进一步优化！

比如**把blog全部存到Redis，再找前TopK时候就不用去根据blog ID去mysql数据库搜索，直接可以输出**了

**但是如果博客过多的话 占用Redis内存太大**