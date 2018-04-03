# Jira

## 1. Jira介绍
JIRA是Atlassian公司出品的项目与事务跟踪工具，被广泛应用于缺陷跟踪、客户服务、需求收集、流程审批、任务跟踪、项目跟踪和敏捷管理等工作领域

## 2. 一般工作流程
  * **项目** ：可以看见所有的项目，每一栏形式如下：**projectName(projectKey)**，表示该项目的 **name** 和 **key**；选择进入可见具体的内容，可以在项目里 **创建** 任务、子任务、缺陷等，记录相关信息，指定分配人、报告人等信息
  * **看板** ：可以看见所有工作小组的任务状态，有 **代办**、 **处理中**、 **完成** 三种。根据小组成员进行分类，有一个 **未分配** 的单独类型
  * 还有 **测试**， **问题** 等板块，目前未使用过

## 3. JIRA API 文档简介
##### 可以通过 [JIRA REST API Reference](https://docs.atlassian.com/software/jira/docs/api/REST/7.1.7/?_ga=2.102340485.525700284.1517821635-1557436875.1517821556) 操作Jira，以下操作均基于此文档，仅为一个简单的整理，测试方式为Postman ####
### 3.1 验证
* Jira url (举个栗子) : http://192.168.1.1:8080  <br/>
* [身份验证](https://developer.atlassian.com/server/jira/platform/basic-authentication/) : 在 **Headers** 添加 <br/>
  * **Key** Authorization <br/>
    **Value** Basic dXNlcm5hbWU6cGFzc3dvcmQ= <br/>
    Basic + username:password 的BASE64方式编码 <br/>
  * 下文若用到Http的POST方法，则还需添加<br/>
    **Key** Content-Type <br/>
    **Value** Basic application/json <br/>

### 3.2 Project
* Get all projects &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;```GET /rest/api/2/project```
* Get project &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;```GET /rest/api/2/project/{projectIdOrKey}``` <br/>
项目每一栏形式如下：projectName(projectKey)

### 3.3 Issue
* Get issue &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;```GET /rest/api/2/issue/{issueIdOrKey}``` <br/>
issueKey可以从Jira界面的问题列表中看到，issueKey一般为 ```projectKey-123```
* Get issue all types &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;```GET /rest/api/2/issuetype``` <br/>
获取诸如任务、子任务、故事等issues的元数据
* Create issue  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;```POST /rest/api/2/issue```
通过JSON表达式新建一个 **issue** 或者 **sub-task** <br/>
在此介绍基本的JSON表达式字段 <br/>
  * issue
  ```JSON
  {
  　　"fields":{
  　　　　"project":{
  　　　　　　"id":"10000 (projectId)"
  　　　　},
  　　　　"summary":"something's wrong (标题)",
  　　　　"issuetype":{
  　　　　　　"id":"10104 (某种issue type: GET /rest/api/2/issuetype)"
  　　　　},
  　　　　"assignee":{
  　　　　　　"name":"homer (经办人)"
  　　　　},
  　　　　"reporter":{
  　　　　　　"name":"smithers (报告人)"
  　　　　},
  　　　　"priority":{
  　　　　　　"id":"1 (优先级类型: GET /rest/api/2/priority)"
  　　　　},
  　　　　"versions":[
  　　　　　　{}
  　　　　],
  　　　　"description":"description (说明，支持markdown语法，可往此处写链接等)",
  　　　　"fixVersions":[
  　　　　　　{}
  　　　　],
  　　　　"components":[
  　　　　　　{}
  　　　　]
  　　}
  }
  ```
  * sub-task，与上面不同之处在于
  ```JSON
  {
  　　"fields":{
  　　　　"issuetype":{
  　　　　　　"id":"11111 (子任务类型)"
  　　　　},
        "parent": {
           "key": "issueIdOrKey"
        }
  　　}
  }
  ```
* 给issue添加关注人 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;```POST /rest/api/2/issue/{issueIdOrKey}/watchers``` <br/>
**参数："fred"** <br/>
每次只能添加一个人，代码中，多个人可以先如下保存；String切分，注意传输数据形式要符合JSON格式，勿忘 **""**
```java
String watchers = "["watcher1","watcher2"]";
```

## 4. Java实现Http请求
以上的测试均是通过Postman进行Http请求，下面我们需要通过Java代码实现Http请求，这部分可以多找找网上资料，以下展示部分关键代码：
```
import java.io.*;
import java.net.HttpURLConnection;
import java.net.URL;
```
* Http Get
  ```Java
  String sendHttpGET(String url){
    HttpURLConnection httpConn = (HttpURLConnection) requestUrl.openConnection();
    httpConn.setRequestMethod("GET");
    httpConn.setRequestProperty("Authorization", "Basic BASE64");
  }
  ```
* Http Post
  ```Java
  String sendHttpPOST(String url, String data){}
    HttpURLConnection httpConn = (HttpURLConnection) requestUrl.openConnection();
    httpConn.setRequestMethod("POST");
    httpConn.setRequestProperty("Authorization", "Basic BASE64");
    httpConn.setRequestProperty("Content-Type", "application/json");
    httpConn.setDoOutput(true);
    httpConn.setDoInput(true);
    OutputStream out = httpConn.getOutputStream();
    if (data != null) {
        out.write(data.getBytes("UTF-8"));
      }
      out.flush();
      out.close();
}
    ```
