# Redis 实现文章点赞和收藏功能

## 采用Redis中的set结构

~~~
key 为 LIKE_KEY  set为 "博客::博客id::用户id" 有数据表示点过赞 无数据表示没点赞
~~~

## 实现点赞取消点赞模块

~~~java
    public void set(Likes likes) {
        //当前用户的ID
        Account currentUser = TokenUtils.getCurrentUser();
        likes.setUserId(currentUser.getId());
        String value = likes.getModule() + "::" + likes.getFid() +"::"+currentUser.getId();
        if(setOperations.isMember(LIKE_KEY,value)){  //点过赞就取消
            setOperations.remove(LIKE_KEY,value);
        }else{  //没点过就添加
            setOperations.add(LIKE_KEY,value);
        }
    }

~~~

## 获取一个文章的赞 收藏数量

~~~java

//    获取一个文章的 点赞或者收藏 数量
    public int acquireLikeCount(Blog blog,String KEY){
        int Count = 0;
        Set<String>  set = setOperations.members(KEY);
        for(String x :set){
            String[] parts = x.split("::");
            int blogId = Integer.parseInt(parts[1]);
            if(blogId == blog.getId()) Count++;
        }
       return Count;
    }

~~~

## 获取当前用户是否点过赞

~~~java
// 获取当前用户是否点过赞或者收藏过
    public boolean isUserLike(int blogId,int userId,String module,String KEY){
        String value = module + "::" + blogId + "::"+ userId;
        if(setOperations.isMember(KEY,value)) return true;
        else return false;

    }
~~~



## 获取一个人的点赞或者收藏文章列表

~~~java
    //获取一个人的点赞列表或者收藏列表
    public List<Blog> userLikeBlogList(int userid,String KEY){
        Set<String>  set = setOperations.members(KEY);
        List<Blog> blogList = new ArrayList<Blog>();
        for(String x :set){
            String[] parts = x.split("::");
            String name = parts[0];
            int userId = Integer.parseInt(parts[2]);
            int blogId = Integer.parseInt(parts[1]);
            if(name.equals("博客") && userId == userid) {
                Blog b = blogMapper.selectById(blogId);
                if(b!=null)  blogList.add(b);
            }
        }
        sortByDate(blogList);
        return blogList;
    }
    //按照日期降序排序
    public static void sortByDate(List<Blog> blogList) {
        Collections.sort(blogList, new Comparator<Blog>() {
            @Override
            public int compare(Blog blog1, Blog blog2) {
                return blog2.getDate().compareTo(blog1.getDate());
            }
        });
    }
~~~

## 活动业务设计

把博客和活动的点赞设计到一块，把博客和活动的收藏设计到一块，因为活动很少不占用多少内存，也不影响查询效率

## 注意细节

- 删除博客时候需要删除点赞收藏以及阅读量相关数据

- 删除用户时候需要删除的东西
  - 对应的用户点赞收藏博客以及报名点赞收藏的活动 
  - 删除用户的所有文章
  - 删除用户的所有评论
  - 删除用户的报名信息

- 删除活动时候需要考虑删除用户报名 点赞收藏活动的数据

~~~java
    //删除redis的点赞数据
    public void removeRedisLike(String name,int typeId,int userId,String type){
        //说明是根据人删除
        if(typeId == -1){
            Set<String> set = setOperations.members(type);
            for(String x:set){
                String [] parts = x.split("::");
                String namex  = parts[0];
                int userid = Integer.parseInt(parts[2]);
                if(namex.equals(name) && userid == userId){
                    setOperations.remove(type,x);
                }
            }
        }
        //根据活动或者博客删除
        if(userId == -1){
            Set<String> setType = setOperations.members(type);
            System.out.println(setType);
            for(String x:setType){
                String [] parts = x.split("::");
                String nameActivity  = parts[0];
                int typeid = Integer.parseInt(parts[1]);
                if(nameActivity.equals(name) && typeid == typeId){
                    setOperations.remove(type,x);
                }
            }
        }
    }
~~~

## 获得阅读量和点赞量

~~~java
            //点赞量
            Integer likesCount = this.acquireLikeCount(b,LIKE_KEY);
            b.setLikesCount(likesCount);
            //阅读量
            Double score = zSetOperations.score(BLOG_KEY, b.getId());
            int readCount = (score != null) ? score.intValue() : 0;
            b.setReadCount(readCount);

//    获取一个文章的 点赞或者收藏 数量
    public Integer acquireLikeCount(Blog blog,String KEY){

        int Count = 0;
        Set<String>  set = setOperations.members(KEY);
        for(String x :set){
            String[] parts = x.split("::");
            String name = parts[0];
            int blogId = Integer.parseInt(parts[1]);
            if(name.equals("博客") && blogId == blog.getId()) Count++;

        }
       return Count;
    }

~~~

