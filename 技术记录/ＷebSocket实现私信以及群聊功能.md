# ＷebSocket实现私信功能

## 引入依赖

~~~xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-websocket</artifactId>
        </dependency>
~~~

## 配置文件 WebSocketConfig

~~~java
package com.example.common;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.socket.config.annotation.EnableWebSocket;
import org.springframework.web.socket.server.standard.ServerEndpointExporter;
 //表明配置文件
@Configuration 
@EnableWebSocket 
public class WebSocketConfig {
    /**
     * 注入一个ServerEndpointExporter,该Bean会自动注册使用@ServerEndpoint注解申明的websocket endpoint
     */
    @Bean
    public ServerEndpointExporter serverEndpointExporter() {
        return new ServerEndpointExporter();
    }
}

~~~

## 前端Vue调用

~~~javascript
    client = new WebSocket(`ws://localhost:8085/imserverSingle`)
    client.onopen = () => {
      console.log('websocket open')
    }
    client.onclose = () => {  // 页面刷新的时候和后台websocket服务关闭的时候
      console.log('websocket close')
    }
    
    send() {
      if (!this.toUser) {
        this.$notify.error('请选择聊天用户')
        return
      }
      if (client) {
        let message = this.getMessage('text')
        client.send(JSON.stringify(message))
      }
      document.getElementById('im-content').innerHTML = ''  // 清空输入框
    },

~~~

## 监听事件

~~~javascript
    client.onmessage = (msg) => { //WebSocket 对象的事件监听器，用于处理从服务器接收到的消息。
      if (msg.data) { // 检查接收到的消息是否存在。
        let json = JSON.parse(msg.data)
        // json.fromuser === this.fromUser
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

## 页面关闭销毁WebSocket

~~~javascript
  // Vue实例销毁之前执行  释放资源
  beforeDestroy() {
    if (client) {
      client.close()
    }
  },
~~~

## 单聊 WebSocketSingleServer

~~~java
package com.example.common;

import cn.hutool.core.date.DateUtil;
import cn.hutool.json.JSONUtil;
import com.example.entity.ImSingle;
import com.example.service.ImSingleService;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.InitializingBean;
import org.springframework.stereotype.Component;

import javax.annotation.Resource;
import javax.websocket.*;
import javax.websocket.server.ServerEndpoint;
import java.io.IOException;
import java.util.Map;
import java.util.concurrent.ConcurrentHashMap;

/**
 * websocket服务 - 单聊
 */
@ServerEndpoint(value = "/imserverSingle")
@Component
public class WebSocketSingleServer implements InitializingBean {
    private static final Logger log = LoggerFactory.getLogger(WebSocketSingleServer.class);
    /**
     * 记录当前在线连接数
     */
    public static final Map<String, Session> sessionMap = new ConcurrentHashMap<>();

    @Resource
    ImSingleService imSingleService;

    static ImSingleService staticImSingleService;

    /**
     * 连接建立成功调用的方法
     */
    @OnOpen
    public void onOpen(Session session) {
        sessionMap.put(session.getId(), session);
        log.info("[onOpen] 新建连接，session={}, 当前在线人数为：{}", session.getId(), sessionMap.size());

    }

    /**
     * 连接关闭调用的方法
     */
    @OnClose
    public void onClose(Session session) {
        sessionMap.remove(session.getId());
        log.info("[onClose] 有一连接关闭，session={}, 当前在线人数为：{}", session.getId(), sessionMap.size());
    }

    /**
     * 收到客户端消息后调用的方法
     * 后台收到客户端发送过来的消息
     * onMessage 是一个消息的中转站
     * 接受 浏览器端 socket.send 发送过来的 json数据
     *
     * @param message 客户端发送过来的消息
     */
    @OnMessage
    public void onMessage(String message, Session fromSession) {
        log.info("服务端收到消息:{}", message);
        ImSingle imSingle = JSONUtil.toBean(message, ImSingle.class);
        imSingle.setTime(DateUtil.now());
        // 存储数据到数据库
        staticImSingleService.add(imSingle);
        String jsonStr = JSONUtil.toJsonStr(imSingle);  // 处理后的消息体
        this.sendAllMessage(jsonStr);
        log.info("[onMessage] 发送消息：{}", jsonStr);
    }

    @OnError
    public void onError(Session session, Throwable error) {
        log.error("[onError] 发生错误", error);
    }

    /**
     * 服务端发送消息给除了自己的其他客户端
     */
    private void sendMessage(Session fromSession, String message) {
        sessionMap.values().forEach(session -> {
            if (fromSession != session) {
                log.info("服务端给客户端[{}]发送消息{}", session.getId(), message);
                try {
                    session.getBasicRemote().sendText(message);
                } catch (IOException e) {
                    log.error("服务端发送消息给客户端异常", e);
                }
            }
        });
    }

    /**
     * 服务端发送消息给所有客户端
     */
    private void sendAllMessage(String message) {
        try {
            for (Session session : sessionMap.values()) {
                log.info("服务端给客户端[{}]发送消息{}", session.getId(), message);
                session.getBasicRemote().sendText(message);
            }
        } catch (Exception e) {
            log.error("服务端发送消息给客户端失败", e);
        }
    }

    @Override
    public void afterPropertiesSet() {
        staticImSingleService = imSingleService;
    }
}

~~~

## JSON字符串处理

 JSON.stringify 方法用于将 JavaScript 对象转换为 JSON 字符串。这个方法接受一个 JavaScript 对象作为参数，并返回一个表示该对象的 JSON 字符串。

JSON.parse 方法用于将 JSON 字符串转换回 JavaScript 对象。它接受一个包含 JSON 数据的字符串，并返回一个与该字符串相对应的 JavaScript 对象。

后端 JSONUtil.toJsonStr 通常是指一个用于将对象转换为 JSON 字符串的工具方法

后端 JSONUtil.toBean 通常是指一个用于将 JSON 字符串转换为对象的工具方法





















