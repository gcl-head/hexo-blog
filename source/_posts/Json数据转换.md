---
title: Json数据转换
date: 2020-04-13 17:58:14
updated: 2020-04-13 17:58:14
tags:
    - android
    - json
categories: frontend
---
> 在python中，json字符串和变量的转换是非常自然和简单的，只需要使用`json.dumps()`和`json.loads()`就可以进行转码和解码的操作。然而在按照或中转码和解码都是较为复杂的。

## 1. Json解码

对于较为简单的数据格式，我们可以直接使用`com.alibaba.fastjson`工具包。

安装依赖：`implementation ‘com.alibaba:fastjson:1.1.54.android’`

首先我们要根据传递的数据格式构建一个对应的类，注意一定要定义空的构造函数哦~

```java
package com.example.demo_001.data_form;

public class Login_data {
    private String number;
    private String name;
    private String re;
    public Login_data() {
		// 空构造函数
    }
    public Login_data(String number, String name, String re) {
        this.number = number;
        this.name = name;
        this.re = re;
    }

    public String getNumber() {
        return number;
    }

    public void setNumber(String number) {
        this.number = number;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getRe() {
        return re;
    }

    public void setRe(String re) {
        this.re = re;
    }
}
```

然后就可以使用`JSON.parseObject()`或`JSON.parseArray()`对json字符串进行解码操作了。

```java
Login_data login_data = JSON.parseObject(login_data_text, Login_data.class);
List<Login_data> login_datas = JSON.parseArray(login_data_text, Login_data.class);
```

而对于比较复杂的数据结构，比如某个变量是列表的数据结构。上述方法往往无法成功进行解码。这时候就需要手动解析数据结构。`fastjson`提供`getString()`方法(即`get().toString()`)来从`JSONObject`对象中提取指定字段的值(通过冒号来判定)。

```java
List<JSONObject> pre_classes_array = JSON.parseArray(pre_classes_text, JSONObject.class);
for (int i = 0; i < pre_classes_array.size(); i++) {
    String class_name = pre_classes_array.get(i).getString("class_name");
    int id = Integer.parseInt(pre_classes_array.get(i).getString("id"));
    ArrayList<String> number = new ArrayList<>(JSON.parseArray(pre_classes_array.get(i)
            .getString("number"), String.class));
    ArrayList<String> name = new ArrayList<>(JSON.parseArray(pre_classes_array.get(i)
            .getString("name"), String.class));
    pre_classes[i] = new Pre_class(id, class_name, number, name);
}


// Pre_class 结构
public class Pre_class {
    private int id;
    private String class_name;
    private ArrayList<String> number;
    private ArrayList<String> name;
    public Pre_class() {}
    public Pre_class(int id, String class_name, ArrayList<String> number, ArrayList<String> name) {
        this.id = id;
        this.class_name = class_name;
        this.number = number;
        this.name = name;
    }
    public Pre_class(String class_name, String[] number, String[] name) {
        this.class_name = class_name;
        this.number = new ArrayList<>(Arrays.asList(number));
        this.name = new ArrayList<>(Arrays.asList(name));
    }

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getClass_name() {
        return class_name;
    }

    public ArrayList<String> getNumber() {
        return number;
    }

    public ArrayList<String> getName() {
        return name;
    }

    // 获取指定索引的学生信息
    public ArrayList<String> getStudent(int i) {
        ArrayList<String> a = new ArrayList<>();
        a.add(number.get(i));
        a.add(name.get(i));
        return a;
    }

    // 获取整个预设班级的学生列表
    public ArrayList<ArrayList<String>> getStudents() {
        ArrayList<ArrayList<String>> a = new ArrayList<>();
        for (int i = 0; i < name.size(); i++) {
            ArrayList<String> b = new ArrayList<>();
            b.add(number.get(i));
            b.add(name.get(i));
            a.add(b);
        }
        return a;
    }
}
```

## 2. Json转码

相对于解码的复杂，转码就十分轻松了。我推荐使用Gson来进行。

首先添加依赖`implementation 'com.google.code.gson:gson:2.8.5'`。

然后调用`toJson()`来进行转码。

```java
Gson gson = new Gson();
String strJson = gson.toJson(pre_class);
```

---

有了json的解码和转码方法， 就可以在前后端交互中使用一些具有复杂或者自定义结构的数据了，大大提高了开发的便利性。