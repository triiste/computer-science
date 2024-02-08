# WebSocket设置在线离线以及优先展示最近聊天的人

## 1.设置在线状态

### 主要逻辑：

#### 1.用户连接之后，首先后端执行onOpen,传入用户的id,然后写入到redis中

#### RedisConfig.java 中加入

~~~java
    @Bean
    public RedisTemplate<String, String> redisTemplate() {
        RedisTemplate<String, String> redisTemplate = new StringRedisTemplate(); // 使用StringRedisTemplate作为基础
        return redisTemplate;
    }
    @Bean
    public ValueOperations<String, String> stringValueOperations(RedisTemplate<String, String> redisTemplate) {
        return redisTemplate.opsForValue();
    }
~~~

~~~java
    /**
     * 连接建立成功调用的方法
     */
    @OnOpen
    public void onOpen(Session session,@PathParam("id") String id) {
        sessionMap.put(session.getId(), session);
        userMap.put(session.getId(),id);
        //使用Redis先缓存到数据库
//        System.out.println(id);
        long timestamp = System.currentTimeMillis();
        stringRedisTemplate.opsForValue().set(id,"存在"+timestamp);
        log.info("[onOpen] 新建连接，session={}, 当前在线人数为：{}", session.getId(), userMap.size());
        this.sendAllMessage(JSONUtil.toJsonStr(Dict.create().set("users", userMap.values())));//广播所有加入的用户
    }

    /**
     * 连接关闭调用的方法
     */
    @OnClose
    public void onClose(Session session) {
        //离线先删除redis数据库中元素
        stringRedisTemplate.delete(userMap.get(session.getId()));
        //因为我关闭了也就是下线了不能送message  退出有一点点延迟
        userMap.remove(session.getId());//删除当前用户
        sessionMap.remove(session.getId());
        log.info("[onClose] 有一连接关闭，session={}, 当前在线人数为：{}", session.getId(), userMap.size());
    }

~~~

#### 2.前端监听变化

~~~java
    client.onmessage = (msg) => { //WebSocket 对象的事件监听器，用于处理从服务器接收到的消息。
      if (msg.data) { // 检查接收到的消息是否存在。

        let json = JSON.parse(msg.data)
        if (json.users && json.users.length){
          this.loadOnline(json);
          return
        }

        if (json.content && (json.fromuser === this.fromUser && json.touser === this.toUser)
            || json.touser === this.fromUser && json.fromuser === this.toUser) {  // 说明是两者互相发消息
          this.messages.push(json) //放到消息队列里
          this.scrollToBottom()  // 滚动页面到最底部
        }
        // 加载消息数字 两者通信如果是对面发来的消息 说明当前正在两个人的通信窗口
        if (this.toUser === json.fromuser) {
          this.setUnReadNums()  // 把对方消息设为已读,更新未读消息数量
        } else {  //用户发给对方的
          // 可能是当前用户发给对方的消息，或者是其他人通信 都 加载当前用户的未读消息数量 每次监听到通信事件就更新未读记录
          this.loadUnReadNums()
        }
      }
    }
~~~

#### 3.查询在线人数

~~~java
    //查询所有的在线人数
    loadOnline(json){
      // 查询用户是否在线
      request.get('/user/selectAllOnline').then(res => {
        //过滤掉自己就可以 //实时刷新
        res.data = res.data.filter(v => (v.id + '_' + v.name) !== this.fromUser)
        this.$set(this.users, 'USER', res.data)
      })
    },
~~~

#### 4.后端进行业务逻辑判断

~~~java
    public List<User> selectAllOnline(User userNo) {
        List<User> userList = userMapper.selectAll(userNo);
        for(User user:userList){
            String key = Integer.toString(user.getId());
            String value = stringRedisTemplate.opsForValue().get(key);
            if(value != null){
                String username = user.getUsername()+"在线";
                user.setUsername(username);
            }
        }
        return userList;
    }
~~~

#### 5.注意在WebSocket直接写Redis相关操作会报错

#### 解决方案看该帖子:https://blog.csdn.net/weixin_45730605/article/details/135898294?spm=1001.2014.3001.5502

