---
title: Wework
date: 2021-06-03 17:03:12
tags:
---

##### 前言

>近期开发了企业微信相关功能，因此编写此文记录开发过程以及期间遇到的各类问题。

* [框架：Laravel 8.0](https://learnku.com/docs/laravel/8.x)
* [类包：EasyWechat](https://easywechat.com/docs/5.x/overview)

<!-- more -->

##### [企业微信登陆流程](https://open.work.weixin.qq.com/api/doc/90000/90135/91020)

>企业微信提供了OAuth的授权登录方式，可以让从企业微信终端打开的网页获取成员的身份信息，从而免去登录的环节

* [构造网页授权链接](https://open.work.weixin.qq.com/api/doc/90000/90135/91022)

```php
$config = [
   'corp_id' => '我的企业>>企业信息>>企业ID',
   'secret' => '应用管理>>内部应用>>Secret',
   'agent_id' => '应用管理>>内部应用>>AgentId',
];    
$app = Factory::work($config);
// $callbackUrl 为授权回调地址,需设置可信域名
$callbackUrl = 'https://www.test.com/api/wework/callback';
$state = 'asdfdf-dfdsfdsf-sdf';
// 携带state获取企业微信跳转目标地址
//state 可以任意设置凭证以加强安全性，比如设置一个一次核销的uuid使得该链接仅能访问一次，也可以不使用
$redirect = $app->oauth->withState($state)->redirect($callbackUrl);
//$redirect 就是获得的网页授权链接
```
* 服务端Callback

```php
//访问上方获取的网页授权链接，会自动跳转https://www.test.com/api/wework/callback?code=CODE&state=STATE
$app->oauth->detailed()->userFromCode($code); //根据code获取企业用户信息
// 业务处理，用户数据存储，颁发token
// 由于项目是前后端分离，所以带token返回给前端
return redirect()->away('前端项目地址').'?'.$token);
```
* 跨域问题参考：
 * [Learnku案例](https://learnku.com/laravel/t/30195)
 * [社区案例](https://developers.weixin.qq.com/community/develop/doc/0006eec604cc98a2907b82cb251000?highLine=%25E8%25B7%25A8%25E5%259F%259F)
 * [简书案例](https://www.jianshu.com/p/8dbb69cc91ca)

##### [通讯录同步](https://open.work.weixin.qq.com/api/doc/90000/90135/90193)

>通讯录同步相关接口，可以对部门、成员、标签等通讯录信息进行查询、添加、修改、删除等操作。
> 
```php
$config = [
  'corp_id' => '我的企业>>企业信息>>企业ID',
  'secret' => '管理工具>>通讯录同步>>Secret',
];   
$app = Factory::work($config);
$result = $app->user->getDetailedDepartmentUsers('部门ID');
//批量将result 存入数据库中
```


##### [客户管理](https://open.work.weixin.qq.com/api/doc/90000/90135/92112)

>企业可通过此接口获取指定成员添加的客户列表。客户是指配置了客户联系功能的成员所添加的外部联系人。没有配置客户联系功能的成员，所添加的外部联系人将不会作为客户返回。

```php
//前提 服务端返回agentConfig配置给到前端 ，前端调用SDK获取external_userid
//获取agentConfig 返回给前端
$agent_config = [
  'corp_id' => '我的企业>>企业信息>>企业ID',
  'secret' => '应用管理>>内部应用>>Secret',
];
$agent = Factory::work($agent_config);
$this->agent->jssdk->setUrl($url);
$config = $this->agent->jssdk->buildAgentConfig($apis, $agentId, $debug = false, $beta = false, $json = true, $openTagList = []);
//前端带agentConfig获取该客户external_userid再调用后端接口
$config = [
  'corp_id' => '我的企业>>企业信息>>企业ID',
  'secret' => '客户联系>>客户>>支持通过API管理客户>>Secret',
];
$app = Factory::work($config);
$customer = $app->external_contact->get($external_userid);
//业务处理客户信息
```

##### [事件接收](https://open.work.weixin.qq.com/api/doc/90000/90135/90238)

>企业可以通过获取成员的客户列表接口来获知成员添加了哪些客户，为了提高实时性，企业可设置外部联系人变更回调，成员添加和删除客户时将以事件的形式推送到指定URL，企业接收到外部联系人变更事件后，响应处理后即可保证获取到的企业客户列表是及时更新的列表。

```php
$callback_config = [
 'corp_id' => '我的企业>>企业信息>>企业ID',
 'secret' => '应用管理>>内部应用>>Secret',
 'suite_id' => '我的企业>>企业信息>>企业ID',
 'suite_secret' => '应用管理>>内部应用>>Secret',
 'token' => '企业应用 接收消息服务器配置 token',
 'aes_key' => '企业应用 接收消息服务器配置 EncodingAESKey',
];
$callback = Factory::work($this->callback_config);
$server = $callback->server;
        $server->push(function ($message) {
            //指令回调
//            if (isset($message['InfoType'])) {
//                switch ($message['InfoType']) {
            //推送suite_ticket
//                    case 'suite_ticket':
//                        break;
            //授权成功通知
//                    case 'create_auth':
//                        break;
            //变更授权通知
//                    case 'cancel_auth':
//                        break;
            //通讯录事件通知
//                    case 'change_contact': //里可以做通讯录的变动回调，实时监听企业成员变动
//                        switch ($message['ChangeType']) {
//                            case 'create_user':
//                                return '新增成员事件';
//                                break;
//                            case 'update_user':
//                                return '更新成员事件';
//                                break;
//                            case 'delete_user':
//                                return '删除成员事件';
//                                break;
//                            case 'create_party':
//                                return '新增部门事件';
//                                break;
//                            case 'update_party':
//                                return '更新部门事件';
//                                break;
//                            case 'delete_party':
//                                return '删除部门事件';
//                                break;
//                            case 'update_tag':
//                                return '标签成员变更事件';
//                                break;
//                        }
//                        break;
//                    default:
//                        return 'fail';
//                        break;
//                }
//            }

            //数据回调
            if (isset($message['MsgType'])) {
                switch ($message['MsgType']) {
                    case 'event': //https://work.weixin.qq.com/api/doc/90000/90135/92130#%E7%BC%96%E8%BE%91%E4%BC%81%E4%B8%9A%E5%AE%A2%E6%88%B7%E4%BA%8B%E4%BB%B6
                        switch ($message['ChangeType']) {
                            case 'add_external_contact': //新增客户
                            case 'edit_external_contact': //编辑客户
                                //做相应业务处理
                        }
                        break;
//                    case 'text':
//                        return '文本消息';
//                        break;
//                    case 'image':
//                        return '图片消息';
//                        break;
//                    default:
//                        return '其他消息';
                        break;
                }
            }
        });
        $response = $server->serve();
        return $response->send();
```

本地开发调试事件服务器可以使用natapp

* [natapp教程](https://natapp.cn/article/wechat_local_debug)




  
