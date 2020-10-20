---
title: JWT的四种使用方式
date: 2020-10-19 20:36:39
tags:
- JWT
categories:
- java
---

# 基本思路
1. access_token 过期设置为15分钟
2. 前端发起请求，后端验证access_token是否过期；如果过期，前端发起refresh_token请求，后端设置已再次授权标记为true，请求成功
3. 前端发起请求，后端验证再次授权标记，如果已经再次授权，则拒绝refresh_token的请求，请求成功
4. 如果前端每隔72小时，必须重新登录，后端检查用户最后一次登录日期，如超过72小时，则拒绝刷新token的请求，请求失败


# 1.0实现
1. 登录成功，返回access_token和refresh_token，客户端缓存此两种token;
2. 使用access_token请求接口资源，成功则调用成功；如果token超时，客户端携带refresh_token调用中间件接口获取新的access_token;
3. 中间件接受refresh_token的请求后，检查refresh_token是否过期。
如过期，拒绝刷新，客户端收到该状态后，跳转到登录页；
如未过期，生成新的access_token和refresh_token并返回给客户端（如有可能，让旧的refresh_token失效），客户端携带新的access_token重新调用上面的资源接口。
4. 客户端退出登录或修改密码后，调用中间件注销旧的token(使access_token和refresh_token失效)，同时清空客户端的access_token和refresh_toke。

	后端表
	id user_id client_id client_secret refresh_token expire_in create_date del_flag
	

# 2.0实现
场景： access_token访问资源 refresh_token授权访问 设置固定时间X必须重新登录

1. 登录成功，后台jwt生成access_token（jwt有效期30分钟）和refresh_token（jwt有效期15天），并缓存到redis（hash-key为token,sub-key为手机号,value为设备唯一编号（根据手机号码，可以人工废除全部token，也可以根据sub-key,废除部分设备的token。），设置过期时间为1个月(redis)，保证最终所有token都能删除)，返回后，客户端缓存此两种token;
2. 使用access_token请求接口资源，校验成功且redis中存在该access_token（未废除）则调用成功；如果token超时，中间件删除access_token（废除）；客户端再次携带refresh_token调用中间件接口获取新的access_token;
3. 中间件接受刷新token的请求后，检查refresh_token是否过期。
	如过期，拒绝刷新，删除refresh_token（废除）； 客户端收到该状态后，跳转到登录页；
	如未过期，检查缓存中是否有refresh_token（是否被废除），如果有，则生成新的access_token并返回给客户端，客户端接着携带新的access_token重新调用上面的资源接口。
4. 客户端退出登录或修改密码后，调用中间件注销旧的token(中间件删除access_token、refresh_token（废除）)，同时清空客户端侧的access_token和refresh_toke。
5. 如手机丢失，可以根据手机号人工废除指定用户设备关联的token。
6. 以上3刷新access_token可以增加根据登录时间判断最长X时间必须重新登录，此时则拒绝刷新token。（拒绝的场景：失效，长时间未登录，频繁刷新）

### 2.0 变动
1.登录
2.登录拦截器
3.增加刷新access_token接口
4.退出登录
5.修改密码


# 3.0实现
场景：自动续期 长时间未使用需重新登录

1. 登录成功，后台jwt生成access_token（jwt有效期30分钟），并缓存到redis（hash-key为access_token,sub-key为手机号,value为设备唯一编号（根据手机号码，可以人工废除全部token），设置access_token过期时间为7天，保证最终所有token都能删除)，返回后，客户端缓存此token;
2. 使用access_token请求接口资源，校验成功且redis中存在该access_token（未废除）则调用成功；如果token超时，中间件删除access_token（废除）,同时生成新的access_token并返回。客户端收到新的access_token，
再次请求接口资源。
3. 客户端退出登录或修改密码后，调用中间件注销旧的token(中间件删除access_token（废除）)，同时清空客户端侧的access_token。
4. 以上2 可以增加根据登录时间判断最长X时间必须重新登录，此时则拒绝刷新token。（拒绝的场景：长时间未登录，频繁刷新）
5. 如手机丢失，可以根据手机号人工废除指定用户设备关联的token。

### 3.0 变动
1.登录
2.登录拦截器
3.退出登录
4.修改密码



# 4.0实现
场景：token过期重新登录 长时间未使用需重新登录

1.登录成功，后台jwt生成access_token（jwt有效期7天），并缓存到redis，key为"user_id:access_token" + 用户id,value为access_token（根据用户id，可以人工废除指定用户全部
token），设置缓存过期时间为7天，保证最终所有token都能删除，请求返回后，客户端缓存此access_token；
2.使用access_token请求接口资源，校验成功且redis中存在该access_token（未废除）则调用成功；如果token超时，中间件删除access_token（废除）,同时生成新的access_token并返回。客户端收到新的access_token，再次请求接口资源。
3.客户端退出登录或修改密码后，调用中间件注销旧的token(中间件删除access_token（废除）)，同时清空客户端侧的access_token。
4.以上2 可以增加根据登录时间判断最长X时间必须重新登录，此时则拒绝刷新token。（拒绝的场景：长时间未登录，频繁刷新）
5.如手机丢失，可以根据手机号人工废除指定用户设备关联的token。

### 4.0 变动
1.登录
2.登录拦截器
3.退出登录
4.修改密码

# 最终实现

# 后端
1. 在登录接口中 如果校验账号密码成功 则根据用户id和用户类型创建jwt token(有效期设置为-1，即永不过期),得到A
2. 更新登录日期(当前时间new Date()即可)（业务上可选），得到B
3. 在redis中缓存key为ACCESS_TOKEN:userId:A(加上A是为了防止用户多个客户端登录 造成token覆盖),value为B的毫秒数（转换成字符串类型），过期时间为7天（7 * 24 * 60 * 60）
4. 在登录结果中返回json格式为{"result":"success","token": A}
5. 用户在接口请求header中携带token进行登录，后端在所有接口前置拦截器进行拦截，作用是解析token 拿到userId和用户类型（用户调用业务接口只需要传token即可）， 如果解析失败（抛出SignatureException），则返回json（code = 0 ,info= Token验证不通过, errorCode = '1001'）； 此外如果解析成功，验证redis中key为ACCESS_TOKEN:userId:A 是否存在 如果不存在 则返回json（code = 0 ,info= 会话过期请重新登录, errorCode = '1002'）； 如果缓存key存在，则自动续7天超时时间（value不变），实现频繁登录用户免登陆。
6. 把userId和用户类型放入request参数中 接口方法中可以直接拿到登录用户信息
7. 如果是修改密码或退出登录 则废除access_tokens（删除key）

# 前端（VUE）
1. 用户登录成功，则把username存入cookie中，key为loginUser;把token存入cookie中，key为accessToken 把token存入Vuex全局状态中
2. 进入首页